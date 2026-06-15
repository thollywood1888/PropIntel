# PropIntel — Claude Code Master Context
# THIS IS THE ONLY CLAUDE.MD FOR THIS PROJECT
# Last updated: 15 June 2026

═══════════════════════════════════════════════════════
THE PROJECT
═══════════════════════════════════════════════════════
PropIntel — Ayrshire & Central Belt Residential
Intelligence Terminal.

Owner:    Taylor Hollywood / Wood Property Group Ltd
GitHub:   https://github.com/thollywood1888/PropIntel
Branch:   main
Live URL: https://thollywood1888.github.io/PropIntel/
Password: wpg2026 (sessionStorage key: propintel_unlocked)
Local:    ~/Projects/PropIntel/index.html

═══════════════════════════════════════════════════════
THE MASTER EXCEL FILE — ONE FILE, ONE TRUTH
═══════════════════════════════════════════════════════
Name:     Master excel Propintel.xlsx
Location: ~/Desktop/MASTER-PROPINTEL-EXCEL/
Sheet:    DASHBOARD_IMPORT_CLEAN (710 rows as of 15 Jun 2026)

This is the ONLY Excel file for this project.
No other Excel file exists. No other file is referenced.
All new evidence rows go into DASHBOARD_IMPORT_CLEAN only.

Columns in DASHBOARD_IMPORT_CLEAN:
  Evidence Type, Town, Property Type, Price,
  Address / Area, Postcode, Date / Period,
  Source Name, Source URL, Notes,
  QA / Benchmark Status, Original QA Status,
  Evidence Quality, Source File, Source Sheet,
  Source Row, Duplicate Count Removed,
  Source Row Count In Group, Dedupe Key

═══════════════════════════════════════════════════════
THE SYNC WORKFLOW
═══════════════════════════════════════════════════════
Script:   ~/Desktop/MASTER-PROPINTEL-EXCEL/sync.py
Output:   ~/Projects/PropIntel/evidence.json
Mode:     Append-only (rows never removed)
URL:      https://thollywood1888.github.io/PropIntel/evidence.json

To sync after updating Excel:
  cd ~/Desktop/MASTER-PROPINTEL-EXCEL
  python3 sync.py

═══════════════════════════════════════════════════════
THE TERMINAL FILE
═══════════════════════════════════════════════════════
File:   ~/Projects/PropIntel/index.html
Lines:  ~33,153
Size:   2.57MB
Closes: </script></body></html>

THIS IS THE ENTIRE PRODUCT. One file. No build step.
No bundler. No node_modules. No dist folder.
ALL changes are ADDITIVE ONLY — paste before </body>.

═══════════════════════════════════════════════════════
FILES IN REPO — EXACTLY THREE
═══════════════════════════════════════════════════════
~/Projects/PropIntel/
├── index.html      ← The terminal. ~33,153 lines.
├── evidence.json   ← 710 rows from master Excel.
└── CLAUDE.md       ← This file.

DO NOT create any other files in the repo.

═══════════════════════════════════════════════════════
CURRENT DATA STATE (15 Jun 2026)
═══════════════════════════════════════════════════════
Total rows:          710
Towns covered:       53
Matrix points:       318 (53 towns × 3 prop types × 2 ev types)
CRITICAL gaps (0):   111
WEAK gaps (<3):      100
OK (3+ rows):        107 (34% of matrix)

Strongest towns: Ardrossan(36), Largs(35), Ayr(30),
                 Hillhead(30), Cathcart(29)

Research priority: Fill CRITICAL gaps first.
Target: minimum 3 rows per town/property/evidence combo.

═══════════════════════════════════════════════════════
MODULES IN index.html (current as of 15 Jun 2026)
═══════════════════════════════════════════════════════
01 Command Centre
   Dashboard, Market Overview, Hotspots, Analytics,
   Export Report

02 Town Intelligence
   All Towns (64), Ayrshire, Glasgow, Central Belt,
   Town Directory, Town Detail panels

03 Deal Lab
   Manual Deal Underwriter (LBTT/ADS Scottish rates),
   AI Deal Analyser (Claude via Netlify edge fn),
   Rent vs Value Calculator,
   BRRR / Refinance Calculator,
   Flip Calculator, Deal Pipeline

04 Comparator & Evidence
   Town Comparator, Comparable Evidence Browser,
   Research Control, Agent Helper,
   Local Agent Runner, Evidence Pipeline,
   BD Web Intel (Bright Data plugin),
   Evidence Export (panel id: evidence-export-panel),
   BTL Deal Scorer (panel id: btl-scorer-panel),
   Watchlist, Portfolio

05 Macro Briefings
   Interest Rates, Mortgage Rates, BTL Lending,
   Policy/Regulation

06 Live Research Links
   Rightmove, Zoopla, Auctions

Additional:
   Password gate (top of body)
   Excel Evidence Sync (bottom of body, excel-sync-bar)
   Auction Intelligence (12 lots, Leaflet map, Deal Lab)
   Data Intake (template generator, coverage tracker)

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
                      → Rejected (stays visible always)
Ready To Import is NEVER set automatically. Human only.
All auto-staged rows = 'Pending'. Always.

═══════════════════════════════════════════════════════
EVIDENCE ROW SCHEMA
═══════════════════════════════════════════════════════
{
  id:           'XLSX-[10 char hash]'
  batchRef:     'MASTER-IMPORT-2026'
  town:         string (Title Case)
  propertyType: '1 Bed Flat' | '2 Bed Flat' | '2 Bed House'
  evidenceType: 'Rent' | 'Sold'
  address:      string
  priceRent:    '£NNN,NNN' or '£NNN pcm'
  valueType:    'Sold Price' | 'Advertised Rent'
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
2.  Every new panel in its own IIFE: (function(){ 'use strict'; ... })()
3.  Unique tryInstall suffix per panel — never reuse names
4.  Guard: if (document.getElementById('panel-id')) return true;
5.  Guard clause on every DOM query
6.  All strings via esc() — define locally in each IIFE
7.  Audit log capped at 80 entries
8.  No external libraries or CDN links
9.  Debounce live calculations 150ms
10. Paste target: immediately before </body>

ESC FUNCTION (define locally in each new IIFE):
function esc(v){ return String(v==null?'':v)
  .replace(/[&<>"']/g,function(c){
    return {'&':'&amp;','<':'&lt;','>':'&gt;',
            '"':'&quot;',"'":'&#39;'}[c];}); }

AUDIT LOG PATTERN:
(function(){
  var K='residential_agent_helper_v71_audit';
  var items=[];
  try{items=JSON.parse(localStorage.getItem(K)||'[]')||[];}catch(e){}
  items.push({ts:new Date().toLocaleString('en-GB'),msg:'...'});
  try{localStorage.setItem(K,JSON.stringify(items.slice(-80)));}catch(e){}
})();

═══════════════════════════════════════════════════════
GIT WORKFLOW
═══════════════════════════════════════════════════════
cd ~/Projects/PropIntel
git add index.html
git commit -m "type: description"
git push origin main

Types: feat / fix / patch / security / data / docs

VERIFY BEFORE EVERY COMMIT:
1. tail -5 index.html → </script></body></html>
2. wc -l index.html → >= 33153
3. grep -c "new-id" index.html → >= 2
4. grep -n "tryInstall_NAME" index.html → >= 2 results

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
WHAT NOT TO DO
═══════════════════════════════════════════════════════
- Do not reference any other Excel file
- Do not create additional files in the repo
- Do not run npm, yarn, pip or any package manager
- Do not add CDN script tags
- Do not touch the password gate
- Do not change existing localStorage key names
- Do not set evidence status above Pending on auto-create
- Do not push without running all 4 verification checks
- Do not confuse this with Domus OS, DEF-EQ, or legal files
  — those are completely separate projects
