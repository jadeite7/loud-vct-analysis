# LOUD Champions Dominance Comparison: 2022 vs 2026
A round-level statistical comparison of LOUD's VALORANT Champions runs in 2022 (Istanbul) and 2026 (Shanghai), asking which run was more dominant relative to its own tournament field.

## Research Question
Relative to their own tournament fields, was LOUD's 2022 Champions run or their 2026 run more dominant?

"Dominant" is defined below using several complementary metrics, with field-adjusted normalized round differential per map as the primary measure.

## Hypotheses
- H0: The two runs differ in field-adjusted dominance (two-sided).
- H1: There is no difference in field-adjusted dominance between the 2022 and 2026 runs.

## Why field-adjusted?
VALORANT’s maps, agents, economy rules, meta, and opponents all changed between 2022 and 2026, so raw numbers are not directly comparable and would introduce bias. Moreover, absolute team strength is not well-defined across eras: with no common opponents and no shared game version, there is no fixed scale on which the two rosters can be placed, and any such scale would be arbitrary. We therefore measure each run relative to the other teams in its own tournament. This project compares dominance relative to the field, not absolute team strength.

## Metric definitions
 
| Metric | Definition |
|---|---|
| Round win % | Rounds won ÷ rounds played; also split by attack/defense |
| Round differential (RD) | Rounds won − rounds lost, per map |
| Normalized RD | RD ÷ total rounds in the map (corrects for overtime) |
| Map / series win % | Wins ÷ played |
| **Field-adjusted RD** (primary) | LOUD's normalized RD relative to the tournament average, adjusted for opponent strength |
| Round-type win % | Win rate by pistol / eco / force / full buy, based on loadout value spent (not credits banked) |
| Eco conversion | Wins ÷ rounds played with a large economic disadvantage |
| Anti-eco loss rate | Rounds lost while holding a large economic advantage |
| Consistency | SD of per-map RD, worst-map RD, share of maps above an RD margin |
| Stage trend | Round win % and RD by stage (group, playoffs, finals) |
| Role-adjusted player rating *(optional)* | ACS, ADR, KAST, FK/FD differential, z-scored within tournament and role |

## Analysis plan
 
1. **Data and cleaning:** Build a round-level table for *all teams* in both tournaments (needed for field baselines and opponent adjustment). Validate that round counts match map scores.
2. **Descriptives:** Metric tables and figures (per-map RD distributions, cumulative RD across the tournament, round win % by round type).
3. **Main comparison:** Welch's t-test on per-map normalized RD, a bootstrap CI for the difference in means, a Mann-Whitney U test as a robustness check, and Cohen's d.
4. **Round-level model:** Logistic regression on rounds with side, map, economy difference, and team effects, using cluster-robust standard errors clustered by map. This is a fixed-effects approximation to a mixed model, chosen deliberately to keep the project dependency-light.
5. **Economy ("luck"):** Estimate the eco baseline from the field with uncertainty, then test LOUD against it with an exact binomial test and Clopper-Pearson CI. Results are framed as "more or fewer wins than expected from baseline," since opponent mistakes and playbook strength look the same in the data.
6. **Consistency:** Compare variance in per-map RD between runs (Brown-Forsythe test and bootstrap on the SD), plus worst-case maps and stage trends.
7. **Sensitivity checks:** Exclude overtime or pistol rounds, vary eco/force thresholds, leave out one opponent at a time, and try alternative opponent-adjustment methods.
8. **Optional extension:** A duelist comparison (aspas vs tkzin) using win probability added per kill, only if the core pipeline is complete.
### Interpretation rules (set in advance)
 
- If all metrics agree, state a clear conclusion with effect sizes and intervals.
- If metrics disagree (for example, one run wins on raw RD but the other after opponent adjustment), report the disagreement as a finding.
---
 
## Data
 
- **Sources:** VLR.gg and/or rib.gg round-by-round match data *(final source to be confirmed after a feasibility check on economy data availability for both tournaments)*.
- Raw data is **not** redistributed in this repo. Scraping code is included instead, with rate limiting and local caching, and is intended to respect each site's terms of service.
- See `docs/data_dictionary.md` for column definitions *(to be written)*.
Core round-level fields: series and map IDs, round number, side, both teams' loadout values, round type, round winner, and kill events with timestamps where available.
