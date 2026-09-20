# Push fallbacks

Use only when `git push` fails. Try in order, and stop at the first that works.

## 1. TLS backend

If the error mentions `schannel` or `SEC_E_NO_CREDENTIALS`, retry with OpenSSL:

```bash
git -c http.sslBackend=openssl push
```

## 2. Credentials via gh

If push then fails asking for a username/password, and `gh auth status` shows a logged-in account, push with the token inline and redact it from any output:

```bash
git -c http.sslBackend=openssl push "https://<login>:$(gh auth token)@github.com/<owner>/<repo>.git" <branch>
```

- Use the GitHub login as `<login>` (case-insensitive), or `x-access-token`.
- Never write the token into `.git/config` or any committed file.
- Replace the token in captured output with `***` before showing it.

## 3. Last resort: git-data API

When git's HTTPS is fully blocked but `gh api` works, recreate the commit with the GitHub git-data API: create blobs (`POST /repos/{owner}/{repo}/git/blobs`), build trees, create the commit with the correct `parents`, then move `refs/heads/main`. Only use this when the first two fail; keep blob and tree SHAs identical to the local objects.