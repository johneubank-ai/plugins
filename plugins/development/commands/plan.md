# /plan

Create a new Linear issue with sub-issues for a project.

## Usage

```
/plan <project-name> <prompt>
```

## Arguments

- `<project-name>`: The Linear project key/name to add the issue to
- `<prompt>`: Description of the feature, bug fix, or task to accomplish

---

# System Instructions

**IMPORTANT**: If you run into any issues with Linear MCP, attempt to send the request without special characters. You may retry the MCP tool call up to 5 times. If you continue to run into failure with Linear, fully stop and wait for the user.

## Step 1: Analyze the User Prompt

Determine the following from the user's prompt:

- **Title**: A concise summary of the changes requested
- **Labels**: Identify which categories apply from: `ui-page`, `ui-block`, `ui-component`, `lib-interface`, `lib-domain-model`, `lib-repository`, `lib-service`, `script`, `styling`, `tailwind`, `api`, `database`, `other`
- **Project**: The `<project-name>` argument provided

## Step 2: Create the Parent Linear Issue

Use the Linear MCP `create_issue` tool with these arguments:

```
team: "<your-team-name>"
title: "<title summary>"
description: "<full user prompt>"
labels: ["<applicable-labels>"]
project: "<project-name>"
state: "Todo"
```

**Note the returned `<PARENT-ISSUE-ID>` for creating sub-issues.**

## Step 3: Plan the Implementation

Before creating sub-issues:

1. Read and analyze the codebase structure relevant to the task
2. Review any coding guidelines (e.g., `CODE_INSTRUCTIONS.md` if present)
3. Use web search if needed for technical research
4. Craft a step-by-step plan that accomplishes the requested changes

Each step should be:
- Atomic and independently completable
- Clearly scoped with specific files/components
- Ordered by dependency (foundational work first)

## Step 4: Create Sub-Issues for Each Task

For each task in your plan, use the Linear MCP `create_issue` tool:

```
team: "<your-team-name>"
title: "<brief title of the task>"
parentId: "<PARENT-ISSUE-ID>"
description: |
  ## Instructions
  <exact instructions to complete the task>
  
  ## Verification
  <how to verify it works as expected>
  
  ## Files
  <list of files to create or modify>
labels: [
  "<ui-page|ui-block|ui-component|lib-interface|lib-domain-model|lib-repository|lib-service|script|styling|tailwind|api|database|other>",
  "<new-file|existing-file>",
  "<code|unit-test|integration-test|e2e-test|other>"
]
project: "<project-name>"
state: "Todo"
```

## Step 5: Output Summary

After creating all issues, output:

```
✅ Created parent issue: <PARENT-ISSUE-ID>
   Title: <title>
   
📋 Created <N> sub-issues:
   1. <SUB-ISSUE-1-ID>: <title>
   2. <SUB-ISSUE-2-ID>: <title>
   ...

🚀 To start working on this issue, run:
   /do-issue <PARENT-ISSUE-ID>
```

---

## Example

```
/plan sketchy-auth Add OAuth2 login with Google provider
```

This would:
1. Create a parent issue titled "Add OAuth2 login with Google provider"
2. Plan implementation steps (e.g., install deps, create auth routes, add UI components)
3. Create sub-issues for each step
4. Output the issue IDs and next steps