# Motivation

Migrating DNS, DHCP and IPAM services is a complex and critical process that requires meticulous planning and execution. This guide provides a comprehensive overview and detailed steps to ensure a successful migration with minimal disruption to services.

The migration plan outlines a high-level approach to transitioning from existing DNS and DHCP systems to a new solution. It includes preparation, data gathering, processing migration data, data validation, service impact analysis and migration execution. A strong focus is placed on minimizing downtime and maintaining service integrity.

*Key Steps:*
-   Preparation: Establishing project scope, team roles and migration strategy.
-   Data Gathering: Collecting DNS, DHCP and IPAM data, incl. configuration and usage metrics.
-   Data Processing: Validating data integrity, identifying conflicts and preparing the import process.
-   Service Impact Analysis: Assessing potential risks and formulating mitigation strategies.
-   Migration Execution: Implementing the transition, conducting cutovers and verifying service functionality.
-   Post-Migration Validation: Conducting tests to ensure operational readiness and monitoring the environment.
-   Early Life Support: Offering direct support and monitoring during the stabilization period.

# Table of Contents
<details>
  <summary>Table of Contents</summary>
  
- [Prerequisites](#prerequisites)
  - [Kick-off Meeting](#kick-off-meeting)
  - [License Keys](#license-keys)
  - [Systems to be built](#systems-to-be-built)
  - [Systems to be dismantled](#systems-to-be-dismantled)
  - [Accessibility](#accessibility)
  - [Site Survey](#site-survey)
  - [IP Address Ranges](#ip-address-ranges)
  - [DHCP Relays](#dhcp-relays)
  - [Firewall Activation](#firewall-activation)
  - [DHCP Lease Time](#dhcp-lease-time)
  - [DDI Exports](#ddi-exports)
  - [Data Preparation](#data-preparation)
  - [Jump Server](#jump-server)
- [Preparation](#preparation)
  - [Follow-up Meeting](#follow-up-meeting)
  - [Minimize DHCP Lease Time](#minimize-dhcp-lease-time)
  - [Connectivity and Communication](#connectivity-and-communication)
  - [Analysis of Relay Configuration](#analysis-of-relay-configuration)
  - [Adaption of DHCP Relays](#adaption-of-dhcp-relays)
  - [Health Check of new Environment](#health-check-of-new-environment)
  - [Alignment of Networks](#alignment-of-networks)
- [Migration](#migration)
  - [Safeguarding DNS/DHCP/IPAM Data](#safeguarding-dnsdhcpipam-data)
  - [Import of DNS/DHCP Data](#import-of-dnsdhcp-data)
  - [DNS Cutover](#dns-cutover)
  - [DHCP Cutover](#dhcp-cutover)
  - [Static Network Components](#static-network-components)
  - [Baseline Test](#baseline-test)
    - [DNS/DHCP Servers](#dnsdhcp-servers)
    - [DNS/DHCP Clients](#dnsdhcp-clients)
    - [IP Address Database](#ip-address-database)
- [Post-Migration](#post-migration)
  - [Post-Migration Checkpoint](#post-migration-checkpoint)
  - [Post-Migration Tasks](#post-migration-tasks)
  - [Early Life Support](#early-life-support)
- [Appendix](#appendix)
  - [Kick-off Meeting Agenda](#kick-off-meeting-agenda)
  - [Environmental Analysis](#environmental-analysis)
    - [Overview](#overview)
    - [Internal DNS](#internal-dns)
      - [DNS Servers](#dns-servers)
      - [Active Directory](#active-directory)
      - [Dynamic DNS](#dynamic-dns)
      - [Further DNS Topics](#further-dns-topics)
    - [DHCP](#dhcp)
      - [General DHCP Topics](#general-dhcp-topics)
      - [Further DHCP Topics](#further-dhcp-topics)
    - [IP Address Management](#ip-address-management)
  - [Fundamental Firewall Concept](#fundamental-firewall-concept)
  - [export_ms-dns-dhcp.ps1](#export_ms-dns-dhcpps1)
  - [Domain Controller Commands](#domain-controller-commands)
    - [Synchronization](#synchronization)
    - [Registration](#registration)
    - [Replication](#replication)
  - [Generic Fall-back Plan](#generic-fall-back-plan)

</details>

# Prerequisites

## Kick-off Meeting

The kick-off meeting marks the official start of the project. During this session, the environment is qualified, business and technical requirements are gathered and both go-live and rollback strategies are determined. Additionally, relevant data is collected and an initial data analysis is performed to establish a solid foundation for project implementation.

## License Keys

-   ensure that license keys are available on the first day of migration process

## Systems to be built

The following details have to be specified and configured for all DNS, DHCP and IPAM systems to be built four weeks before the migration. The DDI engineer can assist in this task.
-   Hostname
-   IP/CIDR
-   Gateway
-   Name Servers
-   Domain Name
-   Search List
-   NTP Server
-   Time Zone
-   SNMP Credentials
-   Syslog/SIEM Configuration (optional)
-   Password Storage
    -   `admin` Account
    -   `root` Account
-   fundamental configuration in UI
    -   add and connect
    -   update and patch
-   basic function tests

## Systems to be dismantled

The DDI engineer receives DNS and DHCP exports of the legacy systems.
-   DHCP servers (`<hostname>,<address>,<Location>,etc.`)
-   DNS servers (`<hostname>,<address>,<Location>,etc.`)

## Accessibility

The DDI engineer needs access to the following resources.
-   IP address management database to be built
    -   web interface (user account with administrator access)
    -   console (root access to SSH console)
-   DNS/DHCP systems to be built
    -   console (root access to SSH console)

## Site Survey

-   collection of feedback from affected departments (users, operations, maintenance, etc.)

-   special applications and devices identified (`t-minus 4 weeks`)
    -   IP phones, PXE clients, access points, wireless LAN controllers, print servers, barcode scanners, VPN concentrators, etc.
    -   software-as-a-service applications (Office 365, MS Teams, etc.)
    -   custom applications (internal servers, web interfaces, etc.)
    -   network-attached storage (network drive, file share, SharePoint, etc.)
    -   statically configured devices (servers, printers, special equipment, etc.)

## IP Address Ranges

Identification of required IP address ranges in relation to:

-   assign deployment roles and deployment options for IP block(s)
-   identify VPN networks for ACLs in DNS and firewalls
-   specify firewall rules for local services (e.g. new DHCP servers and dynamic DNS updates)

## DHCP Relays

-   existing and future DHCP relay addresses (IP helper addresses) specified (`t-minus 3 weeks`)
    -   existing relays: systems to be dismantled
    -   future relays: systems to be built

## Firewall Activation

-   firewall activations specified and configured (`t-minus 3 weeks`)

## DHCP Lease Time

-   DHCP lease time of all scopes reduced to 1 day (`t-minus 1 week`)
-   1 day before the migration date the DHCP lease time reduced to 1 hour

## DDI Exports

-   exports of configuration files made available (`t-minus 1 week`)
-   post-export changes documented for manual addition after migration
-   DNS exports
    -   configuration (e.g. `named.conf`)
    -   zone content (e.g. zone files)
    -   extensions (e.g. include files)
-   DHCP exports
    -   configuration (e.g. `dhcpd.conf`)
    -   extensions (e.g. include files)
    -   active leases (e.g. `dhcpd.leases`)
-   IPAM exports
    -   customer-specific meta data (network ranges, locations, VLANs, etc.)
-   PowerShell script to export DNS and DHCP configurations from Microsoft servers

## Data Preparation

-   evaluating exported data (quantity and quality)
-   initial data "dry run" for short feedback
-   check DHCP configuration
    -   option spaces, client classes, custom options, vendor-specific options, etc.
-   check DNS configuration
    -   global forwarding, forwarding/stub zones, secondary zones, primary zones, etc.
-   identify and implement groupings in DNS and DHCP
    -   DHCP failover associations (prepare, but start off with single server)
    -   DNS server groups (grouping of primary/secondary etc.)
-   processing migration data
    -   loading data into migration tool (parsing/browsing)
    -   transforming of migration data (if required)
    -   finalization of migration data (processing)
-   lab migration of data and verification of objects
-   definition of global DHCP options
-   definition of global DNS options

## Jump Server

A host within the network is required for various tests and data matching, on which scripts such as `dnsdiff` can be executed.

-   Linux with GUI (e.g. Debian, Ubuntu, etc.)
-   static IP address
-   Browser (e.g. Firefox, Chrome)
-   Perl and CPAN (latest stable version)
-   Python (latest stable version)
-   bind-utils
-   ldns-compare-zones
-   dos2unix
-   ssconvert (gnumeric)
-   direct Internet access
-   packet manager repository

# Preparation

## Follow-up Meeting

-   review status of systems to be built
-   review status of accessibility
-   review collection of site survey
-   review collection of address ranges
-   review collection of DHCP relays
-   review adaptation of firewalls
-   review anomalies from lab migration
-   agreement on current status of the preparations and open questions (`t-minus 1 week`)

## Minimize DHCP Lease Time

-   DHCP lease time be reduced to 1 hour (`t-minus 1 day`)

## Connectivity and Communication

-   connectivity between all components verified

## Analysis of Relay Configuration

-   DDI engineer provides list for network operations to change DHCP relays

## Adaption of DHCP Relays

-   add systems to be built to DHCP relays

## Health Check of new Environment

-   connectivity (name servers are responding to DNS queries)
-   performance (response time)
-   resilience (multiple name servers configured for resiliency)
-   name server's `A/AAAA/PTR/NS` records
-   recursive queries

## Alignment of Networks

-   networks found in DHCP export
-   networks configured on DHCP replays
-   networks documented in IP address database

# Migration

## Safeguarding DNS/DHCP/IPAM Data

-   backup DNS data
-   backup DHCP data
-   backup IPAM data
-   disable scheduled processes

## Import of DNS/DHCP Data

-   upload migration file(s) to IP address database
-   validate migration log

## DNS Cutover

-   collect zone transfers from systems to be dismantled for comparison
-   deploy DNS to systems to be built and collect zone transfers for comparison
-   compare legacy AXFR with latest AXFR
-   delete DNS data on systems to be dismantled and configure systems to be built for global forwarding
-   in case of domain controllers:
    -   synchronize with all replication partners
    -   register in DNS
-   configure systems to be built as resolver of systems to be dismantled

## DHCP Cutover

-   disable service on systems to be dismantled
-   deploy DHCP to systems to be built

## Static Network Components

-   adapt statically configured devices by way of example

## Baseline Test

#### DNS/DHCP Servers

-   named started successfully
    -   look for proper number of forward/reverse zones
    -   confirm all zones were deployed
    -   look for zone transfer status (running/deferred)
    -   validate authority (SOA, NS) and glue records (`A, AAAA, PTR`)
    -   confirm recursion is configured as expected
-   dhcpd started successfully
    -   confirm dhcpd.conf is deployed as expected
    -   look for proper number of subnets, pools and reservations
-   DHCP failover established successfully and balancing
-   first clients successfully completing DORA/SARR process
-   DDNS entries being successfully created

#### DNS/DHCP Clients

-   IP address assignment in general
-   assignment of required DHCP options (new DNS server IPs, domain, NTP, etc.)
-   assignment of special DHCP options (VoIP, PXE, etc.)
-   switch DNS resolver addresses of static clients to systems to be built
    -   adapt statically configured devices by way of example

-   basic client tests
    -   Active Directory (AD) log-on
    -   access via VPN for remote workers
    -   access to mail exchange (Outlook)
    -   special equipment according to site survey
        -   access to software-as-a-service applications
        -   access to special application
        -   access to network-attached storage
        -   statically configured devices

#### IP Address Database

-   notifications from DNS/DHCP servers synchronized with IP address database
    -   verify notifications are not queued up on DNS/DHCP server
    -   verify notification processing is not backed up on IP address database
-   review user interface of IP address database for DDNS entries
    -   ensure IP address is leased
    -   ensure DNS entry is in IP address database

# Post-Migration

## Post-Migration Checkpoint

-   environment has been tested according to the site survey
-   members of all responsible teams and affected departments arrived at a decision on fall-back procedures if required.

## Post-Migration Tasks

-   clean-up of DHCP environment
    -   set lease time to operational value
    -   enable update optimization for DDNS
    -   enable update conflict detection for DDNS
    -   remove legacy DHCP relay configuration
-   configure scheduled deployments for DNS/DHCP if any
-   reset preparation of IP address database
    -   enable scheduled backups (if disabled)
    -   enable scheduled deployments (if disabled)
    -   unlock users (if done)
    -   run backup of stable system
-   adapt remaining statically configured devices with new DNS servers
-   close-out meeting (final report to all responsible teams and affected departments)

## Early Life Support

To ensure the stability and performance of the newly migrated system, closely monitoring operations and promptly addressing any issues that arise (`t-plus 1 week`).

-   service validation and monitoring
-   monitor situation in live operation
-   test end-user devices in live operation
-   perform troubleshooting (if necessary)
-   provide direct support by all responsible teams

# Appendix

## Kick-off Meeting Agenda

General Agenda for Global DDI Solution Kickoff Meeting.
-   Welcome and Introduction
    -   Welcome by the Project Manager
    -   Introduction of the Project Team
-   Overview of the Project
    -   Recap of existing DNS and DHCP Challenges
    -   Presentation of the detailed Concept Document
-   DDI Solution Overview
    -   Key Features and Benefits of the new DDI Solution
-   Implementation Plan
    -   Global Rollout Plan
    -   Timeline and Key Milestones Discussion
-   Technical Overview
    -   Technical Aspects of the new DDI Solution
    -   Integration with existing Infrastructure
-   Risk Assessment and Mitigation
    -   Potential Risks and Challenges
    -   Risk Mitigation Strategies
-   Training and Support
    -   Training Plan for Staff of Customer
    -   Support Services provided by Provider
-   Q&A Session
    -   Open Floor for Questions and Answers
-   Next Steps
    -   Discussion on the next Steps and Actions required from each Team
    -   Setting the Dates and Agenda for upcoming Meetings
-   Wrap-up and Closing Remarks
    -   Recap of Key Points discussed during the Meeting
    -   Closing Remarks by the Project Manager

## Environmental Analysis

### Overview
-   Project Scope
-   Location Sketch
-   Classification of Locations
-   Connectivity between Locations

### Internal DNS

#### DNS Servers

-   DNS Servers and Versions
-   Deployment and Distribution of DNS Servers
-   Mission-critical Authoritative Zones (Forward & Reverse)
-   Distribution of DNS Zones
-   DNS Structure (Hierarchy, Forwarding, Secondary Servers)
-   External DNS Relationships (Root Server, External Secondary Servers)
-   DNS Views
-   Resolution of Internet Hostnames

#### Active Directory

-   AD-integrated Zones (excluding underscore Domains)
-   AD Domain Structure

#### Dynamic DNS

-   AD-integrated DDNS
-   Other DDNS Concepts

#### Further DNS Topics

-   Windows Internet Naming Service (WINS)
-   Concept of DNS Names
-   Signed DNS Zones (Zones with DNS Security Extensions)
-   DNS Requirements for IPv6
-   DNS Resilience Concept
-   DNS Test Environment

### DHCP

#### General DHCP Topics

-   DHCP Servers and Versions
-   Deployment and Distribution of DHCP Servers
-   Distribution of Networks
-   Number of DHCP Networks (Scopes, Ranges, Pools per Server)
-   Quantity DHCP Clients
-   Special DHCP Options

#### Further DHCP Topics

-   DHCPv6
-   MAC Authentication
-   DHCP Resilience Concept
-   DHCP Test Environment

### IP Address Management

-   Current Address Management System
-   Format for Data Migration
-   Network Details (Address Conflicts)
-   Integration towards IP Address Database

## Fundamental Firewall Concept

-   systems to be dismantled <-> systems to be built
-   VPN networks <-> systems to be built
-   client networks <-> systems to be built

## [export_ms-dns-dhcp.ps1](https://github.com/ataudte/ddi-helpers/tree/main/export_ms-dns-dhcp)

-   Windows Server (2016/2019 or newer) with DNS/DHCP roles
-   PowerShell 5.0 or later
-   Administrator Privileges
-   Execution Policy set to `RemoteSigned`
-   Backup DNS/DHCP Settings before running the Script
-   Open PowerShell as Administrator and execute the Script
-   Files are saved in `C:\DDI\_Output`
-   Ensure Output includes Config Files and ZIP Archive
-   Check Logs for Errors, ensure Admin Rights and verify Services
-   Test on a non-production Server first

``` 
Set-ExecutionPolicy RemoteSigned
```

``` 
powershell -ExecutionPolicy Bypass -File "C:\path\to\export_ms-dns-dhcp.ps1"
```

## Domain Controller Commands

### Synchronization

``` 
repadmin /syncall /APed
```

-   `/A` synchronizes all naming contexts that are held on the home server
-   `/P` pushes changes outward from the specified domain controller
-   `/e` synchronizes domain controllers across all sites in the enterprise
-   `/d` identifies servers by distinguished name in messages

### Registration

```
net stop netlogon
net start netlogon
ipconfig /registerdns
dcdiag /test:DNS /DnsBasic
dcdiag /test:DNS /DnsRecordRegistration
```

### Replication

Depending on the scope of an Active Directory environment, it may be necessary to deactivate replication for a short time.

```
repadmin /options <DC-NAME> +DISABLE_OUTBOUND_REPL
repadmin /options <DC-NAME> +DISABLE_INBOUND_REPL
```

```
repadmin /options <DC-NAME> -DISABLE_OUTBOUND_REPL
repadmin /options <DC-NAME> -DISABLE_INBOUND_REPL
```

## Generic Fall-back Plan

-   remove DHCP roles on appliances
-   activate DHCP on legacy servers
-   roll back DHCP replay IP addresses
-   remove DNS roles on appliances
-   restore backed DNS zone files
-   reset global forwarding on legacy DNS to previous value
