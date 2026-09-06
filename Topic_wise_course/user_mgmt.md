# WebSphere Application Server — User Management & Security
## Zero to Expert | WAS 8.5.5 / 9.0 (Traditional ND) | Banking Production Focused

> **Focus:** User Management ONLY — users, groups, registries, authentication,
> authorization, admin roles — everything an admin needs, nothing unrelated.
> **Environment:** BankCell01 (DMGR, Node01/Node02, PaymentCluster) throughout.
> **Rule:** One topic at a time. Pass assessment before advancing.

---

## 📋 TEACHING FORMAT (Every Topic)
1. Simple explanation → 2. Technical → 3. Diagram → 4. Banking example
5. Console steps → 6. Commands → 7. Internals → 8. Production procedure
9. Mistakes → 10. Troubleshooting → 11. Interview Qs → 12. Exercise

---

## MODULE 1 — Security Foundations *(Zero Level)*
1.  What is WebSphere administrative security?
2.  Why user management matters (banking audit/compliance context)
3.  Authentication vs Authorization (simple distinction)
4.  Realm, Principal, Credential — key terms explained
5.  User Registry — what it is and why it exists
6.  Federated Repository vs Standalone vs Local OS (overview)
7.  Enable/disable administrative security
8.  What changes when security is enabled (all IDs & passwords)

## MODULE 2 — User Registries
9.  Local OS registry — how it works, limitations
10. Standalone LDAP registry — configuration step-by-step
11. Federated Repositories — the WAS 8.5/9.0 standard
12. Supported LDAP servers (AD, TDS, OID, eDirectory)
13. LDAP bind DN & password — service account concepts
14. Base DN, user filter, group filter, member filter
15. Federated repository: adding multiple LDAPs + file repository
16. File-based repository (default) — where users are stored
17. Custom user registry ( level)
18. Realm configuration & primary realm
19. Registry failover (multiple LDAP hosts)
20. Changing registries safely in production

## MODULE 3 — Administrative (Console) Users & Roles
21. WebSphere administrative roles overview:
    Administrator, Operator, Configurator, Monitor, Deployer,
    Admin Security Manager, Auditor
22. What each role can and cannot do (comparison table)
23. Adding users/groups to admin roles
24. Users vs Groups mapping — best practice (groups-first)
25. Multiple Administrator users — avoiding single-admin risk
26. Admin Security Manager role — role delegation
27. Authorization groups for fine-grained console access
28. Admin ID & password recovery (locked out scenarios)
29. Changing the DMGR admin password — safe procedure

## MODULE 4 — Application User Management
30. Application security vs administrative security
31. Application-level roles (security roles in EAR/WAR)
32. Mapping app roles to users/groups (Run As, special subjects)
33. Special subjects: Everyone, All Authenticated
34. RunAs roles & service user IDs (banking batch jobs)
35. Java EE role-based authorization internals
36. J2EE groups vs registry groups
37. User authentication for PaymentApp end-to-end flow

## MODULE 5 — Passwords, Credentials & LTPA
38. LTPA tokens & keys — what they are, why they matter
39. LTPA key file (ltpa.jceks) — export/import between cells
40. Password encoding (security.xml), pros/cons
41. Propfile-based password management
42. Credential mapping for DataSources (J2C authentication aliases)
43. Creating/managing J2C aliases (DB & MQ service accounts)
44. Password rotation — service accounts without downtime
45. Expiring LTPA keys & SSO breakage

## MODULE 6 — SSO & Token Management
46. Single Sign-On (SSO) configuration
47. SSO domain name & cookie behavior
48. SSO across multiple cells (sharing LTPA keys)
49. Token expiry & session relationships
50. Web SSO vs application SSO
51. Trust association & proxies/SPNEGO (concept level)

## MODULE 7 — SSL & Certificate Basics *(only as needed for user auth)*
52. SSL configuration sets & key/trust stores
53. Certificates & personal/default signing certs
54. Certificate expiry monitoring
55. LDAP over SSL (secure user registry connection)
56. One node has cert problem — symptom & fix

## MODULE 8 — Troubleshooting User Management *(15 Scenarios)*
*Each: Symptoms → Causes → Investigation → Logs → Fix → Validation → Interview Answer*
57. Cannot login to Admin Console
58. " locked" / user not found in registry
59. LDAP connection refused / bind DN failed
60. Users invisible after registry change
61. Console login works but user has no buttons (Monitor vs Admin)
62. Role mapping lost after federated repository change
63. 401 / 403 errors on application
64. Users must re-login after every request (LTPA/SSO broken)
65. SSO broken between two cells
66. J2C alias wrong password — DataSource failure
67. Certificate expired — LDAP SSL failure
68. Admin password forgotten — recovery procedure
69. App role mapping differs between UAT and PROD
70. User can access console but app fails (authZ mismatch)
71. Full user-sync/audit failure before banking audit

## MODULE 9 — Banking Production Procedures
72. Enable security in a production cell (safe cutover plan)
73. Add/remove admin user — change procedure
74. LDAP migration (TDS → AD) — zero-downtime plan
75. Password rotation campaign (admin + J2C + bind DN)
76. Audit-ready access review (who has Admin role?)
77. Emergency: lockout recovery & break-glass account
78. LAB vs PRODUCTION procedure distinctions
79. Change ticket → approval → backup → change → validate → close

## MODULE 10 — wsadmin Scripting for User Management
80. Security-related AdminTask commands overview
81. MapUsersToAdminRole / MapGroupsToAdminRole
82. Create/remove users in file repository
83. Retrieve/search registry users & groups
84. Configure LDAP via wsadmin (federated repository)
85. LTPA key export/import via script
86. J2C alias create/update via script
87. Validation scripts (who has which role?)
88. Automation: quarterly access-review script

## MODULE 11 — Expert Level + Interview Capstone
89. Multi-cell user management strategy (banking)
90. DR considerations — LTPA keys & registries at DR site
91. Hardening: audit roles, remove Everyone mappings
92. Interview drills: architecture, troubleshooting walkthroughs,
    "describe a user-lockout incident" STAR answers
93. Final project: secure BankCell01 end-to-end
    (enable security → federated LDAP → roles → J2C → SSO → audit)

---

## 📅 SCHEDULE (~4 Weeks, 1.5–2 hrs/day)

| Days      | Module |
|-----------|--------|
| Days 1–3  | Module 1 — Foundations |
| Days 4–8  | Module 2 — Registries (core!) |
| Days 9–12 | Module 3 — Admin Roles |
| Days 13–16| Module 4 — App Users |
| Days 17–20| Module 5 — LTPA/Passwords |
| Days 21–23| Module 6 — SSO |
| Days 24–25| Module 7 — SSL basics |
| Days 26–30| Module 8 — Troubleshooting ⭐ |
| Days 31–33| Module 9 — Production Procedures |
| Days 34–36| Module 10 — wsadmin |
| Days 37–38| Module 11 — Expert + Capstone |

## ✅ COMPLETION RULE PER TOPIC
- [ ] Explanation understood  - [ ] Exercise done
- [ ] Interview Qs answered  - [ ] → Next topic

---
*Course by Ox Alpha — WebSphere User Management Training*
