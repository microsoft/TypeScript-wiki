The TypeScript standalone server (aka `tsserver`) is a node executable that encapsulates the TypeScript compiler and language services, and exposes them through a JSON protocol. `tsserver` is well suited for editors and IDE support. 

# Protocol

## Definition

The server communication protocol is defined in [protocol.d.ts](https://github.com/Microsoft/TypeScript/blob/master/lib/protocol.d.ts).

The executable can be found in lib folder under the typescript package.

```cmd
npm install --save typescript
ls node_modules\lib\tsserver.js
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

`tsserver` supports a set of commands. The full list of commands supported by the server can be found under [ts.server.protocol.CommandNames](https://github.com/Microsoft/TypeScript/blob/master/lib/protocol.d.ts#L5). 

Each command is associated with a request and a response interface. For instance command `"completions"` corresponds to response interface `CompletionsRequest`, and response interface defined in `CompletionsResponse`.

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

## Commandline options

Option                        | Description
------------------------------|-------------
`--cancellationPipeName`      | Name of the pipe used as a request cancellation semaphore. See [Cancellation](#cancellation) for more information.
`--eventPort`                 | Port used for receiving events. If non is specified events are sent to stdout.
`--useSingleInferredProject`  | Put all open .ts and .js files that do not have a .tsconfig file in a common project

