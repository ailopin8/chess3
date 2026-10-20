# CHESS³: Diagonal Deployment and Relational Army Expansion
**Status:** Diagonal deployment is implemented on `main`. Additional characters described below are proposals, NOT currently playable.

## Purpose
Make the king a strategic controller of an adaptive multi-agent army on a 512-cell 3D lattice. Give units complementary movement, roles, and explicit relationships so coordination and emergent strategies arise from interacting capabilities, rather than arbitrary large armies.

## Phase 0 — implemented 32-piece baseline
- White back rank `(x,3,0)`; pawns `(x,3,1)`, for x=0..7.
- Black back rank `(7-x,4,7)`; pawns `(7-x,4,6)`.
- Both ranks are mirrored across X/Y/Z about the cube's center; the kings begin on `(4,3,0)` and `(3,4,7)`, differing on all three axes.
- Critically, pawn Y coordinates differ by one, making diagonally-forward captures possible. Moving both armies to far-opposite XYZ corners under the current pawn rules would prevent most pawns from meeting. Opposed corners would require a different pawn vector and therefore a new variant.
- The original six piece classes and king-only command system are unchanged.

## Recommended Phase 1 — 48 pieces, not yet implemented
**24 per side: 16 current pieces + 8 new pieces (two each of four new classes).**
That occupies 48 / 512 = 9.375% of the 3D cells at the start. For comparison, the current 32 / 512 = 6.25%. Cell density is not the same as tactical branching factor; measure legal move count, checks and time per royal dispatch after adding roles.

| New role | Per side | Prototype colour | Movement identity | Unique skill / relationship |
|---|---:|---|---|---|
| **Warden (W)** | 2 | Steel / silver | Single-step along six axes | GUARD: can expend a royal command to protect an adjacent non-king ally from capture for the next enemy turn. Guard is visible, expires, cannot protect kings, and has a cooldown. |
| **Weaver (V)** | 2 | Teal | Sliding up to two squares along any face diagonal (12 directions); blockers matter | LINK: expends an order to connect two allied pieces within its local range. The king rewards linked sequences and the edge expires after two friendly turns; no second move is created. |
| **Riftwalker (F)** | 2 | Magenta | Unblocked `(±2,±1,±1)` jump, with all axis permutations (24 targets from an unrestricted interior cell) | PHASE: the jump bypasses intervening pieces. The movement itself is the skill; no teleport anywhere. |
| **Oracle (O)** | 2 | Pale amber | Single-space-diagonal step `(±1,±1,±1)` (8 directions) | FORECAST: an order computes a bounded one-ply sample of the opponent's strongest possible replies and updates the king's tactical model; an explicit cooldown prevents repeated zero-cost forecasts. |

**Design constraints:** exactly one action per army turn. A move or an activated skill consumes that turn. Only the king chooses what action any unit executes. No unit may be granted extra physical moves by a relationship bonus. The ward does not apply to kings and must be checked in move/capture legality rather than only in presentation.

## Proposed extra starting rank
- White support units `(x,2,0)` for x=0..7.
- Black support units `(7-x,5,7)` for x=0..7.
- Symmetrical role sequence, suggested `[W,F,V,O,O,V,F,W]` for both armies, mirror X on Black.
- No collisions with the existing back ranks or pawn ranks. Additional opening safety tests are required before activation.

## Algorithms and relationships
A new character must have both a movement kernel and relational policy, not just different voxel art.
Define typed, timed directed edges on units:
- `COMMAND(King -> Piece; action, objective)`
- `THREAT(Piece -> Cell/Piece; attack geometry)`
- `GUARD(Warden -> Ally; expiry, cooldown)`
- `LINK(Weaver -> Ally A/B; expiry, synergy)`
- `FORECAST(Oracle -> King; predictions, confidence)`
- `SUPPORT(Piece -> Ally; recapture / defended squares)`

King coordination at each turn chooses a legal action `a*` from all its agents according to:
`a* = argmax[objective_alignment + support_synergy + expected_material_gain + king_safety - capture_risk - predictability_penalty]`.
This is a heuristic design objective, not a claim of actual minimax or reinforcement learning already implemented.

Represent time-varying edges as `R_t[source][target][relationType]`; each edge carries metadata, expiry and evidence. Do not conflate visual proximity with an active relation.

## Encoding migration requirement
The existing board uses **4 bits per cell** (one side bit plus three piece-type bits) and supports six piece types. Ten piece classes create at least `1 empty + (2 sides × 10 types) = 21` distinct states. Four bits encode only 16 combinations, so **a minimum of 5 bits per cell is required**.

- New occupancy encoding: `5 bits × 512 cells = 2,560 bits`.
- Allocate four type bits (10 types supported, capacity for more), plus a team bit.
- Reserve an all-zero code for empty. Update `cellBits`, `matrixBits`, exports, imports, render legend, `SYMBOL`, `SHORT`, `TYPE_NAMES`, `VALUE`, `pieceVoxels`, `vrVoxelGeometry`, `attackSquares`, `pseudo`, and legality/capture handling.
- Import older four-bit saves through version-aware decoding, not silent reinterpretation.
- Role instructions and relationship matrices are *separate state*, not shoehorned into occupancy bits.

## Recommended staged integration
1. Establish benchmark using the deployed 16-per-side diagonal game.
2. Add **Riftwalkers** first (20 per side) to test new geometric leap paths.
3. Add **Wardens** next (22 per side), including explicit ward state, capture rules and safety tests.
4. Add **Weavers + Oracles** last (24 per side) for relationship and forecast algorithms.
5. Test complete matches, move legality, export/import compatibility, desktop/mobile input, 2-eye WebXR rendering, and actual Meta Quest performance after each stage.

**Do not** adopt 32 per side until telemetry demonstrates the king heuristics still produce meaningful engagements and the VR renderer holds a stable frame rate.
