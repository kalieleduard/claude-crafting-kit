 ---
name: prd
description: Specialist in creating PRDs, focused on producing clear and actionable requirement documents for development and product teams. **ALWAYS use when creating product requirements, writing PRDs, defining features, or documenting product specifications.** Use proactively to ensure complete requirements capture, structured workflows, and standardized documentation. Examples - "create PRD", "write requirements", "document feature", "define product specs", "create product requirements document".
---

<params>
- $FEATURE_NAME: --feature-name
- $FEATURE_FOLDER: @tasks/prd-$FEATURE_NAME
- $PRD_PATH: $FEATURE_FOLDER/prd.md
- $TEMPLATE_PATH: @template.md
- $ARGUMENTS: CLI arguments placeholder used in output paths
</params>

<critical_validations>
- Workflow Adherence: Follow the mandatory 5-step workflow (Clarify → Plan → Draft → Create Directory → Report) in sequence; never skip steps.
- Template Usage: Always use the standardized template from `$TEMPLATE_PATH`; maintain focus on WHAT and WHY, not HOW.
- File Structure: Create directory `$FEATURE_FOLDER` and save PRD as `$PRD_PATH` using kebab-case naming convention.
- Quality Standards: Include numbered functional requirements, address all clarifying questions, and keep document around ~1,000 words.
</critical_validations>

## Objectives

1. Capture complete, clear, and testable requirements focused on users and business outcomes
2. Follow the structured workflow before creating any PRD
3. Generate a PRD using the standardized template and save it in the correct location

## Template Reference

* Source template: `$TEMPLATE_PATH`
* Final file name: `prd.md`
* Final directory: `$FEATURE_FOLDER` (name in kebab-case)
* Final file path: `$PRD_PATH`

## Workflow

When invoked with a feature request, follow this sequence:

### 1. Clarify (Mandatory)

Ask questions to understand:

* Problem to solve
* Core functionality
* Constraints
* What is NOT in scope

### 2. Plan (Mandatory)

Create a PRD development plan including:

* Section-by-section approach
* Areas requiring research
* Assumptions and dependencies

### 3. Draft the PRD (Mandatory)

* Use the template `$TEMPLATE_PATH`
* Focus on WHAT and WHY, not HOW
* Include numbered functional requirements
* Keep the main document around ~1,000 words

### 4. Create Directory and Save (Mandatory)

* Create the directory: `$FEATURE_FOLDER`
* Save the PRD in: `$PRD_PATH`

### 5. Report Results

* Provide the path to the final file
* Summary of decisions made
* Open questions

## Critical Validations - Detailed Rules

### Workflow Adherence

**MANDATORY:** Follow the complete 5-step workflow in strict sequence. Each step builds upon the previous one and cannot be skipped or reordered.

**Rules:**
- **Step 1 (Clarify) is mandatory** - Never proceed to planning without asking clarifying questions first
- **Step 2 (Plan) is mandatory** - Always create a detailed plan before drafting
- **Step 3 (Draft) is mandatory** - Use the template and follow the plan
- **Step 4 (Create Directory) is mandatory** - Always create the proper directory structure
- **Step 5 (Report) is required** - Provide summary, path, and open questions

**Violations to avoid:**
- ❌ Starting to draft without clarifying questions
- ❌ Skipping the planning phase
- ❌ Creating PRD without using the template
- ❌ Saving files in incorrect locations

### Template Usage

**MANDATORY:** Always use the standardized template from `$TEMPLATE_PATH`. The template ensures consistency and completeness.

**Rules:**
- Load and use the exact template structure from `$TEMPLATE_PATH`
- Fill all required sections completely
- Maintain focus on **WHAT** (requirements) and **WHY** (rationale), not **HOW** (implementation)
- Do not modify the template structure; adapt content to fit sections
- Ensure all template placeholders are replaced with actual content

**Content Guidelines:**
- Requirements should be testable and measurable
- Avoid technical implementation details
- Focus on user outcomes and business value
- Include acceptance criteria for each requirement

### File Structure

**MANDATORY:** Create the correct directory structure and use proper naming conventions.

**Rules:**
- Directory name format: `$FEATURE_FOLDER` where feature name is in kebab-case
- File name: Always `prd.md` (lowercase, no variations)
- Feature names must be descriptive and use kebab-case (e.g., `user-authentication`, `payment-processing`)
- Create the directory before saving the file
- Ensure the full path is `$PRD_PATH`

**Examples:**
- ✅ `$FEATURE_FOLDER` resolves to `@tasks/prd-user-authentication/prd.md` when `$FEATURE_NAME=user-authentication`
- ✅ `$FEATURE_FOLDER` resolves to `@tasks/prd-payment-gateway-integration/prd.md` when `$FEATURE_NAME=payment-gateway-integration`
- ❌ Wrong: using underscores or wrong case in feature name
- ❌ Wrong: using `PRD.md` instead of `prd.md`

### Quality Standards

**MANDATORY:** Meet all quality criteria before finalizing the PRD.

**Rules:**
- **Numbered functional requirements:** Each functional requirement must have a unique number (FR-1, FR-2, etc.)
- **Complete clarifying questions:** All questions from the checklist must be addressed
- **Document length:** Keep main document around ~1,000 words (exclude template boilerplate)
- **Assumptions and risks:** Must be explicitly listed
- **Measurable statements:** Requirements must be testable and verifiable
- **Accessibility:** Always consider accessibility and inclusion requirements

**Quality Checklist Enforcement:**
- Verify all checklist items are completed before reporting results
- Ensure functional requirements are numbered sequentially
- Confirm assumptions and risks are documented
- Validate that the file is saved in the correct location

## Related Skills

→ Use `product-specialist` skill for product strategy, market analysis, and user research when gathering context for PRD creation (informational)

→ Use `engineering` skills for technical feasibility assessment and implementation constraints when defining technical requirements

## Core Principles

* Clarify before planning; plan before drafting
* Minimize ambiguities; prefer measurable statements
* PRD defines outcomes and constraints, not implementation
* Always consider accessibility and inclusion

## Clarifying Questions Checklist

* **Problem and Goals**: what problem to solve, measurable objectives
* **Users and Stories**: main users, user stories, key flows
* **Core Functionality**: data inputs/outputs, actions
* **Scope and Planning**: what is excluded, dependencies
* **Risks and Uncertainties**: major risks, research items, blockers
* **Design and Experience**: UI guidelines, accessibility, UX integration

## Quality Checklist

* [ ] Complete clarifying questions asked and answered
* [ ] Detailed plan created
* [ ] PRD generated using the template `$TEMPLATE_PATH`
* [ ] Numbered functional requirements included
* [ ] File saved in `$PRD_PATH`
* [ ] Assumptions and risks listed
* [ ] Final path provided

## Output Protocol

In the final message:

1. Summary of decisions and approved plan
2. Full PRD content in Markdown
3. Path where the PRD was saved
4. Open questions for stakeholders

---

## ⚠️ Critical Reminder

**Always follow the `<critical_validations>` section at the top of this document. These rules are mandatory and must be enforced in all PRD creation workflows.**
