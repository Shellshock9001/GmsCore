# Experimental svc189 + TS.43 integration — verified results

**This branch is an interoperability experiment, not a bounty claim or a proven working RCS implementation.**

## Source ownership and provenance

- Original Constellation / Asterism work: microg/GmsCore [PR #3808](https://github.com/microg/GmsCore/pull/3808), revision `223f91fe0cde7be17bcd19bb0e28087526d98bc4`.
- Seven focused TS.43/EAP-AKA fixes: [review PR #8](https://github.com/Chess-Debug/GmsCore/pull/8), revision `df938825bb18db89d7360fc5328063cdb7f9248a`.
- Dedicated `RcsService` (svc189), `IRcsService`, `RcsCallerPolicy` and tests: **Entirework-blip's** [upstream PR #3809](https://github.com/microg/GmsCore/pull/3809), revision `ce3e728cd4cdffa54ceae037eeeee3307cc810c9`. Original authorship and Apache-2.0 license preserved.
- Combined code revision: `40f77c109ca2bb23ba0a4871ccf16164eefb9084` on `experiment/rcs-svc189-integration`. Integration only moved `rcs.START` out of `DummyService` and removed diagnostic dumps of possibly sensitive request extras. **This is not included in PR #8**.

## Real compiled artifact

- [Successful Android build and Constellation module/unit tests, run 39751632231](https://github.com/Shellshock9001/GmsCore/actions/runs/39751632231): PASS. Actual checkout commit `40f77c109ca2bb23ba0a4871ccf16164eefb9084`.
- Output: real `:play-services-core:assembleVtmDefaultDebug` APK, 107,696,107 bytes, Java-debug-signed, six DEX files.
- SHA-256: `D5835DD56DB514442439BAAB3C03F07EAC6436EEBD6F116EF38F3F0E2ECBF965`.
- Independent ZIP CRC, JAR signature, and DEX checks: PASS, including the contributed service-189, binder and caller-policy classes.
- [Additional full build + `:play-services-core:testVtmDefaultDebugUnitTest`, run 39752846353](https://github.com/Shellshock9001/GmsCore/actions/runs/39752846353): both **PASSED**. Its final manifest inspection step had a **script-only failure** (`aapt2` required `--file`), not a compilation or unit-test failure. Do not misreport that workflow as green.
- [Compiled manifest inspection on the *same successful APK*, run 39754764579](https://github.com/Shellshock9001/GmsCore/actions/runs/39754764579): **PASS**, using official `aapt2 dump xmltree --file AndroidManifest.xml "$apk"`.

Actual decoded manifest shows **exactly one** occurrence of `com.google.android.gms.rcs.START`, under an exported `org.microg.gms.rcs.RcsService` in the `:persistent` process. It is no longer routed to the dummy service.

## Hard limitations

The svc189 `IRcsService.Stub` remains an incomplete private-GMS Binder contract. Return of a successful initial service binding does **not** mean Google Messages can complete subsequent Binder transactions. The code has **not** been deployed on a bootloader-locked, non-root microG device with an active SIM. Carrier phone verification, ACS provisioning, RCS transport registration, and real inbound/outbound message delivery are all **unverified**. No test device or SIM was modified. No BountyHub solution was claimed and no payout is confirmed.

A qualifying next test needs a real Google Messages client, appropriately permissioned microG OS with locked verified boot and real SIM, plus redacted post-bind service-189 transaction and Constellation traces. Do not publish subscriber IDs, bearer tokens or message contents.

## Offline evidence on the owner's Windows machine

Workspace: `C:\shellshockhive\microg-rcs-support`, isolated worktree `experiment-svc189`.

- `artifacts/github-ci-39751632231/vtmDefault/debug/com.google.android.gms-252432000.apk`
- `evidence/github-ci-39751632231-full.log`
- `evidence/ci-apk-verification-39751632231.json`
- Verifier: `scripts/verify-ci-apk.py --run-id 39751632231 --source-sha 40f77c109ca2bb23ba0a4871ccf16164eefb9084 --worktree experiment-svc189 --require-rcs-service`

This document is purely experimental evidence and does not alter the compiled code revision used above.
