# Phase 1 — Database Schema

## Goal
Build realistic bank DB schema. Foundation for JNDI DataSource in Phase 9.

## Tomcat Instance
This phase is Oracle-only — no Tomcat instance yet.
Oracle XE listens on: localhost:1521 / SID: XE
DB User: digistack / Password: digistack123

## Tomcat Skills
- #7 Database Connectivity (raw JDBC now → JNDI later)

## Real Bank Concepts Used
| Concept | Implementation |
|---------|---------------|
| CIF Number | cust_ref = CIF-000001 |
| Account Number | acct_no = DSB-SAV-00001 |
| Transfer Types | NEFT / IMPS / RTGS / INTERNAL |
| Account States | ACTIVE / FROZEN / DORMANT / CLOSED |
| Audit Trail | AUDIT_LOG table — every action logged |

## Schema

#### Oracle User Setup

```sal
-- Connect as SYSDBA first
-- sqlplus sys/oracle123@localhost:1521/XE as sysdba

CREATE USER digistack IDENTIFIED BY digistack123;
GRANT CONNECT, RESOURCE TO digistack;
GRANT UNLIMITED TABLESPACE TO digistack;

-- Then connect as digistack to run schema below
-- sqlplus digistack/digistack123@localhost:1521/XE
```

```sql
-- CUSTOMERS
CREATE TABLE CUSTOMERS (
  cust_id        NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  cust_ref       VARCHAR2(20) UNIQUE NOT NULL,
  full_name      VARCHAR2(100) NOT NULL,
  email          VARCHAR2(100) UNIQUE,
  mobile         VARCHAR2(15),
  login          VARCHAR2(50) UNIQUE NOT NULL,
  password_hash  VARCHAR2(200) NOT NULL,
  kyc_status     VARCHAR2(20) DEFAULT 'PENDING',
  status         VARCHAR2(20) DEFAULT 'ACTIVE',
  created_at     TIMESTAMP DEFAULT SYSTIMESTAMP,
  updated_at     TIMESTAMP DEFAULT SYSTIMESTAMP
);

-- ACCOUNTS
CREATE TABLE ACCOUNTS (
  acct_no        VARCHAR2(20) PRIMARY KEY,
  cust_id        NUMBER REFERENCES CUSTOMERS(cust_id),
  type           VARCHAR2(10) NOT NULL,
  balance        NUMBER(15,2) DEFAULT 0,
  min_balance    NUMBER(15,2) DEFAULT 1000,
  status         VARCHAR2(20) DEFAULT 'ACTIVE',
  ifsc_code      VARCHAR2(11) DEFAULT 'DSBK0000001',
  opened_at      TIMESTAMP DEFAULT SYSTIMESTAMP
);

-- BENEFICIARIES
CREATE TABLE BENEFICIARIES (
  id             NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  owner_cust_id  NUMBER REFERENCES CUSTOMERS(cust_id),
  benef_acct_no  VARCHAR2(20),
  nickname       VARCHAR2(50),
  status         VARCHAR2(20) DEFAULT 'PENDING',
  activated_at   TIMESTAMP
);

-- TRANSACTIONS
CREATE TABLE TRANSACTIONS (
  txn_id         VARCHAR2(30) PRIMARY KEY,
  from_acct      VARCHAR2(20),
  to_acct        VARCHAR2(20),
  amount         NUMBER(15,2) NOT NULL,
  type           VARCHAR2(20),
  channel        VARCHAR2(20),
  status         VARCHAR2(20),
  remarks        VARCHAR2(200),
  ref_no         VARCHAR2(64),
  kafka_event_id VARCHAR2(64),
  initiated_at   TIMESTAMP DEFAULT SYSTIMESTAMP,
  completed_at   TIMESTAMP
);

-- AUDIT_LOG
CREATE TABLE AUDIT_LOG (
  log_id      NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  actor       VARCHAR2(50),
  action      VARCHAR2(100),
  entity_type VARCHAR2(50),
  entity_id   VARCHAR2(50),
  old_value   VARCHAR2(500),
  new_value   VARCHAR2(500),
  ip_address  VARCHAR2(50),
  logged_at   TIMESTAMP DEFAULT SYSTIMESTAMP
);

-- NOTIFICATIONS
CREATE TABLE NOTIFICATIONS (
  notif_id    NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  cust_id     NUMBER REFERENCES CUSTOMERS(cust_id),
  type        VARCHAR2(30),
  message     VARCHAR2(500),
  status      VARCHAR2(20) DEFAULT 'UNREAD',
  created_at  TIMESTAMP DEFAULT SYSTIMESTAMP
);
```

## Seed Data
```sql
INSERT INTO CUSTOMERS (cust_ref,full_name,email,mobile,login,password_hash,kyc_status,status)
VALUES ('CIF-000001','Arjun Sharma','arjun@digistack.in','9876543210','arjun.sharma','hashed_pass_1','VERIFIED','ACTIVE');

INSERT INTO CUSTOMERS (cust_ref,full_name,email,mobile,login,password_hash,kyc_status,status)
VALUES ('CIF-000002','Priya Mehta','priya@digistack.in','9123456780','priya.mehta','hashed_pass_2','VERIFIED','ACTIVE');

INSERT INTO ACCOUNTS VALUES ('DSB-SAV-00001',1,'SAVINGS',50000.00,1000,'ACTIVE','DSBK0000001',SYSTIMESTAMP);
INSERT INTO ACCOUNTS VALUES ('DSB-CUR-00001',1,'CURRENT',100000.00,5000,'ACTIVE','DSBK0000001',SYSTIMESTAMP);
INSERT INTO ACCOUNTS VALUES ('DSB-SAV-00002',2,'SAVINGS',30000.00,1000,'ACTIVE','DSBK0000001',SYSTIMESTAMP);
COMMIT;
```

## Verification
```sql
SELECT table_name FROM user_tables ORDER BY 1;
SELECT cust_ref, full_name, status FROM customers;
SELECT acct_no, type, balance, status FROM accounts;
```

## Expected Output
```
ACCOUNTS / AUDIT_LOG / BENEFICIARIES / CUSTOMERS / NOTIFICATIONS / TRANSACTIONS

CIF-000001 Arjun Sharma ACTIVE
CIF-000002 Priya Mehta ACTIVE

DSB-SAV-00001 SAVINGS 50000 ACTIVE
DSB-CUR-00001 CURRENT 100000 ACTIVE
DSB-SAV-00002 SAVINGS 30000 ACTIVE
```

## Phase-End Interview Questions
1. Why use `GENERATED ALWAYS AS IDENTITY` instead of a sequence in Oracle 12c+?
2. What is a CIF number in real banking — what does it represent?
3. Why is `AUDIT_LOG` a separate table and not just application logging (log4j)?
4. In DigiStack, `DSB-SAV-00001` belongs to Arjun Sharma — which column in ACCOUNTS links back to CUSTOMERS?
5. Why does the TRANSACTIONS table have both `from_acct` and `to_acct` as VARCHAR2 instead of FK to ACCOUNTS?

## Status
- [ ] Not started
- [ ] In progress
- [ ] Completed

## Notes
(paste your SQL errors or output here)