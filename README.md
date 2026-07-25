# z4code-releases

Binary distribution endpoint for **z4code**, a coding agent for the Zed Agent
Panel that runs against an OpenAI-compatible model server. This repository
publishes release artifacts only; it contains no source code.

- Project: <https://zeta4.net>
- Current release: [`0.6.0`](https://github.com/zeta4lab/z4code-releases/releases/tag/0.6.0)

## What z4code is

z4code speaks ACP over standard input/output and uses an OpenAI-compatible model
server for model listing, streaming chat, reasoning, and tool calls. Its built-in
tools can read, search, edit, and run approved commands in the active workspace.
File edits and terminal commands are presented through Zed permission flows.

## Supported targets

| Platform | Minimum | Archive |
| --- | --- | --- |
| macOS Apple Silicon | macOS 14 | `z4code-aarch64-apple-darwin.zip` |
| Linux x86_64 | glibc 2.35 | `z4code-x86_64-unknown-linux-gnu.zip` |
| Linux aarch64 | glibc 2.35 | `z4code-aarch64-unknown-linux-gnu.zip` |
| Windows x86_64 | Windows 10 | `z4code-x86_64-pc-windows-msvc.zip` |

Intel macOS is not built. Every archive contains `LICENSE`, `README.txt`, and the
executable (`z4code`, or `z4code.exe` on Windows).

All four archives are built from the same tagged sources. The build workflow
asserts that its crate sources match the `0.6.0` tag on every run, so an archive
cannot silently claim a version it was not built from.

### Credential storage is macOS-only

Persistent bearer credentials are stored in the macOS Keychain. On Linux and
Windows the credential store returns `UnsupportedPlatform`, so `z4code setup`
cannot save a bearer token there. Those platforms work against a loopback server
with no authentication — the primary tested path, LM Studio at
`http://127.0.0.1:1234/v1` — or with a per-process `Z4_API_KEY` override.

## Installation

Zed's ACP Registry is the intended public install path. The upstream entry is
under review in
[agentclientprotocol/registry#445](https://github.com/agentclientprotocol/registry/pull/445),
so the Registry does not list z4code yet.

After the Registry entry is public:

1. Open Zed's Agent Panel and install z4code from the External Agents or ACP
   Registry interface.
2. Start LM Studio or another OpenAI-compatible server and load a tool-calling
   model.
3. Run `z4code setup` to configure the endpoint, authentication mode, and default
   model.
4. Run `z4code doctor` and resolve any reported errors.
5. Open a thread, select the model, and send a small read-only task before
   approving edits or terminal commands.

Until then, register a verified archive as a Zed Custom Agent with command
`z4code` and args `acp`. Do not put a model-server token in Zed's settings JSON.

## Verifying a download

Each archive added after the initial release ships with its own `.sha256` file.
The macOS archive is covered by `checksums.txt`, which is unchanged since the
release was first published.

```sh
# Linux and Windows archives
sha256sum -c z4code-x86_64-unknown-linux-gnu.zip.sha256

# macOS archive
shasum -a 256 -c checksums.txt
```

Published digests for `0.6.0`:

```
404b2461a7b72d2393bf380867916e86c441b8f0003f6b504f7802e8bd511693  z4code-aarch64-apple-darwin.zip
1ae68d9750f8d7aa1656d5020f833a83bcebb999be25112945edc5531d7864c6  z4code-x86_64-unknown-linux-gnu.zip
4d9da953894724b81fe3f139461cc81146e9497bb681aaece8233d5313f5e2eb  z4code-aarch64-unknown-linux-gnu.zip
1ed3776209e153377ac417eda2ccdd60ada001659bff5ac236ae4d8c083c8122  z4code-x86_64-pc-windows-msvc.zip
```

Release assets are immutable. If a digest ever fails to match, do not work around
it — delete the download and report the mismatch.

### macOS code signing

The macOS executable carries an ad-hoc signature, which is what the linker
applies and what Apple Silicon requires in order to execute at all. It is **not**
Developer ID signed and **not** notarized.

Installing through Zed's ACP Registry is unaffected, and so is a `curl`
download. If you download the ZIP with a browser instead, macOS attaches a
quarantine attribute and Gatekeeper will refuse to launch the binary. Verify the
checksum and install through a supported path rather than disabling host security
policy.

## Security model

- `http://` is accepted only for `localhost`, IPv4 loopback, or IPv6 loopback.
  Remote endpoints require `https://`.
- Persistent bearer credentials are entered without echo and stored in the macOS
  Keychain, never in a configuration file.
- Model-server redirects are followed only within the same origin, so a bearer
  credential is not forwarded to another scheme, host, or port.
- ACP JSON-RPC is the only stdout output from `z4code acp`; diagnostic logs go to
  stderr.

## Source and support

The z4code source repository is private. Report problems through this
repository's issue tracker, including the installation output, `z4code
--version`, your OS version, and CPU architecture.

## License

Apache-2.0. See `LICENSE` inside the release archive.
