ID: ARCHVIZ05
Version: 0.1 (scaffold)
Status: Not Populated

Title: MQ Architecture

Imports:
CAP01 (§5 MQ Sizing)
STD (§MQ)

Populate At: P02 v19

---

## What this will show
Queue Manager, channels, queue definitions, DLQ, CHLAUTH — once MQ
actually exists (P02 v19). Queue names per P02 v19 and P03 v23 design:
- BANK.PAYMENT.REQUEST.Q  — external Fund Transfer request (P02 v19)
- BANK.PAYMENT.RESPONSE.Q — external payment response back (P02 v19)
- BANK.NOTIFICATION.Q     — CBS event publish → Notification Service (P03 v23)
- BANK.AUDIT.Q            — security/audit events (P02 v17 trigger, MQ from P02 v19)
- DLQ (SYSTEM.DEAD.LETTER.QUEUE) — undeliverable messages
Channel authentication: CHLAUTH rules + SSL/TLS per P02 v19 security note.

## Status
Not yet populated. MQ isn't built until P02 v19 — P01 hasn't reached
P02 yet.