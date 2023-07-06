# yAcademy Spartan-ecdsa Review <!-- omit in toc -->

**Review Resources:**

- [Code Repo](https://github.com/personaelabs/spartan-ecdsa)
- [Blog post](https://personaelabs.org/posts/spartan-ecdsa/)

**Auditor:**

 - Rajesh Kanna

## Table of Contents <!-- omit in toc -->

- [Review Summary](#review-summary)
- [Scope](#scope)
- [Code Evaluation Matrix](#code-evaluation-matrix)
- [Findings Explanation](#findings-explanation)
- [Critical Findings](#critical-findings)
- [High Findings](#high-findings)
- [Medium Findings](#medium-findings)
- [High Findings](#high-findings-1)
  - [1. High - X, Y pair may be an invalid point on the curve.](#1-high---x-y-pair-may-be-an-invalid-point-on-the-curve)
    - [Technical Details](#technical-details)
    - [Impact](#impact)
    - [Recommendation](#recommendation)
    - [Developer Response](#developer-response)
- [Informational Findings](#informational-findings)
- [Final remarks](#final-remarks)

## Review Summary

**Spartan-ecdsa**

Spartan-ecdsa provides efficient circuits for group membership that uses ecdsa scheme.

The circuits of the Spartan-ecdsa [Repo](https://github.com/personaelabs/spartan-ecdsa) were reviewed. The code review was performed by one auditor. The repository was under active development during the review, but the review was limited to the latest commit at the start of the review. This was commit [3386b30d9b5b62d8a60735cbeab42bfe42e80429](https://github.com/personaelabs/spartan-ecdsa/commit/3386b30d9b5b62d8a60735cbeab42bfe42e80429) for the Spartan-ecdsa repo.

## Scope

The scope of the review consisted of the following circuits at the specific commit:

- eff_ecdsa.circom
- tree.circom
- add.circom
- double.circom
- mul.circom
- poseidon.circom
- pubkey_membership.circom


This review is a code review to identify potential vulnerabilities in the code. The reviewers did not investigate security practices or operational security and assumed that privileged accounts could be trusted. The reviewers did not evaluate the security of the code relative to a standard or specification. The review may not have identified all potential attack vectors or areas of vulnerability.

yAcademy and the auditors make no warranties regarding the security of the code and do not warrant that the code is free from defects. yAcademy and the auditors do not represent nor imply to third parties that the code has been audited nor that the code is free from defects. By deploying or using the code, Spartan ecdsa and users of the contracts agree to use the code at their own risk.


Code Evaluation Matrix
---

| Category                 | Mark    | Description |
| ------------------------ | ------- | ----------- |
| Mathematics              | Good | No issues found with respect to math logic. |
| Complexity               | Good | No complex code is found. Code is pretty straightforward. |
| Libraries                | Good | No issues found with repect to libraries. |
| Code stability           | Good    | Code is pretty stable. |
| Documentation            | Good | No issues found with respect to documentation. |
| Testing and verification | Good | Every test case covered.  |

## Findings Explanation

Findings are broken down into sections by their respective impact:
 - Critical, High, Medium, Low impact
     - These are findings that range from attacks that may cause loss of funds, impact control/ownership of the contracts, or cause any unintended consequences/actions that are outside the scope of the requirements

 - Informational
     - Findings including recommendations and best practices

---

## Critical Findings

None.


## High Findings

None.

## Medium Findings

None.

## High Findings

### 1. High - X, Y pair may be an invalid point on the curve.

Circuits do not check whether the point $(x,y)$ is on the curve $E$.

#### Technical Details

The pair $\(x,y)\$ forms a group $G\$ of order $N\$ under $E(\mathbb{F}_p)/\mathcal{P}\$ where $E\$ represents an elliptic curve, $x, y < P\$ and $\mathbb{F}_p\$ denotes a finite field, and $\mathcal{P}\$ represents the prime order of the base point. But no check for whether $\(x,y)\$ in $G\$ is done. That is, the point $\(x,y)\$ is not being validated.

#### Impact

User may provide a public key (which is just a point $`(x,y)`$) that is not a valid point on the curve. This may leak the private key if the point is chosen from small order $N'$ of another curve $C'$

#### Recommendation

- Validate the given point $(x,y)$ outside of the circuit.

#### Developer Response


## Informational Findings

None.

## Final remarks

Circuits are written efficiently with good supported documentation.
