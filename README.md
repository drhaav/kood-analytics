# kood/Jõhvi — Cohort Analysis Dashboard

An interactive single-page dashboard analysing student enrolment, demographics, and graduation outcomes across all nine batches of the [kood/Jõhvi](https://kood.tech) coding school (2021–2026). Built for FutureCoders OÜ as an internal strategic tool.

**Live:** [drhaav.github.io/kood-analytics](https://drhaav.github.io/kood-analytics/)

---

## What the dashboard shows

The dashboard is organised into three tabs, each answering a distinct question:

| Tab | Question |
|---|---|
| **Geography** | Where do students come from, and how has the regional mix shifted across batches? |
| **Demographics** | How have age, gender, and nationality distributions changed over time? |
| **Deep Dives** | Do female students, under-21 students, and Eastern Estonia students behave differently — in enrolment and in graduation? |

### Linking to a specific view

Every tab, Deep Dive segment, section and batch selection has its own URL, so you can send someone straight to it. Hover a section heading and click the `#` that appears to copy its link (the selected batch is included for batch breakdowns).

| Link | Opens |
|---|---|
| `#demographics` | Demographics tab |
| `#demographics/nationality` | Demographics → Nationality section |
| `#demographics/batch-4` | Demographics → age & gender breakdown for Batch 4 |
| `#demographics/nationality-breakdown/batch-5` | Nationality breakdown for Batch 5 |
| `#geography/breakdown/batch-3` | Geography → batch breakdown for Batch 3 |
| `#deep-dives/under-21` | Deep Dives → Under-21 segment |
| `#deep-dives/eastern-estonia/profile` | Deep Dives → Eastern Estonia → age & gender profile |

Section slugs — Geography: `enrolment`, `regional-mix`, `breakdown`. Demographics: `age`, `age-mix`, `gender`, `gender-mix`, `breakdown`, `nationality`, `nationality-share`, `nationality-mix`, `nationality-breakdown`. Deep Dive segments: `female`, `under-21`, `eastern-estonia`, each with `trend`, `regions`/`profile`, and `age`/`gender`/`nationality`.

---

## Data source

All student records are sourced from **Monday.com**, where each batch has a dedicated board. Data covers Batches 1–9 (September 2021 – September 2026), totalling **1,434 students**. Students who transferred between batches are counted in the batch board they are currently on. Graduation status is determined by group membership within each board (groups "7. Graduated", "7.1 Graduated" and "Graduated & ISA Payment done"). No external data sources are used.

**Data quality notes by batch:**

| Batch | Start | Enrolled | Graduated | Notes |
|---|---|---|---|---|
| B1 | Sep 2021 | 206 | 99 | No birthdate data collected |
| B2 | 2022 | 280 | 150 | Nationality free-text entry (~17% non-standard); location as short city name |
| B3 | Sep 2023 | 212 | 100 | Full individual-level data |
| B4 | Jan 2024 | 118 | 40 | Full individual-level data |
| B5 | Sep 2024 | 175 | 98 | Full individual-level data |
| B6 | Mar 2025 | 103 | 23 so far | Finishing — nominal end 14.09.2026; students on extensions still graduating |
| B7 | Sep 2025 | 136 | 17 so far | In progress — nominal end 13.12.2026 (15-month track); 12-month track already graduating, more graduates expected until early 2027 |
| B8 | Mar 2026 | 114 | — | In progress |
| B9 | Sep 2026 | 90 | — | In progress — started 30.09.2026; two tracks (see below). 1 further student pending approval, not yet counted. Nationality blank for 8 of 90 |

Graduation rate applies to B1–B5 only. B6–B9 are not yet complete; their graduate counts are shown as "graduated so far" and excluded from headline totals.

**Batch status values** (`BM[n].st` in `index.html`):

| Status | Meaning |
|---|---|
| Complete | Graduation is final; the batch counts towards graduation-rate figures (`cg:true`) |
| Finishing | Past the nominal last day of studies, but students on extensions or exceptions are still graduating |
| In progress | Before the nominal last day of studies; some students (e.g. shorter-track) may already have graduated |

---

## Methodology

### Geographic classification

Student addresses are classified into five regions using keyword matching against address strings:

| Region | Key locations |
|---|---|
| Tallinn & Harjumaa | Tallinn, Maardu, Viimsi, Keila, Saue, Rae vald, Laagri, Peetri, Haabneeme and surrounding Harju County municipalities |
| Eastern Estonia | Narva, Jõhvi, Kohtla-Järve, Sillamäe, Ahtme, Kiviõli, Püssi and other Ida-Virumaa localities |
| Southern Estonia | Tartu, Põlva, Võru, Viljandi, Valga, Jõgeva and their counties |
| W. Estonia & Islands | Pärnu, Haapsalu, Rapla, Saaremaa, Hiiumaa, Rakvere, Järvamaa (Paide, Türi) |
| International | Addresses outside Estonia (Ukraine, Latvia, Germany, Finland, etc.) |

Classification is deterministic and applied consistently across all batches. B1 uses short city names (lower precision); B2 uses partial address strings; B3–B8 use full structured addresses (higher precision). B9 addresses end in a county name ("Harjumaa", "Ida-Virumaa", …), so B9 is classified by that county suffix using the same region mapping (Lääne-Virumaa and Järvamaa → W. Estonia & Islands); addresses ending in a non-Estonian region are International. One address with inconsistent city/region fields was manually assigned to Tallinn & Harjumaa. The "Unknown" category captures records with missing or unparseable addresses.

### Age calculation

Age is calculated as **age at batch start date**, not current age. A student is classified as under-21 if they had not yet reached their 21st birthday at the time their batch began. B1 is excluded from all age analysis (no birthdates collected).

### Graduation rate

A student is counted as graduated if they appear in group "7. Graduated" (from B7 onwards also "7.1 Graduated (Full-Stack Proficiency)") **or** the post-graduation group "Graduated & ISA Payment done" within their batch board at the time of data extraction (October 2026). The second group holds students who graduated and have since completed their ISA repayment; they are graduates and are counted as such. Students still active, on study leave, or who left without completing are not counted as graduates. Rates are only reported for B1–B5, where cohorts have had sufficient time to complete. B6 and B7 show graduates so far in the batch drill-downs, labelled as not final.

**Two-track batches (B9 onwards).** B9 runs two tracks: *AI-ready Product Engineer* (15 months, nominal end 31.12.2027) and *Coding fundamentals and back-end development* (6 months, nominal end 31.03.2027). A student may finish the short track and continue on the long one, but doesn't have to. Group membership alone can't capture this, because a student who continues moves back into an active group. Graduates are therefore counted **once per student, by the highest track completed**, using the board's per-track status columns:

| Bucket | Rule |
|---|---|
| Full-track graduate | In "6. Graduated (AI-ready Product Engineer)" or "Graduated & ISA Payment done", or "Product Engineering" = Done |
| Short-track graduate | "Coding fund+ back-end" = Done (or in "6.1 Graduated"), **and not** a full-track graduate |
| Graduated (any track) | Full + short-track-only — no double counting |

This relies on "Coding fund+ back-end" staying Done when a student continues to the long track. Rates use all enrolled students as the denominator. Any headline figure that combines tracks is labelled "any track", because B1–B5 headline rates are full-programme completions. B7/B8 (which have "7.1 Graduated (Full-Stack Proficiency)") currently add both groups; moving them to the same rule is a separate change.

### Cross-tab analysis (Deep Dives tab)

The region × age × gender cross-tab is based on **individual-level records from B3–B8** (n=637 combined). All six batches have complete joined location, birthdate, and gender data at individual level. B2 is excluded from cross-tab analysis due to lower location data precision (short city names rather than full addresses). B1 is excluded as no birthdates were collected. Findings should be treated as directional hypotheses rather than confirmed conclusions.

---

## Key findings

**Geography** — Tallinn & Harjumaa consistently accounts for 45–55% of each batch. Eastern Estonia contributes a stable 11–17% from B3 onwards (higher in B1–B2). Southern Estonia has grown as a share. International students peaked at 14% in B2 (reflecting the 2022 Ukrainian refugee cohort) and stabilised at 3–6%.

**Demographics** — The overall female share is 23%, but volatile across batches (11–29%). There is no structural trend of improvement or decline. Average enrolment age is 27–29 across all batches. The under-21 bracket was stable at 16–20% of each batch through B7; B8 (8%) and B9 (11%) are the two lowest batches recorded.

**Female students** — Graduate at 46% vs 50% for males across B1–B5 (487 total graduates) — no meaningful difference. Female students are underrepresented in the under-21 bracket and overrepresented in the 25–34 range, suggesting they skew toward career-changers rather than recent school leavers. No region stands out as markedly more or less female-diverse across B3–B8; an earlier finding that International students had the highest female share (27%) was specific to the B2 Ukrainian refugee cohort and does not hold across subsequent batches.

**Under-21 students** — Graduate at roughly **80% of the overall rate** across B2–B4 (37–46% vs 49–55% for older students). The gap is consistent in direction across three batches; B5 reverses it but on a small sample. This does not argue against recruiting younger students, but suggests they may benefit from targeted retention support.

**Eastern Estonia students** — Age profile is close to the overall baseline across B3–B8 (n=76): 14% under-21 vs 13% overall, and an age distribution broadly proportional to the full cohort. An earlier finding of 41% under-21 was specific to B3 and B5 and does not hold across six batches. The female share (9% vs 19% baseline) is the more robust and persistent signal. The pooled graduation gap (31% vs 51% for non-Eastern) is directionally concerning but driven largely by B1–B2 where address classification is less reliable; B3 and B4 individually show no graduation gap.

---

## Deep Dives

The Deep Dives tab examines three segments in detail — Female students, Under-21, and Eastern Estonia — each with:

- A hero KPI row (totals, peak/low, notable averages)
- A trend chart showing count and share per batch (B1–B9)
- Cross-tab charts (region breakdown, age profile, gender split) based on B3–B8 pooled individual data (n=637)
- Written findings cards distinguishing confirmed signals from directional observations
- Explicit data coverage caveats per segment

Graduation rate findings are integrated into each segment's findings where the data is sufficient to draw conclusions.

---

## Technical notes

**Stack:** Single HTML file (~79KB). No build step, no dependencies to install, no server required. Works from the filesystem or served via GitHub Pages.

**Libraries (CDN-loaded):**
- [Chart.js 4.4.1](https://www.chartjs.org/) — all charts
- [chartjs-plugin-annotation 3.0.1](https://www.chartjs.org/chartjs-plugin-annotation/latest/) — baseline reference lines
- [DM Sans + DM Mono](https://fonts.google.com/) — typography (Google Fonts)

**Code structure:** The JS is divided into three clearly delimited sections: `BRAND CONFIG` (all chart colours and typography — edit here for CVI updates), `DATA` (all batch constants with a new-batch template — edit here for data updates), and `CHART LOGIC` (rendering and interaction — no batch data belongs here).

**Features:** Dark/light mode toggle, per-batch drill-down tables, batch selector tabs, consistent bottom-placement legends across all charts, responsive layout. All chart instances are destroyed and rebuilt on tab switch and theme toggle to avoid canvas conflicts.

**Browser support:** Any modern browser. No polyfills required.

---

## Publishing workflow

GitHub Pages is configured to serve from the `main` branch root. Merging to `main` triggers automatic deployment — no manual publish step.

### Branch naming convention

| Prefix | Used for | Example |
|---|---|---|
| `feat/` | New tab, chart, or segment | `feat/deep-dives-tab` |
| `fix/` | Broken behaviour | `fix/js-syntax-eastern-findings` |
| `style/` | Visual polish, CVI alignment, layout tweaks | `style/chart-color-palette` |
| `chore/` | Data updates, no user-facing change | `chore/fetch-b8-data` |
| `refactor/` | Code restructuring, same behaviour | `refactor/chart-helpers` |

### Typical update cycle

```bash
# Start from clean main
git checkout main && git pull

# Create a branch for the work
git checkout -b chore/update-b8-enrolment-data

# Work, then commit with intent-first messages
git add index.html
git commit -m "chore: fetch and process B8 individual student data"
git commit -m "feat: add B8 to Deep Dives trend charts"

# Push and open a PR on GitHub
git push origin chore/update-b8-enrolment-data
# Squash-merge the PR to main → Pages deploys
```

Squash-merge is preferred for this project: it collapses work-in-progress commits into a single legible entry on `main`, keeping the history readable without requiring perfect commits during development.

---

## Repo structure

```
/
├── index.html    # The entire dashboard — HTML, CSS, and JS in one file
└── README.md     # This file
```
