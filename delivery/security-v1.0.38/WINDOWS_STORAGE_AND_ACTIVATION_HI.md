# Windows, storage, installation और migration activation

## Environment and paths

This run used Linux, Node 24, Chromium/Playwright and PowerShell 7 with a test-only native resolver mapping the production bridge's winsqlite3 import to system SQLite. No Windows VM, Inno compiler, D: disk, production database or physical Client PC was available. Native/physical checks below are PENDING, never PASS.

New software default remains `D:\Vihan Enterprices\Software`; configured central database/Blobs/backup paths and existing install root are retained. ProgramData/AppData/config/temp locations keep their Windows purposes, as detailed in the historical storage report. No existing installation was moved. New D:/C: offline directories add `Users\<OS-user identifier>`; fallback Electron userData is already per-user. Old PC-wide files are not blindly migrated/deleted. Snapshot is OS-protected and attachments AES-GCM encrypted; Windows API execution remains pending.

## Before production use

1. Create an isolated Windows Main + Client test installation from this full source ZIP. Keep original production runtime/config/DB/Blobs and a verified complete backup separately. Do not copy this source over live data during testing.
2. Compile `Vihan_Enterprices_v1.0.38_Inno_Setup.iss` with Inno Setup on Windows; record compiler/version/output checksum. This cloud run did not produce a compiled installer. Test clean install, upgrade, reinstall, cancel/UAC, uninstall keep-data, and rejected explicit deletion separately.
3. v1.0.36 first upgrade must use the new source installer workflow, because the old updater itself had failures. Merely publishing a new package does not repair that old first-hop updater. Test v1.0.36→1.0.38 and v1.0.37→1.0.38 independently. Main/File hosts and Client desktop host should move together.
4. Existing local data paths must stay unchanged unless a separately approved migration is necessary. Validate actual paths using read-only `runtime\AuditStorageSafe.ps1`. Do not paste connection files/passwords/tokens into chat.
5. Windows native smoke command (NEW isolated directory only):

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File ".\tests\WindowsNativeSmoke.ps1" -OutputDirectory "D:\VihanSecurityTest_UNIQUE"
```

This checks native SQLite save/reopen/backup, Unicode/space path and Framework crypto. It is supplied but NOT RUN here. It refuses a pre-existing output directory; it never starts production services. Its successful result would still NOT certify tasks/UAC/installer/LAN/printing.

6. In an isolated disposable Windows VM, execute Main/File start, stop/restart, simultaneous saves, actual backup/restore and failed-update rollback. Verify all 10 Company 2 sidebar options, Company 1/Utopiya, Staff, other company CRUD and custom permission presets. Use non-production accounts/data. Record first-click/highlight/no overlap, real save/reload, JavaScript errors, attachment bytes and sync results.
7. On two actual PCs, verify login, user/company denials via direct API, save/reload, concurrent edits, disconnect/reconnect, revoked access, offline expiry and protected cache access from another Windows account. Also test D: missing/read-only/ACL failures in a disposable VM; no fallback writable master should appear.
8. Provide and verify a LAN TLS/certificate/connection rollout before treating credentials/sessions as protected against network interception. HTTP-only listeners are a confirmed remaining limitation.

## Credential migration — reviewed copy first, approval before switch

Default plan (no DB open/change):

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File ".\runtime\CredentialMigration.ps1" -SourceDatabase "D:\Vihan Enterprices\Data\VihanEnterprices.db" -OutputDirectory "D:\VihanCredentialReview_UNIQUE"
```

Preparation only (separate copy, no production switch): append `-PrepareCopy`. It acquires the shared attachment/backup mutex, creates consistent SQLite safety/candidate copies plus Blobs, hashes and verifies old passwords in the candidate, removes credential-bearing old change history in the candidate, invalidates copied sessions, runs integrity check and records SHA-256. Windows ACL failure stops preparation before copying data. `before-credentials.db` deliberately contains old credentials for rollback and must remain restricted. Do not publish this directory to GitHub.

Review account/company counts, business records, attachment references, login for every affected role, candidate integrity and backup restore. Then obtain explicit operator approval for production switch and downtime. Stop both services using verified process/task ownership, retain the whole original data directory, place the verified candidate at the SAME intended data path, then start and test services. This switch was NOT performed here. No automatic deletion of the old directory is authorized.

Rollback plan: stop both services; preserve failed/new directory separately; restore the untouched original directory and matching runtime/config together. Recheck integrity and attachments. Restoring a pre-migration copy loses later writes, so plan downtime and reconciliation before approval. Old v1.0.37 runtime cannot use hashed passwords as plaintext; do not downgrade the app while keeping migrated credentials and expect login to work.

For a genuinely NEW empty initialized database only:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File ".\runtime\ProvisionSecurity.ps1" -Database "D:\Vihan Enterprices\Data\VihanEnterprices.db" -Username "admin" -InitializeNewIdentityStore
```

It prompts securely for a 12+ character Admin password, refuses existing records/missing DB, creates no known default password, and gives global Admin login from Company 1. Use Admin UI to create Staff/Users and assign company roles. Existing installations must not use fresh provisioning as a reset.

## Pending draft and offline recovery

Browser drafts with an authenticated owner remain attached to that identity. Legacy ownerless drafts are credential-scrubbed into local `__vihan_legacy_drafts_review` and are NOT automatically uploaded as a new user. Authorized Admin/operator should export/review that LOCAL recovery object, identify its original company/author, compare current Main records, then explicitly re-enter/reconcile appropriate changes. Do not paste it into chat or automatically import the whole root. Pending password changes must be re-entered through protected User Management; passwords are not retained in browser recovery copies.

New offline sync requires enrolled PC + live user login; Native desktop obtains the HttpOnly session through its trusted process, never browser JavaScript. Cache scope changes on login/logout; grants expire after 24 hours. OS secure storage unavailable means enrollment fails explicitly rather than a plaintext fallback. Legacy company-wide credential caches are not served. Their protected archival/removal on actual PCs requires the operator's reviewed cleanup procedure; this run did not delete them.

## Private draft recovery और deployment boundaries

Existing system modal में Export Private Draft encrypted JSON bundle देता है। Recover Private Draft पर bundle चुनें; owner या explicitly authorized Admin reviewed plaintext JSON download प्राप्त करता है। यह स्वतः master database में import नहीं करता। Recovery files sensitive हैं; सुरक्षित local review करें, GitHub/chat पर upload नहीं। Server `auth_draft_keys` table की consistent backup जरूरी है; password/staff-link changes पर पुरानी key केवल authorized Admin recovery के लिए retained है। First Admin login legacy recovery को seal करता है; पुराने backups/browser artifacts की reviewed cleanup अलग है। Cipher build source, exact npm lock और MIT license ZIP में हैं।

Offline last authorized principal पर Windows OS-account trust लागू है, independent disconnected per-app login नहीं। Shared Windows profile के users अलग app identities का पूर्ण isolation नहीं पाते। Separate Windows accounts इस्तेमाल करें या offline deployment disabled रखें जब तक dedicated offline-auth workflow implemented/tested न हो। Disconnected revocation तुरंत लागू नहीं होती; expiry/reconnect तक grant valid है।

Existing D: data/update trees के ACLs इस cloud run में inspect/harden नहीं हुए। Migration-created review folder की ACL logic पूरे existing installation की security certification नहीं है। Isolated Windows acceptance में actual filesystem access और ownership verify/harden करें। Main और File services दोनों की running versions verify करें; mixed version pair supported नहीं। HTTP listeners के लिए TLS/certificate rollout अभी unresolved है।
