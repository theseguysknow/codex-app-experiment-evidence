# Unfinished — article evidence pack

Prepared 28 September 2026. Editorial source material, not a launch announcement, security certification or validated business claim. Screenshots and sanitized machine-readable evidence are in [article-evidence/](article-evidence/README.md).

**Supported conclusion:** a non-developer directing Codex took an existing interactive prototype to an account-backed private beta. Real failures required diagnosis and retesting. Technical progress did not establish that the manual tracker was worth adopting or paying for. Commercial feature expansion was paused because recording work here could duplicate administration performed elsewhere. No tester quotations or private tester details are included in this pack.

## 1. Prototype versus Codex additions

| Area | Recovered original prototype | Added during Codex work |
| --- | --- | --- |
| Interface | Plain HTML/CSS/JavaScript, cream/dark-green identity, overview, forms, history and three fictional scenarios. | Existing identity retained; action-first overview, four separate money categories, conditional fields, clearer responsibility and honest action labels. |
| Records | Working interactions held in memory; reload discarded changes. | SQLite persistence, accounts, server-side ownership, explicit stage transitions, append-only history, version conflicts and replay-safe saves. |
| Money rules | Existing totals/date calculations; an unrestricted edit stage could erase invoice dates and promises. | Original due dates preserved, overdue derived from unpaid due dates, separate promises, independent stored currencies and no exchange-rate conversion. |
| Reminders | No persistent owner delivery system. | Persistent, deduplicated **in-app** owner reminders. No external owner-reminder delivery is implemented. |
| Deployment/security | No app-owned login, database or production recovery. | Session/authentication controls, beta registration code, verification/reset email, account deletion, encrypted backups, restore checks, health/noindex and install manifest/icons. |
| Repeated work | No repeat-entry shortcut. | Local-only “Add another like this”; four stable fields prefilled, independent new ID/history and no lifecycle data copied. |

Sources: [build log, imported provenance and §§1–13](BUILD_LOG.md), [imported source](../imported-prototype/dist/), [operations](OPERATIONS.md), [experiment report](EXPERIMENT_REPORT.md). This is not a from-scratch interface or one-prompt business. Authentication email is not automated invoice/client messaging. Prepared follow-up text is deterministic, not an AI feature. Install metadata is not a native mobile app or offline private-data cache.

## 2. Dated build/deployment timeline

Dates below come from recorded checkpoints; they are not development-hour measurements.

| Documented date | Checkpoint | Evidence qualification |
| --- | --- | --- |
| 16 September 2026 | Imported prototype tested; persistence, ownership, money rules, reminders and UX iterations recorded. | [Build log §§1–8](BUILD_LOG.md). Baseline browser tests included all three fictional workflows. |
| 17 September 2026 | Ordinary-save/stale-server failure corrected; working V1 preserved as `25a37bd` / `v1-local`; beta preparation committed as `b0628de`. | Git commit dates and [build log §9](BUILD_LOG.md). A preparation commit does not prove deployment. |
| 17 September 2026 | Authentication investigation is dated in the logs; cleanup, email presentation and reset-validation flaws recorded. | [Authentication repair](AUTHENTICATION_REPAIR.md). The full repair was committed later. Exact first Render deployment timestamp is **unknown** in the reviewed evidence. |
| 25 September 2026 | Startup race fixed; authentication repair committed as `45e7ec8` with 85 passing tests. Codex Git access failed. | [Build log §11](BUILD_LOG.md) and [experiment report](EXPERIMENT_REPORT.md). That local commit attempt did not itself publish anything. |
| 25 September 2026 | User reported Render smoke-test success, except initial restore failure when the deletion ledger was absent; an empty-file workaround succeeded. | [Deployment checklist, smoke-test results](DEPLOYMENT_CHECKLIST.md). Attributed production results, not a newly witnessed production test in this task. |
| 25 September 2026 | Missing-ledger repair committed as `a1ff085`, 92 tests passing. Later user-provided push output and Live confirmation identify it as the deployed baseline. | [Build log §12](BUILD_LOG.md); later deployment evidence summarized in [experiment report](EXPERIMENT_REPORT.md). |
| 25 September 2026 | Repeat-entry commit `1fd32756`: 97 suite tests plus 3 separate browser tests; kept local. | [Build log §13](BUILD_LOG.md). No recorded deployment of this feature. |
| 28 September 2026 | Local preview verified against `1fd32756`; report/evidence prepared; public Render assets checked again; fresh local test runs and six fictional-data captures completed. | [Live check](article-evidence/live-version-check.json), [test summary](article-evidence/test-summary.json), [capture manifest](article-evidence/capture-manifest.json). |

## 3. Three confirmed failures: reproduction, cause, fix and retest

### A. Normal Add work failed against a stale backend — local preview

**Reproduction:** submit an ordinary Ready-to-invoice entry with client/project, amount 100, next-action date, Automatic responsibility and optional notes. It repeatedly failed in the actual preview. A controlled HTTP probe showed the base request reached validation, but adding `waitingMode: "auto"` produced `400 Unexpected field.`

**Cause:** the server read updated frontend files while its imported backend modules remained old in a running process. Tests against newer source did not describe that process. Generic UI error wording concealed the contract mismatch.

**Fix:** back up the local database, gracefully restart, then snapshot frontend assets at server startup so frontend/backend versions remain paired. Add real form-serializer-to-HTTP coverage and actionable validation/version messages.

**Retest:** the same unchanged browser form saved after restart; the release-blocker suite reported 38 passing tests. A separate saved-USD/default-GBP complaint was checked through settings and reload, without relabelling existing amounts. This was not evidence of production data loss.

Sources: [build log §9](BUILD_LOG.md), [38-test TAP](release-blocker-tests.tap), [contract regression](../tests/create-contract.test.js).

### B. An old startup failure erased a newly validated reset journey — authentication

**Reproduction:** hold startup's request; navigate to a new reset fragment; validate it; observe the new-password form; then reject the old startup request. The old catch called `emailLinks.fail()` on the current journey, clearing the valid reset token and replacing its screen.

**Cause:** startup success/failure callbacks had no check that they still belonged to the active authentication/navigation operation. The first 70 passing tests did not cover this ordering.

**Fix:** capture startup/navigation identities and guard responses before state changes or error handling. Apply equivalent checks to nearby sign-in/focus callbacks and shared API state updates. Keep immediate fragment removal, memory-only token handling, route separation and validation.

**Retest:** 15 additional regressions cover stale successes/failures across config, session and item requests, both link journeys, focus and page exit. Reset tests subsequently submit using the surviving token. The complete suite then reported 85 passing tests.

Sources: [authentication race analysis](AUTHENTICATION_REPAIR.md#startup-race-follow-up--2026-09-25), [browser-boundary regressions](../tests/auth-browser.test.js), [build log §11](BUILD_LOG.md). These are actual-entrypoint/simulated-DOM race tests; do not describe all 15 as physical-browser tests. A reported verification-to-reset screen swap was **not directly reproduced**; it must not be presented as a confirmed token-purpose bypass.

### C. Encrypted restore failed before any account had been deleted — Render smoke test

**Reproduction:** restore a valid encrypted snapshot when the configured deletion-ledger file did not exist. The reported production result was `ENOENT`. Creating an empty ledger allowed restore and existing database checks to complete; temporary restored databases were removed.

**Cause:** the restore script unconditionally read a file that was normally created only by account deletion.

**Fix:** treat only a missing file (`ENOENT`) as an empty ledger; rethrow other read errors. Keep populated-ledger deletion processing, transaction rollback, overwrite refusal, session/token clearing, integrity/foreign-key checks and history protections.

**Retest:** seven CLI regressions cover missing, empty and populated ledgers, malformed JSON, invalid owner type, another filesystem error and existing-destination refusal. The full suite reached 92 passing tests. The production failure/workaround is user-reported; the correction's missing-file case was demonstrated by local regression, not a fresh Render restore in this task.

Sources: [smoke-test report](DEPLOYMENT_CHECKLIST.md#production-smoke-test-results--25-september-2026), [restore tests](../tests/restore.test.js), [build log §12](BUILD_LOG.md).

Additional deployment context: a GitHub credential exposure was reported, followed by the user's confirmation of revocation/deletion. Independent revocation verification is **unknown**. No credential value, account screenshot or private access log is included here. Failed Git authentication and blocked localhost test runs were real operational obstacles; neither justified weakening security checks.

## 4. Test counts: evidence, not certification

| Checkpoint | Count | Source/type |
| --- | --- | --- |
| Initial account/persistence work | 17 passed | [Saved TAP](test-results.tap) |
| Product-logic pass | 27 passed | [Saved TAP](product-pass-tests.tap) |
| Focused usability checks | 17 passed | [Saved TAP](final-usability-tests.tap); full run temporarily blocked at that checkpoint |
| Save/version/currency correction | 38 passed | [Saved TAP](release-blocker-tests.tap) |
| Authentication repair including race | 85 passed | Recorded in authentication/build logs |
| Restore fix | 92 passed | Recorded in build log |
| Repeat iteration | 97 suite tests + 3 separate Chrome tests | Recorded in build log; independently rerun for this pack on 28 September |

**Fresh evidence:** `npm test` reported **97 passed, 0 failed, 0 skipped, 0 cancelled**. The separate `node --test tests/repeat-browser.mjs` run reported **3 passed, 0 failed, 0 skipped, 0 cancelled**, at 1280px, 320px and 390px. [Sanitized counts](article-evidence/test-summary.json) contain no raw logs or credentials. Earlier temporary TAP files referenced by the experiment report were no longer available when this pack was prepared; these fresh runs replace that evidence gap for the current local code, not for historical releases.

Do not add every checkpoint together or call the latest result “100 production tests.” The focused counts are subsets of their full suites. Node's reported totals include nested subtests and parent tests. Screenshot capture is a separate activity and is not counted as extra regression tests.

**Established within tested conditions:** account ownership and logged-out rejection, validation, original due-date/promise rules, currency separation/persistence, history and independent records, session/token invalidation, purpose/expiry/one-use checks, origin protection, replay/conflict handling, recovery behavior, repeat cancellation, keyboard controls and tested narrow layouts.

**Not established:** absence of all vulnerabilities; independent security certification; production host correctness solely from local tests; full device/accessibility coverage; disaster recovery after losing both host and disk; off-host backup/key recovery guarantees; scale or long-term operational reliability; external owner email reminders; reduced late payments; adoption, retention or willingness to pay. Health returning OK is not proof of all features or the backend revision.

Production smoke tests were reported as passing for authentication, isolation, persistence, mobile layout, verification, recovery, one-time rejection, deletion, health and production configuration. That summary does not prove every step/device in the longer checklist. Exact build hours, total actual costs, customer revenue and measured repeat-entry speed are **unknown**. The 10–15-second goal was a target. $19/month remained an unvalidated pricing hypothesis.

## 5. Exact version boundary

- **Reported live Render commit:** `a1ff0856fea4d3c3cddbcb928fccb575b88e4971`. The experiment report records the user's successful push and subsequent Live confirmation.
- **Public frontend verified on 28 September:** Render's `app.js`, `model.js` and `style.css` match that commit byte-for-byte and differ from the local repeat commit. `auth-links.js` matches both, as it was unchanged. Health returned 200/OK. [Timestamped results and hashes](article-evidence/live-version-check.json).
- **Independent current backend Git SHA:** **unknown** from public endpoints. No Render dashboard/backend inspection was performed. The live commit attribution relies on the documented user confirmation, corroborated by public frontend matching.
- **Local source/capture commit:** `1fd32756dec138a08fbc6d6bc5d438afba8a51d2`. “Add another like this” and its streamlined independent-record workflow remain local-only. No push/deploy was performed for this pack.

All new authenticated screenshots are from this **local** commit, using a fresh in-memory database. None is a screenshot of a production account. The history image also shows the local-only repeat button and must not be captioned as the Render UI. The original prototype is a new capture of recovered source, not an archived screenshot from its first release. Its original creation date/commit is **unknown**; the Git blob identifying its retained app source is recorded in the manifest.

## 6. Publication-ready assets and required captions

All six PNGs were opened and visually inspected. Account headers are excluded by real browser element crops; no account addresses, passwords, token URLs, secrets, real customer data or private tester details appear. Only the three explicitly fictional demo scenarios were loaded. Dates are generated relative to the capture date; they are illustrative, not a real client timeline. No DOM text substitution, generated UI, masking or retouching was used.

Use the captions/alt text in [the screenshot index](article-evidence/README.md). The images are ready for publication **with those version and fictional-data disclosures**. The phone image is a full scrolling app-region capture in desktop Chrome at a 390×844 viewport; it is not proof of a physical phone test or one-screen fit. The action image depicts recording an invoice sent elsewhere, not sending one through the app. It was cancelled after capture.

The JSON manifests are sanitized editorial provenance, not necessary article illustrations. Keep the existing experiment report and private source discussions as background; do not republish private tester details or invent quotations. No application source, existing database, Git commit, deployment or DNS setting was changed in preparing this pack.
