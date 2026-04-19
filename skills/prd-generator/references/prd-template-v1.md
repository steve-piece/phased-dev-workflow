# Project Name

Version: x.x  
Date: YYYY-MM-DD  
Owner: Name  
Status: Draft | Active

---

## 0. Defaults & Conventions

- Unless explicitly overridden in this document, use the **Phased Default Project Setup** for:
  - Tech stack and third party services
  - Monorepo structure, coding standards, and architecture rules
  - Database conventions, migrations, and RLS
  - Testing requirements, CI/CD guardrails, and UX/accessibility rules
- Use **Turborepo monorepo** with Next.js apps and shared packages as the default architecture.[file:17]  
- Use **Linear** for issue tracking and phased plan execution, and **GitHub** for version control using feature branches and PRs as the default workflow.[file:17]  

Include a short note if this project intentionally diverges from any default (what and why).

---

## 1. Product Overview & Strategy

### 1.1 Background & Problem

Brief context, current state, and the core problem or opportunity this product addresses.

### 1.2 Objectives & Success Metrics

- Business objectives for this release.
- User objectives.
- 3 to 5 measurable KPIs or target outcomes.

### 1.3 Target Users & Personas

- Primary personas and their main jobs to be done.  
- Secondary personas if relevant.  

### 1.4 Non Goals

Explicit list of use cases or segments that are intentionally out of scope for this release.

---

## 2. Scope, Use Cases & Requirements

### 2.1 In Scope Features

List capabilities grouped by theme or module.  
For each, include a one line description of the user value.

### 2.2 Out of Scope Items

Deferred ideas and features, with a brief rationale.

### 2.3 User Journeys & Scenarios

Narrative flows for the main personas.  
Keep this high level, one block per journey.

### 2.4 Functional Requirements

Bullet behavior and rules for each journey, including key edge cases.  
Focus on what must happen, not implementation details.

### 2.5 Non Functional Requirements

Performance, availability, security, privacy, accessibility, and localization requirements with concrete targets where possible.

---

## 3. Experience & UX Specification

### 3.1 UX Principles & Guardrails

- Core UX principles this product must follow.  
- Reference the shared UX and accessibility rules checklists unless overridden.[file:6]  

### 3.2 Information Architecture & Navigation

- High level sitemap or navigation model.  
- How users move between the main sections or apps.

### 3.3 Key Screens & States

List the core screens per journey, and note required states for each:
- Empty
- Loading or skeleton
- Populated
- Error

### 3.4 Content & Copy Requirements

Tone, key terms, important system messages, and any legal or compliance language that must appear.

---

## 4. Tech Stack, Architecture & Data

### 4.1 Frontend

- If not specified otherwise, use:
  - Next.js App Router, React, TypeScript strict mode, Tailwind CSS, Lucide icons.[file:17]  
  - Monorepo structure and component rules from the shared architecture and component checklists.[file:13][file:12]  
- Note any deviations, such as different framework, styling system, or rendering model.

### 4.2 Backend & APIs

- Default is Supabase PostgreSQL with RLS for all data, plus server actions and limited API routes as defined in the database, actions, and API route checklists.[file:11][file:15][file:14]  
- Describe any additional services or internal APIs.

### 4.3 Data Model

- List core entities and relationships.  
- Call out any special retention, migration, or auditing requirements.

### 4.4 Infrastructure & Environments

- Hosting (default Vercel for apps, Supabase for database and auth).[file:17]  
- Environments: local, preview, staging, production.  
- Any environment specific constraints.

### 4.5 Security, Compliance & Privacy

- Auth model, roles, and access control assumptions.  
- Data protection, logging, audit trail needs before launch.

---

## 5. Integrations, Dependencies & Analytics

### 5.1 External Integrations

For each integration, specify:
- Purpose and where it is used in the product.
- Default provider unless overridden:
  - Email: Resend with React Email components.[file:18]  
  - SMS and OTP: Twilio where messaging is required.[file:17]  
  - Payments and payouts: Stripe (including Connect for marketplaces if needed).[file:18]  
  - Database and auth: Supabase.
  - AI: Vercel AI SDK and Vercel AI Gateway for LLM features.[file:17]  
- Any known failure handling or rate limit constraints.

### 5.2 Internal Dependencies

Other internal systems, services, or teams this product depends on, plus assumptions about their delivery timelines.

### 5.3 Telemetry, Analytics & Experimentation

- Events to track.  
- Dashboards or funnels that must exist.  
- Feature flag and rollout strategy.

---

## 6. Delivery Plan, Domains & Governance

### 6.1 Release Scope

Short description of what is considered the first shippable version versus later phases.  
Tie this back to the objectives and success metrics.

### 6.2 Milestones & Phases

Describe your phases at a high level.  
Examples: Phase 1 Foundations, Phase 2 Core Experience, Phase 3 Admin, Phase 4 Marketing.

For detailed phase planning, fill in section 6.5.

### 6.3 Domains, DNS & Secrets

- Production domain(s) and any subdomains.  
- DNS ownership and registrar.  
- Required environment variables and secrets for production, mapping back to the default env structure unless overridden.[file:17]  

### 6.4 Risks, Assumptions & Open Questions

- Top risks, severity, and mitigation ideas.  
- Critical assumptions that must hold true.  
- Open questions that need decisions before build or launch.

### 6.5 Phased Implementation Plan

Define phases in a repeatable format.  
For each phase, use a table plus a gate.

Example layout:

#### Phase N: Name

| Feature / Work Item | Priority (P0/P1/P2) | Dependencies | Notes |
| ------------------- | ------------------- | ------------ | ----- |
|                     |                     |              |       |

Gate criteria for this phase:  
Short paragraph describing what must be true before moving on, similar to your CageList gates that couple end to end flows with core quality checks.[file:18][file:20]  

### 6.6 Linear Implementation Mapping

Describe how this PRD maps into Linear for execution:

- Linear workspace and team.  
- Project or label naming for each phase.  
- Rules of thumb for creating issues from the tables in 6.5 (one row per issue or sub issues, labels, estimates, and owners).  

### 6.7 Stakeholders & Ownership

List the key stakeholders and who owns decisions for:

- Product and scope.  
- Design and UX.  
- Engineering, QA, and release.  
- Client sign off.

