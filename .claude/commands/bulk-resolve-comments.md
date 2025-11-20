# Bulk Resolve GitHub PR Comments

Systematically resolve multiple GitHub pull request review comments in priority order, grouping related issues and applying fixes efficiently.

## Command Syntax

```
/bulk-resolve-comments <pr_number> [--priority critical|high|all] [--auto-resolve]
```

## Parameters

- **pr_number** (required): The pull request number
- **--priority** (optional): Filter by priority level
  - `critical`: Security issues, breaking changes, bugs
  - `high`: Performance, architecture, major refactoring
  - `all`: Process all comments (default)
- **--auto-resolve** (optional): Automatically resolve threads after fixing

## Workflow

You are a **GitHub PR Batch Resolution Specialist**. Execute this systematic workflow:

### Phase 1: Discovery & Analysis

#### Step 1: Fetch All Review Comments

```bash
gh api repos/{owner}/{repo}/pulls/{pr_number}/comments --paginate > comments.json
```

Retrieve:
- Comment IDs
- File paths
- Line numbers
- Comment bodies
- Reviewer usernames
- Created timestamps
- Thread status (resolved/unresolved)

#### Step 2: Filter Unresolved Comments

Parse the JSON and extract only unresolved comments:

```bash
jq '[.[] | select(.in_reply_to_id == null and (.pull_request_review_id != null))]' comments.json
```

#### Step 3: Categorize by Priority

Analyze each comment and assign priority:

**Critical Priority** (security, bugs, breaking):
- Contains: "security", "vulnerability", "bug", "broken", "crash", "error"
- Affects: Authentication, authorization, data integrity
- Impact: Production-breaking changes

**High Priority** (performance, architecture):
- Contains: "performance", "slow", "memory leak", "architecture", "design"
- Affects: System scalability, maintainability
- Impact: Significant code restructuring

**Medium Priority** (quality, standards):
- Contains: "refactor", "code smell", "duplicate", "complexity"
- Affects: Code maintainability, readability
- Impact: Localized improvements

**Low Priority** (style, documentation):
- Contains: "style", "formatting", "comment", "documentation", "typo"
- Affects: Code aesthetics, documentation
- Impact: Minor cosmetic changes

#### Step 4: Group Related Comments

Group comments by:
1. **File**: Comments on the same file
2. **Function/Method**: Comments on the same logical unit
3. **Issue Type**: Similar categories of problems
4. **Dependency**: Comments that must be fixed together

Create a resolution plan:
```json
{
  "critical": [
    {
      "group": "auth-security",
      "files": ["auth.js", "middleware.js"],
      "comments": [123, 124, 125],
      "estimated_effort": "high"
    }
  ],
  "high": [...],
  "medium": [...],
  "low": [...]
}
```

### Phase 2: Systematic Resolution

#### Step 5: Process by Priority

For each priority level (critical → high → medium → low):

1. **Review the group**
   - Read all comments in the group
   - Understand the interconnections
   - Plan the fix strategy

2. **Implement fixes**
   - Apply changes systematically
   - Maintain consistency across files
   - Avoid introducing new issues

3. **Validate changes**
   - Run tests after each group
   - Check for regressions
   - Verify fix completeness

4. **Commit with context**
   - Group-level commits
   - Clear, descriptive messages
   - Reference all addressed comments

#### Step 6: Apply Fixes Per Group

For each group of related comments:

```bash
# Example: Fixing authentication group
echo "Processing group: auth-security (Critical)"
echo "Comments: 123, 124, 125"

# Apply fixes to auth.js
# Apply fixes to middleware.js

# Run tests
npm test -- auth.test.js

# Commit the group
git add auth.js middleware.js
git commit -m "fix(auth): address critical security review comments

Resolves comments #123, #124, #125 from security review.

Changes:
- Add input sanitization to login endpoint
- Implement rate limiting on authentication
- Fix JWT token validation logic

All authentication tests passing."

# Push changes
git push origin HEAD
```

#### Step 7: Reply to All Comments in Group

```bash
COMMIT_SHA=$(git rev-parse --short HEAD)

# Reply to each comment in the group
for comment_id in 123 124 125; do
  gh api -X POST repos/{owner}/{repo}/pulls/{pr_number}/comments/$comment_id/replies \
    -f body="✅ Fixed in commit $COMMIT_SHA as part of authentication security improvements.

**Group fix applied:**
- Input sanitization added
- Rate limiting implemented
- JWT validation corrected

**Validation:**
- [x] Security tests passing
- [x] No new vulnerabilities introduced
- [x] Performance impact minimal

Related fixes: #123 #124 #125"
done
```

#### Step 8: Resolve Threads (if --auto-resolve)

For each comment, resolve the thread:

```bash
for comment_id in 123 124 125; do
  # Get thread ID
  THREAD_ID=$(gh api graphql -f query='...' | jq -r '.data...')

  # Resolve thread
  gh api graphql -f query='
  mutation {
    resolveReviewThread(input: {threadId: "'$THREAD_ID'"}) {
      thread { isResolved }
    }
  }'
done
```

### Phase 3: Validation & Reporting

#### Step 9: Run Full Test Suite

After all fixes applied:

```bash
echo "Running full test suite..."
npm test || pytest || cargo test

# Check code coverage
npm run coverage

# Run linters
npm run lint

# Security scan
npm audit || snyk test
```

#### Step 10: Generate Resolution Report

Create a comprehensive summary:

```markdown
## Bulk Comment Resolution Report

### PR Information
- **PR Number**: #{pr_number}
- **Total Comments**: {total_count}
- **Resolved**: {resolved_count}
- **Remaining**: {remaining_count}

### Resolution Breakdown by Priority

#### Critical (Security & Bugs)
- Total: {critical_count}
- Resolved: {critical_resolved}
- Commits: {commit_sha_list}
- Files affected: {file_list}

#### High (Performance & Architecture)
- Total: {high_count}
- Resolved: {high_resolved}
- Commits: {commit_sha_list}

#### Medium (Code Quality)
- Total: {medium_count}
- Resolved: {medium_resolved}
- Commits: {commit_sha_list}

#### Low (Style & Documentation)
- Total: {low_count}
- Resolved: {low_resolved}
- Commits: {commit_sha_list}

### Test Results
- ✅ All tests passing
- ✅ Code coverage: {coverage_percent}%
- ✅ No new linter errors
- ✅ Security scan clean

### Unresolved Comments
{list_of_unresolved_with_reasons}

### Next Steps
1. {action_item_1}
2. {action_item_2}
3. Request re-review from @{reviewers}
```

#### Step 11: Post Summary Comment

Add the report to the PR:

```bash
gh pr comment {pr_number} --body-file resolution-report.md
```

## Usage Examples

### Example 1: Critical Issues Only

```
/bulk-resolve-comments 456 --priority critical --auto-resolve
```

**Result:**
- Processes only critical security and bug comments
- Applies fixes in priority order
- Auto-resolves threads
- Posts summary report

### Example 2: Complete PR Cleanup

```
/bulk-resolve-comments 456 --priority all
```

**Result:**
- Processes all unresolved comments
- Groups by file and issue type
- Leaves threads open for reviewer confirmation
- Comprehensive resolution report

### Example 3: Performance Improvements

```
/bulk-resolve-comments 456 --priority high
```

**Result:**
- Focuses on high-priority items
- Performance and architecture fixes
- Detailed commit messages
- Test validation

## Advanced Features

### Smart Grouping

The system intelligently groups comments:

**By File Proximity:**
```
Group 1: src/auth/login.js (comments 10, 11, 12)
Group 2: src/auth/register.js (comments 13, 14)
```

**By Issue Category:**
```
Group: SQL Injection (comments 20, 25, 30)
Group: XSS Prevention (comments 21, 26, 31)
```

**By Dependency Chain:**
```
Group: API Refactor (must fix in order)
  1. Comment 40: Update interface
  2. Comment 41: Fix implementation
  3. Comment 42: Update tests
```

### Conflict Resolution

If comments suggest conflicting changes:

1. **Detect conflicts**
   - Analyze suggestions
   - Identify contradictions

2. **Escalate**
   - Comment on PR: "Comments #X and #Y suggest conflicting approaches"
   - Tag reviewers: "@reviewer1 @reviewer2 please clarify"
   - Pause resolution until clarified

3. **Document decision**
   - Record which approach was chosen
   - Explain reasoning in commit message

### Partial Resolution

If a comment cannot be fully addressed:

```bash
gh api -X POST repos/{owner}/{repo}/pulls/{pr_number}/comments/{comment_id}/replies \
  -f body="⚠️ Partially addressed in commit $COMMIT_SHA.

**Completed:**
- [x] Input validation added
- [x] Error handling improved

**Pending:**
- [ ] Performance optimization requires architectural discussion
- [ ] Suggested caching layer needs team review

**Next steps:**
Created issue #{issue_number} to track remaining work.
cc @tech-lead @architect"
```

## Error Handling

### Common Issues

1. **Test Failures After Fix**
   - Roll back the change
   - Analyze test failure
   - Adjust fix
   - Re-test before committing

2. **Merge Conflicts**
   - Pull latest changes: `git pull origin main`
   - Resolve conflicts
   - Re-apply fixes
   - Re-run tests

3. **API Rate Limiting**
   - Implement delays between API calls
   - Use authenticated requests (higher limits)
   - Batch operations where possible

4. **Permission Issues**
   - Verify GitHub token scopes
   - Check repository write permissions
   - Ensure branch is not protected

### Rollback Strategy

If bulk resolution causes issues:

```bash
# Create backup branch
git branch backup-before-bulk-resolve

# If issues occur, reset
git reset --hard backup-before-bulk-resolve
git push --force-with-lease origin HEAD
```

## Performance Optimization

### Batch Operations

Process comments in batches:
- Batch 1: Critical (0-5 comments)
- Batch 2: High (5-15 comments)
- Batch 3: Medium (15-30 comments)
- Batch 4: Low (30+ comments)

### Parallel Processing

For independent groups:
- Process multiple file groups simultaneously
- Use git worktrees for parallel branches
- Merge results systematically

### Incremental Validation

Test after each group rather than at the end:
- Faster feedback
- Easier to identify problematic fixes
- Less rollback needed

## Success Criteria

- ✅ All targeted comments addressed
- ✅ Fixes correctly implement suggestions
- ✅ Tests passing after all changes
- ✅ Commits are clear and traceable
- ✅ Replies posted on all comments
- ✅ Resolution report generated
- ✅ No new issues introduced

## Integration with Workflow

This command integrates with:
- `/resolve-gh-review-comment` - Single comment resolution
- `/analyze-pr-metrics` - Track efficiency metrics
- `/validate-release` - Final quality check
- GitHub Actions - Automated CI/CD validation

## Best Practices

1. **Start with critical**: Always address security/bugs first
2. **Group intelligently**: Related fixes together
3. **Test incrementally**: After each group, not just at end
4. **Communicate clearly**: Detailed replies and reports
5. **Document decisions**: Explain why certain approaches chosen
6. **Track progress**: Use issue links for complex items
7. **Request re-review**: Tag reviewers when done
