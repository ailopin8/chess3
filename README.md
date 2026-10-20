# CHESS³ | King Command

[Play CHESS³](https://ailopin8.github.io/chess3/)

An 8 × 8 × 8 browser chess simulation where two kings coordinate autonomous armies of **24 pieces each (48 total)**. All piece models are made from cubes. The game is entirely self-contained in `index.html`; GitHub Pages serves it directly.

## Diagonal start (implemented)

Both 24-piece armies start diagonally across the cube. White's back rank and pawns occupy Y=4, Z=1–2; Black's mirrored ranks occupy Y=5, Z=7–8 (1-indexed coordinates). The kings start at **e4·1** and **d5·8**, differing along X, Y and Z. Adjacent Y files keep pawn captures possible under the existing +Z/−Z pawn rule. This is not a far-corner setup, which would require reworking pawn movement. The diagonal deployment is live in `index.html`; the game now begins with 48 pieces, including the mirrored support rank.

The expansion is **implemented** in `index.html`. See [Expansion Design](EXPANSION-DESIGN.md) for the design constraints and original rollout plan. The new movement rules and abilities have been tested locally; headset testing remains outstanding.

## 48-piece live rules (October 2026)

The original 16-piece-per-side formation remains diagonally mirrored. Each king now commands **8 additional supporting pieces** on a separate rank: `[Warden, Riftwalker, Weaver, Oracle, Oracle, Weaver, Riftwalker, Warden]`, giving 24 pieces per side. The game uses 10 distinct character classes and 5-bit occupancy (`512 × 5 = 2,560 bits`). The source is standalone HTML and JavaScript; no backend or installation is required.

- **Warden (W):** moves one cell orthogonally along X, Y or Z. GUARD costs a complete turn and shields a neighboring non-king ally for the next opposing turn. Protected allies may not be captured, but geometric attacks still exist and the king cannot be shielded. Skills have cooldowns.
- **Weaver (V):** slides up to two squares along any of the 12 face diagonals; intervening pieces block movement. LINK costs a complete turn and creates a temporary two-unit support relationship, affecting king command scores and visible as a line.
- **Riftwalker (F):** jumps to any destination matching a signed permutation of `(2,1,1)`, up to 24 distinct destinations from an unrestricted location. It leaps over occupied intervening cells and may capture on arrival.
- **Oracle (O):** steps to any of the 8 space-diagonal neighboring cells. FORECAST costs a turn and records a sample of the enemy's immediate capture threats for king decision-making.

Skills are issued by the king like moves: **one action per army per turn**. Typed relationship effects and expiry/cooldown data are tracked separately from cell occupancy. King safety and normal captures remain governed by the full 3D move legality checks. The new five-bit save format is **version 4**; older state snapshots missing ability data load with empty relationship effects.

**Local testing:** 48-piece mirrored deployment and no duplicate positions; each new movement kernel checked at the board center (W 6, V 24, F 24, O 8); blocked sliding, jumping, pawn captures, king safety, GUARD expiry, LINK, FORECAST, cooldowns, 48 autonomous dispatches, 5-bit export/import, older snapshot restore, mobile configuration, and simulated WebXR rendering of both eyes. No JavaScript errors were observed during these tests. Actual Quest 3S hardware and public Pages runtime are **not** verified.

## Piece colour key

| Role | Colour | White army | Black army |
|---|---|---|---|
| King | Gold | Bright gold | Deep gold |
| Queen | Pink | Bright pink | Deep pink |
| Rook | Cyan | Bright cyan | Deep cyan |
| Bishop | Violet | Bright violet | Deep violet |
| Knight | Orange | Bright orange | Deep orange |
| Pawn | Green | Bright green | Deep green |
| Warden | Silver | Light steel | Deep steel |
| Weaver | Teal | Luminous teal | Deep teal |
| Riftwalker | Magenta | Bright magenta | Deep magenta |
| Oracle | Pale amber | Light amber | Deep amber |

Shapes and letters also identify the roles.

## Desktop and mobile

- **Run** starts autonomous orders; **Step** advances one royal instruction; **Reset** starts a new match.
- Select the **White King** or **Black King** and set a royal doctrine.
- Drag/touch the board to orbit; scroll to zoom. Use layer controls to inspect Z levels.
- The king's coordination algorithm selects pieces and moves; players do not drag pieces to play.
- Mobile: use **⚙** to open the command panel.

## Meta Quest 3S / WebXR

Open the [HTTPS GitHub Pages game](https://ailopin8.github.io/chess3/) in **Meta Quest Browser**. Choose **ENTER VR** if the browser supports `immersive-vr`. The game requests a `local-floor` reference space, falling back to `local`.

Controller intent:
- **Trigger** inspects a piece and its last order.
- **Grip** moves the cubic environment relative to the viewer.
- **Thumbstick** rotates the environment.
- **A / X** runs or pauses the order loop, depending on controller mapping.
- **B / Y** steps one order while paused, depending on controller mapping.

**Stereo display fix (2026-10-20):** Previously the renderer cleared the shared WebXR framebuffer inside each eye's rendering loop. That could erase the first eye when drawing the second. Now it clears the framebuffer **once per XR frame** and sets each eye's own `XRWebGLLayer.getViewport(view)` before drawing. The VR status reports the number of rendered eye views.

A scripted two-eye WebXR simulation confirmed distinct left and right viewport draw calls with a single framebuffer clear. **Physical headset testing is still required**: browser simulations cannot guarantee performance or display-compositor behaviour on every device.

If the game appears stale, reopen it using [this cache-busting link](https://ailopin8.github.io/chess3/?v=stereo-colors-2). Exit and re-enter immersive VR to restart the XR session. If one eye remains dark, note which eye and whether ordinary Quest Browser menus also show the problem.

## Spatial manipulation / cube size (2026-10-20)

CHESS³ now has a **SPACE MANIPULATOR** in the Royal Command Centre. This moves and resizes the *displayed* 8 × 8 × 8 space while preserving the underlying chess rules.

- **Desktop:** select ORBIT or MOVE before dragging the cube. Use the **Cube size / spatial scale** slider to adjust the space from **35% to 300%**. Use the six positional arrow controls for left/right/up/down/near/far. **RESET SPACE & VIEW** restores the default transform. Shift+wheel adjusts spatial scale; ordinary wheel controls camera zoom.
- **Touchscreen:** select MOVE or ORBIT; drag with one finger. Pinch with two fingers to change scale and translate.
- **Quest 3S controllers:** hold a single grip to move the whole game cube. Hold **both grips**, then spread or close controllers to scale it; moving both controllers translates the cube and twisting them rotates it. **Left thumbstick** moves the cube horizontally or nearer/farther. **Right thumbstick** rotates and tilts. Press and hold the right thumbstick while moving it vertically to resize. Press **both thumbsticks** to reset.
- **Game JSON:** Save includes the spatial display scale, desktop pan and VR pose and restores them on import.

A standalone JavaScript test verified scale clamps, 2D movement, reset, both-grip scale and translation, single-grip translation, and preservation of the stereo rendering path. Browser rendering and physical headset testing still require validation on a device.

## Hosting

This is a single-page site: publish from the `main` branch root using GitHub Pages. No Jekyll theme processing, server, package installation or API key is required. The visual style is inspired by the GitHub Pages Hacker theme, adapted directly in its CSS.
