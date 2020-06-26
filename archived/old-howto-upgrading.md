---
copyright:
  years: 2020
lastupdated: "2020-06-25"

keyowrds: postgresql, databases, upgrading, major versions, changing versions

subcollection: databases-for-enterprisedb

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}


# Upgrading to a new Major Version
{: #upgrading}

Once a major version of a database is at its End Of Life (EOL), it is a good idea to upgrade to a current major version. 

You can find the available versions of PostgreSQL on the [{{site.data.keyword.databases-for-enterprisedb_full}} the catalog](https://cloud.ibm.com/catalog/databases-for-enterprisedb) page, from the cloud databases cli plugin command [`ibmcloud cdb deployables-show`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployables-show), or from the cloud databases API [`/deployables`](https://cloud.ibm.com/apidocs/cloud-databases-api#get-all-deployable-databases) endpoint.


## Backup/Restore Upgrade

One way to upgrade your database version is to [restore a backup](/docs/databases-for-enterprisedb?topic=cloud-databases-dashboard-backups#restoring-a-backup) of your data into a new deployment that is running the new database version.

### Upgrading in the UI

You can upgrade to a new version when [restoring a backup](/docs/databases-for-enterprisedb?topic=cloud-databases-dashboard-backups#restoring-a-backup) from the _Backups_ tab of your _Deployment Overview_. Clicking **Restore** on a backup brings up a dialog box where you can change some options for the new deployment. One of them is the database version, which is auto-populated with the versions available for you to upgrade to. Select a version and click **Restore** to start the provision and restore process.

### Upgrading through the CLI

When you upgrade and restore from backup through the  {{site.data.keyword.cloud_notm}} CLI, use the provisioning command from the resource controller.
```
ibmcloud resource service-instance-create <service-name> <service-id> <service-plan-id> <region>
```
The parameters `service-name`, `service-id`, `service-plan-id`, and `region` are all required. You also supply the `-p` with the version and backup ID parameters in a JSON object. The new deployment is automatically sized with the same disk and memory as the source deployment at the time of the backup.

```
ibmcloud resource service-instance-create example-upgrade databases-for-enterprisedb standard us-south \
-p \ '{
  "backup_id": "crn:v1:bluemix:public:databases-for-enterprisedb:us-south:a/54e8ffe85dcedf470db5b5ee6ac4a8d8:1b8f53db-fc2d-4e24-8470-f82b15c71717:backup:06392e97-df90-46d8-98e8-cb67e9e0a8e6",
  "version":11
}'
```

### Upgrading through the API

Similar to provisioning through the API, you need to complete [the necessary steps to use the resource controller API](/docs/databases-for-enterprisedb?topic=cloud-databases-provisioning#provisioning-through-the-resource-controller-api) before you can use it to upgrade from a backup. Then, send the API a POST request. The parameters `name`, `target`, `resource_group`, and `resource_plan_id` are all required. You also supply the version and backup ID. The new deployment has the same memory and disk allocation as the source deployment at the time of the backup.
```
curl -X POST \
  https://resource-controller.bluemix.net/v2/resource_instances \
  -H 'Authorization: Bearer <>' \
  -H 'Content-Type: application/json' \
    -d '{
    "name": "my-instance",
    "target": "bluemix-us-south",
    "resource_group": "5g9f447903254bb58972a2f3f5a4c711",
    "resource_plan_id": "databases-for-enterprisedb-standard",
    "backup_id": "crn:v1:bluemix:public:databases-for-enterprisedb:us-south:a/54e8ffe85dcedf470db5b5ee6ac4a8d8:1b8f53db-fc2d-4e24-8470-f82b15c71717:backup:06392e97-df90-46d8-98e8-cb67e9e0a8e6",
    "version":11
  }'
```

## Upgrading from a Read-only Replica

You can also upgrade by setting up a read-only replica. A quick summary: you provision a read-only replica with the same database version as your deployment, and wait while it replicates all of your data. Once your deployment and its replica are synced, you can promote and upgrade the read-only replica to a full, stand-alone deployment running the new version of the database.

The [Configuring Read-only Replicas](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-read-only-replicas) doc covers the provisioning, syncing, and other details for read-only replicas. To perform the upgrade and promotion step, use a POST to the [`/deployments/{id}/remotes/promotion`](https://cloud.ibm.com/apidocs/cloud-databases-api#promote-read-only-replica-to-a-full-deployment) endpoint with the version that you want to upgrade to in the body of the request.
```
curl -X POST \
  https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/remotes/promotion \
  -H 'Authorization: Bearer <>'  \
 -H 'Content-Type: application/json' \
 -d '{
    "promotion": {
        "version": "11",
        "skip_initial_backup": false
    }
}' \
```
`skip_initial_backup` is optional. If set to `true`, the new deployment does not take an initial backup after running the promotion. You're new deployment is available in a shorter amount of time, at the expense of not being backed up until the next automatic backup is run, or you take an on-demand backup.

### Dry-running the Upgrade

In order to evaluate the affects of upgrading, you can perform a dry-run. A dry-run does not perform the promotion and upgrade, but it does simulate it, with the results printed to the database logs. (You can access/view your database logs through the [Log Analysis Integration](/docs/databases-for-enterprisedb?topic=cloud-databases-logging)). This is mostly so you can ensure that the version you are currently running with it's extensions can be successfully upgraded to your desired version.

The dry-run can only be run with `skip_initial_backup` set to `false`, and `version` defined.
```
curl -X POST \
  https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/remotes/promotion \
  -H 'Authorization: Bearer <>'  \
 -H 'Content-Type: application/json' \
 -d '{
    "promotion": {
        "version": "11",
        "skip_initial_backup": false,
        "dry_run": true
    }
}' \
```

## Changelog for Major PostgreSQL Versions

- [PostgreSQL 10](https://www.postgresql.org/docs/10/release-10.html)
- [PostgreSQL 11](https://www.postgresql.org/docs/11/release-11.html)
- [PostgreSQL 12](https://www.postgresql.org/docs/current/release-12.html)