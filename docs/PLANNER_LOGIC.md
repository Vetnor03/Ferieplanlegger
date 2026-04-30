# Planner logic (premium whole-plan planner)

## 1) Candidate generation
- Generate day-block candidates across the year (2-20 days).
- Only `custom` season acts as hard constraint (`inFilter`), other seasons are soft preferences.
- Candidate metadata stores used vacation days, total free days, bridge/holiday context.

## 2) Strategy generation
Planner builds and compares complete plans from:
- `maximum_days_off`
- `balanced`
- `spread_out`
- `full_weeks`
- `summer_priority`
- `christmas_included` (when Christmas selected)
- `user_selected_priority` (when user selected calendar days)

## 3) Whole-plan scoring
- Uses beam search to build full alternatives (browser-safe beam width).
- Scores complete plans (not one greedy period at a time).
- Score includes: full weeks, partial-week penalties, short work-gaps, isolated single days, month/quarter spread, season preference hit, selected-day hit, Christmas inclusion, leftover-day penalty.

## 4) Season semantics
- `All year`: no preference.
- `Spring/Summer/Autumn`: weighted preference, not strict filter.
- `Christmas`: attempts inclusion and rewards Christmas/New Year block, but still competes with other high-quality periods.
- `Custom`: hard date constraint.

## 5) Grouping slider behavior
- `0` => spread mode: strongly rewards month/quarter spread and penalizes large clusters and off/on patterns.
- `100` => grouping mode: strongly rewards full weeks and larger blocks.
- Mid values blend naturally via dynamic weights.

## 6) Realism and boss-friendly rules
Penalizes:
- many partial weeks,
- partial-week chains,
- periods separated by very short work gaps,
- isolated single vacation days.

Rewards:
- full Mon-Fri weeks,
- clean long weekends,
- smart bridge use,
- summer block and Christmas block when relevant.

## 7) Leftover days
- No random filler days.
- Leftover days are only used if they improve total plan quality.
- If not beneficial, they remain unused and explanation is shown.

## 8) Explainability and labels
- Planner returns a named quality label (e.g. `Mest balansert`, `Best spredning`, `Jul inkludert`).
- Result text explains why this full-plan strategy won and why days were unused.

## 9) Debugging
- `DEBUG_PLANNER` constant controls logging.
- Logs generated candidates, compared complete plans, and final score outcomes.
