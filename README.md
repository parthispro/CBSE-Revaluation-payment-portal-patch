# CBSE-Revaluation-payment-portal-patch
# Client-Side Logic Error and Initialization Loop in Payment Gateway

- **Target / Product:** CBSE Re-evaluation Payment Portal
- **Severity:** Medium (Availability Impact)
- **Vulnerability Class:** Client-Side Logic Error / Unhandled Exception (CWE-754)
- **Discovered By:** Parth Gambhir - 2026 CBSE-Student
- **Date Reported:** 2026-05-19
- **Status:** Resolved / Disclosed following Responsible Disclosure Guidelines but no reply to the report and mail

---

## 1.Summary

The CBSE re-evaluation application portal suffers from a critical frontend logic error within its fee payment interface. A flawed initialization loop and variable conflict in the client-side risk telemetry script causes the primary checkout function to misfire. This architectural flaw results in a browser-side crash during the transaction phase, effectively preventing students from completing their re-evaluation requests.
this loose endpoint also was used in further 'hackings'(not confirmed) which led to payments in other accounts **Including my 400/- INR still not refunded due to date** 

---

## 2. Vulnerability Details

- **Affected Endpoint(s):** CBSE Frontend Checkout / Payment Initialization Script
- **Authentication Required:** Authenticated Student Account
- **Attack Vector:** Client-Side / Browser

### Root Cause / Reason

The vulnerability stems from an initialization loop within the client-side JavaScript responsible for scanning the Document Object Model (DOM). A global variable conflict overrides the core integration pathway. Specifically, a delayed validation check incorrectly compared the current DOM snapshot metrics against a function reference rather than the intended snapshot object. This forced an execution loop to misfire, crashing the browser's transaction initialization phase before the payment window could render.

---

## 3. Proof of Concept (PoC) & Steps to Reproduce

> **Note:** Keep all tokens, personal identifiable information (PII), and sensitive identifiers redacted. The fixed file is attached in repo.

1. Authenticate into the CBSE portal using a standard student account during the re-evaluation application cycle.
2. Proceed to the fee payment interface to initialize a checkout transaction.
3. Observe the client-side execution loop utilizing the vulnerable risk-scanning script.
4. Wait approximately 5 seconds for the `setTimeout` function to execute its DOM rescan.
5. Notice that the script attempts to compare `currentSnapshot.sc.length` against an invalid reference, causing a browser-side crash and preventing the checkout modal from functioning.

---

## 4. Impact

- **Confidentiality:** None 
- **Integrity:** None 
- **Availability:** High (Prevents legitimate users from initializing payments, causing a localized Denial of Service for the re-evaluation process)

---

## 5. Remediation & Fix Recommendations

To ensure platform stability, implement the following technical patches:

* **Correct Variable Referencing:** Modify the `setTimeout` execution block to correctly compare `currentSnapshot` arrays against `initialSnapshot` arrays, rather than the `getApiKey` function reference. 
* **Scope Isolation:** Wrap the telemetry execution loop in an isolated scope (e.g., an IIFE or module) to prevent global variable conflicts from overriding the core payment checkout pathway.
* **Failsafe Execution:** Implement `try...catch` blocks around non-critical telemetry and DOM-scanning functions. If the risk scan fails, the exception should be caught silently, allowing the primary checkout window to initialize without crashing.
* **Sent an unformatted mail:** Sent a mail regarding same on 19 of May 2026
---

## 6. Coordinated Disclosure Timeline

- **2026-05-18:** Frontend vulnerability identified and browser crash reproduced during the 2026 re-evaluation cycle.
- **2026-05-19:** Did cause analysis; Noticed the conflicting variable in the client-side JavaScript.
- **2026-05-19-9:39AM:** Developed functional code patch and submitted a structured bug report via email to the CBSE IT Directorate.
- **2026-05-25~26:** Technical patch applied to ensure platform stability for students.
