# PropIntel — Release Controller Instructions
# Read this file completely and execute the boot sequence
# before touching any file, any code, or any data.
# Last updated: 15 June 2026

═══════════════════════════════════════════════════════
🚨 CRITICAL ARCHITECTURE BOUNDARIES
═══════════════════════════════════════════════════════

Fixed project folder (one folder, always):
  ~/Desktop/PropIntel-Master/

Fixed core file manifest (exactly 6 runtime files):
  1. CLAUDE.md
  2. README.md
  3. Master excel Propintel.xlsx
  4. sync.py
  5. evidence.json
  6. index.html

Additional files policy:
  Additional files (exports, logs, screenshots) are
  permitted ONLY if:
  - Explicitly approved in the task instruction
  - Listed in README.md
  - Not used by any runtime process
  If an unapproved extra file appears: flag it.
  Do not abort — but do report it and ask whether
  to remove it.

Core rules:
  - NO duplicate runtime files
  - NO version suffixes (_v2, _backup, _old, _copy)
  - NO subfolders created by Claude
  - NO renamed runtime files
  - NO alternative sync scripts
  - NO extra JSON outputs
  - NO extra HTML versions

═══════════════════════════════════════════════════════
🛫 PRE-FLIGHT BOOT SEQUENCE
Run this before every single task. No exceptions.
Print the summary table before doing any work.
═══════════════════════════════════════════════════════

Step 1 — Confirm working directory:
  pwd
  → Must return: /Users/hollywood/Desktop/PropIntel-Master
  → If not: cd ~/Desktop/PropIntel-Master

Step 2 — Confirm core files exist:
  ls -1 ~/Desktop/PropIntel-Master/
  → Must include all 6 core files.
  → If any core file is missing: HARD ABORT.
  → If extra files exist: flag them, continue.

Step 3 — Confirm no duplicate runtime files:
  find ~/Desktop/PropIntel-Master -name "*.xlsx" | sort
  find ~/Desktop/PropIntel-Master -name "*.json" | sort
  find ~/Desktop/PropIntel-Master -name "*.html" | sort
  → Each must return exactly one result.
  → If more than one: HARD ABORT.

Step 4 — Confirm Excel row count:
  python3 -c "
  import pandas as pd
  df = pd.read_excel(
    './Master excel Propintel.xlsx',
    sheet_name='DASHBOARD_IMPORT_CLEAN'
  )
  print(f'Excel rows: {len(df)}')
  "
  → Record as EXCEL_ROW_COUNT.
  → Note: town count is dynamic (61+ towns, growing).
    Do not assert a fixed town count.

Step 5 — Confirm evidence.json row count:
  python3 -c "
  import json, os
  with open(os.path.expanduser('~/Projects/PropIntel/evidence.json')) as f:
    d = json.load(f)
  print(f'evidence.json rows: {len(d[\"rows\"])}')
  "
  → Record as JSON_ROW_COUNT.

Step 6 — Confirm row counts:
  → EXCEL_ROW_COUNT must equal JSON_ROW_COUNT.
  → If they differ: expected only when Excel was updated
    but sync has not yet run. Note the delta, flag it,
    do not treat as success. Run syncprop to resolve.

Step 7 — Confirm index.html integrity:
  wc -l ~/Projects/PropIntel/index.html
  tail -3 ~/Projects/PropIntel/index.html
  grep -c "residential_agent_helper_v71_rows" \
    ~/Projects/PropIntel/index.html
  → Line count must be >= 33344
    (Baseline before Phase 4 — will increase after
    LIVE_DATA wiring task is executed)
  → Last line must contain: </html>
  → Storage key grep must return >= 1

Step 8 — Print pre-flight summary table:
  ┌─────────────────────────────┬──────────┐
  │ Check                       │ Result   │
  ├─────────────────────────────┼──────────┤
  │ Working directory           │ [ok/FAIL]│
  │ All 6 core files present    │ [ok/FAIL]│
  │ No duplicate runtime files  │ [ok/FAIL]│
  │ Excel rows                  │ [N]      │
  │ evidence.json rows          │ [N]      │
  │ Counts match                │ [ok/GAP] │
  │ index.html lines            │ [N]      │
  │ index.html closes with </html>│[ok/FAIL]│
  │ Storage key present         │ [ok/FAIL]│
  └─────────────────────────────┴──────────┘
  HARD ABORT if: any core file missing, any runtime
  duplicate found, index.html broken.
  FLAG AND CONTINUE if: extra non-runtime files found,
  row count gap exists (sync not yet run).

═══════════════════════════════════════════════════════
🛬 POST-FLIGHT VALIDATION LOOP
Run after every task before calling anything complete.
═══════════════════════════════════════════════════════

Step 1 — Recount Excel rows:
  python3 -c "
  import pandas as pd
  df = pd.read_excel(
    './Master excel Propintel.xlsx',
    sheet_name='DASHBOARD_IMPORT_CLEAN'
  )
  print(f'Excel rows after: {len(df)}')
  "

Step 2 — Recount evidence.json rows:
  python3 -c "
  import json, os
  with open(os.path.expanduser('~/Projects/PropIntel/evidence.json')) as f:
    d = json.load(f)
  print(f'evidence.json rows after: {len(d[\"rows\"])}')
  "

Step 3 — Validate evidence.json is valid JSON:
  python3 -c "
  import json, os
  with open(os.path.expanduser('~/Projects/PropIntel/evidence.json')) as f:
    json.load(f)
  print('evidence.json: valid')
  "
  → Any error here: HARD ABORT.

Step 4 — Confirm index.html integrity:
  wc -l ~/Projects/PropIntel/index.html
  → Must be >= 33344 (unless index.html was the task,
    in which case must be >= pre-flight count)

Step 5 — Confirm no new runtime files created:
  find ~/Desktop/PropIntel-Master -name "*.xlsx" | sort
  find ~/Desktop/PropIntel-Master -name "*.json" | sort
  find ~/Desktop/PropIntel-Master -name "*.html" | sort
  → Each must return exactly one result.

Step 6 — Confirm git is clean:
  git -C ~/Projects/PropIntel status
  → Must show: nothing to commit, working tree clean
  → If not clean after a push: HARD ABORT.

Step 7 — Print post-flight report:
  ┌──────────────────────────────┬──────────────┐
  │ Validation Check             │ Result       │
  ├──────────────────────────────┼──────────────┤
  │ Excel rows (before)          │ [N]          │
  │ Excel rows (after)           │ [N]          │
  │ evidence.json rows (before)  │ [N]          │
  │ evidence.json rows (after)   │ [N]          │
  │ Row delta                    │ [+N or 0]    │
  │ evidence.json valid JSON     │ [ok/FAIL]    │
  │ index.html lines             │ [N]          │
  │ No new runtime files         │ [ok/FAIL]    │
  │ Git status clean             │ [ok/FAIL]    │
  │ Files changed this task      │ [list]       │
  │ Commit hash                  │ [hash]       │
  └──────────────────────────────┴──────────────┘

═══════════════════════════════════════════════════════
🔴 HARD ABORT RULES
═══════════════════════════════════════════════════════

STOP IMMEDIATELY if:
  - A core file is missing from the manifest
  - A duplicate runtime file is detected
  - evidence.json fails JSON validation
  - index.html line count drops below 33344
  - index.html does not end with </html>
  - git push fails after changes

When aborting, report exactly:
  1. Which check failed
  2. Expected value
  3. Actual value
  4. Delta
  5. Last known safe state
  6. Recommended fix

DO NOT self-heal. DO NOT write more code to patch.
Freeze and report.

═══════════════════════════════════════════════════════
🔧 RECOVERY MODE
If validation fails, follow this sequence only:
═══════════════════════════════════════════════════════

Step 1 — Do not edit any file.
Step 2 — Report the exact failure with values.
Step 3 — Check git log for last clean commit:
  git -C ~/Projects/PropIntel log --oneline -5
Step 4 — If instructed, restore last clean state:
  git -C ~/Projects/PropIntel checkout HEAD -- \
    [filename]
Step 5 — Re-run pre-flight validation from scratch.
Step 6 — Confirm clean state before proceeding.
Step 7 — Only continue after explicit instruction.

═══════════════════════════════════════════════════════
✅ APPROVED RUNTIME COMMANDS
═══════════════════════════════════════════════════════

TWO COMMANDS ONLY. No other sync method approved.

syncprop
  Use after: manually editing Master excel Propintel.xlsx
  What it does:
    - Reads DASHBOARD_IMPORT_CLEAN from master Excel
    - Converts rows to PropIntel evidence schema
    - Writes evidence.json to ~/Projects/PropIntel/
    - Pushes evidence.json to GitHub
    - Live terminal updates in ~60 seconds
  Runs: cd ~/Desktop/PropIntel-Master && python3 sync.py
  Do NOT call sync.py with custom flags.
  Do NOT call sync.py from any other directory.

syncintel
  Use after: downloading a new Excel from Claude/ChatGPT
  What it does:
    - Finds newest Master excel Propintel*.xlsx
      in ~/Downloads/
    - Ignores files with 'backup' in the name
    - Copies to ~/Desktop/PropIntel-Master/
    - Overwrites the existing master Excel
    - Runs sync.py
  Do NOT use after manually editing the master Excel.
  Do NOT invent alternative copy commands.

NEVER invent a third sync method.
NEVER run sync.py from any directory other than
  ~/Desktop/PropIntel-Master/
NEVER edit evidence.json manually.
NEVER use git add . — always specify files explicitly.

═══════════════════════════════════════════════════════
THE PROJECT
═══════════════════════════════════════════════════════
PropIntel — Ayrshire & Central Belt Residential
Intelligence Terminal.

Owner:    Taylor Hollywood / Wood Property Group Ltd
GitHub:   https://github.com/thollywood1888/PropIntel
Branch:   main
Live URL: https://thollywood1888.github.io/PropIntel/
Password: wpg2026 (sessionStorage: propintel_unlocked)
Git repo: ~/Projects/PropIntel/

═══════════════════════════════════════════════════════
THE TERMINAL FILE
═══════════════════════════════════════════════════════
File:    ~/Projects/PropIntel/index.html
Lines:   >= 33,344 (baseline — increases after Phase 4)
Closes:  </html>
Size:    ~2.57MB

THIS IS THE ENTIRE PRODUCT. One file. No build step.
No bundler. No node_modules. No dist folder.

ALL changes to index.html are ADDITIVE ONLY.
New code only. Paste immediately before </body>.
Never modify existing functions, IDs, storage keys,
CSS classes or event listeners.

═══════════════════════════════════════════════════════
🚫 NO-STAGING-DIFF RULE FOR index.html
═══════════════════════════════════════════════════════

Unless the task is an explicit visual or structural
redesign, any edit to index.html must:
  - Leave all existing CSS classes untouched
  - Leave all existing CSS variables untouched
  - Leave all existing layout structure untouched
  - Leave all existing DOM IDs untouched
  - Leave all existing event listeners untouched
  - Leave all existing localStorage keys untouched
  - Not re-indent or reformat existing blocks
  - Not add unrequested helper functions
  - Not add unrequested comments

The git diff must show ONLY the specific functional
logic requested. Nothing else.

If the diff shows changes outside requested scope:
STOP. Revert. Report what changed. Wait for instruction.

═══════════════════════════════════════════════════════
DATA LAYER — CURRENT STATE AND PHASE ROADMAP
═══════════════════════════════════════════════════════

Excel master:  ~/Desktop/PropIntel-Master/
               Master excel Propintel.xlsx
Sheet:         DASHBOARD_IMPORT_CLEAN
Towns:         61+ (dynamic — increases as research runs)
Rows:          720+ (growing — do not assert fixed count)
Sync output:   ~/Projects/PropIntel/evidence.json
Fetch URL:     https://thollywood1888.github.io/PropIntel/
               evidence.json
Sync mode:     Append-only. Rows never removed.

ARCHITECTURE — TWO LAYERS (understand before any task):

Layer 1 — LIVE_DATA (hardcoded JS object in index.html)
  Powers:  Town Matrix, Dashboard KPIs, town cards,
           coverage scores, yield calculations
  Status:  STATIC — not yet connected to evidence.json
  Risk:    Evidence rows increase but dashboard barely
           changes. This is expected until Phase 4.

Layer 2 — localStorage staging
  Powers:  Evidence table, Agent Helper, Evidence Export,
           BTL Scorer, BD Web Intel
  Status:  LIVE — fed by evidence.json on every page load
           via the Excel sync fetch block

PHASE ROADMAP:
  Phase 1 — Controller locked          COMPLETE
  Phase 2 — LIVE_DATA wiring           Next major task
  Phase 3 — Research loop              Ongoing
  Phase 4 — Runtime normalisation      Not yet started
             (wires Layer 1 to evidence.json at boot)

DO NOT attempt Phase 4 unless explicitly instructed.
When Phase 4 is instructed, the line count baseline
will increase. Update this file after Phase 4 completes.

Gap audit: GAP_RESEARCH_CHECKLIST sheet in master Excel
Critical gaps: ~101 (reducing as research continues)

═══════════════════════════════════════════════════════
STORAGE KEYS (exact — never vary)
═══════════════════════════════════════════════════════
Evidence rows:   residential_agent_helper_v71_rows
Audit log:       residential_agent_helper_v71_audit
Portfolio:       ayrshire_portfolio_v1
Auction:         ayrshireAuctionPortfolio
Auction filters: propintel_auction_filters
Export filters:  propintel_export_filters
Scorer last:     propintel_scorer_last
Sync timestamp:  propintel_last_sync
Gate:            propintel_unlocked    (sessionStorage)
BD API key:      bdwi_api_key          (sessionStorage)
BD zone:         bdwi_zone             (sessionStorage)

═══════════════════════════════════════════════════════
STATUS WORKFLOW (never bypass)
═══════════════════════════════════════════════════════
Pending → Needs Check → Ready To Import → Imported
                      → Rejected (always visible)
Ready To Import is NEVER set automatically. Human only.
All auto-staged rows = 'Pending'. Always.

═══════════════════════════════════════════════════════
EVIDENCE ROW SCHEMA
═══════════════════════════════════════════════════════
{
  id:           'XLSX-[10 char hash]'
  batchRef:     'MASTER-IMPORT-2026'
  town:         string (Title Case)
  propertyType: '1 Bed Flat'|'2 Bed Flat'|'2 Bed House'
  evidenceType: 'Rent'|'Sold'
  address:      string
  priceRent:    '£NNN,NNN' or '£NNN pcm'
  valueType:    'Sold Price'|'Advertised Rent'
  source:       string
  sourceUrl:    string
  dateChecked:  string
  notes:        string
  qaStatus:     string
  status:       'Pending' (always on auto-create)
  _fromExcel:   true
}

═══════════════════════════════════════════════════════
CODING RULES
═══════════════════════════════════════════════════════
1.  ADDITIVE ONLY — zero modifications to existing code
2.  Every new panel in its own IIFE:
    (function(){ 'use strict'; ... })()
3.  Unique tryInstall suffix per panel
4.  Guard: if (document.getElementById('id')) return true;
5.  Guard clause on every DOM query
6.  All strings via esc() — define locally in each IIFE
7.  Audit log capped at 80 entries
8.  No external libraries or CDN links
9.  Debounce live calculations 150ms
10. Paste target: immediately before </body>

ESC FUNCTION (define locally in every new IIFE):
function esc(v){ return String(v==null?'':v)
  .replace(/[&<>"']/g,function(c){
    return {'&':'&amp;','<':'&lt;','>':'&gt;',
            '"':'&quot;',"'":'&#39;'}[c];}); }

AUDIT LOG PATTERN (copy exactly):
(function(){
  var K='residential_agent_helper_v71_audit';
  var items=[];
  try{items=JSON.parse(
    localStorage.getItem(K)||'[]')||[];}
  catch(e){}
  items.push({
    ts:new Date().toLocaleString('en-GB'),
    msg:'...'
  });
  try{localStorage.setItem(K,
    JSON.stringify(items.slice(-80)));}
  catch(e){}
})();

═══════════════════════════════════════════════════════
DESIGN SYSTEM
═══════════════════════════════════════════════════════
Forest green dark:  #0f3d2e
Forest green mid:   #1b4332
Gold:               #B68D40
Gold light:         #ffe9a1
Background:         #F5F2EC
Text primary:       #102016
Text muted:         #4a6055
Border:             rgba(31,59,44,.13)
Card radius:        18px
Pill radius:        999px
Input radius:       10px
Font body:          'Outfit', system-ui, sans-serif
Font data:          'DM Mono', monospace
Font headings:      'Playfair Display', Georgia, serif

═══════════════════════════════════════════════════════
GIT WORKFLOW
═══════════════════════════════════════════════════════
Repo local:  ~/Projects/PropIntel/
Remote:      https://github.com/thollywood1888/PropIntel
Branch:      main

After every task:
  cd ~/Projects/PropIntel
  git add [specific files only — never git add .]
  git commit -m "type: description"
  git push origin main

Check status:
  git -C ~/Projects/PropIntel status
  git -C ~/Projects/PropIntel log --oneline -5

Types: feat / fix / patch / security / data / docs

═══════════════════════════════════════════════════════
WHAT NOT TO DO — EVER
═══════════════════════════════════════════════════════
- Do not create unapproved files
- Do not rename any core file
- Do not create subfolders
- Do not run npm, yarn, pip or any package manager
- Do not add CDN script tags to index.html
- Do not touch the password gate
- Do not change existing localStorage key names
- Do not set evidence status above Pending on auto-create
- Do not push without post-flight validation
- Do not rebuild any existing module
- Do not use git add .
- Do not attempt Phase 4 LIVE_DATA wiring unless
  explicitly instructed
- Do not self-heal after validation failure —
  freeze and report
- Do not confuse this with Domus OS, DEF-EQ, or
  any other WPG project
