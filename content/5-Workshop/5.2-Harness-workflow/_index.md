---
title: "Harness Workflow & Standard Docs"
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

### 1. Overview of the Harness Model in TrustBite
The **TrustBite** project is built using a standardized operating and collaboration model called **Harness**.

**Harness** is a set of specification rules and infrastructure sitting between developers (Humans), AI Agents, and the product application code. The goal of the Harness is to automate process friction, document key architectural choices (Decision Records), and guarantee that every feature developed includes visible, testable verification proof (Validation Proof).

Every completed task produces two outputs:
1.  **Product Delta:** Application source code, test files, API endpoints, database schemas.
2.  **Harness Delta:** Operational decision records, validation test reports, backlog items, or policy changes that make future tasks easier.

---

### 2. Risk Classification Process (Feature Intake & Risk Lanes)
The project applies a risk classification process defined in `docs/FEATURE_INTAKE.md` to determine the level of planning, coding, and testing required:

| Risk Lane | Definition | Planning & Testing Standards |
| --- | --- | --- |
| **Tiny** | Minor UI fixes, minor performance improvements, or code comments. | No complex planning or extensive documentation needed. Quick local verification. |
| **Normal** | Regular product features (e.g., Restaurant searching, review pagination). | Requires creating a **Story Packet** and defining clear acceptance criteria. Unit tests are mandatory. |
| **High-Risk** | Database schema alterations, security/authentication (Cognito), data retention, or third-party integrations. | Requires writing an **Architectural Decision Record (ADR)** before starting. Integration/E2E tests covering at least 90% of risk cases are mandatory. |

---

### 3. Local Operational Database (Durable Layer & Harness CLI)
To avoid manual tracking or scattered markdown files, the project uses a local SQLite database (`harness.db`) alongside a custom **Harness CLI** tool built in Rust.

Daily operations use the following main commands:
- **Initialize Harness environment:**
  ```bash
  npm run harness -- init
  ```
- **Apply Harness DB schema migrations:**
  ```bash
  npm run harness -- migrate
  ```
- **Query the Test Matrix and deployment status:**
  ```bash
  npm run harness -- query matrix
  ```
  *(This queries the local SQLite DB to output the implementation status of each domain area).*

---

### 4. Living Documentation Standards
All documentation in TrustBite is checked into the repository as "Living Documentation" under two main directories:

#### 4.1. The `docs/` Directory (Technical Development Specs)
*   **[ARCHITECTURE.md](file:///C:/My_Project/docs/ARCHITECTURE.md):** Defines layered architectural boundaries and dependency rules (Client UI -> API -> Service -> DB).
*   **[CONTEXT_RULES.md](file:///C:/My_Project/docs/CONTEXT_RULES.md):** Rules for context engineering. Guides AI agents on what files to read during different task phases (Intake, Planning, Implementation, Validation) to prevent token overload.
*   **`docs/decisions/`:** Stores long-term Architecture Decision Records (ADRs) in standard Markdown.
*   **`docs/stories/`:** Stores story packets detailing requirements and acceptance criteria for active milestones.

#### 4.2. The `trustbite-docs/` Directory (Product & Business Specifications)
Structured by professional disciplines:
- **`01_Product_Management/`:** Project Charter, PRD, MVP Scope to understand product goals.
- **`02_Business_Analysis/`:** Functional Spec, Business Rules, State Machines detailing system behavior.
- **`04_Software_Engineering/`:** API Specs, DB Schema, AWS Infrastructure Design.
- **`05_Security_Algorithms/`:** Anti-Fraud Specification (Detailing receipt OCR matching, GPS proximity, and fraud scoring rules).
