# HVM3 - Work In Progress

The **HVM** is an efficient implementation of the [Interaction Calculus](https://github.com/VictorTaelin/Interaction-Calculus) (IC).

The Interaction Calculus is a new foundation for computing, similar to the Lambda Calculus, but theoretically optimal. The HVM is an efficient implementation of this new paradigm, and can be seen as a fast engine for symbolic computations.

In some ways, it is very similar to Haskell, but it has some key differences:
- Lambdas must be linear or affine, making it resource-aware
- Lambdas have no scope boundaries, enabling global substitutions
- First-class duplications allow a term to be copied into two locations
- First-class superpositions allow two terms to be stored in one location

These primitives allow HVM to natively represent concepts that are not present in the traditional λ-Calculus, including continuations, linear HOAS interpreters, and mutable references. Moreover, superpositions and duplications allow it to perform optimal beta-reduction, allowing some expressions to be evaluated with an exponential speedup. Finally, being fully affine makes its garbage collector very efficient, and greatly simplifies parallelism.

The HVM3 is the successor to HVM1 and HVM2, combining their strengths. It aims to be the main compile target of [Bend](https://github.com/HigherOrderCO/Bend). It is a WIP under active development.

## Specifications

- [IC.md](./IC.md): Interaction Calculus, the theoretical foundation behind HVM
- [HVM.md](./HVM.md): the HVM language, which extends IC with pragmatic primitives

## Install

1. Install Cabal.
2. Clone this repository.
3. Run `cabal install`.

## Usage

```bash
hvm run file.hvml    # runs interpreted (slow)
hvm run file.hvml -c # runs compiled (fast)
```

Note: the `-c` flag will also generate a standalone `.main.c` file, which if you want, you can compile and run it independently. See examples in the [book/](book/) directory.

---

# Project IC-Prove — Fork Vision

## Preamble: The Vision

This document outlines the foundational principles, architectural design, and implementation strategy for Project IC-Prove: an automated theorem prover built from the first principles of Informational Constructivism (IC). Our goal is to demonstrate that mathematical proof search can be framed as a resource-constrained, shortest-path problem within an operational framework defined by IC, using the HVM3 Interaction Calculus engine as its computational substrate.

The core thesis is that **"simplicity" in proof** (reviving Hilbert's 24th problem) can be quantified as the **minimal computational cost** (Δ-cost, measured in HVM3 interactions) to construct a target theorem-record from foundational distinctions.

## Target Audience

This document is intended for the development team tasked with building IC-Prove, comprising software engineers and mathematically-inclined individuals, as well as stakeholders interested in the theoretical underpinnings and practical realization of this project.

---

## Part I: Conceptual Foundations of IC (Selected Highlights for the Prover)

### 1. Distinction and Construction

- **S₀: Types of Distinction** — The base alphabet of irreducible distinctions.
- **Δ_gen:** Quantity arises via repetition of a Type Uᵢ → 𝑛.
- **Δ_compare / Δ_factor:** Comparison/factorization of counts gives rise to primes.
- **ISG (Integer Structure Grid):** Coordinates (x = max exponent, y = sum of exponents) derived from prime factorization structure.

### 2. Observer and Constraint

- Observer O applies Δ operations under constraint vector C = (cₛ, c_d, cₜ):
  - cₛ: Storage (number of records/formulas)
  - c_d: Max recursion depth
  - cₜ: Total time or compute steps
- Memory Graph Gₒ: DAG of derivations formed by Δ operations.

### 3. Proof Search in IC Terms

- Proof = path in Gₒ from axioms to target theorem-record.
- Δ-cost = number of HVM3 interactions during this derivation.
- Provability is relative to C: weaker C ⇒ smaller provable set.

---

## Part II: HVM3 Interaction Calculus Engine (Overview)

### 1. Runtime Core

- **ICalc primitives**: Lam, App, Dup, Sup, Var, etc. (affine).
- **HVM3 extensions**: CTR (constructors), pattern matching, unboxed numbers, REF (global refs), etc.
- **Term Representation**: Word64-encoded nodes in heap.

### 2. Key Operations

- `reduceAtC`: Executes Δ-steps via HVM3 runtime.
- `getItr`: Returns interaction count = Δ-cost.
- `Sup`, `DUP`, and `Collapse.hs`: Enable multiverse-style superposition evaluation and auto-pruning of invalid paths.

---

## Part III: IC-Prove Architecture and Implementation Plan

### Core Design

- States: HVM3 terms (logical formulas)
- Transitions: Δ operations = HVM3 function applications
- Cost: Measured in HVM3 `itrs`

### Components

#### A. HVM3 Logic Library

- `logic_syntax.hvm`: Encodes Formula and Prop data types.
- `prover_rules.hvm`: Contains rule functions (e.g., Modus Ponens).
- `standard_axioms.hvm`: Pre-injected axioms.

#### B. Haskell Prover Engine

- `Prover.CoreSyntaxBridge.hs`: Translation between abstract and HVM3 forms.
- `Prover.Search.hs`: A* or IDA* search over HVM3 heap.
- `Prover.Proof.hs`: Encodes proofs, prints, exports.

#### C. Main Entrypoint

- `app/Main.hs` extended with `hvm3 prove --target ...` CLI interface.

---

## Part IV: Key Challenges

- **Formula Equality**: Efficient α-equivalence.
- **Unification**: Implemented with HVM3 MAT, SWI, or fallback C helpers.
- **Heuristics**: For A*, cost-to-go estimation on formulas.
- **Heap Management (cₛ)**: ERA tagging, garbage collection, pruning.
- **Cost Bridging**: Aligning IC theoretical cost with runtime `itrs`.
- **Binding & Scope**: Managing quantifiers, contexts, capture.
- **Debugging**: Instrumented introspection across Haskell ↔ C.

---

## Part V: Sprint 0

**Goal**: Prove `P → P` or `P, P→Q ⊢ Q`.

Tasks:
- Define HVM3 data types and basic rules.
- Write simple Haskell A* controller to call rule functions.
- Count itrs before/after rule application → use as edge cost.
- Track and output path from axioms to goal.

---

## Glossary (Selected Terms)

- **IC**: Informational Constructivism.
- **S₀**: Base set of irreducible distinctions.
- **Δ**: Distinction operation (Δ_gen, Δ_apply, etc.).
- **Gₒ**: Observer’s memory DAG of distinctions.
- **C = (cₛ, c_d, cₜ)**: Constraints — storage, depth, time.
- **HVM3**: Runtime for Interaction Calculus.
- **itrs**: Count of HVM3 interaction steps (Δ-cost).
- **ERA**: Erasure/failure node in HVM3.
- **SUP/DUP**: Superposition / duplication primitives.
- **REF**: Reference to defined function.
- **Book**: Set of all defined functions/types.

