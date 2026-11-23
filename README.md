# The Date Crew Profile Checker (prototype)

Internal decision-support tool. It checks a candidate profile against a client's stated preferences **before** the profile is emailed, so avoidable rejections are caught early. The matchmaker always makes the final call.

## Run it
Open `index.html` in a browser. No build step, no server, no API key. State is kept in `localStorage`; "Reset demo data" restores the seed.

## Test it
`node tests/run-tests.js` runs the matching engine against 10 scenarios (all match, one deal-breaker conflict, missing information, soft-preference miss).

## How matching works
- Each client preference is a **deal-breaker**, **preferred**, or **not set**.
- Per preference the engine returns match / partial / mismatch / unavailable.
- Any deal-breaker mismatch -> *Conflicts with deal-breaker*.
- Missing or partial deal-breaker data, or a preferred mismatch -> *Needs matchmaker review*. Missing data is never treated as a match.
- Otherwise -> *Matches stated preferences*.
- Preferred fit % = (match = 1, partial = 0.5), unavailable excluded.

The engine sits between `// ==ENGINE START==` and `// ==ENGINE END==` in `index.html` as pure functions, ready to move behind an API.

## What is mocked
3 fictional clients, 10 fictional candidates, 4 history rows and 12 feedback rows (all labelled demo). Feedback extraction uses keyword rules, not an AI model. In production an LLM would return the same structured fields and the matchmaker would still confirm them.

## Next steps
1. Real client and candidate data behind an API (Supabase/Postgres).
2. LLM extraction of rejection reasons, evaluated on ~100 hand-labelled examples.
3. Hard pre-filter of the candidate pool by deal-breakers, to cut search time.
4. Log overrides and their outcomes to learn which stated deal-breakers are really flexible.
