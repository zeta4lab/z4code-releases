# z4code-releases

Binary distribution endpoint for **z4code**, a coding agent for the Zed Agent
Panel that runs against a local OpenAI-compatible model server. This repository
publishes release artifacts only; it contains no source code.

- Project: <https://zeta4.net>
- Current release: [`0.6.0`](https://github.com/zeta4lab/z4code-releases/releases/tag/0.6.0)

## What z4code is

z4code speaks ACP over standard input/output and uses an OpenAI-compatible model
server for model listing, streaming chat, reasoning, and tool calls. Its built-in
tools can read, search, edit, and run approved commands in the active workspace.
File edits and terminal commands are presented through Zed permission flows.

## Supported target

macOS 14 or newer on Apple Silicon (`arm64`) only. Intel Mac, Linux, and Windows
distributions are not currently supported.

## Installation

Zed's ACP Registry is the supported public install path. The upstream Registry
entry is under review in
[agentclientprotocol/registry#445](https://github.com/agentclientprotocol/registry/pull/445).
Until it merges there is no supported public install path, and the artifacts
below are published for verification rather than manual installation.

After the Registry entry is public:

1. Open Zed's Agent Panel and install z4code from the External Agents or ACP
   Registry interface.
2. Start LM Studio or another OpenAI-compatible server and load a tool-calling
   model.
3. Run `z4code setup` to configure the endpoint, authentication mode, and default
   model. Bearer input is hidden and persistent credentials go to Keychain.
4. Run `z4code doctor` and resolve any reported errors.
5. Open a thread, select the model, and send a small read-only task before
   approving edits or terminal commands.

## Release contents

Each release contains exactly two assets:

| Asset | Contents |
| --- | --- |
| `z4code-aarch64-apple-darwin.zip` | `z4code`, `LICENSE`, `README.txt` |
| `checksums.txt` | SHA-256 of the ZIP |

## Verifying a download

```sh
shasum -a 256 -c checksums.txt
```

The `0.6.0` ZIP is:

```
404b2461a7b72d2393bf380867916e86c441b8f0003f6b504f7802e8bd511693
```

The executable is **unsigned and not notarized**. Verify the checksum before
use. Do not disable macOS security policy to run it — if the artifact is
rejected, delete the download and report the problem rather than working around
the host policy.

## Security model

- `http://` is accepted only for `localhost`, IPv4 loopback, or IPv6 loopback.
  Remote endpoints require `https://`.
- Persistent bearer credentials are entered without echo and stored in macOS
  Keychain, not in a configuration file.
- Model-server redirects are followed only within the same origin, so a bearer
  credential is not forwarded to another scheme, host, or port.
- ACP JSON-RPC is the only stdout output from `z4code acp`; diagnostic logs go to
  stderr.

## Source and support

The z4code source repository is private. Report problems through this
repository's issue tracker, including the installation output, `z4code
--version`, macOS version, and CPU architecture.

## License

Apache-2.0. See `LICENSE` inside the release archive.
