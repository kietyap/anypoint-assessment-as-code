# Anypoint Assessment as Code

A comprehensive Anypoint Platform assessment and reporting tool that leverages MuleSoft MCP Server tools to generate detailed hierarchy reports across all organizations, environments, and applications.

## 🎯 **Overview**

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

## 🚀 **Quick Start**

### **Prerequisites**
- Access to Anypoint Platform with appropriate permissions
- MuleSoft MCP Server tools configured and accessible
- Organization admin or environment admin roles recommended for complete visibility

### **Basic Usage**

1. **Execute Assessment Workflow**
   Follow the step-by-step process in `anypoint-assessment-workflow.md`:

   ```bash
   # Step 1: Get platform insights
   get_platform_insights(includeFlows=true, includeMessages=true, includeDataThroughput=true)
   
   # Step 2: Get reuse metrics  
   get_reuse_metrics(includeReuseMetrics=true)
   
   # Step 3: Dynamic environment discovery and validation
   # (Extract from platform insights, validate with list_applications)
   
   # Step 4: Complete application collection with pagination
   # (Collect ALL applications across all validated environments)
   
   # Step 5: API Manager policy and consumer analysis
   # (Match applications with API instances, collect policies)
   
   # Step 6: Generate timestamped report using template
   ```

2. **Generated Report**
   The assessment produces a timestamped report:
   ```
   anypoint-platform-hierarchy-report-YYYYMMDD-HHMMSS.md
   ```

### **Output Example**
```
📊 EXECUTIVE SUMMARY
Total Organizations: 3
Active Environments: 8  
Total Applications: 47
Active Applications: 35
Total vCores Allocated: 12.4
Applications with Issues: 2

🏢 ANYPOINT PLATFORM DEPLOYMENT HIERARCHY
🏛️ ANA (a1b2c3d4-e5f6-7890-abcd-ef1234567890)
└─── 📁 Sandbox (env-12345)
     ├─── 🔵 SYSTEM APIS (3 apps)
     │    ├─── ana-product-sapi ✅ RUNNING
     │    │    ├─── 💾 CloudHub 2.0: 0.1 vCore × 1 replica
     │    │    ├─── 📈 Flows: 8 | Messages: 15,234 | Data: 2.1 GB throughput
     │    │    ├─── 👥 Consumers: 12 | 🛡️ API Policies: 3 (Client ID Enforcement, Rate Limiting, CORS)
     │    │    └─── 🕒 Last Updated: Feb 10, 2026
```

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

## 🔧 **Advanced Configuration**

### **Environment Access**
The tool automatically handles environments with different access levels:
- **Full Access**: Complete application and metrics data
- **Limited Access**: Application list without detailed metrics
- **No Access**: Environment discovered but inaccessible (logged and skipped)

### **Pagination Handling** 
Automatically handles large environments:
- Dynamic pagination for environments with 100+ applications
- Complete data collection regardless of environment size
- Progress tracking for large assessment operations

### **API Management Matching**
Intelligent application-to-API instance matching using multiple strategies:
- **Exact Match**: Direct name correspondence
- **Contains Match**: Flexible name pattern matching
- **Fuzzy Match**: Word overlap and semantic analysis
- **Cleaned Match**: Remove prefixes/suffixes for better matching

## 📈 **Use Cases**

### **Platform Governance**
- **Policy Compliance**: Identify unmanaged applications requiring API governance
- **Resource Optimization**: Find idle applications consuming unnecessary resources
- **Security Assessment**: Analyze API policy distribution and enforcement
- **Cost Analysis**: vCore allocation and optimization opportunities

### **Migration Planning**
- **Runtime Modernization**: Identify applications on legacy Mule runtime versions
- **Platform Migration**: Complete inventory for CloudHub 2.0 migration planning
- **Architecture Assessment**: API-Led adoption and maturity analysis

### **Operations & Monitoring**
- **Health Dashboards**: Application status and performance monitoring
- **Capacity Planning**: Resource utilization and scaling requirements
- **Consumer Analysis**: API adoption and reuse pattern identification
- **Change Impact**: Understanding application dependencies and relationships

## 🎨 **Report Customization**

### **Template Modification**
The report template (`anypoint-assessment-template.md`) can be customized:
- Add custom sections or metrics
- Modify color schemes and icons
- Include additional data sources
- Adjust formatting and layout

### **Data Extension**
The workflow can be extended to include:
- Custom connector usage analysis
- SLA monitoring and performance metrics
- Integration pattern analysis
- Business domain mapping

## 🔍 **Quality Assurance**

### **Data Validation**
The assessment includes multiple validation layers:
- **Application Completeness**: Ensures all discovered applications are documented
- **Status Accuracy**: Cross-validates application status across data sources  
- **Metric Consistency**: Validates message counts and throughput data
- **Structure Compliance**: Ensures report format consistency

### **Error Handling**
Robust error handling for production environments:
- **Environment Access**: Gracefully handles restricted environments
- **API Rate Limits**: Implements appropriate throttling and retry logic
- **Data Quality**: Marks incomplete data clearly rather than failing
- **Partial Success**: Continues assessment even with some failed components

## 🤝 **Contributing**

### **Enhancement Areas**
- Additional MCP tool integrations
- Custom reporting formats (JSON, CSV, Excel)
- Real-time monitoring capabilities  
- Integration with external systems (ITSM, monitoring tools)

### **Data Quality Improvements**
- Enhanced application classification algorithms
- Extended API management analysis
- Performance metrics correlation
- Historical trend analysis

## 📄 **License**

This project is part of the MuleSoft ecosystem and follows standard MuleSoft licensing terms.

## 🆘 **Support**

For questions, issues, or enhancements:
1. Review the workflow documentation thoroughly
2. Check data access permissions in Anypoint Platform
3. Validate MCP tool connectivity and configuration
4. Consult MuleSoft documentation for API-specific questions

---

**Assessment Tool Version**: 2.3  
**Last Updated**: February 13, 2026  
**Compatibility**: MuleSoft MCP Server Tools, Anypoint Platform (All Versions)  
**Report Template**: `anypoint-assessment-template.md`  
**Workflow Guide**: `anypoint-assessment-workflow.md`
