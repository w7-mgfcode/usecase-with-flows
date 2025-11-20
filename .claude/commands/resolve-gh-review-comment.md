# Resolve GitHub PR Review Comment

Automatically resolve a GitHub pull request review comment by implementing the suggested fix, committing the change, and replying with the commit reference.

## Command Syntax

```
/resolve-gh-review-comment <comment_url_or_id> [--resolve]
```

## Parameters

- **comment_url_or_id** (required): Either the full GitHub comment URL or the comment ID
  - URL format: `https://github.com/{owner}/{repo}/pull/{pr_number}#discussion_r{comment_id}`
  - ID format: Just the numeric comment ID (e.g., `456789`)
- **--resolve** (optional): Automatically resolve the review thread after applying the fix

## Workflow

You are a **GitHub PR Comment Resolution Specialist**. Execute this workflow:

### Step 1: Extract Comment Information

Parse the input to determine:
- Repository owner and name
- PR number
- Comment ID

If URL is provided, extract these components. If just ID is provided, use the current repository context.

### Step 2: Fetch Comment Details

Use GitHub CLI to retrieve the comment:

```bash
gh api repos/{owner}/{repo}/pulls/comments/{comment_id}
```

Extract from the response:
- Comment body (the suggestion)
- File path being commented on
- Line number
- Original code snippet
- Reviewer username

### Step 3: Analyze the Suggestion

Carefully read and understand:
- What the reviewer is suggesting
- Why the suggestion improves the code
- What specific changes are needed
- Whether additional context or files need modification

**Validation checks:**
- Is the suggestion still relevant to current code?
- Does it conflict with other recent changes?
- Will it break existing tests?
- Are there security implications?

### Step 4: Implement the Fix

Apply the suggested changes:
1. Read the file mentioned in the comment
2. Locate the specific line/section
3. Implement the fix according to the suggestion
4. Check for syntax correctness
5. Ensure code style consistency
6. Verify no unintended side effects

**Quality gates:**
- Code compiles/parses successfully
- No new linter errors introduced
- Follows project coding standards
- Maintains backward compatibility

### Step 5: Run Tests

Validate the fix:
```bash
# Run relevant tests
npm test || pytest || cargo test || go test ./...
```

If tests fail:
- Analyze the failure
- Determine if fix needs adjustment
- Make corrections and re-test
- Document any test updates needed

### Step 6: Commit the Change

Create a semantic commit:
```bash
git add <affected_files>
git commit -m "fix: address review comment - <brief_description>

Resolves feedback from @<reviewer> in comment {comment_id}.
<detailed_explanation_of_fix>"
```

**Commit message format:**
- Type: `fix`, `refactor`, `style`, `perf`, `test`, `docs`
- Scope: Optional, e.g., `fix(auth):`
- Subject: Clear, imperative description
- Body: Reference comment, explain reasoning

### Step 7: Push Changes

```bash
git push origin HEAD
```

Wait for push to complete and verify success.

### Step 8: Reply to Comment

Post a reply on the original comment:

```bash
COMMIT_SHA=$(git rev-parse --short HEAD)
OWNER_REPO=$(gh repo view --json nameWithOwner -q .nameWithOwner)

gh api -X POST repos/$OWNER_REPO/pulls/{pr_number}/comments/{comment_id}/replies \
  -f body="✅ Fixed in commit $COMMIT_SHA.

**Changes made:**
- <bullet_list_of_changes>

**Validation:**
- [x] Tests passing
- [x] Code style compliant
- [x] No new issues introduced

Thank you for the feedback!"
```

### Step 9: Resolve Thread (Optional)

Only if `--resolve` flag is provided:

```bash
# First, get the thread ID from the comment
THREAD_ID=$(gh api graphql -f query='
query {
  repository(owner: "{owner}", name: "{repo}") {
    pullRequest(number: {pr_number}) {
      reviewThreads(first: 100) {
        nodes {
          id
          comments(first: 1) {
            nodes {
              databaseId
            }
          }
        }
      }
    }
  }
}' | jq -r --arg cid "{comment_id}" '.data.repository.pullRequest.reviewThreads.nodes[] | select(.comments.nodes[].databaseId == ($cid | tonumber)) | .id')

# Then resolve the thread
gh api graphql -f query='
mutation {
  resolveReviewThread(input: {threadId: "'$THREAD_ID'"}) {
    thread {
      isResolved
    }
  }
}'
```

### Step 10: Generate Summary

Output a summary:
```
## Comment Resolution Summary

- **Comment ID**: {comment_id}
- **Reviewer**: @{reviewer}
- **File**: {file_path}:{line_number}
- **Fix**: {brief_description}
- **Commit**: {commit_sha}
- **Status**: ✅ Resolved and pushed
- **Thread**: {Resolved | Left open for discussion}
```

## Usage Examples

### Example 1: Basic Comment Resolution

```
/resolve-gh-review-comment https://github.com/myorg/myrepo/pull/123#discussion_r456789
```

**Result:** Fetches comment, applies fix, commits, pushes, and replies.

### Example 2: Resolve with Thread Closure

```
/resolve-gh-review-comment 456789 --resolve
```

**Result:** Same as above, plus automatically resolves the review thread.

### Example 3: Multiple Related Comments

For comments on the same issue:
```
/resolve-gh-review-comment 456789 --resolve
/resolve-gh-review-comment 456790 --resolve
```

Or better yet, use `/bulk-resolve-comments` for batch processing.

## Error Handling

If issues occur:

1. **Comment not found**: Verify PR number and comment ID are correct
2. **Fix conflicts with current code**: Manually review and adjust
3. **Tests fail**: Investigate failure, fix root cause, re-commit
4. **Permission denied**: Check GitHub token has write permissions
5. **API rate limit**: Wait and retry, or use authenticated requests

## Success Criteria

- ✅ Fix correctly implements the suggestion
- ✅ All tests pass
- ✅ Code style is consistent
- ✅ Commit message is clear and traceable
- ✅ Reply posted with commit reference
- ✅ Thread resolved (if --resolve flag used)

## Best Practices

1. **Read carefully**: Understand the suggestion before implementing
2. **Test thoroughly**: Always run tests before pushing
3. **Communicate clearly**: Reply explains what was changed and why
4. **Be conservative**: If unsure, leave thread open for discussion
5. **Track changes**: Use semantic commits for traceability

## Integration with Other Commands

This command works well with:
- `/bulk-resolve-comments` - For handling multiple comments
- `/analyze-pr-metrics` - Track resolution efficiency
- `/validate-release` - Ensure quality standards maintained
