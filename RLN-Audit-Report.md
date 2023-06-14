# yAcademy Rate Limiting Nullifier Review <!-- omit in toc -->

**Review Resources:**

- [Formal spec](https://rate-limiting-nullifier.github.io/rln-docs/)
- [Code Repo](https://github.com/Rate-Limiting-Nullifier/circom-rln)

**Auditor:**

 - Rajesh Kanna

## Table of Contents <!-- omit in toc -->

- [Review Summary](#review-summary)
- [Scope](#scope)
- [Code Evaluation Matrix](#code-evaluation-matrix)
- [Findings Explanation](#findings-explanation)
- [Critical Findings](#critical-findings)
- [Low Findings](#low-findings)
- [Informational Findings](#informational-findings)
- [Final Remarks](#final-remarks)

## Review Summary

**Rate Limiting Nullifier**

Rate Limiting Nullifier provides circuits to avoid spamming in communication protocols.

 The repository was under active development during the review, but the review was limited to the latest commit at the start of the review. This was commit [37073131b9c5910228ad6bdf0fc50080e507166a](https://github.com/Rate-Limiting-Nullifier/circom-rln/commit/37073131b9c5910228ad6bdf0fc50080e507166a) for the Rate Limiting Nullifier repo.

## Scope

The scope of the review consisted of the following circuit files at the [this](https://github.com/Rate-Limiting-Nullifier/circom-rln/commit/37073131b9c5910228ad6bdf0fc50080e507166a) commit:

- rln.circom
- utils.circom
- withdraw.circom


This review is a code review to identify potential vulnerabilities in the code. The reviewers did not investigate security practices or operational security and assumed that privileged accounts could be trusted. The reviewers did not evaluate the security of the code relative to a standard or specification. The review may not have identified all potential attack vectors or areas of vulnerability.

yAcademy and the auditors make no warranties regarding the security of the code and do not warrant that the code is free from defects. yAcademy and the auditors do not represent nor imply to third parties that the code has been audited nor that the code is free from defects. By deploying or using the code, Rate Limiting Nullifier and users of the contracts agree to use the code at their own risk.


Code Evaluation Matrix
---

| Category                 | Mark    | Description |
| ------------------------ | ------- | ----------- |
| Access Control           | Good | No issues found with respect. to access control |
| Mathematics              | Good | No issues found in implementation. |
| Complexity               | Good | No complexity found with respect to circuits. |
| Libraries                | Good | No issues found in libraries. |
| Code stability           | Good    | No issues found with respect to code instability. |
| Documentation            | Average | Documentation can be improved to provide more info on how and why of each circuit. |
| Testing and verification | Good | Every test case covered.  |

## Findings Explanation

Findings are broken down into sections by their respective impact:
 - Critical, High, Medium, Low impact
     - These are findings that range from attacks that may cause loss of funds, impact control/ownership of the contracts, or cause any unintended consequences/actions that are outside the scope of the requirements

 - Informational
     - Findings including recommendations and best practices

---
# Critical Findings
## 1.Critical - Risk of secret getting revealed if the input is zero.
In rln template, if the input X is 0, it will reveal the secret of user.

### Technical Details
If the public input X is zero, it will result in revealing the user secret using the output Y.

```
template RLN() {
    ...
    signal output y;
    ...
    y <== identitySecret + a1 * x;
    ...
}
```

### Impact
The user may get his secret revealed without even violating any rules.

### Recommendation
1. Try hashing the random number instead of directly using it.
2. Have a **greater_than** constraint that checks whether the x is greater than zero (Quite inefficient compared to 1).
3. Impose **greater_than** constraint / hash the random number outside of circuit (relatively more efficient).


# Low Findings

## 1.Low - Unconstrained public input.

In withdraw template, address input is unused. 

### Technical Details
If the public input is not constrained, Circom compiler will optimize away the address.<br>

```
template Withdraw() {
    signal input identitySecret;
    signal input address;

    signal output identityCommitment <== Poseidon(1)([identitySecret]);
}
```

### Impact
No impact with respect to protocol yet it is a good practice to constrain the input.

### Recommendation
Add duplicate constraint in withdraw template.
```
template Withdraw(){
    ...
    signal duplicateConstraint <== address * address;
}
```
# Informational Findings

## 1.Field Arithmetic  

### Technical Details
In Shamir Secret Sharing computation, the coefficient of the random value X may be a multiplicative inverse so the secret maybe revealed in that case. Although, the probability of having multiplicative inverse is $\frac{1}{p}$ where $p$ is prime order.

### Impact
The user may get his secret revealed but the probability is negligible.

### Recommendation
This finding is just to address this issue. If the secret sharing is computed using higher degree polynomials, we may not have this issue. 

## Final remarks

The circuit code is not too complex and its pretty straightforward with what it is supposed to do. 
