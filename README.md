# chiron-releases

Binary releases of the Chiron daemon — a local runtime bridge between Ditto cloud and your local agent CLI (Claude Code, Codex, OpenCode). Your source stays on your machine; the daemon talks to the manager and orchestrates task execution locally.

## Install

### Option A — Install and pair in one shot (recommended)

The Engineer board's *New Agent → Local runtime (daemon)* wizard gives you this
single command, already filled with your one-time pairing code:

```bash
curl -fsSL https://raw.githubusercontent.com/Chiron-Team-G/chiron-releases/main/install.sh \
  | bash -s -- --code CHIR-XXXXXX-XXXXXX --server https://your-manager.example.com
```

What it does:

1. Detects whether `chiron` is already installed on this machine and
   skips the download if so (useful when you're pairing a 2nd agent on
   the same machine — config.json just gets a new entry).
2. If it's a fresh install, downloads the matching tarball from the
   latest release, verifies SHA-256 against `checksums.txt`, drops the
   binary at `/usr/local/bin/chiron` (or `~/.local/bin/chiron` without
   sudo).
3. Offers to install Ollama for semantic search (optional — see below).
4. Runs `chiron setup --code … --server …` to authenticate this daemon
   against your Chiron manager. Credentials are stored in
   `~/.chiron/config.json` (`chmod 0600`, raw token local-only).

Then run `chiron start` in this terminal to begin polling for tasks.

### Option B — Install only (manual pair later)

If you just want the binary on your PATH (no pairing yet):

```bash
curl -fsSL https://raw.githubusercontent.com/Chiron-Team-G/chiron-releases/main/install.sh | bash
```

Pair manually when you're ready:

```bash
chiron setup --code CHIR-XXXXXX-XXXXXX --server https://your-manager.example.com
chiron start
```

## Optional: enable semantic search

Without [Ollama](https://ollama.com), the daemon falls back to keyword (BM25) search over its local knowledge store. Installing Ollama enables local vector embeddings on top, so the agent finds related entries even when query words don't exactly match:

```bash
brew install ollama && ollama pull nomic-embed-text
```

Runs 100% on your machine — nothing leaves the laptop.

## Manual install (Windows or other)

If the installer doesn't support your platform yet, download the matching archive from the [latest release](https://github.com/Chiron-Team-G/chiron-releases/releases/latest), verify against `checksums.txt`, and put the binary on your `PATH`.

Available binaries per release:

| Platform | Tarball |
|---|---|
| macOS arm64 (Apple Silicon) | `chiron-darwin-arm64.tar.gz` |
| macOS x64 (Intel) | `chiron-darwin-x64.tar.gz` |
| Linux x64 | `chiron-linux-x64.tar.gz` |
| Linux arm64 | `chiron-linux-arm64.tar.gz` |
| Windows x64 | `chiron-windows-x64.zip` |

## Where is the source?

This repo only hosts binary releases. The daemon source lives in a separate repo managed by the chiron team. Releases here are produced by an automated build pipeline.

## Verifying a download manually

Every release includes a `checksums.txt` with SHA-256 hashes for every artifact:

```bash
shasum -a 256 -c checksums.txt
```
