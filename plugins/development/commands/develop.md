# /develop

Execute work on an existing Linear issue and its sub-issues, then create a GitHub pull request.

## Usage

```
/develop <linear-issue-key>
```

## Arguments

- `<linear-issue-key>`: The Linear issue identifier (e.g., `SKE-123` or the full UUID)

---

# System Instructions

**IMPORTANT**: Work through ALL sub-issues without stopping. If you run into any issues with Linear MCP, attempt to send the request without special characters. You may retry the MCP tool call up to 5 times. If you continue to run into failure with Linear, fully stop and wait for the user.

## Step 1: Fetch the Parent Issue

Use the Linear MCP `get_issue` tool to retrieve the issue:

```
id: "<linear-issue-key>"
```

Read and understand:
- The issue title and description
- Any existing labels and project context
- Note the `<PARENT-ISSUE-ID>` and `<PARENT-ISSUE-KEY>` for querying sub-issues

## Step 2: Prepare Working Environment

### Step 2a: Checkout and Update Main

```bash
git checkout main
git pull origin main
```

### Step 2b: Create Git Worktree Branch

Create a new worktree for this work:

```bash
git worktree add ../<PARENT-ISSUE-KEY> -b <PARENT-ISSUE-KEY>
cd ../<PARENT-ISSUE-KEY>
```

Example: 
```bash
git worktree add ../SKE-42 -b SKE-42
cd ../SKE-42
```

**All subsequent work should be done in this worktree directory.**

## Step 3: Fetch All Sub-Issues

Use the Linear MCP `list_issues` tool to get all sub-issues:

```
parentId: "<PARENT-ISSUE-ID>"
orderBy: "createdAt"
```

If no sub-issues exist, work on the parent issue directly (skip to Step 4b).

## Step 4: Execute the Work Loop

**LOOP: For each sub-issue in order (oldest first), perform the following:**

### Step 4a: Complete the Task

1. Read the issue's "Description" field as your coding instructions
2. Implement the required changes
3. Verify the implementation works as expected

### Step 4b: Run Refresh and Fix Any Issues

Run the full validation suite:

```bash
pnpm run refresh
```

This runs stricter linting, builds, and database migrations.

**If `pnpm run refresh` fails:**
1. Analyze the error output
2. Make code changes to fix the issues
3. Run `pnpm run refresh` again
4. Repeat until all checks pass
5. All fixes will be included in the commit

### Step 4c: Commit Changes

Commit all changed and untracked files (including any fixes from Step 4b):

```bash
git add .
git commit -m "completes <SUB-ISSUE-KEY> part of <PARENT-ISSUE-KEY> <short-message-describing-changes>"
```

Example: `completes SKE-43 part of SKE-42 Add OAuth2 Google provider routes`

### Step 4d: Continue to Next Sub-Issue

Proceed to the next sub-issue in the list. Repeat Steps 4a-4c.

**END LOOP when all sub-issues are complete.**

## Step 5: Push Branch and Create Pull Request

**Only after ALL sub-issues are complete:**

### Step 5a: Push to Origin

```bash
git push -u origin <PARENT-ISSUE-KEY>
```

### Step 5b: Create GitHub Pull Request

Use the GitHub CLI to create a PR linked to the Linear issue:

```bash
gh pr create \
  --title "<PARENT-ISSUE-KEY>: <parent-issue-title>" \
  --body "## Linear Issue

Closes <PARENT-ISSUE-KEY>
https://linear.app/team/<PARENT-ISSUE-KEY>

## Summary

<brief summary of all changes made>

## Changes

<list of sub-issues completed with brief descriptions>

## Testing

- [ ] \`pnpm run refresh\` passes
- [ ] Manual testing completed" \
  --base main
```

Note the returned `<PR-URL>`.

### Step 5c: Link PR to Linear Issue

Use the Linear MCP `update_issue` tool to add the PR link:

```
id: "<PARENT-ISSUE-ID>"
links: [{ "url": "<PR-URL>", "title": "GitHub Pull Request" }]
```

### Step 5d: Cleanup Worktree

After the PR is successfully created, clean up the worktree:

```bash
cd ../<original-repo-directory>
git checkout main
git worktree remove ../<PARENT-ISSUE-KEY>
git branch -D <PARENT-ISSUE-KEY>
git pull origin main
```

Example:
```bash
cd ../sketchy
git checkout main
git worktree remove ../SKE-42
git branch -D SKE-42
git pull origin main
```

## Step 6: Output Summary

```
✅ Completed issue: <PARENT-ISSUE-KEY>
   
📋 Sub-issues completed:
   ✓ <SUB-ISSUE-1-KEY>: <title>
   ✓ <SUB-ISSUE-2-KEY>: <title>
   ...

📝 Commits:
   - <commit-hash>: <message>
   - <commit-hash>: <message>
   ...

🔗 Pull Request: <PR-URL>

Note: Parent and sub-issues will be automatically closed when the PR is merged.
```

---

## Error Handling

### If a sub-issue fails:

1. Update the issue state to "Blocked" or add a blocking label
2. Add a comment explaining the blocker:
   ```
   issueId: "<SUB-ISSUE-ID>"
   body: |
     ❌ Blocked: <description of the issue>
     
     ## Error Details
     <error message or explanation>
     
     ## Attempted Solutions
     <what was tried>
   ```
3. Stop and wait for user intervention

### If `pnpm run refresh` fails repeatedly:

1. Make up to 3 attempts to fix the issues automatically
2. If still failing after 3 attempts, follow the "sub-issue fails" procedure above
3. Include all error output in the blocking comment

### If GitHub PR creation fails:

1. Ensure `gh` CLI is authenticated (`gh auth status`)
2. Retry the PR creation
3. If still failing, output the branch name and manual PR instructions

---

## Example

```
/develop SKE-42
```

This would:
1. Fetch issue SKE-42 and its sub-issues
2. Checkout main and pull latest
3. Create worktree `../SKE-42` with branch `SKE-42`
4. Work through each sub-issue in order (in the worktree)
5. Run `pnpm run refresh` after each task (fixing any issues)
6. Commit changes with proper message format
7. Push branch and create GitHub PR linked to Linear
8. Cleanup: checkout main, remove worktree, delete local branch, pull main
9. Output a summary with PR link