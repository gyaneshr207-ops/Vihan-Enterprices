# D: drive / storage report

यह source/configuration contract की जाँच है। आपके actual Windows PC के configured files या disks यहाँ उपलब्ध नहीं थे। उन्हें verified कहना गलत होगा।

| वस्तु | v1.0.37 behavior / default | वास्तविक मशीन की स्थिति |
|---|---|---|
| New software application | D:\Vihan Enterprices\Software | Windows installation unrun |
| Existing software installation | Valid installation.json binding या existing LocalAppData root retained | आपका existing root unknown; automatic move नहीं |
| Central SQLite | Configured absolute D: dataDir\VihanEnterprices.db; default D:\Vihan Enterprices\Data\VihanEnterprices.db | Actual DB unavailable |
| Attachments | उसी resolved dataDir\Blobs | Actual files unavailable |
| Backups | Explicit configured absolute backupDir; default D:\Vihan Enterprices\Backups | Failure पर silent C: fallback removed; existing explicit custom destination relocate नहीं |
| Central update package/states/logs | D:\Vihan Enterprices\Updates | Source/parser tested; native update pending |
| Client update download/rollback | Existing application root\updates | इसलिए old LocalAppData app होने पर update staging भी वहीं रह सकती है |
| Client offline copy | Existing preference: D:\Vihan Enterprices\OfflineCopy, फिर C: या Electron userData fallback | Read-only client safety copy; writable master नहीं। Fallback behavior intentionally retained |
| Service config, admin key, PID/error/repair logs | %ProgramData%\Vihan Enterprices | Windows/SYSTEM shared configuration; source में C: system location रहना अपेक्षित |
| Client connection/install binding | %APPDATA%\Vihan Enterprices | Per-user Windows configuration; secret contents report में नहीं |
| Browser profile/cache | Electron userData | OS/user-specific cache; central DB नहीं |
| Temporary extraction / backup staging | %TEMP% | Final persistent backup configured destination में; temp files अलग हो सकती हैं |
| Inno source staging | Existing local Windows installer-source directory | Actual running software का root अलग है |

Main Service, File Service और restore worker shared paths helper इस्तेमाल करते हैं। Non-D central configuration या missing existing database पर service error देती है। Repair paths rewrite/migrate नहीं करती। Fresh master DB initialization केवल explicitly configured new Main setup में; File Service master create नहीं करती। Old C:/JSON data मिलने पर migration approval/verified backup के बिना startup नया खाली replacement नहीं बनाती।

SQLite tests में actual engine पर save/reopen, atomic rollback और concurrent-write-consistent snapshots passed। Linux tests ने test-only native resolver से original bridge के `winsqlite3.dll` imports को system `libsqlite3.so.0` से जोड़ा। Production code DLL import नहीं बदला। इससे SQL/bridge behavior जाँचा गया; Windows DLL, D: ACL या SYSTEM task startup certify नहीं होते।

Windows पर read-only observation:

`powershell.exe -NoProfile -ExecutionPolicy Bypass -File "<installed root>\runtime\AuditStorageSafe.ps1"`

यह script database records, passwords, tokens या admin key नहीं दिखाती, और production DB पर integrity/repair command नहीं चलाती। Output में actual application/configured storage paths और presence/size आते हैं। Token/config file contents chat में न भेजें।

## यदि relocation/migration जरूरी निकले

अभी कोई production migration execute नहीं हुई है। पहले current paths और schema inspect करें; दोनों services stopped होने पर verified offline safety copy या verified consistent SQLite backup + Blobs लें। Copy पर integrity, attachment references और record counts जाँचें। Original paths/data बनाए रखें। प्रस्तावित destination, downtime, service-task/config changes और rollback commands लिखकर फिर operator की स्पष्ट approval लें। Approval और Windows verification के बाद ही switch करें; original data तभी साफ करें जब अलग से अधिकृत हो।
