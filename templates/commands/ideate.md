---
description: Transform a high-level software idea into a structured idea document, or refine an existing PRD through iteration.
handoffs:
  - label: Create Specification
    agent: speckit.specify
    prompt: Create a detailed specification from this idea. The feature is...
  - label: Clarify Idea
    agent: speckit.clarify
    prompt: Help clarify and refine this idea further
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Outline

The text the user typed after `/speckit.ideate` can be either:
1. **A high-level idea description** → Creates a new PRD (Create Mode)
2. **A path to an existing PRD file** → Refines the existing PRD (Refine Mode)
3. **A path to a PRD file followed by refinement instructions** → Refines with specific guidance

### Determine the Mode

First, analyze the input to determine which mode to use:

- **Refine Mode**: Input starts with a file path (contains `/ideas/` or ends with `_PRD.md` or `.md`)
  - Examples:
    - `/speckit.ideate ideas/water-tracker/water-tracker_PRD.md`
    - `/speckit.ideate ideas/my-idea/my-idea_PRD.md add more detail to the key features`
    - `/speckit.ideate ideas/my-idea/my-idea_PRD.md focus on enterprise users`

- **Create Mode**: Input is a description of a new idea (no file path detected)
  - Examples:
    - `/speckit.ideate An app that helps people track their daily water intake`
    - `/speckit.ideate Tool to automatically organize photos by location`

---

## Create Mode: New Idea

If the input is a new idea description (no file path), follow these steps:

1. **Validate input**:
   - If no idea description is provided: ERROR "No idea description provided. Please provide a high-level description of your software idea, or a path to an existing PRD to refine."
   - The idea can be as brief as a sentence or as detailed as a paragraph

2. **Extract an idea title**:
   - Analyze the idea description and extract the most meaningful keywords
   - Create a concise title (2-5 words) that captures the essence of the idea
   - Use noun phrases or action-noun format (e.g., "task-automation-tool", "real-time-collaboration", "smart-expense-tracker")
   - Convert to kebab-case (lowercase with hyphens)
   - Examples:
     - "I want to build an app that helps people track their daily water intake" → "water-intake-tracker"
     - "A platform for freelancers to manage invoices and clients" → "freelancer-invoice-platform"
     - "Tool to automatically organize photos by location and date" → "smart-photo-organizer"

3. **Create the ideas folder structure**:

   a. Check if the `ideas/` folder exists in the project root:
      ```bash
      ls -la ideas/ 2>/dev/null || echo "IDEAS_FOLDER_NOT_FOUND"
      ```

   b. If the folder does not exist, create it:
      ```bash
      mkdir -p ideas/
      ```

   c. Create a subfolder with the idea title:
      ```bash
      mkdir -p ideas/<idea-title>/
      ```

   d. The final structure should be:
      ```
      ideas/
      └── <idea-title>/
          └── <idea-title>_PRD.md
      ```

4. **Load the idea template**:
   - Read `templates/idea-template.md` to understand the required sections
   - This template provides the structure for capturing the idea

5. **Generate the idea document**:

   Follow this execution flow:

   a. Parse the user's idea description from Input

   b. Extract key concepts from the description:
      - Identify: the core problem, proposed solution, target users, key features
      - Look for: pain points, value propositions, differentiators

   c. Fill in the template sections:
      - **Problem Statement**: What problem does this solve? Be specific about pain points
      - **Proposed Solution**: High-level description of how the idea addresses the problem
      - **Target Users**: Who would use this? Primary and secondary users
      - **Key Features**: 3-5 essential features that make this valuable
      - **Value Proposition**: Why would someone use this? User benefits and business value
      - **Initial Assumptions**: What are we assuming to be true?
      - **Open Questions**: What needs to be answered before moving to specification?
      - **Risks & Challenges**: What could make this fail?
      - **Success Indicators**: How would we know if this is successful?

   d. For unclear aspects:
      - Make informed guesses based on context and industry patterns
      - Document assumptions explicitly in the "Initial Assumptions" section
      - Add important unknowns to "Open Questions" for later clarification
      - Do NOT use [NEEDS CLARIFICATION] markers - this is ideation, not specification

6. **Write the idea document**:
   - Write to `ideas/<idea-title>/<idea-title>_PRD.md`
   - Replace all placeholders with concrete content derived from the idea description
   - Set `[DATE]` to today's date in ISO format (YYYY-MM-DD)
   - Set `[IDEA_TITLE]` to the extracted title in Title Case

7. **Report completion**:

   Provide a summary to the user with:
   - The idea title and folder path
   - A brief recap of the captured idea (2-3 sentences)
   - Key open questions that should be addressed
   - Suggested next steps:
     - Review and refine the idea document
     - Validate assumptions with potential users
     - When ready, use `/speckit.specify` to create a detailed specification

---

## Refine Mode: Iterate on Existing PRD

If the input contains a file path to an existing PRD, follow these steps:

1. **Parse the input**:
   - Extract the file path (first argument that looks like a path)
   - Extract any refinement instructions (remaining text after the path)
   - If no refinement instructions provided, enter interactive refinement mode

2. **Load the existing PRD**:
   - Read the file at the specified path
   - If file doesn't exist: ERROR "PRD file not found at: <path>. Please check the path and try again."
   - Parse the current content and understand each section

3. **Analyze the current state**:
   - Identify sections that are well-developed vs sparse
   - Note any placeholder text still remaining
   - Check open questions that could be resolved
   - Assess overall completeness and clarity

4. **Determine refinement approach**:

   **If refinement instructions were provided**:
   - Focus on the specific areas mentioned by the user
   - Examples:
     - "add more detail to key features" → Expand the Key Features section
     - "focus on enterprise users" → Adjust Target Users and Value Proposition
     - "add security considerations" → Add to Risks & Challenges and possibly Key Features
     - "resolve open questions" → Attempt to answer Open Questions based on context

   **If no instructions provided (interactive mode)**:
   - Present a summary of the current PRD state
   - Suggest 3-5 specific improvements that could be made:
     - Sections that need more detail
     - Open questions that could be explored
     - Assumptions that should be validated
     - Missing considerations (security, scalability, accessibility, etc.)
   - Ask the user which improvements they'd like to focus on

5. **Apply refinements**:
   - Update the relevant sections while preserving the overall structure
   - Maintain consistency with existing content
   - Add new insights without removing valuable existing content
   - Update the `**Status**` field if appropriate (e.g., "Draft" → "Refined")
   - Do NOT change the `**Created**` date; add a `**Last Refined**` date instead

6. **Write the updated PRD**:
   - Overwrite the existing file with the refined content
   - Preserve the same file path and name

7. **Report refinement completion**:

   Provide a summary to the user with:
   - What was refined/improved
   - Key changes made
   - Remaining open questions or areas for future refinement
   - Suggested next steps:
     - Continue refining with another `/speckit.ideate <path>` call
     - Move to specification with `/speckit.specify` if ready

---

## Guidelines

### Ideation vs Specification

This command is for **early-stage ideation**, not detailed specification:
- Keep descriptions high-level and conceptual
- Focus on the "what" and "why", not the "how"
- Avoid technical implementation details
- Embrace uncertainty - capture questions rather than forcing answers
- This is a thinking tool, not a commitment

### Quality Principles

- **Clarity over completeness**: A clear partial idea is better than a vague complete one
- **Questions are valuable**: Good questions guide better specifications later
- **Assumptions are explicit**: State what you're assuming, don't hide it
- **Users first**: Always ground the idea in user problems and benefits

### Refinement Best Practices

- **Iterative improvement**: Each refinement pass should meaningfully improve the PRD
- **Preserve intent**: Don't fundamentally change the idea unless explicitly asked
- **Add, don't just replace**: Build on existing content rather than rewriting from scratch
- **Track evolution**: The refinement history shows how the idea matured

### When to Use This Command

Use `/speckit.ideate` when:
- You have a new software idea you want to capture (Create Mode)
- You want to iterate and improve an existing idea PRD (Refine Mode)
- You want to think through a concept before committing to build it
- You're exploring multiple ideas and want to document them
- You need a structured format to share an idea with others

After ideation, use `/speckit.specify` to create a detailed feature specification when you're ready to move forward with the idea.

---

## Examples

### Create a new idea:
```
/speckit.ideate A mobile app that gamifies personal finance by turning savings goals into RPG quests
```

### Refine an existing PRD (interactive):
```
/speckit.ideate ideas/savings-quest-app/savings-quest-app_PRD.md
```

### Refine with specific instructions:
```
/speckit.ideate ideas/savings-quest-app/savings-quest-app_PRD.md expand the key features to include social elements

/speckit.ideate ideas/savings-quest-app/savings-quest-app_PRD.md focus on the monetization strategy and add it as a new section

/speckit.ideate ideas/savings-quest-app/savings-quest-app_PRD.md resolve the open questions about target demographics
```
