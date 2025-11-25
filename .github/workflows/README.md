# Issue Management Automation Workflow

This directory contains the automated issue management workflow for this repository.

## Overview

The `issue-automation.yml` workflow provides comprehensive automation for issue triage, categorization, and response. It runs automatically when issues are opened or labeled.

## Workflow Jobs

### 1. Issue Triage (`issue-triage`)
Automatically categorizes and prioritizes newly opened issues.

**Triggers:** When an issue is opened

**Actions:**
- **Category Labels** (based on title keywords, case-insensitive):
  - Title contains "bug" → adds `bug` label
  - Title contains "epic" → adds `epic` label
  - Title contains "maintenance" → adds `maintenance` label

- **Priority Labels** (based on title OR body keywords, highest priority wins):
  - "critical", "urgent", "production", "outage" → `priority-critical`
  - "important", "high", "blocking" → `priority-high`
  - "medium", "normal" → `priority-medium`
  - "low", "nice-to-have", "minor" → `priority-low`
  - Default: `priority-medium` if no keywords found

- **Status Labels:**
  - All issues get `needs-triage` label initially

**Outputs:**
- `issue-type`: The detected issue type (bug, epic, or maintenance)
- `priority-level`: The assigned priority level

### 2. Task Breakdown (`task-breakdown`)
Creates sub-issues for Epic features.

**Triggers:** When an issue with "Epic" in the title is opened

**Depends on:** `issue-triage` job

**Actions:**
- Creates exactly 4 sub-issues with pattern: `[SUBTASK] [Original Title] - Task N: [Task Name]`
- Task names:
  1. Requirements Analysis
  2. Design and Architecture
  3. Implementation
  4. Testing and Documentation
- Links sub-issues to parent with "Related to #[parent-number]"
- Updates parent issue body with "## Epic Tasks" checklist
- All sub-issues get `enhancement` and `needs-review` labels

### 3. Auto-Response (`auto-response`)
Provides automated responses and sets milestones.

**Triggers:** When an issue is opened

**Depends on:** `issue-triage` job

**Actions:**
- **First-time Contributor Detection:**
  - Checks if author is creating their first issue in this repository
  - If yes: adds `first-time-contributor` label and posts welcome message

- **Type-specific Responses:**
  - `bug` issues: Posts "Bug Report Guidelines" comment
  - `epic` issues: Posts "Feature Request Process" comment
  - `maintenance` issues: Posts "Maintenance Guidelines" comment

- **Milestone Assignment:**
  - Sets milestone "v1.0.0" for `priority-high` and `priority-critical` issues
  - Creates milestone if it doesn't exist

- **Status Update:**
  - Changes status from `needs-triage` to `needs-review`

## Labels Managed

### Category Labels
| Label | Color | Description |
|-------|-------|-------------|
| `bug` | ![#d73a4a](https://via.placeholder.com/15/d73a4a/000000?text=+) `#d73a4a` | Something isn't working |
| `enhancement` | ![#a2eeef](https://via.placeholder.com/15/a2eeef/000000?text=+) `#a2eeef` | New feature or request |
| `epic` | ![#3f20ba](https://via.placeholder.com/15/3f20ba/000000?text=+) `#3f20ba` | Large feature requiring multiple sub-tasks |
| `maintenance` | ![#fbca04](https://via.placeholder.com/15/fbca04/000000?text=+) `#fbca04` | Maintenance and housekeeping tasks |

### Priority Labels
| Label | Color | Description |
|-------|-------|-------------|
| `priority-critical` | ![#b60205](https://via.placeholder.com/15/b60205/000000?text=+) `#b60205` | Critical priority issue |
| `priority-high` | ![#d93f0b](https://via.placeholder.com/15/d93f0b/000000?text=+) `#d93f0b` | High priority issue |
| `priority-medium` | ![#fbca04](https://via.placeholder.com/15/fbca04/000000?text=+) `#fbca04` | Medium priority issue |
| `priority-low` | ![#0e8a16](https://via.placeholder.com/15/0e8a16/000000?text=+) `#0e8a16` | Low priority issue |

### Status Labels
| Label | Color | Description |
|-------|-------|-------------|
| `needs-triage` | ![#ededed](https://via.placeholder.com/15/ededed/000000?text=+) `#ededed` | Needs to be reviewed by maintainers |
| `needs-review` | ![#fbca04](https://via.placeholder.com/15/fbca04/000000?text=+) `#fbca04` | Awaiting review from maintainers |
| `first-time-contributor` | ![#7057ff](https://via.placeholder.com/15/7057ff/000000?text=+) `#7057ff` | Issue created by first-time contributor |

## Example Use Cases

### Example 1: Bug Report
**Issue Title:** "Bug: Login form validation not working"  
**Issue Body:** Contains "high priority"

**Automated Actions:**
- ✅ Adds `bug`, `needs-triage`, `priority-high` labels
- ✅ Posts "Bug Report Guidelines" comment
- ✅ Sets milestone "v1.0.0"
- ✅ Changes status to `needs-review`
- ✅ If first issue by author: adds `first-time-contributor` label and welcome message

### Example 2: Epic Feature
**Issue Title:** "Epic: Redesign user dashboard interface"  
**Issue Body:** Contains "high priority"

**Automated Actions:**
- ✅ Adds `epic`, `needs-triage`, `priority-high` labels
- ✅ Creates 4 sub-issues with `enhancement` and `needs-review` labels
- ✅ Updates parent with "## Epic Tasks" checklist
- ✅ Posts "Feature Request Process" comment
- ✅ Sets milestone "v1.0.0"
- ✅ Changes status to `needs-review`

### Example 3: Maintenance Task
**Issue Title:** "Weekly maintenance cleanup and refactor"  
**Issue Body:** Contains "normal" priority

**Automated Actions:**
- ✅ Adds `maintenance`, `needs-triage`, `priority-medium` labels
- ✅ Posts "Maintenance Guidelines" comment
- ✅ NO milestone (only high/critical get milestones)
- ✅ Changes status to `needs-review`

## Features

✨ **Smart Detection:** Uses case-insensitive keyword matching for flexible issue creation  
✨ **Automatic Label Creation:** Creates labels if they don't exist  
✨ **Priority Detection:** Scans both title and body for priority keywords  
✨ **Epic Management:** Automatically breaks down large features into manageable tasks  
✨ **Contributor Welcome:** Welcomes first-time contributors automatically  
✨ **Milestone Tracking:** High-priority issues are automatically tracked  
✨ **Error Handling:** Includes graceful fallbacks for all operations  

## Permissions

The workflow requires the following permissions:
- `issues: write` - To create, update, and label issues
- `contents: read` - To read repository contents

## Troubleshooting

If the workflow doesn't appear to be running:
1. Check that GitHub Actions is enabled for the repository
2. Verify the workflow file exists at `.github/workflows/issue-automation.yml`
3. Ensure the repository has the required permissions
4. Check the Actions tab in the repository for workflow run logs
5. Verify the issue event matches the trigger (opened or labeled)

## Contributing

To modify the automation:
1. Edit `.github/workflows/issue-automation.yml`
2. Test changes on a feature branch
3. Create a pull request with your changes
4. Verify the workflow runs correctly after merging

## Support

For issues with the automation workflow, please:
1. Check the workflow run logs in the Actions tab
2. Review this documentation
3. Create an issue with the `bug` label if you encounter problems
