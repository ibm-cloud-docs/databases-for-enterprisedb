---

copyright:
  years: 2020, 2025
lastupdated: "2025-01-10"

keywords: troubleshooting for EnterpriseDB

subcollection: databases-for-enterprisedb

content-type: troubleshoot

---

{{site.data.keyword.attribute-definition-list}}


# Why can’t I connect to my EnterpriseDB deployment?
{: #troubleshoot-connect}
{: troubleshoot}
{: support}

{{site.data.keyword.databases-for-enterprisedb}} is deprecated. As of 16 June 2025 you can't deploy new instances. Existing instances are supported until 15 October 2025. Any instances that still exist on that date will be deleted. For more information, see [Deprecation of {{site.data.keyword.databases-for-enterprisedb}}](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-deprecation).
{: deprecated}

If you encounter errors when connecting to your {{site.data.keyword.databases-for-enterprisedb_full}} deployment, review these common causes and resolutions.
{: shortdesc}

You receive an error message or fail to connect to a {{site.data.keyword.databases-for-enterprisedb}} deployment.  If reviewing application logs, you might see errors that mention intermittent connection timeouts or unable to connect.
{: tsSymptoms}

Review the following information to troubleshoot and resolve common connectivity problems:
{: #tsResolve}

* An unsecured connection is a common cause of connectivity errors.  All {{site.data.keyword.databases-for-enterprisedb}} connections use TLS/SSL encryption; {{site.data.keyword.databases-for-enterprisedb}} rejects unsecured connections.  To avoid errors, make sure you configured a secure connection.  Refer to [Getting Started](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-getting-started) for an example of a secure connection.
* If you are using a private endpoint, make sure that you specify connection strings that contain the private endpoint (see [Credentials for Private Endpoints](/docs/databases-for-enterprisedb?topic=cloud-databases-service-endpoints#credentials-for-private-endpoints)) and that you followed the steps in [Connecting through Private Endpoints](/docs/databases-for-enterprisedb?topic=cloud-databases-service-endpoints#private-endpoint-connections).
* If your application log captures a short connection interruption, that behavior is expected as a normal part of operations for this managed service. You want to design your applications to retry connections when errors are caused by a temporary loss in connectivity to your deployment or to IBM Cloud. However, if you experience several minutes of connection interruption check the Cloud Status for the service. For more information, see [Application-level High-Availability](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-high-availability#application-level-high-availability).
