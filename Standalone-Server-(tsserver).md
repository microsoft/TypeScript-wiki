The TypeScript standalone server (a.k.a. `tsserver`) is a node executable that encapsulates the TypeScript compiler and language services, and exposes them through a JSON protocol. `tsserver` is well suited for editors and IDE support. 

# Protocol

## Definition

The server communication protocol is defined in the `ts.server.protocol` namespace, declared in [`protocol.ts`](https://github.com/microsoft/TypeScript/blob/main/src/server/protocol.ts).

The executable (`tsserver`) can be found in the **bin** folder under the **typescript** package.

```cmd
npm install --save typescript
ls node_modules\typescript\bin\tsserver
```

## Message format

`tsserver` listens on `stdin` and writes messages back to `stdout`. 

Requests are JSON following the protocol definition. Here is an example request to open a file `c:/DefinitelyTyped/gregorian-calendar/index.d.ts`:

```js
{"seq":1,"type":"request","command":"open","arguments":{"file":"c:/DefinitelyTyped/gregorian-calendar/index.d.ts"}}
```

Responses are augmented JSON format. The Message starts with a header with the content length followed by a line separator (`\r\n`) followed by the response body as a JSON string:

Here is an example of a response for a `quickinfo` command:

```js
Content-Length: 116

{"seq":0,"type":"response","command":"quickinfo","request_seq":2,"success":false,"message":"No content available."}
```

Similarly, responses to events also have the same format. 

Here is an example event for an error message:

```js
Content-Length: 261

{"seq":0,"type":"event","event":"semanticDiag","body":{"file":"c:/DefinitelyTyped/gregorian-calendar/index.d.ts","diagnostics":[{"start":{"line":264,"offset":44},"end":{"line":264,"offset":50},"text":"Binding element 'Object' implicitly has an 'any' type."}]}}
```

## Commands

The full list of commands supported by `tsserver` can be found under [ts.server.protocol.CommandTypes](https://github.com/microsoft/TypeScript/blob/main/src/server/protocol.ts). 

Each command is associated with a request and a response interface. For instance, the command `"completions"` corresponds to the request interface `CompletionsRequest`, and has a response interface defined in `CompletionsResponse`.

# Sample implementations

## Sublime text plugin 

[TypeScript-Sublime-Plugin](https://github.com/Microsoft/TypeScript-Sublime-Plugin) is a Sublime text plugin written in Python that uses `tsserver`.

## Visual Studio Code

[VS Code](https://code.visualstudio.com/)'s [TypeScript support](https://github.com/microsoft/vscode/tree/main/extensions/typescript-language-features) is implemented in TypeScript using `tsserver`.

## Tide

[Tide](https://github.com/ananthakumaran/tide) is an Emacs plugin (implemented in elisp) that uses `tsserver`,

## Neovim

[nvim-typescript](https://github.com/mhartington/nvim-typescript) is a Neovim plugin using `tsserver`.

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

**Example**: 
```sh
set TSS_LOG=-level verbose -file c:\tmp\tsserver.log
```

## Cancellation

On startup. `tsserver` will try to load the module `./cancellationToken` from the containing directory. This module should export a factory function that accepts a list of command line arguments and returns [HostCancellationToken](https://github.com/Microsoft/TypeScript/blob/main/src/services/types.ts#L254-L256). `tsserver` will use this token to check if in-flight operation should be cancelled. 

**NOTE:** This token will be used for all operations; so if one operation is cancelled, and cancellation was reported using this the token, then, when another operation is started, the token should be reset to the non-cancelled state.

Default implementation of the cancellation token uses the presence of named pipes as a way to signal cancellation.

1. Before spawning the server, the client generates a unique name. This name is passed to the server as a `cancellationPipeName` command line argument.  
2. If some operation on the client side should be cancelled, the client opens a named pipe with a name generated on step 1. Nothing needs to be written to the pipe — the default cancellation token will interpret the presence of the named pipe as a cancellation request.  
3. After receiving acknowledgment from the server, the client closes the pipe so it can use the same pipe name for the next operation.

Server can split execution of some commands (like `geterr`) in a few steps that will be executed with a delay. This allows it to react on user actions more promptly and not run heavy computations if their results will not be used. However, it introduces a tricky moment in support of cancellations. By allowing requests to be suspended and resumed later we break the invariant that was the cornerstone for the default implementation of cancellation. Namely, requests can now overlap, so one pipe name can no longer be used because the client has no idea what request is currently being executed and will be cancelled. To deal with this issue, `tsserver` allows pipe names to be computed dynamically based on the current request id.  
To enable this, the client needs to provide a value that ends with `*` as the `--cancellationPipeName` argument. If the provided cancellation pipe name ends with `*`, then the default implementation of the cancellation token will construct the pipe name as `<cancellationPipeName argument without *><currentRequestId>`. This will allow the client to signal any request it thinks is in flight by creating a named pipe with a proper name. Note that the server will always send a `requestCompleted` message to denote that the asynchronous request was completed (either by running to completion or via cancellation), so the client can close the named pipe once this message is received.

## Command line options

| Option                         | Description
|--------------------------------|-------------
| `--cancellationPipeName`       | Name of the pipe used as a request cancellation semaphore. See [Cancellation](#cancellation) for more information.
| `--syntaxOnly`                 | A streamlined mode where the server will only be answering syntactic queries.
| `--suppressDiagnosticEvents`   | Opt out of receiving events when new diagnostics are discovered (i.e., must request them explicitly).
| `--eventPort`                  | Port used for receiving events. If none is specified, events are sent to `stdout`.
| `--useSingleInferredProject`   | Put all open `.ts` and `.js` files that do not have a `.tsconfig` file into a common project.
| `--noGetErrOnBackgroundUpdate` | Opt out of starting `getErr` on the `projectsUpdatedInBackground` event.
| `--locale`                     | The locale to use to show error messages, e.g. `en-us`.  
|                                | Possible values are:  
|                                | ► English (US): `en`  
|                                | ► Czech: `cs`  
|                                | ► German: `de`  
|                                | ► Spanish: `es`  
|                                | ► French: `fr`  
|                                | ► Italian: `it`  
|                                | ► Japanese: `ja`  
|                                | ► Korean: `ko`  
|                                | ► Polish: `pl`  
|                                | ► Portuguese (Brazil): `pt-BR`  
|                                | ► Russian: `ru`  
|                                | ► Turkish: `tr`  
|                                | ► Simplified Chinese: `zh-CN`  
|                                | ► Traditional Chinese: `zh-TW`

# Project System

There are three kinds of projects:

## Configured Project

Configured projects are defined by a configuration file, which can be either a `tsconfig.json` file or a `jsconfig.json` file.  
That configuration file marks the project root path and defines what files to include.  
The configuration file also provide the compiler options to be used for this project.

You can find more information in the [tsconfig.json documentation](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html).

## External Project

An external project represents host-specific project formats that TS is not aware of.  
The host is responsible for supplying the list of files in this project and compiler options to use.

Currently VS is the only consumer of this type of project, to model the TS/JS files in a `.csproj` project.

## Inferred Project

Inferred projects are what is used to represent a loose TS/JS file.
If a file does not have a configuration file (`tsconfig.json` or `jsconfig.json`) **in the current directory or any parent directories**, the server will create an inferred project for it.

The server will include the loose file, then includes all other files included by triple slash references and module imports from the original file transitively.

Compilation options will use the default options for inferred projects.  
The host can set the defaults of an inferred project.

## Relationship Among These Projects

In general, the relationship can be summarized as `configured projects > external projects > inferred projects`.

For example, consider that `file1.ts` belongs to an inferred project, but, later on, a new `tsconfig.json` also includes this file.  
Then after the `tsconfig.json` file is found, `file1.ts` will no longer belong to the previous inferred project, but to the newly created configured project instead.  
If `file1.ts` used to be the root file of the inferred project, that inferred project will now be destroyed; otherwise it will remain with one fewer file.

For another example, if a `tsconfig.json` file is created to include some files used to belong to an external project (let's call it EP1), then, since in the current implementation EP1 will be destroyed, all its files either go to the new configured project or will belong to a new inferred project the next time it is opened.

One thing to notice is that one file can belong to multiple projects of the same kind at the same time. E.g., a file can be included by multiple configured projects / inferred projects / external projects.
