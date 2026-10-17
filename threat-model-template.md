# Threat Model — [Application Name]

| Field | Details |
| --- | --- |
| Author | |
| Date | |
| Version | |
| System owner / team | |
| Architecture / source references | |

> Work through this report in order: what the system does → how it works → where trust changes → what needs protection → what could go wrong. Write explanations in your own words. Place clarification notes beside the relevant component, flow, or boundary.

## 1. System Overview

### Purpose — What are we building?

[Explain what the application does, the problem it solves, and its intended outcome.]

### Users — Who uses it?

[Describe end users, administrators, and services that interact with the application. Explain what each can do.]

### Main workflows — How will users use it?

[Describe the main user journeys in plain language, from the initiating action to the result. Include administrative or background workflows when relevant.]

1. [User or service initiates an action.]
2. [Application processes the request.]
3. [Application stores or retrieves information.]
4. [User or service receives the result.]

### Scope

**Included:** [Components, workflows, environments, and integrations covered by this review.]

**Excluded:** [Items outside this review and the reason for excluding them.]


## 2. Data Flow Diagrams

### 2.1 Context / Level 0 DFD

**Purpose:** Provide a simple, quick overview of the application and its external interactions.

For this report, Level 0 means the application shown as one process, surrounded by users and external systems. DFD numbering conventions vary; use this definition consistently.

**Include:**

- The application as a single process.
- External users, services, and systems.
- Major data exchanges, with labeled arrows showing direction.

[Insert Level 0 DFD here.]

**Explanation:** [Explain where information comes from, what the application does with it at a high level, and where it goes.]

### 2.2 Level 1 DFD

**Purpose:** Show how the application's major internal components work together.

**Include:**

- Major processes, such as APIs, services, and workers.
- Data stores, such as databases, object storage, and queues.
- Relevant external users, systems, and dependencies.
- Labeled, directional data flows between components.

[Insert Level 1 DFD here.]

**Explanation:** [Walk through a complete request from entry point to result. Explain which components handle it and what information moves between them.]

**Diagram conventions:**

- Label arrows with what moves, such as an access token, document upload, or query result.
- Represent processes, data stores, and external entities distinctly; provide a legend if needed.
- Use consistent identifiers: C-01 for components, F-01 for flows, TB-01 for boundaries, and A-01 for assets.
- Add a deeper diagram when a component needs further explanation instead of overcrowding Level 1.

### 2.3 Components Table

Explain what each component actually does. Include relevant external dependencies and data stores.

| ID | Component | What it does | Inputs → outputs | Identity / permissions |
| --- | --- | --- | --- | --- |
| C-01 | | | | |
| C-02 | | | | |
| C-03 | | | | |

**Identity / permissions:** Record the identity the component runs as, the resources it can access, and the actions it can perform. Identify where user identity or authorization context is passed between components.

## 3. Trust Boundaries

### 3.1 DFD with Trust Boundaries

Overlay labeled trust boundaries on the Level 1 DFD. Keep the components and flows consistent with Section 2.

A trust boundary marks a change in trust, control, identity, or privilege. A connection between components does not automatically create a trust boundary. Consider user-controlled clients, service identities, tenant separation, administrative access, and third-party systems.

[Insert Level 1 DFD with trust boundaries here.]

### 3.2 Trust Boundary Table

| ID | Boundary / zones separated | Flows crossing it | What changes in trust or control? | Required controls / verified evidence |
| --- | --- | --- | --- | --- |
| TB-01 | | | | |
| TB-02 | | | | |
| TB-03 | | | | |

For each boundary, explain:

- Who controls each side?
- What identity, data, or request crosses it?
- Why should the receiving side trust what arrives?
- Where are authentication, authorization, and validation enforced?

Distinguish controls that are required from controls you have verified. Add evidence references or clarification notes directly in the relevant row.

## 4. Assets

Identify what needs protection, what it is, and where it lives or flows.

A component describes part of the system; an asset describes something of value. For example, a database is a component, while the sensitive records inside it are assets. A component can also be an asset when its integrity or availability matters.

Consider user data, credentials, tokens, configuration, permissions, important outputs, and service availability where relevant.

| ID | Asset | What it is / why it matters | Where it lives or flows | Security requirements |
| --- | --- | --- | --- | --- |
| A-01 | | | | |
| A-02 | | | | |
| A-03 | | | | |

**Security requirements:** Describe what must be protected, such as confidentiality, integrity, availability, tenant isolation, or restricted administrative access. Be specific about who should have access and what harm would result from compromise.

## 5. STRIDE Threat Analysis

### 5.1 Review Prompts

Use the diagrams, components, boundaries, and assets above to identify concrete threat scenarios. Examine components and flows, paying particular attention to trust boundary crossings. Consider each STRIDE category where applicable; do not force a threat into every category.

| Category | Meaning | Questions to ask |
| --- | --- | --- |
| S — Spoofing | Impersonating an identity | Can someone impersonate a user or service? Can credentials or tokens be stolen, forged, or reused? |
| T — Tampering | Unauthorized modification | Can someone alter requests, stored data, messages, code, or configuration? |
| R — Repudiation | Disputing an action without reliable evidence | Can an important action happen without reliable attribution or audit evidence? Can that evidence be altered? |
| I — Information disclosure | Unauthorized access to information | Can data, credentials, or internal details reach an unauthorized user, tenant, or service? |
| D — Denial of service | Disrupting legitimate use | Can someone exhaust compute, storage, connections, quotas, or downstream capacity? |
| E — Elevation of privilege | Gaining unauthorized permissions | Can a user or service perform actions beyond its intended permissions or cross a tenant or administrative boundary? |

Write each threat as a specific scenario:

> An attacker can [action] through [component or flow], causing [impact to an asset or security objective].

Record existing controls with evidence. A threat scenario is not automatically a confirmed vulnerability; indicate whether its controls and exploitability have been assessed.

### 5.2 Threat Register

Leave this table blank until you complete your system walkthrough and begin STRIDE analysis. Add rows as needed. Use diagram and asset IDs to make threats traceable.

| ID | Component / flow / boundary / asset | STRIDE category | Threat scenario and impact | Existing controls and evidence | Proposed mitigation | Priority / status |
| --- | --- | --- | --- | --- | --- | --- |
| | | | | | | |

Assign priority based on the scenario's likelihood and impact using your team's risk criteria. Use status to track assessment and resolution; include an owner or tracking link in the row when needed.

### 5.3 Completion Check

- [ ] I can explain what the system does and how users interact with it.
- [ ] I can walk through a complete request and explain every component it touches.
- [ ] I can identify the identities and permissions used along that path.
- [ ] I can explain where trust changes and what controls are required there.
- [ ] I can identify the important assets and where they live or flow.
- [ ] I have considered STRIDE across relevant components and flows.
- [ ] Recorded threats describe concrete scenarios and impacts.
- [ ] Existing controls have evidence, and proposed mitigations have a next step.

## References

- [OWASP Threat Modeling Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)
- [Microsoft: Threats and the STRIDE model](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats)
- [Microsoft: Create a threat model using data-flow diagram elements](https://learn.microsoft.com/en-us/training/modules/tm-create-a-threat-model-using-foundational-data-flow-diagram-elements/)
