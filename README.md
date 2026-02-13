# Anypoint Assessment as Code

## 🚀 **Quick Launch**

**`Execute @/anypoint-assessment-workflow.md`**  as the prompt to run in MuleSoft Vibes Agent to start the report generation.

**Generated Report:** `output/anypoint-platform-hierarchy-report-YYYYMMDD-HHMMSS.md`

---

## 🎯 **Overview**

A comprehensive Anypoint Platform assessment and reporting tool that leverages MuleSoft MCP Server tools to generate detailed hierarchy reports across all organizations, environments, and applications.

This project provides an automated, code-driven approach to assess Anypoint Platform deployments, generating detailed reports that include:

- **Complete Platform Hierarchy**: Organizations, environments, and all deployed applications
- **Application Metrics**: Flow counts, message volumes, data throughput, and resource allocation
- **API Management Analysis**: Policy enforcement, consumer counts, and reuse metrics
- **Health & Status Monitoring**: Application health, idle resource identification, and optimization opportunities
- **Dynamic Discovery**: No static configuration required - automatically discovers all accessible environments

## 📋 **Key Features**

### 🔍 **Comprehensive Discovery**
- **Multi-Organization Support**: Automatically discovers and analyzes all accessible organizations
- **Dynamic Environment Detection**: No hardcoded environment names - discovers what actually exists
- **Complete Application Inventory**: Uses pagination to collect ALL applications across all environments
- **Real-Time Data**: Queries live Anypoint Platform APIs for current state

### 📊 **Rich Reporting**
- **Executive Dashboard**: High-level metrics and KPIs for leadership
- **Detailed Hierarchy**: Visual tree structure of entire platform deployment
- **Resource Analytics**: vCore allocation, replica counts, and cost optimization opportunities
- **API Governance**: Policy compliance, consumer analysis, and reuse metrics

### 🛡️ **API Management Insights**
- **Policy Enforcement Tracking**: Which applications are protected by API Manager
- **Consumer Analysis**: Application reuse patterns and adoption metrics  
- **Governance Assessment**: Identify unmanaged applications requiring governance
- **Security Posture**: Policy distribution and compliance status

### 🚦 **Health Monitoring**
- **Application Status**: Running, idle, stopped, and critical application identification
- **Resource Optimization**: Idle application detection for cost savings
- **Performance Metrics**: Message volumes and data throughput analysis
- **Operational Intelligence**: Last update tracking and deployment health

## 🏗️ **Architecture**

### **Assessment Workflow**
```
1. Platform Insights Discovery → Extract organization/environment matrix
2. Dynamic Environment Validation → Test access to discovered environments  
3. Application Collection → Paginated collection of ALL applications
4. API Management Analysis → Policy and consumer data collection
5. Report Generation → Populate template with collected data
6. Quality Validation → Ensure complete and accurate reporting
```

### **Data Sources**
- **Anypoint Platform APIs**: Live platform state via MCP tools
- **Platform Insights**: Aggregated metrics and usage analytics
- **API Manager**: Policy enforcement and consumer data
- **Runtime Manager**: Application deployment and health data

## 📚 **Documentation**

### **Core Files**
- **`README.md`** - This overview and quick start guide
- **`anypoint-assessment-workflow.md`** - Complete step-by-step execution workflow
- **`anypoint-assessment-template.md`** - Report template and structure specification

### **Key Concepts**

#### **Dynamic Discovery**
Unlike static assessment tools, this solution automatically discovers:
- All accessible organizations and environments
- Complete application inventories with pagination
- API Manager instances and policy mappings
- Consumer relationships and reuse patterns

#### **API-Led Architecture Analysis** 
Applications are automatically classified into API-Led tiers:
- 🔵 **System APIs (SAPI)** - Backend system abstractions
- 🟢 **Process APIs (PAPI)** - Business process orchestration  
- 🟠 **Experience APIs (EAPI)** - Channel-specific interfaces
- ⚪ **Other Applications** - Utilities and integrations
- 🧪 **Special Applications** - High-resource or specialized apps

#### **Application Status Classification**
- ✅ **RUNNING** - Active and healthy with traffic
- 💤 **IDLE** - Running but no traffic during insight period
- ⚠️ **APPLYING** - Deployment in progress or issues
- ❌ **STOPPED** - Not running or undeployed
- 🚨 **CRITICAL** - Severe issues requiring immediate attention

## 🛠️ **Prerequisites & Setup**

### **Prerequisites**
- Access to Anypoint Platform with appropriate permissions
- MuleSoft MCP Server tools configured and accessible
- Organization admin or environment admin roles recommended for complete visibility

### **Permissions Required**
- **Runtime Manager**: Application listing and metrics access
- **API Manager**: API instance and policy listing access
- **Platform Insights**: Usage analytics and reuse metrics access
- **Exchange**: Asset browsing for consumer analysis

---

**Assessment Tool Version**: 2.3  
**Last Updated**: February 13, 2026  
**Compatibility**: MuleSoft MCP Server Tools, Anypoint Platform (All Versions)  
**Report Template**: `anypoint-assessment-template.md`  
**Workflow Guide**: `anypoint-assessment-workflow.md`
