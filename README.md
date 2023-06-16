# Principle-of-Hashicorp-Vault
Implementing HCP Vault, cluster and administration

Vault comes with various pluggable components called secrets engines and authentication methods allowing you to integrate with external systems. The purpose of those components is to manage and protect your secrets in dynamic infrastructure (e.g. database credentials, passwords, API keys).

HashiCorp Cloud Platform (HCP) Vault is a fully managed implementation of Vault which is operated by HashiCorp, allowing organizations to get up and running quickly. HCP Vault provides a consistent user experience compared to a self-managed Vault cluster. You can use the same Vault clients to communicate with HCP Vault as you use to communicate with self-managed Vault.

If an organization chooses to allow a public connection, the HCP Vault cluster will have an associated public address where clients can directly connect to Vault. Most often an organization disables the public connection for improved security. The organization can then establish a peering connection between their cloud provider and a HashiCorp Virtual Network (HVN). This ensures that only trusted clients (users, applications, containers, etc.) running in the peered public cloud provider connect to Vault and avoid systems outside of the selected network attempting to connect.


Self-managed vs. HCP Vault cluster

Here is a quick comparison between a self-managed Vault cluster and an HCP Vault cluster.

Self-managed	HCP Vault

Vault Edition	Vault OSS or Vault Enterprise	Vault Enterprise

Storage backend	Choose one and self-manage	Integrated Storage
Seal	

Seal uses Shamir's Secret Sharing algorithm to generate key shares by default.	Auto-unseal is configured. A unique Key Management Service (KMS) key is created for each cluster.

Vault version	Self-manage the upgrade process	The minor versions are upgraded for you automatically. See the Vault Version documentation for more detail.

Top-level Namespace	root	admin

Root/admin token	Vault initialization process generates a root token. To regenerate a root token, unseal keys or recovery keys are required.	Click on the Generate token button via HCP Vault Portal returns an admin token which is valid for 6 hours.

Advanced Data Protection (ADP) features	Available with license	Currently, not available

Enterprise Replication	DR Replication requires Enterprise Standard, and Performance Replication is part of Enterprise Premium.	Performance Replication is available with HCP Vault Plus.

Auth methods	No limitation	A subset of available auth methods have been validated on HCP Vault. Additional auth methods will be validated over time. Refer to Validated secrets engines and auth methods documentation for more details.

Secrets Engines	No restriction	A subset of available secrets engines have been validated on HCP Vault. Additional secrets engines will be validated over time. Refer to the Security Overview documentation for more details.

Cluster Scaling	No built in feature to scale the cluster size up or down.	Scale your cluster size dynamically via the HashiCorp Cloud Platform Portal or Terraform.
S
entinel	Available with license	Available with HCP Vault Plus.

Create a Vault cluster on HCP
* 		4min|HCP HCP Vault  Vault

HashiCorp Cloud Platform (HCP) Vault enables you to quickly deploy a Vault Enterprise cluster in a supported public cloud provider. As a fully managed service, it allows you to leverage Vault as a central secret management service while offloading the operational burden to the Site Reliability Engineering (SRE) experts at HashiCorp.
In this tutorial, you will deploy a Vault Enterprise cluster guided by the HCP portal.

Prerequisites
You will need an HCP account.

Note
Previous experience with Vault and Vault Enterprise are not required to deploy a Vault server in HCP.

Create a Vault cluster

Note

This tutorial assumes you have not previously created HashiCorp Virtual Network (HVN) in your HashiCorp Cloud Platform account.

Launch the HCP Portal and login. HashiCorp Cloud Platform (HCP) provides your account with an organization. Your account may invite others to join your organization or you may be invited to join other organizations.

Choose your organization. 
￼
From the Overview page, click Get started with Vault.
￼
From the Vault overview page you have the option to deploy HCP Vault using a Quick Deploy Template which deploys Vault with a sample configuration or you can choose to Start from scratch which deploys a standard Vault instance with no existing configuration. For the purposes of these tutorials and learning about Vault, click the Create cluster button under Start from scratch.
￼
Select your preferred cloud provider.
￼
Click the Vault tier pull down menu and select Development.  Tip The development tier should not be used for production workloads.  

Click the Cluster size pull down menu and select Extra Small. For the development tier, Extra Small is the only available cluster size.

Under the Network section, accept or edit the Network ID, Region selection, and CIDR block for the HVN.  Note You can learn how to connect to a private HCP Vault cluster on AWS in the Connect an Amazon Transit Gateway to your HashiCorp Virtual Network or Peering an AWS VPC with HashiCorp Cloud Platform (HCP) tutorials, or the Peering an Azure Virtual Network with HashiCorp Cloud Platform (HCP) tutorial for Azure.  

Leave Cluster accessibility set to Public.  Security consideration All new development tier HCP Vault clusters are configured with public access enabled by default. This means clients can connect from anywhere. For production tiers (starter, standard, and plus) private access will be enabled by default. This means you can only connect from a transit gateway or peered VPC (AWS) or VNet (Azure)  

Under the Basics section, accept or edit the default Cluster ID (vault-cluster).
￼ 
Under Templates, select Start from scratch. Templates provide sample configurations for various use cases.

Click Create cluster.

Wait for the cluster to initialize before proceeding.
￼
Vault cluster overview

The Vault page displays the created Vault cluster. Within that view, the Overview page displays information to help you learn about HCP Vault, Vault configuration, Vault usage, and cluster details. The Access Vault pane contains details that enable you to administer the Vault cluster through the Web UI or command-line interface (CLI).
￼

Note

The cluster is created with a top-level Namespace called admin. Namespaces enable you to create isolated Vault environments.

1. Review the Cluster Details pane.
￼
2. Cluster details provide helpful information about your HCP Vault cluster.

3. Review the Quick actions pane.
￼
4. The Quick actions pane provides details for accessing your new HCP Vault cluster. You can use the Cluster URLs links to Copy the public or private addresses, and use the Generate token link to generate a new admin token to perform the initial configuration of the HCP Vault cluster.


Access a Vault Cluster on HCP

Now that you have created a new HCP Vault instance, you will need to perform some initial configuration to support your use case such as enabling secrets engines to store or generate secrets, or adding additional auth methods to allow users or applications to authenticate with HCP Vault.

HCP Vault provides the same type of access as a traditional Vault cluster. You can access it through a command line interface (CLI) using the Vault binary, through the Vault API using common programming languages or tools such as cURL, or by using the Vault User Interface (UI).

Access the Vault cluster

Security consideration

When an HCP Vault cluster has public access enabled, you can connect from any internet connected device. When the HCP Vault cluster has private access enabled you will need to access the cluster from a connected cloud provider such as AWS with a VPC peering connection, a AWS transit gateway connection, or Azure with a Azure Virtual Network peering connection. For the purposes of this tutorial, your cluster should have public access enabled.

Web UI

CLI command

API call using cURL

From Overview page, click Generate token in the New admin token card.
￼
Click Copy to copy the new token to your clipboard
￼
Under Quick actions in the Access web UI card, click the Public Cluster URL. A new tab/window will open.
￼
Enter the token in the Token field.
￼
Click Sign In.
￼
Notice that your current namespace is admin/.


Multi-tenancy with Namespaces

When Vault is primarily used as a central location to manage secrets, multiple organizations within a company may need to be able to manage their secrets in a self-serving manner. This means that a company needs to implement a Vault as a Service model allowing each organization (tenant) to manage their own secrets and policies. Most importantly, tenants should be restricted to work only within their tenant scope.
￼
To achieve this, HashiCorp Cloud Platform (HCP) Vault utilizes the concept of a namespace. A namespace allows you to create separate groups of secrets, and apply policies to those namespaces to ensure each tenant can only access the secrets they have permission to. When you create a new HCP Vault cluster, a Vault Enterprise cluster with a default namespace of admin is provisioned.
In this tutorial, you will explore the creation of namespaces and learn how to navigate between them.

Note
This step assumes that you created and connected to the HCP Vault cluster in the Create a Vault Cluster on HashiCorp Cloud Platform (HCP) step.

Characteristics of Vault namespaces
A Vault namespace enables teams, organizations, or applications a dedicated, isolated environment. Each namespace has its own:

Policies

Auth methods

Secrets engines

Tokens

Identity entities and groups

Tokens are locked to a namespace or child-namespaces. Identity groups can pull in entities and groups from other namespaces.

Create namespaces

You may define nested namespaces within a parent namespace. These child-namespaces enable further isolated environments under the parent namespace.

In the Vault UI, select Access from the menu.

Select Namespaces and then click the Create namespace action.
￼ 
Enter education in the Path field.
￼
Click Save. The education namespace is created as a child-namespace of the admin namespace. This relationship is represented as the path admin/education/.

Click the admin namespace from the menu. 
￼
 The namespace selector displays the child-namespaces of the current namespace.

Select the education namespace. The current namespace changes to the admin/education/.

Navigate to Access > Namespaces and click the Create namespace action.

Enter training in the Path field.
￼ 
Click Save. The training namespace is created as a child-namespace of the admin/education/ namespace. This relationship is represented as the path admin/education/training/.

Use the namespace selector to navigate to the training namespace and then to the admin namespace.
 

Your First Secret

One of the core features of Vault is the ability to read and write arbitrary secrets securely. Secrets written to Vault are encrypted and then written to the backend storage. Therefore, the backend storage mechanism never sees the unencrypted value and doesn't have the means necessary to decrypt it without Vault.

Note

This step assumes that you created and connected to the HCP Vault cluster in the Create a Vault Cluster on HashiCorp Cloud Platform (HCP) step.

Key/Value secrets engine

Key/Value v2 secrets engine is a generic key-value store used to store arbitrary secrets within the configured physical storage for Vault.

Key/Value secrets engine has version 1 and 2. The difference is that v2 provides versioning of secrets and v1 does not.

Use the vault kv <subcommand> [options] [args] command to interact with K/V secrets engine.

Available subcommands:

Subcommand	kv v1	kv v2	Description

delete	x	x	Delete versions of secrets stored in K/V

destroy		x	Permanently remove one or more versions of secrets

enable-versioning		x	Turns on versioning for an existing K/V v1 store

get	x	x	Retrieve data

list	x	x	List data or secrets

metadata		x	Interact with Vault's Key-Value storage

patch		x	Update secrets without overwriting existing secrets

put	x	x	Sets or update secrets (this replaces existing secrets)

rollback		x	Rolls back to a previous version of secrets

undelete		x	Restore the deleted version of secrets



Enable secrets engine

First, enable key/value v2 secrets engine at secret/ path in the admin namespace. Secrets engines are tied to their namespace. Therefore, the secrets you create in the admin namespace are not accessible from other namespaces.

In the Vault UI, set the current namespace to admin/.
￼
Click the Secrets tab.

Click Enable new engine.

Select KV from the list, and then click Next.
￼
Enter secret in the Path field.

Click Enable Engine to complete.

Now that you have a secret engine enabled, you will create a new secret.

Create secrets

Now that you have enabled a secrets engine, in this scenario the key/value v2 secrets engine, you can store and retrieve secrets from HCP Vault.

Click Create secret. Enter test/webapp in the Path for this secret field.

Under the Secret data section, enter api-key in the key field, and ABC0DEFG9876 in the value field. You can click on the sensitive information toggle to show or hide the entered secret values.
￼ 
Click Save.

Click the masked input toggle button to review the value for the api-key key.
￼ 


Create Vault Policies

Policies are a declarative way to grant or forbid access to certain paths and operations in Vault. In this tutorial, you will create a policy and then edit it to support new requirements.

Note

This step assumes that you created and connected to the HCP Vault cluster in the Create a Vault Cluster on HashiCorp Cloud Platform (HCP) step.

Create a policy

ACL policies are authored in HashiCorp Configuration Language (HCL). Here is an example policy:

Grant 'create', 'read' and 'update' permission to paths prefixed by 'secret/data/test/'

path "secret/data/test/*" {
  capabilities = [ "create", "read", "update" ]
}

Manage namespaces

path "sys/namespaces/*" {
   capabilities = [ "create", "read", "update", "delete", "list" ]
}

The policy format uses a prefix matching system on the API path to determine access control. The most specific defined policy is used, either an exact match or the longest-prefix glob match. Since everything in Vault must be accessed via the API, this gives strict control over every aspect of Vault, including enabling secrets engines, enabling auth methods, authenticating, as well as secret access.

IMPORTANT

Policies are tied to their namespace. When you create a policy in the admin/ namespace, the policy is only available in the admin/ namespace. This is to keep each namespace isolated and secure.

Warning

There are two out-of-the-box policies in the admin/ namespace: default and hcp-root. Do NOT edit the hcp-root policy. The admin token generated by the HCP portal has the hcp-root policy attached granting permissions necessary for initial setup. Modifying this policy could deny you from performing the admin tasks you desire.

In the Vault UI, set the current namespace to admin/.
￼
Click the Policies tab.

Select Create ACL policy.

Enter tester in the Name field.

Enter the following policy in the Policy textbox. # Grant 'create', 'read' and 'update' permission to paths prefixed by 'secret/data/test/'

 path "secret/data/test/*" {
    capabilities = [ "create", "read", "update" ]
.  }
 
 Manage namespaces

  path "sys/namespaces/*" {
     capabilities = [ "create", "read", "update", "delete", "list" ]
  }
    Copy   
 Click Create policy at the bottom of the page.
￼
 The policy is created and this view displays its name and contents. You can also refer to the Create Vault Policies tutorial. 

Policies to access another namespace

The policy path is relative to the namespace on which the policy is deployed. If you want to access the database/ path in the admin/education/training namespace from the admin namespace, the policy path must be education/training/database/*.
￼
The policy you deploy on the admin namespace must look similar to the following:
Grant CRUD operations against the path prefixed with 'database/' in the 'training' namespace

path "education/training/database/*" {
   capabilities = [ "create", "read", "update", "delete" ]
}

The equivalent policy you deploy onto the admin/education namespace must look as follows:

Grant CRUD operations against the path prefixed with 'database/' in the 'training' namespace

path "training/database/*" {
   capabilities = [ "create", "read", "update", "delete" ]
}

Manage Authentication Methods
* 		10min|HCP HCP Vault  Vault

Before a client can interact with Vault, it must authenticate against an auth method to acquire a token. This token has policies attached so that the behavior of the client can be governed.

In this tutorial, you will enable and configure AppRole auth method.

Note

As with secrets engines and policies, auth methods are tied to a namespace. The auth method enabled on the admin namespace is only available to the admin namespace and generates a token available to use against the admin namespace.

Personas

The steps described in this tutorial involve two personas:

admin - Vault admin with privileged permissions to configure an auth method

app - Vault client that consumes secrets stored in Vault

Note

This step assumes that you created and connected to the HCP Vault cluster in the Create a Vault Cluster on HashiCorp Cloud Platform (HCP) step, and completed the Create Vault Policies tutorial so the tester policy exists.

Enable AppRole auth method

(Persona: admin)

The AppRole auth method must be enabled before it can be used.

In the Vault UI, make sure that current namespace is admin/.
￼
Click the Access tab.

Click Enable new method.
￼
Select AppRole and click Next.

Leave the path value unchanged and click Enable Method.

Without making any change, click < approle to view its current configuration.
￼ 

Create a role with policy attached

(Persona: admin)

When you enabled the AppRole auth method, it was mounted at the default /auth/approle path. In this example, you are going to create a role for the app persona with tester policy attached.

Create the webapp role with the generated token's time-to-live (TTL) set to 1 hour and can be renewed for up to 4 hours from the time of its creation.

Click the Vault CLI shell icon (>_) to open a command shell in the browser.

Copy the command below. vault write auth/approle/role/webapp token_policies="tester" token_ttl=1h token_max_ttl=4h

Paste the command into the command shell in the browser and press the enter button.
 

Generate RoleID and SecretID
(Persona: admin)

The RoleID and SecretID are like a username and password that a machine or app uses to authenticate.

To retrieve the RoleID, invoke the auth/approle/role/<ROLE_NAME>/role-id endpoint. To generate a new SecretID, invoke the auth/approle/role/<ROLE_NAME>/secret-id endpoint.

Click the Vault CLI shell icon (>_) to open a command shell.

Read the RoleID. $ vault read auth/approle/role/webapp/role-id

   Copy    Example output: Key     Value

 role_id b6ccdcca-183b-ce9c-6b98-b556b9a0edb9

Generate a new SecretID of the webapp role. $ vault write -force auth/approle/role/webapp/secret-id
7.    Copy    Key                Value
8.  secret_id          735a47cc-7a98-77cc-0128-12b1e96a4157
9.  secret_id_accessor 3ab305d1-1eab-df4b-4079-ef7135635c49
10.  ...snip...
11.      The -force (or -f) flag forces the write operation to continue without any data values specified. Or you can set parameters such as cidr_list. 
￼
12.  The acquired role-id and secret-id are the credentials that your trusted application uses to authenticate with Vault.

Tip
The RoleID is similar to a username; therefore, you will get the same value for a given role. In this case, the webapp role has a fixed RoleID. While SecretID is similar to a password that Vault will generate a new value every time you request it.

Test and validate
(Persona: app)
The client (in this case, webapp) uses the RoleID and SecretID passed by the admin to authenticate with Vault. If webapp did not receive the RoleID and/or SecretID, the admin needs to investigate.

Tip
Refer to the Advanced Features section for further discussion on distributing the RoleID and SecretID to the client app securely.
CLI command
API call using cURL
1. To login, use the auth/approle/login endpoint by passing the RoleID and SecretID.  Note Replace the role_id and secret_id values in the example with those generated in the Generate RoleID and SecretID section above.   Example: $ vault write auth/approle/login \
2.      role_id="675a50e7-cfe0-be76-e35f-49ec009731ea" \
3.      secret_id="ed0a642f-2acf-c2da-232f-1b21300d5f29"
4.      Example output: Key                     Value
5.  ---                     -----
6.  token                   hvs.BMXSIJvlm6OjYeWiBmkLxnhgkPAkr3Lx8CbvU1WRnCGTwufIGicKImh2cyDYN0hhaWJIcE5yQUlRWGMxYzZFc05DcDUuWFA1T2oQjQI
7.  token_accessor          FILPoDWPoqd5zeo62HAoWexN.0YFbA
8.  token_duration          1h
9.  token_renewable         true
10.  token_policies          ["default" "tester"]
11.  identity_policies       []
12.  policies                ["default" "tester"]
13.  token_meta_role_name    webapp
14.      Vault returns a client token with default and tester policies attached.
15. Store the generated token value in an environment variable named, APP_TOKEN.  Note Replace the APP_TOKEN value in the example with the one generated above.   Example: $ export APP_TOKEN="hvs.BMXSIJvlm6OjYeWiBmkLxnhgkPAkr3Lx8CbvU1WRnCGTwufIGicKImh2cyDYN0hhaWJIcE5yQUlRWGMxYzZFc05DcDUuWFA1T2oQjQI"
16.     
17. Access the secrets at secret/test/webapp authenticated with the APP_TOKEN. $ VAULT_TOKEN=$APP_TOKEN vault kv get secret/test/webapp
18. 
19.  ====== Metadata ======
20.  Key              Value
21.  ---              -----
22.  created_time     2021-06-17T03:06:34.063027186Z
23.  deletion_time    n/a
24.  destroyed        false
25.  version          1
26. 
27.  ===== Data =====
28.  Key        Value
29.  ---        -----
30.  api-key    ABC0DEFG9876
31.    Copy   
To learn more about the Key/Value v2 secrets engine, read the Versioned Key/Value Secrets Engine tutorial.

Next steps
To learn more about the AppRole auth method, refer to the AppRole Pull Authentication and AppRole with Terraform & Chef tutorials.
If you followed the tutorials all the way through, you completed the common Vault operations workflow.
￼
The differences between the HCP Vault and self-managed Vault are:
* HCP Vault runs Vault Enterprise
* The root namespace of HCP Vault is admin
You can follow any of the Vault tutorials on Learn. Your VAULT_ADDR and VAULT_TOKEN values are different from what the tutorials may say, but you know how to set them for your HCP Vault cluster. In addition, be sure to set VAULT_NAMESPACE.
Next, continue onto the Vault Operations Tasks tutorial which demonstrates the cluster-level operations.

Clean up
If you wish to delete the resources you created to clean up your Vault environment, go through the steps in this section.
1. Disable the AppRole auth method enabled at approle. $ vault auth disable approle
2.  Success! Disabled the auth method (if it existed) at: approle/
3.    Copy   
4. Delete the tester policy. $ vault policy delete tester
5.  Success! Deleted policy: tester
6.    Copy   
7. Disable the key/value v2 secrets engine at secret/. $ vault secrets disable secret
8.  Success! Disabled the secrets engine (if it existed) at: secret/
9.    Copy   
10. Delete the education/training namespace. $ vault namespace delete -namespace=admin/education training
11. 
12.  WARNING! The following warnings were returned from Vault:
13. 
14.    * Namespace deletion has been queued. Progress will be reported in
15.    debug-level logs in Vault's server log.
16.    Copy   
17. Delete the education namespace. $ vault namespace delete education
18. 
19.  WARNING! The following warnings were returned from Vault:
20. 
21.    * Namespace deletion has been queued. Progress will be reported in
22.    debug-level logs in Vault's server log.
23.    Copy   
24. Delete the stored token. $ unset VAULT_TOKEN
25. 
HCP Vault operation tasks
* 		9min|HCP HCP Vault  Vault

HashiCorp Cloud Platform (HCP) Vault provides access to critical operational tasks, such as locking the cluster, accessing audit logs, and managing data snapshots.
In this tutorial, you will perform these operational tasks.

Note
This tutorial assumes that you created and connected to the HCP Vault cluster in the Create a Vault Cluster on HashiCorp Cloud Platform (HCP) tutorial.

Lock and unlock the Vault cluster
Intrusion detection or data breaches may require you to lock your HCP Vault cluster. API lock functions similarly to Vault sealing by preventing normal Vault operations but still allowing the HCP platform access to perform upgrades and snapshots.

Warning
Locking a cluster prevents customer access to the cluster until it is unlocked.

Lock the cluster
1. Under Quick actions, click API Lock.
￼
2.  A Lock API? pop-up dialog displays a warning and explanation of the locking operation.
3. Enter LOCK into the Confirm lock field.
￼
4. 
5. Click Lock to proceed. When it completes, the cluster state changes to Locked.
￼
6. 

Unlock the cluster
1. In the Vault cluster is locked notification, click Unlock. A pop-up dialog displays a warning and explanation of the unseal operation. 
￼
2. 
3. Enter UNLOCK into the Confirm unlock field.
4. Click Unlock.
The Vault cluster unlocks. The Vault Overview page displays the Vault configuration and available operations.

Scale an HCP Vault cluster up or down

Note
Scaling your HCP Vault cluster to a higher tier will increase the hourly charges for your HCP account. Please review carefully before committing any changes to your HCP Vault cluster.
HCP Vault cluster scaling allows you to scale your cluster up or down to meet organizational needs. You can scale between both cluster tiers (e.g. dev to starter, starter to standard) and cluster sizes (e.g standard small to standard medium).

Note
HCP Vault clusters can be scaled up from the development tier to a larger tier, however starter, standard, or plus tier clusters cannot be scaled down to the development tier.
Cluster scaling is fully managed by the HashiCorp Cloud Platform and performed with no downtime, meaning you can continue to utilize HCP Vault while the cluster is being scaled up or down. Cluster scaling is available from the HCP Portal and Terraform when using version 0.21.1 or higher of the HCP Terraform provider.
Follow these steps in the HCP Portal to scale your cluster up from the dev tier cluster created in the Create a Vault Cluster on HCP tutorial.
1. Navigate to the Overview page for your HCP Vault cluster.
2. Click Manage and then select Edit configuration.
3. Scroll down to view the Cluster Tier section.
￼
4. 
5. Click the radio button for the Standard tier. In the Cluster Size section you will see multiple supported sizes. You can scale the HCP Vault cluster up and down between the available sizes within a tier, or scale between different tiers. You can scale up from the Development tier to another tier but you cannot scale back down to the Development tier.
6. Click the radio button for the Starter tier. In the Cluster Size section Small is the only size available in this tier.
7. Click Next.
8. The Review changes screen provides an overview of the requested changes and the pricing differences between the two tiers.
￼
9. 
10. Click Apply changes. You will be returned to the Overview screen.
11. The cluster will begin updating. This process will take several minutes.  Note If the cluster status section displays the status as Running, refresh the browser window/tab.  
Wait for the cluster to complete the scale up process and then move on to the next section.

Data snapshots
Preserving Vault data is critical to production operations and particularly for disaster or sabotage recovery purposes. HCP Vault offers snapshot functionality for the underlying storage to preserve data based on your requirements.

Note
Snapshots are not available for development tier clusters.

Create snapshot
After completing the Scale an HCP Vault cluster up or down tutorial you can follow these steps to manually snapshot your Vault data as needed.
1. Click Snapshots in the left navigation pane.
￼
2.  The view displays a history of the snapshots created.
3. Click Create snapshot.
￼
4.  A Create snapshot pop-up dialog displays.
5. Enter tutorial in the Snapshot name field and click Create snapshot. The view displays the snapshot history. The latest snapshot is appended to the snapshot list. While the snapshot is in progress it will display a Pending animation in the Status column.
￼
6.   Note The duration of time needed for the snapshot to complete can vary and largely depends on the size the of data stored in your Vault cluster.   When the snapshot operation completes the Status changes to Stored.  Note HCP persists the snapshots for up to 30 days after creation, checks every 24 hours, and prunes expired snapshots.  

Restore snapshot
You can use the snapshots to restore data if it ever becomes necessary.
1. Click the Snapshots link in the left navigation pane.
2. Click the ellipsis (...) menu next to the tutorial snapshot entry, and choose Restore.
￼
3. 
4. A confirmation dialog appears; enter RESTORE and click Restore snapshot to confirm restoration. A message will appear informing you the restore process has started.
￼
5. 

Delete snapshot
1. Click the Snapshots link in the left navigation pane.
2. Click the ellipsis (...) menu next to the tutorial snapshot, and choose Delete.
￼
3. 
4. A confirmation dialog appears; enter DELETE and click Delete snapshot to confirm snapshot deletion.
5. A Snapshot deleting dialog appears. Once the snapshot is deleted, it no longer appears in the snapshot list.

Access the audit log for troubleshooting

Note
Audit logging is not available on Development tier clusters.
Effective troubleshooting of requests and responses to Vault requires access to the audit device logs.
HCP Vault enables a File Audit Device by default. This device provides the last hour of Vault requests in a downloadable archive. These logs may be imported into your preferred tooling for auditing and troubleshooting.
1. From the Vault cluster overview page, click Audit Logs.
2. From the Audit logs page, click Select logs in the Download audit logs box. 
￼
3.  A Download audit logs pop-up dialog displays.
4. Use the Start date and Start time components to specify the audit log starting position. The log file will cover a 1 hour period after the date and time that you select.
￼
5. 
6. Once you have selected the desired Start date and Start time, click Generate logs. When the archive is created, a new Download audit logs pop-up dialog displays. The archive is presented with the specific time-frame covered by the log file.  Note The file is only available to download for 10 minutes; after this time elapses, you must begin the download process from the first step.  
7. Click the download icon.
￼
8.  The downloaded file is a gzip compressed file. The filename contains the start and end timestamps as part of its filename (e.g. auditlogs-vault-cluster-202102021400-202102021500.gz). Refer to the HCP Vault Monitoring tutorial collection to learn how to stream audit logs to Datadog, Grafana Cloud, or Splunk. 

Manage major version upgrades
There are scenarios where major version upgrades of the Vault cluster can potentially affect the behavior of Vault clients. For example, the returned JSON output may contain a new field. These changes may require additional testing or operational updates to leverage the enhanced behaviors.
Customers running HCP Vault on either the Standard or Plus tiers can manage when the Vault cluster will be upgraded.

Note
Major version upgrade settings are available on either the Standard or Plus tier. If you would like to follow this tutorial, upgrade your Vault cluster to the Standard or Plus tier.
1. Log into the HCP Portal and navigate to the Vault Overview page.
￼
2. 
3. Click the cluster ID link for a HCP Vault cluster that is on the Standard or Plus tier.
￼
4. 
5. From the Vault cluster Overview page, click Settings.
￼
6. 
7. Click Edit settings.
8. You can choose between three options to control when your cluster will be upgrade. Automatic will upgrade the cluster as new versions of Vault are validated for HCP. Scheduled allows you select a day and time window in which the upgrade will be performed. Manual allows you to initiate the upgrade on any day or time of your choosing, but will be automatically upgraded after 30 days.
9. Select Manual and click Apply changes.  Note The remainder of these steps are for demonstration purposes only. You can follow these steps after a new version of Vault becomes available.  
10. Click Overview.
11. When a cluster is set to manual, and a new upgrade is detected, you will receive a notification that an upgrade is available with a Upgrade now button.
￼
12.  HCP Portal users will also receive an email notification that the upgrade is available.
￼
13. 
14. Click Upgrade now. A dialog will appear with a link to the changelog and upgrade guide so you can review any changes that may impact your usage of HCP Vault.
￼
15. 
16. Click Upgrade now to begin the automated upgrade process.
￼
17. 
18. When the upgrade process completes, a new notification will appear with a link to the release notes.
￼
19.  HCP Portal users will also receive an email notification that the upgrade is complete.


