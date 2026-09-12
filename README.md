# RVTools

Download latest version from Releases:       
https://github.com/rvtlix/RVTools/releases/tag/4.7.1

## Introduction

RVTools is a Windows-based utility for collecting and analyzing configuration and operational information from VMware vSphere environments. It connects to a vCenter Server or an individual ESXi host and retrieves inventory data through the VMware management interface. The collected information is organized into specialized tabular views covering virtual machines, CPU and memory allocation, virtual disks, guest partitions, networking, snapshots, VMware Tools, resource pools, clusters, hosts, storage adapters, virtual switches, datastores, multipathing, and health conditions.

The tool is designed primarily for inspection, auditing, troubleshooting, and reporting rather than as a general-purpose replacement for vSphere administration interfaces. Its tab structure makes it possible to correlate configuration data across different infrastructure layers. For example, an administrator can move from a VM's virtual disk configuration to the corresponding datastore, or from a host's physical adapters to its virtual networking configuration.

RVTools can expose both static configuration and runtime information. Depending on the selected view, this includes power and connection states, guest heartbeat, CPU and memory consumption, resource reservations and limits, storage utilization, network assignments, VMware Tools status, HA settings, EVC information, and hardware characteristics.

The application also supports filtering, automatic refresh, configurable display options, health checks, and export operations. Data can be written to Excel workbooks or individual CSV files, making the output suitable for audits, capacity reviews, migration planning, troubleshooting, and recurring operational reports.

Because some views can generate substantial amounts of vSphere API data, administrators should enable detailed fields selectively. Options such as folder and vApp information can increase collection time significantly in large environments.

## Virtual Machine Configuration and Resource Analysis

The VM-related views provide a detailed inventory that can be used to analyze workload configuration without opening each virtual machine individually. The vInfo view combines identity, placement, state, storage, availability, and protection information. Useful fields include power and connection state, guest operating system state, heartbeat, consolidation status, CPU and memory configuration, network and disk counts, datacenter, cluster, host, resource pool, provisioned storage, used storage, unshared storage, UUID, hardware version, firmware type, and custom fields.

Connection state deserves particular attention during audits. A VM may be connected, disconnected, inaccessible, invalid, or orphaned. An inaccessible configuration can indicate a storage problem, while an orphaned VM can indicate that the registration state no longer matches the host configuration. The consolidation flag is also operationally important because it can indicate that snapshot-related disk changes still require consolidation.

The vCPU and vMemory views expose resource controls such as shares, reservations, limits, static entitlement, and DRS entitlement. These values should be interpreted together. A CPU limit, for example, can restrict a VM even when unused host capacity is available, while reservations guarantee resources but do not mean those resources remain permanently unused. Memory data includes consumed, active, ballooned, and swapped memory. Ballooning and swapping are useful indicators of host memory pressure and should be investigated alongside host-level utilization.

The vDisk view adds storage-specific configuration, including thin provisioning, disk mode, VMDK path, SCSI controller, RDM information, and storage I/O allocation settings. The vPartition view complements this information by showing guest filesystem free space when VMware Tools is active.

## Infrastructure, Storage, and Network Validation

RVTools provides infrastructure-level views that allow administrators to validate the relationships between ESXi hosts, clusters, networks, and datastores. The host view includes CPU model and core information, CPU and memory utilization, VM density, vCPU-to-core ratios, virtual RAM allocation, VM memory usage, ballooning and swapping, vMotion and Storage vMotion capability, EVC information, ESXi version, boot time, DNS configuration, NTP servers, hardware vendor, model, service tag, BIOS version, and other host properties.

These fields are useful during cluster reviews and migration planning. For example, different EVC capabilities or ESXi versions can explain why a workload cannot be moved between particular hosts. High vCPU density combined with host memory ballooning or swapping provides a stronger indication of resource pressure than VM allocation alone.

Storage analysis is handled through the vDatastore and vMultipath views. A datastore report includes accessibility, filesystem type, VM count, capacity, provisioned space, used space, free capacity, connected hosts, SIOC status, and related storage properties. Provisioned capacity should not be confused with physical consumption: thin-provisioned workloads can have substantially more virtual capacity assigned than actual datastore usage. An inaccessible datastore should also be treated differently from one with low free space because its capacity information may no longer be reliable.

The multipath view helps identify degraded storage connectivity. Individual paths can be active, standby, disabled, dead, or unknown. A degraded LUN may still service I/O while already operating with reduced redundancy, so path state should be checked before maintenance or storage changes.

Network views expose virtual NICs, switches, port groups, distributed switches, ports, and VMkernel or service-console interfaces. Reviewing VLAN assignments, uplinks, IP configuration, DHCP state, gateways, and physical adapter relationships can help detect inconsistent host networking before it affects vMotion, management, or storage traffic.

## Health Checks and Operational Troubleshooting

The vHealth view converts selected configuration and infrastructure conditions into actionable health messages. It can identify connected CD-ROM or floppy devices, active snapshots, VMware Tools that are missing, stopped, or outdated, low free space on virtual disks or datastores, excessive virtual CPU density, high VM counts per datastore, possible zombie VMDK files, possible zombie VMs, inconsistent folder names, multipath problems, consolidation requirements, and datastore errors.

Health thresholds are configurable, so the same dataset can be evaluated according to the operational requirements of a particular environment. For example, an administrator can define a minimum free-space threshold for datastores and guest disks and then use the resulting health report as a first-pass exception list. Threshold-based checks are more useful when they reflect actual capacity and performance policies rather than arbitrary values.

The snapshot check should be used together with vSnapshot. A health warning only establishes that a snapshot exists; the snapshot view provides its creation time, description, filename, total snapshot size, memory-state size, quiesced state, and VM state at creation. This information allows an administrator to distinguish a recent operational snapshot from one that has remained in the environment unnecessarily.

Multipath warnings require particular attention because a degraded path does not necessarily mean that the datastore is already unavailable. It indicates reduced path redundancy and a potential failure condition. The administrator should correlate the health message with the detailed path states in vMultipath.

Guest disk warnings can be investigated through vPartition, while storage capacity warnings can be correlated with vDatastore and vDisk. This layered approach prevents treating a symptom as the root cause. For example, low datastore free space may result from large thin-provisioned disks, active snapshots, or abandoned virtual disks rather than from normal VM growth.

## Automated Export and Recurring Reporting

RVTools can be integrated into operational reporting workflows through its command-line interface. It supports connection to a specified vCenter Server or ESXi host, authentication using supplied credentials or Windows pass-through authentication, and automated export of collected information. This makes it possible to run repeatable inventory jobs without manually opening the graphical interface.

The complete dataset can be exported to an Excel workbook, while individual views can be exported separately. Examples include vInfo for VM inventory, vCPU and vMemory for resource analysis, vDisk for storage configuration, vNetwork for connectivity information, vDatastore for capacity reporting, vMultiPath for storage path validation, and vHealth for exception reporting. Individual CSV exports are particularly useful when the output is consumed by PowerShell, spreadsheet processing, or other automation.

A practical scheduled workflow is to authenticate with pass-through credentials, connect to the required vCenter Server, export all views to a timestamped workbook, and place the result in a controlled reporting directory. A separate process can archive the file or distribute it to an operations mailbox. For security, credential-based automation should avoid embedding reusable passwords directly in batch files whenever pass-through authentication is suitable.

Filtering can also be saved so that recurring interactive sessions start with the required selection criteria. Automatic refresh can be configured when the environment needs continuously updated information during an investigation.

RVTools can generate diagnostic logs by enabling debug logging in the logging settings. This feature is disabled by default because it can affect performance, so it is recommended to activate it only during troubleshooting activities that require additional insight. For large-scale environments, administrators should limit the use of costly display options, particularly folder and vApp-related fields, as collecting this information may extend the overall collection process.
