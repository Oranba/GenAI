# 🎯 **COMPLETE PRE-STAGING LOGS SCAN SETUP PROMPT**

## 📋 **Instructions for Team Members**

**Copy the entire section below (starting from "CURSOR CHAT SETUP PROMPT") and paste it into your Cursor Chat to automatically implement the complete pre-staging logging analysis system.**

---

## 🚀 **CURSOR CHAT SETUP PROMPT**

```markdown
Please implement a complete pre-staging logging analysis system for Java projects with cross-platform support. This system will automatically detect logging changes before staging and provide interactive quality analysis.

### **🚨 CRITICAL IDE CONSIDERATION:**

**MANDATORY REQUIREMENT**: This solution MUST work with Cursor/VSCode IDEs which use their **own internal Git implementation** that bypasses:
- Custom Git wrappers in `local-bin/git` 
- System Git hooks monitoring
- External process monitors

**Therefore, the solution MUST:**
- ✅ Intercept at **PRE-STAGING** time (BEFORE files enter staging area, NOT pre-commit)
- ✅ Work with **IDE internal Git** implementations (Cursor/VSCode GUI operations)
- ✅ Detect logging changes in working directory BEFORE staging occurs
- ✅ Block or pause staging operations until analysis is complete
- ✅ NOT rely on custom Git executable paths or wrappers
- ❌ **NOT pre-commit hooks** (wrong timing - happens AFTER staging)

### **IMPLEMENTATION REQUIREMENTS:**

Create the following files and configurations in the project:

#### **1. Cursor Rule File**
Create file: `.cursor/rules/logs-scan-rules.mdc`

```yaml
---
description: "Pre-staging analysis of logging statements before they enter staging area. Triggered by pre-commit detection."
globs:
  - "**/*.java"
alwaysApply: true
---

# Pre-Staging Log Analysis for Java Files

## AUTOMATIC TRIGGER ACTIVATION - PRE-STAGING DETECTION

**IMMEDIATE AUTO-START CHECK**: Check if trigger file exists and auto-start analysis:

**First, check for automatic trigger**: If `.git-pre-staging-logs-detected.trigger` file exists, **immediately start automatic analysis without waiting for user input**.

**STEP 1: PRE-STAGING DETECTION**
This analysis is automatically triggered when Java files with logging changes are about to be staged:

1. **Pre-commit hook detects logging changes** in files before staging using pattern matching
2. **If logging changes found**:
   - **Staging is PAUSED** until analysis is completed
   - Pre-commit hook creates `.git-pre-staging-logs-detected.trigger` file
   - **Auto-starts analysis immediately** in Cursor (no manual trigger needed)
   - Display: "🚨 **PRE-STAGING: LOGGING CHANGES DETECTED** - Starting analysis..."
3. **If no logging changes found**:
   - Staging proceeds normally without any prompts
4. **After analysis completion**:
   - Creates `pre-staging-analysis-completed.flag` to allow staging
   - Removes trigger files automatically
   - Allows staging to proceed

## AUTO-START IMPLEMENTATION

**CRITICAL**: At the beginning of this rule execution, immediately check for auto-trigger conditions:

1. **Check for trigger file**: Look for `.git-pre-staging-logs-detected.trigger` in the repository root
2. **If trigger file exists**:
   - **Auto-start analysis immediately** without waiting for user input
   - Read the list of files with logging changes from the trigger file
   - Display: "🚨 **PRE-STAGING TRIGGERED: LOGGING CHANGES DETECTED** - Starting automatic analysis..."
   - Proceed directly to the logging analysis workflow
3. **If no trigger file exists**:
   - **Check working directory** for Java files with logging changes
   - Use `git diff --name-only` to find modified Java files
   - For each modified Java file, check for logging patterns
   - **If logging changes found**:
     - Create trigger file automatically
     - Display: "🚨 **AUTO-DETECTED: Working directory has Java logging changes** - Starting analysis..."
     - Proceed with analysis workflow

## Analysis Scope and Task - WORKING DIRECTORY LOGS

**Task**: Analyze and fix **logging statements in modified Java files** in the working directory before they are staged.

**Scope**: Focus on **logging statements in Java files that have been modified** in the working directory and contain logging changes. This provides immediate feedback before code enters staging area.

## LOG ANALYSIS GUIDELINES - MANDATORY SYSTEM PROMPT COMPLIANCE

**CRITICAL REQUIREMENT**: All log analysis MUST follow the detailed guidelines specified in `prompt/log_analyzer_system_prompt.md`:

**STEP-BY-STEP COMPLIANCE PROCESS:**

1. **ALWAYS READ SYSTEM PROMPT FIRST**: Before analyzing any log, carefully review `prompt/log_analyzer_system_prompt.md` to understand:
   - Complete 1-10 rating scale with characteristics and examples
   - All evaluation criteria (Message Clarity, Contextual Information, etc.)
   - Special considerations (MDC handling, rating guidelines)
   - Illustrative examples for each rating level

2. **Rating Scale Compliance**: Use the exact 1-10 rating scale defined in the system prompt:
   - **1-2: Poor** - Vague, unclear messages with no contextual information
   - **3-4: Below Average** - Basic information with minimal context
   - **5-6: Average** - Somewhat informative with basic context
   - **7-8: Good** - Clear, informative messages with good context
   - **9-10: Excellent** - Highly detailed and context-rich

3. **Evaluation Criteria**: Apply ALL criteria from the system prompt:
   - Message Clarity and Readability
   - Contextual Information
   - Appropriate Log Levels
   - Exception Handling Best Practices
   - Performance Considerations
   - Structured Logging
   - Traceability and Correlation

4. **Special Considerations**: Follow system prompt guidelines:
   - **Do NOT penalize logs** for missing MDC context (correlationId, userId, etc.)
   - Focus on the explicit content of the log message itself
   - All ratings must be **whole numbers** (rounded to zero decimal places)

5. **RECOMMENDATION VERIFICATION**: Before providing any recommendation:
   - **MANDATORY**: Verify the recommendation is appropriate for the SPECIFIC logging issue
   - **DISTINGUISH**: Test/dummy logs vs. real business logs requiring improvement
   - **CONTEXT-AWARE**: Ensure fixes align with the actual business logic and method purpose
   - **QUALITY CHECK**: Confirm recommendations follow system prompt examples and standards

6. **POST-FIX RATING REQUIREMENT**: For all recommendations that suggest **REAL LOG IMPROVEMENTS** (not removals):
   - **MANDATORY**: Provide the expected rating (1-10) AFTER applying the recommended fix
   - **SHOW IMPROVEMENT**: Demonstrate the quality progression (e.g., "Current: 3/10 → After Fix: 8/10")
   - **VALIDATE UPGRADE**: Ensure the recommended fix actually achieves the projected rating

## Data Collection Strategy - NEW/MODIFIED LOGS ONLY

1. **Extract Modified Files**: Identify **Java files with modifications** in working directory using:
   - `git diff --name-only` to get modified files
   - Filter for `.java` files

2. **Extract Only New/Modified Logging**: For each modified Java file, use `git diff [file]` to identify:
   - **NEW logs**: Lines starting with `+` containing logging patterns: `^\+.*logger\.(debug|info|warn|error|fatal|trace)|^\+.*log\.(debug|info|warn|error|fatal|trace)|^\+.*System\.(out|err)\.print`
   - **SKIP existing unchanged logs**: Do not analyze any logging statements that are not in the git diff output
   - **SKIP deleted logs**: Ignore lines starting with `-` containing logging patterns since they're being removed

3. **Analyze Only Changes**: For each **new/modified** logging statement only:
   - Extract the specific new/modified log line from git diff
   - **MANDATORY**: Apply the 1-10 rating scale from `prompt/log_analyzer_system_prompt.md`
   - **MANDATORY**: Use ALL evaluation criteria from the system prompt
   - **MANDATORY**: Follow the exact rating characteristics and examples from the system prompt
   - Rate only these changed logging statements using the system prompt guidelines
   - **CRITICAL**: Do NOT analyze any existing unchanged logging statements in the file
   - **CRITICAL**: If only deleted logs found (lines starting with `-`), skip analysis entirely and allow staging immediately

## MANDATORY PROGRESS VISUALIZATION - CRITICAL STEP

**⚠️ CRITICAL REQUIREMENT: THIS STEP MUST NEVER BE SKIPPED ⚠️**

**BEFORE** proceeding with log quality analysis, **ALWAYS** display the analysis progress visualization to provide user feedback:

**MANDATORY DISPLAY FORMAT:**
```
🧹 Found [X] New Logging Statement(s) for Analysis

| 🔍 ANALYZING NEW/MODIFIED LOGGING STATEMENTS |
| ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓ |
| |
| 📁 File: [FileName] |
| 📊 New logging statements: [Count] |
| ⚖️ Analyzing quality using system prompt criteria... |
```

**WHEN TO DISPLAY:**
- **IMMEDIATELY** after completing data collection and identifying new/modified log statements
- **BEFORE** starting any log quality analysis or user interaction
- **FOR EVERY** logs scan execution, regardless of the number of logs found

**PURPOSE:**
- Provides transparent feedback that analysis is actively running
- Shows which files are being processed
- Indicates the scope of analysis (number of log statements)
- Serves as confirmation that the system detected logging changes correctly

**ENFORCEMENT:**
- This visualization step is **MANDATORY** and must be included in every analysis workflow
- Failure to display this visualization constitutes a critical workflow violation
- This step ensures proper user experience and workflow transparency

## Interactive User Engagement for Low-Quality Logs

**CRITICAL REQUIREMENT**: For each log statement with a rating lower than 7 (ratings 1-6):
- **MANDATORY**: Apply rating based on `prompt/log_analyzer_system_prompt.md` criteria
- **Pause and prompt the user** with the specific logging issue found
- **Present the problematic log** with its current rating and identified issues
- **Provide specific recommendations** for improvement based on system prompt examples and guidelines
- **Ensure recommendations align** with the quality standards defined in the system prompt
- **Ask for user confirmation or input** on the proposed fix before proceeding
- **Wait for user response** before moving to the next low-rated log statement

**Prompting Format for Low-Rated Logs**:
```
🔍 **Log Quality Issue Found** (Rating: X/10)

**Class**: [ClassName]
**Method**: [MethodName]
**Line**: [LineNumber]
**Status**: [MODIFIED FILE - PRE-STAGING]

📋 **CURRENT LOG**:
```java
[CurrentLogStatement]
```

❌ **Issues Identified**:
- [Issue 1]
- [Issue 2] 
- [Issue 3]

✅ **RECOMMENDED FIX**:
```java
[ProposedImprovedLogStatement]
```

📊 **QUALITY IMPROVEMENT**: Current: X/10 → After Fix: Y/10

📝 **Why This Improvement Is Better**:
[DetailedExplanationOfImprovements]

🔧 **System Prompt Compliance**: 
- ✅ Verified against `prompt/log_analyzer_system_prompt.md` criteria
- ✅ Recommendation appropriate for this specific logging issue
- ✅ Expected rating validated against system prompt examples

🎯 **Your Decision**:
Please choose one option:

**A) ✅ APPROVE** - Apply this recommended fix
**B) ❌ DECLINE** - Keep the current log as-is  
**C) 🔧 MODIFY** - I want to provide my own improved version
**D) ❓ EXPLAIN** - Tell me more about why this improvement is needed
**R) 🔄 REMOVE** - Remove this log completely

**Type A, B, C, D, or R to proceed with this log statement.**
```

**SPECIAL CASE - TEST/DUMMY LOGS**:
For logs that are clearly test/placeholder content (containing "test", "dummy", "TODO", etc.):
- **RECOMMENDED ACTION**: Complete removal
- **NO QUALITY IMPROVEMENT RATING**: Not applicable for removal cases
- **RATIONALE**: Test artifacts should not be committed to production code

## Expected Output

Create an HTML report: `pre_staging_log_analysis_summary.html` that contains all log fixes which have been done.

**Location**: Save to the `logAnalysisOutput/` directory.

## Post-Analysis Actions

**COMPLETION ACTIONS**: After completing the log analysis and generating the HTML report, automatically allow staging to proceed.

**AUTOMATIC STAGING UNBLOCKING**: 
```bash
# Create completion flag to allow staging
touch pre-staging-analysis-completed.flag

# Clean up trigger files
rm -f .git-pre-staging-logs-detected.trigger
```

Please proceed with the analysis focusing specifically on **logging statements in modified Java files** in the working directory and generating the comprehensive HTML report as specified above.
```

#### **2. Log Analyzer System Prompt**
Create file: `prompt/log_analyzer_system_prompt.md`

```markdown
# Log Analyzer System Prompt

## Overview
You are an expert log analyzer tasked with evaluating the quality of logging entries in Java applications for pre-staging analysis. Your primary goal is to assess how effectively each log entry supports debugging, monitoring, and flow traceability.

## Rating Scale (1-10)

### 1-2: Poor
**Characteristics:**
- Vague, unclear messages
- No contextual information
- Generic or uninformative content
- Hinders rather than helps debugging

**Illustrative Examples (NOT from target project):**
- ❌ `"Error occurred"`
- ❌ `"Processing data"`
- ❌ `"Method called"`

### 3-4: Below Average
**Characteristics:**
- Basic information with minimal context
- Missing key identifiers or parameters
- Limited debugging value

**Illustrative Examples (NOT from target project):**
- ❌ `"User not found"`
- ❌ `"Validation failed"`

### 5-6: Average
**Characteristics:**
- Somewhat informative with basic context
- Includes some relevant details
- Could be enhanced with additional information

**Illustrative Examples (NOT from target project):**
- ⚠️ `"Processing user request for productId: 12345"`
- ⚠️ `"Database connection failed, retrying..."`

### 7-8: Good
**Characteristics:**
- Clear, informative messages with good context
- Includes relevant identifiers and state information
- Aids in debugging and monitoring

**Illustrative Examples (NOT from target project):**
- ✅ `"Processing product offering configuration for userId: abc123, productId: 12345, configurationId: config_001"`
- ✅ `"Failed to retrieve product data from cache, falling back to database. ProductId: 12345, cacheKey: product_cache_12345"`

### 9-10: Excellent
**Characteristics:**
- Highly detailed and context-rich
- Comprehensive debugging information
- Well-structured and formatted
- Significantly aids in troubleshooting and flow tracing

**Illustrative Examples (NOT from target project):**
- ✅ `"Successfully processed product offering configuration - userId: abc123, productId: 12345, configurationId: config_001, processingTime: 250ms, resultStatus: COMPLETED"`
- ✅ `"Exception during product validation - userId: abc123, productId: 12345, validationRule: PRICE_CONSTRAINT, input: {price: 99.99, currency: USD}, error: Price exceeds maximum allowed value of 89.99"`

## Evaluation Criteria

### 1. **Message Clarity and Readability**
- Is the log message clear and easy to understand?
- Does it use consistent terminology and formatting?
- Is it free from typos and grammatical errors?

### 2. **Contextual Information**
- Does it include relevant identifiers (user IDs, transaction IDs, entity IDs)?
- Are input parameters and state information provided?
- Is there sufficient context to understand the operation being performed?

### 3. **Appropriate Log Levels**
- **DEBUG**: Detailed diagnostic information for development
- **INFO**: General informational messages about application flow
- **WARN**: Potentially harmful situations that don't break functionality
- **ERROR**: Error events that might still allow the application to continue
- **FATAL**: Critical errors that may cause application termination

### 4. **Exception Handling Best Practices**
- Are exceptions logged with appropriate severity levels?
- Is the full exception message and stack trace included when relevant?
- Is there sufficient context about what operation was being performed when the exception occurred?

### 5. **Performance Considerations**
- Are performance metrics included where relevant (execution time, resource usage)?
- Is conditional logging used to avoid unnecessary overhead?
- Is the logging volume appropriate (not excessive, not insufficient)?

### 6. **Structured Logging**
- Does the log use consistent formatting?
- Are key-value pairs used effectively?
- Is the message structure conducive to parsing and analysis?

### 7. **Traceability and Correlation**
- Can the log be correlated with other system events?
- Does it support end-to-end request tracing?
- Are there sufficient breadcrumbs to follow the execution flow?

## Special Considerations

### MDC Context Handling
- **Do NOT penalize logs** for missing MDC context (correlationId, userId, etc.)
- Assume MDC values may be set globally and not visible in individual log messages
- Focus on the explicit content of the log message itself

### Rating Guidelines
- All ratings must be **whole numbers** (rounded to zero decimal places)
- Consider the log's effectiveness in real-world debugging scenarios
- Evaluate based on the specific context and purpose of each log entry

## Pre-Staging Analysis Focus

Since this is pre-staging analysis:
- Focus on files in the working directory that are about to be staged
- Provide immediate, actionable feedback for developers
- Emphasize improvements that can be made before code enters version control
- Consider the development workflow impact of suggested changes
```

#### **3. VSCode/Cursor Settings Configuration**
Create file: `.vscode/settings.json`

```json
{
  "git.path": "${workspaceFolder}/local-bin/git",
  "git.terminalAuthentication": false,
  "git.useCommitInputAsStashMessage": true,
  "git.enabled": true,
  "git.autoRepositoryDetection": true
}
```

#### **4. Custom Git Wrapper**
Create directory: `local-bin/` and file: `local-bin/git` (make it executable)

```bash
#!/bin/bash

# Git wrapper that intercepts staging operations for Cursor
# This should be called by any Git client, including Cursor

REAL_GIT="/opt/homebrew/bin/git"  # Adjust path for your system

# Function to check for logging in files
check_logging_in_files() {
    local files="$1"
    local logging_files=""
    
    while IFS= read -r file; do
        if [ -f "$file" ] && [[ "$file" =~ \.java$ ]]; then
            if grep -q "logger\.\(debug\|info\|warn\|error\|fatal\|trace\)" "$file" 2>/dev/null; then
                logging_files="${logging_files}${file}"$'\n'
            elif grep -q "log\.\(debug\|info\|warn\|error\|fatal\|trace\)" "$file" 2>/dev/null; then
                logging_files="${logging_files}${file}"$'\n'
            elif grep -q "System\.\(out\|err\)\.print" "$file" 2>/dev/null; then
                logging_files="${logging_files}${file}"$'\n'
            fi
        fi
    done <<< "$files"
    
    echo "$logging_files"
}

# Function to show staging dialog
show_staging_dialog() {
    local file_count="$1"
    
    # Copy command to clipboard
    echo "@logs-scan-rules.mdc" | pbcopy 2>/dev/null || echo "@logs-scan-rules.mdc" | xclip -selection clipboard 2>/dev/null || echo "@logs-scan-rules.mdc" | clip 2>/dev/null
    
    if command -v osascript >/dev/null 2>&1; then
        # macOS Dialog
        DIALOG_RESULT=$(osascript -e "
        try
            set userChoice to display dialog \"STAGE CHANGES: LOGGING DETECTED\" & return & return & \"Found logging statements in $file_count Java file(s).\" & return & \"Quality analysis required before staging.\" & return & return & \"Choose how to proceed:\" buttons {\"Cancel Staging\", \"Manual Steps\", \"Auto-Run Analysis\"} default button \"Auto-Run Analysis\" with title \"Pre-Staging Log Quality Check\" with icon 2
            return button returned of userChoice
        on error
            return \"Manual Steps\"
        end try
        ")
        
        case "$DIALOG_RESULT" in
            "Auto-Run Analysis")
                # Try automation
                osascript -e "
                try
                    tell application \"Cursor\" to activate
                    delay 1
                    tell application \"System Events\"
                        keystroke \"l\" using {command down}
                        delay 1.5
                        keystroke \"v\" using {command down}
                        delay 0.5
                        key code 36
                    end tell
                end try
                " >/dev/null 2>&1
                echo "✅ @logs-scan-rules.mdc sent to Cursor chat!"
                echo "📋 Complete analysis, then try staging again"
                return 1  # Block the staging operation
                ;;
            "Manual Steps")
                echo "📋 Manual steps required:"
                echo "1. Open Cursor Chat (Cmd+L or Ctrl+L)"
                echo "2. Paste: @logs-scan-rules.mdc"
                echo "3. Complete analysis"
                echo "4. Try staging again"
                return 1  # Block the staging operation
                ;;
            *)
                echo "❌ Staging cancelled by user"
                return 1  # Block the staging operation
                ;;
        esac
    elif command -v powershell.exe >/dev/null 2>&1 || command -v powershell >/dev/null 2>&1; then
        # Windows Dialog
        DIALOG_RESULT=$(powershell.exe -Command "
        Add-Type -AssemblyName System.Windows.Forms
        \$result = [System.Windows.Forms.MessageBox]::Show('Found logging statements in $file_count Java file(s).`n`nQuality analysis required before staging.`n`nYes: Auto-run analysis`nNo: Manual steps`nCancel: Cancel staging', 'Pre-Staging Log Quality Check', 'YesNoCancel', 'Question')
        if (\$result -eq 'Yes') { 'Auto-Run Analysis' }
        elseif (\$result -eq 'No') { 'Manual Steps' }
        else { 'Cancel Staging' }
        " 2>/dev/null || echo "Manual Steps")
        
        case "$DIALOG_RESULT" in
            "Auto-Run Analysis")
                # Windows automation attempt
                powershell.exe -Command "
                try {
                    Add-Type -AssemblyName System.Windows.Forms
                    [System.Windows.Forms.Clipboard]::SetText('@logs-scan-rules.mdc')
                    \$cursor = Get-Process | Where-Object {\$_.ProcessName -like '*Cursor*'} | Select-Object -First 1
                    if (\$cursor) {
                        [Microsoft.VisualBasic.Interaction]::AppActivate(\$cursor.Id)
                        Start-Sleep -Milliseconds 1000
                        [System.Windows.Forms.SendKeys]::SendWait('^l')
                        Start-Sleep -Milliseconds 1500
                        [System.Windows.Forms.SendKeys]::SendWait('^v')
                        Start-Sleep -Milliseconds 500
                        [System.Windows.Forms.SendKeys]::SendWait('{ENTER}')
                    }
                } catch {}
                " >/dev/null 2>&1
                echo "✅ @logs-scan-rules.mdc sent to Cursor chat!"
                echo "📋 Complete analysis, then try staging again"
                return 1
                ;;
            *)
                echo "📋 Manual steps or cancellation"
                return 1
                ;;
        esac
    elif command -v zenity >/dev/null 2>&1; then
        # Linux Zenity Dialog
        if zenity --question --title="Pre-Staging Log Quality Check" --text="Found logging statements in $file_count Java file(s).\n\nQuality analysis required before staging.\n\nClick Yes to auto-run analysis, No for manual steps." 2>/dev/null; then
            # Linux automation attempt
            if command -v xdotool >/dev/null 2>&1; then
                CURSOR_WINDOW=$(xdotool search --name "Cursor" | head -1)
                if [ -n "$CURSOR_WINDOW" ]; then
                    xdotool windowactivate "$CURSOR_WINDOW"
                    sleep 1
                    xdotool key ctrl+l
                    sleep 1.5
                    xdotool key ctrl+v
                    sleep 0.5
                    xdotool key Return
                fi
            fi
            echo "✅ @logs-scan-rules.mdc sent to Cursor chat!"
            echo "📋 Complete analysis, then try staging again"
        fi
        return 1
    else
        echo "📋 Logging changes detected - please run @logs-scan-rules.mdc in Cursor chat"
        return 1
    fi
}

# Main logic - intercept git add operations
if [ "$1" = "add" ]; then
    # Get files being added
    shift  # Remove 'add' from arguments
    FILES_TO_ADD="$@"
    
    # Convert arguments to file list
    FILE_LIST=""
    for arg in $FILES_TO_ADD; do
        if [ -f "$arg" ]; then
            FILE_LIST="${FILE_LIST}${arg}"$'\n'
        elif [ -d "$arg" ]; then
            # Directory - get modified files in directory
            while IFS= read -r file; do
                [ -n "$file" ] && [ -f "$file" ] && FILE_LIST="${FILE_LIST}${file}"$'\n'
            done <<< "$($REAL_GIT diff --name-only -- "$arg")"
        fi
    done
    
    # If no specific files, get all modified files
    if [ -z "$FILE_LIST" ] && [ -z "$FILES_TO_ADD" ]; then
        while IFS= read -r file; do
            [ -n "$file" ] && [ -f "$file" ] && FILE_LIST="${FILE_LIST}${file}"$'\n'
        done <<< "$($REAL_GIT diff --name-only)"
    fi
    
    # Check for logging in the files
    LOGGING_FILES=$(check_logging_in_files "$FILE_LIST")
    
    if [ -n "$LOGGING_FILES" ]; then
        # Count files with logging
        FILE_COUNT=$(echo -n "$LOGGING_FILES" | grep -c '^' 2>/dev/null || echo "0")
        
        echo "🔍 Pre-staging check: Found logging statements in $FILE_COUNT Java file(s)"
        
        # Create trigger file for analysis
        echo -e "$LOGGING_FILES" > .git-pre-staging-logs-detected.trigger
        echo "$(date '+%Y-%m-%d %H:%M:%S')" >> .git-pre-staging-logs-detected.trigger
        
        # Show dialog and handle response
        if show_staging_dialog "$FILE_COUNT"; then
            # User approved, proceed with staging
            exec "$REAL_GIT" add $FILES_TO_ADD
        else
            # User cancelled or needs to do analysis first
            exit 1
        fi
    fi
fi

# For all other git operations, just pass through
exec "$REAL_GIT" "$@"
```

#### **5. Create Required Directories and Setup**
```bash
# Create necessary directories
mkdir -p logAnalysisOutput
mkdir -p prompt
mkdir -p local-bin

# Make the git wrapper executable
chmod +x local-bin/git

# Update the git wrapper path for your system
# Edit local-bin/git and change REAL_GIT path:
# - macOS (Homebrew): REAL_GIT="/opt/homebrew/bin/git"
# - macOS (System): REAL_GIT="/usr/bin/git"  
# - Linux: REAL_GIT="/usr/bin/git"
# - Windows (Git Bash): REAL_GIT="/mingw64/bin/git"
```

#### **6. Platform-Specific Git Path Setup**
```bash
# Find your git path and update the wrapper
which git
# Copy the output and update REAL_GIT in local-bin/git
```

#### **7. CRITICAL: Update .gitignore**
**🚨 MANDATORY STEP - Add solution files to .gitignore to prevent committing temporary files:**

```bash
# Add the following lines to your .gitignore file:

# Pre-Staging Log Analysis Solution Files
# Trigger and flag files created during pre-staging analysis
*.trigger
*.flag
*-staging-analysis-completed.flag
.git-pre-staging-logs-detected.trigger
.git-logging-changes-detected.trigger

# Monitor and log files
logs-scan-monitor.log
*monitor*.log

# Custom Git hooks and wrappers (solution-specific)
.git/hooks/git-add-wrapper
.git/hooks/index-monitor.sh
.git/hooks/index-monitor.sh.backup
.git/hooks/pre-index
.git/setup-pre-staging.sh

# Custom Git wrapper
local-bin/git

# VSCode/Cursor settings for Git wrapper
.vscode/settings.json

# Log Analysis Output (if not already present)
logAnalysisOutput/
```

**⚠️ CRITICAL IMPORTANCE:**
- These files are temporary runtime artifacts and should NEVER be committed
- Trigger files (*.trigger, *.flag) control the analysis workflow state
- Git hooks are environment-specific and shouldn't be shared
- Monitor logs contain local process information
- Failing to add these to .gitignore will cause workflow conflicts and merge issues

#### **8. Setup Git Hooks and Background Monitor**
**🔧 CRITICAL SETUP - Configure Git hooks and background monitoring:**

```bash
# Create Git hooks directory structure
mkdir -p .git/hooks

# Copy the Git wrapper functionality to proper Git hooks
# Note: The local-bin/git wrapper handles most functionality
# But we need a background monitor for real-time detection

# Create the background monitor script
cat > .git/hooks/index-monitor.sh << 'EOF'
#!/bin/bash
# Background monitor for Git staging operations
# This script monitors .git/index for changes and triggers dialogs

REPO_ROOT="$(git rev-parse --show-toplevel 2>/dev/null || pwd)"
INDEX_FILE="$REPO_ROOT/.git/index"
LAST_MOD_TIME=""

echo "🔍 Starting Git index monitor for pre-staging analysis..."
echo "📁 Monitoring: $INDEX_FILE"
echo "📝 Monitor log: $REPO_ROOT/logs-scan-monitor.log"

while true; do
    if [ -f "$INDEX_FILE" ]; then
        CURRENT_MOD_TIME=$(stat -f "%m" "$INDEX_FILE" 2>/dev/null || stat -c "%Y" "$INDEX_FILE" 2>/dev/null)
        
        if [ -n "$CURRENT_MOD_TIME" ] && [ "$CURRENT_MOD_TIME" != "$LAST_MOD_TIME" ]; then
            echo "$(date '+%Y-%m-%d %H:%M:%S'): Git index changed - checking for logging in staged files" >> "$REPO_ROOT/logs-scan-monitor.log"
            
            # CRITICAL FIX: Skip analysis during pull/merge/rebase operations
            if [ -f "$REPO_ROOT/.git/MERGE_HEAD" ] || [ -f "$REPO_ROOT/.git/CHERRY_PICK_HEAD" ] || [ -f "$REPO_ROOT/.git/REBASE_HEAD" ] || [ -d "$REPO_ROOT/.git/rebase-merge" ] || [ -d "$REPO_ROOT/.git/rebase-apply" ]; then
                echo "$(date '+%Y-%m-%d %H:%M:%S'): Skipping log analysis - git pull/merge/rebase operation in progress" >> "$REPO_ROOT/logs-scan-monitor.log"
                LAST_MOD_TIME="$CURRENT_MOD_TIME"
                continue
            fi
            
            # Check staged files for logging patterns
            STAGED_JAVA_FILES=$(git diff --cached --name-only | grep '\.java$' || true)
            
            if [ -n "$STAGED_JAVA_FILES" ]; then
                LOGGING_DETECTED=false
                
                while IFS= read -r file; do
                    if [ -f "$file" ]; then
                        if git diff --cached "$file" | grep -iE "^\+.*(logger|LOGGER)\.(debug|info|warn|error|fatal|trace)" >/dev/null 2>&1; then
                            LOGGING_DETECTED=true
                            break
                        elif git diff --cached "$file" | grep -iE "^\+.*(log|LOG)\.(debug|info|warn|error|fatal|trace)" >/dev/null 2>&1; then
                            LOGGING_DETECTED=true
                            break
                        elif git diff --cached "$file" | grep -iE "^\+.*System\.(out|err)\.print" >/dev/null 2>&1; then
                            LOGGING_DETECTED=true
                            break
                        fi
                    fi
                done <<< "$STAGED_JAVA_FILES"
                
                if [ "$LOGGING_DETECTED" = true ]; then
                    # Create trigger file
                    echo -e "$STAGED_JAVA_FILES" > "$REPO_ROOT/.git-pre-staging-logs-detected.trigger"
                    echo "$(date '+%Y-%m-%d %H:%M:%S')" >> "$REPO_ROOT/.git-pre-staging-logs-detected.trigger"
                    
                    # Show dialog based on platform
                    FILE_COUNT=$(echo -n "$STAGED_JAVA_FILES" | grep -c '^' 2>/dev/null || echo "0")
                    
                    if command -v osascript >/dev/null 2>&1; then
                        # macOS
                        DIALOG_RESULT=$(osascript -e "
                        try
                            set userChoice to display dialog \"✨ Code Quality Assistant\" & return & return & \"We've detected logging changes in $FILE_COUNT Java file(s) that could benefit from a quick quality review.\" & return & return & \"To maintain high code standards, we'd like to run a brief analysis to ensure your logs are optimized for debugging and monitoring.\" & return & return & \"How would you like to proceed?\" buttons {\"Skip Analysis\", \"Manual Steps\", \"Run Quality Check\"} default button \"Run Quality Check\" with title \"Pre-Staging Log Quality Assistant\" with icon 1
                            return button returned of userChoice
                        on error
                            return \"Manual Steps\"
                        end try
                        ")
                        
                        case "$DIALOG_RESULT" in
                            "Run Quality Check")
                                osascript -e "
                                try
                                    set the clipboard to \"@logs-scan-rules.mdc\"
                                    -- Force Cursor to foreground and ensure it's the active application
                                    tell application \"Cursor\" to activate
                                    delay 1
                                    
                                    -- Open Chat with explicit focus handling
                                    tell application \"System Events\"
                                        tell process \"Cursor\"
                                            -- Ensure Cursor window is frontmost
                                            set frontmost to true
                                            delay 0.5
                                            
                                            -- Open Chat (Cmd+L)
                                            keystroke \"l\" using {command down}
                                            delay 1.5
                                            
                                            -- Additional safety: Click in chat input area if possible
                                            -- This helps ensure focus is in chat, not editor
                                            try
                                                click at {400, 500}
                                                delay 0.5
                                            end try
                                            
                                            -- Paste the rule reference
                                            keystroke \"v\" using {command down}
                                            delay 0.5
                                            
                                            -- Send with multiple Enter presses for reliability
                                            keystroke return
                                            delay 0.5
                                            keystroke return
                                            delay 0.5
                                            keystroke return
                                        end tell
                                    end tell
                                end try
                                " >/dev/null 2>&1
                                ;;
                        esac
                    fi
                    
                    echo "$(date '+%Y-%m-%d %H:%M:%S'): Logging analysis triggered" >> "$REPO_ROOT/logs-scan-monitor.log"
                fi
            fi
            
            LAST_MOD_TIME="$CURRENT_MOD_TIME"
        fi
    fi
    
    sleep 2
done
EOF

# Make the monitor script executable
chmod +x .git/hooks/index-monitor.sh

# Start the background monitor
nohup .git/hooks/index-monitor.sh > logs-scan-monitor.log 2>&1 &
echo $! > .git/hooks/monitor.pid

echo "✅ Background monitor started (PID: $(cat .git/hooks/monitor.pid))"
echo "📊 Monitor status: ps aux | grep index-monitor"
```

#### **9. Platform-Specific Git Path Configuration**
**🔧 IMPORTANT - Configure your specific Git executable path:**

```bash
# Find your system's Git path
echo "Your Git path: $(which git)"

# Update the wrapper with your system's Git path
# Edit local-bin/git and update the REAL_GIT variable:

# For macOS (Homebrew): 
sed -i '' 's|REAL_GIT="/opt/homebrew/bin/git"|REAL_GIT="'$(which git)'"|' local-bin/git

# For macOS (System) or Linux:
sed -i '' 's|REAL_GIT="/opt/homebrew/bin/git"|REAL_GIT="/usr/bin/git"|' local-bin/git

# For Windows (Git Bash):
sed -i 's|REAL_GIT="/opt/homebrew/bin/git"|REAL_GIT="/mingw64/bin/git"|' local-bin/git

echo "✅ Git wrapper configured for your system"
```

### **VERIFICATION STEPS:**

After implementation, test the system step-by-step:

#### **Step 1: Verify File Structure**
```bash
# Confirm all files are created correctly
ls -la .cursor/rules/logs-scan-rules.mdc
ls -la prompt/log_analyzer_system_prompt.md
ls -la local-bin/git
ls -la .vscode/settings.json
ls -la .git/hooks/index-monitor.sh
ps aux | grep index-monitor  # Verify background monitor is running
```

#### **Step 1.5: CRITICAL - Verify Case-Insensitive Logging Detection and Single Monitor Process**
**🚨 ESSENTIAL STEP - This must be verified before proceeding:**

```bash
# CRITICAL FIX 1: Verify case-insensitive logging detection
echo "🔍 Testing case-insensitive logging detection..."

# Test with uppercase LOGGER (the fix we discovered)
echo 'LOGGER.error("Test uppercase logging");' > TestCaseDetection.java
git add TestCaseDetection.java
git diff --cached TestCaseDetection.java | grep -iE "^\+.*(logger|LOGGER)\.(debug|info|warn|error|fatal|trace)"

# Should return the line - if empty, detection is broken
if [ $? -eq 0 ]; then
    echo "✅ Case-insensitive detection working correctly"
else
    echo "❌ CRITICAL: Case-insensitive detection FAILED - update the regex patterns!"
fi

# Clean up test
git reset HEAD TestCaseDetection.java
rm TestCaseDetection.java

# CRITICAL FIX 2: Verify single monitor process with polite dialog
echo "🔍 Checking monitor processes..."
ps aux | grep index-monitor

# If you see multiple processes, kill old ones and restart:
# Kill all existing monitor processes
killall -f index-monitor.sh 2>/dev/null
pkill -f "index-monitor" 2>/dev/null

# Wait a moment for processes to terminate
sleep 3

# Start fresh monitor with polite dialog and case-insensitive detection
nohup .git/hooks/index-monitor.sh > logs-scan-monitor.log 2>&1 &
echo $! > .git/hooks/monitor.pid

# Verify only one monitor process is running
echo "✅ Monitor processes running:"
ps aux | grep index-monitor | grep -v grep

# Verify the monitor log shows recent activity
echo "📊 Recent monitor activity:"
tail -5 logs-scan-monitor.log
```

**🔍 CRITICAL VALIDATION:**
- **MUST see only ONE monitor process** with recent timestamp
- **MUST NOT see multiple processes** from different start times
- **Monitor MUST use polite dialog version** ("✨ Code Quality Assistant")
- **Monitor MUST detect both lowercase `logger.` AND uppercase `LOGGER.`** patterns

#### **Step 1.6: CRITICAL - Test Cursor GUI Staging Detection**
**🚨 ESSENTIAL - Verify background monitor detects Cursor's GUI staging operations:**

```bash
# Create a test Java file with logging
echo 'public class TestStaging {
    private static final Logger logger = LoggerFactory.getLogger(TestStaging.class);
    
    public void testMethod() {
        logger.info("Test logging statement for staging detection");
    }
}' > TestCursorStaging.java

# Verify file is detected as modified
git status

echo "🎯 CRITICAL TEST: Now perform these steps manually:"
echo "1. ✋ Do NOT use terminal git commands"
echo "2. 📱 Right-click on TestCursorStaging.java in Cursor's file explorer"
echo "3. 🔄 Select 'Stage Changes' from the context menu"
echo "4. 📋 Verify polite dialog appears: '✨ Code Quality Assistant'"
echo "5. ✅ Verify dialog message mentions logging changes detected"
echo "6. 🚫 Click 'Skip Analysis' or 'Manual Steps' to test dialog dismissal"
echo ""
echo "⚠️  If dialog does NOT appear, the background monitor is not properly detecting Cursor's GUI operations!"
echo "⚠️  This indicates a critical configuration issue that must be resolved."
```

**🔧 Expected Result:**
- **Dialog MUST appear** when staging via Cursor GUI
- **Dialog MUST show polite message** ("✨ Code Quality Assistant")
- **Dialog MUST NOT show harsh message** ("STAGING BLOCKED: LOGGING DETECTED")
- **"Run Quality Check" button MUST actually send the message** (double Enter press)

**🚨 CRITICAL AUTOMATION FIX:**
The automation script includes these essential fixes:
- **Explicit process targeting**: Uses "tell process Cursor" for reliable window focus
- **Chat focus verification**: Ensures Cursor Chat is active, not editor window
- **Safety click mechanism**: Attempts to click chat input area to prevent source code corruption  
- **Triple Enter press**: Multiple Enter presses ensure message is reliably sent to AI
- **Increased delays**: `delay 3` for app activation, `delay 4` for chat loading, `delay 3` for paste completion
- **Frontmost window handling**: Explicitly sets Cursor as frontmost application
- **Case-insensitive detection**: Detects both `logger.` and `LOGGER.` patterns

**⚠️ CRITICAL WARNING**: The automation must paste into Cursor Chat, NOT source code files!

**🚨 If dialog doesn't appear when using Cursor GUI:**
**ROOT CAUSE**: Cursor/VSCode use internal Git implementation that bypasses:
- Custom Git wrappers (`local-bin/git`)
- Background process monitors
- VSCode `git.path` settings

**SOLUTION**: Must intercept at **PRE-STAGING** level that works with IDE Git:
1. **Monitor working directory changes** before staging begins
2. **Intercept staging operations** at the moment they're triggered
3. **Block staging until analysis complete** - files should NOT enter staging area until approved
4. **Test with Cursor GUI "Stage Changes"** operations, not commit operations
5. **Focus on staging-time detection** (before staging), NOT commit-time

#### **Step 2: Test with Dummy Log**
```bash
# Add a test log to any Java file
echo 'logger.error("test log for verification");' >> business-validation-service/src/main/java/com/amdocs/catalog/App.java

# Check git status
git status
```

#### **Step 3: Test Staging Detection**
1. **Stage the file** using Cursor Git UI (right-click → Stage Changes)
2. **Verify dialog appears** with "STAGING BLOCKED: LOGGING DETECTED" message
3. **Verify buttons present**: "Cancel", "Manual Steps", "Auto-Run Analysis"
4. **Click "Auto-Run Analysis"** - should auto-trigger @logs-scan-rules.mdc in Cursor Chat

#### **Step 4: Test Analysis Workflow**
1. **Wait for Cursor Chat to open** with @logs-scan-rules.mdc
2. **Verify mandatory progress visualization appears**:
   ```
   🧹 Found [X] New Logging Statement(s) for Analysis
   | 🔍 ANALYZING NEW/MODIFIED LOGGING STATEMENTS |
   ```
3. **Verify log analysis prompt appears** with rating and recommendations
4. **Choose option A, B, C, D, or R** to test interaction
5. **Verify HTML report generation** in `logAnalysisOutput/` directory
6. **Verify staging unblocks** after analysis completion

#### **Step 5: Clean Up Test**
```bash
# Remove the test log
git restore business-validation-service/src/main/java/com/amdocs/catalog/App.java
# Or manually remove the test line from the file
```

#### **Step 6: MANDATORY - Restart Cursor IDE**
**🚨 CRITICAL FINAL STEP - Cursor MUST be restarted to activate the new rule:**

```bash
# Display restart dialog to user
if command -v osascript >/dev/null 2>&1; then
    # macOS
    osascript -e "
    try
        display dialog \"✅ Pre-Staging Log Analysis Setup Complete!\" & return & return & \"🔄 Cursor IDE must be restarted to activate the new @logs-scan-rules.mdc rule.\" & return & return & \"Click OK to restart Cursor now, or Cancel to restart manually later.\" buttons {\"Cancel\", \"Restart Cursor\"} default button \"Restart Cursor\" with title \"Setup Complete - Restart Required\" with icon 1
        set userChoice to button returned of result
        if userChoice is \"Restart Cursor\" then
            return \"restart\"
        else
            return \"manual\"
        end if
    on error
        return \"manual\"
    end try
    "
    
    RESTART_CHOICE=$?
    if [ "$RESTART_CHOICE" = "restart" ]; then
        echo "🔄 Restarting Cursor IDE..."
        osascript -e "
        try
            tell application \"Cursor\" to quit
            delay 3
            tell application \"Cursor\" to activate
        end try
        " >/dev/null 2>&1
        echo "✅ Cursor restarted! The @logs-scan-rules.mdc rule is now active."
    else
        echo "⚠️ IMPORTANT: Please restart Cursor manually to activate @logs-scan-rules.mdc"
    fi
elif command -v powershell.exe >/dev/null 2>&1 || command -v powershell >/dev/null 2>&1; then
    # Windows
    RESTART_CHOICE=$(powershell.exe -Command "
    Add-Type -AssemblyName System.Windows.Forms
    \$result = [System.Windows.Forms.MessageBox]::Show('✅ Pre-Staging Log Analysis Setup Complete!`n`n🔄 Cursor IDE must be restarted to activate the new @logs-scan-rules.mdc rule.`n`nClick Yes to restart Cursor now, or No to restart manually later.', 'Setup Complete - Restart Required', 'YesNo', 'Information')
    if (\$result -eq 'Yes') { 'restart' } else { 'manual' }
    " 2>/dev/null || echo "manual")
    
    if [ "$RESTART_CHOICE" = "restart" ]; then
        echo "🔄 Restarting Cursor IDE..."
        powershell.exe -Command "
        try {
            Get-Process | Where-Object {\$_.ProcessName -like '*Cursor*'} | Stop-Process -Force
            Start-Sleep -Seconds 3
            Start-Process 'Cursor'
        } catch {}
        " >/dev/null 2>&1
        echo "✅ Cursor restarted! The @logs-scan-rules.mdc rule is now active."
    else
        echo "⚠️ IMPORTANT: Please restart Cursor manually to activate @logs-scan-rules.mdc"
    fi
else
    # Linux or fallback
    echo "✅ Pre-Staging Log Analysis Setup Complete!"
    echo "🔄 CRITICAL: Cursor IDE must be restarted to activate @logs-scan-rules.mdc"
    echo "📋 Please restart Cursor manually to complete the setup."
fi

echo ""
echo "🎯 SETUP VERIFICATION:"
echo "1. ✅ All files created and configured"
echo "2. ✅ Background monitor running"
echo "3. ✅ Case-insensitive logging detection active"
echo "4. ✅ Polite dialog system ready"
echo "5. 🔄 Cursor restart required (for @logs-scan-rules.mdc activation)"
echo ""
echo "🚀 Your pre-staging log analysis system is ready!"
echo "📝 Test by staging Java files with logging changes via Cursor GUI"
```

### **SUCCESS CRITERIA:**

✅ **File Structure**: All required files created in correct locations
✅ **Git Wrapper**: Custom Git wrapper intercepts staging operations
✅ **Background Monitor**: Monitor process running and detecting index changes
✅ **Native Dialog**: Cross-platform dialog appears when staging Java files with logging changes
✅ **Auto-Trigger**: "Auto-Run Analysis" button auto-triggers @logs-scan-rules.mdc in Cursor Chat
✅ **Progress Visualization**: Mandatory progress bar displayed before analysis starts
✅ **Interactive Analysis**: Prompts appear for low-quality logs (rated < 7) with A/B/C/D/R options
✅ **System Prompt Compliance**: Analysis follows log_analyzer_system_prompt.md criteria
✅ **HTML Report**: Comprehensive report generated in logAnalysisOutput/ directory
✅ **Staging Control**: Staging blocked until analysis complete, then proceeds smoothly
✅ **Cross-Platform**: Works on macOS, Windows, and Linux platforms
✅ **IDE Integration**: Seamlessly integrates with Cursor/VSCode Git UI
✅ **Git Ignore**: All temporary files properly excluded from version control
✅ **State Management**: Trigger and flag files control workflow correctly
✅ **Cursor Restart**: Automated dialog and restart to activate @logs-scan-rules.mdc rule

### **TROUBLESHOOTING:**

If the system doesn't work on first run:

#### **CRITICAL FIXES - Common Issues We Discovered:**

1. **Case-sensitive logging detection failure**:
   - **Problem**: Monitor only detects lowercase `logger.` but code uses uppercase `LOGGER.`
   - **Solution**: Update regex patterns to use `-iE` flag and `(logger|LOGGER)` patterns
   - **Test**: Create file with `LOGGER.error("test")` and verify detection works

2. **Dialog appears but doesn't send message to Cursor**:
   - **Problem**: Message gets pasted but not actually sent to AI (user has to manually press Send)
   - **Solution**: Use triple Enter press with increased delays for reliable message sending
   - **Fix**: Use explicit process targeting and chat focus handling

3. **CRITICAL: @logs-scan-rules.mdc pasted into source code instead of Chat**:
   - **Problem**: Automation pastes chat command into Java files, corrupting source code
   - **Root Cause**: Focus is on editor window instead of Cursor Chat when paste occurs
   - **Solution**: Use explicit process targeting with "tell process Cursor" and chat focus verification
   - **Prevention**: Add click safety mechanism to ensure chat input area focus
   - **Detection**: Check source files for accidental @logs-scan-rules.mdc text after automation

4. **Multiple monitor processes running**:
   - **Problem**: Old monitor processes interfere with new ones
   - **Solution**: Kill all existing processes before starting new monitor
   - **Command**: `killall -f index-monitor.sh && pkill -f "index-monitor"`

5. **Harsh dialog message**:
   - **Problem**: Dialog shows "STAGING BLOCKED: LOGGING DETECTED" (too harsh)
   - **Solution**: Use polite "✨ Code Quality Assistant" message
   - **Verification**: Dialog must show friendly, helpful language

6. **@logs-scan-rules.mdc rule not active after setup**:
   - **Problem**: Cursor doesn't recognize the new .mdc rule file
   - **Solution**: Restart Cursor IDE to reload all rule files
   - **Test**: Try `@logs-scan-rules.mdc` in Cursor Chat - should auto-complete if rule is active

7. **🚨 CRITICAL PERFORMANCE ISSUE: Slow automation execution (13+ seconds)**:
   - **Problem**: "Auto-Run Analysis" takes 13+ seconds instead of ~0.5 seconds due to excessive delays in background monitor
   - **Root Cause**: Background monitor (`.git/hooks/index-monitor.sh`) uses unoptimized delay settings:
     ```bash
     # SLOW/UNOPTIMIZED (13+ seconds total):
     delay 3      # Cursor activation (TOO SLOW!)
     delay 4      # After Cmd+L chat opening (TOO SLOW!)  
     delay 3      # After paste (TOO SLOW!)
     delay 1      # Between Enter presses
     ```
   - **CRITICAL SOLUTION**: Use performance-optimized delays (~4.5 seconds total):
     ```bash
     # FAST/OPTIMIZED (4.5 seconds total):
     delay 1      # Cursor activation (OPTIMIZED!)
     delay 1.5    # After Cmd+L chat opening (OPTIMIZED!)
     delay 0.5    # After paste (OPTIMIZED!)
     delay 0.5    # Between Enter presses (OPTIMIZED!)
     ```
   - **Performance Impact**: 65% reduction in wait time (8.5 seconds faster)
   - **Implementation**: The TEAM_SETUP_PROMPT.md already includes optimized delays - ensure you copy the exact script
   - **Verification**: Test "Auto-Run Analysis" - should execute in ~4.5 seconds, not 13+ seconds
   - **⚠️ CRITICAL**: This is a major user experience issue that makes the system feel slow and unresponsive

8. **🚨 CRITICAL GIT PULL PROTECTION: Log scan triggered by git pull operations**:
   - **Problem**: Background monitor triggers log analysis during `git pull`/`git merge`/`git rebase` operations
   - **Root Cause**: Monitor detects index changes during remote merge operations and treats them as local staging
   - **Critical Issue**: Remote changes (already reviewed) trigger local log analysis inappropriately
   - **MANDATORY SOLUTION**: Add git operation detection to skip analysis during pull/merge/rebase:
     ```bash
     # Add this check in background monitor BEFORE checking staged files:
     if [ -f "$REPO_ROOT/.git/MERGE_HEAD" ] || [ -f "$REPO_ROOT/.git/CHERRY_PICK_HEAD" ] || [ -f "$REPO_ROOT/.git/REBASE_HEAD" ] || [ -d "$REPO_ROOT/.git/rebase-merge" ] || [ -d "$REPO_ROOT/.git/rebase-apply" ]; then
         echo "Skipping log analysis - git pull/merge/rebase operation in progress"
         continue
     fi
     ```
   - **Impact**: Prevents analysis of remote changes that are already reviewed and approved
   - **Verification**: Test `git pull` with logging changes - should NOT trigger log analysis
   - **⚠️ CRITICAL**: Without this fix, every git pull with logging changes will inappropriately trigger analysis

9. **🚨 CRITICAL TERMINAL COMMAND EXECUTION ERROR:**
   - **Problem**: AI implementation uses complex terminal command concatenation causing malformed quotes and terminal hanging
   - **Root Cause**: Using multiple `&&` operators with complex quoted strings in single terminal commands
   - **Critical Issue**: Commands like `echo "text" && command1 && echo "more text"` cause terminal to hang with malformed quote errors
   - **MANDATORY SOLUTION**: Use separate, simple terminal commands instead of concatenation:
     ```bash
     # BAD - CAUSES TERMINAL HANGING:
     echo "step 1" && command1 && echo "step 2" && command2
     
     # GOOD - SEPARATE SIMPLE COMMANDS:
     echo "step 1"
     command1
     echo "step 2" 
     command2
     ```
   - **Implementation Fix**: Break down complex operations into individual `run_terminal_cmd` calls
   - **Prevention**: Never use command concatenation with `&&` and quoted strings
   - **Detection**: If terminal shows `cmdand cmdand dquote>` prompt, the command is malformed
   - **⚠️ CRITICAL**: This is a fundamental execution issue that breaks the entire setup process

#### **Standard Common Issues:**


8. **Git path incorrect**: Update `REAL_GIT` variable in `local-bin/git`
9. **Monitor not running**: Check `ps aux | grep index-monitor` and restart if needed
10. **Dialog not appearing**: Verify platform-specific dialog tools (osascript/powershell/zenity)
11. **Cursor not activating**: Check application name in automation scripts
12. **Files being committed**: Verify .gitignore includes all solution files

#### **Debug Commands:**
```bash
# CRITICAL: Check for multiple monitor processes (should see only ONE)
ps aux | grep index-monitor | grep -v grep

# CRITICAL: Verify performance-optimized delays (should show 0.5, 1, 1.5 delays, NOT 3, 4 delays)
echo "🔍 Checking automation delay optimization..."
grep -A 20 "tell application.*Cursor.*to activate" .git/hooks/index-monitor.sh | grep "delay"
echo "✅ Should show: delay 1, delay 0.5, delay 1.5, delay 0.5, delay 0.5"
echo "❌ Should NOT show: delay 3, delay 4, delay 3, delay 1"

# CRITICAL: Test case-insensitive detection
echo 'LOGGER.error("test");' > TestCase.java
git add TestCase.java
git diff --cached TestCase.java | grep -iE "^\+.*(logger|LOGGER)\.(debug|info|warn|error|fatal|trace)"
git reset HEAD TestCase.java && rm TestCase.java

# Check recent monitor activity with polite dialog
tail -f logs-scan-monitor.log

# Verify monitor is using latest polite dialog version
grep -A 5 "Code Quality Assistant" .git/hooks/index-monitor.sh

# Test automation timing (should show double Enter press)
grep -A 10 "keystroke return" .git/hooks/index-monitor.sh

# Verify Git wrapper
./local-bin/git --version

# Test trigger creation
echo "test" > .git-pre-staging-logs-detected.trigger
ls -la *.trigger

# Check Cursor settings
cat .vscode/settings.json

# CRITICAL: Check for source code corruption (automation bug detection)
echo "🚨 Checking for @logs-scan-rules.mdc accidentally pasted into source files..."
find . -name "*.java" -exec grep -l "@logs-scan-rules.mdc" {} \; 2>/dev/null || echo "✅ No source code corruption found"

# CRITICAL: Verify Cursor rule is active (after restart)
ls -la .cursor/rules/logs-scan-rules.mdc
echo "🔍 Test: Type '@logs-scan-rules.mdc' in Cursor Chat - should auto-complete if rule is active"

# CRITICAL: Restart monitor with all fixes (improved focus handling)
killall -f index-monitor.sh 2>/dev/null
pkill -f "index-monitor" 2>/dev/null
sleep 3
nohup .git/hooks/index-monitor.sh > logs-scan-monitor.log 2>&1 &
echo "Monitor restarted with PID: $! (includes explicit process targeting and chat focus fixes)"

# CRITICAL: Test terminal command execution (should NOT hang)
echo "Testing simple command execution..."
echo "This should complete without hanging"
```

Please implement this complete solution with all files and functionality described above.

**🛡️ IMPORTANT**: For each critical file creation/modification step, PAUSE and request explicit user approval before proceeding. This ensures transparency and user control over the installation process.

**🔥 CRITICAL REMINDER FOR AI IMPLEMENTATION:**
1. **Execute ALL steps in sequence** - missing any step will break the system
2. **Update .gitignore FIRST** - prevent committing temporary files
3. **Configure platform-specific Git paths** - system won't work with wrong path
4. **Start background monitor** - required for real-time detection
5. **MANDATORY: Use case-insensitive regex patterns** - must detect both `logger.` and `LOGGER.`
6. **MANDATORY: Implement double Enter press** - ensures message is sent to Cursor Chat
7. **MANDATORY: Use polite dialog message** - "✨ Code Quality Assistant" not harsh blocking language
8. **MANDATORY: Kill existing monitor processes** - prevent conflicts from multiple processes
9. **MANDATORY: Use performance-optimized delays** - automation must execute in ~4.5 seconds, not 13+ seconds
10. **MANDATORY: Implement git pull protection** - skip analysis during pull/merge/rebase operations (remote changes already reviewed)
11. **MANDATORY: Restart Cursor IDE** - required to activate @logs-scan-rules.mdc rule
12. **MANDATORY: Request user approval for each critical file** - pause and get explicit confirmation before creating/modifying essential files
13. **MANDATORY: Use simple terminal commands** - NEVER use complex command concatenation with `&&` and quoted strings
14. **Test thoroughly** - follow all verification steps before declaring success
15. **Preserve existing functionality** - don't break current Git workflow

**🛡️ MANDATORY USER APPROVAL WORKFLOW:**
**CRITICAL REQUIREMENT**: For EACH crucial step in the setup process that creates or modifies files essential to log scan functionality, the AI MUST:

1. **PAUSE the implementation process** before creating/modifying any critical file
2. **CLEARLY EXPLAIN** what file is being created/modified and its purpose in the system
3. **REQUEST EXPLICIT USER APPROVAL** with a clear prompt like: "Ready to create [filename]? Please confirm to proceed (Y/N):"
4. **WAIT for user confirmation** before proceeding with the file creation/modification
5. **ONLY CONTINUE** to the next step after receiving user approval

**CRITICAL FILES REQUIRING MANUAL APPROVAL:**
- ✅ `.cursor/rules/logs-scan-rules.mdc` - Core rule file
- ✅ `prompt/log_analyzer_system_prompt.md` - Analysis guidelines
- ✅ `.vscode/settings.json` - Git wrapper configuration
- ✅ `local-bin/git` - Custom Git wrapper (executable)
- ✅ `.git/hooks/index-monitor.sh` - Background monitor script
- ✅ `.gitignore` modifications - Exclusion rules for temporary files
- ✅ Directory creation for critical components

**PURPOSE**: This approval workflow ensures users understand each component being installed and can review the implementation before proceeding. This prevents unexpected system modifications and provides transparency in the setup process.

**🚨 CRITICAL IMPLEMENTATION FIXES:**
- **Regex patterns MUST be case-insensitive**: Use `-iE` flag and `(logger|LOGGER)` patterns
- **Automation MUST target Chat, NOT source code**: Use explicit process targeting with "tell process Cursor"
- **Chat focus MUST be verified**: Include frontmost window handling and safety click mechanism
- **Automation MUST reliably send message**: Use increased delays and triple `keystroke return`
- **Monitor MUST be singular**: Kill all existing processes before starting new one
- **Dialog MUST be polite**: Use helpful language, not blocking/harsh messaging
- **Performance MUST be optimized**: Use delay 1, 0.5, 1.5, 0.5, 0.5 (NOT delay 3, 4, 3, 1) for fast execution
- **User approval MUST be requested**: Pause and get explicit confirmation before creating/modifying each critical file
- **Git pull protection MUST be implemented**: Skip analysis during pull/merge/rebase operations (remote changes are already reviewed)
- **Cursor MUST be restarted**: Display dialog and restart IDE to activate @logs-scan-rules.mdc rule
- **Source code corruption MUST be prevented**: Verify automation pastes to Chat, not editor
- **Terminal commands MUST be simple**: NEVER use complex concatenation with `&&` and quoted strings that cause hanging

This is a production-ready solution that has been thoroughly tested and debugged. Following these instructions exactly, including the critical fixes, will result in a fully functional pre-staging log analysis system on the first run.
```

---

## 🎊 **BENEFITS FOR YOUR TEAM**

- 🎯 **Pre-staging detection** - catches logging issues before they reach staging area
- 📱 **Native dialogs** with one-click automation across all platforms
- 🔄 **Cursor/VSCode integration** - works seamlessly with Git UI
- ⚡ **Immediate feedback** - fix issues when they happen, not at commit time
- 🌍 **Cross-platform** - works on Windows, Mac, Linux
- 👥 **Team-ready** - easy setup with this single prompt
- 🚀 **Zero manual setup** - completely automated implementation
- 📊 **Quality metrics** - comprehensive HTML reports for tracking improvements

## 🔧 **FOR MANAGERS**

This solution provides:
- **Automated code quality enforcement** for logging standards
- **Developer productivity improvement** through immediate feedback
- **Consistent logging quality** across the entire team
- **Zero training required** - developers just use their normal Git workflow
- **Comprehensive reporting** for quality metrics and compliance tracking
- **Cross-platform standardization** ensuring consistent experience

**Ready to deploy across your entire development team! 🚀**
