# Analyze PR Metrics

Generate comprehensive analytics and insights about pull request performance, review efficiency, and team productivity metrics.

## Command Syntax

```
/analyze-pr-metrics [--period 7d|30d|90d|all] [--team team-name] [--export csv|json|markdown]
```

## Parameters

- **--period** (optional): Time period for analysis
  - `7d`: Last 7 days
  - `30d`: Last 30 days (default)
  - `90d`: Last 90 days (quarter)
  - `all`: All available data
- **--team** (optional): Filter by team or author (GitHub username or team slug)
- **--export** (optional): Export format
  - `csv`: Comma-separated values
  - `json`: JSON format
  - `markdown`: Formatted markdown report (default)

## Workflow

You are a **GitHub PR Analytics Specialist**. Execute this comprehensive analysis workflow:

### Phase 1: Data Collection

#### Step 1: Calculate Time Period

```bash
case $PERIOD in
  7d)
    START_DATE=$(date -d "7 days ago" --iso-8601)
    ;;
  30d)
    START_DATE=$(date -d "30 days ago" --iso-8601)
    ;;
  90d)
    START_DATE=$(date -d "90 days ago" --iso-8601)
    ;;
  all)
    START_DATE="2000-01-01"  # Effectively all data
    ;;
esac

echo "Analyzing PRs from $START_DATE to $(date --iso-8601)"
```

#### Step 2: Fetch Pull Request Data

```bash
# Get all PRs in the time period
gh pr list \
  --state all \
  --limit 1000 \
  --search "created:>=$START_DATE" \
  --json number,title,state,createdAt,closedAt,mergedAt,author,reviewers,additions,deletions,files,reviews,comments,labels,milestone \
  > prs_data.json

TOTAL_PRS=$(jq 'length' prs_data.json)
echo "Found $TOTAL_PRS pull requests"
```

#### Step 3: Fetch Review Comments

```bash
# For each PR, get review comments
echo "[]" > all_comments.json

jq -r '.[].number' prs_data.json | while read pr_number; do
  gh api repos/{owner}/{repo}/pulls/$pr_number/comments --paginate >> all_comments.json
done

TOTAL_COMMENTS=$(jq 'length' all_comments.json)
echo "Found $TOTAL_COMMENTS review comments"
```

### Phase 2: Metric Calculations

#### Step 4: PR Cycle Time Metrics

Calculate time from PR creation to merge:

```bash
# Average time to merge (in days)
AVG_MERGE_TIME=$(jq -r '[.[] | select(.mergedAt != null) |
  (((.mergedAt | fromdateiso8601) - (.createdAt | fromdateiso8601)) / 86400)] |
  add / length | floor' prs_data.json)

# Median time to merge
MEDIAN_MERGE_TIME=$(jq -r '[.[] | select(.mergedAt != null) |
  (((.mergedAt | fromdateiso8601) - (.createdAt | fromdateiso8601)) / 86400)] |
  sort | .[length/2 | floor]' prs_data.json)

# 90th percentile (outliers)
P90_MERGE_TIME=$(jq -r '[.[] | select(.mergedAt != null) |
  (((.mergedAt | fromdateiso8601) - (.createdAt | fromdateiso8601)) / 86400)] |
  sort | .[length*0.9 | floor]' prs_data.json)

echo "Average merge time: $AVG_MERGE_TIME days"
echo "Median merge time: $MEDIAN_MERGE_TIME days"
echo "90th percentile: $P90_MERGE_TIME days"
```

#### Step 5: Review Efficiency Metrics

```bash
# Time to first review
AVG_FIRST_REVIEW=$(jq -r '[.[] | select(.reviews | length > 0) |
  ((.reviews[0].submittedAt | fromdateiso8601) - (.createdAt | fromdateiso8601)) / 3600] |
  add / length | floor' prs_data.json)

# Number of review rounds
AVG_REVIEW_ROUNDS=$(jq -r '[.[] | .reviews | length] | add / length | floor' prs_data.json)

# Comment density (comments per PR)
AVG_COMMENTS=$(jq -r '[.[] | .comments | length] | add / length | floor' prs_data.json)

echo "Average time to first review: $AVG_FIRST_REVIEW hours"
echo "Average review rounds: $AVG_REVIEW_ROUNDS"
echo "Average comments per PR: $AVG_COMMENTS"
```

#### Step 6: PR Size Metrics

```bash
# Average additions
AVG_ADDITIONS=$(jq -r '[.[] | .additions] | add / length | floor' prs_data.json)

# Average deletions
AVG_DELETIONS=$(jq -r '[.[] | .deletions] | add / length | floor' prs_data.json)

# Average files changed
AVG_FILES=$(jq -r '[.[] | .files | length] | add / length | floor' prs_data.json)

# Code churn ratio (deletions / additions)
CHURN_RATIO=$(echo "scale=2; $AVG_DELETIONS / $AVG_ADDITIONS" | bc)

echo "Average additions: $AVG_ADDITIONS lines"
echo "Average deletions: $AVG_DELETIONS lines"
echo "Average files changed: $AVG_FILES"
echo "Code churn ratio: $CHURN_RATIO"
```

#### Step 7: Throughput Metrics

```bash
# PRs merged per day
DAYS_IN_PERIOD=$(( ($(date +%s) - $(date -d "$START_DATE" +%s)) / 86400 ))
MERGED_PRS=$(jq '[.[] | select(.mergedAt != null)] | length' prs_data.json)
PRS_PER_DAY=$(echo "scale=2; $MERGED_PRS / $DAYS_IN_PERIOD" | bc)

# PRs closed without merge
CLOSED_UNMERGED=$(jq '[.[] | select(.state == "closed" and .mergedAt == null)] | length' prs_data.json)

# Currently open
OPEN_PRS=$(jq '[.[] | select(.state == "open")] | length' prs_data.json)

echo "PRs merged per day: $PRS_PER_DAY"
echo "PRs closed without merge: $CLOSED_UNMERGED"
echo "Currently open: $OPEN_PRS"
```

#### Step 8: Author and Reviewer Metrics

```bash
# Top contributors
TOP_AUTHORS=$(jq -r '[.[] | .author.login] | group_by(.) |
  map({author: .[0], count: length}) | sort_by(.count) | reverse | .[0:5]' prs_data.json)

# Top reviewers
TOP_REVIEWERS=$(jq -r '[.[] | .reviewers[].login] | group_by(.) |
  map({reviewer: .[0], count: length}) | sort_by(.count) | reverse | .[0:5]' prs_data.json)

# Review participation rate
TOTAL_TEAM_MEMBERS=10  # TODO: Get from GitHub API
AVG_REVIEWERS=$(jq -r '[.[] | .reviewers | length] | add / length | floor' prs_data.json)
PARTICIPATION_RATE=$(echo "scale=2; $AVG_REVIEWERS / $TOTAL_TEAM_MEMBERS * 100" | bc)

echo "Top contributors: $TOP_AUTHORS"
echo "Review participation rate: $PARTICIPATION_RATE%"
```

### Phase 3: Quality Metrics

#### Step 9: Review Quality Indicators

```bash
# PRs with requested changes
CHANGES_REQUESTED=$(jq '[.[] | select(.reviews[] | .state == "CHANGES_REQUESTED")] | length' prs_data.json)

# PRs approved on first review
FIRST_REVIEW_APPROVALS=$(jq '[.[] | select(.reviews | length == 1 and .[0].state == "APPROVED")] | length' prs_data.json)

# Comment resolution rate
RESOLVED_THREADS=$(jq '[.[] | .reviews[] | select(.state == "COMMENTED")] | length' all_comments.json)
# Calculate from thread data

echo "PRs requiring changes: $CHANGES_REQUESTED"
echo "First-review approvals: $FIRST_REVIEW_APPROVALS"
```

#### Step 10: Automation Efficiency

```bash
# PRs using automated comment resolution
AUTO_RESOLVED=$(jq '[.[] | .comments[] | select(.body | contains("Fixed in commit"))] | length' all_comments.json)

# Copilot agent usage
COPILOT_REVIEWS=$(jq '[.[] | .reviews[] | select(.user.login == "github-copilot")] | length' prs_data.json)

# CodeRabbit/Sourcery usage
CODERABBIT_REVIEWS=$(jq '[.[] | .reviews[] | select(.user.login | contains("coderabbit"))] | length' prs_data.json)

echo "Auto-resolved comments: $AUTO_RESOLVED"
echo "Copilot reviews: $COPILOT_REVIEWS"
echo "CodeRabbit reviews: $CODERABBIT_REVIEWS"
```

### Phase 4: Trend Analysis

#### Step 11: Week-over-Week Trends

```bash
# Calculate metrics for each week
for week in 1 2 3 4; do
  WEEK_START=$(date -d "$((week * 7)) days ago" --iso-8601)
  WEEK_END=$(date -d "$(((week - 1) * 7)) days ago" --iso-8601)

  WEEK_MERGED=$(jq --arg start "$WEEK_START" --arg end "$WEEK_END" \
    '[.[] | select(.mergedAt != null and
     (.mergedAt >= $start and .mergedAt < $end))] | length' prs_data.json)

  WEEK_AVG_TIME=$(jq --arg start "$WEEK_START" --arg end "$WEEK_END" \
    '[.[] | select(.mergedAt != null and
     (.mergedAt >= $start and .mergedAt < $end)) |
     (((.mergedAt | fromdateiso8601) - (.createdAt | fromdateiso8601)) / 86400)] |
     add / length | floor' prs_data.json)

  echo "Week $week: $WEEK_MERGED merged (avg ${WEEK_AVG_TIME}d)"
done
```

#### Step 12: Identify Bottlenecks

```bash
# PRs waiting > 3 days for review
REVIEW_BOTTLENECK=$(jq '[.[] | select(.state == "open" and
  ((.reviews | length) == 0) and
  ((now - (.createdAt | fromdateiso8601)) / 86400) > 3)] | length' prs_data.json)

# Large PRs (> 500 lines)
LARGE_PRS=$(jq '[.[] | select(.additions + .deletions > 500)] | length' prs_data.json)

# Stale PRs (open > 14 days)
STALE_PRS=$(jq '[.[] | select(.state == "open" and
  ((now - (.createdAt | fromdateiso8601)) / 86400) > 14)] | length' prs_data.json)

echo "⚠️ Bottlenecks:"
echo "- PRs waiting for review: $REVIEW_BOTTLENECK"
echo "- Large PRs (>500 lines): $LARGE_PRS"
echo "- Stale PRs (>14 days): $STALE_PRS"
```

### Phase 5: Report Generation

#### Step 13: Generate Markdown Report

```markdown
# Pull Request Metrics Report

**Period**: $START_DATE to $(date --iso-8601)
**Total PRs Analyzed**: $TOTAL_PRS
**Generated**: $(date)

---

## Executive Summary

| Metric | Value | Trend |
|--------|-------|-------|
| Average Merge Time | ${AVG_MERGE_TIME}d | ${TREND_MERGE_TIME} |
| Median Merge Time | ${MEDIAN_MERGE_TIME}d | ${TREND_MEDIAN} |
| PRs Merged/Day | ${PRS_PER_DAY} | ${TREND_THROUGHPUT} |
| Time to First Review | ${AVG_FIRST_REVIEW}h | ${TREND_FIRST_REVIEW} |
| Review Rounds | ${AVG_REVIEW_ROUNDS} | ${TREND_REVIEW_ROUNDS} |

---

## 📊 Key Performance Indicators

### Cycle Time

- **Average**: ${AVG_MERGE_TIME} days
- **Median**: ${MEDIAN_MERGE_TIME} days
- **90th Percentile**: ${P90_MERGE_TIME} days

**Interpretation**:
${AVG_MERGE_TIME <= 3 ? "✅ Excellent - PRs are merging quickly" :
  AVG_MERGE_TIME <= 7 ? "⚠️ Good - Consider ways to speed up review" :
  "❌ Needs Improvement - PRs taking too long to merge"}

### Review Efficiency

- **Time to First Review**: ${AVG_FIRST_REVIEW} hours
- **Average Review Rounds**: ${AVG_REVIEW_ROUNDS}
- **Comments per PR**: ${AVG_COMMENTS}

**Interpretation**:
${AVG_FIRST_REVIEW <= 24 ? "✅ Fast response time" :
  "⚠️ Consider dedicating more time to reviews"}

### PR Size

- **Average Additions**: ${AVG_ADDITIONS} lines
- **Average Deletions**: ${AVG_DELETIONS} lines
- **Average Files Changed**: ${AVG_FILES}
- **Code Churn Ratio**: ${CHURN_RATIO}

**Interpretation**:
${AVG_ADDITIONS <= 300 ? "✅ Good - PRs are appropriately sized" :
  AVG_ADDITIONS <= 500 ? "⚠️ Moderate - Some PRs may be large" :
  "❌ Large PRs - Encourage smaller, focused changes"}

### Throughput

- **PRs Merged**: ${MERGED_PRS}
- **PRs per Day**: ${PRS_PER_DAY}
- **Closed without Merge**: ${CLOSED_UNMERGED}
- **Currently Open**: ${OPEN_PRS}

**Merge Rate**: ${MERGE_RATE}% (${MERGED_PRS}/${TOTAL_PRS})

---

## 👥 Team Performance

### Top Contributors

| Author | PRs Created | Avg Lines | Merge Rate |
|--------|-------------|-----------|------------|
${TOP_AUTHORS_TABLE}

### Top Reviewers

| Reviewer | Reviews | Avg Response Time | Approval Rate |
|----------|---------|-------------------|---------------|
${TOP_REVIEWERS_TABLE}

### Participation

- **Review Participation Rate**: ${PARTICIPATION_RATE}%
- **Average Reviewers per PR**: ${AVG_REVIEWERS}

---

## 🤖 Automation Effectiveness

### AI-Assisted Reviews

- **GitHub Copilot Reviews**: ${COPILOT_REVIEWS}
- **CodeRabbit/Sourcery Reviews**: ${CODERABBIT_REVIEWS}
- **Automated Comment Resolutions**: ${AUTO_RESOLVED}

**Automation Adoption**: ${AUTOMATION_RATE}%

**Impact**:
- Estimated time saved: ${TIME_SAVED} hours
- Average review time with automation: ${AUTO_REVIEW_TIME}h
- Average review time without automation: ${MANUAL_REVIEW_TIME}h
- Efficiency gain: ${EFFICIENCY_GAIN}%

---

## 📈 Trends (Last 4 Weeks)

\`\`\`
Week 1: ${WEEK1_MERGED} merged (avg ${WEEK1_AVG_TIME}d) ${WEEK1_TREND}
Week 2: ${WEEK2_MERGED} merged (avg ${WEEK2_AVG_TIME}d) ${WEEK2_TREND}
Week 3: ${WEEK3_MERGED} merged (avg ${WEEK3_AVG_TIME}d) ${WEEK3_TREND}
Week 4: ${WEEK4_MERGED} merged (avg ${WEEK4_AVG_TIME}d) ${WEEK4_TREND}
\`\`\`

**Trend Direction**: ${OVERALL_TREND}

---

## ⚠️ Bottlenecks & Issues

### Current Issues

1. **Waiting for Review** (${REVIEW_BOTTLENECK} PRs)
   - PRs open > 3 days without review
   - Action: Assign reviewers, increase review bandwidth

2. **Large PRs** (${LARGE_PRS} PRs > 500 lines)
   - Action: Encourage smaller, focused PRs
   - Consider breaking into multiple PRs

3. **Stale PRs** (${STALE_PRS} PRs > 14 days)
   - Action: Close or prioritize
   - Review relevance and priority

### Quality Indicators

- **Changes Requested**: ${CHANGES_REQUESTED} PRs (${CHANGES_REQUESTED_PERCENT}%)
- **First-Review Approvals**: ${FIRST_REVIEW_APPROVALS} PRs (${FIRST_APPROVAL_PERCENT}%)

${CHANGES_REQUESTED_PERCENT > 50 ? "⚠️ High change request rate - Consider improving PR quality or reviewer alignment" :
  "✅ Good balance of thoroughness and approval"}

---

## 🎯 Recommendations

### Immediate Actions

${RECOMMENDATIONS}

### Process Improvements

1. **Reduce Cycle Time**
   ${CYCLE_TIME_RECOMMENDATIONS}

2. **Improve Review Efficiency**
   ${REVIEW_EFFICIENCY_RECOMMENDATIONS}

3. **Optimize PR Size**
   ${PR_SIZE_RECOMMENDATIONS}

4. **Increase Automation**
   ${AUTOMATION_RECOMMENDATIONS}

---

## 📊 Detailed Statistics

### PR State Distribution

\`\`\`
Merged:    ${MERGED_PRS} (${MERGED_PERCENT}%)
Open:      ${OPEN_PRS} (${OPEN_PERCENT}%)
Closed:    ${CLOSED_UNMERGED} (${CLOSED_PERCENT}%)
\`\`\`

### Review Comments Analysis

- **Total Comments**: ${TOTAL_COMMENTS}
- **Critical Issues**: ${CRITICAL_COMMENTS}
- **High Priority**: ${HIGH_COMMENTS}
- **Medium Priority**: ${MEDIUM_COMMENTS}
- **Low Priority**: ${LOW_COMMENTS}

### Label Distribution

${LABEL_DISTRIBUTION}

---

## 📥 Export Data

Raw data available in:
- CSV: \`pr-metrics.csv\`
- JSON: \`pr-metrics.json\`
- Charts: \`pr-metrics-charts/\`

---

*Report generated by \`/analyze-pr-metrics\` command*
*Data source: GitHub API*
```

#### Step 14: Generate Visualizations (if possible)

Create simple ASCII charts for trends:

```bash
# Generate sparkline for weekly throughput
echo "Weekly PR Throughput:"
echo "${WEEK1_MERGED} ${WEEK2_MERGED} ${WEEK3_MERGED} ${WEEK4_MERGED}" | \
  spark

# Generate bar chart for cycle time distribution
echo "Cycle Time Distribution:"
# < 1 day: ████████
# 1-3 days: ████████████████
# 3-7 days: ██████████
# > 7 days: ████
```

### Phase 6: Export & Storage

#### Step 15: Export Data

```bash
# Export to CSV
jq -r '["Number","Title","State","Created","Merged","Author","Additions","Deletions","Files","Reviews","Comments"],
  (.[] | [.number, .title, .state, .createdAt, .mergedAt, .author.login, .additions, .deletions, (.files|length), (.reviews|length), (.comments|length)]) |
  @csv' prs_data.json > pr-metrics.csv

# Export to JSON (formatted)
jq '.' prs_data.json > pr-metrics.json

echo "✅ Data exported to pr-metrics.csv and pr-metrics.json"
```

#### Step 16: Store Historical Data

```bash
# Store in metrics history
mkdir -p .github/metrics
REPORT_FILE=".github/metrics/pr-report-$(date +%Y-%m-%d).md"

mv pr-metrics-report.md "$REPORT_FILE"

# Update metrics index
echo "- [$(date +%Y-%m-%d)]: $REPORT_FILE" >> .github/metrics/INDEX.md

git add .github/metrics/
git commit -m "chore: add PR metrics report for $(date +%Y-%m-%d)"
git push origin HEAD
```

## Usage Examples

### Example 1: Standard Monthly Report

```
/analyze-pr-metrics --period 30d
```

Generates a comprehensive 30-day analysis with trends and recommendations.

### Example 2: Quarterly Review

```
/analyze-pr-metrics --period 90d --export json
```

Analyzes the last quarter and exports raw data in JSON format.

### Example 3: Team-Specific Analysis

```
/analyze-pr-metrics --period 30d --team frontend-team
```

Focuses on a specific team's PR performance.

### Example 4: Weekly Standup Report

```
/analyze-pr-metrics --period 7d --export markdown
```

Quick weekly summary for team meetings.

## Output

Display comprehensive summary with actionable insights and historical comparison.

## Integration

This command integrates with:
- `/setup-pr-workflow` - Initial metrics baseline
- `/bulk-resolve-comments` - Track resolution efficiency
- GitHub Actions - Automated weekly reports
- SERENA MCP - Store metrics history across sessions

## Best Practices

1. **Regular Analysis**: Run monthly for trend tracking
2. **Action on Insights**: Address bottlenecks proactively
3. **Share with Team**: Use in retrospectives
4. **Compare Periods**: Track improvement over time
5. **Celebrate Wins**: Acknowledge positive trends
6. **Data-Driven Decisions**: Base process changes on metrics
