---

copyright:
  years: 2020, 2025
lastupdated: 2025-01-17

keywords: deployment, crn, task, gui, api endpoint, edb, enterprisedb

subcollection: databases-for-enterprisedb

---

{{site.data.keyword.attribute-definition-list}}

# The Dashboard Overview
{: #dashboard-overview}

{{site.data.keyword.databases-for-enterprisedb}} is deprecated. As of 16 June 2025 you can't deploy new instances. Existing instances are supported until 15 October 2025. Any instances that still exist on that date will be deleted. For more information, see [Deprecation of {{site.data.keyword.databases-for-enterprisedb}}](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-deprecation).
{: deprecated}

The _Overview_ page shows you information about your {{site.data.keyword.databases-for-enterprisedb_full}} deployment. The overview includes essential identifying information.

## Overview
{: #dashboard-overview-overview}

### Type
{: #dashboard-overview-type}

The type of database that is offered by the service, and the database version that your service uses.

### ID
{: #dashboard-overview-id}

The ID is a [CRN (Cloud Resource Name)](/docs/account?topic=account-crn) which uniquely identifies the database deployment. The CRN is used to refer to the database in the API and can be used with the CLI.The _Overview_ pane shows details of your service.

### Recent Tasks
{: #dashboard-overview-recent-tasks}

Every time that you make administrative changes to your service (such as scaling, or taking a manual backup), a task starts up. The _Recent Tasks_ panel shows the task name and progress bar for any running tasks, and a list of the most recent completed tasks. Depending on how busy your deployment is, successful tasks can be shown for 24-48 hours. Unsuccessful tasks can show for 7-8 days. Tasks can also be retrieved from the [Cloud Databases API](https://cloud.ibm.com/apidocs/cloud-databases-api#get-currently-running-tasks-on-a-deployment) and [CLI plug-in](https://cloud.ibm.com/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-tasks-list). A historical record of tasks from any time period is available through the [{{site.data.keyword.atracker_full}} integration](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-at_events).

### Endpoints
{: #dashboard-overview-endpoints}

The _Endpoints_ pane within the _Overview_ pane contains connection strings for your deployment. Each tab contains connection information tailored to the type of connection or the protocol that uses it. Basic information includes things like _hostname_ and _port_, as well as the TLS self-signed certificate, TLS/SSL parameters, and the default database of your deployment.

Reference tables for the different connection types are available on the [Getting Credentials and Connection Strings](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-connection-strings) page.

Connection strings reflect whether your deployment uses public endpoints, private endpoints or both. You can configure which endpoints are available on your deployment. For more information, see the [Service Endpoints Integration](/docs/databases-for-enterprisedb?topic=cloud-databases-service-endpoints) page.

You can manage your {{site.data.keyword.databases-for-enterprisedb}} service through the {{site.data.keyword.databases-for}} API. This panel provides the essential information for using the API. For more information about the {{site.data.keyword.databases-for}} API, see the [API reference](https://{DomainName}/apidocs/cloud-databases-api) page.

## Resources
{: #dashboard-overview-resources}

The resources tab contains information and configuration options on the size and resource usage of your deployment. You can
- [Scale disk, memory, and CPU](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-resources-scaling)
- [Configure Autoscaling](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-autoscaling)

## Read Replicas
{: #dashboard-overview-read-replicas}

The _Read Replicas_ tab contains the UI for details regarding the read replicas for your deployment. Updates to replication leaders are asynchronously copied to their read-only replicas. You can
- [create and manage read-replicas](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-read-only-replicas)

## Backups
{: #dashboard-overview-backups}

The _Backups_ tab is the UI for managing your deployments backups. All of the available backups are listed with their timestamps. Click a backup to grab its ID or to restore it into a new deployment. More information is on the [Managing Backups](/docs/databases-for-enterprisedb?topic=cloud-databases-dashboard-backups) page.

## Observability
{: #dashboard-overview-observability}

The _Observability_ tab provides access to the IBM Cloud monitoring, logging, and event tracking integrations available for your deployment.
- [{{site.data.keyword.atracker_full}}](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-at_events)
- [{{site.data.keyword.logs_full}}](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-logging)
- [{{site.data.keyword.monitoringfull}}](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-monitoring)

## Settings
{: #dashboard-overview-settings}

The _Settings_ tab contains the UI for many of the tunable settings for your deployment. You can
- view encryption details. Encryption at rest is enabled for all {{site.data.keyword.databases-for-enterprisedb}} deployments. If you brought your own encryption key from [Key Protect](/docs/databases-for-enterprisedb?topic=cloud-databases-key-protect), the panel provides a link to your Key Protect instance and the _Encryption Key_ field has the name of the key.
- [Change the admin password](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-user-management&interface=ui#user-management-set-admin-password-ui)
- [Implement or modify an IP allowlist](/docs/databases-for-enterprisedb?topic=cloud-databases-allowlisting)

## Service Credentials
{: #dashboard-overview-service-credentials}

You can generate a new set of credentials for cases where you want to manually [connect an app](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-ibmcloud-app) or [external consumer](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-external-app) to an IBM Cloud service. [Learn more](/docs/account?topic=account-service_credentials).

## Connections
{: #dashboard-overview-connections}

Shows connected resources. You can use the `Create connection` button to to bind this service to another resource.

## View docs
{: #dashboard-overview-view-docs}

The _View docs_ link from the `Actions` drop list opens the main documentation page for {{site.data.keyword.databases-for-enterprisedb}} in a new tab.
