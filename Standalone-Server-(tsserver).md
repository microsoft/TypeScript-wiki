The TypeScript standalone server (aka `tsserver`) is a node executable that encapsulates the TypeScript compiler and language services, and exposes them through a JSON protocol. `tsserver` is well suited for editors and IDE support. 

# Protocol

## Definition

The server communication protocol is defined in [protocol.d.ts](https://github.com/Microsoft/TypeScript/blob/master/lib/protocol.d.ts).

The executable can be found in lib folder under the typescript package.

```cmd
npm install --save typescript
ls node_modules\typescript\lib\tsserver.js
```

## Message format

`tsserver` listens on `stdin` and writes messages back to `stdout`. 

Requests are JSON following the protocol definition. Here is an example request to open a file `c:/DefinitelyTyped/gregorian-calendar/index.d.ts`:

```js
{"seq":1,"type":"quickinfo","command":"open","arguments":{"file":"c:/DefinitelyTyped/gregorian-calendar/index.d.ts"}}
```

Responses are augmented JSON format. the Message stars with a header with the content length followed by a line separator (`\r\n`) followed by the response body as a JSON string:

Here is an example of a response for a `quickinfo` command:

```js
Content-Length: 116

{"seq":0,"type":"response","command":"quickinfo","request_seq":2,"success":false,"message":"No content available."}
```

Similarly events have the same format as a response. 

Here is an example event for error message:

```js
Content-Length: 261

{"seq":0,"type":"event","event":"semanticDiag","body":{"file":"c:/DefinitelyTyped/gregorian-calendar/index.d.ts","diagnostics":[{"start":{"line":264,"offset":44},"end":{"line":264,"offset":50},"text":"Binding element 'Object' implicitly has an 'any' type."}]}}
```

## Commands

`tsserver` supports a set of commands. The full list of commands supported by the server can be found under [ts.server.protocol.CommandTypes](https://github.com/Microsoft/TypeScript/blob/master/lib/protocol.d.ts#L5). 

Each command is associated with a request and a response interface. For instance command `"completions"` corresponds to request interface `CompletionsRequest`, and response interface defined in `CompletionsResponse`.

# Sample implementations

## Sublime text plugin 

[TypeScript-Sublime-Plugin](https://github.com/Microsoft/TypeScript-Sublime-Plugin) is a Sublime text plugin written in Python that uses `tsserver`.

## VS Code

[VS Code](https://code.visualstudio.com/)'s [TypeScript support](https://github.com/Microsoft/vscode/tree/master/extensions/typescript) is implemented in TypeScript using `tsserver`.


## Tide

[Tide](https://github.com/ananthakumaran/tide) is an elisp implementation for emacs plugin using `tsserver`

# Advanced topics

## Logging

`tsserver` logging is configured through the `TSS_LOG` environment variable. 

`TSS_LOG` can have the following format:

```cmd
[-level <terse | normal | requestTime | verbose>] 
[-traceToConsole <true | false>] 
[-logToFile <true | false>] 
[-file <log file path>]
```

Note: `file` defaults to `__dirname\.log<PID>` if not specified

**Example**: `set TSS_LOG=-level verbose -file c:\tmp\tsserver.log`

## Cancellation

`tsserver` on startup will try to load module `./cancellationToken` from the containing directory. This module should export a factory function that accepts a list of command line arguments and returns [HostCancellationToken](https://github.com/Microsoft/TypeScript/blob/master/src/services/types.ts#L119-L121). tsserver will use this token to check if in-flight operation should be cancelled. 

NOTE: This token will be used for all operations so if one operation is cancelled and cancellation was reported through the token then when another operation is started - token should be reset into the non-cancelled state.

Default implementation of the cancellation token uses presence of named pipes as a way to signal cancellation.

1. before spawning the server client generates a unique name. This name is passed to the server as a `cancellationPipeName` command line argument. 
2. if some operation on the client side should be cancelled - client opens a named pipe with a name generated on step 1. Nothing needs to be written in the pipe - default cancellation token will interpret the presence of named pipe as a cancellation request.
3. After receiving acknowledgment from the server client closes the pipe so it can use the same pipe name for the next operation.

Server can split execution of some commands (like `geterr`) in a few steps that will be executed with a delay. This allows to react on user actions more promptly and do not run heavy computations if their results will not be used however it introduces a tricky moment in support of cancellations. By allowing request to be suspended and resumed later we break the invariant that was the cornerstone for default implementation of cancellation, namely now requests can overlap so one pipe name can no longer be used because client have no reason what request is currently executing and will be cancelled. To deal with this issue tsserver allows pipe name to be computed dynamically based on current request id. To enable this client need to value that ends with `*` as `--cancellationPipeName` argument. If provided cancellation pipe name ends with `*` then default implementation of cancellation token will build expected pipe name as `<cancellationPipeName argument without *><currentRequestId>`. This will allow client to signal any request it thinks is in flight by creating a named pipe with a proper name. Note that server will always send `requestCompleted` message to denote that asynchronous request was completed (either by running to completion or via cancellation) so client can close named pipe once this message is received

## Commandline options

Option                        | Description
------------------------------|-------------
`--cancellationPipeName`      | Name of the pipe used as a request cancellation semaphore. See [Cancellation](#cancellation) for more information.
`--syntaxOnly`                | A streamlined mode for when the server will only be answering syntactic queries.
`--suppressDiagnosticEvents`  | Opt out of receiving events when new diagnostics are discovered (i.e. must request them explicitly).
`--eventPort`                 | Port used for receiving events. If non is specified events are sent to stdout.
`--useSingleInferredProject`  | Put all open .ts and .js files that do not have a .tsconfig file in a common project
`--locale`                    | The locale to use to show error messages, e.g. en-us. <br/>Possible values are:  <br/>► English (US): `en` <br/>► Czech: `cs` <br/>► German: `de` <br/>► Spanish: `es` <br/>► French: `fr` <br/>► Italian: `it` <br/>► Japanese: `ja` <br/>► Korean: `ko` <br/>► Polish: `pl` <br/>► Portuguese(Brazil): `pt-BR` <br/>► Russian: `ru` <br/>► Turkish: `tr` <br/>► Simplified Chinese: `zh-CN`  <br/>► Traditional Chinese: `zh-TW`

# Project System

There are three kinds of projects:

## Configured Project

Configured projects are defined by a configuration file, which can be either `tsconfig.json` file or a `jsconfig.json` file. 
That configuration file marks the project root path and defines what files to include.
The configuration file also provide the compiler options to be used for this project.

You can find more information in the [tsconfig.json documentation](http://www.typescriptlang.org/docs/handbook/tsconfig-json.html).

## External Project

An external project represents host-specific project formats that TS is not aware of.
The host is responsible for supplying the list of files in this project and compiler options to use.

Currently VS is the only consumer of this type of project, to model the TS/JS files in a .csproj project.


## Inferred Project

Inferred projects are what is used to represent a loose TS/JS file.
If a file does not have a configuration file (`tsconfig.json` or `jsconfig.json`) **in the current directory or any parent directories**, the server will create an inferred project for it.

The server will include the loose file, then includes all other files included by triple slash references and module imports from the original file transitively.

Compilation options will use a the default options for inferred projects.
The host can set the defaults of an inferred project.

## Relationship Among These Projects

In general, the relationship can be summarized as `configured projects > external projects > inferred projects`.

For example, if `file1.ts` belongs to an inferred project, but later a new `tsconfig.json` also includes this file.
Then after the `tsconfig.json` file is found, `file1.ts` will no longer belong to the previous inferred project but the newly created configured project instead. 
If `file1.ts` used to be the root file of the inferred project, that inferred project will now be destroyed; otherwise it will remain with one fewer file.

For another example, if a `tsconfig.json` file is created to include some files used to belong to an external project (let's call it EP1), then in the current implementation EP1 will be destroyed, all its files either go to the new configured project or will belong to a new inferred project the next time it is opened. 

One thing to notice is that one file can belong to multiple projects of the same kind at the same time. E.g., a file can be included by multiple configured projects / inferred projects / external projects.


