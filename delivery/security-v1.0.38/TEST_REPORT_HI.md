# v1.0.38 — executed test report

## Environment / evidence boundaries

Linux; Node v24.19.0; Chromium 154.0.8037.57; Playwright 1.62.1; PowerShell 7.4.6; production C# SQLite bridge exercised with a **test-only Linux resolver** for system SQLite. No actual Windows machine, native winsqlite3.dll, D: drive, Inno Setup compiler or physical Main–Client LAN was available. Fixtures use disposable data only. No production DB/config/path was changed.

`BASELINE.tap`: existing 77-test suite actually ran: **76 PASS, 1 FAIL, 0 SKIP**. Failure: Company 2 ordinary-user navigation click timeout on Photo. Baseline was not 77/77.

**Final executed run: 114 tests, 114 PASS, 0 FAIL, 0 SKIP, 235.248 seconds**, recorded in `FINAL_ACCEPTANCE.tap`. This is the final source revision; assertions/timeouts unchanged. Earlier final attempt `FINAL_VERIFIED.tap`: **113 PASS, 1 FAIL, 0 SKIP / 114**; ordinary-user long navigation test timed out on Day Book. This intermittent issue was already present in baseline. Separate actual user navigation rerun passed, and an instrumented all-81-transition run completed. Profiling did not establish a persistent observer loop. **Root cause remains unconfirmed; a later passing run does not prove this intermittent timeout repaired.** No timeout increase, assertion weakening or forced click was used. Treat navigation performance as an open acceptance issue.

## Reproduction

Install Node + `playwright@1.62.1` (resolve via local node_modules or NODE_PATH), Chromium, PowerShell 7 on Linux. Point `PWSH_PATH` and optional `CHROMIUM_PATH` at actual executables:

```sh
PWSH_PATH=/path/to/pwsh CHROMIUM_PATH=/usr/bin/chromium node --test --test-concurrency=1 --test-reporter=tap --test-force-exit tests/*.test.js
```

This run additionally used writable `XDG_CACHE_HOME`, `XDG_CONFIG_HOME`, `XDG_DATA_HOME`. For real Windows native SQLite run the separately supplied `tests/WindowsNativeSmoke.ps1` exactly as described in the Windows report. That script was NOT RUN here.

## Security cases actually executed

| Case | Expected | Actual/evidence |
|---|---|---|
| No login / invalid token / forged Admin headers | 401, no protected data | PASS, actual HTTP + shared production security gate |
| Wrong password | 401 | PASS |
| Expired session | 401 despite valid device token | PASS |
| Logout replay; other session | Old 401; unrelated valid session 200 | PASS |
| Disabled account/password/role change | Old session invalid | PASS |
| Ordinary user backup/download/restore/publish/user management | 403 | PASS |
| Cross-company snapshot/change feed/record/file identifiers | Filtered own scope or 403 | PASS |
| Unauthorized file upload/delete and unknown namespace | 403 | PASS |
| Authorized business add / real Company 2 form save/reload | Saved; no cross-company modification | PASS |
| Unauthorized edit/delete/owner spoof/financial approval | 403, no partial mutation | PASS |
| Credential leakage | No plaintext/hash in normal snapshots/change responses/browser/offline API | PASS on fixtures |
| Salted hash + migration/rollback | Correct/wrong password verified; source unchanged; safety snapshot restores | PASS on isolated real SQLite copies |
| Public same-company signatures | Reports retain signer; private profile denied | PASS |
| Linked Staff attendance | Own today Mark In/Out saved, payroll/other Staff unchanged | PASS, actual Chromium + production gate |
| No saved legacy permission profile | Existing own-company business access retained, Admin/other companies denied | PASS |
| Browser private drafts | Owner reload/recovery works; another account/wrong key/tamper denied | PASS, actual Chromium export/download/file chooser + vendored cipher |
| Legacy pending root | Credentials scrubbed, Admin seals review copy, no automatic replay | PASS |
| Active HTML document / injected ID / username apostrophe | No script execution, valid username editor works | PASS, Chromium/direct APIs |
| Offline cache | Encrypted snapshot/index/files; expiry/tamper/unavailable OS crypto fail closed | PASS with OS-protection test adapter |
| Unsupported old Main protocol / partial legacy cache | No unscoped data serving | PASS |
| Restored login grants | Cleared before exposure | PASS helper/SQLite; full Windows restore untested |

HTTP integration uses real PowerShell HttpListener, actual shared UserSecurity/BlobStorage and production service functions, disposable SQLite. It is **not a Windows MainService process**. Chromium Company 2 broad legacy-function/navigation tests use a separate HTTP contract fixture, not production authorization. Main/Electron tests use mocked Electron/OS integration. These evidence types are not interchangeable.

## Company 2: all 10 sidebar options

All nine business modules were individually opened, selected green/other blue colors asserted, visible root/no overlap checked, all 81 pair transitions and rapid switches executed for Admin and ordinary User. Admin Dashboard/Back tested from every module. User long-run intermittent timeout described above prevents unconditional navigation certification.

| Sidebar option | Functional regression actually run | Qualification |
|---|---|---|
| Admin Dashboard | Open/return from each module, single selection | Fixture browser PASS |
| Inquiry Form | Add/edit/search/approved-list filter/print/delete | Fixture browser PASS |
| Counter Account File | CRUD, upload/download, internal navigation | Fixture browser PASS |
| Day Book | Entry, report/ledger tabs, edit/delete, search/filter/print, draft retention | Fixture browser PASS; user navigation timeout in earlier final attempt |
| Bank Book | Bank/settings CRUD, entry save/edit/search/filter/report/print/delete | Fixture browser PASS |
| Cash Main Counter | Transfer/report/settings, tabs/draft/save/edit/delete | Fixture browser PASS |
| Data Entry | Actual form save/reload; view/edit/delete/settings and file draft retention | Fixture + real security-gate browser PASS |
| 4×6 Photo | Upload, adjust/reset, export/print; original-image fallback | Fixture browser PASS; baseline user navigation timeout |
| All Documents | Folder/file CRUD/search/filter/export, upload/download, viewer safety; move authorization via API | Fixture + security browser PASS |
| All Software | Folder/file CRUD/search/filter/export, upload/download; move authorization via API | Fixture browser PASS |

Print tests inspect browser print/report behavior, not physical printer output. Camera/DPAPI/Windows shell were not certified; downloaded file bytes were verified in Chromium. Optional external AI photo processing was deliberately blocked to test existing fallback; AI model processing is NOT certified. Company 1/Utopiya and other-company broad CRUD/Staff tests ran on fixture data; every production historical workflow/permission preset was not available.

## Main–Client, storage, backup/update/installation

Same-machine independent browser sessions share fixture storage, sync and keep separate unsaved drafts. SQLite tests execute persistence, revisions/concurrent connections, backup consistency/integrity and service helper safety. Source/parser/isolated helper tests cover corrupt restore rejection, rollback, archive/download/checksum guards and update deferral. Actual service lifecycle, Windows process ownership/task scheduling and v1.0.36→1.0.38 / v1.0.37→1.0.38 installed upgrades were **NOT RUN**. The old v1.0.36 first-hop updater limitation remains; use reviewed source-installer workflow on isolated Windows.

D: defaults and existing configurable paths were preserved in source. Actual D: available/missing/read-only/full/ACL-denied behavior is **NOT RUN**, not PASS. Existing D: ACL hardening remains uncompleted. No compiled installer was produced. See `WINDOWS_STORAGE_AND_ACTIVATION_HI.md` for scripts, environment, migration and rollback.

Confirmed remaining boundaries: HTTP transport has no TLS rollout; production legacy credential/history/cache migration awaits explicit approval; offline trusts last authorized principal under Windows OS account without a separate disconnected app password; mixed old/new services unsupported; custom historical key workflows conservatively deny pending reviewed mapping. These are not hidden under “all tests passed.”
