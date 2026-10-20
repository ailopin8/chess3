# CHESS³ | King Command

[Play CHESS³](https://ailopin8.github.io/chess3/)

An 8 × 8 × 8 browser chess simulation where two kings coordinate autonomous pieces. All piece models are made from cubes. The game is entirely self-contained in `index.html`; GitHub Pages serves it directly.

## Diagonal start (implemented)

Both 16-piece armies start diagonally across the cube. White's back rank and pawns occupy Y=4, Z=1–2; Black's mirrored ranks occupy Y=5, Z=7–8 (1-indexed coordinates). The kings start at **e4·1** and **d5·8**, differing along X, Y and Z. Adjacent Y files keep pawn captures possible under the existing +Z/−Z pawn rule. This is not a far-corner setup, which would require reworking pawn movement. The change is live in `index.html`; the game still begins with 32 pieces.

An expanded game with **24 pieces per side (48 total)** and four distinct new role classes is proposed, **not yet implemented**. See [Expansion Design](EXPANSION-DESIGN.md). Adding ten total classes will require five-bit occupancy encoding and new game/VR legality tests.

## Piece colour key

| Role | Colour | White army | Black army |
|---|---|---|---|
| King | Gold | Bright gold | Deep gold |
| Queen | Pink | Bright pink | Deep pink |
| Rook | Cyan | Bright cyan | Deep cyan |
| Bishop | Violet | Bright violet | Deep violet |
| Knight | Orange | Bright orange | Deep orange |
| Pawn | Green | Bright green | Deep green |

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
