# Anypoint Platform Live Hierarchy Report - Workflow Execution Guide

**Purpose:** This workflow guide contains the step-by-step process for generating comprehensive Anypoint Platform hierarchy reports using MuleSoft MCP Server Tools and Enhanced Dynamic Discovery.

**Output:** Generates timestamped reports using the `anypoint-assessment-template.md` template

---

## 🛠️ **WORKFLOW EXECUTION STEPS**

### Step 1: Get Platform Insights
```
Use MCP Tool: get_platform_insights
Parameters:
- includeFlows: true
- includeMessages: true  
- includeDataThroughput: true
```

**Purpose:** Retrieve comprehensive platform metrics including flow counts, message volumes, and data throughput across all organizations and environments.

### Step 1.5: Get Reuse Metrics
```
Use MCP Tool: get_reuse_metrics
Parameters:
- includeReuseMetrics: true
```

**Purpose:** Retrieve application reuse metrics to identify number of consumers for each application, enabling better understanding of application adoption and impact.

---

### Step 2: Dynamic Business Group and Environment Discovery
**APPROACH:** Dynamically discover all organizations and environments without relying on static environment names.

#### Step 2.1: Extract Organization and Environment Inventory from Platform Insights
From Step 1 results, build complete inventory:
```
Platform Insights Analysis:
- Extract ALL unique combinations: (org_id, org_name, env_id, env_name)
- Create comprehensive organization/environment matrix
- Note: Platform insights aggregates ALL environments across ALL organizations
- Build discovery list for validation in Step 2.2

Example Output:
Organization: "Acme Corp" (org-123)
  └── Environments discovered: ["Production", "Sandbox", "QA"]
Organization: "Beta Ltd" (org-456)  
  └── Environments discovered: ["Prod", "Dev", "Staging"]
```

#### Step 2.2: Dynamic Environment Validation Loop
**CRITICAL:** Validate each discovered environment instead of using static names:
```
For each organization discovered in Step 2.1:
  For each environment discovered for that organization:
    
    Use MCP Tool: list_applications
    Parameters:
    - environmentName: [discovered_environment_name]  # Dynamic from Step 2.1
    - organizationId: [discovered_org_id]             # Dynamic from Step 2.1
    - page: 1
    
    If successful (applications returned or empty list):
      → Environment validated and accessible
      → Add to validated_environments list
      → Proceed to Step 3 for this environment
    
    If failed (environment not found/access denied):
      → Environment inaccessible or renamed
      → Log as inaccessible_environment
      → Skip this environment

Example Dynamic Loop:
✅ Validate: list_applications(environmentName="Production", organizationId="org-123")
✅ Validate: list_applications(environmentName="Sandbox", organizationId="org-123")  
✅ Validate: list_applications(environmentName="Prod", organizationId="org-456")
❌ Skip: list_applications(environmentName="Old-Env", organizationId="org-789") → Access Denied
```

#### Step 2.3: Environment Discovery Summary
Generate discovery summary for transparency:
```
Dynamic Discovery Results:
- Total Organizations Discovered: [X]
- Total Environments Discovered: [X]  
- Validated Accessible Environments: [X]
- Inaccessible/Renamed Environments: [X]
- Ready for Application Analysis: [X] environments across [X] organizations

Validated Environment Matrix:
Organization: "Acme Corp" (org-123)
  ✅ Production (env-abc) - [X] apps
  ✅ Sandbox (env-def) - [X] apps
  
Organization: "Beta Ltd" (org-456)  
  ✅ Prod (env-ghi) - [X] apps
  ❌ Old-Dev (env-jkl) - Access Denied
```

---

### Step 3: Dynamic Application Collection Loop
**CRITICAL**: For each validated environment from Step 2.2, retrieve ALL applications using dynamic pagination:

#### Step 3.1: Dynamic Application Collection Algorithm
```
Initialize: validated_environments = [] # From Step 2.2
Initialize: all_applications = {}

For each validated_environment in validated_environments:
  org_id = validated_environment.org_id
  org_name = validated_environment.org_name  
  env_name = validated_environment.env_name
  
  # Start pagination loop for this environment
  page = 1
  total_apps_for_env = 0
  
  While True:
    
    Use MCP Tool: list_applications
    Parameters:
    - environmentName: env_name          # Dynamic from validation
    - organizationId: org_id             # Dynamic from validation  
    - page: page                         # Dynamic pagination
    
    If response.success:
      apps_on_page = response.applications.length
      total_pages = response.total_pages
      
      # Collect application details
      For each app in response.applications:
        Record: name, status, deployment_status, v_cores, replicas, last_update_time
        Store in: all_applications[org_id][env_name][app.name]
      
      total_apps_for_env += apps_on_page
      
      # Check if more pages exist
      If page >= total_pages:
        Break  # All pages collected for this environment
      Else:
        page += 1  # Continue to next page
        
    Else:
      Log: "Failed to retrieve page {page} for {org_name}/{env_name}"
      Break  # Stop pagination for this environment

  Log: "Collected {total_apps_for_env} applications from {org_name}/{env_name}"

Final Result: Complete application inventory across all validated environments
```

#### Step 3.2: Real-World Dynamic Collection Examples
```
Example 1: Multi-Organization Discovery
✅ Platform Insights discovers:
   - Acme Corp (org-123): ["Production", "Sandbox"]  
   - Beta Ltd (org-456): ["Prod", "Dev", "Staging"]
   - Gamma Inc (org-789): ["Live", "Test"]

✅ Dynamic validation and collection:
   # Acme Corp - Production
   list_applications(environmentName="Production", organizationId="org-123", page=1) → 15 apps, 2 pages
   list_applications(environmentName="Production", organizationId="org-123", page=2) → 3 apps
   Total: 18 applications collected
   
   # Acme Corp - Sandbox  
   list_applications(environmentName="Sandbox", organizationId="org-123", page=1) → 8 apps, 1 page
   Total: 8 applications collected
   
   # Beta Ltd - Prod
   list_applications(environmentName="Prod", organizationId="org-456", page=1) → 25 apps, 3 pages
   list_applications(environmentName="Prod", organizationId="org-456", page=2) → 10 apps  
   list_applications(environmentName="Prod", organizationId="org-456", page=3) → 2 apps
   Total: 37 applications collected
   
   # Continue for all validated environments...

Example 2: Handling Access Denied Scenarios  
❌ Some environments may be inaccessible:
   list_applications(environmentName="Old-Dev", organizationId="org-789", page=1) → Access Denied
   → Skip this environment, log as inaccessible
   → Continue with remaining validated environments

Final Collection Summary:
- Total Organizations: 3
- Validated Environments: 5 (1 skipped due to access issues)  
- Total Applications: 63 applications across all accessible environments
```

---

### Step 3.5: Collect API Manager Policy Information
**PURPOSE**: For each application, determine if it's protected by API Manager and collect applied policies.

#### Step 3.5.1: API Instance Discovery and Policy Collection
```
CRITICAL DISCOVERY APPROACH: Use environment-wide API instance listing instead of app-specific search

For each environment in validated_environments:
  
  Use MCP Tool: list_api_instances
  Parameters:
  - environmentName: env_name
  - organizationId: org_id
  - includeAppliedPolicies: true        # Get applied policies
  - page: 1 (and iterate through all pages)
  
  # Build API instance mapping
  For each api_instance in response.instances:
    api_asset_id = api_instance.assetId
    applied_policies = api_instance.appliedPolicies
    
    # AGILE MATCHING: Use flexible, multi-strategy matching algorithm
    For each application in environment_applications:
      app_name = application.name.toLowerCase()
      api_asset_id = api_instance.assetId.toLowerCase()
      
      match_found = false
      match_strategy = ""
      
      # Strategy 1: Exact match
      If app_name == api_asset_id:
        match_found = true
        match_strategy = "exact"
      
      # Strategy 2: Application contains API asset ID (most common)
      If not match_found AND api_asset_id in app_name:
        match_found = true
        match_strategy = "contains"
      
      # Strategy 3: API asset ID contains application name
      If not match_found AND app_name in api_asset_id:
        match_found = true
        match_strategy = "reverse_contains"
      
      # Strategy 4: Remove common prefixes/suffixes and retry
      If not match_found:
        # Remove common prefixes (org names, env names, etc.)
        cleaned_app_name = remove_common_prefixes(app_name)
        cleaned_api_name = remove_common_prefixes(api_asset_id)
        
        If cleaned_app_name == cleaned_api_name OR 
           cleaned_api_name in cleaned_app_name OR 
           cleaned_app_name in cleaned_api_name:
          match_found = true
          match_strategy = "cleaned"
      
      # Strategy 5: Fuzzy matching using word overlap
      If not match_found:
        app_words = extract_meaningful_words(app_name)
        api_words = extract_meaningful_words(api_asset_id)
        
        # Calculate word overlap percentage
        overlap_score = calculate_word_overlap(app_words, api_words)
        
        If overlap_score >= 0.6:  # 60% word overlap threshold
          match_found = true
          match_strategy = "fuzzy"
      
      # Strategy 6: Semantic matching for known patterns
      If not match_found:
        # Check for semantic equivalents
        If semantic_match(app_name, api_asset_id):
          match_found = true
          match_strategy = "semantic"
          
          # Examples:
          # "marketing-xapi" ↔ "marketplace-xapi" (marketing = marketplace)
          # "product-mgmt-papi" ↔ "product-catalog-and-availability-papi"
          # "checkout-process" ↔ "checkout-papi"
      
      # If match found by any strategy:
      If match_found:
        → Application IS protected by API Manager
        → Record: api_asset_id, applied_policies, match_strategy
        → Calculate active policy count (exclude disabled policies)
        → Store mapping: api_management_data[org_id][env_name][app_name]
        → Log: "Matched {app_name} ↔ {api_asset_id} via {match_strategy}"

# For applications with no API instance match:
For each unmatched_application:
  → Application is NOT protected by API Manager
  → Status = "Not API Managed"
  → Still include in report with "0 API policies" notation
  → Log: "No API instance found for {app_name}"

AGILE MATCHING EXAMPLES:
✅ ana-product-sapi ↔ product-sapi (contains strategy)
✅ server-info ↔ info-server-api (reverse_contains strategy) 
✅ checkout-flow ↔ checkout-papi (fuzzy/word overlap strategy)
✅ marketing-xapi ↔ marketplace-xapi (semantic strategy)
✅ product-mgmt ↔ product-catalog-and-availability-papi (fuzzy strategy)
✅ order-api ↔ orders-system-api (cleaned + fuzzy strategy)
❌ mule-demo-mcp → No semantic match → "Not API Managed"

POLICY COUNT CALCULATION:
- Only count policies where disabled = false
- Include policy names in parentheses for active policies
- Format: "X (PolicyName1, PolicyName2)" or "0 (Not API Managed)"
- Log matching strategy used for transparency and debugging
```

#### Step 3.5.2: Consumer Count Integration
```
Cross-reference reuse metrics from Step 1.5 with application data:

For each application in all_applications:
  If app exists in reuse_metrics:
    → Extract consumer_count from reuse metrics
    → Record number of consumers/dependencies
    → Include in application details
  
  If app NOT in reuse_metrics:
    → Consumer count = "N/A" or "0"
    → Still include in report
    → Flag as "Reuse metrics unavailable"

Example Consumer Integration:
✅ ana-product-sapi → 12 consumers (high reuse)
✅ ana-checkout-papi → 3 consumers (moderate reuse)
💤 backend-info → 0 consumers (no reuse)
```

### Step 4: Generate Report Using Template

#### Step 4.1: Load Report Template
Load the `anypoint-assessment-template.md` template file containing the report structure.

#### Step 4.2: Data Population Process
1. **Replace Executive Summary placeholders** with aggregated metrics from collected data
2. **Build deployment hierarchy tree** using organization/environment/application structure
3. **Generate deployment metrics tables** with organization overview and status distribution
4. **Calculate health metrics** and identify applications requiring attention
5. **Populate key findings** based on platform analysis
6. **Update timestamp** in header with current datetime

**🚨 CRITICAL REQUIREMENT - Application Detail Structure Enforcement:**

**MANDATORY**: Every single application in the hierarchy MUST follow this EXACT detail structure format:
```
├─── [application-name] [STATUS_ICON] [STATUS_TEXT]
│    ├─── 💾 [Platform]: [vCores] vCore × [replicas] replica/worker
│    ├─── ⚙️ Runtime: [Mule Version] | 📈 Flows: [count] | Messages: [count] | Data: [amount] GB throughput
│    ├─── 👥 Consumers: [count] | 🛡️ API Policies: [count] ([Policy Names or "Not API Managed"])
│    └─── 🕒 Last Updated: [Date]
```

**APPLICATION DETAIL VALIDATION RULES:**
- **NO EXCEPTIONS**: Every application must have all 4 detail lines (💾, 📈, 👥, 🕒)
- **NO SHORTCUTS**: Never list applications with just status - always include full details
- **DATA COMPLETENESS**: Use "N/S" for missing metrics, but structure must be complete
- **CONSISTENT FORMATTING**: Maintain exact indentation and icon structure

**PRE-SAVE VALIDATION REQUIREMENT:**
Before saving the report, verify that NO application appears as:
```
❌ WRONG: ├─── application-name ✅ RUNNING
❌ WRONG: ├─── application-name ✅ RUNNING (0.2 vCores)
```

All applications must appear as:
```
✅ CORRECT: ├─── [application-name] [STATUS_ICON] [STATUS_TEXT]
            │    ├─── 💾 [Platform]: [vCores] vCore × [replicas] replica/worker
            │    ├─── ⚙️ Runtime: [Mule Version] | 📈 Flows: [count] | Messages: [count] | Data: [amount] GB throughput
            │    ├─── 👥 Consumers: [count] | 🛡️ API Policies: [count] ([Policy Names or "Not API Managed"])
            │    └─── 🕒 Last Updated: [Date]

#### Step 4.3: Create Timestamped Report File
Generate the report filename using current datetime:
```
Format: anypoint-platform-assessment-report-YYYYMMDD-HHMMSS.md
Example: anypoint-platform-assessment-report-20260212-211500.md

Use current datetime in Europe/Paris timezone for consistency
```

#### Step 4.4: Save Final Report
1. Apply populated template data to generate complete report
2. Save to timestamped filename
3. Preserve original template file unchanged for future use

#### Step 4.5: Data Quality Validation & Application Status Assignment
**CRITICAL**: Perform comprehensive data validation before finalizing the report:

##### 4.5.1: Application Detail Completeness Check
```
For each application in all_applications:
  Validate required fields are present:
  - ✅ Application name
  - ✅ Deployment status (RUNNING, STOPPED, FAILED, etc.)
  - ✅ Platform type (CloudHub 2.0, CloudHub Legacy, Runtime Fabric)
  - ✅ Resource allocation (vCores, replicas)
  - ✅ Last update timestamp
  
  If missing critical details:
    → Flag as "Incomplete Data" in report
    → Add to "Applications Requiring Review" section
    → Document missing fields for follow-up
```

##### 4.5.2: IDLE Application Status Assignment
```
MANDATORY RULE: Applications with message_count = 0 MUST be flagged as IDLE

For each application in platform insights message data:
  If app.mule_message_count == 0:
    → Status = 💤 IDLE (Running but no traffic during insight period)
    → Include in "Resource Optimization" recommendations
    → Calculate idle vCore consumption for cost analysis
  
  If app.mule_message_count > 0:
    → Status = ✅ RUNNING (Active with traffic)
    → Include message volume in application metrics

Example Status Assignment:
✅ server-info: 10,366 messages → RUNNING (Active)
💤 backend-info: 0 messages → IDLE (No traffic)
💤 mule-demo-mcp: 0 messages → IDLE (No traffic)
```

##### 4.5.3: Complete Application Documentation Check
```
CRITICAL: Ensure ALL applications from list_applications are documented in the report

Cross-reference validation:
1. **Platform Insights Applications**: From get_platform_insights (may be incomplete)
2. **Complete Application List**: From list_applications per environment (comprehensive)

Validation Process:
For each environment in validated_environments:
  list_apps = get_applications_from_list_applications(org_id, env_name)
  insights_apps = get_applications_from_platform_insights(org_id, env_name)
  
  For each app in list_apps:
    If app NOT in insights_apps:
      → Missing metrics data (flows, messages, throughput)
      → Status = "Metrics N/A" or "N/S" (Not Specified)
      → Still include in report hierarchy
      → Flag as "Limited Metrics Available"
    
    If app in insights_apps:
      → Full metrics available
      → Use insights data for flows, messages, throughput
      → Apply IDLE status rule (message_count = 0)

Documentation Requirements:
- ✅ ALL applications from list_applications MUST appear in hierarchy
- ✅ Applications without insights data marked with "N/S" or "N/A" 
- ✅ Clear indication when metrics are unavailable
- ✅ No applications excluded from report due to missing metrics
```

##### 4.5.4: Environment Assignment Validation
```
Handle applications that cannot be assigned to proper API tier (SAPI/PAPI/EAPI):

Classification Rules:
1. **Check application name patterns**:
   - *-sapi, *-system-api → 🔵 System API
   - *-papi, *-process-api → 🟢 Process API  
   - *-eapi, *-xapi, *-experience-api → 🟠 Experience API

2. **If no clear pattern detected**:
   - → Classify as ⚪ Other Applications
   - → Still include full application details
   - → Document in "Other Applications" section
   - → No applications excluded due to classification uncertainty

3. **Special application handling**:
   - broker, mcp, workshop, demo applications → 🧪 Special Applications
   - High-resource applications (>0.2 vCore) → 🧪 Special Applications
   - Applications with unique purposes → 🧪 Special Applications

Example Classification:
✅ ana-product-sapi → 🔵 System API (clear pattern)
✅ mule-b2b-mcp → 🧪 Special Application (MCP pattern)
✅ unknown-app → ⚪ Other Application (no clear pattern)
```

##### 4.5.5: Final Validation Checklist
Before saving the report, confirm:
- [ ] All applications from list_applications appear in report hierarchy
- [ ] Applications with 0 messages marked as 💤 IDLE
- [ ] Missing metrics marked as "N/S" or "N/A"
- [ ] Applications properly classified by tier (SAPI/PAPI/EAPI/Other/Special)
- [ ] Resource totals calculated correctly (vCores, replicas)
- [ ] Status distribution counts match actual application statuses
- [ ] No applications excluded due to missing data or unclear classification

#### Step 4.6: Automated Application Detail Structure Verification
**🚨 MANDATORY PRE-SAVE VALIDATION STEP - PREVENTS INCOMPLETE REPORTS**

**PURPOSE**: Automatically scan the generated report to ensure ZERO applications are missing detailed structure and status distribution counts are accurate.

##### 4.6.1: Pattern-Based Detail Structure Verification
```
AUTOMATED SCAN: Search the report for any application entries that match these INVALID patterns:

❌ INVALID PATTERNS (MUST NOT EXIST):
- "├─── [app-name] ✅ RUNNING\n├───" (missing details)
- "├─── [app-name] ❌ STOPPED\n├───" (missing details)  
- "├─── [app-name] 💤 IDLE\n├───" (missing details)
- "├─── [app-name] [STATUS]\n│" (where next line is NOT "│    ├─── 💾")

✅ VALID PATTERNS (REQUIRED):
- "├─── [app-name] [STATUS]\n│    ├─── 💾" (starts detail structure)
- Every application must have 4 detail lines: 💾, 📈, 👥, 🕒

VALIDATION ALGORITHM:
1. Extract all application names from list_applications data
2. For each application name:
   - Search for pattern: "├─── {app_name}"
   - Verify next line contains: "│    ├─── 💾"
   - Count detail lines (must be exactly 4)
   - Verify all required icons present: 💾, 📈, 👥, 🕒

If ANY application fails validation:
  → STOP REPORT GENERATION
  → Log missing applications with details
  → FIX report before proceeding
  → RE-RUN validation until 100% pass rate
```

##### 4.6.4: CRITICAL - Application Status Count Validation Against Hierarchy
```
🚨 MANDATORY FINAL VALIDATION: Count applications from ANYPOINT PLATFORM DEPLOYMENT HIERARCHY to ensure Status Distribution table accuracy

HIERARCHY-BASED COUNTING ALGORITHM:
1. Parse the "🏢 ANYPOINT PLATFORM DEPLOYMENT HIERARCHY" section
2. For each organization, count applications by status from actual hierarchy:
   
   Status Icon Counting Rules:
   - ✅ RUNNING/STARTED = Count as "Running"
   - 💤 IDLE = Count as "Idle" 
   - ⚠️ APPLYING/ISSUES = Count as "Issues"
   - ❌ STOPPED/UNDEPLOYED = Count as "Stopped"
   - 🚨 FAILED/CRITICAL = Count as "Critical"

3. Cross-validate with "🚦 Application Status Distribution" table:

VALIDATION PROCESS:
For each organization in hierarchy:
  hierarchy_counts = count_by_status_from_hierarchy(org_name)
  table_counts = get_status_counts_from_table(org_name)
  
  For each status (Running, Idle, Issues, Stopped, Critical):
    If hierarchy_counts[status] != table_counts[status]:
      → FLAG MISMATCH ERROR
      → Log: "ORG: {org} - {status}: Hierarchy={hierarchy_count}, Table={table_count}"
      → STOP REPORT GENERATION
      → FIX table counts to match hierarchy
      → RE-RUN validation

EXAMPLE VALIDATION:
✅ CORRECT: 
   Hierarchy shows: ANA has 10 apps with ✅ status
   Table shows: ANA "Running" = 10
   
❌ INCORRECT:
   Hierarchy shows: B2B Partner Manager has 11 apps with ✅ status  
   Table shows: B2B Partner Manager "Running" = 14
   → MUST FIX table to match hierarchy count

4. Validate TOTALS row matches sum of all organization counts

CRITICAL SUCCESS CRITERIA:
✅ Every organization's table counts EXACTLY match hierarchy counts
✅ TOTALS row equals sum of all individual organization counts  
✅ No discrepancies between hierarchy display and status distribution table
✅ Applications from platform insights but not in standard environments properly counted (e.g., kiet.yap org)

This validation prevents counting inconsistencies and ensures the status distribution table is 100% accurate based on the actual deployed hierarchy.
```

##### 4.6.2: Application Count Cross-Validation
```
AUTOMATED COUNT VERIFICATION:

1. **Count applications in hierarchy**: Count all "├─── [app-name]" entries in report
2. **Count applications from data**: Sum all applications from list_applications calls
3. **Cross-validate**: hierarchy_count MUST equal data_collection_count

Validation Rules:
- If hierarchy_count < data_collection_count: MISSING APPLICATIONS
- If hierarchy_count > data_collection_count: DUPLICATE APPLICATIONS
- If hierarchy_count = data_collection_count: ✅ PASSED

Required Action if validation fails:
→ IDENTIFY missing/duplicate applications by name
→ FIX hierarchy section
→ RE-RUN validation until counts match exactly
```

##### 4.6.3: Detail Structure Completeness Audit
```
AUTOMATED DETAIL AUDIT:

For each application found in hierarchy:
  Check presence of required detail structure:
  
  Required Structure (EXACT format):
  ├─── [app-name] [STATUS]
  │    ├─── 💾 [Platform]: [vCores] vCore × [replicas] replica/worker
  │    ├─── 📈 Flows: [X] | Messages: [X] | Data: [X] GB throughput  
  │    ├─── 👥 Consumers: [X] | 🛡️ API Policies: [X] ([Details])
  │    └─── 🕒 Last Updated: [Date]

  Validation Checks:
  - [x] Application name line present
  - [x] 💾 Platform/resource line present
  - [x] 📈 Metrics line present  
  - [x] 👥 Consumer/policy line present
  - [x] 🕒 Update date line present
  
  If ANY check fails:
    → Application flagged as "INCOMPLETE STRUCTURE"
    → STOP report generation
    → FIX missing detail lines
    → RE-RUN audit until 100% pass rate
```

##### 4.6.4: Error-Proof Workflow Execution
```
MANDATORY EXECUTION SEQUENCE:

Step 4.2: Generate hierarchy with application detail structure
   ↓
Step 4.6: Run automated validation (THIS STEP)
   ↓
IF validation PASSES:
   → Proceed to Step 4.4: Save Final Report
   → Mark as ✅ VALIDATED AND COMPLETE
   
IF validation FAILS:
   → DO NOT SAVE REPORT
   → FIX identified issues
   → RE-RUN Step 4.6 validation
   → Only save when validation passes

VALIDATION SUCCESS CRITERIA:
✅ Application count matches exactly (hierarchy = data collection)
✅ Zero applications with incomplete detail structure  
✅ All required icons present (💾, 📈, 👥, 🕒)
✅ No applications listed with just status line
```

---

## 🔄 **REFRESH WORKFLOW**

To regenerate reports with current data using the Enhanced Dynamic Discovery approach:

### **🔄 Complete Dynamic Workflow Execution**

```bash
# Step 1: Platform Insights Discovery
get_platform_insights(includeFlows=true, includeMessages=true, includeDataThroughput=true)

# Step 2: Dynamic Business Group/Environment Extraction  
# Parse insights response to build organization/environment matrix:
discovered_orgs = extract_unique_organizations(insights.response)
discovered_envs = extract_unique_environments_per_org(insights.response)

# Example extracted inventory:
# org-123 ("Acme Corp") → ["Production", "Sandbox"]
# org-456 ("Beta Ltd") → ["Prod", "Dev", "Staging"]  
# org-789 ("Gamma Inc") → ["Live", "Test"]

# Step 3: Dynamic Environment Validation Loop
validated_environments = []
For each (org_id, env_name) in discovered_envs:
  validation_result = list_applications(environmentName=env_name, organizationId=org_id, page=1)
  If validation_result.success:
    validated_environments.append({org_id, org_name, env_name})
  Else:
    log_inaccessible_environment(org_id, env_name, validation_result.error)

# Step 4: Complete Application Collection with Dynamic Pagination  
all_applications = {}
For each validated_env in validated_environments:
  page = 1
  While True:
    apps_response = list_applications(
      environmentName=validated_env.env_name, 
      organizationId=validated_env.org_id, 
      page=page
    )
    
    all_applications[org_id][env_name].extend(apps_response.applications)
    
    If page >= apps_response.total_pages:
      Break
    page += 1

# Step 5: Generate Timestamped Report
load_template("anypoint-assessment-template.md")
populate_template_with_dynamic_data(all_applications, validated_environments)
save_report(f"anypoint-platform-assessment-report-{current_timestamp}.md")
```

---

## 🎯 **Key Dynamic Advantages**

**✅ No Static Environment Names**: Discovers whatever environments actually exist  
**✅ Organization Agnostic**: Works with any number of business groups  
**✅ Auto-Pagination**: Handles environments with 100+ applications automatically  
**✅ Access Control Aware**: Gracefully handles environments with restricted access  
**✅ Future-Proof**: Automatically discovers new environments and organizations  

### **🔧 Implementation Notes**

1. **Discovery Phase**: Extract (org_id, org_name, env_id, env_name) tuples from platform insights
2. **Validation Phase**: Test each discovered environment with actual API calls  
3. **Collection Phase**: Use dynamic pagination to collect ALL applications per environment
4. **Error Handling**: Log and skip inaccessible environments without breaking the workflow
5. **Reporting Phase**: Generate comprehensive report using template with actual discovered data

This approach eliminates static assumptions and provides complete visibility into the actual Anypoint Platform deployment regardless of naming conventions or organizational structure.

---

## ⚠️ **WORKFLOW DATA QUALITY CONSIDERATIONS**

### **Platform Insights Limitations:**
- **Missing env_type Field:** Platform insights may not populate environment type consistently
- **Inconsistent Classification:** Environment classification may be unreliable
- **All Environments Included:** Platform insights aggregates ALL environments (production + sandbox)

### **Environment Validation Requirements:**
1. **Never assume environment type** from platform insights alone
2. **Always test multiple environment name variations** per organization
3. **Cross-reference** environment IDs from insights with successful API calls
4. **User validation required** when environment classification is unclear
5. **Document validated environment names** for future reference

### **Execution Best Practices:**
- **Sequential Execution**: Execute workflow steps in order for proper data dependencies
- **Error Handling**: Continue processing even if some environments are inaccessible
- **Pagination Handling**: Always check for multiple pages in large environments
- **Data Validation**: Cross-reference platform insights with actual application data
- **Template Preservation**: Keep template file unchanged during report generation

---

**Workflow Version:** 1.0  
**Created:** February 12, 2026  
**Purpose:** Separate workflow execution logic from report template  
**Template File:** `anypoint-assessment-template.md`
**Output Format:** `anypoint-platform-assessment-report-YYYYMMDD-HHMMSS.md`
