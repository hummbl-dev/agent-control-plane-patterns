# Receipt — Agent Control Plane Patterns v0.1 Packet

This receipt records what was produced for the v0.1 packet, under what
authority, and with what posture. It is required by the v0.1 schema
(`receiptRequirements.receiptRequired: true`).

## Authority and posture

- **Posture:** non-canon
- **Review state:** none
- **Provenance:** https://github.com/hummbl-dev/agent-control-plane-patterns
- **Produced under:** v0.1 candidate scope (see `docs/v0.1-boundary.md`)

This packet is **candidate** material. It is not an adopted specification, not a
universal standard, and not a product. It must not be described as "adopted",
"standard", or "canonical".

## Issues addressed

- #1 — Define v0.1 scope and boundary
- #2 — Collect prior art and adjacent ecosystem references
- #3 — Design minimal schema/examples layout

## Artifacts produced

| Piece        | Path                                                                  |
|--------------|-----------------------------------------------------------------------|
| Boundary     | `docs/v0.1-boundary.md`                                               |
| Prior art    | `docs/prior-art.md`                                                   |
| Schema       | `schemas/agent-control-plane-patterns-v0.1.json`                      |
| Example      | `examples/control-plane-packet-v0.1.example.json`                     |
| Valid fixture| `fixtures/valid/control-plane-packet-v0.1.valid.json`                 |
| Invalid fixture | `fixtures/invalid/control-plane-packet-v0.1.invalid.json`          |
| Receipt      | `receipts/agent-control-plane-patterns-v0.1-packet-receipt.md` (this file) |

## Validation expectations

- `fixtures/valid/control-plane-packet-v0.1.valid.json` **must** validate
  against `schemas/agent-control-plane-patterns-v0.1.json`.
- `fixtures/invalid/control-plane-packet-v0.1.invalid.json` **must fail**
  validation. It omits the required `controlPlaneManifest.version` field, which
  is required by the schema. Future CI should assert that this fixture fails
  validation against the schema.

## Non-canon reminder

Nothing in this receipt or the artifacts it references is canon. All v0.1
artifacts are candidate material pending a reviewed governance path that does
not yet exist.
