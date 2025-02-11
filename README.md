# Vault Production Readiness Checklist

This checklist will prepare you launch production-ready vault clusters into any major Cloud provider or on Premise

1. [Infrastructure Architecture](#Infrastructure-Architecture)
1. [Load Balancing](#Load-Balancing)
1. [Monitoring and Alerting](#Monitoring-and-Alerting)
1. [Configuration Management](#Configuration-Management)
1. [Vault Configuration](#Vault-Configuration)
1. [Identity & Access Management](#Identity-&-Access-Management)
1. [Security Hardening](#Security-Hardening)
1. [Operational Readiness](#Operational-Readiness)
1. [Observability](#Disaster-Recovery)
1. [Governance and compliance](#Governance-and-compliance)
1. [Disaster Recovery](#disaster-recovery)
1. [Automation Playbooks](#automation-playbooks)
1. [SLI, SLO and SLA](#sli-slo-sla)
1. [Keys Rotation](#keys-rotation)

### References

1. [Adopting Hashicorp Vault](https://www.hashicorp.com/resources/adopting-hashicorp-vault)
1. [Vault on Kubernetes Reference Architecture](https://developer.hashicorp.com/vault/tutorials/kubernetes/kubernetes-reference-architecture)

### **Infrastructure Architecture**

|  |  |
| --------- | ------- |
| &#9744;   | <details><summary>Infrastructure Workloads spread accross Availability Zones</summary> <p> Nodes (Container) in Vault clusters should be spread accross two or more failure domains known as Availability zones. The loss of a single Availability zone should not result result in a loss of service. </p> </details> |
| &#9744;   | <details><summary>Helm Charts/Operator Created </summary> <p> If you are deploying your vault on Kubernetes, it is reccomended to build helm charts or operator to create vault cluster nodes in an immutable way.</p> </details> |
| &#9744;   | <details><summary>Replication Enabled (Enterprise only)</summary> <p> If you are using the Enterprise version of Vault, you can enable replication between two or more Vault clusters in different geographical regions for added protection is Disaster Recovery scenarios.  Replication can be configured in Disaster Recovery Mode or Performance Replication mode.  If you are planing on using Replication, you need to provision infrastructure in an alternative region, with nodes spread accross multiple Availability Zones. For more information about the Enterprise Replication feature, see [the official documentation.](https://www.vaultproject.io/docs/internals/replication/) </p> </details> |
| &#9744;   | <details><summary>Firewall rules configured to control access to Vault</summary> <p> Vault will likely contain business critical secrets which makes it a prime target for malicious actors. Access to vault to should be restricted to your private networks and not be accessible on the internet.  The Use of Virtual Private Networks is a commonly used approach to allow access to Vault from unknown networks</p> </details> |
| &#9744;   | <details><summary>Compute Resouces satisfy the minimum requirements</summary> <p> Ensure Hardware servers, Virtual Machines and Containers have been appropriately resources in accordance with the [Deployment System Requirements](https://learn.hashicorp.com/vault/operations/ops-reference-architecture#deployment-system-requirements) </p> </details> |
| &#9744;   | <details><summary>Secondary Disk</summary> <p>Ensure that vault servers have a secondary disk attached to them. This will help with Audit Device Fault tolerance</p> </details> |

### **Load Balancing**

|  |  |
| --------- | ------- |
| &#9744;   | <details><summary>TLS Encryption is configured on Load Balancer</summary> <p> </summary> <p> Vault’s communications should be encrypted end-to-end with TLS and this should not be terminated at the Load balancer layer. The load balancer should also use the same encryption to communicate with Vault</p> </details> |
| &#9744;   | <details><summary>HTTP Redirects configured</summary> <p> </summary> <p> With TLS configured, all traffic going via HTTPS will be encrypted; however, we need to ensure that there are no connections to vault via HTTP. The Load balancer should be configured to redirect all HTTP traffic to HTTPS.</p> </details> |
| &#9744;   | <details><summary>Health checks enabled and configured</summary> <p> </summary> <p>Load balancer health probes can be used to ensure that traffic is only routed to a healthy leader node. Configure routing rules according to [these response codes](https://www.vaultproject.io/api/system/health.html) </p> </details> |


### **Monitoring and Alerting**

|  |  |
| --------- | ------- |
| &#9744;   | <details><summary>Vault Telemetry Enabled.</summary> <p> </summary> <p>Vault telemetry should be configured in the telemetry stanza within the Vault config file. This will enable monitoring and alerting with a wide range of open source tools (Telegraf and prometheus)</p> </details> |
| &#9744;   | <details><summary>Vault platform monitoring configured</summary> <p> </summary> <p>Monitoring system of your choice is configured to monitor and alert on vault application metric thresholds as per the [best practice guidance of Hashicorp.](https://s3-us-west-2.amazonaws.com/hashicorp-education/whitepapers/Vault/Vault-Consul-Monitoring-Guide.pdf)</p> </details> |
| &#9744;   | <details><summary>Infrastructure Monitoring Configured</summary> <p> </summary> <p>Monitoring system of your choice is configured to monitor and alert on consul application metric thresholds as per the [best practice guidance of Hashicorp.](https://s3-us-west-2.amazonaws.com/hashicorp-education/whitepapers/Vault/Vault-Consul-Monitoring-Guide.pdf)</p> </details> |
| &#9744;   | <details><summary>Monitoring Dashbaord Created</summary> <p> </summary> <p>Using a Dashboard tool a of your choice, create a monitoring dashboard for operations staff to easily identify any issues that may be occurring.</p> </details> |
| &#9744;   | <details><summary>Prometheus Alerts Configured</summary> <p> </summary> <p>Configure and package prometheus alerts for operations team</p> </details> |


### **Configuration Management**

|  |  |
| --------- | ------- |
| &#9744;   | <details><summary>Infrastructure as code written (Containers only)</summary> <p> </summary> <p> Code written to deploy the infrastructure Vault.</p> </details> |
| &#9744;   | <details><summary>Vault Platform Configuration Code Written</summary> <p> </summary> <p> Vault Platform configuration should be described in code using a tool like [Terrafrom](https://www.terraform.io/).  Configuration such as Auth Methods, Secrets Engines, Audit Devices and Policies should all be configured using code</p> </details> |
| &#9744;   | <details><summary>Code under Version Control in Source Code Repositories</summary> <p> </summary> <p>All Infrastructure code and application code should be stored separate source control repositories and be placed under version control. An appropriate branching strategy should be implemented and documented in the README file.</p> </details> |
| &#9744;   | <details><summary>Code Owners in repositories</summary> <p> </summary> <p>Repository files should have code owners assigned to them to control who can approve Pull Requests that will be merged into the Master branch.</p> </details> |
| &#9744;   | <details><summary>Repository rules implemented</summary> <p> </summary> <p>Configure the minimum number of Pull Request approvers, restrictions on Pull Request Authors approving their own requests and any other rules that your organisation’s security standards require for Integrity.</p> </details> |
| &#9744;   | <details><summary>Deployment pipelines implemented</summary> <p> </summary> <p>Code deployments should be automated using deployment pipelines. Where possible, the pipeline should be written as code and stored under version control with the code</p> </details> |
| &#9744;   | <details><summary>Dev/Staging environments created</summary> <p> </summary> <p>Create development and staging environments for Vault.  Staging Environment should be identical to production, with the only divergence being, when pre-production changes are implemented for final testing prior to being deployed to production.</p> </details> |
| &#9744;   | <details><summary>Naming and coding standards established</summary> <p> </summary> <p>Implement and document naming and coding standards. Naming standards for Namespaces, Policies, Vault Roles, secrets keys and AppRoles.  Coding standards where applicable for variable names and function names.</p> </details> |
| &#9744;   | <details><summary>Integration tests written</summary> <p> </summary> <p>A suite of automated integration tests written to be run either during the deployment pipeline or as a pre-check on your chosen VCS required to pass before a Pull Request can be merged.</p> </details> |


### **Vault Configuration**

|  |  |
| --------- | ------- |
| &#9744;   | <details><summary>Enable audit device on 2 files on different disks</summary> <p> </summary> <p>Vault logs all requests and responses to requests. If Vault is unable to log requests and responses to these requests, it will immediately seize operations. To provide redundancy, each vault node should have 2 file audit devices enabled on separate volumes on separate disks.</p> </details> |
| &#9744;   | <details><summary>Log rotation configured on log files</summary> <p> </summary> <p>Enable and configure log rotation on the audit files to ensure the disks do not fill up and cause a vault outage.</p> </details> |
| &#9744;   | <details><summary>Configure at least one IDP as an auth method</summary> <p> </summary> <p>Where appropriate, configure an existing identity provider (or multiple if required) as an authentication method in Vault</p> </details> |
| &#9744;   | <details><summary>Configure the required secrets engines</summary> <p> </summary> <p>Identify and enable the required secrets engines for your business and technical use cases</p> </details> |
| &#9744;   | <details><summary>Ensure KV v2 is enabled</summary> <p> </summary> <p>Ensure that Version 2 of the KV secrets engine is used to enable secrets versioning</p> </details> |


### **Identity & Access Management**

|  |  |
| --------- | ------- |
| &#9744;   | <details><summary>Create default policies</summary> <p> </summary> <p>Create default policies that all user entities will inherit according to your business security model.  This could be list permissions on a particular KV path for example.</p> </details> |
| &#9744;   | <details><summary>Create policy mappings for default policies</summary> <p> </summary> <p>Create a mapping for default policies to ensure all user entities inherit these policies.</p> </details> |
| &#9744;   | <details><summary>Configure aliases for entities when more than one auth method is in use</summary> <p> </summary> <p>Using the Identity Secrets engine, create aliases to attach vault logins via different auth methods to a single entity to ensure the correct policies are inherited and to make the logging data easier to mine</p> </details> |
| &#9744;   | <details><summary>Design a path structure for KV v2 that matches the way your org works (team based or product/service based)</summary> <p> </summary> <p>Map you KV path design to the way your organisation works or product groupings.</p> </details> |
| &#9744;   | <details><summary>Meta AppRole process defined</summary> <p> </summary> <p>Meta Approles are a mechanism that allow an application or service to read the secret id of an app role without exposing this to application developers.</p> </details> |
| &#9744;   | <details><summary>SSO for end customer</summary> <p> </summary> <p>Configure SSO for user to authenticate via the company IDP.</p> </details> |
| &#9744;   | <details><summary>SSO for management team</summary> <p> </summary> <p>Configure SSO for management team via company IDP.</p> </details> |

### **Security Hardening**

|  |  |
| --------- | ------- |
| &#9744;   | <details><summary>Ensure TLS is configured on Vault Cluster</summary> <p> </summary> <p>Enable end-to-end encryption using TLS certificates.  Vault agents should also use TLS certificates</p> </details> |
| &#9744;   | <details><summary>Enable and configure SELinux / App Armour</summary> <p> </summary> <p>Enable and config SELinux / app amour depending on your operating system to create sandboxed contexts to  reduce blast radius if even the system is compromised.</p> </details> |
| &#9744;   | <details><summary>Randomise the ports used to differ from standard ports for Vault</summary> <p> </summary> <p>By default, Vault uses port 8200 and 8201. Change the port to a non-standard port to provide extra hardening</p> </details> |
| &#9744;   | <details><summary>Revoke root token</summary> <p> </summary> <p>Once initial set-up of Vault cluster has been completed, the root token should be revoked.</p> </details> |
| &#9744;   | <details><summary>Configure server firewalls to only allow access to required ports.</summary> <p> </summary> <p>Using firewalld or IP Tables, configure these firewalls to limit port access to the vault servers.</p> </details> |
| &#9744;   | <details><summary>Disable SSH</summary> <p> </summary> <p>Interaction with Vault is done via the API, even when using the CLI.  As such, there is no reason to have to SSH on to a vault server (if it’s a virtual machine) so SSH should be disabled to mitigate the risk of unauthorised access to the server.</p> </details> |


### **Operational Readiness**

|  |  |
| --------- | ------- |
| &#9744;   | <details><summary>Configure auto-unseal</summary> <p> </summary> <p>Add a seal stanza to the Vault config file to reduce operational burden on operators. For more information check the [auto-unseal documentation here](https://www.vaultproject.io/docs/concepts/seal/#auto-unseal)</p> </details> |
| &#9744;   | <details><summary>PGP encryption of unseal/recovery keys</summary> <p> </summary> <p>Use PGP or Keybase to add an extra layer of security to the distribution of unseal/recovery keys. For more details, see the [official documentation here](https://www.vaultproject.io/docs/concepts/pgp-gpg-keybase/)</p> </details> |
| &#9744;   | <details><summary>Node rebuild practice run</summary> <p> </summary> <p>Practice building and replacing a node in the vault clusters with zero downtime.</p> </details> |
| &#9744;   | <details><summary>Vault upgrade practice run</summary> <p> </summary> <p>Practice upgrading Vault binaries to newer versions with zero downtime.</p> </details> |
| &#9744;   | <details><summary>Load testing</summary> <p> </summary> <p>Consuct load testing to ensure your infrastructure compute resources are sufficient for the load you are expecting. There are projects like [wrk](https://github.com/wg/wrk) That can assist with generating traffic.</p> </details> |
| &#9744;   | <details><summary>Document key holders and contact details</summary> <p> </summary> <p>Ensure unseal/recovery key holders are documented on a Wiki and this document is kept up-to-date</p> </details> |
| &#9744;   | <details><summary>Trusted Broker/Platform pattern</summary> <p> </summary> <p>Choose a platform or broker that your business trusts and use this for secure injection of initial secrets. Examples are using Azure as a trusted platform or using Jenkins as a trusted broker.  Each organisation will differ with regards to what they trust so this should be a business driven decision.</p> </details> |

### **Observability**

|  |  |
| --------- | ------- |
| &#9744;   | <details><summary>Logs shipping to central logs data warehouse</summary> <p> </summary> <p>Logs should be streamed to a central data warehouse as log rotation on the servers should be enabled and logs will be lost locally. A platform like splunk  is ideal for this use case.  There are other viable options available.</p> </details> |
| &#9744;   | <details><summary>Logs data mining scripts written.</summary> <p> </summary> <p>Decide the value that the log data should provide and write some scripts to extract this value from the data. Scripts can be written in python.  Models can also be produced to predict future loads based on existing data sets.  This kind of insight can be useful for planning.</p> </details> |
| &#9744;   | <details><summary>Logs alerting configured</summary> <p> </summary> <p>Some events should generate some kind of alert, for example, a root token being generated should be flagged and alerted on. Ensure these events have alerts configured for them.</p> </details> |
| &#9744;   | <details><summary>UpDown Alerting</summary> <p> </summary> <p>Configure updown monitoring service which checks periodically the URL you want and reports back any anomaly, be it downtime, bad response, degraded performance or even broken SSL certificate. And publishes alerts in realtime to your alerting system.</p> </details> |
| &#9744;   | <details><summary>Status Page</summary> <p> </summary> <p>Configure a status page with uptime and performance metrics.</p> </details> |

### **Governance and compliance**

|  |  |
| --------- | ------- |
| &#9744;   | <details><summary>Threat Model Exercise </summary> <p> </summary> <p>Conduct a threat modelling exercise using a framework of your organisations choosing and ensure you have documented and mitigated against all identified threats.</p> </details> |

### **Disaster Recovery**

|  |  |
| --------- | ------- |
| &#9744;   | <details><summary>DR Excercise </summary> <p> </summary> <p>Conduct a DR excercise to ensure vault can be recovered in case of disaster.</p> </details> |

### **Automation Playbooks**

|  |  |
| --------- | ------- |
| &#9744;   | <details><summary>Automation Playbooks</summary> <p> </summary> <p>Helm Charts and/or operator to deploy the above setup multiple times on different clusters.</p> </details> |

### **SLI, SLO and SLA**

|  |  |
| --------- | ------- |
| &#9744;   | <details><summary>SLIs (service level indicators), SLO (service level objectives) and SLA (service level agreements)</summary> <p> </summary> <p>Have properly defined SLIs, SLOs and SLAs.</p> </details> |

### Keys Rotation

|  |  |
| --------- | ------- |
| &#9744;   | <details><summary>Storage Key Rotation</summary> <p> </summary> <p>The Storage Key encrypts every secret that is stored in Vault, and only lives unencrypted in memory. This key can be rotated online by simply sending a call to the right API endpoint, or from the CLI. This requires the right privileges as set on the policy. From the point in time of rotating the key every new secret gets encrypted with the new key. This is a fairly straightforward process that most organizations carry out every six months, unless there is a compromise.</p> </details> |
| &#9744;   | <details><summary>Master Key Rotation</summary> <p> </summary> <p>The Master Key wraps the Storage Key, and only lives unencrypted in memory. When using automatic unsealing, the Master Key will be encrypted by the Seal Key, and recovery keys will be provided for certain operations. This process is also online, and causes no disruption, but requires the key holders to input their current shard or recovery key to validate the process, and it's time bound. This procedure is generally carried out by organizations yearly, unless there is a compromise..</p> </details> |
| &#9744;   | <details><summary>Seal Key Rotation</summary> <p> </summary> <p>...</p> </details> |

### Define Organizational Roles

In most organizations where Vault has been deployed at scale, there is no requirement for extra staffing or hiring. In terms of deploying and running the solution. Vault has no predefined roles and profiles, as the policy system allows very granular definitions of the duties for different teams, but generally speaking these have been defined in most organizations as follows:

- Consumers: Individuals or teams that require the ability to consume secrets or have a need for a namespaced Vault capability.
- Operators: Individuals or teams who onboard consumers, as well as secret engine capabilities, policies, namespaces and authentication methods.
- Crypto (Key Officers): Individual or teams who manage key rotation and audit policies
- Audit: Individual or teams who review and audit usage.
