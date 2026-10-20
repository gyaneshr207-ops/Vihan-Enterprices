# VIHAN ENTERPRICES v1.0.37 — audit और repair रिपोर्ट

स्रोत: GitHub `gyaneshr207-ops/Vihan-Enterprices`, `delivery/Vihan_Company2_Fixed.zip` (v1.0.36)। Existing application पर repairs किए गए; नया application/framework नहीं बनाया। यह release source package है, Windows-certified installer नहीं।

## महत्वपूर्ण बाकी काम — इसे “100% सभी bugs समाप्त” न मानें

1. **Server-side role/company authorization अधूरी है।** Main/File services का existing shared service-token model storage/snapshot/backup APIs पर प्रति-user और प्रति-company permission लागू नहीं करता। Browser page/sidebar guards सुधारे और test किए हैं, लेकिन valid service token रखने वाला custom client इन UI guards से बाहर API access कर सकता है। Existing snapshot में account/password fields भी शामिल हैं; passwords का existing plaintext data model नहीं बदला गया। पूर्ण repair के लिए authenticated server sessions, server-side authorization, credential handling और snapshot projection का अलग design/migration चाहिए। इस package को इस सीमा से सुरक्षित या untrusted-network deployment के लिए certified नहीं कहा गया है। यह केवल “test pending” नहीं, **confirmed unresolved architectural issue** है।
2. **वास्तविक Windows verification बाकी है:** `winsqlite3.dll`, Windows PowerShell 5.1, Task Scheduler/SYSTEM account, UAC, Inno installer/uninstaller, actual D: drive permissions, दो physical PCs की LAN/reconnect, native Electron/camera और physical printing यहाँ execute नहीं हुए।
3. **आपके PC का actual data/configuration उपलब्ध नहीं था।** कोई production `.db`, connection file या वास्तविक company dataset इस source ZIP में नहीं था। D: पर आपके actual save को इस Linux machine से certify नहीं कर सकते। `runtime/AuditStorageSafe.ps1` Windows पर read-only metadata देता है; उसमें credentials/records नहीं दिखाए जाते।
4. पुरानी `%LOCALAPPDATA%` application installation अपने-आप D: पर move नहीं की जाती। New install default D: है; existing application root और configured data preserve हैं। Legacy JSON/C:-drive data की migration अब स्वतः नहीं होगी। Existing legacy/missing-data cases में verified backup और आपकी अलग migration approval जरूरी है।

## Confirmed repairs और उनका प्रमाण

| समस्या | सुधार | प्रमाण / सीमा |
|---|---|---|
| Staff की company checkboxes CSS से hidden थीं, कोई toggle नहीं था; नया Staff चुनना रुकता था | Existing checkbox controls render करते समय दिखाए | Browser में company select करके Staff create/edit/delete |
| Company 1 Student में खाली नाम और rapid double Save से blank/duplicate records बन सकते थे | Required name + async save guard | खाली save blocked; same-tick double-save पर एक student |
| Existing company edit में दूसरे company का नाम रख सकते थे | Create/edit दोनों में case-insensitive uniqueness validation | Duplicate rename rejected; existing company identity preserved |
| User direct page call से Admin sections खोल सकता था | `page()` में Admin-section role check | Company 2 User से पांच Admin pages blocked; server authorization की ऊपर दी सीमा अलग है |
| Permission marker को high-specificity sidebar CSS override करती थी | उसी marker के अनुसार scoped inline visibility | Denied sidebar button hidden और router denial checked; colors/styles preserved |
| Selected-state observer अपनी class writes पर खुद को trigger कर सकता था | Idempotent add/remove | Actual class writes guarded; rapid navigation regression; हर possible observer sequence की performance certification नहीं |
| Backup success पर undefined `last` variable से ReferenceError | Last Backup element bind किया | Success reply पर filename/time और success message checked |
| Late status refresh, backup outcome message overwrite कर सकता था | User backup message का ownership सुरक्षित रखा | Delayed startup status के बावजूद success/error result checked |
| Main/File/restore code configured database directory ignore करती थी | Shared `StoragePaths.ps1`, same resolved D: database/Blobs contract | Path/helper tests; actual Windows paths अभी unavailable |
| Repair script बिना migration review data/backup paths बदल देती थी | Existing configuration retain; wrong/missing location पर स्पष्ट failure | Config merge/path validation tests; production migration नहीं की |
| Missing master DB पर SQLite silently empty DB बना सकती थी | Default no-create; explicit fresh initialization flag केवल Main के लिए | Actual SQLite missing-file guard; existing DB save/reopen checked |
| File Service Main से पहले empty master create कर सकती थी | Existing DB का bounded wait; File Service कभी master initialize नहीं करती | Code/parser + bridge no-create test; native boot ordering pending |
| Service startup पुराना C: data D: में अपने-आप copy करती थी | Automatic production data move removed; legacy-data detection blocks initialization | Existing/legacy detection tests |
| Backup destination failure पर silent C:/ProgramData fallback | Configured backup path honor; failed configured destination को hide नहीं किया | Source path contract; disk-failure UI test; real D: ACL pending |
| Live DB और WAL files अलग copy करने से inconsistent backup | SQLite online backup API; दोनों services के attachment/backup I/O के लिए shared mutex | Real SQLite engine पर concurrent writes के दौरान चार consistent snapshots |
| Failed group SQL commit transaction खुली/partial रह सकती थी | Failure पर transaction rollback | Actual SQL failure injection; partial write absent और next commit successful |
| एक batch में duplicate key revision checks bypass कर सकती थी | Planned revisions भी subsequent ops में validate | Actual `Plan-Ops`/`Commit-Plan` extracted-service integration |
| Attachment overwrite/delete में SQL failure पर पुराने bytes खो सकते थे | New immutable filename, metadata commit, फिर old-file removal; delete metadata-first | Partial upload और injected SQL failure पर पुराना attachment बचा; successful replacement/delete checked |
| Restore केवल SQLite header देखता था और old backup WAL ignore करता था | Staging copy पर actual integrity/schema/attachment checks; supplied WAL recovery/checkpoint | Validator code + real SQLite primitives tested; complete Windows restore/restart pending |
| Manual restore केवल Main रोकता था, File Service live रह सकती थी | Manual और remote restore का shared validated worker; दोनों tracked services | Source/parser verified; native tasks/rollback execution pending |
| Main/File service stale configuration writes एक-दूसरे का port/path बदल सकती थीं | Locked read/merge/atomic configuration replacement | Identity, custom D: path और दूसरे service port preserve होने का test |
| Frontend और desktop File Service को हमेशा Main port+1 मानते थे | Main status से configured actual File Service port discover | Nonadjacent-port HTTP/browser test |
| Stale client conflict पर expected revision बदलकर auto retry remote changes overwrite कर सकती थी | Conflicting group pending रोका; explicit operator resolution | दो browser sessions: Main unchanged, local pending retained; दोनों explicit resolution choices tested |
| Failed/local-only save पर success शब्द भ्रम दे सकता था | Student/Inquiry confirmation Main acknowledgement पर; pending स्पष्ट | Injected HTTP 500 पर pending copy और pending badge; server save claim नहीं |
| AutoUpdater में `$host` / `$pid` reserved variables से failures | Safe variable names | PowerShell AST checks और actual Backup-Current/Restore-Current execution |
| Manual update सीधे files copy करती थी; repair failure success माना जा सकता था | Shared rollback-capable updater; failure propagation | Actual rollback primitives tested; native whole update process pending |
| Failed update installed-version metadata नया छोड़ सकती थी | Metadata भी backup/rollback में | Isolated updater rollback test |
| ZIP/update paths में ambiguity/traversal और invalid version filenames | Shared validated archive extraction, duplicates/device paths reject; numeric version validation | Real ZIP extraction tests: valid spaces/nesting + पांच unsafe cases rejected before writes |
| Truncated update stream hang/partial package छोड़ सकती थी | Pipeline failure cleanup, required SHA-256, completed file के बाद publish | Real local HTTP truncated/checksum tests; old package preserved |
| Pending client save/conflict के दौरान auto update page reload | Acknowledged pending count आने तक client update defer | Pending/unknown state पर no reload tests |
| Central update publish का boolean Admin header forge किया जा सकता था | Protected admin key की server validation; UI key केवल request header में | Key matcher actual tests; native publish/install pending; overall role API issue अभी बाकी |
| Failed offline attachment refresh पुराना index reference हटाती थी | Failure पर previous usable attachment reference/bytes retain | Offline sync failure → old file retained → retry success |
| New application software location LocalAppData थी | New software install D:\Vihan Enterprices\Software; existing install binding preserve | Installer source/parser; actual Windows installation pending |
| Uninstall केवल source staging हटाती/old LocalAppData root मानती थी | Actual recognized install binding, explicit data retention/deletion, Inno uninstall integration | Source/parser reviewed; cancellation/UAC/native removal pending |
| Reset Connection cancel पर working connection मिट सकती थी / Main role खो सकती थी | Client import cancel retains config; Main reset uses repair | Source/parser; Windows file dialog pending |
| PID singleton ownership से पहले publish होती थी / PID reuse पर गलत shell रुक सकती थी | Singleton मिलने के बाद PID; maintenance verifies expected process command line | Source/parser; actual Windows process/task behavior pending |
| Health check unrelated HTTP 200/File/Offline service को Main मान सकती थी | Expected product/service identity check | Desktop health unit tests; actual Windows discovery pending |
| Camera अनुमति सभी HTTPS origins को मिल सकती थी | Configured service origins only | Allowed/denied origin tests; actual camera device pending |
| Old test में `condition || true` वास्तविक assertion नहीं थी | वास्तविक scheduled poll का इंतजार और assertion | Existing sync test अब वास्तविक condition check करती है |

## Modules / scope

- Company 2: पहले वाले नौ module navigation और Admin वापसी tests फिर चलाए; सभी 81 transitions प्रति role, same-tick switching, async race, draft/file/photo preservation। Existing Inquiry, Counter PDF, Day Book, Bank Book, Cash Main Counter, Data Entry, Photo, Documents और Software functions के regression tests साथ हैं।
- Company 1 / Utopiya Library: source में student module `studentCompanyId`/module binding से जुड़ा है; अलग Utopiya code/database नहीं मिला। Fixture में Company 1 को Utopiya Library rename करके identity, student menu और reload जाँचा। Student CRUD के create/edit/search/validation paths, Joining/Payment linkage, Leave/Report navigation और totals exercised हैं। वास्तविक Utopiya dataset नहीं मिला।
- Other companies: generic company create/edit, open/return, duplicate-name guard और cross-company record isolation checked। Source में unrelated company-specific business modules मौजूद न हों तो उन्हें बनाया नहीं।
- Staff: company selection, create/edit/delete, tabs, Day Attendance repeat-save, salary/payment ledger sample balance checked। हर payroll policy/month/attendance combination का exhaustive audit नहीं।
- Static DOM inventory: 174 existing button-handler targets resolve हुए। यह 174 buttons के हर workflow का end-to-end pass प्रमाण नहीं है; JSON inventory और अलग executed tests देखें।
- Print checks document/frame + `window.print()` invocation तक हैं; physical printer/PDF driver नहीं। Optional remote Photo AI model inference नहीं; existing original-image fallback tested है।

## Preservation

Application source के सभी existing `<style>` blocks byte-for-byte समान रखे हैं। Existing fonts/colors/layout को redesign नहीं किया। Functional repair में hidden existing Staff controls और permission visibility के scoped runtime changes जरूरी थे। No production database, attachments या service file was opened, moved, reset or deleted. Only isolated fixtures/temp databases were used. Electron runtime pin 38.2.0 retained; original SHA-256 official release checksum से match किया, verification disabled नहीं की। Current Electron security-support status इस audit में assess नहीं किया गया।

Final test counts और exact executed commands `TEST_REPORT_HI.md` / `RELEASE_VERIFIED.tap` में हैं। Source diff/file hash सूची `FILE_CHANGES.json` और `SOURCE_CHANGES.patch` में है। D: paths और installation guidance अलग reports में हैं।
