# PCSX2XR-SA Phase 0 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox syntax for tracking.

**Goal:** Produce an unchanged Windows stereo baseline and validate GTA San Andreas on the user's Windows 11 PC.

**Architecture:** Preserve the stereo emulator source at commit 84bb90d138942300317b89b73c9edf449adc0241. Use the inherited Windows Actions matrix unchanged; a documentation commit on phase0-baseline triggers the push workflow. Separate CI build success from user-verified gameplay success.

**Tech Stack:** Windows 11 x64, GitHub Actions windows-2025, MSVC/clang, Qt, Vulkan.

**Spec:** docs/superpowers/specs/2026-09-17-pcsx2xr-sa-phase0-design.md

## Global Constraints

- Baseline commit: `84bb90d138942300317b89b73c9edf449adc0241`
- Initial platform: Windows 11 x64
- Default development renderer for the later XR work: Vulkan
- `master`: preserved stereo 0.1.1 baseline at `84bb90d`
- `phase0-baseline`: Phase 0 validation/documentation work
- No BIOS, GTA San Andreas ISO, Rockstar assets, Sony assets, save files, or other copyrighted game data are stored in or distributed with this repository.
- Verify the actual user's game serial and CRC; do not assume a particular CRC.
- Do not rewrite the inherited Windows matrix, rebase, or add OpenXR, head tracking, camera patches, or any emulator behavior changes.

## Task 1: Trigger and verify the inherited build

**Files:**
- Create: `docs/superpowers/plans/2026-09-17-pcsx2xr-sa-phase0.md` (this plan).
- Inspect only: `.github/workflows/windows_build_matrix.yml`.
- Inspect only: `.github/workflows/windows_build_qt.yml`.

**Interfaces:** Consumes the approved spec and existing phase0-baseline branch; produces an Actions run tied to the new documentation commit.

- [x] Read the approved spec and both Windows workflows. Confirm push events include phase0-baseline and the matrix retains MSVC/clang SSE4/AVX2 and CMake variants.
- [ ] Commit this document to phase0-baseline with message "docs: add Phase 0 build and validation plan". Do not alter master.
- [ ] Query Actions runs for the returned commit SHA. Record the Windows Builds run URL and commit SHA.
- [ ] Inspect job results. If a build fails, obtain the failing step and logs before proposing any fix. Permission/approval blockers require user action; do not bypass them.
- [ ] On success, list artifacts and choose the non-symbols artifact beginning `PCSX2-windows-Qt-x64-sse4-msvc`. Record artifact URL and exact name. AVX2 is optional on compatible hardware.
- [ ] Confirm the branch diff against the frozen baseline contains documentation only. A build is not gameplay validation.

**Verification:** Windows compilation and artifact upload succeed. Report matrix failures separately even if the preferred artifact succeeds. The CMake workflow's "Run Tests" step builds the unittests target; do not claim tests executed without confirming the logs.

## Task 2: Validate the build on the target PC

**Files:** No emulator source edits. Record the user's results in a later documentation-only validation record after actual testing.

**Interfaces:** Consumes the Windows artifact from Task 1; produces an explicit pass/fail report and observed game serial/CRC.

- [ ] Download and extract the non-symbols build into a new dedicated folder. Keep existing PCSX2 installations and memory cards untouched; use copies of any existing memory cards.
- [ ] Launch the executable. Configure the user's local BIOS and ISO without uploading or committing them.
- [ ] Record Windows version, CPU/GPU, executable/build commit, detected GTA serial, and CRC from game properties or the emulator log.
- [ ] Select Vulkan and map the controller. Leave stereo disabled for the first test.
- [ ] Reach gameplay, walk and drive, and confirm baseline rendering and controller input. Record crashes or graphics defects.
- [ ] Enable the fork's existing stereo setting and side-by-side output. Capture a gameplay screenshot showing the two eye views; verify near/far objects exhibit different horizontal disparity, not merely duplicate images.
- [ ] Play for at least ten minutes, including walking and driving. Record stereo defects separately and whether they prevent usable stereo.
- [ ] Mark Phase 0 complete only when CI, launch, BIOS, gameplay, Vulkan, controls, distinct stereo views, and recorded serial/CRC are all verified.

## Handoff and preservation

Provide the exact successful run/artifact link and the short Windows validation checklist. Keep Phase 0 incomplete until the user reports runtime results. Preserve the successful baseline commit and download; do not call an untested artifact "known-good gameplay". Phase 1 requires a separate approved implementation scope.
