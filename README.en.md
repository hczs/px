# px

[简体中文](./README.md)

[![CI](https://github.com/hczs/px/actions/workflows/ci.yml/badge.svg)](https://github.com/hczs/px/actions/workflows/ci.yml)
[![Release](https://img.shields.io/github/v/release/hczs/px)](https://github.com/hczs/px/releases)
[![License](https://img.shields.io/github/license/hczs/px)](./LICENSE)
[![Go Reference](https://pkg.go.dev/badge/github.com/hczs/px.svg)](https://pkg.go.dev/github.com/hczs/px)

`px` is a lightweight Go CLI for quickly enabling and disabling proxy environment variables in the current terminal session.

It detects common local proxy tools, saves a local config, and installs a shell function so `px on` / `px off` can affect the current shell.

## Features

- One-command setup: detect proxy config and install the shell integration.
- Current-session effect: update proxy variables in the active terminal.
- Restore previous values: `px off` restores proxy variables that existed before `px on`.
- Auto-detection for Clash, Clash Verge, Surge, v2rayA, and v2rayN.
- Shell support for bash, zsh, and PowerShell.
- Small and transparent: config lives at `~/.px/config.yaml`.

## Installation

Download a binary for your platform from [GitHub Releases](https://github.com/hczs/px/releases).

If you are not sure which asset to download:

- Apple Silicon Mac: `darwin_arm64`
- Intel Mac: `darwin_amd64`
- Common Linux x86_64: `linux_amd64`
- Linux ARM64: `linux_arm64`
- Common Windows x86_64: `windows_amd64.zip`
- Windows ARM64: `windows_arm64.zip`

### macOS Binary Install

The example below is for Apple Silicon Macs. On Intel Macs, replace `darwin_arm64` with `darwin_amd64`.

```bash
cd ~/Downloads
tar -xzf px_VERSION_darwin_arm64.tar.gz
chmod +x px
sudo mv px /usr/local/bin/px
px init
```

If macOS says the file is from an unidentified developer, run:

```bash
xattr -d com.apple.quarantine /usr/local/bin/px
```

Then restart your terminal, or run the `source ...` command printed by `px init`. After that:

```bash
px on
px status
px off
```

### Linux Binary Install

The example below is for x86_64 Linux. On ARM64 machines, replace `linux_amd64` with `linux_arm64`.

```bash
cd ~/Downloads
tar -xzf px_VERSION_linux_amd64.tar.gz
chmod +x px
sudo mv px /usr/local/bin/px
px init
```

Restart your terminal, or run the `source ...` command printed by `px init`. After that:

```bash
px on
px status
px off
```

### Windows Binary Install

1. Download `px_VERSION_windows_amd64.zip` from Releases.
2. Unzip it to get `px.exe`.
3. Open PowerShell in the directory that contains `px.exe`, then run:

```powershell
$InstallDir = "$env:LOCALAPPDATA\Programs\px"
New-Item -ItemType Directory -Force $InstallDir | Out-Null
Copy-Item .\px.exe "$InstallDir\px.exe" -Force

$UserPath = [Environment]::GetEnvironmentVariable("Path", "User")
if (($UserPath -split ";") -notcontains $InstallDir) {
  [Environment]::SetEnvironmentVariable("Path", "$UserPath;$InstallDir", "User")
  $env:Path = "$env:Path;$InstallDir"
}
```

Check the installation:

```powershell
px init
```

`px init` writes the PowerShell profile integration. Reopen PowerShell. After that:

```powershell
px on
px status
px off
```

Or install from source:

```bash
go install github.com/hczs/px@latest
```

Release builds support macOS, Linux, and Windows on amd64 / arm64.

## Quick Start

Initialize `px`:

```bash
px init
```

Follow the prompts to use a detected proxy config, or enter one manually. Then restart your terminal, or run the `source ...` command printed by `px init`.

Enable proxy:

```bash
px on
```

Check status:

```bash
px status
```

Test the current outbound IP:

```bash
px test
```

Disable proxy:

```bash
px off
```

## Commands

| Command | Description |
| --- | --- |
| `px init` | Detect shell/proxy config, save config, and install the shell function |
| `px on` | Enable proxy environment variables in the current shell |
| `px off` | Restore or clear proxy environment variables in the current shell |
| `px status` | Show current proxy environment status |
| `px test` | Test the current proxy through `https://ipinfo.io/json` |
| `px list` | List detected local proxy software |
| `px config` | Reconfigure proxy manually |

## Supported Environment Variables

`px on` sets these variables according to the saved config:

- `http_proxy` / `HTTP_PROXY`
- `https_proxy` / `HTTPS_PROXY`
- `all_proxy` / `ALL_PROXY`

HTTP and HTTPS proxies use `http://host:port`. SOCKS5 proxies use `socks5://host:port`.

## How It Works

A normal CLI child process cannot directly modify its parent shell environment. `px init` therefore writes a `px` shell function into your shell profile.

When you run `px on` / `px off`, that function asks the `px` binary to generate shell code and evaluates it in the current shell.

## Configuration

Default config file:

```text
~/.px/config.yaml
```

Example:

```yaml
proxy:
  http:
    enabled: true
    host: 127.0.0.1
    port: 7890
  https:
    enabled: true
    host: 127.0.0.1
    port: 7890
  socks5:
    enabled: true
    host: 127.0.0.1
    port: 7890
  auto_detect: true
  source: Auto-detected(Clash)
```

## Development

Run all local checks:

```bash
make check
```

Equivalent commands:

```bash
go fmt ./...
go vet ./...
go test ./...
go build ./...
```

## Release

Create and push a version tag:

```bash
git tag v0.1.0
git push origin v0.1.0
```

GitHub Actions will run tests and publish release artifacts with GoReleaser.

## Contributing

Issues and pull requests are welcome. Before submitting changes, run:

```bash
make check
```

If you change CLI behavior, update README docs, help text, and related tests.

## License

[MIT](./LICENSE)
