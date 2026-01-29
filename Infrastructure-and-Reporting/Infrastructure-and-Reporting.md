📄 **Indian Net Bank (iNB)**
**Infrastructure Architecture Document**
(Azure-Hosted ASP.NET Application)

**1. Purpose of the Document**
This document describes the infrastructure architecture for the Indian Net Bank (iNB) Online Banking Application.
The objective is to define a secure, scalable, and highly available infrastructure to support the migration from Classic ASP to ASP.NET, using Microsoft Azure as the hosting platform.
This document is submitted as part of the Upgrade to Architect Training Assessment.

**2. Infrastructure Architecture Overview**
The proposed infrastructure leverages Microsoft Azure Cloud and follows a layered deployment model, while fully complying with the requirement to deploy the application on IIS.
The architecture supports:
•	High availability
•	Security
•	Performance
•	Ease of deployment
•	Future scalability

**3. Deployment Model**
Cloud Model: Public Cloud
Deployment Approach: Hybrid IaaS / PaaS
Hosting Platform: Microsoft Azure

**4. Logical Infrastructure Architecture**
**4.1 High-Level Flow**
End User
→ Internet
→ Azure Load Balancer
→ ASP.NET Application (IIS)
→ Business Services
→ Azure SQL Database

**5. Azure Infrastructure Components**
**5.1 Web Tier**
Service Used: Azure App Service (Windows)
**Responsibilities:**
•	Hosts ASP.NET web application
•	Provides IIS runtime environment
•	Manages HTTP/HTTPS requests
•	Enforces SSL encryption
**Key Features:**
•	Built-in IIS
•	Auto-scaling
•	High availability
•	Load balancing

**5.2 Application Tier**
**Service Used:**
•	Azure App Service (Business Layer)
or
•	Azure Virtual Machines (Windows Server + IIS)

**Responsibilities:**
•	Implements business rules
•	Handles account processing
•	Manages cheque workflows
•	Enforces security policies

**5.3 Database Tier
Service Used: Azure SQL Database
Responsibilities:**
•	Stores customer data
•	Stores account and transaction data
•	Maintains cheque and bill payment records
•	Supports reporting and reconciliation
**Database Features:**
•	Automated backups
•	High availability
•	Transparent Data Encryption (TDE)
•	Firewall and access control

**5.4 Storage Tier
Service Used: Azure Blob Storage
Used For:**
•	Scanned cheque slips
•	Generated reports
•	Audit files
•	Exported statements (PDF/Excel)


**6. IIS Deployment Strategy**
The ASP.NET application is deployed on IIS, which is:
•	Managed internally by Azure App Service (Windows)
•	Fully compliant with IIS deployment requirements
**IIS Configuration:**
•	Integrated Pipeline Mode
•	Application Pool isolation
•	Session management
•	HTTPS enforced

**7. Security Infrastructure
7.1 Network Security**
•	Azure Network Security Groups (NSG)
•	Restricted inbound and outbound traffic
•	SQL firewall rules
**7.2 Application Security**
•	Forms Authentication
•	Account lock after 3 invalid login attempts
•	Role-based authorization
•	Secure session handling
**7.3 Data Security**
•	Encryption at rest (Azure SQL TDE)
•	Encryption in transit (HTTPS)
•	Secure credential management

**8. Availability & Scalability
8.1 High Availability**
•	Built-in redundancy in Azure App Service
•	Database replication
•	Automated failover
**8.2 Scalability**
•	Horizontal scaling of web tier
•	Stateless application design
•	Elastic database scaling

**9. Performance Considerations**
•	Connection pooling via ADO.NET
•	Indexed transaction tables
•	Caching of static configuration data
•	Background jobs for:
o	Interest calculation
o	Overdraft charge computation

**10. Monitoring & Logging**
**Azure Services Used:**
•	Azure Monitor
•	Application Insights
**Monitored Metrics:**
•	Application availability
•	Response time
•	Failed login attempts
•	Database performance

**11. Reporting Infrastructure**
**11.1 Report Types**
•	Mini Statements
•	Detailed Account Statements
•	Cheque Reconciliation Reports
•	Overdraft & Interest Reports
•	Security Audit Reports
**11.2 Reporting Flow**
Azure SQL Views / Stored Procedures
→ Reporting Module
→ PDF / Excel Export
→ Azure Blob Storage

**12. Environment Separation**
Environment	Purpose
Development	Feature development
Testing	Functional & UAT
Production	Live banking operations
Each environment uses separate Azure resources to ensure isolation.

**13. Justification for Azure Infrastructure**
Requirement	Azure Capability
IIS Hosting	Native IIS support
Scalability	Auto-scaling
Security	Defense-in-depth
Availability	Built-in redundancy
Cost Control	Pay-as-you-go
Future Readiness	Cloud-native

**14. Conclusion**
The proposed Azure-based infrastructure provides a robust, secure, and scalable platform for hosting the Indian Net Bank Online Banking Application.
It supports the ASP to ASP.NET migration, complies with IIS deployment requirements, and meets enterprise architecture standards expected in an Upgrade to Architect training program.

