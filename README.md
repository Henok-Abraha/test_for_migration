# Lab 4 — Cloud Migration Discovery and Assessment

**Name:** Henok Abraha  
**Course:** CST8913 - Cloud Migration  
**Section:** 12  
**Lab:** Lab 4  
**Date:** October 1, 2026

---

### 1. Digital Estate Understanding

- By digital estate means knowing all the technology assets that you have and use in your organization. This technology assets are applications, VMs, data and ect..

- Before migration knowing our Digital estate is important and it's the first thing we do before planning our cloud migration. The reason for this is first we have to know what we have on our premises so we can plan our migration to the cloud.

- We have a tool called Azure Migrate appliance which is a component of Azure Migrate which helps us with our estimate assessment. If we do our migration without adequate discovery and assessment there will be risks of whether on premises machines are ready to migrate to the cloud, we will not have an estimate of how many or the sizes of the VMs we need on the cloud and we will not have an estimate of what the cost will be migrate and use the cloud.

For this mid-sized organization we where given the on premises environment information which are all the 5 servers that they have and basic info about them like name, operating system and roles. This information would not be enough and risky to migrate tehm to the cloud.




### 3. Dependency Analysis

#### 1. Confirmed Dependency

Based on the information provided, the only confirmed dependency is that **APP-01 depends on DB-01**. The application server needs the SQL Server database to access and store application data.

Other possible dependencies would need to be investigated before migration.

#### 2. Assumed Dependencies

The following dependencies are possible but are **not confirmed by the provided information** and would need to be investigated:

- **WEB-01 → APP-01:** The IIS web server may communicate with the application server to process requests.
- **Other servers → FILE-01:** Applications or users may depend on FILE-01 for shared files or data.

#### 3. Dependency Analysis and Risk of Outages

Dependency analysis reduces the risk by showing which servers and applications rely on each other. This helps the migration team understand which systems need to remain connected and plan the correct migration order.

#### 4. Validating Assumed Dependencies

To validate the assumed dependencies, first I would use the Azure **Migrate appliance** to discover the VMware environment and collect information about the servers.

I would use dependency analysis to identify communication between servers, including which machines communicate with each other and the network connections between them.


#### Dependency Diagram (Mermaid)

![Dependency Diagram](images/Dependency_Diagram.png)

### 4. Azure Readiness and Suitability

| Server | Readiness Category | Justification and Missing Information | Remediation or Next Step |
|---|---|---|---|
| **WEB-01** | Insufficient Information | WEB-01 runs IIS on Windows Server 2019 and is public-facing. However, CPU, memory, storage, IIS/application versions, utilization, security requirements, and detailed dependencies are not provided. It is also not confirmed whether WEB-01 depends on APP-01 or other servers. | We use Azure Migrate discovery and dependency analysis to collect configuration and performance data. Identify IIS applications, network connections, ports, security requirements, and dependencies before confirming readiness. |
| **APP-01** | Insufficient Information | APP-01 runs Windows Server 2016 and has a confirmed dependency on DB-01. However, the application name, version, system requirements, CPU, memory, storage, utilization, and other dependencies are unknown. This prevents a confirmed readiness decision. | Discover the installed application and its requirements. Collect configuration and performance data and validate all dependencies. Plan APP-01 and DB-01 as a coordinated migration group if assessment confirms suitability. |
| **DB-01** | Insufficient Information | DB-01 runs SQL Server on Windows Server 2016 and is mission-critical. The SQL Server version is not provided, and database size, CPU, memory, storage, performance, backup requirements, availability requirements, and acceptable downtime are unknown. | Identify the SQL Server version and check its compatibility with the target Azure VM environment. Collect database and performance information and confirm backup, recovery, security, availability, and downtime requirements before migration. |
| **DC-01** | Insufficient Information | DC-01 runs Active Directory Domain Services on Windows Server 2019 and provides identity services. The scenario does not identify which servers or applications depend on it, and DNS, authentication, replication, configuration, and availability requirements are not provided. | Discover and validate dependencies on Active Directory and DNS. Review the AD DS configuration, security requirements, replication, and availability requirements before planning migration. |
| **FILE-01** | Conditionally Ready | FILE-01 runs Windows Server 2012 R2, which is identified as a legacy operating system. Its storage usage, file shares, permissions, applications, dependencies, and performance requirements are unknown. Migration may be feasible, but the legacy operating system and missing workload information must be addressed. | Assess application and file-share compatibility, storage requirements, permissions, and dependencies. Evaluate upgrading the operating system to a supported Windows Server version before or as part of the migration plan. |


### 5. Sizing and Cost Considerations

#### 1. Azure Migrate Sizing and Cost Estimation

After the on premisses servers are marked as ready for Azure, the Azure Migrate appliance tool makes sizing recommendations that identify the Azure VM SKU, disk type for your machines and based on the performance history. Based on the Azure VM sizing needed the Azure Migrate appliance tool estimates the cost estimation.

#### 2. Performance-Based Sizing and As-Is Sizing

Performance-based sizing uses the **actual utilization and performance data** collected from the servers, such as CPU and memory usage.

As-is sizing uses the server's **current configuration** rather than its actual utilization. The Azure VM is sized based on the existing CPU, memory, storage, and other configured resources.

#### 3. Configuration and Utilization Data

To recommend appropriate Azure VM sizes, I would collect both **configuration** and **utilization** data from each server, such as Number of CPU cores, Amount of RAM, Disk size and storage configuration, Network configuration and Operating system. For the **utilization** i would collect Storage usage, Network traffic and peak CPU.

#### 4. Factors That Could Affect Azure Operating Cost

The **three factors** that could affect the estimated Azure operating cost would be the:

- The azures vm size: It means that it will depend on the configuration of the vms like for example with more CPU and memory generally will cost more than smaller VMs
- **Storage:** The amount and type of disk storage required can affect the total cost.
- **Usage:** which is The number of hours the VMs will be running.

#### Sizing and Cost Estimates

To make an estimated **Sizing and Cost for** cloud migration for this mid sized organization wouldnt be possible because the inventory provided is insufficient. We would require more information like **CPU, memory, storage configuration, or utilization data for all 5 servers.**

**Without this information, it is not possible to determine how many resources each workload actually needs or select an appropriate Azure VM size.**


### 6. Migration Prioritization and Plan

For the migration **Prioritization and Plan we will put them in 5 sections**

#### Preparation

The first step would be the preparation. By this i intend to have the digital estate of the environments. I will start by usnif the Azure Migrate Tools to identify and collect all CPU, memory, storage, performance, application, and dependency information for all 5 servers.

#### First Workload or Pilot

I would choose **WEB-01** for the first test migration because it is not listed as a mission-critical server. I would migrate a test version of WEB-01 to Azure without affecting the real production server or sending users to the test server.

Before testing WEB-01, I would first check its dependencies. For example, if WEB-01 depends on APP-01 or another server, I would make sure the connection can be tested safely without affecting the production systems.

#### Migration Order

WEB-01 would be migrated first as a test to make sure the migration process works correctly. After that, FILE-01 could be migrated after checking its old operating system and its dependencies.

**APP-01 and DB-01 should be migrated together as one group** because we already know that APP-01 depends on DB-01. They should be migrated after the other tests are successful because DB-01 is a mission-critical server.

DC-01 could be migrated after checking how the other servers depend on Active Directory, DNS and identity services. The migration order can also change if the dependency analysis finds new dependencies between the servers.

#### Validation

After each migration, I would verify that the VM starts correctly and that its applications and services are running. I would test network connectivity, application functionality, authentication, database connections, file access, and performance where applicable.

#### Rollback

A rollback would be needed if an important service does not work after migration. For example, if APP-01 cannot connect to DB-01 or there is too much downtime.

In this case, I would stop the migration and move the service back to the original on-premises environment. Then I would fix the problem before trying the migration again.



## References

- Algonquin College. (2026). *Week 4: Cloud Migration Discovery and Assessment*. CST8913 Cloud Migration.
  https://brightspace.algonquincollege.com/d2l/le/content/932966/viewContent/13569286/View

- Microsoft. (n.d.). *About Azure Migrate*. Microsoft Learn.  
  https://learn.microsoft.com/en-us/azure/migrate/migrate-services-overview

- Microsoft. (n.d.). *Azure Migrate discovery and assessment*. Microsoft Learn.  
  https://learn.microsoft.com/en-us/azure/migrate/migrate-appliance

- Microsoft. (n.d.). *Assess VMware VMs for migration to Azure*. Microsoft Learn.  
  https://learn.microsoft.com/en-us/azure/migrate/tutorial-assess-vmware-azure-vm
