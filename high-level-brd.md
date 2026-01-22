---
post_title: Multi-Tenant CRM Platform - Business Requirements Document
author1: Product Team
post_slug: multi-tenant-crm-platform-brd
microsoft_alias: product-team
featured_image: ""
categories:
  - Business Requirements
  - CRM Platform
tags:
  - multi-tenant
  - crm
  - saas
  - postgresql
  - rls
  - multi-process
ai_note: false
summary:
  Business requirements document for a secure, multi-tenant CRM platform built
  with Next.js and Supabase, establishing the foundation for future modular business
  modules with database-enforced tenant isolation and multi-process sales pipeline management.
post_date: 2026-01-22
---

## Document Overview

**Product Name (Working):** Multi-Tenant CRM Platform

**Document Type:** Business Requirements Document (BRD)

**Version:** 1.2

**Status:** Approved (Comprehensive - Aligned with Epic and Architecture)

**Primary Tech Stack:** Next.js, Supabase (PostgreSQL + RLS), tRPC, Stack Auth

**Related Documents:**

- Epic Product Requirements Document: `/docs/ways-of-work/plan/multi-tenant-crm-platform/epic.md`
- Technical Architecture Specification: `/docs/ways-of-work/plan/multi-tenant-crm-platform/arch.md`

## Purpose of the Document

This BRD defines the business goals, scope, user journeys, and high-level requirements for building
a secure, multi-tenant CRM platform with multi-process sales pipeline support. It establishes the foundation for future business modules (HCM, testing systems, internal tools) with database-enforced tenant isolation and role-based access control.

## Business Objective

The objective is to build a modular, SaaS-ready platform that:

- Launches with a CRM-first module featuring flexible multi-process sales pipeline management
- Supports multiple independent tenants (organizations) on shared infrastructure
- Enables each tenant to define multiple customizable sales processes for different deal types
- Enforces strict tenant-level data isolation at the database level (PostgreSQL RLS)
- Implements role-based access control (Admin, Manager, Sales Rep)
- Decouples user signup from tenant creation (tenants provisioned only on contract signing)
- Allocates unique subdomains to each tenant (e.g., `acme.aura.com`)
- Can expand into additional modules without re-architecting core systems

The platform must prioritize data security, correctness, and scalability from day one. Zero cross-tenant and zero cross-process data leakage incidents are non-negotiable requirements.

## Target Market

### Primary Market

- Small to Medium Businesses (10–300 employees)
- Sales-driven organizations needing affordable, secure CRM with flexible pipelines
- Organizations needing multiple distinct sales processes (enterprise, SMB, partners)

### Secondary Market

- SaaS founders and startups requiring secure multi-tenant infrastructure
- Internal tooling teams requiring modular, extensible platforms
- Enterprises evaluating alternatives to expensive legacy CRM systems

## Problem Statement

Organizations require CRM systems but face significant challenges:

**Existing CRM platforms are:**

- Prohibitively expensive ($100+ per user per month)
- Overly complex with unnecessary features
- Difficult to customize for multiple distinct sales processes
- Lacking in database-level security (rely on application-layer isolation)

**Custom-built CRMs often:**

- Support only a single pipeline, forcing all deal types into one process
- Rely solely on application-layer tenant checks (high data leakage risk)
- Risk cross-tenant and cross-process data leakage
- Are hard to scale into multiple modules without major refactoring
- Lack proper audit trails and compliance features

**Critical business risks:**

- Tenant data leaks destroy trust and expose compliance violations (GDPR, SOC2)
- Inability to manage multiple sales processes reduces team efficiency
- Lack of scalable architecture increases time-to-market for new features

## Solution Overview

The Multi-Tenant CRM Platform provides:

- **Multi-Tenant Architecture**: Each tenant receives isolated workspace with unique subdomain (`tenant-name.aura.com`)
- **Database-Enforced Isolation**: PostgreSQL Row Level Security (RLS) prevents cross-tenant data access at database level
- **Multi-Process Sales Pipeline**: Each tenant can create 1-N distinct sales processes for different deal types (enterprise, SMB, partner channel)
- **Deal-to-Process Binding**: Each deal belongs to exactly one sales process at creation time (immutable relationship)
- **Flexible Stage Management**: Customize deal stages within each process independent of other processes
- **Product Management**: Product catalog with pricing blueprints and deal-level line item overrides
- **Contract Lifecycle Integration**: Deferred tenant creation triggered by contract signing and system admin approval
- **Role-Based Access Control**: Three baseline roles (Admin, Manager, Sales Rep) with extensible permission model
- **Activity Audit Trail**: Immutable activity logs for contacts, deal stage transitions, pricing changes, and user actions
- **Shared Core Metadata Layer**: Users, tenants, roles, processes managed centrally with RLS enforcement
- **Module-Based Architecture**: Foundation supports adding HCM, testing systems, and internal tools modules

## Scope

### In Scope (Phase 1 – MVP)

**Core Platform Layer**

- User signup with pre-tenant account support (email/password via Supabase Auth)
- Contract signing workflow and tenant activation on admin approval
- Tenant provisioning (auto-creation of tenant, subdomains, default process, product catalog)
- Tenant management (metadata, subdomain allocation, lifecycle tracking)
- User management (invitations, role assignment, deactivation)
- Role-Based Access Control (Admin, Manager, Sales Rep roles with API/UI enforcement)
- Tenant-level data isolation via PostgreSQL RLS policies
- Session management with tenant context propagation (JWT tokens)
- Audit logging (immutable, 12-month retention for compliance)

**CRM Module**

- **Organizations**: Create, read, update, soft-delete organizations with metadata, relationships, custom fields
- **Contacts**: Create, read, update, soft-delete contacts with email uniqueness per tenant, custom fields, user assignment
- **Deals**: Create, read, update, soft-delete deals with process binding (immutable), stage transitions, filtering by process/stage/owner
- **Activities**: Immutable call/email/meeting/note logs linked to contacts with participant tracking
- **User Assignment**: Contacts and deals support assigned_to field; role-based visibility enforcement

**Process Management**

- **Sales Processes**: Create, read, update, delete sales processes per tenant with default process designation
- **Deal Stages**: Create, read, update, delete, reorder deal stages within each process
- **Stage Metadata**: Support optional required fields, win probability defaults per stage
- **Deal Stage Transitions**: Track stage changes with timestamps and user attribution in audit trail
- **Process Filtering**: Dashboard and list views filterable by sales process
- **Default Process Seeding**: System auto-seeds default process (Prospect → Qualified → Proposal → Negotiation → Won/Lost) during tenant activation

**Product Management**

- **Product Catalog**: SKU, name, description, category, activate/deactivate per tenant
- **Pricing Blueprints**: Default price, cost, discount policy, margin targets per product
- **Deal Line Items**: Add products to deals with quantity and unit price (inherited from blueprint, overridable)
- **Pricing Audit Trail**: Track all pricing changes (blueprint and overrides) with before/after values
- **Deal Totals**: Automatic calculation as sum of line items (quantity × adjusted unit price)

### Out of Scope (Phase 1)

- **Email Integration**: Email sync, native email client, email templates
- **Advanced Reporting**: Custom report builder, forecasting, AI-driven insights
- **Marketing Automation**: Campaigns, nurture sequences, lead scoring
- **Workflow Automation**: Custom workflows, third-party integrations, webhooks
- **Mobile Native Apps**: iOS/Android native applications (web-responsive UI in-scope)
- **Marketplace**: App store, third-party extensions, ecosystem partnerships
- **Other Modules**: HCM, testing systems, document collaboration (Phase 2+)
- **Advanced Customization**: Custom fields UI builder, custom object types
- **Multi-Language Support**: English-only in Phase 1; internationalization deferred
- **Enterprise SSO/SAML**: Single sign-on features deferred to Phase 2+
- **Process Templates**: Pre-built process templates deferred to Phase 2+

## Key Business Rules

### Tenant Isolation (Critical)

- Users may only access data belonging to their assigned tenant
- Tenant isolation must be enforced at the database level via PostgreSQL RLS policies
- Each tenant's processes, deals, contacts, and organizations are isolated and not visible to other tenants
- Pre-tenant users cannot access any tenant-specific functionality until activated

### Sales Process Model

- Each tenant can define 1 to N distinct sales processes (e.g., "Enterprise Sales", "SMB Sales", "Partner Channel")
- Each deal must be associated with exactly one sales process at creation time
- Deal-to-process relationship is immutable (cannot be changed after creation)
- Each sales process contains an ordered sequence of customizable stages
- Stages are unique within a process but can have identical names across different processes
- Each tenant designates one process as the default for new deals

### Data Ownership & Scoping

- All tenant-scoped records must include a tenant_id column
- All queries must be tenant-aware (filtered by current user's tenant_id)
- Sales processes are tenant-owned resources (not shared across tenants)
- RLS policies enforce that users can only access data of their assigned tenant

### Security Enforcement

- No reliance on frontend filtering for security
- No reliance on middleware-only enforcement (database is the source of truth)
- All sensitive operations require RLS validation at database level
- All mutations tracked in immutable audit logs with user attribution

### Contract Lifecycle

- Users sign up in pre-tenant state (email/password via Supabase Auth)
- Contract signing marked in system (contract_status field updated)
- System admin approves activation, triggering automatic provisioning
- Tenant created in isolated database environment with unique subdomain
- Default sales process and product catalog auto-seeded
- Signup user promoted to tenant admin role on activation

## Non-Functional Requirements

### Security (Critical)

- **Database-Level RLS**: PostgreSQL RLS policies enforce tenant isolation (no application-layer bypasses)
- **Cross-Tenant Prevention**: Zero tolerance for cross-tenant data access incidents
- **Cross-Process Prevention**: Zero tolerance for cross-process data access incidents
- **Authentication**: Supabase Auth with JWT tokens including tenant_id and user role
- **Passwords**: Salted and hashed; plaintext passwords never stored
- **API Communication**: HTTPS/TLS 1.2 or higher for all communications
- **Rate Limiting**: 100 requests per minute per user
- **Sensitive Operations**: User deletion, data export, pricing changes, process deletion require email verification or OTP
- **PII Protection**: Personally identifiable information logged only in immutable audit trails
- **Audit Logging**: All sensitive operations logged immutably with 12-month minimum retention
- **Deal-Process Binding**: Immutable at database level via constraints (prevent unauthorized reassignment)

### Scalability

- Support up to 1,000 concurrent users per tenant
- Support 100+ tenants on shared infrastructure without performance degradation
- Support 10+ sales processes per tenant without performance impact
- Horizontally scalable (stateless API servers behind load balancer)
- Support addition of new modules without modifying core platform tables

### Performance

- API response time for list queries: <2 seconds (10k records per tenant)
- Search queries (contacts by name, deals by organization/process): <1 second
- Dashboard load time: <3 seconds on 4G connection
- Deal calculations: <500ms latency (real-time pricing calculations)
- 99th percentile API latency: <5 seconds
- Database indexes on tenant_id, process_id, assigned_to, stage, created_at, product_id

### Reliability & Uptime

- **SLA Target**: 99.5% uptime (monitored monthly)
- **Backup**: Automated daily backups retained 30 days
- **Disaster Recovery**: Restoration capability within 4 hours of failure
- **Error Handling**: Graceful handling of database connection failures with user-friendly messages

### Data Privacy & Compliance

- **GDPR Compliance**: Support data retention and deletion requirements
- **Data Export**: Tenants can export data in JSON/CSV formats
- **Account Termination**: Support full data deletion on request
- **Data Processing**: Document all data processing in Data Processing Addendum
- **PII Encryption**: Optional encryption at rest (tenant-configurable)
- **SOC2 Ready**: Architecture supports SOC2 Type II compliance

### Accessibility & Usability

- **WCAG 2.1 Level AA**: UI compliance with accessibility standards
- **Form Validation**: Clear error messaging and validation feedback
- **Mobile Responsive**: Works on screens as small as 320px width
- **Intuitive Navigation**: Non-technical users can operate CRM without training
- **Drag-and-Drop**: Deal stage management supports drag-and-drop operations
- **Process Clarity**: Sales process selection and switching clearly labeled and intuitive

### Maintainability

- **Code Standards**: Next.js and TypeScript best practices throughout
- **API Documentation**: OpenAPI/Swagger specifications for all endpoints
- **Database Documentation**: Schema version-controlled and documented
- **RLS Policies**: Clearly commented and auditable
- **Structured Logging**: Machine-readable logs for monitoring and alerting
- **Type Safety**: End-to-end type safety via TypeScript and tRPC

## Technical Constraints

- Must use **PostgreSQL** as primary datastore (with Supabase managed service)
- Must support **tenant isolation via PostgreSQL RLS** (database-level, not application-level)
- Must support **multi-process model** with deal-to-process mapping (immutable relationships)
- Must support **role-based access control** (Admin, Manager, Sales Rep with extensible model)
- Must be compatible with **Next.js application architecture** with App Router
- Must use **tRPC** for end-to-end type-safe APIs
- Must use **Stack Auth** for authentication
- Must use **Turborepo** for monorepo workspace management
- Must use **Docker** for containerization and deployment

## Assumptions

### Architecture & Deployment

1. Each user belongs to exactly one tenant (no cross-tenant user membership in MVP)
2. Each deal belongs to exactly one sales process (no multi-process deals)
3. Supabase is the system of record for authentication and primary database
4. Contract signing is managed by external system (e.g., DocuSign); Aura tracks status
5. Initial team size per tenant: <100 users (auto-scaling after MVP validation)
6. Initial processes per tenant: <10 (typical Phase 1 usage)
7. CRM will be the only module at launch (other modules in Phase 2+)
8. System admin has approval role for contract signing/tenant activation
9. Sales process stages are fully customizable per process (no global defaults enforced)
10. Product pricing blueprints are optional; sales reps can override prices per deal line item
11. Tenants can create sales processes dynamically (no approval workflow required)

### User Behavior & Adoption

1. Sales teams will organize deals by sales process (not force all deals into one process)
2. Admins will customize processes to match their sales methodology within first week
3. Team members will invite additional users during first month of activation
4. Most deals will use the default process; advanced processes created as needed
5. Product pricing will be maintained centrally; line item overrides will be exception cases

## Risks and Mitigations

| Risk                                    | Likelihood | Impact | Mitigation Strategy                                                        |
| --------------------------------------- | ---------- | ------ | -------------------------------------------------------------------------- |
| Cross-tenant data leakage               | Low        | High   | Enforce RLS at DB level; comprehensive security audit; penetration testing |
| Cross-process data leakage              | Low        | High   | Process isolation testing; deal-process binding validation at API and DB   |
| Performance degradation at scale        | Medium     | Medium | Index tenant_id/process_id; cache processes/products; load testing         |
| Developer error accessing wrong data    | Medium     | High   | Middleware enforcement; TypeScript validation; code review process         |
| Deal calculation pricing errors         | Low        | Medium | Unit tests for pricing logic; audit trail validation; reconciliation       |
| Subdomain collision or conflicts        | Low        | Medium | Unique DB constraint; DNS validation; pre-check availability               |
| Contract workflow confusion             | Medium     | Low    | Clear UI indicators; logging of state transitions; documentation           |
| Slow adoption due to process complexity | Medium     | Medium | Simple defaults; wizard-based process creation; documentation; training    |
| Tenant-process-deal coupling errors     | Low        | High   | Integration tests; database constraints; comprehensive validation          |

## Success Metrics

### Functional Completeness

| Metric                              | Target                | Measurement Method         |
| ----------------------------------- | --------------------- | -------------------------- |
| MVP feature launch                  | 100% of requirements  | Feature checklist audit    |
| Zero cross-tenant incidents         | 0/month               | Security audit + testing   |
| Zero cross-process incidents        | 0/month               | Process isolation testing  |
| API endpoint coverage               | ≥95% unit test        | Test report                |
| RLS policy enforcement              | 100% of queries       | Automated RLS policy tests |
| Process configuration accuracy      | 100% compliance       | Integration tests          |
| Deal-to-process assignment accuracy | 100% correct mappings | Integration tests          |
| Deal pricing calculation accuracy   | 100% reconciliation   | Pricing validation tests   |

### User Experience

| Metric                        | Target            | Measurement Method          |
| ----------------------------- | ----------------- | --------------------------- |
| Signup to contract signing    | <5 minutes        | User flow timing            |
| Tenant provisioning time      | <5 minutes        | Admin activation workflow   |
| Full onboarding (team access) | <30 minutes       | Beta user timing data       |
| Login-to-CRM time             | <10 seconds       | Performance monitoring      |
| Deal stage change time        | <30 seconds       | UX testing                  |
| Process creation time         | <10 minutes       | UX testing                  |
| Adding product to deal        | <1 minute         | UX testing                  |
| Average CRUD operation time   | <3 clicks, <2 min | UX testing                  |
| Mobile usability score        | ≥80/100           | Lighthouse + manual testing |

### Performance & Reliability

| Metric                           | Target                | Measurement Method         |
| -------------------------------- | --------------------- | -------------------------- |
| API response time (list queries) | <2 seconds (10k recs) | APM monitoring             |
| Deal calculation latency         | <500ms                | Performance monitoring     |
| Dashboard load time              | <3 seconds (4G)       | Synthetic monitoring       |
| Search latency                   | <1 second             | Query performance logs     |
| 99th percentile API latency      | <5 seconds            | APM dashboard              |
| Uptime (monthly)                 | ≥99.5%                | Monitoring alerts          |
| Backup success rate              | 100%                  | Automated backup logs      |
| Mean time to recovery (MTTR)     | <1 hour               | Incident response tracking |

### Adoption & Business

| Metric                         | Target                   | Measurement Method  |
| ------------------------------ | ------------------------ | ------------------- |
| Beta tenant activation         | 10 tenants (Phase 1 end) | Signup tracking     |
| Monthly active users (beta)    | ≥50                      | Analytics dashboard |
| Average processes per tenant   | ≥1.5                     | Usage analytics     |
| Average deal products per deal | ≥1.5                     | Usage analytics     |
| Net promoter score (NPS)       | ≥40                      | User survey         |
| Feature request volume         | Track trends             | Feedback analysis   |
| User retention (30-day)        | ≥70%                     | Cohort analysis     |

## High-Level User Journeys

### Journey 1: Prospect Signup → Contract Signing → Tenant Activation

**Timeline:** <1 hour from contract approval to team CRM access

1. Prospect signs up via marketing website (email + password)
2. System creates pre-tenant account (no tenant membership)
3. Sales team engages prospect, discusses pricing and features
4. Prospect signs contract (marked in system as "contract_signed")
5. System admin reviews and approves activation
6. System automatically provisions isolated tenant:
   - Creates tenant with unique subdomain (e.g., `acme.aura.com`)
   - Seeds default sales process with 5 stages
   - Seeds default product catalog
   - Creates TenantMember with admin role for signup user
   - Updates contract_status to "activated"
7. Tenant admin logs in and configures workspace
8. Admin invites first team members via email (7-day links)
9. Team members accept invites, create passwords, access CRM
10. Team begins managing pipeline using configured processes

### Journey 2: Sales Manager - Daily Multi-Process Workflow

**Typical interaction time:** 10-15 minutes per session

1. Manager logs in via secure auth
2. Views CRM dashboard with pipeline overview across processes
3. Selects primary sales process (or views all processes)
4. Reviews open deals in selected process
5. Moves deals between stages, updates probability/value
6. Views/edits deal line items (products and pricing)
7. Assigns deals to team members
8. Views team member deals and provides coaching
9. Runs quick pipeline snapshot per process for standup
10. Reviews activity log for recent interactions

### Journey 3: Sales Rep - Deal Lifecycle with Process Assignment

**Typical deal cycle:** 2-30+ days

1. Rep logs in to see assigned deals
2. Creates new contact from prospect info
3. Creates new deal:
   - Selects sales process (or uses tenant default)
   - Links to prospect contact and organization
   - Sets initial stage within process
   - Adds products and pricing
4. Logs interactions (calls, emails, meetings) over time
5. Moves deal through process stages as it progresses
6. Adjusts deal probability and close date
7. Adds additional products to deal as needed
8. Closes deal as "Won" or "Lost" with final notes
9. Refers to closed deal history and interactions

### Journey 4: Tenant Admin - Sales Process & Product Configuration

**Typical setup time:** 30-60 minutes post-activation

1. Admin logs in to newly activated tenant
2. Configures workspace metadata (company name, logo, industry)
3. Reviews and customizes default sales process stages
4. Creates additional processes for different deal types:
   - "Enterprise Sales" with 6 stages
   - "SMB Sales" with 4 stages
   - "Partner Channel" with 3 stages
5. Manages product catalog:
   - Reviews default products
   - Adds/customizes SKUs and pricing
   - Sets discount policies per product
6. Sets one process as default for new deals
7. Assigns roles to team members (Admin, Manager, Sales Rep)
8. Monitors initial team activity and provides guidance

## Future Roadmap (High-Level)

### Phase 2: CRM Enhancements (Q3-Q4 2026)

- Advanced reporting and forecasting
- Process templates (pre-built processes for common industries)
- Deal workflows and automation
- Email integration and sync
- Custom fields UI builder

### Phase 3: HCM Module (2027)

- Employee management
- Team/department organization
- Performance and compensation management

### Phase 4: Testing/QA System (2027)

- Test case management
- Results tracking
- Compliance reporting

### Phase 5: Marketplace & Integrations (2027+)

- Webhook support for third-party integrations
- Native integrations with common tools
- App marketplace for partners

## Business Value Assessment

### Assessment: **HIGH**

### Justification

1. **Market Opportunity**: SMB CRM market is fragmented with few affordable, secure alternatives. Estimated TAM of $500M+ in SMB CRM segment. Early entry positions Aura as a growing alternative to expensive incumbents (Salesforce, HubSpot, Pipedrive).

2. **Technical Competitive Advantage**:

   - Database-enforced RLS differentiates Aura from competitors relying on application-layer security
   - Multi-process support differentiates from single-pipeline competitors
   - This is a trust and security differentiator that resonates with security-conscious and compliance-sensitive buyers

3. **Operational Efficiency**:

   - Deferred tenant creation reduces operational overhead
   - Multi-process flexibility improves sales team adoption across diverse deal types
   - Product pricing customization increases average deal values

4. **Platform Foundation**:

   - Establishes reusable architecture for future modules (HCM, testing, internal tools)
   - Every dollar invested in core platform multiplies across future products
   - Reduces time-to-market for modules 2–5 by 40-50%

5. **Revenue Generation**:

   - CRM module generates immediate recurring revenue (SaaS model)
   - Conservative Year 1 estimate: 50 paying tenants at $99–299/month = $59K–179K ARR
   - Product customization and multi-process support increase average revenue per tenant

6. **Risk Mitigation**:

   - Proven secure multi-tenant foundation reduces pivot/rewrite risk
   - Upfront investment prevents costly architectural debt
   - Establishes credibility for enterprise sales in future phases

7. **Talent & Partnership**:
   - Successful secure platform attracts developers, investors, and strategic partners

### ROI Estimate

- **Development Cost (rough):** 8–11 months × 3 FTE engineers ≈ $400K–500K
- **Year 1 Revenue (conservative):** $60K–180K
- **Break-even Timeline:** 24–36 months post-launch
- **Long-term Value:** Platform extends to 5+ modules, each generating $100K–500K+ ARR; total potential $1M–3M ARR by Year 4

## Implementation Timeline

| Milestone                      | Duration      | Key Deliverables                                                |
| ------------------------------ | ------------- | --------------------------------------------------------------- |
| Architecture & Design          | 2 weeks       | Technical arch spec, DB schema, API design, security model      |
| Core Platform Development      | 4 weeks       | Auth, tenants, users, RBAC, RLS policies, contract workflow     |
| CRM + Process Management Dev   | 4 weeks       | Orgs, contacts, deals, sales processes, stages, CRUD, filtering |
| Product Management Development | 2 weeks       | Product catalog, pricing blueprints, line items, calculations   |
| Testing & QA                   | 2 weeks       | Unit, integration, security, performance, compliance tests      |
| Beta Launch Preparation        | 1 week        | Monitoring, documentation, onboarding, tenant activation tools  |
| **Total Duration**             | **~15 weeks** | Ready for beta user testing                                     |

## Approval & Next Steps

This document establishes the comprehensive business foundation for the Multi-Tenant CRM Platform with multi-process support and authorizes progression to:

1. ✅ Engineering team resource allocation and capacity planning
2. ✅ Detailed feature-level PRD creation and task breakdown
3. ✅ Sprint planning and development initiation
4. ✅ Beta user recruitment and onboarding process design

**Next Deliverables:**

- Feature-level PRDs for each epic feature
- Technical implementation tasks and story breakdown
- Engineering estimation and velocity planning
- Beta user recruitment and testing plan
