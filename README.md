# Can a non-developer build an app with Codex? Evidence from Unfinished

This is the public evidence pack for These Guys Know’s Unfinished experiment. We started with an interactive, in-memory web prototype and used Codex to build an account-backed private beta. The experiment tested how far a non-developer could direct that work, spot failures and get them repaired. It did not establish that Unfinished is a viable paid product.

The original prototype already had an interface and fictional scenarios. Codex work added persistent records, account separation, money and date rules, history, in-app reminders, authentication and recovery, backups and a deployed beta. We made the product decisions, tried the real workflows, reported failures and checked fixes in the browser and on the host. This was not a one-prompt build.

## What was live, and what stayed local

| Version | Status at the 28 September 2026 evidence check | What it shows |
| --- | --- | --- |
| `a1ff0856` | Reported live on Render; public frontend files matched this commit | The private beta with accounts, records, history and the action-first overview. The public asset comparison cannot independently establish the backend Git SHA. |
| `1fd32756` | Local only | Adds “Add another like this,” a quicker way to start a separate record for repeated client work. No deployment of this shortcut was recorded. |

The screenshots below were captured from local code at `1fd32756` with fictional data in a disposable workspace. They are **not production-account screenshots**. The phone-sized image was captured in desktop Chrome at a narrow viewport; it is not a photograph of a physical phone. An iPhone screen recording discussed for the article is separate from these six captures and is not included in this pack.

## Recorded checkpoints

| Date | Checkpoint |
| --- | --- |
| 16 September 2026 | The retained prototype was tested and work on persistence, ownership and money rules was recorded. The prototype's original creation date is unknown. |
| 17 September 2026 | A working local V1 was preserved and private-beta preparation was committed. This alone did not publish the app. |
| 25 September 2026 | Authentication and restore repairs were committed. The user later confirmed `a1ff0856` live on Render. Repeat entry was committed separately as `1fd32756` and remained local. |
| 28 September 2026 | The local suite and browser checks were rerun, six fictional-data captures were made, and public frontend files were compared with the reported live baseline. |

These are dated checkpoints, not measured development hours.

## What to inspect

| Evidence | What it demonstrates | Boundary |
| --- | --- | --- |
| [Original prototype](article-evidence/01-original-prototype-desktop.png) | Recovered interactive starting interface and fictional scenarios | Newly rendered from retained source; original creation date is unknown. |
| [Local desktop overview](article-evidence/02-local-desktop-overview.png) | Due actions ahead of separate money categories | Local commit `1fd32756`. |
| [Phone-sized local overview](article-evidence/03-local-phone-overview-390.png) | Narrow layout at 390 × 844 | Desktop Chrome viewport, not physical-device evidence. |
| [Record history](article-evidence/04-local-atlas-history.png) | Original invoice due date, payment promise and follow-ups remain distinguishable | Fictional Atlas Creative; the visible repeat button is local only. |
| [Record invoice sent](article-evidence/05-local-record-invoice-action.png) | An action with sent, due and follow-up dates | The invoice is created and sent outside Unfinished; this dialog only records that event. |
| [Local repeat-entry form](article-evidence/06-local-only-fast-repeat.png) | Client, project, amount and currency carry into a new independent entry | Not deployed or timed with a tester. |

Capture details and checksums are in the [capture manifest](article-evidence/capture-manifest.json). The [sanitized test summary](article-evidence/test-summary.json) records fresh local runs, and the [live asset check](article-evidence/live-version-check.json) records the public frontend comparison. These links require the named files to be uploaded alongside this README.

## Three failures that mattered

1. **A normal record would not save although tests passed.** The local preview served newer frontend files while an older server process rejected the form’s `waitingMode` field. After a restart, the same entry saved. The app was changed to load frontend assets with the server at startup, and contract coverage was added.
2. **An older startup error could erase a valid password-reset screen.** Review reproduced the race after an earlier 70-test pass. The callbacks were guarded by the current authentication journey, and regression tests covered stale success and failure responses. A separate reported verification-to-reset switch was not directly reproduced as a token-purpose bypass.
3. **An encrypted backup restore failed on Render when no deletion ledger existed.** An empty ledger allowed the smoke restore to complete. A later local fix treats only a missing ledger as empty and retains other error and deletion protections. The specific missing-file scenario was then covered by local regression; the pack does not claim a second Render restore of that case.

## Tests and limits

On 28 September, the latest local suite reported **97 passed, 0 failed, 0 skipped**. A separate real-Chrome repeat-entry suite reported **3 passed** at desktop, 320px and 390px. These are separate runs, not 100 production tests. Earlier passing totals describe earlier checkpoints and must not be added to the latest count.

The testers exposed product friction that the test suite could not measure. One lesson tutor already tracked paid and unpaid work in a notebook and found the original entry flow slower. Another tester emphasized the template-like appearance and phone use. A third suggested user-defined fields instead of profession-specific templates. These are three individual responses, not a representative market study or proof that the later local shortcut solved the tutor’s complaint. No direct tester quotes or private tester details are published here.

The evidence does **not** establish total development hours or cost, independent security certification, long-term reliability, reduced late payments, user retention or willingness to pay. Unfinished prepares follow-up text and records actions; it does not send client email, generate invoices, read a mailbox or sync payments. The live beta and the local-only iteration are identified separately throughout this pack.

## Publication note

The six images were captured with fictional examples and inspected for account addresses, tokens and private tester information. This repository should contain only this README, the six reviewed PNGs and the three sanitized JSON files linked above. Do not copy the private application repository, databases, raw tester messages, account screenshots, credentials or deployment configuration into it. Internal evidence notes such as `ARTICLE_EVIDENCE.md` link to private build files; they need separate public-link review before publication.
