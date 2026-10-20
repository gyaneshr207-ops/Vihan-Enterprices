# Company 2 navigation repair — v1.0.36 source

आधार: उपयोगकर्ता का पूरा `Vihan_Enterprices_Update_v1.0.36.zip`। कोई नया application नहीं बनाया गया। Application का सिर्फ `app/index.html` बदला है। सभी original CSS/style blocks समान हैं; button color, आकार, spacing, font और sidebar/page designs नहीं बदले।

## मिले दोष और सुधार

1. **Dedicated page से सामान्य page पर blank workspace:** original ZIP में All Software/Photo/Documents से Inquiry, Counter Account File या Day Book चुनने पर sidebar सही चुना जाता था, पर `companyHome` active नहीं होता था। ब्राउज़र baseline में visible `.page.active` सूची खाली मिली। Existing unified router अब सही parent page और केवल चुने workspace को दिखाता है।
2. **देर से पूरा load नई navigation को उलट देता था:** original ZIP पर Documents → Day Book → Software → Inquiry का rapid test किया। अंतिम अनुरोध Inquiry था, लेकिन delayed completion के बाद All Software selected और visible मिला। Router के पुराने promise-success callbacks पुराना view/selection दोबारा लगा रहे थे। अब synchronous page selection/visibility एक स्थान पर commit होती है; पुराने async completions उसे नहीं बदलते।
3. **Admin Dashboard छिप सकता था:** सामान्य `page()` navigation जिस `v843ClearDedicatedCompany2View` hook को बुलाती थी, वह source में परिभाषित नहीं था। Hook अब उसी existing router में है; dedicated-view attribute और page state साफ होती है।
4. **एक ही sidebar action के कई handlers:** builder के नौ direct onclick handlers, उसके ऊपर permission onclick wrappers, अलग selected-state listener और Documents/Software के sidebar-hide listeners एक ही काम के अलग रास्ते थे। Existing capture router पहले ही इन नौ mapped sidebar clicks को intercept करता था, इसलिए ये duplicate paths अनावश्यक थे। उन्हें हटाया; module entry points और permission guards रखे। कोई दूसरा router/navigation wrapper नहीं जोड़ा।
5. **दोबारा click या page switch पर draft reset:** selected module को फिर click करने पर form/photo दोबारा बनता था। अब same-module click data reset नहीं करता। Shared workspace में अधूरे edited forms के actual DOM nodes को अस्थायी memory fragment में रखा जाता है, जिससे file selection और listeners भी बचते हैं। Day Book और Photo के अधूरे controls उसी session/company scope में सुरक्षित रहते हैं। यह disk autosave या reload/crash recovery नहीं है।
6. **Selection और render failure:** चयन page खोलने के बाद commit होता है। Missing/throwing module का injected test किया; पिछला page और parked draft usable रहा। Inquiry report को draft restore के दौरान उसके original parent में सुरक्षित रखने का regression test भी है।

## सभी 10 विकल्पों की navigation जाँच

नीचे “पास” वास्तविक Linux Chromium execution का परिणाम है। Main PC और Client PC की **Windows/LAN जाँच इस तालिका में पास नहीं मानी गई है**। उसी supplied app की दो independent browser sessions और HTTP-contract fixture से compatibility/sync अलग जाँचे गए।

| विकल्प | First click / सही page | केवल सही highlight | कोई page overlap / uncaught JS error नहीं | Rapid switching | कार्यात्मक जाँच |
|---|---|---|---|---|---|
| Admin Dashboard | पास; हर Company 2 module से वापसी | पास; एक चयन | पास | पास; dedicated pages से भी | Dashboard खुलना, Back to Admin, Company 2 दोबारा खोलना |
| Inquiry Form | पास | पास; existing green | पास | पास | Modal, Save/Edit/Delete, search, Pending/Approve toggle, print invocation, service-fixture save |
| Counter Account File | पास | पास | पास | पास | PDF upload/save, search, description update, viewer open/close, download byte comparison, delete |
| Day Book | पास | पास | पास | पास | Entry save/edit/delete, draft retention, transaction search/type filter, print invocation, Reports/Ledger tabs |
| Bank Book | पास | पास | पास | पास | Bank settings create/edit/delete; entry save/edit/delete; search/filter/report/print invocation; draft retention |
| Cash Main Counter | पास | पास | पास | पास | Opening adjustment prerequisite, transfer save/edit/delete, report/settings/all-counter report tabs, draft retention |
| Data Entry | पास | पास | पास | पास | Form + PDF selection retention, save/edit history/delete, View/Settings, attachment save, page reload |
| 4×6 Photo | पास | पास | पास | पास | Upload, existing original-image fallback, background/rotation controls, navigation में photo/control retention, PNG/JPG download, print-frame invocation, reset |
| All Documents | पास | पास | पास | पास | Folder create/rename/open/return/delete; file upload/search/type filter/edit/download/delete; index export |
| All Software | पास | पास | पास | पास | Folder create/rename/open/return/delete; file upload/search/edit/download/delete; index export |

दो login roles (Super Admin और Company 2 User) पर नौ modules की 81-81 directed transitions जाँचीं। प्रत्येक role पर 27 same-tick rapid clicks के बाद final selection/visible page भी जाँचा। Green `#0f9f6e`, normal blue `#3559db` तथा single selection browser में सत्यापित हैं। Existing CSS hover/transition behavior सुरक्षित है; Admin Dashboard का अपना original highlight style रखा है। Permission-denied और missing/throwing-module scenarios अलग tests हैं; intentionally injected errors सामान्य navigation failures नहीं हैं।

Main/Client browser test में Main session पर draft बनाए रखा, Client session पर सभी modules चलाए, actual sync JavaScript से inquiry को fixture में save कराकर दूसरे browser context तक पहुँचाया और visible report में जाँचा। यह real Windows MainService या SQLite का test नहीं है।

## बदली और नई files

**बदली application file:** `app/index.html` — केवल Company 2 sidebar dispatch, page visibility, selection, exit cleanup और navigation draft handling।

**नए regression tests:**

- `tests/company2_navigation.browser.test.js`
- `tests/company2_functions.browser.test.js`
- `tests/helpers/company2-browser-harness.js`

**नए review/delivery documents:** यह रिपोर्ट, `reports/TESTING.md`, `reports/automated-tests.tap`, `reports/original-file-integrity.json`, `reports/company2-navigation.patch`।

Original ZIP की 31 files की तुलना में केवल `app/index.html` बदला। बाकी 30 original files byte-for-byte समान हैं। Installer scripts, Electron runtime, Main/File services, SQLite code, DB paths, Backup/Restore, Login, Central Auto Update और sync script नहीं बदले। Company 1, Utopiya Library और अन्य companies का implementation नहीं बदला। Input ZIP में production `.db` या user dataset नहीं था; कोई production data इस्तेमाल/बदला नहीं गया। SHA-256 comparison JSON और exact patch साथ हैं।

## परीक्षण की सीमा / Windows installer

- यहाँ Linux, Node.js 24.19.0, Playwright 1.62.1 और Chromium 154.0.8037.57 इस्तेमाल हुए।
- HTTP fixture isolated test storage/files रखता है। **Actual Windows MainService SQLite save/reload, दो physical PCs की LAN connection, real Electron UI, printer drivers, UAC, native Backup/Restore और Central Auto Update installation execution नहीं किए जा सके।** Existing automated offline/launcher/sync regression tests simulated dependencies के साथ चलाए गए हैं।
- Print tests document/frame और `window.print()` invocation तक हैं; physical printing या OS PDF-printer output नहीं जाँचा। Counter PDF viewer की visibility/close और original download bytes जाँचे; सभी प्रकार के PDF rendering/content का exhaustive test नहीं।
- Photo का remote AI model inference नहीं जाँचा; deliberately unavailable CDN पर existing original-photo fallback चला। इसकी navigation/controls/download/print tests पास हैं।
- Tests प्रत्येक page की ऊपर सूचीबद्ध representative functions जाँचते हैं। हर advanced report, transfer variant, permission combination, large dataset और सभी possible feature combinations का exhaustive certification नहीं है।
- Windows और Inno Setup compiler (`iscc`) / Wine उपलब्ध नहीं थे। **Compiled updated `.exe` installer नहीं बनाया या सत्यापित किया गया।** पूरा repaired source ZIP में original `.iss`, `installer/` और `runtime/` शामिल हैं; Windows पर supplied Inno Setup source उसी repaired `app/` को package करेगा। Existing version metadata `1.0.36` रखी है; same-version central-update acceptance का दावा नहीं है।
- Windows release acceptance बाकी: fresh backup के साथ existing installation workflow से Main और Client पर install/update जाँचना; दोनों पर सभी 10 options; actual SQLite save/reload; attachments; backup/restore; central update; actual print और repeated/rapid clicks। इन्हें यहाँ pass नहीं लिखा गया है।

पूरा automated run, exact executed test names और final counts `automated-tests.tap` में हैं। Re-run instructions `TESTING.md` में हैं।

अंतिम संयुक्त run: **52 tests पास, 0 failed, 0 skipped** (34 original regression tests + 18 नए browser tests)। Command exit status 0। विस्तृत TAP evidence साथ है।
