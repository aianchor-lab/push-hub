# Commercial License Terms Outline

> **Status:** Draft
> **Last updated:** 2026-10-30
>
> This document outlines the key terms to include in the push-hub commercial license agreement. It is not a contract. The actual agreement must be drafted or reviewed by legal counsel before the first sale. Pricing is out of scope and tracked in the [PRD Open Questions](PRD.md#5-open-questions).

## 1. Background

While no legal entity exists, the Licensor is an individual (Atsushi Inoue) with unlimited personal liability. The commercial license agreement is the main tool for limiting that liability. This outline is designed so that the liability exposure stays bounded and predictable until the project is incorporated.

## 2. Liability Exposure Overview

| Who may claim | Source of liability | Can a contractual cap apply? | Mitigation |
|---|---|---|---|
| Commercial license customers | Contract | **Yes** (B2B caps are generally enforceable) | Liability cap, exclusion of indirect damages, no SLA (Section 3) |
| Free users under the BSL | License / tort | No (the BSL text cannot be modified) | BSL "AS IS" warranty disclaimer; risk is low because use is free and self-hosted |
| Third parties (e.g. a customer's end users) | Tort | **No** (not bound by the contract) | Liability insurance (Section 4) |
| Third-party IP claims against customers | Indemnity, if offered | Yes, if indemnity is limited | No indemnity by default, or indemnity within the cap (Section 3.6) |

## 3. Key Terms

### 3.1 Parties and Assignment

- The Licensor is Atsushi Inoue (individual) until incorporation.
- The Licensor may assign the agreement to a successor entity (incorporation, merger, acquisition) **without the customer's consent**.

### 3.2 License Grant

- Production use of push-hub by the customer and its Affiliates, within the scope stated in the order form (e.g. environments, delivery volume).
- Term: annual subscription with renewal.
- **Not included:** offering push-hub to third parties as a hosted or managed service (requires a separate OEM / service provider agreement).

### 3.3 Support

- Best-effort support only. **No SLA**, no guaranteed response or resolution times.
- Security fixes follow [SECURITY.md](../SECURITY.md).

### 3.4 Customer Responsibilities

push-hub is self-hosted, so the customer is responsible for:

- Deployment, operation, monitoring, and backups
- Security configuration of its infrastructure, including network exposure and access control
- APNs / FCM credentials and device tokens
- Notification content, end-user data, and compliance with applicable laws and regulations

### 3.5 Warranty Disclaimer

- The software is provided "AS IS", consistent with the BSL.
- No warranty of fitness for a particular purpose, uninterrupted operation, or delivery of every notification.

### 3.6 Limitation of Liability

- **Cap:** the Licensor's total liability is limited to the license fees actually paid by the customer in the **12 months** preceding the event giving rise to the claim.
- **Exclusions:** indirect, incidental, special, or consequential damages, lost profits, lost revenue, and loss or corruption of data.
- **IP indemnity:** not offered by default. If a customer requires it:
  - It must stay within the cap above.
  - The Licensor may choose to modify the software, replace it, or terminate the license and refund prepaid fees.
- **Known limit:** under Japanese case law, a cap may not be enforced against **intentional misconduct or gross negligence** (e.g. Tokyo District Court, January 23, 2014). Security practices, such as following SECURITY.md and fixing known vulnerabilities, therefore remain important even with a cap.

### 3.7 Term and Termination

- Either party may terminate for material breach that is not cured within 30 days of notice.
- On termination, production use must stop, or continue only under the BSL terms where applicable.

### 3.8 Governing Law and Jurisdiction

- Laws of Japan; the Tokyo District Court has exclusive jurisdiction as the court of first instance (consistent with the [CLA](../CLA.md)).

## 4. Pre-Sales Checklist

Complete the following before the first commercial sale:

- [ ] Have the commercial license agreement drafted or reviewed by legal counsel
- [ ] Prefer individually signed agreements (order form + terms) over click-through terms. Under Article 548-2 of the Civil Code (standard terms), unreasonable clauses in click-through terms may be invalid.
- [ ] Obtain liability insurance that sole proprietors can buy (IT professional liability / cyber insurance)
- [ ] Keep records of all signed agreements, which are needed later for incorporation or acquisition

## 5. Incorporation Triggers

Sell as an individual (with the cap and insurance above) only for small deals. Incorporate (e.g. as a 合同会社 / LLC) **before** signing if any of the following applies:

- The customer is in a regulated sector (healthcare, finance, government)
- The customer requires an SLA, uncapped liability, or uncapped IP indemnity
- The annual contract value exceeds a threshold (TBD)
- Cumulative commercial revenue makes incorporation reasonable for tax purposes (to be confirmed with a tax accountant)

See [PRD §3 Future procedures](PRD.md#3-business-model--licensing) for the steps required at incorporation.
