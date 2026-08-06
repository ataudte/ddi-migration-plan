# DDI Migration Plan

## Purpose

This repository provides a vendor neutral plan for migrating DNS, DHCP and IP address management data, services and operating processes.

A DDI migration is not only a data import or server replacement. It changes critical network services, sources of authority, execution points, operational workflows, security controls and support responsibilities. The plan therefore covers:

* Migration governance and entry gates
* Data authority and reconciliation
* Lab migration and proof
* Infrastructure and operational readiness
* Phased migration waves
* DNS, DHCP and IPAM cutover
* Validation of service behavior
* Rollback triggers and procedures
* Early life support
* Legacy retirement and closure

This repository is a reference plan, not a mandatory runbook template. Enterprises should use the considerations to build their own project records, wave plans, change procedures and acceptance evidence. The minimum lifecycle inputs and the minimum handover to operations are the required interfaces.

## Position In The DDI Lifecycle

This repository is one part of a three document lifecycle:

1. [`ddi-workshop`](../../../ddi-workshop) establishes the current state, business outcomes, target operating model, target architecture and data authority model.
2. [`ddi-questionnaire`](../../../ddi-questionnaire) evaluates candidate solutions, validates critical workflows and records accepted conditions.
3. **`ddi-migration-plan`** executes the approved design and selected solution through controlled migration waves.

### Minimum Input From [`ddi-questionnaire`](../../../ddi-questionnaire)

The migration plan can be drafted while evaluation is in progress, but Gate 0 cannot pass without the following minimum input package.

| Evaluation output | Minimum content required by the migration plan |
|---|---|
| Selection decision | Product, components, versions, deployment model, licenses and support scope |
| Architecture mapping | How the selected solution implements the workshop architecture principles, including accepted deviations |
| Requirement baseline | Mandatory implementation and validation requirements with acceptance criteria |
| Gaps and conditions | Limitation, impact, mitigation, owner, due date and contractual commitment where applicable |
| Proof of concept package | Test procedure, representative data, expected result, observed result, evidence and open findings |
| Data migration findings | Supported sources, export and import methods, mapping, transformations, error handling and reconciliation behavior |
| Coexistence findings | Retained services, synchronization behavior, authority boundaries and conflict scenarios |
| Integration dependencies | Identity, ITSM, cloud, automation, security, monitoring and reporting dependencies |
| Operational requirements | Backup, recovery, patching, monitoring, access, audit, support and training requirements |
| Capacity and placement assumptions | Object counts, query and lease rates, sites, regions, failure domains and growth assumptions |
| Delivery assumptions | Vendor and customer responsibilities, lead times, services, infrastructure and prerequisites |
| Decision record | Accepted solution, rejected alternatives, unresolved decisions and review triggers |

### Supporting Input From [`ddi-workshop`](../../../ddi-workshop)

The following workshop artifacts remain authoritative supporting inputs:

* Current state architecture and inventory
* Business and operational baseline
* Target operating model
* Service disposition decisions
* Data authority and reconciliation rules
* Target architecture and security principles
* Success measures and service objectives
* Migration constraints, risks and dependencies
* Recovery objectives
* Legacy retirement objectives

### Input Acceptance Rule

If a minimum item is missing, the migration team must record the gap, owner, impact and due date. Planning may continue where useful, but the design acceptance gate must not pass through an undocumented assumption.

### Minimum Output To Operations

Migration is complete only when operations receives a usable service, not only a successful cutover.

| Migration output | Minimum content required by operations |
|---|---|
| As built architecture | Deployed management, DNS, DHCP and IPAM components, versions, locations, roles, flows, integrations and dependencies |
| Service inventory | Service instances, zones, scopes, address spaces, cloud connections, certificates, keys and ownership |
| Data authority model | Final authoritative sources, discovery sources, execution points, reconciliation rules and owners |
| Administration model | Roles, delegated scope, approvals, service accounts, break glass access and audit behavior |
| Routine runbooks | Administration, change, maintenance, backup, patch, certificate, key, reporting and capacity procedures |
| Incident runbooks | Service failure, data inconsistency, DNSSEC failure, DHCP exhaustion, integration failure, security event and emergency procedures |
| Monitoring baseline | Monitors, thresholds, dashboards, log destinations, alert routes, expected state and retention |
| Backup and recovery | Backup schedule, storage, integrity check, restore procedure, recovery evidence and objectives |
| Service validation | Confirmed DNS, DHCP, IPAM, API, security, performance and resilience behavior after migration |
| Support model | Internal and vendor contacts, support boundaries, maintenance windows, service desk knowledge and escalation paths |
| Training and knowledge | Administrator training, service desk guidance, architecture briefing and known issue review |
| Residual issues | Known defects, workarounds, coexistence controls, owners, due dates and expiry conditions |
| Legacy status | Retired, retained or rollback only components, archives, dependency clearance and disposal records |
| Acceptance record | Final decisions, open actions, lessons learned and acceptance by service and operational owners |

The detailed checklist appears in [minimum handover to operations](#minimum-handover-to-operations).

### Additional Outputs

* Approved migration strategy
* Wave plan and runbooks
* Data mapping and reconciliation results
* Readiness and go or no go decisions
* Validation evidence
* Incident, issue and decision records
* Rollback decisions where required
* Migration closure report

### Not Covered

This plan does not replace:

* A current state assessment
* Product evaluation
* Target architecture approval
* Detailed vendor installation guides
* Formal change authorization
* Organization specific incident and disaster recovery procedures

# Migration Principles

* Visibility before control.
* Authority before synchronization.
* Reconcile data before migrating services.
* Prove transformations with representative data in a lab.
* Move noncritical services before critical services.
* Define acceptance criteria and rollback triggers before cutover.
* Test rollback before depending on it.
* Preserve legacy configuration and data until the rollback window has expired.
* Do not run competing DHCP services unless coexistence behavior is deliberately designed and tested.
* Treat DNS cache, TTL, delegation and DNSSEC timing as part of the cutover.
* Treat lease state, relay behavior and client renewal timing as part of the DHCP cutover.
* Ensure monitoring, logging and support are active before production migration.
* Assign an owner and expiry date to every temporary coexistence measure.
* Include legacy retirement in the definition of done.
* Record evidence for every gate and wave.

# Table Of Contents

* [Migration Governance](#migration-governance)
  * [Roles And Responsibilities](#roles-and-responsibilities)
  * [Entry Gate](#migration-entry-gate)
  * [Decision Gates](#decision-gates)
  * [Success And Exit Criteria](#migration-success-and-exit-criteria)
* [Migration Strategy](#migration-strategy)
  * [Scope And Service Disposition](#scope-and-service-disposition)
  * [Wave Model](#recommended-wave-model)
  * [Minimum Content Of A Migration Wave](#minimum-content-of-a-migration-wave)
  * [Coexistence](#coexistence-model)
* [Data Authority And Reconciliation](#data-authority-and-reconciliation)
* [Prerequisites](#prerequisites)
* [Preparation](#preparation)
* [Migration Execution](#migration-execution)
  * [DNS Cutover](#dns-cutover)
  * [DHCP Cutover](#dhcp-cutover)
  * [IPAM Transition](#ipam-transition)
  * [Static Components](#static-network-components)
* [Validation](#validation)
* [Rollback](#rollback)
* [Post Migration](#post-migration)
  * [Minimum Handover To Operations](#minimum-handover-to-operations)
* [Legacy Retirement](#legacy-retirement)
* [Appendix](#appendix)

# Migration Governance

## Project Governance Content

Use the organization’s project, change and service management systems. Migration governance should make the following unambiguous:

* Executive sponsor and DDI service owner
* Migration manager and technical leads for DNS, DHCP, IPAM, network, cloud, Active Directory and security
* Change authority, incident commander and rollback decision authority
* Vendor, partner and customer responsibilities
* Start, target completion, early life support and legacy retirement horizons
* Decision cadence, escalation path and communication model
* Evidence repository for designs, exports, runbooks, test results and approvals

The important outcome is decision clarity. A name in a project plan is not enough unless the person understands the authority and availability expected during migration.

## Roles And Responsibilities

Define at least the following roles:

* Executive sponsor
* DDI service owner
* Migration manager
* DNS lead
* DHCP lead
* IPAM and data lead
* Network and relay lead
* Active Directory lead
* Cloud lead
* Security operations lead
* Monitoring lead
* Application validation lead
* Change manager
* Communications lead
* Vendor or implementation partner lead
* Service desk lead
* Incident commander
* Rollback decision authority

### Example Responsibility Split

| Activity | Service owner | Migration lead | DNS | DHCP | IPAM | Network | AD | Cloud | Security | Vendor |
|---|---|---|---|---|---|---|---|---|---|---|
| Approve scope | A | R | C | C | C | C | C | C | C | C |
| Approve data authority | A | C | C | C | R | C | C | C | C | C |
| Approve wave readiness | A | R | R | R | R | R | R | R | C | C |
| Execute cutover | C | A | R | R | R | R | R | R | C | C |
| Validate service | A | R | R | R | R | R | R | R | R | C |
| Decide rollback | A | R | C | C | C | C | C | C | C | C |
| Retire legacy | A | R | R | R | R | R | R | R | C | C |

`R` means Responsible, `A` means Accountable, `C` means Consulted and `I` means Informed.

## Migration Entry Gate

Migration planning may begin earlier, but production execution must not begin until the minimum input package defined in [position in the DDI lifecycle](#position-in-the-ddi-lifecycle) and the following readiness criteria are accepted. Missing input must be recorded as a condition with an owner and due date rather than converted into an implementation assumption.

### Architecture And Operating Model

* Target architecture is approved.
* Target operating model is approved.
* Service ownership and escalation are assigned.
* Replace, retain, overlay and retire decisions are recorded.
* Security architecture is approved.
* Availability and disaster recovery objectives are approved.

### Product And Delivery

* Solution selection is approved.
* Mandatory requirements are passed or covered by accepted exceptions.
* Required licenses, subscriptions and support are available for build, test, recovery and production.
* Mandatory professional services and support responsibilities are agreed.
* Product versions and support lifecycles are accepted.
* Required integrations and migration tooling are available.

### Data And Workflows

* Data authority is defined for each migrated domain.
* Conflict and reconciliation rules are approved.
* Representative source data has been analyzed.
* Critical workflows have been proven or have approved conditions.
* Import, deployment, validation and rollback methods are understood.

### Operations

* Monitoring and logging design is approved.
* Backup and recovery are designed.
* Support and escalation paths are active.
* Change windows and business restrictions are known.
* Service owners accept planned outage and recovery parameters.
* Training and operational handover plans exist.

### Entry Gate Decision

The entry gate decision should explicitly confirm that the migration team received and accepted the lifecycle input package. At minimum, the decision should state:

* Which architecture and operating model versions are approved
* Which product, components, versions, licenses and support scope will be implemented
* Which data authority and coexistence rules apply
* Which proof of concept findings must be repeated or mitigated
* Which integrations, infrastructure and external teams are prerequisites
* Which gaps are accepted and who owns them
* Which recovery, monitoring, support and training requirements are mandatory

Do not proceed when the implementation team is expected to infer unresolved design decisions from product defaults.

## Decision Gates

### Gate 0: Design Accepted

Required evidence:

* Approved target architecture
* Approved operating model
* Approved source of truth and authority rules
* Approved migration strategy

### Gate 1: Lab Ready

Required evidence:

* Target lab installed and patched
* Representative data available
* Import and transformation process version controlled
* Validation tools ready
* Rollback or reset of the lab tested

### Gate 2: Wave Ready

Required evidence:

* Wave scope and dependencies confirmed
* Source data and delta plan confirmed
* Target capacity confirmed
* Monitoring and support ready
* Runbook rehearsed
* Rollback tested
* Stakeholders and communications confirmed

### Gate 3: Go Or No Go

Required immediately before cutover:

* Change approval active
* Required personnel present
* Backups and state captures complete
* No blocking incident or infrastructure degradation
* Final export or delta captured
* Validation tools functioning
* Rollback deadline and decision authority confirmed

### Gate 4: Wave Exit Or Rollback

* Acceptance criteria met within the agreed observation period, or
* Rollback initiated because a trigger was reached, or
* Conditional acceptance approved with an owner, mitigation and deadline

### Gate 5: Legacy Retirement

* All dependent waves completed
* Rollback window expired
* Stable backup and evidence archived
* Remaining consumers removed
* Legacy licenses, firewall rules and monitoring cleaned up
* Secure data disposal completed where required

## Migration Success And Exit Criteria

Define success before migration. Use exact thresholds where the enterprise already has reliable service objectives, baselines or business impact criteria.

### Service Criteria

* DNS availability meets the approved target.
* DNS response latency and failure rate remain within accepted thresholds.
* Required authoritative, recursive, forwarding, DNSSEC and policy behavior is correct.
* DHCP clients complete allocation and renewal successfully through all required relays.
* DHCP failover or high availability reaches the expected state.
* DDNS behavior is correct.
* IPAM receives and represents service state as designed.

### Data Criteria

* Object counts reconcile within approved tolerances.
* Unsupported or rejected objects are documented.
* Required metadata and ownership are preserved.
* No unapproved duplicate or overlapping objects remain.
* Source and target differences are explained.
* Audit and provenance are available.

### Operational Criteria

* Monitoring, alerting and logs are received.
* Administrative roles and workflows are validated.
* Backup and restore are successful.
* Operations and service desk have accepted handover.
* Open defects are within the accepted threshold.
* Legacy systems are disabled or have a dated retirement plan.

### Success Evidence

Success evidence should make it possible to decide whether a wave can exit, must remain under observation or should roll back.

Evidence should cover:

* DNS availability, correctness, latency and expected authoritative or recursive behavior
* DHCP allocation, renewal, failover, relay and DDNS behavior
* IPAM object integrity, relationships, metadata and authority
* Reconciliation of source, transformed, imported, rejected and target objects
* Monitoring, alerting, logging, backup and restore
* Critical application and workflow validation
* Security policy and audit behavior
* Operational acceptance, known defects and remaining conditions

Thresholds and tolerances should come from service objectives, baselines and business impact. The plan should not invent universal values.

# Migration Strategy

## Scope And Service Disposition

Translate the workshop disposition decisions and the selected architecture into migration scope. Review at least:

* Internal authoritative DNS
* Recursive and forwarding DNS
* External authoritative DNS, registrar and parent delegation dependencies
* Active Directory integrated DNS
* DHCPv4 and DHCPv6
* IPAM and address planning data
* Cloud native DNS and IPAM services
* Discovery, reporting, security and automation services
* Retained third party or network device services

For every service domain, migration planning should state:

* Whether the service is retained, replaced, consolidated, governed through an overlay or retired
* Which system is authoritative before, during and after migration
* Which systems remain execution points
* Which wave moves management, data or service traffic
* What coexistence is required and how long it may remain
* Which consumers, integrations and operational teams are affected
* What proves completion and permits retirement

Service disposition and data disposition may differ. A DNS server may remain in service while zone administration moves, and historical lease or audit data may be archived rather than migrated.

## Recommended Wave Model

Adapt the number and order of waves to risk and dependencies.

### Wave 0: Lab And Representative Data

* Install target components.
* Import representative data.
* Reconcile errors and unsupported constructs.
* Test workflows, failure, recovery and rollback.
* Produce repeatable migration tooling.

### Wave 1: Management, Visibility And Overlay

* Establish the target data model.
* Import and reconcile noncontrolling data.
* Discover retained DNS, DHCP and cloud services.
* Validate source of truth and ownership.
* Enable reporting and monitoring without changing service execution.

### Wave 2: Noncritical Services

* Migrate test, development, lab, low impact zones or selected sites.
* Validate runbook, support and operational measures.
* Correct tools and procedures before wider rollout.

### Wave 3: Selected Production Services

* Migrate representative production sites or service classes.
* Include real application, AD, relay and security dependencies.
* Observe through a defined stability period.

### Wave 4: Critical Services

* Migrate mission critical DNS, DHCP and IPAM functions.
* Use the proven runbook and staffed support model.
* Apply stricter go or no go and rollback thresholds.

### Wave 5: Optimization And Legacy Retirement

* Remove temporary forwarding, relay and coexistence.
* Retire legacy management, service and reporting tools.
* Optimize automation, policy and delegation.
* Compare outcomes with the modernization baseline.

## Minimum Content Of A Migration Wave

Every wave should be defined well enough that a change team can execute it without redesigning the migration during the window.

| Wave dimension | Questions to answer |
|---|---|
| Business scope | Which services, users, applications and owners are affected? |
| Technical scope | Which zones, views, resolvers, DHCP servers, scopes, relays, networks, metadata, integrations and cloud environments are included? |
| Criticality | Why is the wave placed at this point in the sequence, and what is the maximum acceptable impact? |
| Dependencies | Which network, identity, firewall, database, cloud, registrar, monitoring and application dependencies must be ready? |
| Data | Which export, transformation, delta and reconciliation process applies? |
| Coexistence | Which source and target components operate simultaneously, and which system may change each object? |
| Schedule | What freeze, cutover, observation and rollback windows apply? |
| Runbook | Which prechecks, actions, validations, decision points and communications are required? |
| Acceptance | Which service, data, security, workflow and operational evidence permits wave exit? |
| Rollback | Which triggers apply, who decides and how is source state restored safely? |
| Ownership | Who leads execution, validates each domain and accepts the result? |

The wave definition should reference exact object lists and procedures stored in the enterprise’s preferred systems. Avoid broad scopes such as “migrate DNS” without service and data boundaries.

## Coexistence Model

Temporary coexistence may be necessary for discovery, forwarding, secondary service, phased clients or rollback. For every coexistence mechanism record:

* Purpose
* Direction of synchronization or forwarding
* Authoritative source
* Execution points
* Conflict behavior
* Monitoring
* Security implications
* Owner
* Start date
* Expiry date
* Removal criteria

### Coexistence Controls

For every temporary or permanent coexistence mechanism, define:

| Coexistence concern | Minimum rule |
|---|---|
| Purpose | State why coexistence is required and which migration dependency it solves |
| Authority | Declare which system may create, update and delete each data domain during coexistence |
| Data flow | Define direction, frequency, filtering, transformation and duplicate handling |
| Service traffic | Define which clients, relays, resolvers or authoritative paths use source and target services |
| Failure behavior | Explain what happens when synchronization or one side of the service is unavailable |
| Observability | Monitor divergence, backlog, errors and unauthorized changes |
| Ownership | Assign an operator for both the source, target and integration boundary |
| Expiry | Set a review or retirement condition so temporary coexistence does not become permanent by default |

Avoid active active DHCP or bidirectional DNS synchronization unless the behavior is deliberately designed, supported and tested.

# Data Authority And Reconciliation

## Authority Rules

Apply the approved data authority model to migration mechanics.

| Data domain | Migration considerations |
|---|---|
| IPv4 and IPv6 networks | Preserve hierarchy, VRF or tenant context, allocation status, reservations, exclusions and ownership. Resolve overlaps before target allocation is enabled. |
| IP addresses | Distinguish static assignments, DHCP leases, reservations, discovered use and cloud assigned addresses. Do not promote observed state automatically without policy. |
| DNS zones | Preserve type, view, delegation, transfer, update, DNSSEC, forwarding and publication behavior. Define authority per namespace. |
| DNS records | Account for static, dynamic, Active Directory, application managed and infrastructure as code records. Define duplicate and update precedence. |
| DHCP scopes and pools | Preserve network association, failover, options, exclusions, classes, relay context and lease timing. |
| Reservations | Define matching keys, duplicate behavior, metadata and relationship to IPAM objects. |
| Lease state | Treat active lease state differently from configuration. Decide whether leases migrate, age out or are recreated through controlled renewal. |
| Metadata and ownership | Preserve mandatory fields, provenance, inheritance and data classification. Decide how field conflicts are resolved. |
| Cloud resources | Preserve cloud account, region, project, network and object identity. Distinguish discovery from enterprise authority. |

For each domain, the migration design should define matching keys, transformation rules, conflict handling, approval of exceptions and who may authorize a change in authority.

## Data Quality Checks

### DNS

* Invalid zone or record syntax
* Duplicate owners or conflicting record types
* Orphaned CNAME, PTR, delegation or glue records
* Lame or inconsistent delegations
* Stale records
* Serial and transfer inconsistencies
* Views and split DNS ambiguity
* Unsupported record or option types
* Dynamic versus static ownership
* DNSSEC state, keys, DS and signature validity
* Forwarding and recursion policy

### DHCP

* Overlapping scopes and pools
* Reservations outside valid scope
* Duplicate MAC, client identifier or DUID mappings
* Invalid or unsupported options
* Conflicting classes and option inheritance
* Lease state and reservation conflicts
* Relay and subnet mismatch
* Failover peer and state consistency
* DDNS ownership and cleanup behavior

### IPAM

* Overlapping networks without VRF or tenant context
* Duplicate addresses
* Missing parent or child hierarchy
* Invalid prefix length
* Missing ownership and lifecycle
* Inconsistent metadata values
* Conflicts with cloud and routing data
* Stale or orphaned addresses
* IPv4 and IPv6 relationship assumptions

## Transformation Controls

* Transformations are scripted or documented repeatably.
* Source files are preserved unchanged.
* Transformation code and mapping files are version controlled.
* Every rejected or modified object is logged.
* Dry run output is reviewed before import.
* Unsupported constructs have an approved disposition.
* Custom scripts have an owner and test cases.
* Counts and checksums are produced for each stage.
* Sensitive data is handled according to policy.

## Reconciliation Stages

1. Inventory source systems and export methods.
2. Capture baseline object counts and configuration checksums.
3. Normalize formats without changing source evidence.
4. Parse and classify objects.
5. Apply approved mapping and transformation.
6. Identify conflicts and unsupported constructs.
7. Resolve according to authority rules.
8. Import into an isolated target.
9. Compare source and target semantically.
10. Validate service behavior.
11. Repeat until results meet acceptance criteria.
12. Define final delta capture for cutover.

## Reconciliation Evidence

A reconciliation report should explain the result of each rehearsal, delta import and production wave. At minimum, show:

| Object family | Minimum checks |
|---|---|
| DNS zones | Source, transformed and target counts; rejected zones; type or view changes; delegation and DNSSEC exceptions |
| DNS records | Record counts by type; duplicate and invalid records; dynamic records excluded or handled separately; forward and reverse consistency |
| DHCP scopes | Scope and pool counts; overlaps; option inheritance; failover association; relay coverage |
| Reservations and leases | Matching key behavior; duplicates; active state treatment; client renewal observations |
| IPAM networks and addresses | Hierarchy, overlaps, utilization, status, VRF or tenant context and relationships |
| Metadata | Required field completeness, ownership, provenance and values that were defaulted or transformed |
| Integrations | Objects or events processed, rejected, retried or left pending |

Explain every material difference rather than forcing counts to match. A lower target count may be correct when stale or duplicate data is intentionally removed, but the rationale and approval must be visible.

# Prerequisites

## Kickoff Meeting

The kickoff confirms project scope, roles, business and technical requirements, wave strategy, validation, communications and rollback.

Required decisions:

* Scope and exclusions
* Project and service ownership
* Migration waves
* Data authority
* Change and freeze process
* Go or no go authority
* Rollback authority
* Escalation and communications
* Required evidence and reporting

## Licenses And Support

Required licenses, subscriptions and support must be available before installation and testing, not only on the first migration day.

Confirm:

* Production capacity
* Disaster recovery capacity
* Test and lab capacity
* Temporary coexistence capacity
* Migration tooling
* API and integration entitlements
* Security, reporting and discovery entitlements
* Vendor support coverage during cutover
* Emergency escalation contacts

## Target Systems To Be Built

Specify for all management, DNS, DHCP, database, worker, reporting and security components:

* Hostname
* IPv4 and IPv6 address and prefix
* Gateway and routing
* Name servers and search domains
* NTP sources and time zone
* Management and service interfaces
* VLAN, VRF and firewall context
* SNMP or monitoring credentials
* Syslog, SIEM and observability configuration
* Backup target
* Authentication and identity integration
* Certificate and key requirements
* Secret storage
* Administrative and break glass access
* Product role and capacity
* High availability peer or cluster
* Software and patch version
* License assignment
* Support registration

### Build Acceptance

* Components are installed according to approved design.
* Required patches are applied.
* Time and name resolution are correct.
* Authentication and roles are validated.
* Monitoring and logging are active.
* Backup and restore are tested.
* High availability is tested.
* Management outage behavior is tested.
* Configuration is documented and exported.

## Legacy Systems

Inventory every source or retained system that can affect migration, including appliances, virtual machines, cloud services, network device functions, databases, scripts and spreadsheets.

For each legacy system, understand:

* Product, version, support state and access method
* Service role and managed data
* Hosting location and dependencies
* Owners, administrators and support provider
* Export, backup and restore capability
* Consumers, integrations and hidden operational use
* Whether it remains active, becomes read only, supports rollback or is retired
* How long evidence and configuration must be retained
* How credentials, licenses, firewall rules, monitoring and data are removed at retirement

Do not decommission a system merely because production traffic has moved. Management, reporting, automation or audit consumers may still depend on it.

## Access And Administrative Readiness

Required access may include:

* Target management UI and API
* Target service console
* Source management UI, API and console
* Cloud accounts or read only discovery roles
* DNS hosting and registrar portals
* Active Directory administration
* Network devices and relay configuration
* Firewall and load balancer administration
* Monitoring and SIEM
* Backup systems
* Jump or validation host

Use least privilege. Root or equivalent access is required only when the approved installation, recovery or troubleshooting procedure requires it.

## Site Survey

Collect feedback from operations, application, support and affected business teams.

Identify at least four weeks before a production wave:

* IP phones
* PXE clients
* Wireless access points and controllers
* Printers and barcode scanners
* VPN concentrators
* Network devices
* Storage and file services
* SaaS applications
* Custom applications
* Statically configured servers and devices
* Devices with embedded DNS or DHCP settings
* Clients with unusual lease or option behavior
* Services with pinned resolver or server addresses
* Sites with limited support or connectivity

### Application Validation Coverage

Application validation should cover representative dependency patterns, not only a small list of well known applications.

| Dependency pattern | Validation considerations |
|---|---|
| Static DNS dependency | Forward and reverse records, aliases, TTL, search suffix and resolver path |
| Dynamic DNS dependency | Update identity, ownership, scavenging, conflict behavior and replication timing |
| Service discovery | SRV, NAPTR, TXT or application specific record behavior |
| DHCP dependent clients | Allocation, renewal, options, relay, PXE or vendor class behavior |
| Active Directory | Domain controller registration, site location, secure dynamic update and replication related records |
| Cloud workload | Private zone association, conditional forwarding, resolver endpoints and account or project boundaries |
| Security controls | DNS filtering, logging, sinkhole, policy exception and investigation visibility |
| Legacy or embedded systems | Hard coded resolver, server or relay addresses and long lived caches |

Choose applications by criticality and dependency diversity. The owner should validate business behavior, while the migration team validates the DDI path and evidence.

## IP Address Ranges And Network Context

* Identify target management and service subnets.
* Identify client, VPN, server, voice, guest, cloud and special networks.
* Map VRF or tenant context.
* Identify ACL requirements for recursive DNS, DHCP and administration.
* Identify routes and return paths.
* Confirm reverse DNS boundaries.
* Confirm IPv6 and dual stack requirements.
* Confirm NAT or overlapping address implications.

## DHCP Relays

Review every DHCP relay path before changing server addresses or service ownership.

Consider:

* Relay device, interface, VLAN, VRF and address family
* Current and target server addresses
* Redundant path and routing behavior
* ACL, firewall and control plane policy
* Source address and gateway address behavior
* Option 82 insertion, trust and policy
* Load balancer, anycast or relay chaining where present
* Device configuration method and rollback
* Client segments used for validation
* Monitoring for unanswered requests, retransmissions and unexpected responders

Do not add source and target DHCP servers to every relay as a generic migration method. Simultaneous responses can create unpredictable allocation unless coexistence is explicitly supported and tested.

## Firewall And Communication Activation

Specify and validate flows at least three weeks before a wave.

Include:

* Management to service
* Service to management
* DNS client to resolver
* Authoritative DNS transfers and notifications
* Dynamic DNS updates
* DHCP client, relay and server
* High availability and cluster communication
* Monitoring, syslog and SIEM
* Backup and restore
* Authentication and directory services
* Cloud API endpoints
* Vendor support where approved

### Communication Flow Coverage

Communication readiness should cover all control and data paths, including:

| Flow category | Examples to verify |
|---|---|
| DNS service | Client queries, recursion, forwarding, notify, transfer, dynamic update and DNSSEC related communication |
| DHCP service | Client relay traffic, failover, lease query, DDNS and administrative control |
| Management | Administrator access, API, database, clustering and configuration deployment |
| Identity | Directory, federation, multifactor, certificate and privileged access dependencies |
| Integration | ITSM, CMDB, automation, cloud, event, message bus and webhook traffic |
| Monitoring and security | Metrics, logs, query data, traps, SIEM, SOAR and threat intelligence |
| Backup and recovery | Backup repository, replication, archive and restore paths |
| Vendor support and update | Package repositories, licensing, support tunnel or remote assistance paths where approved |

For each flow, verify direction, protocol, source identity, destination, purpose, security control, owner and test method using the enterprise’s existing firewall and network documentation.

## DNS TTL And Delegation Preparation

Create a record specific plan rather than reducing all TTLs blindly.

* Identify records and delegations that change during cutover.
* Identify parent zone and registrar dependencies.
* Identify DNSSEC DS changes where applicable.
* Reduce TTLs early enough for previous values to expire.
* Preserve appropriate negative caching behavior.
* Confirm that low TTLs will not create unacceptable query load.
* Record when normal TTLs will be restored.
* Confirm external resolver visibility where public DNS is involved.

## DHCP Lease Preparation

Lease reduction must be scoped and timed according to client behavior, T1 and T2, failover design and rollback needs.

* Identify scopes included in each wave.
* Record current lease duration and client sensitivity.
* Reduce lease duration gradually where required.
* Confirm clients receive the reduced lease before cutover.
* Avoid an unnecessary global reduction.
* Confirm target capacity for increased renewal traffic.
* Define when operational lease values will be restored.
* Record exceptions for devices that cannot tolerate short leases.

## DDI Exports

Capture exports at the agreed times and preserve them unchanged.

### DNS Exports

* Server configuration
* Views
* Zone definitions
* Zone content
* Forwarding and recursion policy
* Transfer and update ACLs
* TSIG or key references handled securely
* Dynamic update state
* DNSSEC configuration and key inventory
* Extensions and include files
* Query and service statistics

### DHCP Exports

* Server configuration
* Shared networks, scopes, pools and reservations
* Options, classes and option spaces
* Failover configuration and state
* Active leases where migration requires them
* DDNS settings
* Extensions and include files
* Service statistics

### IPAM Exports

* Spaces, VRFs, ranges, networks and addresses
* Metadata and ownership
* VLAN and device relationships
* DNS and DHCP relationships
* Users, roles and permissions where in scope
* Audit or history where required
* Reports and templates where supported

### Export Controls

* Record source, time and operator.
* Calculate checksums.
* Store securely with access control.
* Document post export changes.
* Define final delta capture.
* Confirm restore usability.

## Data Preparation

* Evaluate quantity and quality.
* Run initial and repeated dry imports.
* Review DHCP options, classes and vendor specific behavior.
* Review DNS views, forwarding, zone types, dynamic updates and DNSSEC.
* Define target server and policy groupings.
* Transform only through controlled mappings.
* Resolve data authority conflicts.
* Verify imported objects in the lab.
* Define global and inherited DNS and DHCP options.
* Produce a reconciliation report.
* Obtain approval for rejected or transformed objects.

## Validation Host

A controlled validation host or service is required for query comparison, packet capture, scripts and client tests.

Suggested capabilities:

* Supported Linux or equivalent operating system
* Static IPv4 and IPv6 configuration
* Browser
* Python and required modules
* DNS tools such as `dig`, `delv`, `kdig` or equivalent
* DHCP client and packet capture tools
* Zone comparison utilities
* File conversion and checksum tools
* Access to approved package repositories
* Synchronized time
* Secure storage for test results

Internet access should be limited to what the approved design requires.

## Monitoring And Support Readiness

Before production migration:

* Target service health is monitored.
* Synthetic DNS and DHCP tests are active.
* Logs reach the required platforms.
* Alerts have owners and escalation.
* Dashboards show source and target during coexistence.
* Service desk has knowledge articles and contact paths.
* Vendor support case and escalation procedures are prepared.
* War room or coordination channel is scheduled.
* Incident and rollback decision logs are ready.

# Preparation

## Follow Up Review

At least one week before a wave, review:

* Target build status
* Access
* Site survey
* Application test plan
* Address and DNS scope
* Relay changes
* Firewall changes
* TTL and lease preparation
* Lab migration anomalies
* Monitoring and support
* Open risks and defects
* Runbook and rollback rehearsal

No open item may be treated as accepted by silence. It must be closed, conditionally accepted or escalated.

## Lab Migration

The lab migration must use representative complexity, not only clean sample data.

Test:

* Data import and reconciliation
* Zone, view and record deployment
* Scope, option, class and reservation deployment
* Active Directory secure updates where in scope
* DNSSEC signing and validation where in scope
* Cloud discovery and retained service overlay
* API and workflow automation
* Role based access
* Backup, restore and reset
* Failure and recovery
* Rollback
* Performance at representative scale

## Cutover Runbook

The runbook must contain exact commands or UI actions, owners and expected results for:

* Start and communication
* State capture and backup
* Change freeze
* Final export and delta
* Target import and deployment
* DNS changes
* DHCP and relay changes
* IPAM authority transition
* Client and application validation
* Monitoring review
* Go, hold or rollback decision
* Rollback execution
* Closure and observation

### Minimum Content Of A Runbook Step

Each executable runbook step should state:

* Planned time or dependency sequence
* Responsible operator and validating operator
* Exact action or command
* Preconditions and expected result
* Evidence to capture
* Decision point and escalation path
* Rollback action where the step changes state
* Status and actual completion time during execution

Commands in a production runbook should be exact and prevalidated. Avoid symbolic placeholders in the final executable procedure. Where values differ by wave, generate the runbook from an approved inventory or list the exact value for each object.

## Rollback Rehearsal

Rehearse:

* Restoring source DNS authority or forwarding
* Restoring recursive client configuration
* Restoring DHCP service and relay targets
* Restoring IPAM or management state
* Reversing registrar, delegation or DNSSEC changes where applicable
* Replaying or reconciling changes made during the failed window
* Communicating rollback
* Validating source services after rollback

Measure the rehearsal time and compare it with the approved recovery objective.

## Go Or No Go Meeting

Review immediately before the change:

* Blocking incidents
* Target health
* Source health
* Final backups
* Personnel and support availability
* Change and freeze status
* Data delta
* Monitoring and validation
* Rollback deadline
* Weather, event or business restrictions where relevant

Record the decision and accountable approver.

# Migration Execution

## Start Of Change

* Open coordination channel and attendance.
* Confirm change authorization.
* Confirm no blocking incident.
* Record source and target health.
* Confirm time synchronization.
* Activate enhanced monitoring.
* Announce start to stakeholders.

## Safeguarding DNS, DHCP And IPAM Data

* Capture final source configuration.
* Capture source service state and object counts.
* Capture target configuration and backup.
* Calculate and record checksums.
* Disable only the scheduled processes that can interfere with migration.
* Record disabled processes for restoration.
* Preserve source services for rollback.
* Protect keys, credentials and lease data.

## Final Delta And Import

* Apply the agreed change freeze.
* Export changes since the last approved migration data set.
* Transform the delta through the tested process.
* Review conflicts.
* Import into the target.
* Validate import logs.
* Reconcile counts and known variances.
* Obtain data lead approval before service cutover.

## DNS Cutover

Use only the subsections relevant to the wave.

### Authoritative DNS

* Capture final zone content from the authoritative source.
* Deploy target zones and policies.
* Compare source and target semantically, not only as raw text.
* Validate SOA, NS, glue, delegations and authoritative answers.
* Validate views and source based behavior.
* Validate NOTIFY and transfer paths.
* Validate dynamic update authentication and ownership.
* Validate DNSSEC signatures, DNSKEY and DS relationships where applicable.
* Change delegation, load balancer, anycast advertisement or service address according to the design.
* Observe external or internal cache behavior.
* Do not delete legacy zones or keys during the rollback window.

### Recursive DNS

* Validate recursion, forwarding, root access and conditional forwarding.
* Validate access control and policy behavior.
* Validate DNSSEC validation.
* Validate RPZ or equivalent security policy.
* Change client resolver addresses through DHCP, Group Policy, configuration management or other approved methods.
* Validate source address and network path.
* Monitor query rate, latency, timeouts, SERVFAIL and policy actions.
* Retain the approved source resolver rollback method.

### Active Directory DNS

* Confirm replication health before change.
* Synchronize required partners according to the approved procedure.
* Confirm domain controller locator records.
* Confirm secure dynamic update.
* Confirm registration and scavenging behavior.
* Validate authentication, Group Policy, service location and replication.
* Avoid suppressing replication unless the specific runbook requires it and rollback is understood.

### Public DNS And Registrar

* Confirm registrar and registry access.
* Confirm parent delegation timing.
* Confirm DNSSEC DS publication or removal sequence.
* Validate from multiple independent external resolvers and authoritative paths.
* Monitor for lame delegation, SERVFAIL and inconsistent answers.
* Preserve old service until the approved propagation and rollback window expires.

## DHCP Cutover

### Before Service Switch

* Confirm target scopes, pools, reservations, classes and options.
* Confirm relay reachability.
* Confirm failover or high availability health.
* Confirm DDNS credentials and policy.
* Confirm lease state strategy.
* Confirm target capacity for renewal traffic.

### Service And Relay Sequence

Choose one tested sequence. Examples include:

* Disable source service, change relay target, enable or activate target service.
* Add target to a controlled relay set, remove source, validate, then continue by network.
* Use a supported failover or migration mechanism provided by the platforms.

The runbook must prevent unintended simultaneous authoritative allocation from source and target.

### Validation During Cutover

* Confirm DHCPDISCOVER and SOLICIT reach the intended service.
* Confirm DORA and SARR complete.
* Confirm correct address, prefix and options.
* Confirm renew and rebind.
* Confirm reservation behavior.
* Confirm relay information handling.
* Confirm DDNS forward and reverse records.
* Confirm failover state and load distribution.
* Monitor declines, NAK, exhaustion, duplicate address and latency.

### Source Preservation

* Stop or isolate source allocation according to the rollback design.
* Preserve source configuration and lease data.
* Do not remove source roles until the rollback window expires.

## IPAM Transition

* Activate the approved authoritative data model.
* Confirm data ownership and resource scopes.
* Confirm target DNS and DHCP associations.
* Confirm discovery versus authoritative state.
* Enable synchronization and event processing in a controlled order.
* Monitor queues, failed deployments and reconciliation exceptions.
* Validate API, reporting and workflow integrations.
* Confirm audit trail and previous state.
* Prevent changes in the legacy IPAM after authority transfers, except through the rollback procedure.

## Static Network Components

Migrate statically configured resolver, server or option addresses through a tracked inventory.

* Servers
* Network devices
* Printers and appliances
* Hypervisors
* Storage
* VPN and security appliances
* Application configuration
* Embedded systems

### Static Dependency Coverage

Static dependencies often cause the final migration failures. Review and track at least:

* DHCP helper and relay server addresses
* Client and server resolver configuration
* Conditional forwarders and stub or secondary relationships
* Firewall, ACL and network security policy
* Load balancer, anycast, VIP and routing configuration
* Registrar nameserver, glue and DS data
* Monitoring targets, dashboards and synthetic tests
* Backup, automation and configuration management references
* Application allow lists and hard coded DNS or DHCP server addresses
* Documentation, scripts and operational shortcuts that name legacy systems

Each dependency needs an owner, target state, change method, validation and retirement action.

# Validation

Validation must compare expected service behavior, not only process status.

## DNS And DHCP Server Validation

### DNS

* Service process and role are healthy.
* Expected forward and reverse zones are loaded.
* Zone counts match the accepted reconciliation report.
* Transfers and notifications are healthy.
* SOA, NS, glue, A, AAAA and PTR data are correct.
* Delegations are valid.
* Recursive behavior matches policy.
* Views return the expected answers.
* Dynamic updates succeed and are audited.
* DNSSEC signing and validation are correct.
* Security policy and logging are active.
* Query latency, error rate and capacity are within thresholds.

### DHCP

* Service process and role are healthy.
* Expected networks, scopes, pools, reservations, classes and options are present.
* Failover or high availability is healthy.
* Client allocations and renewals succeed.
* Relay paths are correct.
* DDNS updates succeed.
* Utilization and capacity are within thresholds.
* No unplanned source DHCP response is observed.

Product specific process names such as `named` or `dhcpd` may be added to the runbook where relevant.

## Client Validation

* IPv4 address allocation
* IPv6 address or prefix allocation where applicable
* Required DHCP options
* Special VoIP, PXE, wireless and device options
* DNS resolution of internal and external names
* Split DNS behavior
* Active Directory logon
* Group Policy
* VPN access
* Email and collaboration services
* SaaS access
* File and storage access
* Custom applications
* Network devices and embedded systems
* Static resolver configuration

## IPAM And Management Validation

* DNS and DHCP events reach the management platform.
* Event and deployment queues are not blocked.
* Leases and dynamic DNS records appear as designed.
* Discovery does not overwrite authoritative data unexpectedly.
* Data provenance and ownership are visible.
* Roles and delegated access are correct.
* API and workflow operations succeed.
* Audit records include user and machine actions.
* Reports and exports are accurate.
* Backup completes.

## Security Validation

* Authentication, MFA and role based access operate correctly.
* Administrative and service communication uses approved protection.
* DNS security policy actions are correct.
* DNSSEC state is valid.
* Logs reach SIEM and observability systems.
* Alerts are generated and routed.
* Incident responders can identify client, query, answer and policy action where required.
* DHCP lease and identity events are available according to policy.
* No unexpected open recursion, transfer, update or administration exposure exists.

## Performance And Resilience Validation

* DNS query latency and error rate
* DHCP response and allocation time
* Management and API latency
* Queue and deployment time
* CPU, memory, disk and network utilization
* Failover and recovery behavior
* Management plane isolation behavior
* Backup and restore
* Monitoring and alert timing

## Validation Evidence

Retain validation evidence in the enterprise’s test or change system. For every critical test, record:

* The service, client, application or workflow tested
* Expected behavior and the source of that expectation
* Actual observation and timestamp
* Test location, resolver, relay or execution point
* Logs, packet evidence, query output, API response or screenshot where useful
* Severity and business impact of any deviation
* Decision to accept, remediate, hold or roll back
* Accountable validator

Validation should include positive and negative tests. Confirm not only that intended users can perform a task, but also that unauthorized access, invalid updates and prohibited recursion or allocation are blocked.

## Observation Period

For each wave define:

* Start and end
* Enhanced monitoring
* Defect thresholds
* Business validation
* Support staffing
* Rollback deadline
* Criteria for proceeding to the next wave

# Rollback

Rollback is a planned migration outcome, not an improvisation after failure.

## Rollback Triggers

Rollback triggers must be agreed before the change window and tied to service objectives, business impact and the remaining rollback time. Consider triggers such as:

* Sustained DNS unavailability, error rate or latency outside the accepted service envelope
* Incorrect authoritative data, delegation, DNSSEC or resolution behavior that affects critical services
* DHCP allocation or renewal failure across a material client scope
* Unexpected DHCP responders, duplicate allocation or relay path failure
* Unexplained data loss, object divergence or authority conflict
* Failure of required monitoring, logging, access control or security policy
* Critical application failure traced to the migration
* Inability to complete validation before the safe rollback deadline
* Loss of operational control, vendor support or required personnel during an unstable state

A single transient test failure does not always require rollback. The trigger definition should include signal, scope, duration, evidence source, decision authority and whether the immediate action is hold, remediate, escalate or roll back.

## Rollback Decision

Record:

* Trigger reached
* Time detected
* Evidence
* Options considered
* Time remaining
* Decision
* Approver
* Communication

## Generic DNS Rollback

Adapt to the architecture:

* Stop further target changes.
* Restore previous delegation, service address, routing, forwarding or client resolver configuration.
* Reenable preserved source authoritative or recursive service.
* Restore source zone data or configuration from the final approved snapshot if required.
* Reverse DNSSEC DS or key sequence only according to the tested plan.
* Validate source service and cache behavior.
* Reconcile changes made during the target window.
* Preserve target evidence for analysis.

## Generic DHCP Rollback

* Stop target allocation safely.
* Restore source service from preserved configuration and lease state.
* Restore relay targets according to the runbook.
* Confirm only the intended service is authoritative.
* Validate DORA, SARR, renewal, options and DDNS.
* Reconcile leases and reservations changed during the target window.
* Monitor for duplicate or conflicting allocations.

## Generic IPAM Rollback

* Stop target deployment and write workflows.
* Restore legacy authority according to the operating model.
* Restore or reconcile management data.
* Preserve audit and changed objects from the target period.
* Confirm DNS and DHCP execution points are controlled by the restored authority.
* Communicate temporary process restrictions.

## Post Rollback

* Validate restored services.
* Inform stakeholders.
* Open problem analysis.
* Preserve logs, configurations and packet captures.
* Reconcile data changed during the window.
* Reassess risk and corrective actions.
* Repeat the wave only after a new readiness decision.

# Post Migration

## Post Migration Checkpoint

Before accepting the wave:

* All mandatory validation tests pass.
* Conditional results have owners and deadlines.
* Business and application owners accept service.
* Security and monitoring teams accept telemetry.
* No rollback trigger is active.
* Data reconciliation is approved.
* Source systems remain available according to the rollback plan.
* The next wave is not started automatically without review.

## Post Migration Tasks

### DHCP

* Restore operational lease values.
* Enable approved DDNS optimization and conflict handling.
* Remove legacy relay targets after rollback expiry.
* Review utilization and failover balance.

### DNS

* Restore normal TTLs according to plan.
* Remove temporary forwarding or secondary relationships after rollback expiry.
* Review delegation and DNSSEC state.
* Confirm dynamic update and scavenging policy.

### IPAM And Management

* Reenable approved scheduled backups and deployments.
* Remove temporary change locks.
* Run a stable state backup.
* Review failed events and reconciliation exceptions.
* Confirm users, roles, integrations and reports.

### Operations

* Complete remaining static client changes.
* Update CMDB, diagrams and support documentation.
* Record final configuration and checksums.
* Review incidents, defects and lessons.
* Update the next wave runbook.

## Early Life Support

Define a staffed stabilization period based on criticality, not a fixed one week for every migration.

Activities:

* Enhanced service and client monitoring
* Direct support by responsible teams
* Daily defect and risk review
* Application and user validation
* Capacity and performance review
* Security event review
* Data quality and reconciliation review
* Vendor escalation where required

### Early Life Support Exit Criteria

* No unresolved critical or high defect.
* Service measures remain within target for the agreed period.
* Support volume is within the accepted range.
* Operations can resolve routine incidents.
* Backup and recovery evidence is accepted.
* Documentation and training are complete.
* Legacy retirement decision is approved.

## Minimum Handover To Operations

Operations should accept the migrated service only when the following minimum package is complete.

| Handover item | Minimum content | Acceptance check |
|---|---|---|
| As built architecture | Management, DNS, DHCP and IPAM components, versions, locations, roles, network flows, integrations and dependencies | Matches the deployed service and approved change record |
| Service inventory | Service instances, zones, scopes, address spaces, cloud connections, certificates, keys and external dependencies | Ownership and lifecycle are assigned |
| Data authority model | Authoritative source, discovery source, execution point, reconciliation rule and owner for each data domain | Operations can resolve conflicts without project interpretation |
| Administration model | Roles, groups, delegated scope, approvals, service accounts and break glass access | Access is tested and audit records are visible |
| Routine runbooks | Provisioning, change, backup, patch, certificate, key, reporting and capacity procedures | An operator not involved in the project can follow them |
| Incident runbooks | Service failure, data inconsistency, DNSSEC failure, DHCP exhaustion, integration failure and security event procedures | Escalation and decision authority are explicit |
| Monitoring baseline | Monitors, synthetic tests, thresholds, dashboards, alert routes, expected state and log retention | Alerts reach the responsible team and have been tested |
| Backup and recovery | Backup schedule, storage, integrity check, restore procedure, recovery evidence and objectives | At least one representative restore has been completed |
| Service validation | Final DNS, DHCP, IPAM, API, security, performance and resilience test results | Results meet approved acceptance criteria or have accepted conditions |
| Support model | Internal support tiers, vendor support, maintenance windows, escalation paths and service desk knowledge | Support ownership is active after project closure |
| Training and knowledge | Administrator training, service desk guidance, architecture briefing and known issue review | Required roles confirm readiness |
| Residual issues | Known defects, workarounds, temporary coexistence, risk owner, due date and expiry condition | No temporary measure is open without an owner and review date |
| Legacy status | Retired, retained or rollback only components, archives and dependency clearance | Legacy state is explicit and unauthorized reuse is prevented |
| Acceptance record | Service owner acceptance, operational owner acceptance and remaining actions | Migration can close without losing accountability |

### Operational Handover Acceptance

The handover is accepted when:

* Operations can administer and recover the service without relying on undocumented project knowledge.
* Monitoring, logging, backup and support paths are active and tested.
* Final authority, access and ownership models are approved.
* Residual issues and temporary measures have owners and review dates.
* Legacy systems are retired or governed by an explicit retention decision.

## Closeout Report

Include:

* Scope migrated
* Timeline and decisions
* Data reconciliation results
* Validation results
* Incidents and rollback events
* Open conditions and owners
* Performance against baseline
* Lessons for later waves
* Legacy retirement status
* Final acceptance

# Legacy Retirement

Legacy retirement is a controlled phase. Disabling a service is not the same as retiring it.

## Retirement Prerequisites

* Dependent services and clients have moved.
* Rollback window has expired.
* Stable target backup exists.
* Required historical data and audit are archived.
* Regulatory retention is satisfied.
* Ownership approves retirement.

## Retirement Tasks

* Disable legacy service roles.
* Remove legacy DHCP relay targets.
* Remove obsolete DNS forwarding, secondary and transfer relationships.
* Remove old resolver addresses from DHCP, Group Policy and static configuration.
* Remove obsolete firewall rules and routes.
* Remove monitoring and backup jobs.
* Revoke certificates, keys, tokens and service accounts.
* Update CMDB and architecture documentation.
* Terminate licenses and support where appropriate.
* Export retained data.
* Securely erase systems and storage according to policy.
* Record retirement date and evidence.

### Retirement Evidence

Legacy retirement evidence should confirm:

* The last production and management consumers have moved
* The rollback window has expired and required archives exist
* Dependencies, integrations and scheduled jobs have been removed or redirected
* Monitoring, backup and support references have been closed
* Licenses, subscriptions, credentials, certificates and firewall rules have been cleaned up
* Data retention and secure disposal requirements have been met
* Asset, CMDB, architecture and operational documentation has been updated
* The service owner and security or compliance stakeholders have accepted retirement

A disabled system is not retired while it remains a hidden dependency or an undocumented recovery plan.

# Appendix

## Kickoff Meeting Agenda

* Welcome and project team
* Scope, outcomes and exclusions
* Current DNS, DHCP and IPAM challenges
* Approved target architecture and operating model
* Solution and migration approach
* Wave plan and milestones
* Data authority and reconciliation
* Technical prerequisites and integrations
* Security, monitoring and support
* Risks and mitigations
* Validation and acceptance
* Rollback and incident command
* Training and handover
* Actions, owners and next meetings

## Environmental Analysis

### Overview

* Project scope
* Location diagram
* Location classification
* Connectivity and failure domains
* Service criticality

### Internal DNS

* Servers and versions
* Roles and distribution
* Critical forward and reverse zones
* Views and split DNS
* Hierarchy, forwarding and secondary relationships
* Active Directory integration
* Dynamic DNS
* DNSSEC
* IPv6
* Resilience
* Test environment

### DHCP

* Servers and versions
* Roles and distribution
* Networks, scopes, pools and reservations
* Client quantity and utilization
* Special options
* DHCPv6 and prefix delegation
* NAC and MAC related controls
* Resilience
* Test environment

### IPAM

* Current systems
* Address hierarchy and routing contexts
* Migration format
* Data conflicts and quality
* Metadata and ownership
* DNS and DHCP integration
* Cloud integration
* API and workflow

## Fundamental Communication Concept

Document at least:

* Legacy systems to target systems
* Target management to target services
* Client networks to DNS services
* DHCP relays to DHCP services
* VPN networks to DNS services
* DNS transfer and update partners
* Active Directory to DNS
* Cloud workers to cloud APIs
* Monitoring, logging and backup

## Microsoft DNS And DHCP Export

When using a PowerShell export script:

* Use a supported Windows Server and PowerShell version.
* Run with approved administrative privileges.
* Back up DNS and DHCP before the export.
* Test the script on a nonproduction server.
* Review the script and output location.
* Record logs and errors.
* Protect output because it may contain sensitive configuration and lease data.
* Calculate checksums and preserve the original archive.

Example invocation:

```powershell
Set-ExecutionPolicy RemoteSigned
powershell -ExecutionPolicy Bypass -File "C:\path\to\export_ms-dns-dhcp.ps1"
```

## Domain Controller Commands

Commands must be reviewed and adapted by the Active Directory owner.

### Synchronization Example

```powershell
repadmin /syncall /APed
```

Common options:

* `/A` synchronizes all naming contexts held by the home server.
* `/P` pushes changes outward from the specified domain controller.
* `/e` includes domain controllers across enterprise sites.
* `/d` identifies servers by distinguished name in messages.

### Registration Example

```powershell
net stop netlogon
net start netlogon
ipconfig /registerdns
dcdiag /test:DNS /DnsBasic
dcdiag /test:DNS /DnsRecordRegistration
```

### Replication Control Example

The following PowerShell example uses an explicit variable rather than an angle bracket placeholder. Replace the example value with an approved domain controller name before use.

```powershell
$DcName = "dc01.example.net"

repadmin /options $DcName +DISABLE_OUTBOUND_REPL
repadmin /options $DcName +DISABLE_INBOUND_REPL

# Re-enable After The Approved Migration Or Recovery Step
repadmin /options $DcName -DISABLE_OUTBOUND_REPL
repadmin /options $DcName -DISABLE_INBOUND_REPL
```

Replication changes can affect Active Directory well beyond DNS. Use them only with Active Directory ownership, a documented reason, validation and a recovery plan.

## Shared Terminology

* **Authoritative source**: The source whose state is accepted as correct for a defined data domain.
* **Coexistence**: A temporary, controlled state in which source and target services both have defined roles.
* **Delta**: Changes made after the baseline migration data set was captured.
* **Discovery source**: A source that observes infrastructure state but is not automatically authoritative.
* **Execution point**: A DNS, DHCP or cloud service that applies or publishes configuration.
* **Migration wave**: A bounded set of services, data, sites and dependencies migrated under one readiness and acceptance decision.
* **Rollback trigger**: A measurable condition requiring a hold, escalation or restoration of the prior service state.
* **Source of truth**: Governed authoritative data domains with explicit ownership, provenance and reconciliation rules.

## Acronyms

* AD: Active Directory
* API: Application Programming Interface
* DDI: DNS, DHCP and IP address management
* DDNS: Dynamic DNS
* DHCP: Dynamic Host Configuration Protocol
* DORA: Discover, Offer, Request and Acknowledge
* DNS: Domain Name System
* DNSSEC: Domain Name System Security Extensions
* IPAM: IP Address Management
* MFA: Multi Factor Authentication
* RACI: Responsible, Accountable, Consulted and Informed
* SARR: Solicit, Advertise, Request and Reply
* SIEM: Security Information and Event Management
* SOAR: Security Orchestration, Automation and Response
* SVI: Switched Virtual Interface
* TTL: Time to Live
* VRF: Virtual Routing and Forwarding
