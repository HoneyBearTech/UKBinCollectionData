# Plan: ukbin-fixes-1

## What Sean asked for
"UKBin": fix broken councils in UKBinCollectionData and send each fix to the original project (robbrad/UKBinCollectionData) as its own pull request.

## What we found
- **Most broken councils can't be checked from here.** Many UK council websites refuse visitors from outside the UK or block automated visitors, so their fixes couldn't be tested before sending. They're left out.
- **Already fixed or not reproducible:** Sevenoaks was fixed in the owner's November release; Wiltshire works for every address tried and needs the reporter's details; East Lindsey and Stockton are behind a bot check.
- **Two real problems can be fixed and checked now:**
  - **#2329 Carmarthenshire:** homes moved to the council's new recycling service (from 2 November 2026) get no recycling, food or glass dates. The council's site now gets this from a different page, which covers both the old and new services.
  - **An expired test:** a test for Adur & Worthing has October dates written into it. Since 19 November it fails, which turned the project's nightly test run red, and it will fail on every outside pull request until fixed. Nobody has reported it yet.
- **How the owner takes outside fixes:** he copies contributors' commits (keeping their name) into a monthly release, then closes the pull request with a credit note. He expects one council per pull request, an offline test for every parser fix, public non-residential test addresses, and commit titles in the project's `type(scope): summary` form.

## What we'll change
1. **Carmarthenshire (#2329)** in `uk_bin_collection/uk_bin_collection/councils/CarmarthenshireCountyCouncil.py`:
   - Read collections from the council's current recycling page (`SurfaceKerbsiteRecyclingChecker/GetRecyclingInfo`), which lists every container and its next date for both the old and the new service.
   - Use the container names the council shows (for example "Blue bags", "Food waste bin", "Black box"), keep the garden and nappy collections as today, and give a clear error if no collections are found.
   - What users enter (the property number, UPRN) doesn't change.
   - **Check:** a new offline test with saved council pages for both services (no addresses in them), which fails without the fix; the project's live test for Carmarthenshire passes; a live run for a home on the new service shows its recycling, food and glass dates (that home's details are not committed).
   - **Note for users:** bin names change for Carmarthenshire users (for example "Blue" becomes "Blue bags"), so their Home Assistant sensors get new names. The pull request says this plainly.
2. **Adur & Worthing expired test** in `uk_bin_collection/tests/test_adur_and_worthing_councils.py`:
   - Build the test's dates from today's date (or fix the clock in the test), so it can't expire again. The council code itself doesn't change.
   - **Check:** the project's unit tests go from 2 failures to 1 (the remaining one needs a Chrome browser this machine doesn't have, and fails the same way on the original code); the test also passes with the clock moved months ahead.
3. **For both:** commit titles pass the project's commit-message check, formatting passes `black`, and each commit applies on its own to the latest `master`.
4. **Send upstream, only after the final approval on this PR:**
   - Each fix gets its own clean branch from the original project's latest `master`, holding only that fix. The plan file never goes upstream.
   - One pull request per fix to robbrad/UKBinCollectionData, from Sean's account, written as Sean working alone, in the style of recently accepted outside fixes (problem, what changed on the council side, fix, tests run, user impact).
   - Draft titles:
     - "fix(carmarthenshire): read collections for homes on the new separated recycling service"
     - "test(adur-worthing): build fixture dates from today so the parser tests stop expiring"
   - The full title and description of each is shown here first.

## Who's on it
Researcher (done), Engineer, QA, Security Officer, Auditor.

## Risks
- **Sensor names change for Carmarthenshire users.** Keeping the old names would mean two naming schemes for one council; the owner did the same kind of rename for Lancaster and called it out.
- **New-service test data:** no public, non-residential address is on the new service yet, so the new-service case is tested with a saved page, not a live test entry.
- **Slow to land:** the owner releases outside fixes monthly, so these may take a few weeks to appear.
- **Councils can change their sites again.**

## Out of scope
- Councils that block this machine (Walsall, Ipswich, Southampton, Mid and East Antrim, Waverley, Woking, Lancaster, Breckland, South Holland, South Tyneside, Wealden, East Lindsey, Stockton).
- Wiltshire (#2347): needs the reporter's details.
- Issues already fixed in recent releases or covered by open pull requests.
- Fork-only files: no guidelines file, badges or notes go into the upstream pull requests.
