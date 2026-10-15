# pi-ephemeral

Ephemeral chats for the [pi](https://github.com/earendil-works/pi) coding agent.

Pi saves every session to disk by default. `--no-session` disables that, but only for a whole process and only at startup. This extension adds `/ephemeral`, which starts a new chat that is never written to disk, at any point during a running pi session.

## Usage

```
/ephemeral              # ask, then start a new ephemeral chat
/ephemeral <prompt>     # start a new ephemeral chat and send <prompt>
```

The active chat is never converted. When it is saved, `/ephemeral` says so and asks whether to create a new chat:

```
Start a new ephemeral chat?
This chat is saved and can't be turned into an ephemeral chat.

Create a new chat that is never written to disk?
```

While an ephemeral chat is active, the footer shows `◌ ephemeral`. Running `/ephemeral` on an already ephemeral chat just says so. Built-in session commands keep working: `/new` from an ephemeral chat stays ephemeral, `/resume` can still open a saved chat, and so on.

## Install

```bash
# from npm, once published
pi install npm:@thgilciffart/pi-ephemeral

# from this repo
pi install git:github.com/thgilciffart/pi-ephemeral

# from a local checkout
pi install ./pi-ephemeral
```

Or try it for one run:

```bash
pi --extension ./pi-ephemeral/src/index.ts
```

## How it works

`ctx.newSession()` inherits the persistence mode of the outgoing session, and the extension command API has no option to request an in-memory session. So `/ephemeral` passes a `setup` callback that clears the persistence fields of the new `SessionManager` before the first message can create a file — exactly the state `SessionManager.inMemory()` starts in. The plugin then verifies `isPersisted()` is false and `getSessionFile()` is undefined. If a future pi version changes those internals, the plugin reports an error and tells the user the chat will be saved instead of silently pretending it is ephemeral.

The verification is why the plugin does not simply delete a session file on exit: an ephemeral chat should never hit the disk at all, including when pi crashes.

## Notes

- Ephemeral chats do not appear in `/resume`, and `/session` reports the file as `In-memory`.
- Nothing is recoverable after pi exits. If you want a record, start a saved chat instead.
- `/export`, `/share`, `/bug`, and `/copy` still work in an ephemeral chat; they only write or upload when you run them.
- This is not an incognito mode for the model provider: the conversation still goes to the configured model API.

## Development

```bash
bun install
bun run typecheck
bun test
```

`scripts/link-pi-types.sh` points `../.pi-types` at the pi packages installed on this machine, which `typecheck` uses to resolve the host-provided `@earendil-works/pi-coding-agent` module.

## License

MIT
