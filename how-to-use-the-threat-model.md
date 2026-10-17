# How to Fill Out and Use Your Threat Model

Follow these instructions alongside `threat-model-template.md`. Complete the report in order, updating earlier sections when you discover new information.

The report contains five sections:

1. System Overview.
2. Data Flow Diagrams and Components Table.
3. Trust Boundaries.
4. Assets.
5. STRIDE Threat Analysis.

Use the structure below without a separate security objectives section or assumptions and open questions section. Put clarification notes beside the item they concern.

## Before Filling Out the Report

1. **Identify the application and review scope.** Establish which system and environment you are examining.
2. **Collect available sources.** Gather architecture documents, existing diagrams, relevant source code, configuration, and Infrastructure as Code (IaC).
3. **Identify a system contact.** Find someone who can explain the design and validate your understanding.
4. **Record report details.** Fill in the application name, author, date, version, system owner, and source references.
5. **Choose one main workflow to trace first.** Begin with a normal operation and follow it from its initiating action to its final result. Repeat for the other important workflows.

Use documentation to understand intended behavior, code to inspect implemented behavior, and IaC to inspect infrastructure definitions and permissions. A repository definition alone does not establish what is currently deployed.

Attach evidence to significant claims using document headings, file paths, functions, configuration entries, or other precise references. Mark information as **Documented**, **Implemented**, **Inferred**, or **Not verified** when needed. Explain conflicting evidence beside the affected item.

## 1. System Overview

**Purpose of this section:** Explain what the application does, who interacts with it, and what the review covers.

### 1.1 Purpose

**What to do:**

1. Identify the problem the application solves.
2. State who benefits from or depends on it.
3. Identify its main inputs, operations, and outputs.
4. Summarize its purpose in a short paragraph.

**What to record:** A plain-language description of the application's function. Describe the service it provides before naming the technologies used to provide it.

**Complete when:** A reader can explain what the application does without needing to inspect its code or diagrams.

### 1.2 Users and Other Callers

**What to do:**

1. Identify people and systems that interact with the application.
2. Separate different roles where their access or capabilities differ.
3. Include administrative, operational, and service-to-service interactions when relevant.
4. Record how each actor interacts with the application and what actions it is intended to perform.

**What to record:** The actor or role, its purpose, and its intended capabilities. Keep intended permissions separate from permissions you have verified.

**Complete when:** You know who or what initiates the application's important operations and which roles have different access.

### 1.3 Main Workflows

**What to do:**

1. Identify the application's important operations.
2. For each operation, find its initiating actor or event.
3. Trace the information entering the application.
4. Follow the components that receive, process, store, or forward it.
5. Identify where caller identity is established and access decisions are made.
6. Follow the result to its recipient or final storage location.
7. Include important background and administrative operations.

**What to record:** A numbered sequence for each workflow. State who acts, which component handles each step, what information moves, and what result is produced. Note important alternate paths and failure behavior when they affect security.

If implementation is available, start at the entry point and follow downstream calls. Check shared middleware, gateways, libraries, and resource policies when an access check is not visible in the main handler.

**Complete when:** You can explain an operation from beginning to end and identify any steps that remain unverified.

### 1.4 Scope

**What to do:**

1. List the workflows, components, environments, and integrations included in the review.
2. Identify what is excluded.
3. Explain why each significant exclusion is outside scope.
4. Keep relevant external dependencies visible even when you are not reviewing their internal implementation.

**What to record:** Clear included and excluded scope, including any limits on available evidence or access.

**Complete when:** Another reviewer can tell exactly what this report does and does not cover.

## 2. Data Flow Diagrams and Components Table

**Purpose of this section:** Show how information moves through the application and explain the responsibilities of its parts.

Build diagrams from the workflows and evidence you collected. Do not add components or connections simply because they would be typical for this kind of application.

### 2.1 Context / Level 0 DFD

For this report, Level 0 means a context diagram showing the application as one process and its external interactions. Numbering conventions vary; use this definition consistently.

**What to do:**

1. Draw the application as a single process.
2. Place external users, calling systems, and dependencies around it.
3. Add arrows showing major information entering and leaving the application.
4. Label each arrow with the data or operation it carries.
5. Show direction clearly. Separate incoming and outgoing information when necessary for clarity.
6. Add a short explanation beneath the diagram.

**What to record:** The diagram and a brief explanation of its main interactions. Keep internal processing and storage details for Level 1.

**Complete when:** A reader can identify the application's external participants and principal inputs and outputs at a glance.

### 2.2 Level 1 DFD

**What to do:**

1. Expand the application into its major internal processes and data stores.
2. Keep the relevant external participants from Level 0.
3. Connect the components using the workflows you traced.
4. Label each arrow with the information moving across it.
5. Include significant responses, background processing, and external calls.
6. Give components and flows stable identifiers: `C-01` for components and `F-01` for flows.
7. Use distinct shapes for processes, data stores, and external entities, with a compact legend.
8. Add focused supporting diagrams if a single diagram becomes difficult to read.
9. Explain the main paths below the diagram using the same identifiers.

**What to record:** The detailed diagram and a concise walkthrough. Include protocols only when supported by evidence. Use workflow steps to explain order and conditions; a DFD alone does not establish execution sequence.

**Complete when:** Each important workflow can be followed through the diagram, and every arrow has an understandable source, destination, and purpose.

### 2.3 Components Table

Create one row for each meaningful component represented in the diagrams, including relevant data stores and external dependencies.

| Column | How to fill it in |
| --- | --- |
| ID | Use the component's identifier from the diagram. |
| Component | Give its name and type. |
| What it does | Explain its specific responsibility in this application. |
| Inputs → outputs | Identify what it receives, what it does with that information, and what it produces or forwards. |
| Identity / permissions | Identify who or what it runs as, which resources it can access, and which actions it may perform. Cite supporting evidence. |

**What to do:**

1. Read each component's implementation or supporting documentation.
2. Explain its responsibility in your own words.
3. Check its callers and downstream dependencies.
4. Inspect relevant identities, permission grants, and resource policies.
5. Distinguish permissions granted from actions used by the workflow.
6. Mark details you cannot verify directly in the affected row.

Check both the component's permission to access a resource and its decision about whether the requesting user may perform the operation. Establishing one does not establish the other.

**Complete when:** Every component has a clear responsibility, its inputs and outputs match the arrows, and its access is supported by evidence or visibly unverified.

## 3. Trust Boundaries

**Purpose of this section:** Identify where information crosses a meaningful change in trust, control, identity, or privilege.

### 3.1 DFD with Trust Boundaries

**What to do:**

1. Reuse the Level 1 DFD, preserving its components, flows, and identifiers.
2. Examine each interaction and identify who controls each side.
3. Determine whether the interaction changes trust, identity, privilege, ownership, or administrative control.
4. Identify what the receiver must verify before accepting the information or performing the requested action.
5. Mark justified boundaries with labeled outlines or lines.
6. Assign boundary identifiers using `TB-01`, `TB-02`, and so on.
7. Explain logical boundaries in text when they cannot be represented clearly as physical groupings.

Do not automatically place a boundary between every pair of components. A boundary needs an explanation of what changes. Components inside the same boundary still require appropriate access checks.

**What to record:** An annotated Level 1 DFD with clearly identified boundaries and a brief explanation where the visual grouping alone is insufficient.

**Complete when:** Each marked boundary corresponds to an explainable change in trust or control.

### 3.2 Trust Boundary Table

| Column | How to fill it in |
| --- | --- |
| ID | Use the boundary identifier from the annotated diagram. |
| Boundary / zones separated | Name the two sides and identify who controls them. |
| Flows crossing it | List the flow IDs crossing that boundary in either direction. |
| What changes in trust or control? | Explain the identity, privilege, ownership, or trust difference. |
| Required controls / verified evidence | State what needs to be checked and separately record which checks are evidenced, with references. |

**What to do:**

1. Create a row for every boundary on the diagram.
2. Identify the data and identities crossing it.
3. Locate where the caller's identity is established.
4. Locate where allowed actions and resource access are checked.
5. Determine how incoming information is validated before use.
6. Record control evidence or mark it not verified.

Authentication establishes identity; authorization determines permitted actions. Check both where relevant. Network connectivity alone does not establish either.

**Complete when:** You can explain what crosses each boundary, why that crossing matters, and how the receiving side decides what to accept.

## 4. Assets

**Purpose of this section:** Identify what needs protection and connect it to the architecture.

### Find the Assets

**What to do:**

1. Review the information entering and leaving the application.
2. Inspect what each data store contains.
3. Identify sensitive information processed or transmitted between components.
4. Identify credentials, identities, permissions, configuration, and important outputs.
5. Consider application capabilities and availability when their compromise would cause harm.
6. Determine where each asset is stored, processed, or transmitted.
7. Identify who should be able to access or modify it.

### Fill Out the Assets Table

| Column | How to fill it in |
| --- | --- |
| ID | Assign a stable asset identifier using `A-01`, `A-02`, and so on. |
| Asset | Name the valuable information, resource, or capability. |
| What it is / why it matters | Explain its purpose, sensitivity, or importance. |
| Where it lives or flows | Reference the relevant component and flow IDs. |
| Security requirements | State who may access or modify it and what must remain confidential, accurate, available, or separated. |

Distinguish valuable information from the infrastructure that handles it. Include infrastructure itself as an asset when its integrity or availability needs separate consideration.

Record requirements here without creating a separate security objectives section. Do not treat a requirement as evidence that its protection has been implemented.

**Complete when:** Every important asset has a clear value, location, and access or protection requirement.

## 5. STRIDE Threat Analysis

**Purpose of this section:** Use the architecture, boundaries, and assets to identify specific ways the system could be harmed.

If an agent prepared the earlier sections, verify its claims and diagrams first. Leave the threat register blank until you begin this analysis.

### 5.1 Choose a Workflow to Analyze

**What to do:**

1. Select a workflow you can follow from beginning to end.
2. Identify its components, flows, boundaries, and assets.
3. Identify what an attacker could control at each point.
4. Determine what access or conditions the attacker would need.
5. Apply the STRIDE questions below to that path.
6. Repeat for the other important workflows and shared components.

Start with the paths you understand well. Refine the architecture whenever threat analysis reveals a missing component, flow, or access decision.

### 5.2 Apply STRIDE

| Category | Questions to ask | What to inspect |
| --- | --- | --- |
| Spoofing | Can a caller pretend to be another user or service? | Identity validation, credentials, tokens, sessions, and identity propagation. |
| Tampering | Can someone alter information or behavior without permission? | Input handling, writes, messages, configuration changes, and integrity protections. |
| Repudiation | Could an important action occur without reliable evidence of who performed it? | Attribution, audit coverage, record integrity, and access to logs. |
| Information disclosure | Could information reach an unauthorized person or system? | Read access, responses, errors, logs, storage policies, and data destinations. |
| Denial of service | Could a caller exhaust resources or prevent legitimate use? | Input limits, request rates, expensive operations, queues, retries, quotas, and dependencies. |
| Elevation of privilege | Could a caller gain actions or permissions beyond its intended access? | Role checks, administrative operations, service permissions, and separation between users or tenants. |

Consider each category where applicable. Do not force a threat into every category. Categories may overlap; the scenario and its impact matter more than finding a single perfect label.

### 5.3 Write a Specific Threat Scenario

For each candidate threat, identify:

1. **Actor:** Who could cause the harm?
2. **Prerequisites:** What access or conditions would they need?
3. **Action:** What would they do or manipulate?
4. **Path:** Which component, flow, or boundary would they use?
5. **Condition:** What control would need to be absent, bypassed, or ineffective?
6. **Impact:** Which asset would be affected, and how?

Use this structure:

> [Actor] could [action] through [component or flow] if [condition], causing [impact to an asset].

Avoid recording only a category name or a general statement that something might be insecure.

### 5.4 Investigate Existing Controls

**What to do:**

1. Identify what would prevent or limit the scenario.
2. Locate the relevant code, configuration, design decision, or operational control.
3. Check whether it covers the affected workflow and alternate entry points.
4. Record the evidence and its limits.
5. Validate uncertain behavior with the system owner or authorized testing where appropriate.

Treat a candidate threat as something to assess. Do not label it a confirmed vulnerability solely because you have not found evidence of a control. Record what remains unverified beside the threat.

### 5.5 Fill Out the Threat Register

| Column | How to fill it in |
| --- | --- |
| ID | Assign a stable threat identifier using `T-01`, `T-02`, and so on. |
| Component / flow / boundary / asset | Reference the relevant diagram and asset IDs. |
| STRIDE category | Select the applicable category or categories. |
| Threat scenario and impact | Describe the actor, action, prerequisites, path, necessary condition, and harm. |
| Existing controls and evidence | Record the protections you inspected and whether their coverage is verified. |
| Proposed mitigation | Describe what should change or what must be investigated before choosing a change. |
| Priority / status | Apply your team's risk criteria and state the assessment or resolution status. |

Assess priority using likelihood and impact, including attacker access, prerequisites, asset sensitivity, affected scope, and existing protections. If essential information is missing, mark priority pending rather than inventing a rating.

For actionable items, include an owner or tracking reference and identify how resolution will be verified. A proposed mitigation is not a completed fix.

**Complete when:** Each recorded threat has a concrete path to harm, an assessment of existing controls, and a clear status or next step.

## Final Review and Ongoing Use

1. Walk through the report with someone who builds or operates the system.
2. Confirm that the overview, diagrams, and tables describe the same architecture.
3. Check that component, flow, boundary, and asset IDs match throughout.
4. Confirm that important workflows and alternate paths are represented.
5. Review unsupported claims, conflicting evidence, and unverified controls.
6. Confirm that threats are specific and actionable items have ownership and follow-up.
7. Record the reviewed architecture version or date.
8. Update the report when significant data flows, integrations, identities, permissions, or workflows change.

Check completion boxes only after performing the corresponding review. A generated report or completed table alone does not establish that the system has been validated.

## References

- [OWASP: Threat Modeling Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)
- [Microsoft: Threats and STRIDE](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats)
- [Microsoft: Create a threat model using data-flow diagram elements](https://learn.microsoft.com/en-us/training/modules/tm-create-a-threat-model-using-foundational-data-flow-diagram-elements/)
