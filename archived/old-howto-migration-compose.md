---
copyright:
  years: 2019, 2020
lastupdated: "2020-06-25"

keywords: postgresql, databases, compose

subcollection: databases-for-enterprisedb

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Migrating from a Compose PostgreSQL
{: #compose-migrating}

Current users of {{site.data.keyword.composeForPostgreSQL}} or Compose.com can migrate to {{site.data.keyword.databases-for-enterprisedb_full}} by using a {{site.data.keyword.databases-for-enterprisedb}} read-only replica.

The general process for a migration is to create an {{site.data.keyword.databases-for-enterprisedb}} read-only replica that is subscribed to a Compose Deployment as the source. Your data is copied from the Compose deployment and the replica. Once the initial copy is made, replication is established and any new changes are replicated from Compose over to the replica.

When you want to migrate and switch your applications to use {{site.data.keyword.databases-for-enterprisedb}}, disconnect your applications from the Compose deployment, promote the replica to a stand-alone {{site.data.keyword.databases-for-enterprisedb}} deployment, and reconnect your applications to {{site.data.keyword.databases-for-enterprisedb}}. 

Replication between Compose and {{site.data.keyword.databases-for-enterprisedb}} is set up to be a short-term operation only for the purposes of migration. It is not supported as a permanent replication strategy.

Downtime is limited to the window where you stop your applications that use the Compose deployment, promote the replica, and reconnect your applications to the new {{site.data.keyword.databases-for-enterprisedb}} deployment.

## Setting Up on Compose

You need to know the `admin` username and password of your Compose deployment. The replica subscribes to your Compose deployment with the `admin` user, so you provide the admin credentials to the replica when you provision it.

Next, you are going to want to take note of the size of your Compose deployment. You want to be able to provision a {{site.data.keyword.databases-for-enterprisedb}} replica with enough resources to accommodate your data and workload.

Pick one of the connection strings from your Compose deployment to have the replica connect to. You provide the hostname and port to the replica when you provision it.

Compose deployments support having only one read-only replica connected at a time. 

If you use whitelists to limit connections to your Compose deployment, you need to either disable the whitelist before you create the read-only replica or whitelist the range of IP addresses that your new {{site.data.keyword.databases-for-enterprisedb}} uses. Use the subnets listed on the [Whitelisting](/docs/databases-for-enterprisedb?topic=cloud-databases-whitelisting#whitelisting-cloud-databases-in-your-environment) page, and add all the ranges for the region where your {{site.data.keyword.databases-for-enterprisedb}} deployment lives to your Compose whitelist.

## Setting Up on IBM Cloud

First, if you don't already have one, you need to have an [{{site.data.keyword.cloud_notm}} account](https://cloud.ibm.com/registration){:new_window}. 

You are going to provision a {{site.data.keyword.databases-for-enterprisedb}} read-only replica by using either the Resource Controller API or CLI. Using the CLI is a little easier, so you can also install the [{{site.data.keyword.cloud_notm}} CLI](/docs/cli/reference/ibmcloud?topic=cloud-cli-getting-started). If you install the CLI from the cURL command that is provided, you get a selection of extra plug-ins and extensions for multiple IDEs. You can install just the stand-alone package from the [Installing the stand-alone IBM Cloud CLI](/docs/cli/reference/ibmcloud?topic=cloud-cli-install-ibmcloud-cli) page. 


## Provisioning the Read-only Replica

To provision the {{site.data.keyword.databases-for-enterprisedb}} deployment using the `ibmcloud` CLI, log in to your account with the `ibmcloud login` command. Provisioning of services is handled by the Resource Controller, so you use the
[`ibmcloud resource service-instance-create`](/docs/cli/reference/ibmcloud?topic=cloud-cli-ibmcloud_commands_resource#ibmcloud_resource_service_instance_create) command to provision {{site.data.keyword.databases-for-enterprisedb}}.
```
ibmcloud resource service-instance-create <your-new-deployment-name> databases-for-enterprisedb standard <region> \
-p '{
  "source_username":"admin",
  "source_password": "your-compose-admin-password",
  "source_host":"sl-us-south-1-portal.52.dblayer.com",
  "source_port":"25417",
  "version":"9.6",
  "members_memory_allocation_mb": "8192",
  "members_disk_allocation_mb": "20480"
 }'
```
**Required**  
- `<your-new-deployment-name>` - The name you want your {{site.data.keyword.databases-for-enterprisedb}} deployment to have.
- `<region>` - The region where you want your {{site.data.keyword.databases-for-enterprisedb}} deployment to live in.
- `source_username` - The `admin` user on the Compose deployment, so it should be `admin`.
- `password` - The `admin` password from the Compose deployment.
- `source_host` - The hostname of your Compose deployment.
- `source_port` - The port of your Compose deployment.
- `version` - The major version must match the major version of your Compose deployment. If the major version of your Compose deployment is not available for {{site.data.keyword.databases-for-enterprisedb}}, you must upgrade your Compose deployment before migrating.
 
**Optional**
- `members_memory_allocation_mb` - The total amount of memory you need for the {{site.data.keyword.databases-for-enterprisedb}} deployment. If omitted the minimum allocation of 4096 MB.
- `members_disk_allocation_mb` - The total amount of disk space you need for the {{site.data.keyword.databases-for-enterprisedb}} deployment. If omitted the minimum allocation of 10240 MB.

This command kicks off provisioning a {{site.data.keyword.databases-for-enterprisedb}} deployment that is configured as a [read-only replica](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-read-only-replica) of your Compose deployment. 


### Provisioning through the Resource Controller API

You can also provision by using the Resource Controller API. More information on the Resource Controller API is found in its [API Reference](https://cloud.ibm.com/apidocs/resource-controller#create-provision-a-new-resource-instance).

The provision request is a `POST` to the `https://resource-controller.cloud.ibm.com/v2/resource_instances` endpoint.

```
curl -X POST \
  https://resource-controller.cloud.ibm.com/v2/resource_instances \
  -H 'Authorization: Bearer <>' \
  -H 'Content-Type: application/json' \
    -d '{
    "name": "your-new-deployment-name",
    "target": "your-region",
    "resource_group": "your-resource-group-id",
    "resource_plan_id": "databases-for-enterprisedb-standard",
    "source_username":"admin",
    "source_password": "your-compose-admin-password",
    "source_host":"sl-us-south-1-portal.52.dblayer.com",
    "source_port":"25417"
    "version":"9.6",
    "members_memory_allocation_mb": "8192",
    "members_disk_allocation_mb": "20480"
    }'
```
The `target` is the region where you would like your {{site.data.keyword.databases-for-enterprisedb}} deployment to live in. The `resource_group` is the ID of resource group on your IBM Cloud account where you want your {{site.data.keyword.databases-for-enterprisedb}} deployment to live in.

## Performing the Migration

Once provisioning is finished, you have a {{site.data.keyword.databases-for-enterprisedb}} deployment that is configured as a [read-only replica](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-read-only-replicas) of your Compose deployment. Replication begins as soon as the provisioning is complete.

You can access the {{site.data.keyword.databases-for-enterprisedb}} deployment in a few different ways.
- The deployment's UI is accessible by clicking its name from the [Resource List](https://cloud.ibm.com/resources) in your IBM Cloud account.
- You can use the [Cloud Databases CLI Plugin](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference). The {{site.data.keyword.cloud_notm}} CLI tool is what you use to communicate with {{site.data.keyword.cloud_notm}} from your terminal or command line, and the plugin contains the commands that you use to communicate with your database deployments. 
- Or you can also use the [{{site.data.keyword.databases-for}} API](https://{DomainName}/apidocs/cloud-databases-api)

Most importantly, you can access the PostgreSQL database directly using `psql`, which can be used to monitor the replication status.

### Monitoring the Migration

The admin user from your Compose deployment can log in to and execute commands on the read-only replica. Connect to both the Compose deployment and the {{site.data.keyword.databases-for-enterprisedb}} replica using `psql` and the admin user.

On the Compose deployment run
```
SELECT pg_current_xlog_flush_location();
```

On the {{site.data.keyword.databases-for-enterprisedb}} replica run
```
SELECT pg_last_xlog_replay_location();
```

These commands output a PostgreSQL logical sequence number (`lsn`). If the two `lsn` match, the replica is caught up and synced to the Compose deployment. If the two `lsn` do not match, the replica still has to catch up. If you want to compare how far apart they are, you can run the following command on either member
```
SELECT pg_size_pretty(pg_xlog_location_diff('<Output_from_Compose>','<Output_from_replica>'));
```

The goal is to make sure that replication catches up after the initial subscription (which might take a while), but after that you want to check that replication is close to up-to-date. When the replication is in sync or close to synced, you can shut down your applications that are writing to the Compose database. Perform a last check to ensure that the replica is completely caught up and all your data is migrated over.

### Things to note on Compose
During the migration, the Compose deployment might scale. While the replica is subscribed to the deployment, transaction logs on the Compose deployment are kept to catch the replica up later. The extra logs might cause the Compose deployment to grow and scale. As the replication catches up, you might be able to scale the deployment back down.

### Things to note on the Replica
If you use the [Logging Integration](/docs/databases-for-enterprisedb?topic=cloud-databases-logging) to view logs on your {{site.data.keyword.databases-for-enterprisedb}} replica, you may see logs that contain
```
2019-11-13 22:02:00 UTC [1207]: [1-1] user=ibm,db=postgres,client=127.0.0.1 ERROR:  could not get commit timestamp data
2019-11-13 22:02:00 UTC [1207]: [2-1] user=ibm,db=postgres,client=127.0.0.1 HINT:  Make sure the configuration parameter "track_commit_timestamp" is set on the master server.
```
They can be safely ignored and no longer appear after the read-only replica is promoted.

### Troubleshooting a Migration

If replication is lagging and does not appear to catch up, check the following. On the Compose deployment, as the admin user, run
```
SELECT count(*) FROM pg_replication_slots WHERE slot_name = 'ibm_cloud_databases_migration';
```

If the command does not return a result, run the following command against Compose as the admin user.
```
SELECT pg_create_physical_replication_slot('ibm_cloud_databases_migration');
```

If replication does not start catching up, check the [logs](/docs/databases-for-enterprisedb?topic=cloud-databases-logging) on the {{site.data.keyword.databases-for-enterprisedb}} deployment for the following error message.
```
2019-11-25 17:04:16 UTC [296409]: [2-1] user=,db=,client= FATAL: could not receive data from WAL stream: ERROR: requested WAL segment 0000000C0000372C000000C5 has already been removed
```

If you see the message, {{site.data.keyword.databases-for-enterprisedb}} deployment will not sync back to the Compose deployment. You need to delete the {{site.data.keyword.databases-for-enterprisedb}} deployment, run the [cleanup](#cleaning-up) steps below, then create a new {{site.data.keyword.databases-for-enterprisedb}} read-only replica, starting the entire process again.

## Promoting the Read-only Replica

After the replication is complete, you need to promote the read-only replica into a stand-alone deployment. Promotion severs the connection between the replica and your source Compose deployment and they can not be rejoined. 

It is important promote the replica to a full {{site.data.keyword.databases-for-enterprisedb}} deployment promptly after the migration of your data is finished. The replication operation between Compose and {{site.data.keyword.databases-for-enterprisedb}} is not intended to be a long-term arrangement. Keep the window between creating the replica and promoting it as small as possible, ideally within a few days.

To get the {{site.data.keyword.databases-for-enterprisedb}} deployment up and minimize your downtime, check the **Skip Initial Backup** option, which makes the deployment available more quickly. You can start an on-demand backup after promotion is finished.

You can either perform the promotion by using the UI of your deployment, the promotion button is on the _Settings_ tab, in the _Replication_ section.

If you want to stick with using the CLI, you can use the [`ibmcloud cdb read-replica-promote`](https://cloud.ibm.com/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#read-replica-promote) command. 
```
ibmcloud cdb read-replica-promote <your-new-deployment-name> --skip-initial-backup
```

To promote through the API and skip the initial backup after the promotion, send a POST to the [`/deployments/{id}/remotes/promotion`](https://cloud.ibm.com/apidocs/cloud-databases-api#promote-read-only-replica-to-a-full-deployment) endpoint.
```
curl -X POST \
  https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/remotes/promotion \
  -H 'Authorization: Bearer <>'  \
 -H 'Content-Type: application/json' \
 -d '{"promotion": {"skip_initial_backup": true}}' \ 
 ```

After the promotion is complete, you can switch your applications to connect to your {{site.data.keyword.databases-for-enterprisedb}} deployment and get back up and running.

## Cleaning Up

On your new {{site.data.keyword.databases-for-enterprisedb}} deployment, certain PostgreSQL extensions might need to be updated. You can connect with `pqsl` and check which extensions are available and what versions you should update. Users of PostGIS, for example, should check its versions from the table,
```
SELECT name, version FROM pg_available_extension_versions; 
```
and then update the extension appropriately.
```
ALTER EXTENSION postgis UPDATE TO '2.4.6';
```

On the Compose deployment, you might have to perform a few actions post-migration to clean up the replication slots and log archive settings. This is especially true if the promotion fails, or if you create a replica and then delete it without promoting it.

1. Connect to the `template1` database on your Compose deployment as the `admin` user.
2. Run the following commands.
  - `SELECT pg_drop_replication_slot('ibm_cloud_databases_migration');` 
  - `DROP ROLE ibm;`
  - `SELECT public.set_wal_keep_segments(0);` (specifying 0 sets it back to the default of 16 internally)

You should also now be able to scale your Compose deployment back down to a pre-migration size if you experienced it growing during the migration.

You might want to keep the Compose deployment around for backup or disaster recovery concerns. 

After your applications are running smoothly on your {{site.data.keyword.databases-for-enterprisedb}} deployment, and you no longer need to keep the Compose deployment, it can be shut down.