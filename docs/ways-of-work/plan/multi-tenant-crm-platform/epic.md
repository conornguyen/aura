---
post_title: Multi-Tenant CRM Platform - Epic Product Requirements Document
author1: Product Team
post_slug: multi-tenant-crm-platform-epic-prd
microsoft_alias: product-team
featured_image: ""
categories:
  - Product Requirements
  - CRM Platform
  - Epic
tags:
  - multi-tenant
  - crm
  - saas
  - postgresql
  - rls
  - epic
ai_note: false
summary:
  Epic PRD for the foundational Multi-Tenant CRM Platform, detailing functional and
  non-functional requirements, user journeys, and success metrics for the MVP phase.
post_date: 2026-01-22
---

## Epic Overview

**Epic Name:** Multi-Tenant CRM Platform Foundation

**Scope:** Phase 1 MVP - Core platform infrastructure + CRM module

**Alignment:** Business Requirements Document v1.0 (Approved)

**Primary Stakeholders:** Product Team, Engineering, Security

---

## Goal

### Problem

Organizations struggle with finding affordable, customizable CRM solutions that don't sacrifice data security. Existing platforms are either prohibitively expensive or built without proper database-level tenant isolation, creating cross-tenant data leakage risks. Additionally, custom-built CRMs often lack the architectural foundation to scale into multiple business modules without major refactoring.

### Solution

Build a secure, modular Multi-Tenant CRM Platform using Next.js and Supabase that:

- Prioritizes database-enforced tenant isolation via PostgreSQL Row Level Security (RLS)
- Provides a CRM-first module with core entities (organizations, contacts, deals)
- Establishes a shared core platform layer (tenants, users, roles, auth)
- Enables future module expansion (HCM, testing systems, internal tools) without architectural re-engineering

### Impact

**Expected Outcomes:**

- Enable SMBs (10–300 employees) to adopt a CRM solution at 40–60% cost reduction vs. enterprise platforms
- Achieve zero cross-tenant data leakage incidents through enforced database-level security
- Reduce time-to-onboard new tenants from days to minutes via automated provisioning
- Establish a reusable platform foundation for launching 3+ additional business modules within 12–18 months

---

## User Personas

### Primary User: Sales Manager / Sales Lead

**Demographics:** 25–45 years old, uses CRM 4–8 hours/day, manages 5–20 sales reps

**Goals:**

- Centralize customer and opportunity tracking
- Monitor sales pipeline and deal progress
- Assign leads/deals to team members
- Run basic reporting on pipeline health

**Pain Points:**

- Current systems are slow or expensive
- Limited customization for their sales process
- Poor mobile/web experience

**Behaviors:**

- Logs in daily
- Creates and updates deals/opportunities
- Manages team member access
- Runs weekly pipeline reviews

---

### Secondary User: Sales Representative

**Demographics:** 22–50 years old, uses CRM 3–6 hours/day

**Goals:**

- Log interactions with prospects/customers
- Track assigned deals and contact details
- Update deal progress and close status
- Access contact history quickly

**Pain Points:**

- Outdated interfaces
- Difficult data entry workflows
- Slow search/lookup functionality

**Behaviors:**

- Logs in daily to multiple sessions
- Creates new contacts and updates records
- Moves deals through pipeline stages
- Communicates deal status to managers

---

### Tertiary User: Tenant Administrator

**Demographics:** 30–55 years old, technical-minded non-engineer

**Goals:**

- Manage user accounts and permissions
- Set up organizational structure
- Configure basic access controls
- Monitor system usage and health

**Pain Points:**

- Manual user provisioning is time-consuming
- Limited role customization in existing tools
- Audit trails are unclear or absent

**Behaviors:**

- Logs in weekly or as-needed
- Provisions new users
- Manages team structure changes
- Reviews access logs occasionally

---

## High-Level User Journeys

### Journey 1: New Tenant Onboarding

1. **Signup:** Prospect signs up via marketing website
2. **Tenant Creation:** System auto-creates isolated tenant with schema isolation
3. **Admin User Provisioning:** Initial admin account created; credentials sent securely
4. **First Login:** Admin logs in and enters organization metadata (company name, logo, industry)
5. **Team Invite:** Admin invites first sales team members via email links
6. **Team Onboarding:** Team members accept invites, set passwords, access CRM
7. **Initial Data:** Admin can import or seed initial contacts/company data (optional)
8. **Go Live:** Team begins using CRM to manage pipeline

**Key Success Indicators:**

- Onboarding completes in <30 minutes
- No manual intervention required
- New users can log in and see empty but ready-to-use CRM module

---

### Journey 2: Sales Manager - Daily Workflow

1. **Login:** Manager logs in via secure auth
2. **Dashboard:** Views CRM dashboard with pipeline overview, team assignments, recent activity
3. **Deal Management:** Reviews open deals; moves deals through stages; updates probability/value
4. **Team Coordination:** Views deals assigned to team members; coaches on specific opportunities
5. **Reporting:** Runs quick pipeline snapshot for standup or management review
6. **Logout:** Session ends

**Key Success Indicators:**

- Login to usable dashboard: <10 seconds
- Common actions (move deal, update value, assign rep): <3 clicks
- Dashboard data refreshes within 2 seconds

---

### Journey 3: Sales Rep - Deal Lifecycle

1. **Login:** Rep logs in
2. **Assigned Deals:** Views list of assigned opportunities
3. **Contact Lookup:** Searches for or creates new prospect contact
4. **Deal Creation:** Creates new deal linked to prospect contact with pipeline stage
5. **Updates:** Over time, logs interactions (calls, emails, meetings) and updates deal progress
6. **Close:** Moves deal to "Won" or "Lost" with notes
7. **History Access:** Reviews past interactions/notes on closed deals for reference

**Key Success Indicators:**

- Creating a new deal: <2 minutes
- Updating deal status: <30 seconds
- Contact search returns results: <2 seconds
- Historical records fully auditable and accessible

---

### Journey 4: Tenant Admin - Access Management

1. **Login:** Admin logs in
2. **Users Section:** Views current team members and roles
3. **Add User:** Invites new sales rep via email; system auto-generates secure invite link
4. **Remove User:** Deactivates departing rep; system revokes access; data remains intact
5. **Role Assignment:** Assigns role (Admin, Sales Manager, Sales Rep) to control permissions
6. **Audit Access:** Views access logs to understand who accessed what data and when

**Key Success Indicators:**

- User invite processed immediately
- New user can log in within 5 minutes of accepting invite
- Access revocation is instantaneous
- Audit logs show all user actions with timestamps

---

## Business Requirements

### Functional Requirements

#### F1. Tenant Management

- **F1.1:** System shall support automatic creation of isolated tenants during signup
- **F1.2:** Each tenant shall have unique workspace metadata (company name, logo, industry, timezone)
- **F1.3:** Tenant admins shall be able to update workspace metadata
- **F1.4:** Tenant deletion shall hard-delete or archive all associated data (configurable retention policy)
- **F1.5:** System shall support tenant suspension (data preserved, access revoked)

#### F2. Authentication & Authorization

- **F2.1:** System shall support email/password authentication via Supabase Auth
- **F2.2:** System shall issue JWT tokens for session management
- **F2.3:** System shall support secure password reset workflows with email verification
- **F2.4:** System shall support email-based user invitations with time-limited invite links
- **F2.5:** Session timeout shall occur after 30 days of inactivity or on explicit logout

#### F3. User Management

- **F3.1:** Tenant admins shall be able to invite new users via email
- **F3.2:** System shall auto-generate secure invite links (valid for 7 days)
- **F3.3:** Invited users shall create passwords on first login
- **F3.4:** Tenant admins shall be able to deactivate (soft-delete) users
- **F3.5:** Deactivated users shall be unable to log in but their data shall remain for audit
- **F3.6:** System shall support user profile (name, email, avatar, department, role)

#### F4. Role-Based Access Control (RBAC)

- **F4.1:** System shall define three baseline roles: Admin, Manager, Sales Rep
  - **Admin:** Full access to user management, settings, reporting, CRM
  - **Manager:** Can manage team members, view all team deals, view basic reporting, full CRM access
  - **Sales Rep:** Can manage own deals and assigned deals, create/edit contacts assigned to them
- **F4.2:** Tenant admins shall be able to assign roles to users
- **F4.3:** System shall enforce role-based permissions at API and UI levels
- **F4.4:** System shall support future role customization (custom roles with granular permissions)

#### F5. Tenant-Level Data Isolation (RLS)

- **F5.1:** All queries for tenant-scoped data shall filter by tenant_id via PostgreSQL RLS policies
- **F5.2:** RLS policies shall be enforced at the database level; no reliance on application logic
- **F5.3:** All tables containing business data shall have a tenant_id column
- **F5.4:** Queries shall set the `app.current_tenant_id` context variable before execution
- **F5.5:** Database policies shall ensure users can only access data of their assigned tenant
- **F5.6:** System shall log all RLS policy violations for security auditing

#### F6. CRM - Organizations (Companies)

- **F6.1:** Users shall be able to create new organization records (company name, industry, size, location, website)
- **F6.2:** Users shall be able to view all organizations in their tenant
- **F6.3:** Users shall be able to update organization metadata
- **F6.4:** Users shall be able to soft-delete organizations (data preserved, marked inactive)
- **F6.5:** Organizations shall support relationship tracking (e.g., parent company, subsidiaries)
- **F6.6:** System shall support organization-level custom fields (extensible schema)

#### F7. CRM - Contacts

- **F7.1:** Users shall be able to create contact records (name, email, phone, title, company association)
- **F7.2:** Users shall be able to view all contacts in their tenant
- **F7.3:** Users shall be able to search contacts by name, email, phone, or company
- **F7.4:** Users shall be able to update contact information
- **F7.5:** Users shall be able to soft-delete contacts
- **F7.6:** Contacts shall support custom fields (e.g., LinkedIn profile, social handles)
- **F7.7:** System shall prevent duplicate contacts via email uniqueness constraint per tenant

#### F8. CRM - Deals / Opportunities

- **F8.1:** Users shall be able to create deals (deal name, amount, stage, close date, probability, associated contact/organization)
- **F8.2:** Users shall be able to view all deals in their tenant
- **F8.3:** Users shall be able to filter deals by stage, owner, probability, organization, or date range
- **F8.4:** Users shall be able to update deal fields (amount, stage, probability, close date, notes)
- **F8.5:** Users shall be able to soft-delete deals
- **F8.6:** Users shall be able to assign deals to other team members (managers and admins only)
- **F8.7:** Deals shall support a standard pipeline (Prospect → Qualified → Proposal → Negotiation → Won/Lost)
- **F8.8:** Deal stage changes shall be logged with timestamp and user info for audit trail
- **F8.9:** System shall support deal custom fields and custom pipeline stages (future enhancement)

#### F9. CRM - User Assignment & Ownership

- **F9.1:** Contacts and deals shall have an assigned_to field (user_id)
- **F9.2:** Users shall see all records assigned to them
- **F9.3:** Managers and admins shall see all records in their tenant regardless of assignment
- **F9.4:** Sales reps shall see only records assigned to them (unless escalated to manager)
- **F9.5:** Reassigning records shall be audited and logged

#### F10. CRM - Activity Tracking

- **F10.1:** System shall support activity log entries (call, email, meeting, note) linked to contacts
- **F10.2:** Activities shall capture: type, description, date/time, participant (user), contact
- **F10.3:** Users shall view activity history for any contact
- **F10.4:** Activities shall be immutable after creation (no edit, only soft-delete or archive)

#### F11. Data Integrity & Audit

- **F11.1:** All records shall have created_at and updated_at timestamps
- **F11.2:** All records shall have created_by and updated_by user references
- **F11.3:** System shall maintain an audit log table for sensitive operations (user creation, role changes, data deletions, RLS violations)
- **F11.4:** Audit logs shall be immutable and retained for minimum 12 months
- **F11.5:** Tenant admins shall be able to view audit logs for their tenant

#### F12. Notifications (Future Phase)

- **F12.1:** System shall support in-app notification preferences
- **F12.2:** System shall notify users of relevant events (deal reassignment, contact activity, team invitations)

### Non-Functional Requirements

#### Performance

- **NFR-P1:** API response time for list queries (deals, contacts, organizations) shall not exceed 2 seconds for up to 10,000 records per tenant
- **NFR-P2:** Search queries (contacts by name, deals by organization) shall return results within 1 second
- **NFR-P3:** Dashboard and overview page shall load within 3 seconds on a standard 4G connection
- **NFR-P4:** Database indexes shall be maintained on all frequently-queried fields (tenant_id, assigned_to, stage, created_at)
- **NFR-P5:** Query pagination shall default to 25 records per page; max 100 per page to prevent memory exhaustion

#### Security

- **NFR-S1:** All authentication shall occur via industry-standard OAuth/JWT (Supabase Auth)
- **NFR-S2:** All passwords shall be salted and hashed; plaintext passwords never stored
- **NFR-S3:** All inter-tenant communication shall be blocked at the database level via RLS
- **NFR-S4:** API endpoints shall validate tenant membership before processing requests
- **NFR-S5:** All API communication shall use HTTPS/TLS 1.2 or higher
- **NFR-S6:** Tenant admins shall have no visibility into other tenants' data under any circumstance
- **NFR-S7:** System shall enforce API rate limiting (100 requests per minute per user)
- **NFR-S8:** Sensitive operations (user deletion, data export) shall require email verification or OTP
- **NFR-S9:** PII (personally identifiable information) shall not be logged except in audit trails

#### Scalability

- **NFR-Sc1:** Platform shall support up to 1,000 concurrent users per tenant
- **NFR-Sc2:** Database schema shall support 100+ tenants with shared infrastructure without performance degradation
- **NFR-Sc3:** Application shall be horizontally scalable (stateless API servers behind load balancer)
- **NFR-Sc4:** System shall support addition of new modules without modifying core platform tables

#### Data Privacy & Compliance

- **NFR-D1:** System shall comply with GDPR data retention and deletion requirements
- **NFR-D2:** Tenants shall be able to export their data in standard formats (JSON, CSV)
- **NFR-D3:** Tenants shall be able to request full data deletion (account termination)
- **NFR-D4:** All data processing shall be documented in a Data Processing Addendum
- **NFR-D5:** PII encryption at rest shall be supported (tenant-configurable)

#### Reliability & Uptime

- **NFR-R1:** Platform shall achieve 99.5% uptime SLA (monitored monthly)
- **NFR-R2:** All database operations shall be backed by automated daily backups (retained 30 days)
- **NFR-R3:** Disaster recovery plan shall enable restoration within 4 hours of failure
- **NFR-R4:** System shall gracefully handle database connection failures with user-friendly error messages

#### Accessibility & Usability

- **NFR-U1:** UI shall comply with WCAG 2.1 Level AA standards
- **NFR-U2:** All forms shall have clear error messaging and validation feedback
- **NFR-U3:** Mobile responsiveness shall work on screens as small as 320px width
- **NFR-U4:** Navigation and workflows shall be intuitive for non-technical users

#### Maintainability

- **NFR-M1:** All code shall follow Next.js and TypeScript best practices
- **NFR-M2:** API endpoints shall be documented via OpenAPI/Swagger
- **NFR-M3:** Database schema shall be version-controlled and documented
- **NFR-M4:** RLS policies shall be clearly commented and auditable
- **NFR-M5:** Logs shall be structured and machine-readable for monitoring/alerting

---

## Success Metrics

### Functional Completeness

| Metric                           | Target                        | Measurement              |
| -------------------------------- | ----------------------------- | ------------------------ |
| MVP feature launch               | 100% of F1–F11 requirements   | Feature checklist audit  |
| Zero cross-tenant data incidents | 0/month                       | Security audit + testing |
| API endpoint coverage            | ≥95% unit test coverage       | Test report              |
| RLS policy enforcement           | 100% of tenant-scoped queries | Automated policy tests   |

### User Experience

| Metric                    | Target                 | Measurement                 |
| ------------------------- | ---------------------- | --------------------------- |
| Tenant onboarding time    | <30 minutes end-to-end | Timing data from beta users |
| Login-to-CRM time         | <10 seconds            | Performance monitoring      |
| CRUD operation time (avg) | <3 clicks, <2 minutes  | UX testing                  |
| Mobile usability score    | ≥80/100                | Lighthouse / manual testing |

### Performance

| Metric                           | Target                   | Measurement            |
| -------------------------------- | ------------------------ | ---------------------- |
| API response time (list queries) | <2 seconds (10k records) | APM / monitoring       |
| Dashboard load time              | <3 seconds on 4G         | Synthetic monitoring   |
| Search latency                   | <1 second                | Query performance logs |
| 99th percentile API latency      | <5 seconds               | APM dashboard          |

### Security & Reliability

| Metric                           | Target  | Measurement                |
| -------------------------------- | ------- | -------------------------- |
| Uptime                           | ≥99.5%  | Monitoring alerts          |
| Security vulnerability incidents | 0       | Incident logs              |
| Backup success rate              | 100%    | Automated backup logs      |
| Mean time to recovery (MTTR)     | <1 hour | Incident response tracking |

### Adoption & Business

| Metric                      | Target                   | Measurement         |
| --------------------------- | ------------------------ | ------------------- |
| Beta tenant acquisition     | 10 tenants (Phase 1 end) | Signup tracking     |
| Monthly active users (beta) | ≥50                      | Analytics dashboard |
| Net promoter score (NPS)    | ≥40                      | User survey         |
| Feature request volume      | Track trends             | Feedback analysis   |

---

## Out of Scope (Phase 1)

The following items are explicitly excluded from this epic and will be addressed in future phases:

- **Email Integration:** Email sync, native email client, email templates
- **Advanced Reporting:** Custom report builder, advanced analytics, forecasting
- **Marketing Automation:** Campaigns, nurture sequences, lead scoring
- **Workflow Automation:** Custom workflows, integrations with third-party tools, webhooks
- **Mobile Native Apps:** iOS and Android native applications (web responsive UI is in-scope)
- **Marketplace & Integrations:** App store, third-party extensions, ecosystem partnerships
- **HCM Module:** Human resources, payroll, talent management
- **Testing/Collaboration Modules:** Testing systems, document collaboration, project management
- **Advanced Customization:** Custom fields UI builder, custom object types (will be added as simple feature, not full builder)
- **Multi-Language Support:** Currently English-only; internationalization deferred
- **Advanced Analytics:** Predictive analytics, AI-driven insights, forecasting
- **SSO / SAML:** Enterprise single sign-on (Phase 2+)

---

## Business Value

### Assessment: **HIGH**

### Justification

1. **Market Opportunity:** SMB CRM market is fragmented with few affordable, secure alternatives. Estimated TAM of $500M+ (SMB CRM segment). Early entry positions Aura as a growing alternative to expensive incumbents.

2. **Technical Competitive Advantage:** Database-enforced RLS differentiates Aura from competitors relying on application-layer security. This is a trust and security differentiator that resonates with security-conscious buyers and compliance-sensitive industries.

3. **Platform Foundation:** This epic establishes reusable architecture for future modules (HCM, testing, internal tools). Every dollar invested in core platform infrastructure multiplies across future products, reducing time-to-market for modules 2–5.

4. **Revenue Generation:** CRM module generates immediate recurring revenue (SaaS subscription model). Conservative estimate: 50 paying tenants in Year 1 at $99–299/month = $59K–179K ARR.

5. **Risk Mitigation:** Proven, secure multi-tenant foundation reduces risk of future pivots or rewrites. Upfront investment prevents costly architectural debt.

6. **Talent & Partnership:** Successful, secure SaaS platform attracts developers, investors, and strategic partners. Establishes credibility for enterprise sales in future phases.

### ROI Estimate

- **Development Cost (rough):** 6–8 months × 3 FTE engineers ≈ $300K–400K
- **Year 1 Revenue (conservative):** $60K–180K
- **Break-even:** 24–36 months post-launch
- **Long-term Value:** Platform extends to 5+ modules, each generating $100K–500K+ ARR; total potential $1M–3M ARR by Year 4

---

## Epic Dependencies & Milestones

### Phase 1 Milestones

| Milestone             | Duration      | Key Deliverables                               |
| --------------------- | ------------- | ---------------------------------------------- |
| Architecture & Design | 2 weeks       | Technical arch spec, DB schema, API design     |
| Core Platform Dev     | 4 weeks       | Auth, tenants, users, RBAC, RLS policies       |
| CRM Module Dev        | 3 weeks       | Orgs, contacts, deals, basic CRUD              |
| Testing & QA          | 2 weeks       | Unit, integration, security, performance tests |
| Beta Launch Prep      | 1 week        | Monitoring setup, documentation, onboarding    |
| **Total**             | **~12 weeks** | Ready for beta user testing                    |

### External Dependencies

- Supabase infrastructure provisioning & RLS policy support
- Next.js framework stability
- Security audit resource availability
- Beta user availability for feedback

---

## Open Questions & Assumptions

### Assumptions

1. Each user belongs to exactly one tenant (no multi-tenancy at user level in MVP)
2. Supabase is the system of record for authentication
3. Initial team size per tenant: <100 users (auto-scaling after MVP)
4. CRM will be the only module at launch

### Open Questions for Stakeholder Alignment

1. **Pricing Model:** Flat fee per tenant, per-user seat pricing, or usage-based? → Impacts feature flagging for Phase 1
2. **Data Export:** Should Phase 1 support automated data export for GDPR/compliance? → Impacts timeline
3. **Custom Fields:** Should Phase 1 support admin-defined custom fields on contacts/deals? → Impacts schema design
4. **Mobile App:** Is a native iOS/Android app planned within 12 months? → Informs API stability requirements
5. **Integrations:** Should Phase 1 reserve API hooks for third-party integrations (webhooks)? → Impacts API design

---

## Approval & Next Steps

**This Epic PRD is ready for:**

1. ✅ Stakeholder review and sign-off
2. ✅ Translation into a Technical Architecture Specification (arch.md)
3. ✅ Story breakdown and sprint planning
4. ✅ Engineering estimation and capacity planning

**Next Deliverable:** Technical Architecture Specification (TAS) detailing:

- Database schema and RLS policies
- API endpoint specifications
- Authentication & authorization flows
- Deployment & infrastructure strategy
- Security & compliance implementation details
