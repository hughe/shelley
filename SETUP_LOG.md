# Setup Log

## 2025-04-12: Cloned GitHub repos via exe.dev integrations

Cloned three private repos using exe.dev GitHub integration URLs:

```
git clone https://hughe-sldb.int.exe.xyz/hughe/sldb.git
git clone https://hughe-sldbman.int.exe.xyz/hughe/sldbman.git
git clone https://hughe-sqpy.int.exe.xyz/hughe/sqpy.git
git clone https://hughe-leaser.int.exe.xyz/hughe/leaser.git
git clone https://hughe-failpoint.int.exe.xyz/hughe/failpoint.git
git clone https://hughe-shelley.int.exe.xyz/hughe/shelley.git
```

Requires GitHub integrations named `hughe-sldb`, `hughe-sldbman`, `hughe-sqpy`, `hughe-leaser`, `hughe-failpoint`, `hughe-shelley` to be attached to this VM.

- **shelley** — this repo; stores Shelley's notes and setup log.

### Repo relationships

- **sldbman** is the top-level repo. It knows about both `sldb` and `sqpy`.
- **sldb** knows about `leaser` and `failpoint`, but they don't know about `sldb`.
- **sqpy** is independent — doesn't know about any of the others.
- **leaser** and **failpoint** are independent — they don't know about each other or any upstream repos.

Dependency direction is strictly one-way: we never modify a dependency repo to reference a dependent. Changes flow downstream only.

### SLDB build notes

- The **container builds are normative**. If the container build and tests don't pass, it's broken — regardless of whether it works locally.
- Local builds are useful for compiling, running, and debugging, but are not the source of truth.

### Build discipline

- **Always use Makefile targets** to build, test, and run things — never invoke `cargo`, `cc`, `cmake`, etc. directly.
- This ensures every build step is reproducible and the human can repeat exactly what Shelley did.

### Git discipline

- **Always show diffs before committing.** Run `git diff` (or `git diff --cached` for staged changes) and display the output before creating any commit.

## 2025-04-13: Submodule migration and container build fixes

### Git submodules for sldb dependencies

Added `leaser` and `failpoint` as git submodules under `extern/` in the sldb repo:
```
git submodule add https://github.com/hughe/leaser extern/leaser
git submodule add https://github.com/hughe/failpoint extern/failpoint
```

Changed `rs/Cargo.toml` from git deps to path deps:
```
failpoint = { path = "../extern/failpoint", version = "3.0.0" }
leaser-client = { path = "../extern/leaser/rs/client" }
```

### Removed SSH requirements from Docker build

- Removed `--ssh default` from all `docker build` commands in the Makefile
- Removed `--mount=type=ssh` from all `RUN` steps in the Dockerfile
- Removed `ssh-keyscan github.com` setup from Dockerfile
- Added `COPY ./extern ./extern` to provide submodule sources in build context
- Added `DOCKER_BUILD_EXTRA_ARGS` Make variable for environment-specific flags

### VM-specific setup

- Installed Rust 1.94.1 via `rustup install 1.94.1`
- Disabled IPv6 system-wide (`/etc/sysctl.d/99-disable-ipv6.conf`) to work around Docker networking issues
- Configured Docker DNS (`/etc/docker/daemon.json`) with `1.1.1.1` and `8.8.8.8`
- Git URL rewriting for exe.dev integration proxies in `~/.gitconfig`:
  ```
  url."https://hughe-leaser.int.exe.xyz/hughe/leaser.git".insteadOf = https://github.com/hughe/leaser
  url."https://hughe-failpoint.int.exe.xyz/hughe/failpoint.git".insteadOf = https://github.com/hughe/failpoint
  ```
- Container build uses `DOCKER_BUILD_EXTRA_ARGS=--network=host` and `BUILD_PLATFORM=linux/amd64`
- 20G disk is barely enough for the full Docker build — prune build cache after builds
