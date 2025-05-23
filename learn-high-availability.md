---

copyright:
  years: 2018, 2025
lastupdated: "2025-01-10"

keywords: postgresql, databases, connection limits, edb, enterprisedb

subcollection: databases-for-enterprisedb

---

{{site.data.keyword.attribute-definition-list}}

# High-Availability
{: #high-availability}

{{site.data.keyword.databases-for-enterprisedb}} is deprecated. As of 16 June 2025 you can't deploy new instances. Existing instances are supported until 15 October 2025. Any instances that still exist on that date will be deleted. For more information, see [Deprecation of {{site.data.keyword.databases-for-enterprisedb}}](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-deprecation).
{: deprecated}

{{site.data.keyword.databases-for-enterprisedb_full}} is a managed cloud database service that is fully integrated into the {{site.data.keyword.cloud_notm}} environment. The database, storage, and supporting infrastructure all run in {{site.data.keyword.cloud_notm}}.

{{site.data.keyword.databases-for-enterprisedb}} provides replication, fail-over, and high-availability features to protect your databases and data from infrastructure maintenance, upgrades, and failures. Deployments typically contain a cluster with three data members: a leader and two replicas. The replica members contain a copy of your data from the leader by using asynchronous replication, with a distributed consensus mechanism to maintain cluster state and handle failovers. If the leader becomes unreachable, the cluster initiates a failover and a replica is selected and promoted to leader. The replica rejoins the cluster and your cluster continues to operate normally, but with reduced capacity. 

With three members, {{site.data.keyword.databases-for-enterprisedb}} provides more protection against zonal outages and improves the safety of synchronous transactions. 

{{site.data.keyword.databases-for-enterprisedb}} will, at times, do controlled switchovers under normal operation. These switchovers are no-data-loss events that result in resets of active connections. There is a period of up to 15 seconds where reconnections can fail. At times, unplanned failovers might occur due to unforeseen events on the operating environment. These can take up to 45 seconds, but can be less. In both cases, potential exists for the downtime to be longer.

You can extend high-availability to more regions and spread to more replicas by adding [read-only replicas](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-read-only-replicas). 

## Synchronous replication
{: #sync-repl}

By default, streaming replication is asynchronous. If the primary server crashes then some transactions that were committed may not have been replicated to the standby server, causing data loss. {{site.data.keyword.databases-for}} ensures data loss is kept to a minimum substantial data loss; however, synchronous replication offers the ability to confirm that all changes made by a transaction have been transferred to a synchronous member, ensuring consistency across a cluster. This consistency come from confirming that writes are written to a secondary before returning to the connecting client with a success. For variables regarding synchronous replication, see [`synchronous_commit`](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-changing-configuration#general-settings) on the Changing Configuration page. 

Synchronous replication brings replica availability into the primary write path. If there is no replica to acknowledge the write, the write will hang until such time as a replica is available. This requires at least three members to function reliably, as synchronous replication is not supported on two-member deployments. You _must_ horizontally scale to at least three members before synchronous replication can be enabled.

While unlikely, it is possible that more than one replica may become unavailable simultaneously. If this happens, the primary database will not be able to complete any writes until a replica comes back online, effectively blocking all write traffic to your database. When deciding to use synchronous replication, you should weigh the relative costs and benefits of higher data durability versus potential availability issues.

Employing synchronous replication will negatively impact the performance of the database. Typically, a performant and effective way to employ this feature is by using it only on specific databases or users that must have the highest degree of data durability available.
{: .note}

## Application-level High-Availability
{: #app-level-high-availability}

Applications that communicate over networks and cloud services are subject to transient connection failures. You want to design your applications to retry connections when errors are caused by a temporary loss in connectivity to your deployment or to {{site.data.keyword.cloud_notm}}.

Because {{site.data.keyword.databases-for-enterprisedb}} is a managed service, regular updates and database maintenance occurs as part of normal operations. This can occasionally cause short intervals where your database is unavailable. It can also cause the database to trigger a graceful fail-over, retry, and reconnect. It takes a short time for the database to determine which member is a replica and which is the leader, so you might also see a short connection interruption. Failovers generally take less than 30 seconds.

Your applications must be designed to handle temporary interruptions to the database, implement error handling for failed database commands, and implement retry logic to recover from a temporary interruption.

Several minutes of database unavailability or connection interruption are not expected. Open a [support ticket](https://cloud.ibm.com/unifiedsupport/cases/add) with details if you have time periods longer than a minute with no connectivity so we can investigate.

## Connection Limits
{: #connection-limits-ha}

{{site.data.keyword.databases-for-enterprisedb}} sets the maximum number of connections to your database to **115**. 15 connections are reserved for the superuser to maintain the state and integrity of your database, and 100 connections are available for you and your applications. After the connection limit is reached, any attempts at starting a new connection results in an error. To prevent overwhelming your deployment with connections, use connection pooling, or scale your deployment and increase its connection limit. For more information, see the [Managing {{site.data.keyword.databases-for-enterprisedb}} Connections](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-managing-connections) page.

## High availability, disaster recovery, and SLA resources
{: #ha-dr-sla}

{{site.data.keyword.databases-for-enterprisedb}} deployments conform to the {{site.data.keyword.cloud_notm}} Databases [HA, DR, and SLA](/docs/cloud-databases?topic=cloud-databases-ha-dr) information and terms.
