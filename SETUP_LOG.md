# Setup Log

## 2025-04-12: Cloned GitHub repos via exe.dev integrations

Cloned three private repos using exe.dev GitHub integration URLs:

```
git clone https://hughe-sldb.int.exe.xyz/hughe/sldb.git
git clone https://hughe-sldbman.int.exe.xyz/hughe/sldbman.git
git clone https://hughe-sqpy.int.exe.xyz/hughe/sqpy.git
git clone https://hughe-leaser.int.exe.xyz/hughe/leaser.git
git clone https://hughe-failpoint.int.exe.xyz/hughe/failpoint.git
```

Requires GitHub integrations named `hughe-sldb`, `hughe-sldbman`, `hughe-sqpy`, `hughe-leaser`, `hughe-failpoint` to be attached to this VM.

### Repo relationships

- **sldbman** is the top-level repo. It knows about both `sldb` and `sqpy`.
- **sldb** knows about `leaser` and `failpoint`, but they don't know about `sldb`.
- **sqpy** is independent — doesn't know about any of the others.
- **leaser** and **failpoint** are independent — they don't know about each other or any upstream repos.
