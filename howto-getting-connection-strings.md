---
copyright:
  years: 2017, 2025
lastupdated: "2025-01-10"

keywords: postgresql, databases, edb, enterprisedb

subcollection: databases-for-enterprisedb

---

{{site.data.keyword.attribute-definition-list}}


# {{site.data.keyword.databases-for-enterprisedb_full}} Connection Strings
{: #connection-strings}

{{site.data.keyword.databases-for-enterprisedb}} is deprecated. As of 16 June 2025 you can't deploy new instances. Existing instances are supported until 15 October 2025. Any instances that still exist on that date will be deleted. For more information, see [Deprecation of {{site.data.keyword.databases-for-enterprisedb}}](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-deprecation).
{: deprecated}

In order to connect to {{site.data.keyword.databases-for-enterprisedb_full}}, you need some users and connection strings. 

## Getting Connection Strings in the UI
{: #connection-strings}
{: ui}

Connection Strings for your deployment are displayed on the **Dashboard Overview**, in the **Endpoints** pane. 

![Endpoints pane](images/getting-started-endpoints-panel.png){: caption="Endpoints pane" caption-side="bottom"}

A {{site.data.keyword.databases-for-enterprisedb}} deployment is provisioned with an admin user, and after [setting the admin password](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-user-management&interface=ui#user-management-set-admin-password-ui), you can use its connection strings to connect to your deployment.
{: .tip}

## Getting Connection Strings in the CLI
{: #connection-strings}
{: cli}

You can also grab connection strings from the [CLI](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-connections).
```sh
ibmcloud cdb deployment-connections example-deployment -u <newusername> [--endpoint-type <endpoint type>]
```

Full connection information is returned by the `ibmcloud cdb deployment-connections` command with the `--all` flag. To retrieve all the connection information for a deployment named "example-deployment", use the following command.
```sh
ibmcloud cdb deployment-connections example-deployment -u <newusername> --all [--endpoint-type <endpoint type>]
```

If you don't specify a user, the `deployment-connections` commands return information for the admin user by default. If you don't specify an endpoint type, the connection string returns the public endpoint by default. If your deployment has only a private endpoint, you must specify `--endpoint-type private` or the commands return an error. The user and endpoint type is not enforced. You can use any user on your deployment with either endpoint (if both exist on your deployment).

To use the `ibmcloud cdb` CLI commands, you must [install the {{site.data.keyword.databases-for}} plugin](/docs/databases-for-enterprisedb?topic=databases-cli-plugin-cdb-reference#installing-the-cloud-databases-cli-plug-in).
{: .tip}

## Getting Connection Strings through the API
{: #connection-strings}
{: api}

To retrieve user's connection strings through the API, use the [`/users/{userid}/connections`](https://{DomainName}/apidocs/cloud-databases-api#discover-connection-information-for-a-deployment-f-e81026) endpoint. You must specify in the path which user and which type of endpoint (public or private) should be used in the returned connection strings. The user and endpoint type is not enforced. You can use any user on your deployment with either endpoint (if both exist on your deployment).
```sh
curl -X GET -H "Authorization: Bearer $APIKEY" 'https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/users/{userid}/connections/{endpoint_type}'
```

## Additional Users and Connection Strings
{: #add-users-conn-strings}

Access to your {{site.data.keyword.databases-for-enterprisedb}} deployment is not limited to the admin user. You can create users by using the _Service Credentials_ pane, the {{site.data.keyword.IBM_notm}} CLI, or through the {{site.data.keyword.IBM_notm}} {{site.data.keyword.databases-for}} API. 

All users on your deployment can use the connection strings, including connection strings for either public or private endpoints.

When you create a user, it is assigned certain database roles and privileges. These privileges include the ability to log in, create databases, and create other users. For more information, see the [Managing Users, Roles, and Privileges](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-user-management) page.

### Creating Users in the Service Credentials UI
{: #create-users-service-cred}
{: ui}

1. Navigate to the service dashboard for your service.
2. Click _Service Credentials_ to open the _Service Credentials_ pane.
3. Click **New Credential**.
4. Choose a descriptive name for your new credential. 
5. (Optional) Specify whether the new credentials should use a public or private endpoint. Use either `{ "service-endpoints": "public" }` / `{ "service-endpoints": "private" }` in the _Add Inline Configuration Parameters_ field to generate connection strings that use the specified endpoint. Use of the endpoint is not enforced, it just controls which hostnames are in the connection strings. Public endpoints are generated by default.
6. Click **Add** to provision the new credentials. A username and password, and an associated database user in the {{site.data.keyword.databases-for-enterprisedb}} database are auto-generated.

The new credentials appear in the table, and the connection strings are available as JSON in a click-to-copy field under _View Credentials_.

### Creating Users from the CLI
{: #create-users-cli}
{: cli}

If you manage your service through the {{site.data.keyword.cloud_notm}} CLI and the [cloud databases plug-in](/docs/cli?topic=cli-install-ibmcloud-cli), you can create a new user with `cdb user-create`. For example, to create a new user for an "example-deployment", use the following command.
```sh
ibmcloud cdb user-create example-deployment <newusername> <newpassword>
```

Once the task finishes, you can retrieve the new user's connection strings with the `ibmcloud cdb deployment-connections` command.

### Creating Users through the API
{: #create-users-api}
{: api}

The _Foundation Endpoint_ that is shown on the _Overview_ pane of your service provides the base URL to access this deployment through the API. To create and manage users, use the base URL with the `/users` endpoint.
```sh
curl -X POST 'https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/users' \
-H "Authorization: Bearer $APIKEY" \
-H "Content-Type: application/json" \
-d '{"username":"jane_smith", "password":"newsupersecurepassword"}'
```

Once the task finishes, you can retrieve the new user's connection strings, from the `/users/{userid}/connections` endpoint.

### Adding users in the Service Credentials UI
{: #add-users-service-cred}
{: ui}

Creating a new user from the CLI doesn't automatically populate that user's connection strings into _Service Credentials_. If you want to add them there, you can create a new credential with the existing user information.

Enter the username and password in the JSON field _Add Inline Configuration Parameters_, or specify a file where the JSON information is stored. For example, putting `{"existing_credentials":{"username":"Robert","password":"supersecure"}}` in the field generates _Service Credentials_ with the username "Robert" and password "supersecure" filled into connection strings.

Generating credentials from an existing user does not check for or create that user.
{: tip}

## Connection String Breakdown
{: #connection-string-breakdown}

### The Postgres Section
{: #postgres-section}

The "postgres" section contains information that is suited to applications that make connections to {{site.data.keyword.databases-for-enterprisedb}}.

| Field Name | Index | Description |
| ---------- | ----- | ----------- |
| `Type` | | Type of connection - for {{site.data.keyword.databases-for-enterprisedb}}, it is "URI" |
| `Scheme` | | Scheme for a URI - for {{site.data.keyword.databases-for-enterprisedb}}, it is "postgresql" |
| `Path` | | Path for a URI - for {{site.data.keyword.databases-for-enterprisedb}}, it is the database name. The default is `ibmclouddb`. |
| `Authentication` | `Username` | The username that you use to connect. |
| `Authentication` | `Password` | A password for the user - might be shown as `$PASSWORD` |
| `Authentication` | `Method` | How authentication takes place; "direct" authentication is handled by the driver. |
| `Hosts` | `0...` | A hostname and port to connect to |
| `Composed` | `0...` | A URI combining Scheme, Authentication, Host, and Path |
| `Certificate` | `Name` | The allocated name for the self-signed certificate for database deployment |
| `Certificate` | Base64 | A base64 encoded version of the certificate. |
{: caption="postgresql/URI connection information" caption-side="top"}

* `0...` Indicates that there might be one or more of these entries in an array.

### The CLI Section
{: #cli-section}

The "CLI" section contains information that is suited for connecting with `psql` .

| Field Name | Index | Description |
| ---------- | ----- | ----------- |
| `Bin` | | The recommended binary to create a connection; in this case it is `psql`. |
| `Composed` | | A formatted command to establish a connection to your deployment. The command combines the `Bin` executable, `Environment` variable settings, and uses  |`Arguments` as command-line parameters.
| `Environment` | | A list of key/values you set as environment variables. |
| `Arguments` | 0... | The information that is passed as arguments to the command shown in the Bin field. |
| `Certificate` | Base64 | A self-signed certificate that is used to confirm that an application is connecting to the appropriate server. It is base64 encoded. |
| `Certificate` | Name | The allocated name for the self-signed certificate. |
| `Type` | | The type of package that uses this connection information; in this case `cli`.  |
{: caption="psql/cli connection information" caption-side="top"}

* `0...` Indicates that there might be one or more of these entries in an array.
