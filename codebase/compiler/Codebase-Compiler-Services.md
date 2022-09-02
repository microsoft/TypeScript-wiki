## Services

`services` is effectively the place where all the IDE and TS meet. It it a series of files which power the
LSP-like TSServer.

The services are APIs are used by TSServer, which creates a `ts.server` SessionClient in `src/harness/client.ts`
(it seems most of the `class`es in the compiler live in the server/services space. Maybe a by-product of working
tightly with the VS Code team? )
