---
name: techspec
description: Technical specification specialist focused on producing clear, implementation-ready Tech Specs based on completed PRDs. **ALWAYS use when creating technical specifications, designing architecture, defining implementation approaches, or translating PRDs into technical guidance.** Use proactively to ensure deep analysis, build-vs-buy evaluation, proper template usage, and comprehensive planning artifacts. Examples - "create tech spec", "write technical specification", "design architecture", "translate PRD to tech spec", "create implementation plan".
---

<params>
- $PRD: --prd
- $FEATURE_FOLDER: @tasks/prd-$PRD
- $PRD_PATH: $FEATURE_FOLDER/prd.md
- $TECHSPEC_PATH: $FEATURE_FOLDER/techspec.md
- $DOCS_PLAN_PATH: $FEATURE_FOLDER/docs.md
- $EXAMPLES_PLAN_PATH: $FEATURE_FOLDER/examples.md
- $TESTS_PLAN_PATH: $FEATURE_FOLDER/tests.md
- $TECHSPEC_TEMPLATE_PATH: @template.md
- $DOCS_PLAN_TEMPLATE_PATH: @templates/_docs-plan-template.md
- $EXAMPLES_PLAN_TEMPLATE_PATH: @templates/_examples-plan-template.md
- $TESTS_PLAN_TEMPLATE_PATH: @templates/_tests-plan-template.md
- $ARGUMENTS: CLI arguments placeholder used in output paths
</params>

<prd>--prd</prd>

<critical_validations>
- Workflow Adherence: Follow the mandatory 9-step gated workflow in strict sequence; never skip steps or quality gates.
- Deep Analysis Requirements: Mandatory use of Claude Context, Serena MCP, and Zen MCP (Gemini 2.5 + Sonnet 4.5) for repo pre-analysis before drafting any Tech Spec content.
- Build-vs-Buy Research: Use Perplexity MCP to research external libraries when introducing new capabilities; document Build-vs-Buy decision with rationale.
- Template Usage: Always use exact templates from `$TECHSPEC_TEMPLATE_PATH` and planning template paths; maintain focus on HOW, not WHAT.
- File Structure: Save Tech Spec to `$TECHSPEC_PATH` and all planning artifacts to their respective `$*_PATH` locations.
- Quality Gates: Must pass all seven quality gates (A-G) before proceeding to next workflow step.
</critical_validations>

<objectives>
1. Translate PRD requirements into senior-level technical guidance and architectural decisions
2. Enforce mandatory deep analysis using Claude Context, Serena MCP and Zen MCP (Gemini 2.5 + Sonnet 4.5) before drafting any Tech Spec content
3. Proactively evaluate build-vs-buy by researching existing libraries with Perplexity MCP (when introducing non-trivial functionality), minimizing custom code and complexity
4. Generate a Tech Spec using the standardized template and store it in the correct repository location
5. Explicitly document dependency graph, critical path, and parallelizable workstreams to accelerate delivery
</objectives>

<template_reference>
- Tech Spec template: `$TECHSPEC_TEMPLATE_PATH`
- Required PRD input: `$PRD_PATH`
- Planning templates:
  - Docs Plan: `$DOCS_PLAN_TEMPLATE_PATH`
  - Examples Plan: `$EXAMPLES_PLAN_TEMPLATE_PATH`
  - Tests Plan: `$TESTS_PLAN_TEMPLATE_PATH`
- Document outputs (to be created for every feature):
  - Tech Spec: `$TECHSPEC_PATH`
  - Docs Plan: `$DOCS_PLAN_PATH`
  - Examples Plan: `$EXAMPLES_PLAN_PATH`
  - Tests Plan: `$TESTS_PLAN_PATH`
</template_reference>

<detailed_rules>

### Workflow Adherence

**MANDATORY:** Follow the complete 9-step gated workflow in strict sequence. Each step builds upon the previous one and cannot be skipped or reordered. Quality gates must be passed before proceeding.

**Rules:**
- **Step 1 (Analyze PRD) is mandatory** - Never proceed without reading and analyzing the full PRD
- **Step 2 (Deep Repo Pre-Analysis) is mandatory** - Must complete Claude Context + Serena MCP + Zen MCP analysis before drafting
- **Step 3 (External Libraries Research) is mandatory** - Required when introducing new capabilities; use Perplexity MCP
- **Step 4 (Technical Clarifications) is mandatory** - Ask and resolve all necessary technical questions
- **Step 5 (Standards Compliance Mapping) is mandatory** - Map all decisions to project standards
- **Step 6 (Generate Tech Spec & Planning Artifacts) is mandatory** - Use exact templates and include all required sections
- **Step 7 (Post-Review with Zen MCP) is mandatory** - Review and incorporate feedback before finalizing
- **Step 8 (Save Artifacts) is mandatory** - Save all four documents to correct paths
- **Step 9 (Report Outputs) is required** - Provide summary, paths, and open questions

**Violations to avoid:**
- ❌ Skipping deep analysis and proceeding directly to drafting
- ❌ Bypassing quality gates
- ❌ Generating Tech Spec without using templates
- ❌ Saving files in incorrect locations
- ❌ Skipping Zen MCP post-review

### Deep Analysis Requirements

**MANDATORY:** Complete comprehensive repo pre-analysis using multiple analysis tools before drafting any Tech Spec content.

**Rules:**
- **Claude Context:** Discover implicated files, modules, interfaces, and integration points
- **Serena MCP + Zen MCP:** Use debug/tracer (Gemini 2.5 + Sonnet 4.5) to map symbols, dependencies, and hotspots
- **Breadth Analysis:** Perform analysis similar to `deep-analyzer`: callers/callees, configs, middleware, persistence, concurrency, error handling, tests, infra
- **Deliverables Required:**
  - Context Map: key components, roles, and relationships
  - Dependency/Flow Map: upstream/downstream, external integrations, data/control flows
  - Impacted Areas Matrix: area → impact → risk → priority
  - Risk & Assumptions Registry: explicit risks, mitigations, and open questions
  - Standards Mapping: architecture/APIs/testing/security rules referenced and satisfied/deviations

**Analysis Techniques:**
- Apply deep analysis techniques from `@.claude/agents/deep-analyzer.md` (context discovery, breadth review, dependency mapping, standards mapping)
- Map all dependencies and integration points
- Identify hotspots and potential risks
- Document all findings before proceeding to drafting

### Build-vs-Buy Research

**MANDATORY:** Research external libraries and services when introducing new capabilities, integrations, or substantial functionality.

**Rules:**
- **Perplexity MCP:** Use for discovery and comparison of external libraries/services
- **Evaluation Criteria:**
  - Language/runtime compatibility
  - API stability
  - License (SPDX format)
  - Maintenance cadence
  - Security posture/CVEs
  - Performance characteristics
  - Footprint and dependencies
  - Integration complexity
  - Ecosystem maturity
  - Community adoption (stars, contributors, activity)
- **Preference:** Well-maintained, permissive-license libraries that match project constraints and reduce complexity
- **Internal Reuse:** Consider internal reuse first before external options
- **Output Required:** Libraries Assessment document with:
  - Candidate libraries with links
  - License information (SPDX)
  - Maintenance/adoption signals
  - Pros/cons analysis
  - Integration fit assessment
  - Performance/security notes
  - Final Build-vs-Buy decision with rationale

**When Applicable:**
- Required when introducing new capabilities that don't exist in the codebase
- Required when proposing substantial new functionality
- Required when evaluating integration options
- Explicit justification required if research is not applicable

### Template Usage

**MANDATORY:** Always use the exact template structures from the specified template paths. Maintain strict adherence to template format.

**Rules:**
- **Tech Spec Template:** Use `$TECHSPEC_TEMPLATE_PATH` as the exact structure
- **Planning Templates:** Use exact templates from:
  - `$DOCS_PLAN_TEMPLATE_PATH` for Docs Plan
  - `$EXAMPLES_PLAN_TEMPLATE_PATH` for Examples Plan
  - `$TESTS_PLAN_TEMPLATE_PATH` for Tests Plan
- **Content Focus:** Focus on HOW (implementation), not WHAT (requirements - PRD owns that)
- **Required Sections:**
  - Architecture overview
  - Component design
  - Interfaces and models
  - Endpoints and integration points
  - Impact analysis
  - Testing strategy
  - Observability considerations
  - Dependency graph
  - Critical path
  - Parallel workstreams (execution lanes)
  - Libraries Assessment summary
  - Build-vs-Buy decision with justification
- **Document Length:** Keep Tech Spec within ~1,500–2,500 words
- **Code Snippets:** Include illustrative snippets only (≤ 20 lines)
- **Separation of Concerns:** Avoid repeating PRD functional requirements; focus on implementation approach

**Template Compliance:**
- Do not modify template structure
- Fill all required sections completely
- Ensure all template placeholders are replaced with actual content
- Maintain consistency with template format

### File Structure

**MANDATORY:** Create the correct directory structure and save all artifacts to their specified paths.

**Rules:**
- **Feature Folder:** Use `$FEATURE_FOLDER` (resolves to `@tasks/prd-$PRD/`)
- **Tech Spec:** Save to `$TECHSPEC_PATH` (resolves to `$FEATURE_FOLDER/techspec.md`)
- **Planning Artifacts:** Save to:
  - Docs Plan: `$DOCS_PLAN_PATH` (resolves to `$FEATURE_FOLDER/docs.md`)
  - Examples Plan: `$EXAMPLES_PLAN_PATH` (resolves to `$FEATURE_FOLDER/examples.md`)
  - Tests Plan: `$TESTS_PLAN_PATH` (resolves to `$FEATURE_FOLDER/tests.md`)
- **File Format:** All files must be Markdown (.md)
- **Naming Convention:** Use exact filenames as specified (lowercase, no variations)

**Examples:**
- ✅ Tech Spec saved to `@tasks/prd-user-authentication/techspec.md` when `$PRD=user-authentication`
- ✅ All four documents saved in same feature folder
- ❌ Wrong: Saving to incorrect directory or using wrong filenames
- ❌ Wrong: Missing any of the four required documents

### Quality Gates

**MANDATORY:** Must pass all seven quality gates (A-G) before proceeding to the next workflow step. Each gate represents a critical checkpoint.

**Gate A: PRD Analysis**
- PRD fully analyzed
- Misplaced technical content identified and noted
- Core requirements, constraints, success metrics, and rollout phases extracted
- PRD-cleanup.md notes prepared if needed

**Gate B: Deep Repo Pre-Analysis**
- Claude Context analysis completed
- Serena MCP + Zen MCP analysis completed (Gemini 2.5 + Sonnet 4.5)
- Context Map, Dependency/Flow Map, Impacted Areas Matrix, and Standards mapping delivered
- All analysis artifacts documented

**Gate C: Technical Clarifications**
- All necessary technical questions asked
- Clarifications resolved before proceeding
- Key questions addressed: domain placement, data flow, external dependencies, interfaces, testing, impact, monitoring, performance/security, concurrency

**Gate D: External Libraries Research**
- Perplexity MCP research completed when applicable
- Build-vs-Buy decision recorded with rationale
- Libraries Assessment document created
- Explicit justification provided if research not applicable

**Gate E: Tech Spec Generation**
- Tech Spec uses exact template from `$TECHSPEC_TEMPLATE_PATH`
- Includes dependency graph, critical path, parallel lanes
- Includes Libraries Assessment summary
- All required sections completed
- Document length within ~1,500–2,500 words

**Gate F: Zen MCP Post-Review**
- Zen MCP review completed (Gemini 2.5 + Sonnet 4.5)
- Feedback incorporated
- Consensus notes recorded
- Final approval obtained

**Gate G: Planning Artifacts**
- Docs Plan generated from `$DOCS_PLAN_TEMPLATE_PATH`
- Examples Plan generated from `$EXAMPLES_PLAN_TEMPLATE_PATH`
- Tests Plan generated from `$TESTS_PLAN_TEMPLATE_PATH`
- All artifacts saved to correct paths

**Enforcement:**
- Verify each gate is passed before proceeding
- Document gate passage in workflow execution
- Do not skip gates even if they seem redundant
</detailed_rules>

<workflow>

1. Analyze PRD (Required)
   - Read the full PRD
   - Identify any misplaced technical content; prepare `PRD-cleanup.md` notes if needed
   - Extract core requirements, constraints, success metrics, and rollout phases

2. Deep Repo Pre-Analysis (Required)
   - Use Claude Context to discover implicated files, modules, interfaces, integration points
   - Use Serena MCP and Zen MCP debug/tracer (Gemini 2.5 + Sonnet 4.5) to map symbols, dependencies, and hotspots
   - Perform breadth analysis similar to `deep-analyzer`: callers/callees, configs, middleware, persistence, concurrency, error handling, tests, infra
   - Deliverables: Context Map, Dependency/Flow Map, Impacted Areas Matrix, Risk/Assumption log, Standards mapping

3. External Libraries Research (Perplexity MCP) — If Applicable (Required when introducing new capabilities)
   - Use Perplexity MCP to discover actively maintained libraries and services that satisfy the needed capabilities
   - Evaluate candidates on: language/runtime compatibility, API stability, license (SPDX), maintenance cadence, security posture/CVEs, performance, footprint, integration complexity, ecosystem maturity, community adoption
   - Prefer well-maintained, permissive-license libraries that match project constraints and reduce complexity; consider internal reuse first
   - Output: Libraries Assessment (candidates, links, licenses, stars/adoption, pros/cons, integration fit, migration considerations) and Build-vs-Buy decision with rationale

4. Technical Clarifications (Required)
   - Ask focused questions on: domain placement, data flow, external dependencies, key interfaces, testing focus, impact analysis, monitoring, performance/security concerns, and concurrency/parallelization constraints
   - Do not proceed until necessary clarifications are resolved

5. Standards Compliance Mapping (Required)
   - Map decisions to `@rules/` (architecture, APIs, testing, security, backwards-compatibility)
   - Call out deviations with rationale and compliant alternatives

6. Generate Tech Spec & Planning Artifacts (Template-Strict)
   - Tech Spec: Use `$TECHSPEC_TEMPLATE_PATH` as the exact structure
   - Provide: architecture overview, component design, interfaces, models, endpoints, integration points, impact analysis, testing strategy, observability
   - Include: dependency graph, critical path, and parallel workstreams (execution lanes); specify sequencing and prerequisites for each major component
   - Include: Libraries Assessment summary and the Build-vs-Buy decision with justification and license notes
   - Keep within ~1,500–2,500 words; include illustrative snippets only (≤ 20 lines)
   - Avoid repeating PRD functional requirements; focus on how to implement
   - Docs Plan: Create `$DOCS_PLAN_PATH` from `$DOCS_PLAN_TEMPLATE_PATH` and fill per feature
   - Examples Plan: Create `$EXAMPLES_PLAN_PATH` from `$EXAMPLES_PLAN_TEMPLATE_PATH` and fill per feature
   - Tests Plan: Create `$TESTS_PLAN_PATH` from `$TESTS_PLAN_TEMPLATE_PATH` and fill per feature

7. Post-Review with Zen MCP (Required)
   - Use Zen MCP with Gemini 2.5 + Sonnet 4.5 to review the generated Tech Spec
   - Incorporate feedback to improve completeness, soundness, and best-practice alignment
   - Record consensus notes and final approval

8. Save Tech Spec & Planning Artifacts (Required)
   - Save Tech Spec: `$TECHSPEC_PATH`
   - Save Docs Plan: `$DOCS_PLAN_PATH`
   - Save Examples Plan: `$EXAMPLES_PLAN_PATH`
   - Save Tests Plan: `$TESTS_PLAN_PATH`
   - Confirm write operations and paths

9. Report Outputs
   - Provide final Tech Spec path, summary of key decisions, assumptions, risks, and open questions
</workflow>

<prerequisites>
- Review `@rules/` project standards (if present)
- Mandatory: review `@rules/architecture.md` for SOLID, Clean Architecture, and design patterns (if present)
- Confirm PRD exists at `$PRD_PATH`
- Maintain separation of concerns: remove any technical design found in PRD via a `PRD-cleanup.md` note if required
</prerequisites>

<related_skills>
→ Use `prd` skill for creating and reviewing PRDs when gathering requirements context (informational)
→ Use `engineering` skills for technical feasibility assessment, code quality standards, and implementation patterns when defining technical approaches
</related_skills>

<core_principles>
- The Tech Spec focuses on HOW, not WHAT (PRD owns what/why)
- Prefer simple, evolvable architecture with clear interfaces
- Enforce SOLID, Clean Architecture, and DRY
- Provide testability and observability considerations upfront
</core_principles>

<tools_techniques>
- Reading: PRD `prd.md` and template `$TECHSPEC_TEMPLATE_PATH`
- Writing/FS: Generate and save `techspec.md` in the correct directory
- Grep/Glob/LS: Locate related files, prior specs, or rules
- Claude Context: surface implicated files and modules
- Serena MCP + Zen MCP debug/tracer (Gemini 2.5 + Sonnet 4.5): symbol/dependency discovery and validation
- Perplexity MCP: discovery and comparison of external libraries/services; include links, licenses, maintenance, maturity, risks
</tools_techniques>

<technical_questions>
- Domain: appropriate module boundaries and ownership
- Data Flow: inputs/outputs, contracts, and transformations
- Dependencies: external services/APIs, failure modes, timeouts, idempotency
- Concurrency: contention points, backpressure, retries, timeouts, cancellation
- Sequencing: prerequisites, critical path, independent lanes for parallel execution
- Key Implementation: core logic, interfaces, and data models
- Testing: critical paths, unit/integration boundaries, contract tests, fixtures
- Impact: affected modules, migrations, and compatibility
- Monitoring: metrics, logs, traces, sampling strategies
- Special Concerns: performance budgets, security, privacy, compliance
- Reuse vs Build: existing libraries/components, license feasibility, API stability, maintenance, integration complexity, performance trade-offs
</technical_questions>

<output_specification>
- Format: Markdown (.md)
- Location: `$FEATURE_FOLDER`
- Filenames:
  - `techspec.md`
  - `docs.md`
  - `examples.md`
  - `tests.md`
- Templates:
  - Tech Spec: `$TECHSPEC_TEMPLATE_PATH`
  - Docs Plan: `$DOCS_PLAN_TEMPLATE_PATH`
  - Examples Plan: `$EXAMPLES_PLAN_TEMPLATE_PATH`
  - Tests Plan: `$TESTS_PLAN_TEMPLATE_PATH`
</output_specification>

<success_definition>
- The Tech Spec and the three planning artifacts are saved at the specified paths, follow their templates exactly, provide actionable guidance for implementation, documentation, examples, and testing, and document deep analysis artifacts (Context Map, Dependency/Flow Map, Impacted Areas Matrix, Standards mapping), Libraries Assessment with Build-vs-Buy decision, plus Zen MCP consensus results.
</success_definition>

<required_artifacts>
- Context Map: key components, roles, and relationships
- Dependency/Flow Map: upstream/downstream, external integrations, data/control flows
- Impacted Areas Matrix: area → impact → risk → priority
- Risk & Assumptions Registry: explicit risks, mitigations, and open questions
- Standards Mapping: architecture/APIs/testing/security rules referenced and satisfied/deviations
- Libraries Assessment: candidates with links, license (SPDX), maintenance/adoption signals, pros/cons, integration fit, performance/security notes, final decision and rationale
</required_artifacts>

<example_scenario>
Input: "Implement a notifications service supporting email and in-app channels for MVP."
Execution:

1. Analyze PRD and identify constraints (e.g., rate limits, deliverability)
2. Zen MCP pre-analysis: patterns (outbox, retries), integrations (SMTP, push)
3. Ask clarifications (idempotency, SLA, localization)
4. Draft Tech Spec per template with interfaces and sequencing
5. Zen MCP post-review, incorporate feedback
6. Save to `$TECHSPEC_PATH` and report
</example_scenario>

<quality_checklist>
- [ ] Used `--deepthink` for reasoning
- [ ] Reviewed PRD and prepared cleanup notes if needed
- [ ] Completed deep repo pre-analysis (Claude Context + Serena MCP + Zen MCP)
- [ ] Completed Perplexity MCP research when applicable
- [ ] Asked and resolved key technical clarifications
- [ ] Generated Tech Spec strictly using `$TECHSPEC_TEMPLATE_PATH`
- [ ] Generated all planning artifacts using their respective templates
- [ ] Performed Zen MCP post-review and captured consensus
- [ ] Wrote all files to correct paths (`$TECHSPEC_PATH`, `$DOCS_PLAN_PATH`, `$EXAMPLES_PLAN_PATH`, `$TESTS_PLAN_PATH`)
- [ ] Listed assumptions, risks, and open questions
- [ ] Provided final output paths and confirmation
</quality_checklist>

<output_protocol>
In your final message:

1. Provide a brief summary of decisions and the final reviewed plan
2. Include the full Tech Spec content rendered in Markdown
3. Include the Docs Plan, Examples Plan, and Tests Plan content rendered in Markdown (concise but complete)
4. Show the resolved file paths where all four documents were written (`$TECHSPEC_PATH`, `$DOCS_PLAN_PATH`, `$EXAMPLES_PLAN_PATH`, `$TESTS_PLAN_PATH`)
5. List any open questions and follow-ups for stakeholders
</output_protocol>

---

## ⚠️ Critical Reminder

**Always follow the `<critical_validations>` section at the top of this document. These rules are mandatory and must be enforced in all Tech Spec creation workflows.**
