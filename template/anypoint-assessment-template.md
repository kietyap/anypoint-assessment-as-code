# Anypoint Platform Assessment Report Template

**Generated:** [To be populated with current datetime - Format: Month DD, YYYY at HH:MM AM/PM (Timezone)]  
**Data Source:** Live Anypoint Platform API with Environment Validation  
**Analysis Method:** MuleSoft MCP Server Tools  
**Scope:** Complete organizational hierarchy across all environments

---

## 📊 **EXECUTIVE SUMMARY**

| **Metric** | **Value** |
|------------|-----------|
| **Total Organizations** | [To be populated] |
| **Active Environments** | [To be populated] |
| **Total Applications** | [To be populated] |
| **Active Applications** | [To be populated] |
| **Total Flows** | [To be populated] |
| **Deployment Platform** | [To be populated] |
| **Total vCores Allocated** | [To be populated] |
| **Applications with Issues** | [To be populated] |

---

## 🏢 **ANYPOINT PLATFORM DEPLOYMENT HIERARCHY**

```
🏢 ANYPOINT PLATFORM DEPLOYMENT
│
├─── 🏛️ [Organization Name] ([org-id])
│    └─── 📁 [Environment Name] ([env-id])
│         ├─── 🔵 SYSTEM APIS ([X] apps)
│         │    ├─── [app-name] ✅ RUNNING
│         │    │    ├─── ⚙️ Runtime: [Mule Version]
│         │    │    ├─── 💾 [Platform]: [vCores] vCore × [replicas] replica/worker
│         │    │    ├─── 📈 CPU: [cpu_usage] | Flows: [count] | Messages: [count] | Data: [amount] GB 
│         │    │    ├─── 👥 Consumers: [count]
│         │    │    └─── 🔗 API Reference: [API Instance/ID or "Not Managed"]
│         │    │    │     └─── 📋 Policies: [Policy1 v1.0] or "None"
│         │    │    │     └─── 📋 Policies: [Policy2 v1.1] or "None" 
│         │    │    │           └─── 👤 Clients: [count] active contract(s) (when policy is related to client id enforcement)
│         │    │    └─── 🕒 Last Updated: [Date]
│         ├─── 🟢 PROCESS APIS ([X] apps)
│         │    ├─── [app-name] ✅ RUNNING
│         │    │    ├─── ⚙️ Runtime: [Mule Version]
│         │    │    ├─── 💾 [Platform]: [vCores] vCore × [replicas] replica/worker
│         │    │    ├─── 📈 CPU: [cpu_usage] | Flows: [count] | Messages: [count] | Data: [amount] GB 
│         │    │    ├─── 👥 Consumers: [count]
│         │    │    └─── 🔗 API Reference: [API Instance/ID or "Not Managed"]
│         │    │    │     └─── 📋 Policies: [Policy1 v1.0] or "None"
│         │    │    │     └─── 📋 Policies: [Policy2 v1.1] or "None" 
│         │    │    │           └─── 👤 Clients: [count] active contract(s) (when policy is related to client id enforcement)
│         │    │    └─── 🕒 Last Updated: [Date]
│         └─── 🟠 EXPERIENCE APIS ([X] apps)
│         │    ├─── [app-name] ✅ RUNNING
│         │    │    ├─── ⚙️ Runtime: [Mule Version]
│         │    │    ├─── 💾 [Platform]: [vCores] vCore × [replicas] replica/worker
│         │    │    ├─── 📈 CPU: [cpu_usage] | Flows: [count] | Messages: [count] | Data: [amount] GB 
│         │    │    ├─── 👥 Consumers: [count]
│         │    │    └─── 🔗 API Reference: [API Instance/ID or "Not Managed"]
│         │    │    │     └─── 📋 Policies: [Policy1 v1.0] or "None"
│         │    │    │     └─── 📋 Policies: [Policy2 v1.1] or "None" 
│         │    │    │           └─── 👤 Clients: [count] active contract(s) (when policy is related to client id enforcement)
│         │    │    └─── 🕒 Last Updated: [Date]
│         └─── 🟡 API Proxies ([X] apis)
│              └─── [api-name] ✅ RUNNING (when the api-name can not be match with an application above)
│                   ├─── ⚙️ Runtime: [Mule or Flex Gateway] [Version]
│                   └─── 🔗 API Reference: [API Instance/ID or "Not Managed"]
│                   │     └─── 📋 Policies: [Policy1 v1.0] or "None"
│                   │     └─── 📋 Policies: [Policy2 v1.1] or "None" 
│                   │           └─── 👤 Clients: [count] active contract(s) (when policy is related to client id enforcement)
│                   └─── 🕒 Last Updated: [Date]
``

---

## 📈 **DEPLOYMENT METRICS SUMMARY**

### **📊 Organization Overview**

| **Organization** | **Environment** | **Apps** | **Flows*** | **vCores** | **Platform** |
|------------------|-----------------|----------|-------------|------------|--------------|
| 🏛️ **[Organization Name]** | [Environment] | **[X]** | **[X]** | [X] | [Platform] |
| 🏛️ **[Organization Name]** | [Environment] | **[X]** | **[X]** | [X] | [Platform] |
| 🏛️ **[Organization Name]** | [Environment] | **[X]** | **[X]** | [X] | [Platform] |
| **📊 PLATFORM TOTALS** | **[X] Envs** | **[X]** | **[X]** | **[X]** | **Mixed** |

### **🚦 Application Status Distribution**

| **Organization** | **✅ Running** | **💤 Idle** | **⚠️ Issues** | **❌ Stopped** | **🚨 Critical** |
|------------------|----------------|-------------|---------------|----------------|-----------------|
| 🏛️ **[Organization Name]** | 🟢 **[X]** | 🟡 **[X]** | 🟢 **[X]** | 🟢 **[X]** | 🟢 **[X]** |
| 🏛️ **[Organization Name]** | 🟢 **[X]** | 🟠 **[X]** | 🟢 **[X]** | 🔴 **[X]** | 🔴 **[X]** |
| 🏛️ **[Organization Name]** | 🟢 **[X]** | 🟢 **[X]** | 🟢 **[X]** | 🟢 **[X]** | 🟢 **[X]** |
| **📊 TOTALS** | 🟢 **[X]** | 🟠 **[X]** | 🟢 **[X]** | 🔴 **[X]** | 🔴 **[X]** |

### **🛡️ API Management & Reuse Metrics**

| **Organization** | **API Managed Apps** | **Total Policies** | **High Reuse Apps** | **Total Consumers** |
|------------------|---------------------|---------------------|---------------------|---------------------|
| 🏛️ **[Organization Name]** | **[X]** | **[X]** | **[X]** | **[X]** |
| 🏛️ **[Organization Name]** | **[X]** | **[X]** | **[X]** | **[X]** |
| 🏛️ **[Organization Name]** | **[X]** | **[X]** | **[X]** | **[X]** |
| **📊 TOTALS** | **[X]** | **[X]** | **[X]** | **[X]** |

### **📋 Status Legend & Health Metrics**

**Application Status Types:**
- ✅ **RUNNING** - Active and healthy with traffic
- 💤 **IDLE** - Running but no traffic during insight period  
- ⚠️ **ISSUES** - Deployment in progress or applying updates
- ❌ **STOPPED** - Not running (includes undeployed applications)
- 🚨 **CRITICAL** - Severe issues (CrashLoopBackOff, failed deployments)

**Platform Distribution:**
- **CloudHub 2.0**: [X] applications ([X]% of total)
- **CloudHub Legacy**: [X] applications ([X]% of total)  
- **Runtime Fabric**: [X] applications ([X]% of total)

**API Management & Reuse Distribution:**
- **API Managed**: [X] applications ([X]% of total)
- **Unmanaged**: [X] applications ([X]% of total)
- **High Reuse (5+ consumers)**: [X] applications
- **Total API Policies**: [X] policies across all managed applications

### **⚠️ Data Quality & Action Items**

**Flow Count Accuracy:**
- **(*) Asterisk indicates potential flow count inaccuracy** - Applications marked with (*) may have missing or unreliable flow metrics from platform insights
- **Root Cause**: Older Mule Runtime versions do not properly report flow metrics to platform insights API
- **Action Required**: Update Mule Runtime versions for flagged applications to ensure accurate reporting

**Immediate Actions Required:**
- 🚨 **Critical Issues**: [X] applications need immediate attention
- ⚠️ **Runtime Updates**: [X] applications need Mule Runtime version updates for accurate metrics
- 💤 **Resource Optimization**: [X] idle applications consuming [X] vCores can be optimized
- 🛡️ **API Governance**: [X] unmanaged applications should be evaluated for API Manager protection
- 👥 **Reuse Opportunities**: [X] low-reuse applications could benefit from better promotion/discovery

### **Key Findings**

- [Finding 1]
- [Finding 2]
- [Finding 3]

---

## 🎨 **LEGEND**

### **API Tier Colors:**
- 🔵 **System APIs (SAPI)** - Backend system abstractions
- 🟢 **Process APIs (PAPI)** - Business process orchestration  
- 🟠 **Experience APIs (EAPI)** - Channel-specific interfaces
- 🟡 **Proxies API** - Entries in list_api_instance but can't be matched with an app
- ⚪ **Other Applications** - Utilities, demos, integrations
- 🧪 **Special Applications** - High-resource or specialized apps

### **Status Icons:**
- ✅ **RUNNING/STARTED** - Application is active and healthy with traffic
- 💤 **IDLE** - Application is running but has no traffic during insight period
- ⚠️ **APPLYING** - Application deployment in progress (may have issues)
- ❌ **STOPPED** - Application not running (includes NOT_RUNNING/UNDEPLOYED)
- 🚨 **CRITICAL** - Application with severe issues (CrashLoopBackOff)

### **Application Detail Icons:**
- 👥 **Consumers** - Number of applications/systems consuming this API
- 🛡️ **API Policies** - Applied API Manager policies (security, rate limiting, etc.)
- 🔗 **API Reference** - API Manager instance details with dedicated sub-nodes
- 📋 **Policies** - Detailed policy information (names and versions)
- 👤 **Clients** - Subscribed client applications for the API instance

### **Environment Types:**
- 📁 **Production** - Production environment
- 📁 **Sandbox** - Development/testing environment

---

**Report Generated by:** MuleSoft MCP Server Tools  
**Data Accuracy:** Live platform state  
**Output File Format:** `anypoint-platform-hierarchy-report-YYYYMMDD-HHMMSS.md`

---

**Template Version:** 2.4  
**Last Updated:** February 14, 2026  
**Enhancement:** Added comprehensive API Reference details including API Instance ID, Policy Names/Versions, and Subscribed Client Applications to application hierarchy  
**Workflow Guide:** See `anypoint-assessment-workflow.md` for MCP tool execution steps
