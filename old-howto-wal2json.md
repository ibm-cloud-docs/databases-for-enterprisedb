---

Copyright:
  years: 2019, 2020
lastupdated: "2020-06-22"

keywords: postgresql, databases, wal2json

subcollection: databases-for-enterprisedb

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Configuring Wal2json
{: #wal2json}

{{site.data.keyword.databases-for-enterprisedb_full}} deployments support the [wal2json](https://github.com/eulerto/wal2json) plugin, enabling [logical decoding](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html) on your deployment. The plugin is already installed, but you do have to configure your deployment and databases in order to start using it.

1. First, you need to [configure](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-changing-configuration) the `wal_level`, `max_replication_slots`, and `max_wal_senders` settings. Change the `wal_level` to `logical`. The `max_replication_slots`, and `max_wal_senders` both need to be set to a value greater than 20. {{site.data.keyword.databases-for-enterprisedb}} reserves 20 replication slots and WAL senders for current and future operational purposes.
```
curl -X PATCH https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/configuration 
  -H 'Authorization: Bearer <>'
  -H 'Content-Type: application/json'
  -d '{"configuration": {
        "wal_level": "logical",
        "max_replication_slots": 21,
        "max_wal_senders": 21
        }
      }'
```

2. Set a password for the [`repl` user](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-user-management#the-repl-user). Any user's password can be changed by using the {{site.data.keyword.databases-for}} CLI plug-in [`cdb deployment-user-password`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-user-password) command or {{site.data.keyword.databases-for}} API [`/deployments/{id}/users/{username}`](https://cloud.ibm.com/apidocs/cloud-databases-api#set-database-level-user-s-password) endpoint. The `repl` user has REPLICATION privileges and the `wal2json` plugin uses it after you set a password for it.

3. Create a replication slot on the database from the {{site.data.keyword.databases-for}} API. Send a POST request to the [`/deployments/{id}/postgresql/logical_replication_slots`](https://cloud.ibm.com/apidocs/cloud-databases-api#create-a-new-logical-replication-slot) endpoint.
```
curl -X POST https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/postgresql/logical_replication_slots   -H 'Authorization: Bearer <>'
  -H 'Content-Type: application/json' 
  -d '{"logical_replication_slot": {
        "name": "<slot_name>",
        "database_name": "<database_name>",
        "plugin_type": "wal2json"
        }
      }'
```
The plugin type must be `wal2json`. The database must be an existing database. The slot name can only contain lower case letters, numbers, and the underscore character. You can check the existence of the replication slot by connecting to any database and running 
```
SELECT * FROM pg_replication_slots WHERE slot_name = '<slot_name>';
```

4. To test the plugin run `pg_recvlogical` from the terminal. The command is available with an installation of PostgreSQL. Use the host and port from your deployment, and the database and slot name you created via the API,
```
PGSSLMODE=require pg_recvlogical -d <DATABASE NAME> -U repl -h <HOST> -p <PORT> --slot <SLOT NAME> --start -o pretty-print=1 -f -
```

5. Create a table on `ibmclouddb` and insert some data. You should see that the inserts come out in the terminal that is running `pg_recvlogical`. Note that table creates do not appear.

## Wal2json Considerations and Tips

- Setting `wal_level` to `logical` increases the size of the WAL files because PostgreSQL needs more data to accomplish logical decoding. If you aren't using `wal2json`, you should leave `wal_level` at the default. Larger WAL files potentially mean that more disk space is required, there can be decrease in write throughput, there is greater potential for replication lag to affect HA and read-only replicas, and longer times to restore from a backup.

- Logical decoding has a set of restrictions on what replicates. Some of these include schema/DDL, sequences, TRUNCATE, and Large Objects.

- Logical replication slots do not sync between a primary (leader) and a replica. In the event of controlled switchover or failover, the slot is recreated on the new leader automatically, but it does not maintain the place in the replication stream that it was prior to switchover or failover. This can result in downstream consumers missing changes. Systems have to be able to detect when failover happens and and resync as needed.

- If you create a logical replication slot, and a consumer is not connected and consuming the changes, you run the risk of running your deployment out of disk space. The replication slot tells PostgreSQL to keep all the transaction logs that have the changes that the consumer needs. If nothing is consuming those changes, PostgreSQL continues collecting them until it is out of disk space. You can monitor disk space with the [monitoring integration](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-sysdig-monitoring). If you run out of space, you can [scale up disk](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-resources-scaling), which allows the database to start. Then, you can either start consuming the changes or drop the slot.

- You can check how much disk space is being used by a given replication slot and whether that replication slot has an active consumer. Use the `admin` user to run one of the following commands.  
**PostgreSQL 10.x and above**
```
SELECT slot_name, pg_size_pretty(pg_wal_lsn_diff(pg_current_wal_lsn(),restart_lsn)) AS lag, active from pg_replication_slots WHERE slot_type='logical';
```
**PostgreSQL 9.x**
```
SELECT slot_name, pg_size_pretty(pg_xlog_location_diff(pg_current_xlog_location(),restart_lsn)) AS lag, active FROM pg_replication_slots WHERE slot_type='logical';
```
Checking to see that your replication slot has a consumer and isn't running your deployment out of disk space can help troubleshoot if you see higher than expected disk usage on your deployment.