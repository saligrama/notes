# Using zk-SNARKs for Privacy on the Blockchain

## What is a zk-SNARK?

* SNARK: a succinct proof that a certain statement is true
    - e.g. statement: "I know an *m* such that $SHA256(m) = 0$
    - Note: SNARK means that the proof is short and fast to verify (if *m* is 1GB then using the message *m* as a proof is not a SNARK)
* Blockchain applications:
    - Private Tx on a public blockchain: Tornadocash, Zcash, Ironfish, Aleo
    - Compliance: proving that private Tx are in compliance with banking laws; proving solvency in zero-knowledge
    - Bridging between blockchains: zkBridge

## Review: arithmetic circuit

* Fix finite field $\mathrm{F} = \{0, ..., p-1\}$ for some prime $p > 2$
* Arithmetic circuit $C : \mathrm{F}^n \rightarrow \mathrm{F}$
    - Directed acyclic graph (DAG) where internal nodes labeled $+$, $-$, $\times$
    - Inputs labeled $1, x_1, \ldots, x_n$
    - Defines $n$-variate polynomial with an evaluation recipe
    - $|C|$: number of inner gates in $C$
* Interesting arithmetic circuits
    - $C_\text{hash}(h, m)$: outputs 0 if $SHA256(m) = h$, and nonzero otherwise
    - $C_\text{hash}(h, m) = (h - SHA256(m))$, $|C_\text{hash}| \approx $ 20k gates
    - $C_\text{sig}(pk, m, \sigma)$: outputs 0 if $\sigma$ is a valid ECDSA signature on $m$ with respect to $pk$, hundreds of thousands of gates

## NARK: Non-interactive ARgument of Knowledge

* Public arithmetic circuit: $C(x, w) \rightarrow F$
    - Where $x$ public statement in $F^n$, $w$ secret witness in $F^m$
* Preprocessing (setup): $S(C) \rightarrow$ public parameters $(pp, vp)$ for prover, verifier
* Prover: $P(pp, x, w) \rightarrow $ proof $\pi$
* Verifier: $V(vp, x, \pi) \rightarrow $ accept or reject

### NARK requirements

* Complete: if both $P$, $V$ honest, then $V$ should accept proof $\pi$
* Adaptively knowledge sound: $V$ accepts a proof: $P$ "knows" $w$ such that $C(x, w) = 0$ (an extractor $E$ can extract a valid $w$ from $P$)
* Optional: Zero knowledge: $(C, pp, vp, x, \pi)$ "reveal nothing" about $w$

## SNARK: a succinct NARK

Setup: similar, except

* $P(pp, w, x)$ generates a *short* proof $\pi$ such that $|\pi| = O_{\lambda}(\log(|C|))$
* $V(vp, \pi, x)$ is fast to verify such that $\text{time}(V) = O_{\lambda}(|x|, \log(|C|))$

For zero-knowledge, defining knowledge soundness and zero-knowledge: see CS255 notes

### Types of preprocessing setup

* $r$ random bits
* Trusted setup per circuit: $S(C; r)$ random $r$ must be kept secret from prover
    - Prover learns $r$: can provide false statements
    - Therefore: destroy machine after using this once
* Trusted but universal (updatable) setup: secret $r$ is independent of $C$
* Transparent setup: $S(C)$ does not use secret data (no trusted setup)

### Recent progress on proof systems

* Groth (2016): proof size ~ 200 bytes, verifier time ~ 1.5ms, trusted per circuit setup
* Plonk and Marlin (2019): proof size ~ 400 bytes, verifier time ~ 3ms, universal trusted setup
* Bulletproofs (Bunz et al. (2017)): proof size ~ 1.5 kb (non-constant), verifier time ~ 3 sec, transparent setup
* STARK (Ben-Sasson et al. (2018)): proof size ~ 100 kb (non-constant), verifier time ~ 10 ms, transparent setup, post-quantum enabled
* And more!