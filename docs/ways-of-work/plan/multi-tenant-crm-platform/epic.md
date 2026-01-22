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

**Epic Name:** Multi-Tenant CRM Platform Foundation with Multi-Process Sales Pipeline

**Scope:** Phase 1 MVP - Core platform infrastructure + CRM module + Multi-Process Management + Product Management + Tenant Subdomain Allocation

**Alignment:** Business Requirements Document v1.1 (Approved)

**Primary Stakeholders:** Product Team, Engineering, Security

---

## Goal

### Problem

Organizations struggle with finding affordable, customizable CRM solutions that don't sacrifice data security. Existing platforms are either prohibitively expensive or built without proper database-level tenant isolation, creating cross-tenant data leakage risks. Additionally, sales teams often need multiple distinct sales processes (e.g., enterprise vs. SMB sales, partnerships) within a single CRM, but custom-built CRMs often support only a single pipeline and lack the architectural foundation to scale into multiple business modules without major refactoring. Current platforms also lack flexible deal process management and integrated product pricing capabilities.

### Solution

Build a secure, modular Multi-Tenant CRM Platform using Next.js and Supabase that:

- Prioritizes database-enforced tenant isolation via PostgreSQL Row Level Security (RLS)
- Provides a CRM-first module with core entities (organizations, contacts, deals)
- Establishes a shared core platform layer (tenants, users, roles, auth)
- Decouples user signup from tenant creation (tenants created only on contract signing)
- Allocates unique subdomains to each tenant (e.g., `acme.aura.com`) for workspace isolation
- Maintains root domain (e.g., `aura.com`) for company marketing, landing page, and public content
- Enables multiple customizable sales processes per tenant with flexible stage management
- Ties each deal to exactly one sales process, allowing teams to use different pipelines for different deal types
- Includes product management with pricing blueprints and deal product line items
- Enables future module expansion (HCM, testing systems, internal tools) without architectural re-engineering

### Impact

**Expected Outcomes:**

- Enable SMBs (10–300 employees) to adopt a CRM solution at 40–60% cost reduction vs. enterprise platforms
- Achieve zero cross-tenant data leakage incidents through enforced database-level security
- Reduce time-to-activate new tenants (from contract to go-live) to <1 hour via automated provisioning
- Provide each tenant with branded workspace URL (`tenant-name.aura.com`) for professional appearance and easy access
- Allow flexible sales processes with multiple customizable pipelines per tenant
- Enable teams to create distinct sales processes for different deal types (enterprise, SMB, partner)
- Enable dynamic pricing through product blueprints and adjustable deal line items
- Establish a reusable platform foundation for launching 3+ additional business modules within 12–18 months

---

## User Personas

### Primary User: Sales Manager / Sales Lead

**Demographics:** 25–45 years old, uses CRM 4–8 hours/day, manages 5–20 sales reps

**Goals:**

- Centralize customer and opportunity tracking
- Monitor sales pipeline with multiple custom processes for different deal types
- Assign leads/deals to team members
- Run basic reporting on pipeline health
- Manage product catalog and pricing for deals
- Switch between different sales processes as needed

**Pain Points:**

- Current systems are slow or expensive
- Limited ability to support multiple distinct sales processes
- Poor mobile/web experience
- Cannot customize product offerings per deal
- Cannot manage different pipeline types (e.g., enterprise vs. SMB)

**Behaviors:**

- Logs in daily
- Creates and updates deals/opportunities
- Manages team member access
- Configures deal pipeline stages
- Creates and switches between sales processes
- Manages product pricing and deal line items
- Runs weekly pipeline reviews

---

### Secondary User: Sales Representative

**Demographics:** 22–50 years old, uses CRM 3–6 hours/day

**Goals:**

- Log interactions with prospects/customers
- Track assigned deals and contact details
- Update deal progress through pipeline stages
- Access contact history quickly
- Add products to deals with pricing
- Understand which process applies to each deal

**Pain Points:**

- Outdated interfaces
- Difficult data entry workflows
- Slow search/lookup functionality
- Inflexible product and pricing options
- Confusion about which pipeline to use for which deal

**Behaviors:**

- Logs in daily to multiple sessions
- Creates new contacts and updates records
- Moves deals through pipeline stages
- Communicates deal status to managers
- Adds and prices products on deals
- References the correct sales process for each deal

---

### Tertiary User: Tenant Administrator

**Demographics:** 30–55 years old, technical-minded non-engineer

**Goals:**

- Manage user accounts and permissions
- Set up organizational structure
- Configure basic access controls
- Monitor system usage and health
- Create and configure multiple sales processes
- Manage product catalog and pricing
- Define default process for new deals

**Pain Points:**

- Manual user provisioning is time-consuming
- Limited role customization in existing tools
- Audit trails are unclear or absent
- Cannot customize multiple process workflows simultaneously
- Cannot easily manage different pipeline types

**Behaviors:**

- Logs in weekly or as-needed
- Provisions new users
- Manages team structure changes
- Reviews access logs occasionally
- Creates and updates sales processes
- Manages product catalog and pricing blueprints
- Sets process defaults and configurations

---

### Quaternary User: System Administrator / Ops Team

**Demographics:** Technical, oversees multiple tenant activations

**Goals:**

- Manage tenant contract signing and activation
- Provision tenants on demand when contracts are signed
- Set up initial product catalogs and default sales processes
- Monitor tenant health and data integrity

**Pain Points:**

- Manual tenant provisioning is error-prone
- Lack of clear activation workflow from signup to go-live
- Difficulty tracking tenant lifecycle
- Need to seed default processes during activation

**Behaviors:**

- Reviews pending activations
- Approves and activates tenants on contract
- Seeds initial product catalogs and default processes
- Monitors tenant onboarding

---

## High-Level User Journeys

### Journey 1: Prospect Signup → Contract Signing → Tenant Activation

1. **Prospect Signup:** Prospect signs up via marketing website (email + password)
2. **Pre-Tenant Account:** System creates user account (not yet a tenant member)
3. **Sales Process:** Sales team engages prospect, discusses pricing and features
4. **Contract Signing:** Prospect signs contract; marked in system as "activated"
5. **Tenant Provisioning:** System admin approves activation; isolated tenant created automatically
6. **Admin User Linkage:** Signup user promoted to tenant admin role in new tenant
7. **Initial Setup:** Admin enters organization metadata (company name, logo, industry)
8. **Product Catalog Seeding:** System seeds default product catalog; admin can customize
9. **Sales Process Setup:** System seeds default sales process (or multiple processes); admin can customize or create additional processes
10. **Team Invite:** Admin invites first sales team members via email links
11. **Team Onboarding:** Team members accept invites, set passwords, access CRM
12. **Go Live:** Team begins using CRM to manage pipeline using configured sales processes

**Key Success Indicators:**

- Signup to activation request: <5 minutes
- Tenant provisioning on activation: <5 minutes
- Full onboarding (through team access): <30 minutes
- No manual intervention required beyond approval
- New users can log in and see empty but ready-to-use CRM module with default processes

---

### Journey 2: Sales Manager - Daily Workflow with Multiple Sales Processes

1. **Login:** Manager logs in via secure auth
2. **Dashboard:** Views CRM dashboard with pipeline overview for multiple processes, team assignments, recent activity
3. **Process Selection:** Can view deals across all processes or filter by specific process
4. **Deal Management:** Reviews open deals within selected process; moves deals through custom stages; updates probability/value
5. **Deal Composition:** Views/edits deal line items (products, pricing, adjustments)
6. **Process Configuration:** Configures sales processes and stages (optional; only admins) to match sales team needs
7. **Team Coordination:** Views deals assigned to team members; coaches on specific opportunities
8. **Reporting:** Runs quick pipeline snapshot per process for standup or management review
9. **Logout:** Session ends

**Key Success Indicators:**

- Login to usable dashboard: <10 seconds
- Common actions (move deal, update value, assign rep, add product): <3 clicks
- Deal stage changes reflected immediately within process
- Process switching instant and seamless
- Dashboard data refreshes within 2 seconds

---

### Journey 3: Sales Rep - Deal Lifecycle with Sales Process Assignment

1. **Login:** Rep logs in
2. **Assigned Deals:** Views list of assigned opportunities (organized by process)
3. **Contact Lookup:** Searches for or creates new prospect contact
4. **Deal Creation:** Creates new deal linked to prospect contact; selects sales process and initial stage within that process
5. **Product Addition:** Adds products to deal; uses pricing blueprint defaults or adjusts price
6. **Updates:** Over time, logs interactions (calls, emails, meetings) and updates deal progress within its assigned process
7. **Stage Management:** Moves deal through stages (specific to the deal's process)
8. **Close:** Moves deal to "Won" or "Lost" with final pricing and notes
9. **History Access:** Reviews past interactions/notes and deal composition on closed deals for reference

**Key Success Indicators:**

- Creating a new deal with process selection: <2 minutes
- Adding products and adjusting pricing: <1 minute
- Updating deal status: <30 seconds
- Moving deal stages within its process: <30 seconds
- Contact search returns results: <2 seconds
- Historical records fully auditable and accessible
- Process context always visible with deal

---

### Journey 4: Tenant Admin - Process & Product Configuration

1. **Login:** Admin logs in
2. **Sales Process Management:** Views all sales processes for the tenant
3. **Create Process:** Adds new sales process (e.g., "Enterprise Sales", "SMB Sales", "Partner Channel")
4. **Configure Stages:** Customizes pipeline stages within each process (add, remove, rename stages)
5. **Stage Rules:** (Optional) Sets stage-specific rules (e.g., required fields, automatic actions)
6. **Set Defaults:** Designates a default process for new deals
7. **Product Management:** Views product catalog
8. **Pricing Blueprint:** Creates/updates product pricing templates (default prices, discount policies)
9. **Product Details:** Manages product SKU, description, category, default price
10. **Activate Products:** Enables/disables products for use in deals
11. **Access Control:** Assigns roles (Admin, Sales Manager, Sales Rep) to control permissions

**Key Success Indicators:**

- Creating a new sales process: <10 minutes
- Configuring pipeline stages within a process: <10 minutes
- Creating product pricing blueprint: <5 minutes
- Changes take effect immediately across all deals in that process
- Sales reps see updated products and correct process stages instantly

---

## Business Requirements

### Functional Requirements

#### F1. Tenant Management & Lifecycle

- **F1.1:** System shall support user signup (email/password) without immediate tenant creation
- **F1.2:** Signup users shall be created in a "pre-tenant" state (system user, no tenant membership)
- **F1.3:** System shall allow marking users as "contract signed" (contract status field)
- **F1.4:** System admin approval of contract signing shall trigger automatic isolated tenant creation
- **F1.5:** On tenant activation, signup user shall be automatically added as tenant admin
- **F1.6:** Each tenant shall have unique workspace metadata (company name, logo, industry, timezone)
- **F1.7:** Tenant admins shall be able to update workspace metadata
- **F1.8:** Tenant deletion shall hard-delete or archive all associated data (configurable retention policy)
- **F1.9:** System shall support tenant suspension (data preserved, access revoked)
- **F1.10:** System shall track tenant lifecycle stages (prospect → pending → active → suspended → deleted)
- **F1.11:** System shall allocate unique subdomains to each tenant (e.g., `tenant-name.aura.com`) for workspace isolation
- **F1.12:** System shall maintain root domain (e.g., `aura.com`) for company marketing, landing page, and public content

#### F2. Authentication & Authorization

- **F2.1:** System shall support email/password authentication via Supabase Auth
- **F2.2:** System shall issue JWT tokens for session management
- **F2.3:** System shall support secure password reset workflows with email verification
- **F2.4:** System shall support email-based user invitations with time-limited invite links
- **F2.5:** Session timeout shall occur after 30 days of inactivity or on explicit logout
- **F2.6:** Pre-tenant users shall not have access to any tenant-specific functionality

#### F3. User Management

- **F3.1:** Tenant admins shall be able to invite new users via email
- **F3.2:** System shall auto-generate secure invite links (valid for 7 days)
- **F3.3:** Invited users shall create passwords on first login
- **F3.4:** Tenant admins shall be able to deactivate (soft-delete) users
- **F3.5:** Deactivated users shall be unable to log in but their data shall remain for audit
- **F3.6:** System shall support user profile (name, email, avatar, department, role)

#### F4. Role-Based Access Control (RBAC)

- **F4.1:** System shall define three baseline roles: Admin, Manager, Sales Rep
  - **Admin:** Full access to user management, settings, reporting, CRM, process configuration, product management
  - **Manager:** Can manage team members, view all team deals, view basic reporting, full CRM access, read-only process/product access
  - **Sales Rep:** Can manage own deals and assigned deals, create/edit contacts assigned to them, add products to deals with pricing
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

- **F8.1:** Users shall be able to create deals (deal name, amount, sales process, stage, close date, probability, associated contact/organization)
- **F8.2:** Users shall be able to view all deals in their tenant
- **F8.3:** Users shall be able to filter deals by sales process, stage, owner, probability, organization, or date range
- **F8.4:** Users shall be able to update deal fields (amount, stage, probability, close date, notes)
- **F8.5:** Users shall be able to soft-delete deals
- **F8.6:** Users shall be able to assign deals to other team members (managers and admins only)
- **F8.7:** Each deal shall be associated with exactly one sales process at creation time
- **F8.8:** Deal stage changes shall be logged with timestamp and user info for audit trail
- **F8.9:** System shall support deal custom fields (future enhancement)

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

#### F11. Process Management - Sales Processes & Deal Stages

- **F11.1:** Tenant admins shall be able to create multiple sales processes per tenant
- **F11.2:** Each sales process shall contain an ordered sequence of deal stages
- **F11.3:** System shall provide a default sales process: Prospect → Qualified → Proposal → Negotiation → Won/Lost
- **F11.4:** Tenant admins shall be able to view, create, update, and delete sales processes per tenant
- **F11.5:** Tenant admins shall be able to view, create, update, and delete deal stages within each sales process
- **F11.6:** Deal stage changes shall be tracked with timestamp and user audit trail
- **F11.7:** Each deal stage shall support optional metadata (e.g., required fields, win probability default)
- **F11.8:** Admins shall be able to reorder stages to define custom pipeline sequence within each process
- **F11.9:** Deal stage configuration shall be immutable for historical deals (stage names locked on close)
- **F11.10:** System shall prevent deletion of stages with active deals without confirmation
- **F11.11:** Sales managers and reps shall move deals between stages via drag-and-drop or UI controls
- **F11.12:** Each stage change shall be logged in deal activity history with user attribution
- **F11.13:** Tenant admins shall be able to set one sales process as the default for new deals
- **F11.14:** Each new deal shall be assigned to either the default process or a selected process during creation

#### F12. Product Management - Pricing Blueprint

- **F12.1:** Tenant admins shall be able to create and manage product catalog (SKU, name, description, category)
- **F12.2:** System shall support defining pricing templates (blueprints) per product
- **F12.3:** Each product blueprint shall include: default price, cost, discount policy, margin targets
- **F12.4:** Admins shall be able to activate/deactivate products to control availability in deals
- **F12.5:** Admins shall be able to view all products and their pricing history
- **F12.6:** Products shall support custom fields (e.g., size, color, variant) per tenant

#### F13. Deal Products & Pricing

- **F13.1:** Users shall be able to add products (line items) to deals during deal creation and updates
- **F13.2:** Each deal line item shall reference a product and inherit the product's pricing blueprint
- **F13.3:** Deal line items shall display: product name, SKU, quantity, unit price (from blueprint), extended price
- **F13.4:** Users shall be able to manually adjust unit price on deal line items (override blueprint default)
- **F13.5:** Deal total shall automatically calculate as sum of all line items (quantity × adjusted unit price)
- **F13.6:** Price adjustments shall be tracked and logged in deal audit trail
- **F13.7:** Users shall be able to add, edit, and remove line items from deals
- **F13.8:** Removed line items shall be soft-deleted (retained in audit history)
- **F13.9:** Deal line items shall support quantity field (minimum 1)
- **F13.10:** System shall validate deal totals match sum of line items on deal save

#### F14. Data Integrity & Audit

- **F14.1:** All records shall have created_at and updated_at timestamps
- **F14.2:** All records shall have created_by and updated_by user references
- **F14.3:** System shall maintain an audit log table for sensitive operations (user creation, role changes, data deletions, RLS violations, stage changes, pricing changes, process creation)
- **F14.4:** Audit logs shall be immutable and retained for minimum 12 months
- **F14.5:** Tenant admins shall be able to view audit logs for their tenant
- **F14.6:** Deal audit trail shall include all price changes, stage transitions, and product additions/removals

#### F15. Notifications (Future Phase)

- **F15.1:** System shall support in-app notification preferences
- **F15.2:** System shall notify users of relevant events (deal reassignment, stage changes, contact activity, team invitations, process changes)

### Non-Functional Requirements

#### Performance

- **NFR-P1:** API response time for list queries (deals, contacts, organizations, products, sales processes) shall not exceed 2 seconds for up to 10,000 records per tenant
- **NFR-P2:** Search queries (contacts by name, deals by organization, deals by process) shall return results within 1 second
- **NFR-P3:** Dashboard and overview page shall load within 3 seconds on a standard 4G connection
- **NFR-P4:** Database indexes shall be maintained on all frequently-queried fields (tenant_id, process_id, assigned_to, stage, created_at, product_id)
- **NFR-P5:** Query pagination shall default to 25 records per page; max 100 per page to prevent memory exhaustion
- **NFR-P6:** Deal calculations (total, line items) shall be computed in real-time with <500ms latency

#### Security

- **NFR-S1:** All authentication shall occur via industry-standard OAuth/JWT (Supabase Auth)
- **NFR-S2:** All passwords shall be salted and hashed; plaintext passwords never stored
- **NFR-S3:** All inter-tenant communication shall be blocked at the database level via RLS
- **NFR-S4:** API endpoints shall validate tenant membership before processing requests
- **NFR-S5:** All API communication shall use HTTPS/TLS 1.2 or higher
- **NFR-S6:** Tenant admins shall have no visibility into other tenants' data under any circumstance
- **NFR-S7:** System shall enforce API rate limiting (100 requests per minute per user)
- **NFR-S8:** Sensitive operations (user deletion, data export, pricing changes, process deletion) shall require email verification or OTP
- **NFR-S9:** PII (personally identifiable information) shall not be logged except in audit trails

#### Scalability

- **NFR-Sc1:** Platform shall support up to 1,000 concurrent users per tenant
- **NFR-Sc2:** Database schema shall support 100+ tenants with shared infrastructure without performance degradation
- **NFR-Sc3:** Application shall be horizontally scalable (stateless API servers behind load balancer)
- **NFR-Sc4:** System shall support addition of new modules without modifying core platform tables
- **NFR-Sc5:** Tenants shall be able to create and manage 10+ distinct sales processes without performance impact

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
- **NFR-U5:** Deal stage management shall support drag-and-drop and intuitive bulk operations
- **NFR-U6:** Sales process selection and switching shall be intuitive and clearly labeled

#### Maintainability

- **NFR-M1:** All code shall follow Next.js and TypeScript best practices
- **NFR-M2:** API endpoints shall be documented via OpenAPI/Swagger
- **NFR-M3:** Database schema shall be version-controlled and documented
- **NFR-M4:** RLS policies shall be clearly commented and auditable
- **NFR-M5:** Logs shall be structured and machine-readable for monitoring/alerting

---

## Success Metrics

### Functional Completeness

| Metric                               | Target                            | Measurement              |
| ------------------------------------ | --------------------------------- | ------------------------ |
| MVP feature launch                   | 100% of F1–F15 requirements       | Feature checklist audit  |
| Zero cross-tenant data incidents     | 0/month                           | Security audit + testing |
| Zero cross-process data incidents    | 0/month                           | Security audit + testing |
| API endpoint coverage                | ≥95% unit test coverage           | Test report              |
| RLS policy enforcement               | 100% of tenant-scoped queries     | Automated policy tests   |
| Sales process configuration accuracy | 100% compliance with admin config | Integration tests        |
| Deal-to-process assignment accuracy  | 100% correct mappings             | Integration tests        |
| Deal pricing calculation accuracy    | 100% line item reconciliation     | Pricing validation tests |

### User Experience

| Metric                              | Target                | Measurement                 |
| ----------------------------------- | --------------------- | --------------------------- |
| Signup to contract signing          | <5 minutes            | User flow timing            |
| Tenant provisioning time            | <5 minutes            | Admin activation workflow   |
| Full onboarding (admin first login) | <30 minutes           | Timing data from beta users |
| Login-to-CRM time                   | <10 seconds           | Performance monitoring      |
| Deal stage change time              | <30 seconds           | UX testing                  |
| Process creation time               | <10 minutes           | UX testing                  |
| Adding product to deal              | <1 minute             | UX testing                  |
| CRUD operation time (avg)           | <3 clicks, <2 minutes | UX testing                  |
| Mobile usability score              | ≥80/100               | Lighthouse / manual testing |

### Performance

| Metric                           | Target                   | Measurement            |
| -------------------------------- | ------------------------ | ---------------------- |
| API response time (list queries) | <2 seconds (10k records) | APM / monitoring       |
| Deal calculation latency         | <500ms                   | Performance monitoring |
| Dashboard load time              | <3 seconds on 4G         | Synthetic monitoring   |
| Search latency                   | <1 second                | Query performance logs |
| 99th percentile API latency      | <5 seconds               | APM dashboard          |
| Sales process list latency       | <1 second                | Query performance logs |

### Security & Reliability

| Metric                           | Target  | Measurement                |
| -------------------------------- | ------- | -------------------------- |
| Uptime                           | ≥99.5%  | Monitoring alerts          |
| Security vulnerability incidents | 0       | Incident logs              |
| Backup success rate              | 100%    | Automated backup logs      |
| Mean time to recovery (MTTR)     | <1 hour | Incident response tracking |

### Adoption & Business

| Metric                               | Target                   | Measurement         |
| ------------------------------------ | ------------------------ | ------------------- |
| Beta tenant activation (post-signup) | 10 tenants (Phase 1 end) | Signup tracking     |
| Monthly active users (beta)          | ≥50                      | Analytics dashboard |
| Average processes per tenant         | ≥1.5                     | Usage analytics     |
| Average deal products per deal       | ≥1.5                     | Usage analytics     |
| Net promoter score (NPS)             | ≥40                      | User survey         |
| Feature request volume               | Track trends             | Feedback analysis   |

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
- **Advanced Product Management:** Multi-tiered pricing, volume discounts, tiered promotions (future phase)
- **Deal Approval Workflows:** Deal approval/authorization workflows (future phase)
- **Process Templates:** Pre-built process templates (Phase 2+)

---

## Business Value

### Assessment: **HIGH**

### Justification

1. **Market Opportunity:** SMB CRM market is fragmented with few affordable, secure alternatives. Estimated TAM of $500M+ (SMB CRM segment). Early entry positions Aura as a growing alternative to expensive incumbents.

2. **Technical Competitive Advantage:** Database-enforced RLS differentiates Aura from competitors relying on application-layer security. Multi-process support differentiates from single-pipeline competitors. This is a trust and security differentiator that resonates with security-conscious buyers and compliance-sensitive industries.

3. **Operational Efficiency:** Deferred tenant creation (on contract signing) reduces operational overhead and enables cleaner sales funnel tracking. Multi-process flexibility and dynamic deal routing improve sales team adoption across diverse deal types. Product pricing and deal customization increase deal values.

4. **Platform Foundation:** This epic establishes reusable architecture for future modules (HCM, testing, internal tools). Every dollar invested in core platform infrastructure multiplies across future products, reducing time-to-market for modules 2–5.

5. **Revenue Generation:** CRM module generates immediate recurring revenue (SaaS subscription model). Conservative estimate: 50 paying tenants in Year 1 at $99–299/month = $59K–179K ARR. Product pricing, deal customization, and multi-process support increase deal values.

6. **Risk Mitigation:** Proven, secure multi-tenant foundation reduces risk of future pivots or rewrites. Upfront investment prevents costly architectural debt.

7. **Talent & Partnership:** Successful, secure SaaS platform attracts developers, investors, and strategic partners. Establishes credibility for enterprise sales in future phases.

### ROI Estimate

- **Development Cost (rough):** 8–11 months × 3 FTE engineers ≈ $400K–500K (includes multi-process and product management features)
- **Year 1 Revenue (conservative):** $60K–180K
- **Break-even:** 24–36 months post-launch
- **Long-term Value:** Platform extends to 5+ modules, each generating $100K–500K+ ARR; total potential $1M–3M ARR by Year 4

---

## Epic Dependencies & Milestones

### Phase 1 Milestones

| Milestone                    | Duration      | Key Deliverables                                                         |
| ---------------------------- | ------------- | ------------------------------------------------------------------------ |
| Architecture & Design        | 2 weeks       | Technical arch spec, DB schema, API design, tenant lifecycle flow        |
| Core Platform Dev            | 4 weeks       | Auth, pre-tenant accounts, contract lifecycle, tenants, users, RBAC, RLS |
| CRM + Process Management Dev | 4 weeks       | Orgs, contacts, deals, sales processes, deal stages, CRUD, customization |
| Product Management Dev       | 2 weeks       | Product catalog, pricing blueprints, deal line items, pricing logic      |
| Testing & QA                 | 2 weeks       | Unit, integration, security, performance, pricing validation tests       |
| Beta Launch Prep             | 1 week        | Monitoring setup, documentation, onboarding, tenant activation workflow  |
| **Total**                    | **~15 weeks** | Ready for beta user testing                                              |

### External Dependencies

- Supabase infrastructure provisioning & RLS policy support
- Next.js framework stability
- Security audit resource availability
- Beta user availability for feedback
- Legal review of contract signing workflow

---

## Open Questions & Assumptions

### Assumptions

1. Each user belongs to exactly one tenant (no multi-tenancy at user level in MVP)
2. Each deal belongs to exactly one sales process (no cross-process deals)
3. Supabase is the system of record for authentication
4. Initial team size per tenant: <100 users (auto-scaling after MVP)
5. Initial processes per tenant: <10 (typical for Phase 1 usage)
6. CRM will be the only module at launch
7. Contract signing is handled by external system (e.g., DocuSign); we track status in Aura
8. System admin has approval role for tenant activation
9. Sales process stages are fully customizable per process (no global defaults enforced)
10. Product pricing blueprints are optional; sales reps can override prices per deal
11. Tenants can create processes dynamically (no approval workflow required)

### Open Questions for Stakeholder Alignment

1. **Process Switching:** Can deals be reassigned from one process to another after creation? → Impacts data model and UI
2. **Process Cloning:** Should admins be able to clone existing processes as templates? → Phase 2 enhancement
3. **Stage Locking:** Should completed stages be locked to prevent reopening? → Impacts workflow design
4. **Process Metrics:** Should different processes have separate pipeline metrics? → Impacts analytics and reporting
5. **Default Process:** Should all deals use a default process if not specified? → Impacts creation workflow
6. **Process Archival:** Should admins be able to archive processes (hide but keep data)? → Impacts data lifecycle

---

## Approval & Next Steps

**This Epic PRD is ready for:**

1. ✅ Stakeholder review and sign-off
2. ✅ Translation into a Technical Architecture Specification (arch.md)
3. ✅ Story breakdown and sprint planning
4. ✅ Engineering estimation and capacity planning

**Next Deliverable:** Technical Architecture Specification (TAS) detailing:

- Updated database schema (sales processes, process-to-deal mapping, process stages)
- Tenant activation workflow with default process seeding
- Multi-process management (creation, configuration, stage management)
- Product management (pricing blueprints and deal line items)
- Deal calculation and pricing logic with process context
- API endpoint specifications
- Security & compliance implementation details
