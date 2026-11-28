![Ion: ion · Privacy Act Management](docs/assets/ion-social-preview.png)

# Ion
Privacy Act Management: small, read-only tools that help a DAF unit find and protect PII in Microsoft 365 (AFI 33-332).

**DoD365 PII Reporter**

<!-- site:intro -->
Keep your eyes on the files in local, network, and cloud directories you can access. Ion checks them for Personally Identifiable Information (PII) and gives you a comprehensive report: an Excel workbook that summarizes and categorizes its findings. It only reads, and every value it finds is masked.
<!-- /site:intro -->

---

### 🔺 Summary

<!-- site:key-facts -->
<table>
<tr><td><b>Universal:</b></td><td>Ion scans every type of file in the chosen directory</td></tr>
<tr><td><b>Credentials:</b></td><td>CAC sign-on is needed to scan cloud directories</td></tr>
<tr><td><b>Access:</b></td><td>Only sees what your account can see</td></tr>
<tr><td><b>Read-only:</b></td><td>it makes no changes to anything it scans</td></tr>
<tr><td><b>Accountable:</b></td><td>see which account first saved each PII value, and when</td></tr>
<tr><td><b>Boundary:</b></td><td>nothing leaves DoD365; the export is saved to your computer</td></tr>
<tr><td><b>Verifiable:</b></td><td>inspect the commented code yourself within a .txt before execution</td></tr>
<tr><td><b>Accessible:</b></td><td>paste and run via ISE; no download, no admin rights, no policy change</td></tr>
</table>

> Have concerns? Speak with your WCO.
<!-- /site:key-facts -->

---

[**Open the script**](https://raw.githubusercontent.com/rhope-ops/ion/main/paste/Ion-script.txt)

The steps to run it are at the top of the script.

---

### 🔺 How it works

<kbd>Sign in</kbd> → <kbd>List and confirm</kbd> → <kbd>Read files in memory</kbd> → <kbd>Find PII</kbd> → <kbd><b>Mask it</b></kbd> → <kbd>Report</kbd>

- **Paste check:** when you press F5, Ion reads the pasted text back and compares its SHA-256 with the value written at the build. If the text is changed, nothing runs and you see "Paste check: STOPPED". If the text is cut short, ISE shows a red error and nothing runs. If Ion cannot read the text back, you see "Paste check: not done" and the scan continues. The Settings lines stay free to edit.
- **Sign in:** paste a Teams link (... next to the team or channel name > Copy link), a SharePoint site address or a drive folder. A Teams link alone is enough: Ion looks up that one team's site. A CAC sign-in window opens when one is needed: on the gov device, 2 windows opened for 4 sign-ins (probe 3, 2026-11-24). If the lookup fails, Ion asks for the site address. If the SharePoint sign-in does not work on your computer (for example, it is blocked), Ion shows how to sync the library with OneDrive and scan the synced folder instead.
- **Speed:** after the targets, Ion shows a note on each mode and asks "Speed: [N]ormal or [F]ast?". Enter selects Normal: one reader for each 2 processors, at least 1 reader and at most 8 readers. Type `f` for Fast: one reader for each processor, at most 8 readers. From a team or site, Normal downloads 2 files at a time and Fast downloads 3. In both modes, the readers and downloads run at below-normal Windows priority, so other programs come first.
- **List and confirm:** Ion lists the files first and shows how many it found, their size, and how many it will list but not read. When it finds files, it asks "Scan now? [Y/n]"; Enter starts the scan, and "n" saves nothing.
- **Several in one run:** after the first link, site or folder, Ion asks "Add another"; paste the next one, or press Enter to start. With two or more, Ion lists them and asks "Scan these N now? [Y/n]" once, then scans them one after the other (no sign-out between the sites, so a site on the same host usually needs no new sign-in window; Ion signs out one time, at the end of the run), with one workbook each and no question per site except the OneDrive scope question. At the end, one line per target shows its workbook, or why there is none, and Ion offers to open the results folder. Ion touches only what you paste. Ctrl+C stops the whole run: the target in work saves its results so far, and the targets after it are not scanned.
- **Progress:** one bar shows the folder, the run so far (files done, read, not scanned, possible PII, time elapsed and left) and the file in work. For a SharePoint or Teams file of 1 MB or more, a second bar, "Downloading...", shows the MB downloaded. A download goes into memory only, never to disk, and is gone when the file is read.
- **Report:** one Excel workbook per run, in `Documents\Ion\<site>\Ion <site> <yyyy-MM-dd HHmm>.xlsx` (a drive folder gets a folder of its own name; a site or folder name longer than 40 characters is cut to 40 in the path). It has a Summary tab (totals, and hits per type that update as you review), Hits (every hit in one Excel Table, worst first, with an ID per hit, to filter, sort or pivot), Files (one row per file, with its counts and how many of its hits are reviewed), Not scanned, By account (when an account is named) and, last, Method. Dates are date cells; every tab prints landscape, one page wide, marked CUI. Each hit row shows when the file was created and by whom, when it was last saved and by whom, which account first saved a version with the PII and when, and a History note when the file's history cannot show that. For a file on SharePoint or OneDrive on the web, the Hits and Files tabs also show Own permissions (Yes or No) and Access entries: who has access, counted by kind (people, site groups, team groups, sharing link groups, the whole organization), never by name; SharePoint shows the entries to site owners only. Five empty columns, right after the hit, are for the reviewer to fill in: Is it PII? (Yes, No, Not sure), Action taken (Removed, Moved, Access limited, Left (not PII), Other), Reported to Privacy Manager (Yes, No, Not needed), Reviewed by and Review date. The By account tab counts files and High, Medium and Low hits per "first saved by" account. The Method tab explains, for each PII type, when a match is Low, Medium or High, and how each column is worked out: the "first saved by" account is evidence, not proof of who typed the PII.

#### What it looks for

<!-- site:looks-for -->
| Type | Found when | Shown as |
|---|---|---|
| SSN | 3-2-4 digits the SSA could issue | `***-**-1234` |
| DoD ID | 10 digits after "DoD ID", "DODID", "EDIPI" or "CAC"; or in an e-signature (Low; High with one of those labels): after a CAC name (LAST.FIRST.M. then 10 digits, also after a gap; in small letters after a signature word; in capitals without dots before an SSN shape and one more digit), or after "signed", "signature", `CN=` or "DN:", or just before a CAC name; also with spaces or line breaks inside | `******7890` |
| Date of birth | A date after "DOB", "date of birth" or "born" | `[date masked]` |
| Payment card | 13 to 19 digits with a card network's prefix that pass the card check | `************1111` |
| Passport | 6 to 9 letters and digits after "passport" | `*****567` |
| Phone | A US 10-digit number written with separators (dashes, dots, spaces or brackets); 10 digits in a row are not found | `***-***-0142` |
| Personal e-mail | Any address not ending in .gov or .mil (Gmail, Yahoo and the like rank higher) | `***@domain` |
| Home address | Number, street name and street type (St, Ave, Rd ...) | `[address masked]` |
| Name | After "Name:", "Full name:", "Member:", "Spouse:", "Dependent:", "NOK:" or "POC:" | Initials |
| PII in a file or folder name | Any of the above in the path | As above |
<!-- /site:looks-for -->

---

### 🔺 Limitations

| Not scanned | What to do |
|---|---|
| Channel posts and replies | Requires tenant admin rights |
| Private and shared channels | Each has its own SharePoint site: run Ion again with that site's link |
| Subsites | Run Ion again with the subsite's link |
| A link (symbolic link or junction) to another folder or file inside a scanned drive folder | Listed as NOT SCANNED (a link can lead outside the folder): check it by hand |
| Other reparse points: deduplicated or Azure File Sync files, and any outside a OneDrive folder | Listed as NOT SCANNED (a link can lead outside the folder): check it by hand |
| Your whole OneDrive folder (it can hold shortcuts to other teams' libraries) | Ion lists its top folders and asks before it scans them all; to scan one team, paste that team's folder |
| A folder in your own OneDrive | Ion signs in once (a CAC sign-in window can open) to that OneDrive only. It reads the files on this computer from the disk. It reads online-only files and files that are only on the web into memory: nothing is saved to the computer. "PII first saved" and "PII first saved by" come from the web version history. If the sign-in fails, Ion reads the files on this computer only, and the Summary says so |
| A synced team library or a shortcut in OneDrive | As for your own OneDrive. Ion signs in once. It reads the history and the online-only files from the web folder that the OneDrive app names. If the app names no web folder, Ion scans on this computer only. The Summary says so |
| OneDrive files that are online only, when the web part is not used | Ion shows how many and their MB, and asks before it downloads them; on "no" they are listed as NOT SCANNED. Downloaded files stay on the computer until you click Free up space |
| Style Library and Form Templates | SharePoint's own files, not people's: skipped on purpose |
| Files with a sensitivity label that encrypts them (Microsoft Purview) | Listed as NOT SCANNED, in a group of their own: open them and check by hand |
| Encrypted or password-protected files, and PDFs with limits set by their author | Listed as NOT SCANNED, in a group of their own: check them by hand |
| A PDF that takes more than 2 minutes to read (large or complex; the file is usually fine) | Listed as NOT SCANNED ("PDF time limit reached"): check it by hand |
| A file (or an old version of it) that takes more than 3 minutes to read, or a SharePoint download that waits more than 5 minutes (or the connection's own shorter limit) | The file is listed as NOT SCANNED and the scan goes on: check it by hand (a file list that waits more than 5 minutes stops the scan as INCOMPLETE) |
| Parts of a PDF written in a way the reader does not follow (the file may still open fine) | Listed as NOT SCANNED ("PDF parts not read"); the rest of the file is still checked |
| Files over 25 MB | Listed as NOT SCANNED: check them by hand (the limit is a setting) |
| Scripts, programs, audio, video and other file types Ion does not read | Listed as NOT SCANNED: check them by hand |
| An archive or e-mail nested more than two levels deep | Listed as NOT SCANNED: open it and check by hand |
| Attachments of an Outlook .msg | A note says they were not read: open them and check by hand |
| Images and scanned PDFs on a computer without Windows OCR | Listed as NOT SCANNED: check them by hand |
| Scanned PDF pages not stored as JPEG images | Not read with OCR: check them by hand |
| A scanned page with a hidden text layer (a searchable scan) | Ion reads its text layer only, not the image |
| A picture on a typed PDF page that is less than 800 pixels wide or high (a logo, or a small scan such as an ID card) | Not read with OCR. If a side is 300 pixels or more, the file gets a "check by hand" row on the Not scanned tab |
| Symbols in picture fonts (Wingdings, Webdings, Dingbats, Marlett, Symbol) in a PDF | Not read as text, so not checked for PII |
| Text in a PDF font with no character map and made-up glyph names (such as g12) | Not read; the file gets the note "A font in this PDF could not be read": check it by hand |
| Old .doc .xls .ppt and Outlook .msg | Read in part (numbers in an old .xls are not read): check these files by hand |
| A Low-confidence match inside text that is mostly unreadable characters | Dropped as noise; High and Medium matches are always kept |

---

### 🔺 If it finds PII

<!-- site:breach -->
> [!CAUTION]
> PII where it should not be is a potential or actual breach; report it to your servicing Privacy Manager/Monitor without delay.
>
> AFI 33-332 para 3.1.2, as changed by AFI33-332_DAFGM2026-01 (7 July 2026), page 1, second paragraph.
<!-- /site:breach -->

---

### 🔺 Safety

- **Read-only:** never changes, moves or deletes scanned content.
- **Scoped:** scans only the location you give; lists no other sites, teams or users.
- **No stored credentials:** you sign in with your CAC each run; no password or token is saved.
- **Masked output:** every found value is masked on screen and in the workbook; links open the file or folder without showing PII.
- **Formula-safe workbook:** all text is stored as plain text, so scanned content cannot run as an Excel formula.
- **Fails safe:** on an error or Ctrl+C during the scan, the workbook is still saved and marked INCOMPLETE, and Ion signs out of SharePoint. A "no" or a cancel at "Scan now?" reads and saves nothing.
- **Handle the workbook as CUI:** file names and masked fragments still show where PII is.

---

### 🔺 Files

Each file starts with a header: purpose, inputs, outputs, how to run, run target, and the three safety lines `Network:`, `Writes:`, `Credentials:`. Read those before you run anything.

| File | What it does |
|---|---|
| `src/Find-ChannelPII.ps1` | Entry point: address or folder, sign-in for cloud sites, folders, files, results |
| `src/ChannelScan.psm1` | What you paste (Teams link, site address or drive folder), Teams site lookup, CAC sign-in, libraries, and parallel downloads |
| `src/DriveScan.psm1` | Scans a folder on a mapped or network drive (`X:\folder` or `\\server\share\folder`) |
| `src/ReaderPool.psm1` | Reads several files at once in PowerShell runspaces, and each identical file only once per run |
| `src/ScanQueue.psm1` | Live progress bars and masked per-folder lines, turns reader pool results into report rows, and asks "Scan now?" |
| `src/Accountability.psm1` | Who made and changed a file with PII, and who put the PII in and when |
| `src/TextExtract.psm1` | Turns file bytes into plain text, in memory, and picks the reader for each file type |
| `src/PdfExtract.psm1` | The PDF reader, with .NET only: limits, stream filters, objects, fonts, word spacing, page text |
| `src/ContainerText.psm1` | Text from images (Windows OCR), old .doc/.xls/.ppt and Outlook .msg files, and .zip archives / .eml e-mails |
| `src/PiiEngine.psm1` | One detector per PII type, e-signature DoD ID, overlap removal, and masking of every value and snippet |
| `src/ReportExport.psm1` | Screen summary, severity order, workbook tabs, save path, and .xlsx writer with .NET only (no Excel, no module) |
| `tests/Run-Tests.ps1` | Runs all tests (invented data, fake PnP cmdlets, no network) |
| `tests/TestKit.ps1`, `tests/FakePnP.ps1` | The test assertion and samples; the fake PnP cmdlets |
| `tests/*.Tests.ps1` | Tests per module, a start-to-end run, and a run of the paste text |
| `tests/Lint.ps1` | Windows PowerShell 5.1 syntax check (PSScriptAnalyzer) |
| `paste/Ion-script.txt` | GENERATED: the one text you paste into ISE (the run steps, a Settings block, the modules and the script); the README and the site link it |
| `scripts/build_paste.py` | Builds the paste text from `src/`; `--check` fails when it is out of date |
| `scripts/build_paste_test.py` | Self-test of the paste build and of the script links |
| `scripts/site_page.py` | Checks the site page: size, security metas, skip link, live region, image files and the WCAG contrast of its colours (run by the site check) |
| `assets/logo-33-332z.svg`, `assets/logo-core.svg` | The two logos of the site page |
| `docs/MAINTAINER_GUIDE.md` | Steps to change Ion, rebuild the paste text, change the site page and check the work |
| `site.config.json` | The one home of the repo's GitHub owner, name, branch and script path (`scripts/build_paste_test.py` checks the links against it) |
| `ref/afi33-332.pdf`, `ref/afi33-332.txt` | The rule source: AFI 33-332 with AFI33-332_DAFGM2026-01 |
