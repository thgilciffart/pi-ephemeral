# pi-ephemeral

Start an in-memory chat from a running [Pi](https://pi.dev) session. Pi does not save the new conversation as a session file.

## Install

```sh
pi install npm:@thgilciffart/pi-ephemeral
```

Run `/reload` in an open Pi session.

## Start a chat

```text
/ephemeral
/ephemeral Explain this function
```

Confirm the dialog to start a new chat. An optional prompt is sent after the extension verifies that persistence is disabled. Your previous saved chat remains on disk.

The footer shows `◌ ephemeral`. Running `/ephemeral` again reports that the chat is already ephemeral. `/new` keeps the in-memory mode, and `/resume` can open a saved chat. Creating an ephemeral chat requires an interactive UI.

Ephemeral chats do not appear in `/resume`, and `/session` shows `In-memory`. Their conversation history is lost when Pi exits. The conversation still goes to your configured model provider. Tools can still write files, and commands such as `/export` and `/share` can save or upload content.

## Compatibility

The extension changes internal `SessionManager` persistence fields before the new chat receives a message. It then verifies that `isPersisted()` is false and `getSessionFile()` is undefined. If verification fails, it reports that the new chat will be saved and does not send the optional prompt.

## Develop

```sh
bun install
./scripts/link-pi-types.sh
bun run typecheck
bun test
pi -e ./src/index.ts
```

The linking script supplies types from your installed Pi packages.

[MIT license](LICENSE).
