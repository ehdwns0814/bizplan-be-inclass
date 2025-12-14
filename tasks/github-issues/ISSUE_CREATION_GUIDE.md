# GitHub Issues Creation Guide

## 📋 Overview
This guide provides instructions for creating GitHub issues for the Bizplan Backend project.

## ⚠️ Important Notes
- **EPIC 0 (Issues #001-#005)**: These frontend PoC tasks were completed in a separate project. **DO NOT create these issues** in this backend repository.
- **Active Issues**: Only create issues #006-#015 (10 issues total)

## 📝 Issues to Create

### Backend Issues (10 total)

#### EPIC 1: Core Backend (5 issues)
- `#006`: REQ-FUNC-001-BE-001 - Project Creation API
- `#007`: REQ-FUNC-002-BE-001 - Wizard Answer API
- `#008`: REQ-FUNC-003-AI-001 - Bizplan LLM Engine (FastAPI)
- `#009`: REQ-FUNC-003-BE-001 - Document Generation Orchestration
- `#010`: REQ-FUNC-011-BE-001 - Export HWP/PDF

#### EPIC 2: Special Features (2 issues)
- `#011`: REQ-FUNC-008-AI-001 - PMF Diagnosis Engine
- `#012`: REQ-FUNC-012-BE-001 - Financial Calculation Engine

#### EPIC 3: Non-Functional (3 issues)
- `#013`: REQ-NF-006-SEC-001 - Data Encryption & Security
- `#014`: REQ-NF-012-OPS-001 - Logging & Monitoring
- `#015`: REQ-NF-001-PERF-001 - K6 Load Testing

## 🚀 Creation Commands

### Prerequisites
```bash
# Ensure GitHub CLI is installed and authenticated
gh --version

# Verify authentication
gh auth status

# Refresh with project scope if needed
gh auth refresh -s project
```

### Batch Issue Creation Script

Create a script `create-backend-issues.sh`:

```bash
#!/bin/bash

# Configuration
REPO_OWNER="your-username"
REPO_NAME="bizplan-be-inclass"
PROJECT_NUMBER="1"  # Update with your project number

BASE_DIR="tasks/github-issues"

echo "🚀 Creating Backend Issues for Bizplan Project..."
echo "================================================"
echo ""

# Array of issue files to create (excluding EPIC0 #001-#005)
declare -a ISSUES=(
    "006-REQ-FUNC-001-BE-001-PROJECT-CREATION-API.md"
    "007-REQ-FUNC-002-BE-001-WIZARD-ANSWER-API.md"
    "008-REQ-FUNC-003-AI-001-BIZPLAN-LLM-ENGINE.md"
    "009-REQ-FUNC-003-BE-001-DOCUMENT-GENERATION-ORCHESTRATION.md"
    "010-REQ-FUNC-011-BE-001-EXPORT-HWP-PDF.md"
    "011-REQ-FUNC-008-AI-001-PMF-DIAGNOSIS-ENGINE.md"
    "012-REQ-FUNC-012-BE-001-FINANCIAL-CALCULATION-ENGINE.md"
    "013-REQ-NF-006-SEC-001-DATA-ENCRYPTION-SECURITY.md"
    "014-REQ-NF-012-OPS-001-LOGGING-MONITORING.md"
    "015-REQ-NF-001-PERF-001-K6-LOAD-TESTING.md"
)

# Function to extract title from markdown
extract_title() {
    local file=$1
    grep -m 1 "^# \[#" "$file" | sed 's/^# //'
}

# Function to determine labels
get_labels() {
    local file=$1
    local labels="Issue-Automation"
    
    # Add epic label
    if [[ $file == *"006"* ]] || [[ $file == *"007"* ]] || [[ $file == *"008"* ]] || [[ $file == *"009"* ]] || [[ $file == *"010"* ]]; then
        labels="$labels,epic-1"
    elif [[ $file == *"011"* ]] || [[ $file == *"012"* ]]; then
        labels="$labels,epic-2"
    elif [[ $file == *"013"* ]] || [[ $file == *"014"* ]] || [[ $file == *"015"* ]]; then
        labels="$labels,epic-3"
    fi
    
    # Add type label
    if [[ $file == *"NF"* ]]; then
        labels="$labels,non-functional"
    else
        labels="$labels,feature"
    fi
    
    # Add component label
    if [[ $file == *"AI"* ]]; then
        labels="$labels,ai"
    elif [[ $file == *"BE"* ]]; then
        labels="$labels,backend"
    fi
    
    # Add priority
    if [[ $file == *"011"* ]] || [[ $file == *"012"* ]]; then
        labels="$labels,priority-should"
    else
        labels="$labels,priority-must"
    fi
    
    echo "$labels"
}

# Create issues
for issue_file in "${ISSUES[@]}"; do
    file_path="$BASE_DIR/$issue_file"
    
    if [ ! -f "$file_path" ]; then
        echo "❌ File not found: $file_path"
        continue
    fi
    
    echo "📝 Creating issue from: $issue_file"
    
    title=$(extract_title "$file_path")
    labels=$(get_labels "$issue_file")
    
    echo "   Title: $title"
    echo "   Labels: $labels"
    
    # Create the issue
    issue_url=$(gh issue create \
        --repo "$REPO_OWNER/$REPO_NAME" \
        --title "$title" \
        --body-file "$file_path" \
        --label "$labels")
    
    if [ $? -eq 0 ]; then
        echo "   ✅ Created: $issue_url"
        
        # Optional: Add to project
        # Uncomment if you want to automatically add to project
        # gh project item-add $PROJECT_NUMBER \
        #     --owner $REPO_OWNER \
        #     --url $issue_url
        
    else
        echo "   ❌ Failed to create issue"
    fi
    
    echo ""
    sleep 2  # Rate limiting
done

echo "================================================"
echo "✅ Issue creation completed!"
echo ""
echo "Next steps:"
echo "1. Review created issues on GitHub"
echo "2. Add issues to your GitHub Project if not done automatically"
echo "3. Set Start/End dates for roadmap planning"
```

### Make Script Executable
```bash
chmod +x create-backend-issues.sh
```

### Run the Script
```bash
# Update REPO_OWNER and REPO_NAME in the script first
./create-backend-issues.sh
```

## 📊 Manual Creation (Alternative)

If you prefer to create issues manually:

### Example for Issue #006
```bash
gh issue create \
  --repo your-username/bizplan-be-inclass \
  --title "[#006] REQ-FUNC-001-BE-001: 프로젝트 생성 및 템플릿 목록 API" \
  --body-file tasks/github-issues/006-REQ-FUNC-001-BE-001-PROJECT-CREATION-API.md \
  --label "Issue-Automation,epic-1,feature,backend,priority-must"
```

Repeat for issues #007-#015.

## 🏷️ Label Guide

### Epic Labels
- `epic-1`: Issues #006-#010 (Core Backend)
- `epic-2`: Issues #011-#012 (Special Features)
- `epic-3`: Issues #013-#015 (Non-Functional)

### Type Labels
- `feature`: Issues #006-#012
- `non-functional`: Issues #013-#015

### Component Labels
- `backend`: #006-#007, #009-#010, #012-#014
- `ai`: #008, #011
- `testing`: #015

### Priority Labels
- `priority-must`: #006-#010, #013-#015
- `priority-should`: #011-#012

## 📅 Adding to GitHub Project

After creating issues, add them to your GitHub Project:

```bash
# Get your project ID
gh project list --owner your-username

# Add issue to project
gh project item-add PROJECT_NUMBER \
  --owner your-username \
  --url ISSUE_URL
```

## 🗓️ Recommended Roadmap Schedule

Based on dependencies and execution order from `EXECUTION_ORDER.md`, here's the recommended schedule with specific dates:

### Phase 1: Core Backend Foundation (Week 1: Dec 16-20, 2025)

**Sequential Execution (Must be in order):**

| Issue | Title | Start Date | End Date | Duration | Depends On |
|-------|-------|------------|----------|----------|------------|
| #2 (#006) | Project Creation API | 2025-12-16 | 2025-12-18 | 3 days | None |
| #3 (#007) | Wizard Answer API | 2025-12-19 | 2025-12-20 | 2 days | #2 |

### Phase 2: AI Pipeline & Integration (Week 2-3: Dec 23-Jan 3, 2026)

**Parallel + Sequential:**

| Issue | Title | Start Date | End Date | Duration | Depends On | Parallel With |
|-------|-------|------------|----------|----------|------------|---------------|
| #4 (#008) | LLM Engine (FastAPI) | 2025-12-23 | 2025-12-27 | 5 days | #3 | - |
| #5 (#009) | Orchestration API | 2025-12-28 | 2026-01-02 | 5 days | #3, #4 | - |
| #6 (#010) | Export HWP/PDF | 2026-01-02 | 2026-01-03 | 2 days | #5 | - |

### Phase 3: Special Features (Week 4: Jan 6-10, 2026)

**✅ FULLY PARALLEL - Can run simultaneously:**

| Issue | Title | Start Date | End Date | Duration | Depends On | Parallel With |
|-------|-------|------------|----------|----------|------------|---------------|
| #7 (#011) | PMF Diagnosis AI | 2026-01-06 | 2026-01-08 | 3 days | #4 | #8 |
| #8 (#012) | Financial Engine | 2026-01-06 | 2026-01-10 | 5 days | #2 | #7 |

### Phase 4: Non-Functional Requirements & QA (Week 5: Jan 13-17, 2026)

**Parallel + Final Sequential:**

| Issue | Title | Start Date | End Date | Duration | Depends On | Parallel With |
|-------|-------|------------|----------|----------|------------|---------------|
| #9 (#013) | Security | 2026-01-13 | 2026-01-15 | 3 days | #2 | #10 |
| #10 (#014) | Monitoring | 2026-01-13 | 2026-01-15 | 3 days | #2 | #9 |
| #11 (#015) | Performance Test | 2026-01-16 | 2026-01-17 | 2 days | #5 | - |

### 📊 Visual Timeline

```
Week 1 (Dec 16-20):        [#006]--->[#007]
                              |
Week 2-3 (Dec 23-Jan 3):     +------>[#008]
                                         |
                                         +--->[#009]--->[#010]
                                         
Week 4 (Jan 6-10):                  [#011 || #012]
                                        (Parallel)
                                        
Week 5 (Jan 13-17):                [#013 || #014]--->[#015]
                                      (Parallel)        (Final)
```

### 🔗 Dependency Chain Summary

**Critical Path (Sequential):**
```
#006 → #007 → #008 → #009 → #010 → #015
```

**Parallel Opportunities:**
- **Phase 3**: #011 and #012 can run together
- **Phase 4**: #013 and #014 can run together

**Blockers:**
- ⚠️ #009 requires BOTH #007 AND #008 to be complete
- ⚠️ #015 should wait for all features (#009) to be done

---

## 🛠️ Setting Roadmap Dates in GitHub Project

### Step 1: Get Project and Field IDs

```bash
# Get your project ID
gh project list --owner your-username

# Get field IDs for Start and End dates
gh project field-list PROJECT_NUMBER --owner your-username
```

### Step 2: Apply Dates to Each Issue

**Phase 1 Issues:**

```bash
# Issue #2 (#006) - Project Creation API
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_2> \
  --field-id <START_FIELD_ID> --date "2025-12-16"
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_2> \
  --field-id <END_FIELD_ID> --date "2025-12-18"

# Issue #3 (#007) - Wizard Answer API (Depends on #2)
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_3> \
  --field-id <START_FIELD_ID> --date "2025-12-19"
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_3> \
  --field-id <END_FIELD_ID> --date "2025-12-20"
```

**Phase 2 Issues:**

```bash
# Issue #4 (#008) - LLM Engine
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_4> \
  --field-id <START_FIELD_ID> --date "2025-12-23"
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_4> \
  --field-id <END_FIELD_ID> --date "2025-12-27"

# Issue #5 (#009) - Orchestration (Depends on #007 AND #008)
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_5> \
  --field-id <START_FIELD_ID> --date "2025-12-28"
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_5> \
  --field-id <END_FIELD_ID> --date "2026-01-02"

# Issue #6 (#010) - Export
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_6> \
  --field-id <START_FIELD_ID> --date "2026-01-02"
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_6> \
  --field-id <END_FIELD_ID> --date "2026-01-03"
```

**Phase 3 Issues (Parallel):**

```bash
# Issue #7 (#011) - PMF Diagnosis
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_7> \
  --field-id <START_FIELD_ID> --date "2026-01-06"
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_7> \
  --field-id <END_FIELD_ID> --date "2026-01-08"

# Issue #8 (#012) - Financial Engine
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_8> \
  --field-id <START_FIELD_ID> --date "2026-01-06"
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_8> \
  --field-id <END_FIELD_ID> --date "2026-01-10"
```

**Phase 4 Issues:**

```bash
# Issue #9 (#013) - Security
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_9> \
  --field-id <START_FIELD_ID> --date "2026-01-13"
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_9> \
  --field-id <END_FIELD_ID> --date "2026-01-15"

# Issue #10 (#014) - Monitoring
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_10> \
  --field-id <START_FIELD_ID> --date "2026-01-13"
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_10> \
  --field-id <END_FIELD_ID> --date "2026-01-15"

# Issue #11 (#015) - Performance Test
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_11> \
  --field-id <START_FIELD_ID> --date "2026-01-16"
gh project item-edit --project-id <PROJECT_ID> --id <ITEM_ID_11> \
  --field-id <END_FIELD_ID> --date "2026-01-17"
```

## ✅ Verification Checklist

After creating all issues:
- [ ] 10 issues created (#006-#015)
- [ ] All issues have correct labels
- [ ] Issues are added to GitHub Project (if applicable)
- [ ] Issue descriptions are properly formatted
- [ ] Dependencies are clear in issue descriptions

## 📚 Reference

- **Execution Order**: See `EXECUTION_ORDER.md` for detailed phase planning
- **Issue Details**: Each markdown file in `tasks/github-issues/` contains full specifications
- **Excluded Issues**: #001-#005 (Frontend PoC - completed separately)

---

**Last Updated**: 2025-12-14  
**Active Issues**: 10 (#006-#015)  
**Excluded Issues**: 5 (#001-#005 - Frontend PoC)

