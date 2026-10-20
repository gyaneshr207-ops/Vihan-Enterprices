# निष्पादित परीक्षण — v1.0.37

Final संयुक्त run: **77 tests, 77 PASS, 0 FAIL, 0 SKIP**, 160.991 seconds. मूल delivered build baseline: 52 PASS. दोनों counts अलग runs हैं; इन्हें जोड़ना नहीं है। Final evidence: RELEASE_VERIFIED.tap.

Command (Linux):
```
PWSH_PATH=/workspace/audit-tools/pwsh/pwsh XDG_CACHE_HOME=/workspace/audit-tools/cache XDG_CONFIG_HOME=/workspace/audit-tools/config XDG_DATA_HOME=/workspace/audit-tools/data node --test --test-concurrency=1 --test-reporter=tap --test-force-exit tests/*.test.js
```

## Test प्रकार और सीमाएँ

- Chromium/Playwright browser: company/student/staff workflows, permissions, save conflicts, error/pending UI और Company 2 regression. Backend browser fixtures simulated HTTP हैं, production Windows service नहीं।
- वास्तविक local HTTP: update download success, truncated stream, missing/wrong checksum; existing package retained.
- वास्तविक C#/SQLite engine: isolated temporary databases, transactions/rollback, concurrent connection writes, four online consistent snapshots, reopen, missing-file guard. Linux native resolver maps winsqlite3.dll to libsqlite3.so.0 केवल test में; native Windows DLL run नहीं हुआ।
- चार PowerShell wrapper tests 77 में शामिल हैं: SQLite integration (15 assertions), extracted real backend functions (19 assertions), archive extraction (6 assertions), syntax (22 scripts). इन inner assertions को 77 में दोबारा जोड़कर count नहीं किया है। Backend functions tested हैं, पूरे Windows services boot नहीं हुए।
- Desktop health/camera/update-deferral tests mocked Electron interfaces पर हैं; native Electron device test नहीं।

## Company 2 के 10 sidebar options

| Option | Executed coverage |
|---|---|
| Admin Dashboard | First click/back, selected state, role guard, return transitions |
| Inquiry Form | Navigation/draft, validation/save regression, delayed-response race |
| Counter Account File | Navigation, existing PDF workflow regression |
| Day Book | Navigation, existing entries/search/totals/print regression |
| Bank Book | Navigation, existing entries/search/totals/print regression |
| Cash Main Counter | Navigation, existing counter/print regression |
| Data Entry | Navigation, existing entry workflow regression |
| 4×6 Photo | Navigation/file draft, original image fallback/output regression |
| All Documents | Navigation, existing documents/upload regression |
| All Software | Navigation, existing software workflow regression |

Navigation suite checks every pair among nine company pages (81 transitions per tested role), Admin return, rapid switching, selected green/normal blue, visible page and overlap, async routing, console errors, drafts. Exact assertions and workflow limits are in tests/company2_navigation.browser.test.js and other tests/*.test.js; names in final TAP identify actual executed cases. Unsupported operations are N/A; no missing feature was invented. Static inventory of 174 handler targets is NOT 174 end-to-end workflow passes.

Company 1/Utopiya: student create/edit/search/blank/duplicate-save guard, company rename identity/reload, Joining-Payment totals, Leave/Report navigation. Staff: create/edit/delete, company checkbox, tabs, repeat attendance, salary/payment sample. Other companies: create/edit/name uniqueness/open/isolation. Every possible button, record combination, report or payroll rule has NOT been exhaustively tested.

## Pending / unresolved

Actual Windows D: disk/ACL, installed production paths/data integrity, Windows PowerShell 5.1, Task Scheduler, SYSTEM/UAC, full service start/stop/restore/update/uninstall, physical Main–Client LAN/reconnect, native camera, physical printing/PDF driver: NOT RUN. No compiled Windows installer is delivered. New installer source is supplied.

Confirmed unresolved: shared-token service APIs lack per-user/company authorization and snapshots include existing plaintext credential fields. See AUDIT_REPORT_HI.md. UI guard passes do not resolve this server-side defect. No production migration or destructive production operation performed.
