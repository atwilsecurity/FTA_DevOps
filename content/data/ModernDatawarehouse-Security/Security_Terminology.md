## Terminology

### Authentication

Authentication is the process of verifying the identity of the user or application. A single source identity provider is preferred, which handles identity management and authentication. This provider is known as a directory service. It provides methods for storing directory data and making this data available to network users and administrators.

In the cloud, Azure Active Directory (Azure AD) is a centralized identity provider and the preferred source for identity management. Delegating authentication and authorization to Azure AD enables scenarios such as conditional access policies that require a user to be in a specific location. It supports multifactor authentication to increase the level of access security. Configure data lake data store services with Azure AD integration when possible.

For data services that don't support Azure AD, use access key or token for authentication. The client should store the access key in a key management store such as Azure Key Vault.

Authentication scenarios for the data management and analytics scenario are:
•	User authentication
•	Application and service-to-service authentication

**User Authentication** 

Users who connect to a data service or resource must present a credential. This credential proves that users are who they claim. Then they can access the service or resource. Authentication also allows the service to know the identity of the users. The service decides what a user can see and do after the identity is verified.

Azure Data Lake Storage Gen2, Azure SQL Database, and Azure Synapse support Azure AD integration. The interactive user authentication mode requires users to provide credentials in a dialog box.

**Application and service-to-service authentication**

These requests aren't associated with a specific user or there's no user available to enter credentials.

Even if a service accesses another service without human interactions, the service must present a valid identity. This identity proves that the service is real. The accessed service can use the identity to decide what the service can do.

For service-to-service authentication, the preferred method for authenticating Azure services is managed identities. Managed identities for Azure resources allow for authentication to any service that supports Azure AD authentication without any explicit credentials. 

**Managed Identities**

Managed identities are service principals, which can only be used with Azure resources. For example, a managed identity can be created directly for an Azure Data Factory instance. This managed identity is an object registered to Azure AD. It represents this Data Factory instance. This identity can then be used to authenticate to any service, such as Data Lake Storage, without any credential in the code. Azure takes care of the credentials that are used by the service instance. The identity can grant authorization to Azure service resources, such as a folder in the Azure Data Lake Storage. When you delete this Data Factory instance, Azure cleans up the identity in Azure AD.

Managed identities should be used to authenticate an Azure service to another Azure service or resource. They provide the following benefits:

•	A managed identity represents the service for which it's created. It doesn't represent an interactive user.

•	Managed identity credentials are maintained, managed, and stored in Azure AD. There's no password for a user to keep.

•	With managed identities, the client services don't use passwords.

•	The system-assigned managed identity is deleted when the service instance is deleted

**Service Principals**

An Azure service principal is a security identity used by user-created apps, services, and automation tools to access specific Azure resources. Think of it as a 'user identity' (login and password or certificate) with a specific role, and tightly controlled permissions to access your resources.

The key difference between Azure service principals and managed identities is that, with the latter, admins do not have to manage credentials, including passwords.

[Authentication for cloud-scale analytics in Azure | Microsoft Learn](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/scenarios/data-management/secure-authentication)

**Access Control**

Authorization is the act of granting an authenticated party permission to perform an action. The key principle of access control is giving users only the amount of access that they need to do their jobs and only allowing certain actions at a particular scope. Role-based security/role-based access control (RBAC) corresponds to access control.

When using Azure Active Directory (Azure AD) as the centralized identity provider, authorization to access data services and storage can be granted per user or per application and is based on an Azure AD identity. 

Authorization covers RBAC to the service and access control list at the file, folder, or object level in the storage.

Microsoft Azure includes standard and built-in RBAC, which is an authorization system built on Azure Resource Manager that provides detailed access management to Azure resources. RBAC roles help to control access levels to resources; what can a security principal, user, group, service, or application do with the resources and areas to which they have access?

When planning an access control strategy, it's recommended to grant users only the amount of access that they need to perform their jobs and only allow certain actions at a particular scope.

| Access       | Description                                                  |
| :----------- | :----------------------------------------------------------- |
| Owner:       | This role has full access to the resource and can manage everything about the resource, including the right to grant access to it. |
| Contributor: | This role can manage the resource but can't grant access to it. |
| Reader:      | This role can view the resource and information about it (except for sensitive information like access keys or secrets), but they can't make any changes to the resource. |


Some services have specific RBAC roles like Storage Blob Data Contributor or Data Factory Contributor, which means that specific RBAC roles should be used for these services. RBAC is an additive model where adding role assignments is an active permission. RBAC also supports deny assignments which take precedence over role assignments.

**The following best practices can help you to get started with RBAC:**

•	**Use RBAC roles for service management and operations, and use service-specific roles for data access and workload-specific tasks:**  Security principals that need to access data within storage don't require an RBAC role on the resource, because they don't need to manage it. Instead, grant permission to data objects directly. For example, grant read access to a folder in Azure Data Lake Storage Gen2 or a contained database user and table permission on a database in Azure SQL Database.

•	**Use built-in RBAC roles:** First, use the built-in RBAC Azure resource roles to manage services and assign operations roles to control access. Create and use custom roles for Azure resources only when built-in roles don't meet specific needs.

•	**Use groups to manage access:** Assign access to Azure AD groups, and manage group memberships for ongoing access management.

•	**Subscription and resource group scopes:** While it makes sense to grant access at the resource group scope to separate service management and operations access needs versus granting access to individual resources (especially in the nonproduction environment), you can instead grant access to individual resources for workload-specific tasks like data lake file system support and operations, especially in the production environment. This is because in nonproduction environments, developers and testers will need to manage resources like creating an Azure Data Factory ingestion pipeline or creating a container in Data Lake Storage Gen2. While in production, users only need to use resources like viewing the status of a scheduled Data Factory ingestion pipeline or reading data files in Data Lake Storage Gen2.

•	**Don't grant unnecessary access at the subscription scope:** This scope covers all resources within the subscription.

•	**Opt for least-privilege access**: Select the right and only role for the job.

[Data management and role-based access control for cloud-scale analytics in Azure | Microsoft Learn](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/scenarios/cloud-scale-analytics/secure-analytics-role-based-access-control)
