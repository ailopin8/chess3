# CHESS³ | King Command

[Play CHESS³](https://ailopin8.github.io/chess3/)

An 8 × 8 × 8 browser chess simulation where two kings coordinate autonomous pieces. All piece models are made from cubes. The game is entirely self-contained in `index.html`; GitHub Pages serves it directly.

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

## Hosting

This is a single-page site: publish from the `main` branch root using GitHub Pages. No Jekyll theme processing, server, package installation or API key is required. The visual style is inspired by the GitHub Pages Hacker theme, adapted directly in its CSS.
