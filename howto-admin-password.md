---
copyright:
  years: 2020, 2024
lastupdated: "2024-09-30"

keywords: admin password, credentials, edb, enterprisedb

subcollection: databases-for-enterprisedb

---

{{site.data.keyword.attribute-definition-list}}

# Setting the {{site.data.keyword.databases-for-enterprisedb}} Admin Password
{: #admin-password}

{{site.data.keyword.databases-for-enterprisedb}} is deprecated. As of 15 March 2025 you can't deploy new applications. Existing instances are supported until 30 September 2025. Any instances that still exist on that date will be deleted. For more information, see Deprecation of {{site.data.keyword.databases-for-etcd_full}}.
{: deprecated}

The {{site.data.keyword.databases-for-enterprisedb}} service is provisioned with an admin user. You must set the admin password before you can use it to connect. 

## Setting the {{site.data.keyword.databases-for-enterprisedb}} admin password in the UI
{: #admin-password-ui}
{: ui}

To set the password through the {{site.data.keyword.cloud_notm}} dashboard, select _Manage_ from the service dashboard to open the management pane for your service. Open the _Settings_ tab, and use the _Change Database Admin Password_ pane to set a new admin password.

![The Admin Password pane in Settings](images/settings-admin-password.png){: caption="Figure 1. The Admin Password pane in Settings" caption-side="bottom"}

## Setting the {{site.data.keyword.databases-for-enterprisedb}} admin password with the CLI
{: #setting-adminpw-cli}
{: cli}

Use the `cdb user-password` command from the [{{site.data.keyword.databases-for-enterprisedb}} CLI plug-in](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference) to set the admin password with the command line.

For example, to set the admin password for a deployment named "example-deployment", use the following command.
```sh
ibmcloud cdb user-password example-deployment admin <newpassword>
```

## Setting the {{site.data.keyword.databases-for-enterprisedb}} admin password through the API
{: #setting-adminpw-api}
{: api}

The _Foundation Endpoint_ that is shown on the _Overview_ pane of your service provides the base URL to access this deployment through the API. Use it with the `/deployments/{id}/users/{username}` endpoint to set the admin password.

```sh
curl -X PATCH `https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/users/admin' \
-H "Authorization: Bearer $APIKEY" \
-H "Content-Type: application/json" \
-d '{"password":"newrootpasswordsupersecure21"}'
```

For more information, see the [API Reference](https://{DomainName}/apidocs/cloud-databases-api#set-database-level-user-s-password).
