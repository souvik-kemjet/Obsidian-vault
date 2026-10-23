

# ADR Template
One ADR = one decision = one file, saved as `docs/adr/ADR-NNN-short-title.md` in the project's Git repo. Copy the block below for every new decision.

## ADR-NNN: <Short decision title, in the active voice>

| Field | Value |
|---|---|
| Status | Proposed / Accepted / Rejected / Superseded by ADR-XXX / Deprecated |
| Impact tier | 1 (team) / 2 (project) / 3 (company or client) |
| Project | <e.g. Nagaland ILP> |
| Author | <name> |
| Reviewers | <names> |
| Approver | <name + role> |
| Date proposed | YYYY-MM-DD |
| Date decided | YYYY-MM-DD |
| Review by | YYYY-MM-DD (when to re-check this decision) |
| Related | <ADR links, ticket ids, client requirement ids> |

## 1. Context
What problem are we solving? What facts, constraints and deadlines apply?
State only facts. Mark assumptions as **Assumption:**.

## 2. Decision
We will do X. One clear paragraph. Use "we will", not "we might".

## 3. Options considered
| Option | Pros | Cons | Cost / effort |
|---|---|---|---|
| A (chosen) | | | |
| B | | | |
| C: do nothing | | | |

## 4. Consequences
- Good: ...
- Bad / trade-offs we accept: ...
- Risks and how we reduce them: ...
- Cost impact (licences, hosting, people-days): ...
- Security and data-protection impact: ...

## 5. Compliance and client constraints
Laws, government guidelines or contract clauses this touches
(e.g. DPDP Act 2023, CERT-In directions, GIGW, MeitY cloud rules).

## 6. Approval record
| Role | Name | Decision | Date | Comment |
|---|---|---|---|---|
| Author | | Proposed | | |
| Reviewer | | Approve / Changes requested | | |
| Approver | | Approved / Rejected | | |

## 7. Change log
| Date | Change | By |
|---|---|---|
| | Created | |


---


```markdown
# ADR-NNN: <Short decision title, in the active voice>

| Field | Value |
|---|---|
| Status | Proposed / Accepted / Rejected / Superseded by ADR-XXX / Deprecated |
| Impact tier | 1 (team) / 2 (project) / 3 (company or client) |
| Project | <e.g. Nagaland ILP> |
| Author | <name> |
| Reviewers | <names> |
| Approver | <name + role> |
| Date proposed | YYYY-MM-DD |
| Date decided | YYYY-MM-DD |
| Review by | YYYY-MM-DD (when to re-check this decision) |
| Related | <ADR links, ticket ids, client requirement ids> |

## 1. Context
What problem are we solving? What facts, constraints and deadlines apply?
State only facts. Mark assumptions as **Assumption:**.

## 2. Decision
We will <do X>. One clear paragraph. Use "we will", not "we might".

## 3. Options considered
| Option | Pros | Cons | Cost / effort |
|---|---|---|---|
| A (chosen) | | | |
| B | | | |
| C: do nothing | | | |

## 4. Consequences
- Good: ...
- Bad / trade-offs we accept: ...
- Risks and how we reduce them: ...
- Cost impact (licences, hosting, people-days): ...
- Security and data-protection impact: ...

## 5. Compliance and client constraints
Laws, government guidelines or contract clauses this touches
(e.g. DPDP Act 2023, CERT-In directions, GIGW, MeitY cloud rules).

## 6. Approval record
| Role | Name | Decision | Date | Comment |
|---|---|---|---|---|
| Author | | Proposed | | |
| Reviewer | | Approve / Changes requested | | |
| Approver | | Approved / Rejected | | |

## 7. Change log
| Date | Change | By |
|---|---|---|
| | Created | |
```


#### **How to fill it well**
- **Title** says the decision, not the topic: "Use PostgreSQL for the ILP database", not "Database".
- **Context** is facts only. Anything you are not sure of gets the word _Assumption_ in front of it.
- **Options** always include "do nothing" and at least one real alternative. If there was no alternative, it probably doesn't need an ADR.
- **Consequences** must list the downsides honestly. An ADR with only benefits hasn't been thought through.
- **Never edit an accepted ADR's decision.** If you change your mind, write a new ADR and set the old one to _Superseded by ADR-XXX_.



**Worked example** (your existing Nagaland ADR-003, in this format)

| Field        | Value                                                                                                     |
| ------------ | --------------------------------------------------------------------------------------------------------- |
| Title        | ADR-003: Use PostgreSQL 16 with EF Core 10 for the Nagaland ILP database                                  |
| Status       | Proposed                                                                                                  |
| Impact tier  | 3 — depends on NIC hosting, needs government confirmation                                                 |
| Context      | Fresh database, no Arunachal data migrated; Arunachal runs MySQL 8; hosting likely NIC MeghRaj            |
| Decision     | PostgreSQL 16+, Npgsql provider, reviewed idempotent migration scripts, money stored as paise in `bigint` |
| Options      | MySQL 8 (fallback), SQL Server (licence cost), Dapper only (rejected as default)                          |
| Consequences | Testcontainers tests match production; if NIC mandates another engine only the Data tier changes          |
| Approver     | Souvik (technical) + Director (because it affects a government commitment)                                |


## ADR approval procedure

Every ADR goes through the same five steps. Its impact tier decides who has to sign it.

![[Pasted image 20261019151428.png]]


**The five steps**
1. **Draft.** The author copies the template, fills it in, and opens a pull request (PR) in Git with status _Proposed_.
2. **Review.** Reviewers comment on the PR. The author answers every comment and updates the file. Disagreements are written down, not settled in a hallway.
3. **Decide.** The approver writes _Approved_ or _Rejected_ on the PR, with a reason. For tier 3, get the Director's approval by email or signed note and attach it (or link it) in the Approval record. A verbal OK is not enough.
4. **Record.** Set the status to _Accepted_ or _Rejected_, fill in the date, merge the PR. Rejected ADRs are kept too, so nobody re-argues the same idea.
5. **Communicate.** Post a one-line note to the team channel and add the ADR to the monthly report to the Director.

---

### **Suggested Shared Drive structure**
```
Kemjet (Shared Drive)
├── 00_Company        (incorporation, board minutes, delegation of authority)  → Director only
├── 01_Clients
│   └── Nagaland_ILP
│       ├── 01_Contract_and_POs
│       ├── 02_Requirements_and_CRs
│       ├── 03_Approvals         (signed ADR approvals, client sign-offs)
│       ├── 04_Status_Reports
│       ├── 05_Minutes
│       └── 06_Deliverables
├── 02_Engineering    (policies, access list, incident log, vendor/licence register)
├── 03_People         (HR files)  → Director + HR only
├── 04_Finance        → Director + accountant only
└── 99_Archive        (closed projects, superseded policies — read-only)
```

**Naming rule:** `YYYY-MM-DD_Project_DocumentType_vN.ext`, for example `2026-10-18_NagalandILP_StatusReport_v1.pdf`. Dates first means files sort themselves.