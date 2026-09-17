# PCSX2XR-SA Phase 0 Design

Date: 2026-09-17

## Goal

Establish a reproducible, known-good Windows baseline for PCSX2XR-SA before any OpenXR or GTA San Andreas camera work begins.

Phase 0 succeeds when an unmodified build of the stereoscopic PCSX2 base can be produced from this repository and run GTA San Andreas on Windows 11 using the user's own PlayStation 2 BIOS and game ISO.

## Fixed Base

- Repository: `Zeroable/PCSX2XR-SA`
- Upstream stereo fork: `AndrewGDX/pcsx2-stereoscopic`
- Baseline commit: `84bb90d138942300317b89b73c9edf449adc0241`
- Upstream release represented by that commit: stereoscopic fork 0.1.1
- Default development renderer for the later XR work: Vulkan
- Initial platform: Windows 11 x64

The `master` branch is treated as the preserved upstream stereo snapshot for Phase 0. Project changes begin on dedicated PCSX2XR-SA branches.

## Initial Game Target

Phase 0 is concerned only with GTA San Andreas for PlayStation 2.

The first intended executable target is the NTSC-U release with serial `SLUS-20946`. Before any game-specific memory or camera patches are added in a later phase, the exact ISO revision and CRC will be verified from the user's copy rather than assumed.

No BIOS, GTA San Andreas ISO, Rockstar assets, Sony assets, save files, or other copyrighted game data are stored in or distributed with this repository.

## Build Strategy

Use the Windows GitHub Actions workflows inherited from the stereo fork.

The existing workflow currently produces multiple Windows x64 variants, including MSVC and clang builds and SSE4/AVX2 variants. For Phase 0 we do not rewrite that matrix. The first objective is to prove that the inherited workflow still builds successfully from this fork at the frozen stereo commit.

For hands-on testing, prefer a normal Release x64 MSVC build. AVX2 may be used on compatible hardware, but Phase 0 does not depend on AVX2-specific behavior.

A local Visual Studio/CMake toolchain is not required for the first validation. Local development tooling can be introduced later when interactive debugging becomes necessary.

## Runtime Validation

The Phase 0 test on Windows 11 is:

1. Launch the newly built PCSX2 executable.
2. Configure a user-supplied PS2 BIOS.
3. Add or directly boot the user-supplied GTA San Andreas ISO.
4. Confirm the game is identified as `SLUS-20946` or record the actual detected serial.
5. Select Vulkan as the renderer.
6. Boot through the game to normal gameplay.
7. Confirm ordinary controller input works.
8. Confirm normal non-stereo rendering is stable.
9. Enable the fork's stereoscopic rendering.
10. Confirm the game produces distinct left/right stereo views and remains playable long enough to establish a baseline.
11. Record any GTA-specific stereo defects separately; those defects do not automatically fail Phase 0 unless they prevent usable stereo rendering.

## What Phase 0 Does Not Include

Phase 0 intentionally does not add:

- OpenXR
- Quest headset output
- head tracking
- positional tracking
- GTA camera memory injection
- motion-controller support
- first-person camera changes
- HUD reconstruction
- cutscene handling
- frustum/culling patches
- performance or frame-pacing modifications
- rebase onto current upstream PCSX2
- support for other PS2 games

Those are later phases.

## Branching Policy

- `master`: preserved stereo 0.1.1 baseline at `84bb90d`
- `phase0-baseline`: Phase 0 validation/documentation work
- Later XR implementation will use separate feature branches and merge only after each milestone is verified.

This keeps a permanently recoverable known-good stereo base.

## Success Criteria

Phase 0 is complete when all of the following are true:

- GitHub Actions produces a usable Windows x64 build from this fork.
- The executable launches on the target Windows 11 PC.
- A user-supplied BIOS is accepted.
- The user's GTA San Andreas ISO boots to gameplay.
- Vulkan rendering works.
- Controller input works.
- The stereoscopic renderer can be enabled and produces separate left/right views.
- The exact GTA serial/CRC used for later game-specific work is recorded.
- No PCSX2XR/OpenXR code has been added yet.

## Exit to Phase 1

After Phase 0 is verified, Phase 1 will introduce the smallest possible OpenXR integration. The first XR milestone will be a head-locked image presented to the headset, without GTA camera injection. Stereo eye submission and head-tracked GTA camera control will follow as separate milestones so failures remain easy to isolate.
