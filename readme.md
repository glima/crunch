# crunch

![Crates.io Version](https://img.shields.io/crates/v/crunch-app)

`crunch` is a drop-in `cargo` replacement for offloading Rust compilation to a remote server.

Cut compile times and iterate faster!

## Usage

Just replace `cargo` with `crunch`.

```bash
c̶a̶r̶g̶o̶crunch check
c̶a̶r̶g̶o̶crunch clippy --workspace
c̶a̶r̶g̶o̶crunch t -p sys-internals
```

### Remote Build Paths with `--remote-path`

By default, `crunch` mirrors your local directory structure on the
remote server (`--remote-path mirror`). You can also choose
alternative behaviors:

**Temporary builds** (`--remote-path tmp`): Creates builds in
temporary directories that are automatically cleaned up after
completion:

```bash
crunch --remote-path tmp build --release
```

**Unique persistent builds** (`--remote-path unique`): Creates a
dedicated directory in `~/crunch-builds/<project-name>` that persists
across builds, similar to `cargo-remote`:

```bash
crunch --remote-path unique build --release
```

The temporary approach avoids filesystem assumptions at the cost of
more bandwidth usage. The unique approach provides persistent build
artifacts while keeping projects isolated.

## Installation

```bash
cargo install crunch-app
```

## Setup

1. Install Rust on a Debian-based machine
2. Add a `crunch` host to your `~/.ssh/config`

```text
Host crunch
  HostName your-machine-ip
  User your-machine-user
  IdentityFile ~/.ssh/your-key.pem
  ControlMaster auto
  ControlPath ~/.ssh/control-%r@%h:%p
  ControlPersist 5m
```

3. Ready to use `crunch` 🔥

### What Hardware Should I Use?

I recommend prioritising fewer high performing cores over many slower cores.

As of mid-2025, I'm personally using a [`Hetzner AX102`](https://www.hetzner.com/dedicated-rootserver/ax102/), which has compile times approximately equivalent to an Apple M4 Pro chip. The AX42 and AX52 are also great options.

If there is demand, I will consider selling access to managed hardware directly in the cli. Interested? [Come say hi in Discord](https://discord.gg/pS5rvjZXzq)!

## rust-analyzer (experimental)

Use `crunch` with `rust-analyzer` by setting `rust-analyzer.check.overrideCommand` to your preferred `crunch` command, including the `--message-format=json` flag.

e.g. in VSCode, you might set

```text
  "rust-analyzer.check.overrideCommand": [
    "crunch",
    "check",
    "--quiet",
    "--workspace",
    "--message-format=json",
    "--all-targets",
    "--all-features"
  ],
```

in your `settings.json`.

## Advanced Usage

```
Usage: crunch [OPTIONS] <COMMAND>...

Arguments:
  <COMMAND>...
          The cargo command to execute

          Example: `build --release`

Options:
  -e, --build-env <BUILD_ENV>
          Set remote environment variables. RUST_BACKTRACE, CC, LIB, etc

          [default: RUST_BACKTRACE=1]

      --exclude <EXCLUDE>
          Path or directory to exclude from the remote server transfer. Specify multiple entries using delimiter ','.

          By default the `target` and `.git` directories are excluded.

          Example: `--exclude "target,.git,cat.png,*.lock,mocks/**/*.db"`

          [default: target,.git]

      --post-cargo <POST_CARGO>
          A command to execute on the machine after the cargo command has finished executing.

          Example: `--post-cargo "cd target/release && profile my-binary"`

      --copy-back <COPY_BACK>
          Path or directory to sync back from the remote server after all other work has been done. Each entry should be in the format `source:destination`. Specify multiple entries using delimiter ','.

          Example: `--copy-back "./target/release/cuter-cat.png:.,*.bin:~/my-bins"`

      --remote-path <REMOTE_PATH>
          Specify the remote path behavior for builds

          [default: mirror]

          Possible values:
          - mirror: Mirror the local directory structure on the remote server (default)
          - tmp:    Use a temporary directory that is cleaned up after the build
          - unique: Use a unique persistent directory in the user's home directory for each project

  -h, --help
          Print help (see a summary with '-h')

  -V, --version
          Print version

EXAMPLES:
    crunch -e RUST_LOG=debug check --all-features --all-targets
    crunch test -- --nocapture
```

## `cargo-remote`

`crunch` was inspired by [cargo-remote](https://github.com/sgeisler/cargo-remote), aiming to achieve the same goals but with a simpler developer experience.

- Just replace `cargo` with `crunch`
- Minimal configuration (just set a host in `~/.ssh/config`)

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=liamaharon/crunch-cli&type=Date)](https://www.star-history.com/#liamaharon/crunch-cli&Date)
