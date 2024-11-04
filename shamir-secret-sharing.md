# Shamir Secret Sharing: Where Threshold Signature Begins

Threshold Signing is a multi-party signature scheme that allows a set of participants to collaboratively create a signature based on a secret distributed through all the parties. Not all participants need to take direct part in the signing process - only a sufficient number of them, meeting or exceeding a defined threshold, is required. This paper describes the basics of sharing secrets among signature participants in a way that supports threshold signature creation.

## Introduction

[Shamir's Secret Sharing (SSS)](https://en.wikipedia.org/wiki/Shamir%27s_secret_sharing) is essential in threshold signing as it enables a signing key to be divided into multiple "shares" and distributed among participants. In this model, only a predetermined subset, or threshold number, of shares is necessary to reconstruct the signing key and produce a valid signature. By leveraging finite field arithmetic, SSS ensures that partial knowledge of the signing key cannot be exploited. This guarantees that no single participant can generate a signature independently, maintaining both security and distributed control over signing authority.

## Math

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

## Further Reading

1. [Fieldman Verifiable Secret Sharing (VSS)](https://en.wikipedia.org/wiki/Verifiable_secret_sharing)

2. [Pedersen Distributed Key Ggeneration (DKG)](https://link.springer.com/content/pdf/10.1007/3-540-46766-1_9.pdf)

3. [DKG by Flexible Round-Optimized Schnorr Threshold Signatures (FROST)](https://eprint.iacr.org/2020/852.pdf)

