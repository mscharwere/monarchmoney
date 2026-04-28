# PATCHES — mscharwere/monarchmoney fork

> **DEPRECATED — 2026-04-28:** This fork has been superseded by
> `bradleyseanf/monarchmoneycommunity` (60 stars, active maintainer, weekly updates).
> `C:/repos/monarch-mcp` now pins to `bradleyseanf/monarchmoneycommunity@v1.3.2`.
> This repo is **retained as a snapshot/fallback only** — do not delete, but do not
> actively maintain or pin new projects to it.



This fork diverges from `hammem/monarchmoney` (upstream, last release v0.1.15, Jan 2025).
The upstream maintainer has been unresponsive since Nov 2025. These patches fix critical
API breakage and port improvements from `bradleyseanf/monarchmoneycommunity`.

## Patch 1 — BASE_URL domain migration (CRITICAL)

**Commit:** `fix: update BASE_URL to api.monarch.com (domain migration Jan 2026)`

Monarch migrated their API domain from `api.monarchmoney.com` to `api.monarch.com` in early 2026.
The old domain returns 301 redirects that the aiohttp client mishandles, surfacing as fake 429 or
connection errors. This patch changes the single constant that controls all API endpoints.

```python
# Before (broken):
BASE_URL = "https://api.monarchmoney.com"

# After (working):
BASE_URL = "https://api.monarch.com"
```

Upstream reference: PR #192 (filed by another contributor, unmerged as of Apr 2026).

## Patch 2 — bradleyseanf/monarchmoneycommunity ports

**Commit:** `feat: port bradleyseanf/monarchmoneycommunity improvements`

Ports a comprehensive set of improvements from the community fork. Changes include:

- **gql v4 compatibility** — `_is_gql_v4_or_newer()` helper + dynamic `request` vs `document`
  kwarg selection in `gql_call()`. Required because `gql>=4.0` renamed the parameter.
- **Cloudinary attachment upload** — `upload_attachment()` method + `getAttachmentUploadEndpoint()`;
  allows uploading receipt images to transactions.
- **BalanceHistoryRow dataclass** — typed input for `upload_account_balance_history()` replacing
  raw CSV string. Includes `_convert_to_csv_string()` helper.
- **Improved `upload_account_balance_history()`** — full session-based upload with polling loop
  instead of a broken fire-and-forget POST.
- **Long-lived token enforcement** — login and MFA flows now explicitly request `trusted_device=True`,
  detect and reject short-lived JWT-style features tokens (1-hour Ably tokens), and raise clear
  errors when the server returns the wrong token type.
- **JWT guard helpers** — `_looks_like_jwt()` and `_is_long_lived()` static methods.
- **`save_session()` guard** — refuses to persist a JWT-style token to disk.
- **Better error surfacing in login** — `_login()` parses `detail` / `error_code` from error
  responses instead of swallowing them.
- **`get_credit_history()`** — new method returning Spinwheel credit score snapshots.
- **`trusted_device` param on `multi_factor_authenticate()`** — caller can override.
- **Stderr for session-found message** — `print(..., file=sys.stderr)` so it doesn't pollute
  stdout-based MCP transport.
- **Budget GraphQL query modernized** — `GetJointPlanningData` replaces the old
  `Common_GetJointPlanningData`; inline fragments replace named fragments; `$useV2Goals` variable
  wired up properly.
- **Import additions** — `csv`, `importlib.metadata`, `mimetypes`, `sys`, `dataclass`, `StringIO`.
- **`DEFAULT_DELAY_SECS` and `DEFAULT_TIMEOUT_SECS` constants** replace magic numbers.
- **Updated User-Agent** — points to the community fork.

## Pinning this fork in your project

In `pyproject.toml`:

```toml
"monarchmoney @ git+https://github.com/mscharwere/monarchmoney.git@<tag-or-sha>",
```

After updating, run `uv sync` to pull from the fork instead of PyPI.

## How to update this fork in the future

```bash
cd C:/repos/monarchmoney
git remote add upstream https://github.com/hammem/monarchmoney.git  # already set if cloned via gh
git fetch upstream
git rebase upstream/main   # or merge, depending on preference
# Re-verify patches are intact, then push + re-tag
```
