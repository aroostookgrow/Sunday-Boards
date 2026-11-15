# Sunday Board — Weekly Build Spec (v2)

Owner: Vincent · Times: always US Eastern (ET) · Reference build: `SundayBoard_2026_W10_Final_v2.xlsx` (script: `build/build_w10.py`)

This spec is the instruction set for whoever builds the board each week: a weekly automation, the "Sunday Boards" Grok Project, or a person. Follow the steps in order. **Step 0 is a hard gate.** If it fails, stop and report the problem. Do not build a board for the wrong week.

---

## Step 0 — Week validation (mandatory, before any data pull)

1. Find the date of the **next Sunday** in ET. If today is Sunday before 1:00 PM ET, use today.
2. Confirm which NFL week that Sunday belongs to, using **at least two** live sources:
   - NFL.com schedule: `https://www.nfl.com/schedules/<season>/by-week/week-<N>`
   - ESPN scoreboard API: `https://site.api.espn.com/apis/site/v2/sports/football/nfl/scoreboard?dates=<season>&seasontype=2&week=<N>` (check the event dates)
   - Or NFL Football Operations / CBS schedule pages
3. Write this line at the **top of Full Slate Matrix** and in **Sources & Notes**:
   `Target week confirmed: Week X (source 1 + source 2, checked <Day Mon DD, YYYY h:mm AM/PM ET>)`
   Include the bye teams, any international game, and any game already played this week (TNF or Saturday).
4. **Mismatch handling.** If the requested or scheduled week ≠ the validated week, or the previous board's label ≠ the week its own notes describe:
   - Put `MISMATCH FLAG: …` in red on both validation lines.
   - Build only for the validated week.
   - Name the file `SundayBoard_<season>_W<XX>_<Stage>.xlsx` using the **validated** week.
   - Mark any mislabeled earlier file as superseded in Sources & Notes.
5. **Sanity checks.** Every game on the slate must have a date inside the validated week's window (Thu–Mon). The bye list plus the playing teams must add up to 32.

## Step 1 — Data pull (record time of each pull in ET)

| Data | Primary | Backup |
|---|---|---|
| Records, streaks, PF/PA | ESPN standings API `apis/v2/sports/football/nfl/standings?season=<season>` | NFL.com / PFR standings |
| Home/road splits | FootyForecaster / PFR | ESPN |
| ATS records | TrifectaPicks ATS standings, TeamRankings | Covers |
| Current lines | ESPN scoreboard API odds (snapshot time) | OOSTA, CBS/DraftKings, Covers |
| Openers / line moves | Covers opening-lines article, NBC/Rotoworld previews | the prior Early board (label it as such) |
| Injuries (final) | NFL.com weekly injury report; team sites | WhyNFL / CBS final-report roundups |
| Results + box | ESPN API, PFR box scores, team sites | Sportsnet/AP roundups |
| Closing lines for grading | a closing-line source (e.g., getoddy, wagerbird, Covers matchup) | If none is available, grade against the prior board's line **and say so** |

Rest days: for each team, count the days since its last game, and mark the teams that have a rest edge.

## Step 2 — Sheet structure (keep exactly these 6 tabs, in this order)

1. **Full Slate Matrix**: validation line on top. Then one row per game: Game | Kick ET / TV | Spread | Total | Moneyline | Open → now (source) | Away rec/streak | Home rec/streak | Rest days A/H | Edge note (data, sourced) | Key injury impact | Fantasy lean | Betting lean.
2. **Win Streaks & Records**: all 32 teams. Record, streak, PF-PA (diff), division, home SU, road SU, ATS, this week's spot plus rest days, and one sourced note.
3. **Key Injuries Week X**: see Rule 2.
4. **Last Week + TNF Box**: last week's full results plus this week's TNF. Include the line used, the spread result, the total result, and a **Corrections** row for any earlier board that graded a game wrong.
5. **Betting & Fantasy Leans**: sections A (betting kept), B (betting cut, with the reason), C (fantasy), and D (Situational History Check). See Rules 1 and 3.
6. **Sources & Notes**: validation line, one row per source category with URL and time checked, the verification status of every cited trend, the list of unverified items, corrections, methodology, and the disclaimer.

Styling: title band in dark blue `1F4E79` (white, bold, 14 pt). Header row in `2E75B6` (white, bold). Data rows alternate with `D6EAF8`. Validation and warning band in `FCE4D6` with red text. Section bands in `BDD7EE`. Wrap text and freeze panes under the header.

---

## Rule 1 — Betting and fantasy leans must be falsifiable

Every lean row has these columns: **# | Game | Lean | Conviction (High / Medium / Lean) | Falsifiable reason (with number) | Source | What would prove it wrong**

- The reason is **one sentence** that contains **at least one sourced number**: a stat, a trend record, a matchup rank, or a line move. Example: "Teams off a bye are 5-0 SU and 4-0-1 ATS vs. opponents returning from an international game (CBS)."
- "What would prove it wrong" is a concrete outcome. For betting, a result vs the number (e.g., "CIN wins by 4+"). For fantasy, a stat threshold (e.g., "Warren < 70 rush yds").
- **Banned on their own:** "hot," "rolling," "elite," "struggling," "sharp move," "high implied total," and "EPA/PFF strong" with no number attached. Narrative can only support a number. It can't replace one.
- If no sourced number supports a lean, **cut it**. List it in section B with the reason it was cut (e.g., offsetting signals, or no data).
- If two sourced signals conflict, the conviction can be **Lean** at most. Show the counter-signal in the "prove it wrong" cell.
- If the market has already moved a lot in the lean's direction (≥ 3 pts on a spread or ≥ 4 on a total), say so and lower the conviction one step.
- Conviction guide. **High**: two or more independent sourced signals agree and no sourced counter-signal. **Medium**: one strong signal (a large sample or a top/bottom-3 matchup rank) plus nothing that contradicts it. **Lean**: one signal, or signals that conflict.
- Fantasy leans follow the same rule. Name the player, the action (start, sit, stream, or fade), the number, and the threshold that would prove it wrong.
- **Next week:** grade last week's leans in Last Week + TNF Box (hit / miss / push) so the hit rate can be tracked.

## Rule 2 — Key injuries need concrete impact

Columns: **Game | Team | Player (Pos) | Status | Replacement | Spread/Total impact | Fantasy winners/losers | Backup upgrade/downgrade + stat**

For every key injury (QBs, WR1/RB1/TE1, two or more OL starters, top pass rushers or CBs):
- **Spread/Total impact:** cite the actual line move with its source ("MIN +3 → +4.5 → +2.5"). If there was no move, explain why the market treats the backup as roughly equal.
- **Fantasy winners/losers:** name the players who gain or lose and say why (the target pool concentrates, the run game loses blocking, and so on).
- **Backup upgrade or downgrade:** give at least one comparison stat between the starter and the backup (rating, TD/INT, YPC, ypg). Model sentence: *"Murray OUT (Cousins in): Cousins 20 TD/97.1 rtg in 9 games vs Murray 5 TD in 6 starts and MIN 31st in EPA/play → UPGRADE; Jefferson target share rises with Hockenson/Jennings also out."*
- If no stat can be found, write **"unverified"** in the cell. Never estimate a number without a source.
- Statuses come from the final injury report. Mark questionable players who are "expected to play" according to a beat reporter.

## Rule 3 — Situational history check (run every week)

Go through every item for every game. In the Situational block, list only the angles that **apply**, naming the game and the side. Keep the standing trends below as **N/A this week** rows so the check stays visible. Before using any figure, re-verify it from a source dated this season, or mark it **"user-cited, unverified."**

| # | Angle | Current verified figure (as of Nov 14, 2026) | Source |
|---|---|---|---|
| S1 | Team off a bye vs opponent returning from an international game | 5-0 SU, 4-0-1 ATS all-time; 3-0 when the trip was Europe (all London) | CBS Sports, Nov 13, 2026 |
| S2 | Favorites with a rest advantage (days since last game) | 26-1 SU, 21-5-1 ATS in 2026 through Week 9 | Action Network (Evan Abrams), *The Favorites* podcast, Nov 12, 2026 (spoken; no written table) |
| S3 | Road favorites coming off a bye | 119-50 SU, 97-68-4 ATS (58.8%) since 1999 | VSiN (Makinen), Oct 13, 2026 |
| S4 | Post-bye road favorites vs non-conference | 36-17-3 ATS (67.9%) since **2000** | VSiN |
| S5 | Home underdogs off a bye (play against) | 50-20 SU, 38-30-2 ATS since 1999 | VSiN |
| S6 | Road teams off a bye → Under | 46-26-1 since 2021 | VSiN |
| S7 | Double-digit favorites off a bye (play against) | 19-10-1 ATS since 2014 | VSiN |
| S8 | Team playing the week after a Europe game, no bye | 11-6 SU, 9-8 ATS, 10-7 Over (2016-2024) | BetIQ / TeamRankings |
| S9 | Short week: Thursday after Sunday, or Sunday after MNF | Compute rest days; cite a trend only if sourced | schedule |
| S10 | International game this week, or travel home from one | Flag both teams this week **and** their next-week opponent spot | NFL Ops schedule |
| S11 | Divisional games and rematches | First-meeting score, recent head-to-head SU/ATS | team sites, NBC/Rotoworld |
| S12 | Primetime (TNF/SNF/MNF), QB primetime splits | Cite only if sourced | — |
| S13 | First-start or backup QBs | List them; cite a debut-QB ATS figure only if sourced | — |
| S14 | Teams off a bye this season (league-wide) | 13-1 SU entering Week 10, 2026 | CBS Sports |
| S15 | Team-specific post-bye records | e.g., PIT 9 straight post-bye wins, TEN 8-1-1 ATS post-bye | VSiN team trends |

How to work through it:
1. Compute rest days for all teams. List the rest-edge teams and mark which of them are favorites (S2).
2. From last week's schedule, list the teams coming off a bye and the teams coming off an international game (S1, S3–S8).
3. Mark divisional games, rematches, primetime games, and QB changes (S11–S13).
4. Each angle that applies goes in the block as: `S# | Game | Side | APPLIES / INFO / conflicted | figure + verification status | source | what falsifies it`.
5. An angle only becomes a **lean** if it passes Rule 1. When an angle and a team trend conflict (e.g., S5 vs TEN's post-bye record), the lean is capped at **Lean**.
6. Refresh any figure that changes during the season (S2, S14) every week.

## Step 3 — Final QA checklist (all must be true before delivery)

- [ ] The validation line is present in Full Slate Matrix **and** Sources & Notes, and any mismatch is flagged.
- [ ] Exactly 6 tabs, with the names and order above.
- [ ] Every number in the leans and injury tabs has a source named in the same row, and every source category is listed in Sources & Notes with a URL.
- [ ] No lean is based only on a narrative, and the cut list is present.
- [ ] Each cited trend is marked VERIFIED (with source) or "user-cited, unverified."
- [ ] Totals are graded arithmetically: points scored vs the number. Recheck every Over/Under call, because prior boards got two wrong.
- [ ] All times are in ET, and the snapshot time of the lines is stated.
- [ ] Disclaimer: analysis only, not betting advice.

## Naming and stages

`SundayBoard_<season>_W<XX>_<Early|PostTNF|Final>[_vN].xlsx`. Early is built on Saturday a week ahead (opening lines). PostTNF is built Friday. Final is built on the Saturday before the slate. The week number always comes from Step 0.
