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

## 🗓️ Setting Roadmap Dates

To set Start and End dates for roadmap visualization:

```bash
# First, get field IDs
gh project field-list PROJECT_NUMBER --owner your-username

# Set dates (example)
gh project item-edit \
  --project-id PROJECT_NODE_ID \
  --id ITEM_ID \
  --field-id START_DATE_FIELD_ID \
  --date "2025-12-16"

gh project item-edit \
  --project-id PROJECT_NODE_ID \
  --id ITEM_ID \
  --field-id END_DATE_FIELD_ID \
  --date "2025-12-20"
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

