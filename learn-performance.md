---

copyright:
  years: 2020, 2021 
lastupdated: "2021-03-30"

keywords: postgresql, databases, monitoring, scaling, autoscaling, resources, connection limits, edb, enterprisedb

subcollection: databases-for-enterprisedb

---

{:external: .external target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Performance
{: #performance}

{{site.data.keyword.databases-for-enterprisedb_full}} deployments can be both manually [scaled to your usage](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-resources-scaling), or configured to [autoscale](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-autoscaling) under certain resource conditions. There are a few factors to consider if you are tuning the performance of your deployment.

## Monitoring your deployment

{{site.data.keyword.databases-for-enterprisedb}} deployments offer an integration with the [{{site.data.keyword.monitoringfull}} service](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-monitoring) for basic monitoring of resource usage on your deployment. Many of the available metrics, like disk usage and IOPS, are presented to help you configure [autoscaling](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-autoscaling) on your deployment. Observing trends in your usage and configuring the autoscaling to respond to them can help alleviate performance problems before your databases become unstable due to resource exhaustion.

## Disk IOPS

The number of Input-Output Operations Per Second (IOPS) is limited by the type of storage volume. Storage volumes for {{site.data.keyword.databases-for-enterprisedb}} deployments are provisioned on [Block Storage Endurance Volumes in the 10 IOPS per GB tier](/docs/BlockStorage?topic=BlockStorage-orderingthroughConsole#orderingthroughConsoleEndurance). If your operational load saturates or exceeds the IOPS limit, database requests and operations are delayed until the disk can catch up. Extended periods of heavy-load can cause your deployment to be unable to process queries and become effectively unavailable. If you experience delayed responses and failing operations, you might be exceeding the disk's IOPS limit. You can increase the number IOPS available to your deployment by increasing disk space.

## Memory Usage

{{site.data.keyword.databases-for-enterprisedb}} deployment's memory settings are auto-tuned based on the deployment's total memory. Specifically, [`work_mem`](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM), [`maintenance_work_mem`](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM), and [`effective_cache_size`](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) are set on provision, restore, or scale. 

You can set the amount of memory that is dedicated to the databases' shared buffer pool by adjusting the [`shared_buffers`](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-SHARED-BUFFERS) in your [{{site.data.keyword.databases-for-enterprisedb}} configuration](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-changing-configuration). The recommended value to use is 25% of the deployment's total memory. Allocating too much to the shared buffer pool can starve the system of memory for other purposes and hurts performance (or crash the database). The maximum size of the shared buffer pool is 8 GB, based on recommendations of the PostgreSQL community.

Allocating larger amounts of memory (outside of the shared buffer pool) to your deployment still benefits performance. For example, {{site.data.keyword.databases-for-enterprisedb}} fills memory with cached disk pages for performance. You don't have to allocate memory to {{site.data.keyword.databases-for-enterprisedb}} directly for {{site.data.keyword.databases-for-enterprisedb}} to use it.

## Connection Limits 
{: #connection-limits-performance}

{{site.data.keyword.databases-for-enterprisedb}} sets the maximum number of connections to your database to **115**. 15 connections are reserved for the superuser to maintain the state and integrity of your database, and 100 connections are available for you and your applications. After the connection limit has been reached, any attempts at starting a new connection results in an error. To prevent overwhelming your deployment with connections, use connection pooling, or scale your deployment and increase its connection limit. For more information, see the [Managing {{site.data.keyword.databases-for-enterprisedb}} Connections](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-managing-connections) page.