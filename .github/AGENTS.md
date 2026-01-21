## Agent Roles

### 🎯 PM Agent (Product Manager + Business Analyst)

**Location**: `.github/agents/pm/`

**Responsibilities**:

- Create and manage features
- Define product roadmap and strategy
- Write user stories
- Define acceptance criteria
- Create technical specifications
- Break features into tasks
- Prioritize work items
- Bridge business and development

**Key Files**:

- `system-prompt.md` - Combined PM/BA guidelines
- `instructions/` - Feature creation, user stories, acceptance criteria, prioritization, specifications, task breakdown
- `skills/` - Create features, write stories, define criteria, prioritize, create specs
- `prompts/` - Feature request templates, user story templates, acceptance criteria guides

**Quick Commands**:

- `@pm create feature for [feature-name]`
- `@pm create story for [feature]`
- `@pm define acceptance criteria for [story]`

---

### 💻 Dev Agent (Developer)

**Location**: `.github/agents/dev/`

**Responsibilities**:

- Implement features from stories
- Write and review code
- Create pull requests
- Fix bugs
- Maintain code quality
- Setup infrastructure

**Key Files**:

- `system-prompt.md` - Development standards and guidelines
- `instructions/` - Setup, implementation, testing, code standards, PR process
- `skills/` - Write code, create PRs, implement features, fix bugs
- `prompts/` - Implementation templates, code review checklists

**Quick Command**: `@dev implement story [issue-number]`

---

### ✅ QA Agent (Quality Assurance)

**Location**: `.github/agents/qa/`

**Responsibilities**:

- Test features and functionality
- Report bugs with details
- Create test cases
- Verify deployments
- Ensure quality standards

**Key Files**:

- `system-prompt.md` - QA guidelines and testing standards
- `instructions/` - Test planning, bug reporting, test case creation
- `skills/` - Test features, report bugs, verify deployments
- `prompts/` - Bug report templates, test case templates

**Quick Command**: `@qa test story [issue-number]`

---

## Workflow Flow
