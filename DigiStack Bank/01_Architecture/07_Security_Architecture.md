ID: ARCHVIZ07
Version: 0.1 (scaffold)
Status: Not Populated

Title: Security Architecture

Imports:
STD (§Security)
RACI01 (Security Team role)

Populate At: P01 v10 (roles/registry, Customer/Administrator roles,
file registry), extended v11/v12 (SSL/mTLS hops), extended P02 v17
(LDAP/LTPA, MFA/OTP, CSRF/XSS, API key enforcement on REST/SOAP
endpoints), extended P03 v23 (auth boundary shifts — Internet Banking
authenticates users, CBS performs business-service authorization)

---

## What this will show
Customer/Administrator roles (v10), file/LDAP registry (v10),
SSL hops browser→IHS→AppServer→DB (v11/v12), which hop carries mTLS (v12),
LTPA token propagation across cluster members (v17),
MFA/OTP enforcement at login (P02 v17),
CSRF/XSS protection (P02 v17),
API key enforcement on REST/SOAP endpoints (P02 v17),
auth boundary post-CBS-split: Internet Banking authenticates users (login/
MFA/LTPA), CBS performs authorization for business services (P03 v23),
SSL certificate provisioning per new subdomain (P03 v26/v27/v28, per CI01 §5.1).

## Status
Not yet populated. Security features don't exist until v10-v12.