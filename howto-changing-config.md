---
copyright:
  years: 2019, 2021
lastupdated: "2021-11-23"

keywords: postgresql, databases, edb, enterprisedb

subcollection: databases-for-enterprisedb

---

{:external: .external target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Changing the {{site.data.keyword.databases-for-enterprisedb}} Configuration
{: #changing-configuration}

{{site.data.keyword.databases-for-enterprisedb_full}} is configurable to change some of the PosgreSQL settings so you can tune your {{site.data.keyword.databases-for-enterprisedb}} databases to your use-case. To make permanent changes to the database configuration, use the {{site.data.keyword.databases-for}} [cli-plugin](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-configuration) or [API](https://{DomainName}/apidocs/cloud-databases-api#change-your-database-configuration) to write the changes to the configuration file for your deployment.

The configuration is defined in a schema. To make a change, you send a JSON object with the settings and their new values to the API or the CLI. For example, to set the `max_connections` setting to 150, you would supply 
```shell
{"configuration":{"max_connections":150}}
```
to either the CLI or to the API.

## Using the CLI
{: #using-cli}

You can check the current configuration of your deployment with 
```shell
ibmcloud cdb deployment-configuration-schema <deployment name or CRN>
```

To change your configuration through the {{site.data.keyword.databases-for}} cli-plugin, use `deployment-configuration` command. 
```shell
ibmcloud cdb deployment-configuration <deployment name or CRN> [@JSON_FILE | JSON_STRING]
```

The command reads the changes that you would like to make from the JSON object or a file. For more information, see the [reference page](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-configuration).

## Using the API
{: #using-api}

Two deployment-configuration endpoints exist: one for viewing the configuration schema and one for changing the configuration. To view the configuration schema, send a `GET` request to `/deployments/{id}/configuration/schema`.

To change the configuration, send the settings that you would like to change as a JSON object in the request body of a `PATCH` request to `/deployments/{id}/configuration`.

For more information, see the [API Reference](https://cloud.ibm.com/apidocs/cloud-databases-api#change-your-database-configuration).


## Available Configuration settings
{: #avail-config-settings}

### Memory Settings
{: #mem-settings}

[`shared_buffers`](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-SHARED-BUFFERS)
- Default - `32000` (number of 8 KiB buffers, or about 262 MB) 
- Restarts database? - **Yes**
- Options - The maximum number of buffers is 1048576. 
- Notes - The setting specifies the number of 8 KiB shared memory buffers. For example, 1 GB of `shared_buffers` space is 1048576 KiB, and (1048576 KiB / 8 KiB) is 131072 buffers. The recommended memory allocation for `shared_buffers` is 25% of the deployment's RAM. Setting `shared_buffers` any higher can result in memory issues that cause the database to crash. Setting `shared_buffers` equal, close to equal, or higher than the amount of allocated memory prevents the database from starting. The maximum amount of total space for `shared_buffers` is 8 GB or 1048576 buffers based on recommendations from the PostgreSQL community. Your deployment can use more RAM for caching and performance, even without allocating it to `shared_buffers`. You do not have to configure the database to use all of the allocated RAM in order for your deployment to use it.

### General Settings
{: #gen-settings}

[`max_connections`](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-MAX-CONNECTIONS)
- Default - 115
- Restarts database? - **YES**
- Notes - [You might need to scale before you increase max connections.](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-high-availability#connection-limits-ha)

[`max_prepared_transactions`](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS)
- Default - 0
- Restarts database? - **YES**
- Notes - The default value of `0` disables use of [prepared transactions](https://www.postgresql.org/docs/current/sql-prepare-transaction.html) and is recommended to remain at the default unless you need to use prepared transactions.

[`synchronous_commit`](https://www.postgresql.org/docs/current/wal-async-commit.html)
- Default - `local`
- Restarts database? - No
- Options - `local` or `off`
- Notes - Setting `synchronous_commit` to `off` increases transaction commit rate at the expense of a loss of committed transactions if an unclean shutdown occurs.

[`effective_io_concurrency`](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-EFFECTIVE-IO-CONCURRENCY)
- Default - `12`
- Restarts database - No
- Notes - It is recommended to leave this setting at the default. Increase this setting only if you profiled SQL queries and observed inefficient bitmap heap scans. As [IOPS are tied to disk size](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-performance#disk-iops), increasing this setting on default or smaller sized disks is also not recommended.

[`deadlock_timeout`](https://www.postgresql.org/docs/current/runtime-config-locks.html)
- Default - 10000
- Restarts database - No
- Options - Minimum value of 100
- Notes - The number of milliseconds to wait before checking for deadlock and the duration where lock waits are logged. Logs available through the [logging integration](/docs/databases-for-enterprisedb?topic=cloud-databases-logging). Setting this number too low negatively impacts performance.

[`log_connections`](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CONNECTIONS)
- Default - `off`
- Restarts database - No
- Options - Values of `on` or `off` 
- Notes - Setting this value to `on` makes the logs very verbose. It also shows the connections of the monitoring tool as it extracts metrics every 60 seconds. When this is set to `on`, it is recommended to set the application_name in the connection URI to keep an overview in the logs, as the IP addresses shown are the Kubernetes internal IPs. Details about adjusting the connection URI are found in the [PostgreSQL documentation](https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNSTRING). When set to `off`, there is no change in behavior to the default setting and no connections are logged. Logs are available through the [logging integration](/docs/databases-for-postgresql?topic=cloud-databases-logging). If `on` is set, the logs show lines similar to this example, where the application name is set as `test-app`:

```shell
2021-03-01 10:27:56 UTC [[unknown]] [00000] [708]: [2-1] user=admin,db=ibmclouddb,client=127.0.0.1 LOG:  connection authorized: user=admin database=ibmclouddb application_name=test-app SSL enabled (protocol=TLSv1.3, cipher=TLS_AES_256_GCM_SHA384, bits=256, compression=off)
```

[`log_disconnections`](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DISCONNECTIONS)
- Default - `off`
- Restarts database - No
- Options - Values of `on` or `off` 
- Notes - Setting this value to `on` makes the logs very verbose. It also shows the disconnections of the monitoring tools as it extracts metrics every 60 seconds. When this is set to `on`, it is recommended to set the application_name in the connection URI to keep an overview in the logs, as the IP addresses shown are the Kubernetes internal IPs. Details about adjusting the connection URI are found in the [PostgreSQL documentation](https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNSTRING). When set to `off`, there is no change in behavior to the default setting and no disconnections are logged. Logs are available through the [logging integration](/docs/databases-for-postgresql?topic=cloud-databases-logging). If `on` is set, the logs show lines similar to this example where the application name is set as `test-app`:
    ```shell
    2021-03-01 10:27:56 UTC [test-app] [00000] [708]: [3-1] user=admin,db=ibmclouddb,client=127.0.0.1 LOG:  disconnection: session time: 0:00:00.793 user=admin database=ibmclouddb host=127.0.0.1 port=50638
    ```    

### WAL Settings
{: #wal-settings}

[`archive_timeout`](https://www.postgresql.org/docs/current/runtime-config-wal.html)
- Default - `1800`
- Restarts database - No
- Options - Minimum value of 300
- Notes - The number of seconds to wait before forcing a switch to the next WAL file. If the number of seconds passed and if there has been database activity, the server switches to a new segment. Effectively limits the amount of time data can remain unarchived.

[`log_min_duration_statement`](https://www.postgresql.org/docs/current/runtime-config-logging.html)
- Default - `100`
- Restarts database - No
- Options - Minimum value of 100
- Notes - Statements that take longer than the specified number of milliseconds are logged.

The next three settings `wal_level`, `max_replication_slots`, and `max_wal_senders` enable use of the [`wal2json` logical decoding plug-in](/docs/databases-for-postgresql?topic=databases-for-postgresql-wal2json). Anyone not using this plug-in should leave these settings at the default.

[`wal_level`](https://www.postgresql.org/docs/current/runtime-config-wal.html)
- Default - `hot_standby`
- Restarts database - **YES**
- Notes - Controls WAL level. Allowed values are `hot_standby` or `logical`. Set to logical to use logical decoding. If you are not using logical decoding, using `logical` increases the WAL size, which has several disadvantages and no real advantage. If you check your configuration by using `SHOW wal_level;` in `psql`, note: as of version 9.6 the value `hot_standby` is mapped to `replica`.

[`max_replication_slots`](https://www.postgresql.org/docs/current/runtime-config-replication.html)
- Default - `10`
- Restarts database - **YES**
- Notes - The maximum number of simultaneously defined replication slots. The default and minimum number of slots is 10. Twenty slots are reserved for internal use by your deployment for High-Availability (HA) purposes. To use slots, you need to set the value above 20 and have one slot per consumer. It is recommended to add one extra slot over the minimum per expected consumer. Using `wal2json` and not increasing `max_replication_slots` can impact HA and read-only replicas. If you are not using `wal2json`, you should leave this setting at the default.

[`max_wal_senders`](https://www.postgresql.org/docs/current/runtime-config-replication.html)
- Default - `12`
- Restarts database - **YES**
- Notes - The maximum number of simultaneously running WAL sender processes. The default and minimum is 12. One `wal_sender` per consumer is required.  Twenty slots are reserved for internal use by your deployment for High-Availability (HA) purposes. You need to set the value higher than 20 and it is recommended to add one extra `wal_sender` over the minimum per expected consumer. Using `wal2json` and not increasing `max_wal_senders` can impact HA and read-only replicas. If you are not using `wal2json`, you should leave this setting at the default.
