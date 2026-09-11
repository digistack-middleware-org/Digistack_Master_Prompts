# 📅 Master Day Plan — WebSphere Authentication & Authorization (User Management)
## Zero → Expert | 1 hr/day | Banking Production Focused
**Ox Alpha | WAS 8.5.5 / 9.0 Traditional ND | BankCell01 (DMGR, Node01/Node02, PaymentCluster)**

> **Total: ~60–70 days ≈ 11 weeks**
> **Order:** Foundations → ⭐ Registries (full pace) → Roles → App Users → LTPA → SSO/SSL
> → ⭐⭐ Troubleshooting (full pace) → Procedures → wsadmin → Capstone → Interview Pack
>
> **Per-topic mix:** Theory 80% → Real Banking Scenario 15% → Interview 5%
> **Every topic:** Admin Console steps + wsadmin steps
> **Environment:** BankCell01 — DMGR, Node01, Node02, PaymentCluster (UPI/cards), CustPortalCluster (internet banking)

---

## 🗓️ WEEKS 1–2 — Foundations (Compressed, Days 1–4)

| Day | Topics | Coverage |
|---|---|---|
| **1** | 1–2 | What is WAS security + why banks can't disable it; AuthN vs AuthZ (bank gate analogy) |
| **2** | 3–4 | Realm/Principal/Credential/Subject; What is a User Registry |
| **3** | 5–6 | 3 registry types comparison; Enable/Disable Global Security (console + wsadmin) |
| **4** | 7–8 | What breaks when security turns ON; **LAB: enable security in BankCell01 safely** |

---

## 🗓️ WEEKS 3–5 — ⭐ REGISTRIES (FULL PACE — Days 5–18, 14 days)

| Day | Topic | Focus |
|---|---|---|
| **5** | 9 | Local OS registry — how it works, why banks reject it |
| **6** | 10 | Standalone LDAP — concepts |
| **7** | 10 | Standalone LDAP — full console config |
| **8** | 10 | Standalone LDAP — full wsadmin config |
| **9** | 11 | Federated Repositories — why it's the standard |
| **10** | 12 | Supported LDAPs: AD, TDS, OID, eDirectory — bank usage patterns |
| **11** | 13 | Bind DN & password — the service account that can kill your cell |
| **12** | 14 | Base DN + user/group/member filters — decoded with AD example |
| **13** | 15 | Federated repo: multiple LDAPs + file repository in one realm |
| **14** | 16–17 | File repository (FileRegistry.xml); Realm config & primary realm |
| **15** | 18 | LDAP failover — multiple hosts, SSL (ldaps 636) |
| **16** | 19 | **Changing registries in production — TDS→AD zero-downtime story** |
| **17** | 20 | **LAB: Connect BankCell01 to AD, login with AD user** |
| **18** | — | 🧪 Registry assessment: config quiz + fix a broken LDAP config |

---

## 🗓️ WEEK 6 — Admin Roles (Compressed, Days 19–23)

| Day | Topics | Coverage |
|---|---|---|
| **19** | 21–22 | 7 admin roles + full capability matrix (banking staff examples) |
| **20** | 23–24 | Add users/groups to roles (console + `mapUsersToAdminRole`); groups-first practice |
| **21** | 25–27 | Multi-admin risk; Admin Security Manager; authorization groups |
| **22** | 28–29 | Password change procedure + **lockout recovery & break-glass** |
| **23** | — | LAB: build role structure for BankCell01 (WAS_Admins, WAS_Ops, WAS_Auditors) |

---

## 🗓️ WEEK 7 — Application Users (Compressed, Days 24–28)

| Day | Topics | Coverage |
|---|---|---|
| **24** | 30–31 | App vs admin security; security roles in EAR/WAR |
| **25** | 32–33 | Map app roles (console + wsadmin); Everyone vs All Authenticated |
| **26** | 34–36 | RunAs roles & service IDs; isUserInRole internals; J2EE vs registry groups |
| **27** | 37 | End-to-end: CustPortal → authN → authZ → PaymentApp |
| **28** | 38 | LAB: Secure PaymentApp with Teller/Supervisor/Auditor roles |

---

## 🗓️ WEEK 8 — LTPA & Passwords (Compressed, Days 29–33)

| Day | Topics | Coverage |
|---|---|---|
| **29** | 39–40 | LTPA tokens & keys; ltpa.jceks export/import (DR must match PROD) |
| **30** | 41 | `{xor}` password encoding — the audit trap |
| **31** | 42–43 | J2C aliases for DB2/MQ (console + wsadmin) |
| **32** | 44–45 | Zero-downtime rotation (2-alias trick); LTPA expiry outage story |
| **33** | 46 | LAB: rotate PaymentCluster DB password with zero downtime |

---

## 🗓️ WEEK 9 — SSO (Days 34–36) + SSL (Days 37–38)

| Day | Topics | Coverage |
|---|---|---|
| **34** | 47–48 | SSO config, LtpaToken2 cookie, domain name; cross-cell SSO (PROD+DR keys) |
| **35** | 49–51 | Token vs session timeout; web vs app SSO; SPNEGO/proxy concepts |
| **36** | 52 | LAB: single login across console + PaymentApp |
| **37** | 53–55 | Keystores/truststores/signers; LDAP over SSL; cert expiry monitoring script |
| **38** | 56 | Node cert problem → sync failure — symptom & fix |

---

## 🗓️ WEEKS 10–12 — ⭐⭐ TROUBLESHOOTING (FULL PACE — Days 39–53, 15 days)

*Each: Symptoms → Causes → Investigation → Logs (CWWIM/CWWIS/SECJ codes) → Fix → Validation*

| Day | Scenario | Theme |
|---|---|---|
| **39** | 57 | Cannot login to Admin Console |
| **40** | 58 | User locked / not found in registry |
| **41** | 59 | LDAP connection refused / bind DN failed (**server won't start!**) |
| **42** | 60 | Users invisible after registry change |
| **43** | 61 | Login works but buttons missing (Monitor vs Administrator) |
| **44** | 62 | Role mapping lost after federated repo change |
| **45** | 63 | 401/403 on PaymentApp |
| **46** | 64 | Re-login every request (LTPA/SSO broken) |
| **47** | 65 | SSO broken between PROD and DR |
| **48** | 66 | J2C wrong password → DataSource fails → **payments down** |
| **49** | 67 | Cert expired → LDAP SSL failure → nobody can login |
| **50** | 68 | Admin password forgotten — full recovery walkthrough |
| **51** | 69–70 | UAT vs PROD role mismatch; console works but app authZ fails |
| **52** | 71 | Pre-audit access review failure — the 2-day war story |
| **53** | — | 🧪 Troubleshooting gauntlet: 5 mock outages, you diagnose from logs |

---

## 🗓️ WEEK 12 (cont.) — Production Procedures (Days 54–56)

| Day | Topics |
|---|---|
| **54** | 72–73 | Enable security in live bank cell (cutover + rollback); add/remove admin procedure |
| **55** | 74–75 | LDAP migration plan; quarterly password rotation campaign |
| **56** | 76–78 | Audit access review + evidence pack; break-glass policy; change flow |

---

## 🗓️ WEEK 13 — wsadmin (Days 57–59) + Capstone (Day 60)

| Day | Topics |
|---|---|
| **57** | 79–81 | Security AdminTask overview; mapUsers/GroupsToAdminRole scripts; file repo users |
| **58** | 82–85 | searchUsers/searchGroups; LDAP config via wsadmin; LTPA export/import script |
| **59** | 86–87 | J2C alias scripts; quarterly access-review automation |
| **60** | 88–91 | Multi-cell strategy; DR design; hardening; **FINAL PROJECT: secure BankCell01 end-to-end** |

---

## 📊 Summary

| Block | Days | Pace |
|---|---|---|
| Foundations | 4 | ⚡ Compressed |
| ⭐ Registries | 14 | 🐢 Full — untouched |
| Admin Roles | 5 | ⚡ Compressed |
| App Users | 5 | ⚡ Compressed |
| LTPA/Passwords | 5 | ⚡ Compressed |
| SSO + SSL | 5 | ⚡ Compressed |
| ⭐⭐ Troubleshooting | 15 | 🐢 Full — untouched |
| Procedures + wsadmin + Capstone | 7 | ⚡ Compressed |
| **TOTAL** | **60 days** | *(realistic: 60–70 days)* |

---

## 🎯 PHASE 12 — INTERVIEW MASTERY PACK (after Day 60)

| # | Pack | Count |
|---|------|-------|
| 1 | Real interview Q&A (10-yr level) | 20 |
| 2 | "Recent issues you faced" answers | 5 |
| 3 | Banking scenario-based questions | 20 |
| 4 | Troubleshooting-based questions | 20 |
| 5 | Outage war stories (STAR format) | 5 |
| 6 | Behavioral/Leadership questions | 5 |
| 7 | Architecture/Design (Lead level) | 5 |

---

## ✅ Completion Rule Per Topic

- [ ] Explanation understood
- [ ] Console step done
- [ ] wsadmin step done
- [ ] Banking scenario discussed
- [ ] Interview Qs answered
- [ ] → Say `NEXT`

## 🔖 Commands

| Command | Action |
|---|---|
| `START` / `NEXT` | Move to next topic |
| `QUIZ` | Get assessed on current topic |
| `REVIEW <topic#>` | Revisit a topic |
| `EXERCISE` | Extra hands-on practice |

---
*Course by Ox Alpha — WebSphere Authentication & Authorization | Banking Production Training*
