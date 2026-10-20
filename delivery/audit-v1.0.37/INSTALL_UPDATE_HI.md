# Installation / update / rollback guidance

यह complete source package है। Compiled Windows `.exe` नहीं दिया गया: Windows/Inno compiler उपलब्ध नहीं थे। `Vihan_Enterprices_v1.0.37_Inno_Setup.iss` में updated packaging और uninstall integration है, लेकिन Windows compilation/execution बाकी है।

1. पहले isolated Windows Main + Client test environment में installation/upgrade जाँचें। अपने production software पर सीधे deployment को इस Linux test run का प्रमाण न मानें।
2. Existing database path/data को move/reset न करें। Backup को actual SQLite integrity और attachments के साथ verify करें। Existing v1.0.36 live-file-copy backup path में audit issue मिला था; उसे बिना verification safety proof न मानें।
3. ZIP निकालकर Windows PowerShell में supplied `installer\Install.ps1` चलाएँ। Existing configured installation के लिए `-Mode Auto` connection/role retain करता है। New machine के लिए `-Mode Main` या `-Mode Client` चुनें। Source installer का working directory extracted package रहे।
4. New software default `D:\Vihan Enterprices\Software` है। Existing LocalAppData application automatically relocate नहीं होती। D: न मिलने पर new D: installation fail करेगी। Configured master DB missing/non-D होने पर नया empty DB बनाकर उसे successful installation नहीं कहा जाएगा।
5. Main service repair के लिए UAC/SYSTEM scheduled tasks की native verification आवश्यक है। Error पर `install.log`, `%ProgramData%\Vihan Enterprices\service_error.txt`, `file_service_error.txt` और `service_repair.log` देखें; credential file contents share न करें।
6. Manual update और Central Auto Update अब shared rollback-capable worker पर हैं। Updated package में numeric manifest/version और required app/runtime files चाहिए। Publish करने के लिए protected Main admin key जरूरी है; इसे browser storage में save नहीं किया जाता।
7. **v1.0.36 से पहली upgrade के लिए नया supplied installer workflow test/use करें।** पुराने AutoUpdater में reserved PowerShell-variable failures मिले थे; सिर्फ `.veupdate` को old updater पर भेजकर first-hop success मानना सही नहीं। v1.0.37 का update package future/native validation के लिए साथ है; remote publish/apply यहाँ execute नहीं हुआ।
8. Failed application update में app/runtime/desktop host और installed-version metadata का rollback है। Actual helper functions का isolated rollback test हुआ; Windows service restart/relaunch अभी unrun है। Logs में success न हो तो operator verify किए बिना data-cleanup न करें।
9. Restore source archive को preserve करता है; inbox copy isolated staging में validate होती है। Current data directory before-restore suffix के साथ retained रहती है। Native restart और actual restore path Windows पर जाँचें; इस audit ने production restore नहीं चलाया।
10. Uninstall में existing explicit data-keep/delete confirmations हैं; normal keep-data path business DB/Backups को रखना चाहिए। D:/legacy install binding support और Inno hook added हैं। UAC, cancel ordering, SYSTEM task deletion और keep-data behavior का वास्तविक Windows test बाकी है।

पूरा per-user server authorization अभी unresolved है; `AUDIT_REPORT_HI.md` की पहली सीमा पढ़ें। User interface permission tests को server API security certification न मानें।
