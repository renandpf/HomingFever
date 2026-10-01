# Homing Fever — RG353P / ArkOS test port

Experimental local package for Anbernic RG353P with ArkOS 2.0 (02092025),
AArch64, glibc 2.30 and PortMaster. Startup and visible output were confirmed
on the device with revision r3; full gameplay validation is still pending.
Thanks to Artur Rojek (zear), the original game author.

## Build on an x86_64 Linux notebook

Requires Docker with a running daemon accessible by the current user:

```sh
./platform/rg353p/build.sh
```

The Ubuntu 18.04 container contains an AArch64 cross compiler and SDL 1.2
headers/libraries. No QEMU or native host SDL installation is needed.
This older base is used only as a compatibility build environment.
Both DEBUG=1 (warnings are errors) and release builds are performed.
The script prints ELF architecture, shared dependencies and GLIBC symbol versions.
Output: `release/homingfever-rg353p.zip`.

## Install and test

Extract the ZIP into `/roms2/ports/`, preserving its structure:

```text
/roms2/ports/Homing Fever.sh
/roms2/ports/homingfever/fever.aarch64
/roms2/ports/homingfever/data/...
```

Launch **Homing Fever** from the console's Ports menu, not directly from SSH.
If necessary, restart EmulationStation to refresh the menu.
The launcher looks for PortMaster beside the selected ROM partition first.
Revision r3 bundles sdl12-compat 1.2.68 under `libs.aarch64/`. It translates
SDL 1.2 calls to the installed SDL 2 library, bypassing the system SDL 1.2
which stalled during initialization on the first device test. Only the game
process receives the private library path; system libraries are not replaced.
Video and joystick initialization are logged separately. The user confirmed
that revision r3 starts successfully and resolves the black screen.

- D-pad left/right: turn (native joystick hat).
- Left analog stick: steer toward the selected direction (native axes 0/1).
- A or B: skip intro / start game (both confirm for this first test package).
- Start: start game / pause / resume after the original two-second delay.
- Select: return to title when not paused; press again to quit.
- Start + Select: PortMaster emergency exit; may bypass saving.

PortMaster gptokeyb maps buttons to keyboard events; native joystick button
handling is disabled only for this build to prevent duplicate actions.
Hat/axis assignment and button mapping must be verified on the actual device.
Configuration (`game.cfg`), record (`score.dat`) and launch log (`log.txt`)
are stored in `homingfever/`. Updating the ZIP does not overwrite saves.

If launching fails, collect:

```sh
cat /roms2/ports/homingfever/log.txt
LD_LIBRARY_PATH=/roms2/ports/homingfever/libs.aarch64 ldd /roms2/ports/homingfever/fever.aarch64
```

Remaining device checks: steering, button mapping, pause/resume, return to
menu, record persistence, visual correctness, performance and a second launch.
The log identifies the selected SDL 2 backend and startup stage.

## Licenses and scope

Game source: MIT, copyright Artur Rojek. Graphics and fonts: CC0 according to
the upstream README. Original license and README are included under `licenses/`.
sdl12-compat 1.2.68 is redistributed unmodified under the zlib license, included
in `licenses/SDL12-COMPAT-LICENSE.txt` along with its source location.
SDL 2 and gptokeyb are supplied by the device/PortMaster, not redistributed here.
This is a local testing package, not an approved PortMaster catalog submission.
