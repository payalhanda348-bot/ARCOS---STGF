# ARCOS-STGF

**Specification, Testing & Governance Framework for ARCOS.**

ARCOS makes an NFT a Programmable, Operational Digital Asset — not by replacing ERCs, and not by assembling them, but by building each capability module distinctively and coordinating all of them through one reusable orchestration layer, MAS.

## Read this in order

1. **This README** — the problem, the core finding, how the repo works
2. [`module-taxonomy.md`](./specs/module-taxonomy.md) — what makes something a Module vs. Extension vs. Standard, and how each module gets built once it's classified.  `modules/` — each of the eight modules, ERC-mapped, spec status varies
3. `mas.md` — the coordination layer
4. `tier-3.md` — the enforcement model



## The problem

NFT standards provide important building blocks. Ownership, transfer, permissions, royalties, metadata, verification, rights, composition, and other capabilities can each be represented through a standard, an extension, or custom contract logic.

But a Programmable, Operational Digital Asset/NFT needs more than having those capabilities individually. It needs them to operate as one system.

A strong project can build that coordination itself — write custom Solidity that connects its ownership, permissions, royalties, licensing, and verification logic correctly for that one NFT. The problem isn't that this coordination is impossible. It's that it's project-specific infrastructure: the next project has to build its own version again, from scratch.

**ARCOS's central proposition:** turn the coordination of NFT capabilities from project-specific engineering into reusable infrastructure.

## The core finding

ARCOS separates the problem into layers:

```
NFT capabilities
       ↓
ARCOS Modules
       ↓
     MAS
       ↓
Tier 3 execution
       ↓
Operational Digital Asset / NFT
```

ERC standards remain valuable — they provide standardized interfaces, established patterns, and individual capabilities. ARCOS doesn't try to replace them. It asks a different question: **how should these capabilities exist and operate together, inside one asset?**

The answer varies by capability, not by a single fixed rule:

- Some can **reuse** an established standard as-is.
- Some need to be **unified**, because several standards approach the same problem differently and don't coordinate with each other.
- Some need ARCOS to add **state or enforcement** the standard doesn't provide.
- Some need to be **designed from nothing**, because no existing standard adequately covers the required behavior.

The detailed ERC-by-ERC analysis lives in [`erc-capability-mapping.md`](./decisions/erc-capability-mapping.md), not here — this page is the architecture, not a standards survey.

## The approach

- **Modules** — eight governed state machines, each built by whichever pattern fits its ERC material: Ownership, Transfer, Permission, Metadata, Verification, Royalty, Rights, Composition.
- **MAS** — the reusable coordination layer. A module's contract doesn't call another module's contract directly; it asks MAS, which resolves the decision off-chain using whichever modules actually have a stake, then returns one signed result the contract verifies.
- **Tier 3** — the enforcement model. ARCOS-native contracts only; hooks (`transferFrom`, `tokenURI`, `royaltyInfo`, plus a single generic dispatcher for module-native operations like delegation or custody changes) call into MAS at the point of action.
- **STGF (this repo)** — where a raw ERC capability gets evaluated: is it a module, an extension, or a standard, and once classified, does the module reuse-and-unify it, enforce what it only described, or originate the capability from nothing.

