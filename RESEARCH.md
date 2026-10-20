# Anime Fighter — ROM research log (SNES SFA2 USA)

## Confirmed from uploaded image
- Filename: `Street Fighter Alpha 2 (USA).sfc`
- Size: 4,194,304 bytes (0x400000), no 512-byte copier header.
- SNES internal title at 0x7FC0 and 0xFFC0: `STREET FIGHTER ALPHA2`.
- Mapping header byte at +0x15: `0x32` (SA-1 / HiROM-related mapping; special-chip mapping needs emulator verification).
- ROM size exponent header byte: `0x0C` (4 MiB).
- Region byte: `0x01`.
- Header checksum at 0x7FDE: `0xDB9A`; complement at 0x7FDC: `0x2465` (sum 0xFFFF).
- Entire-file 16-bit byte sum: `0xDB9A`.
- Alternate header at 0xFFDE: `0xDB99`; complement `0x2466`.
- Reset vector bytes at 0x7FFC and 0xFFFC: `90 FF`. Mapping of this vector must be validated before assuming its execution address.

These observations establish an inspectable ROM image, **not** that any gameplay routines or hitbox tables have been decoded.

## Reconstruction status
- `index.html` is a **new original browser prototype**; it does not execute or copy the ROM's machine code, original sprites or audio.
- Fixed 60 Hz simulation step, independent AABB hurtbox/pushbox/attack box, move phases (startup/active/recovery), six normal attacks, a quarter-circle special, hitstop, hitstun, blockstun, rounds, local versus, and simple CPU are implemented as **provisional authored rules**.
- Numeric move parameters in this prototype are **not extracted from SFA2**.
- Known prototype gaps: air attacks, detailed crouching hitboxes, true input buffering, throw exclusion tests, stage/camera scroll, animation assets, priority and clash rules, training mode, robust controller support, and responsive frame-perfect input on all browsers.

## Next reverse-engineering tasks
1. Verify cartridge mapping and coprocessor behavior in a SNES debugger (Mesen 2 / bsnes-plus).
2. Capture execution traces of character select, round start, movement, punch, kick, blocking, jump and hit reaction.
3. Locate per-character state structures and active collision data through RAM watchpoints; distinguish push, hurt and attack boxes.
4. Record per-frame input, position, velocity, box dimensions, hitstop, stun and state transitions for representative moves.
5. Document observed vs inferred values separately; add reproducible tests against the browser simulation.
6. Replace temporary rules with verified behavior where feasible. Do not implement grabs.

## Controls
P1: A/D, W, S, Q/E/F punches, Z/X/C kicks, R guard.
P2: arrow keys, U/I/O punches, J/K/L kicks, P guard.
Special: down, down-forward, forward + heavy punch (relative to facing).
Enable the debug button to inspect collision rectangles.

## IP / asset policy
Use the ROM only for analysis and comparison. Browser release uses original code and replacement artwork/audio; do not redistribute the ROM or copyrighted game assets.


## 2026-10-20 — Wesley v0.3 and SNES ROM verification

ROM file: `Street Fighter Alpha 2 (USA).sfc` (4,194,304 bytes).
SHA-256: `910a29f834199c63c22beddc749baba746da9922196a553255deade59f4fc127`.
At `0x7FC0` and `0xFFC0`, the title is `STREET FIGHTER ALPHA2`; both mapping bytes at header +0x15 are `0x32`, and both reset vectors are `0xFF90`. The two checksum fields differ by one, so debugger confirmation of the active header and cartridge mapping is required. These bytes **do not reveal attack timing or collision tables**. No disassembly of gameplay routines has yet been completed.

### Evidence and implementation boundaries
- The v0.3 browser game loads the user-supplied `assets/characters/Wesley.png` (96×96 cells, 4 columns, 13 populated rows) through PixiJS.
- Rows mapped: idle 0, guard 1, low-health 2, hurt 3, heavy 4, kick 5, punch 6, fire 7, walk 8, backwalk 9, victory 10, alternate idle 11, KO 12.
- Attack phases and hitbox sizes are **authored prototype values**. No hitboxes or move data have been extracted from the SNES ROM.
- MUGEN's documented `StateDef`, `ChangeState`, `HitDef` and state-trigger approach inspired the v0.3 CPU behavior/state separation, but this is **not** a port of Kung Fu Man's exact state tables.
- Debug overlays distinguish hurtboxes, pushboxes and attack boxes. Projectile uses a separate world-space hitbox.
- Future work: cartridge debugger trace, locate runtime position/state values, observe hitbox geometry with RAM breakpoints, extract/validate per-frame data, automate browser tests.

References:
- https://network.mugenguild.com/justnopoint/1.1docs/cns.html
- https://github.com/fanyer/mugen/blob/master/chars/kfm/kfm.cns

