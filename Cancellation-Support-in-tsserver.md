`tsserver` on startup will try to load module `./cancellationToken` from the containing directory. This module should export a factory function that accepts a list of command line arguments and returns [HostCancellationToken](https://github.com/Microsoft/TypeScript/blob/master/src/services/types.ts#L119-L121). tsserver will use this token to check if in-flight operation should be cancelled. 

NOTE: This token will be used for all operations so if one operation is cancelled and cancellation was reported through the token then when another operation is started - token should be reset into the non-cancelled state.

Default implementation of the cancellation token uses presence of named pipes as a way to signal cancellation.

1. before spawning the server client generates a unique name. This name is passed to the server as a `cancellationPipeName` command line argument. 
2. if some operation on the client side should be cancelled - client opens a named pipe with a name generated on step 1. Nothing needs to be written in the pipe - default cancellation token will interpret the presence of named pipe as a cancellation request.
3. After receiving acknowledgment from the server client closes the pipe so it can use the same pipe name for the next operation.