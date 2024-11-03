# Shamir Secret Sharing: Where Threshold Signature Begins

Threshold Signing is a multi-party signature scheme that allows a set of participants to collaboratively create a signature based on a secret distributed through all the parties. Not all participants need to take direct part in the signing process - only a sufficient number of them, meeting or exceeding a defined threshold, is required. This paper describes the basics of sharing secrets among signature participants in a way that supports threshold signature creation.

## Introduction

[Shamir's Secret Sharing (SSS)](https://en.wikipedia.org/wiki/Shamir%27s_secret_sharing) is essential in threshold signing as it enables a signing key to be divided into multiple "shares" and distributed among participants. In this model, only a predetermined subset, or threshold number, of shares is necessary to reconstruct the signing key and produce a valid signature. By leveraging finite field arithmetic, SSS ensures that partial knowledge of the signing key cannot be exploited. This guarantees that no single participant can generate a signature independently, maintaining both security and distributed control over signing authority.

## Mathematical Foundation

Shamir's Secret Sharing relies on [the polynomial interpolation](https://en.wikipedia.org/wiki/Polynomial_interpolation). In this approach, the secret to be split into shares is represented as an integer modulo $m$, with each share also an element of this field.
The steps to distribute a secret s among $n$ participants with a threshold $k$ are as follows:

1. Define a Polynomial

$$
f(i)=a_0​+a_1​i+a_2i^2+\ldots+a_{k−1}​i^{k−1}\text{,}
$$

where $a_0=s$ (the secret) and $a_i$​ values are selected at random.

2. Evaluate $f(i)$ at $n$ distinct, non-zero points $i_1, i_2, \ldots, i_n$​ to generate the shares $f(1), f(2), \ldots, f(n)$.

3. Provide each participant with a unique pair $(i, f(i))$.

To reconstruct the secret, at least $k$ participants (corresponding to the polynomial's degree) must collaborate. Using the Lagrange interpolation formula, the secret can be computed as:

$$
s=f(0)=\sum_{i=1}^k f(i)\cdot L_i(0)\text{,}
$$

where $L_i(x)$ - Lagrange coefficients for the share $(i, f(i))$

$$
L_i​(0) = \prod_{\substack{j=1 \\ j\neq i}}^k\frac{i}{i-j}
$$

The initial secret $s$ can thus be derived from any combination of $k$ participants' shares.

## [Fieldman Verifiable Secret Sharing (VSS)](https://en.wikipedia.org/wiki/Verifiable_secret_sharing)

The pure Shamir secret sharing has a number of pitfalls and a number of enhancements exist.

* To make the signing process distributed all the participants create their own secret and polynomial, then distribute it to other participants independently. The shares received by a single party from every other participant are then combined on each side resulting in combined key shares.

* To protect against a chosen key attack every participant should [commit](https://en.wikipedia.org/wiki/Commitment_scheme) to its share before starting the next communication round. The chosen key attack is the scheme where a malicious participant selects his key after observing the shares from other participants. This way the adversary can for example select his secret to be symmetric to others such that its sum results in some predicted value.

## Schnorr Signature

[The Schnorr signature](https://medium.com/coinmonks/the-bitcoin-schnorr-signature-in-a-nutshell-c0c28cc79179) uses two kinds of keys combined in every signature: the so-called long-living key and the ephemeral key. In simple words, two independent VSS sessions are required to create a shared signature.
The linear nature of the Schnorr signature equation allows to simplify secret shares usage in the way where the shares itself are used as independent private keys to generate signature shares. The signature shares then can be summed by anybody (even by non-participants) to construct the final signature. The final signature cannot be distinguished from any other Schnorr signature, created by many participants or a single one.
[The Flexible Round-Optimized Schnorr Threshold Signatures (FROST)](https://eprint.iacr.org/2020/852.pdf) protocol is a good example and reference.
