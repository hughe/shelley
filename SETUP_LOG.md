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

### SLDB build notes

- The **container builds are normative**. If the container build and tests don't pass, it's broken — regardless of whether it works locally.
- Local builds are useful for compiling, running, and debugging, but are not the source of truth.

### Build discipline

- **Always use Makefile targets** to build, test, and run things — never invoke `cargo`, `cc`, `cmake`, etc. directly.
- This ensures every build step is reproducible and the human can repeat exactly what Shelley did.
