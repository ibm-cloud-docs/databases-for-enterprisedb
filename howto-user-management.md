---

copyright:
  years: 2019, 2025
lastupdated: "2025-01-10"

keywords: admin, superuser, roles, service credentials, postgresql, edb, enterprisedb

subcollection: databases-for-enterprisedb

---

{{site.data.keyword.attribute-definition-list}}

# Managing Users and Roles
{: #user-management}

{{site.data.keyword.databases-for-enterprisedb}} is deprecated. As of 16 June 2025 you can't deploy new instances. Existing instances are supported until 15 October 2025. Any instances that still exist on that date will be deleted. For more information, see [Deprecation of {{site.data.keyword.databases-for-enterprisedb}}](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-deprecation).
{: deprecated}

{{site.data.keyword.databases-for-enterprisedb_full}} uses a system of roles to manage database permissions. Roles are used to give a single user or a group of users a set of privileges. Determine roles, groups, and privileges for all roles across your deployment by using the `psql` command `\du`.

The `\du` command lists all users in the current database server. It can also be used to determine roles, groups, and privileges for all roles across a deployment.

Add users in the UI in _Service Credentials_, with the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin), or the [{{site.data.keyword.databases-for}} API](https://cloud.ibm.com/apidocs/cloud-databases-api/cloud-databases-api-v5#introduction).

## The `admin` user
{: #user-management-admin}
{: ui}
{: cli}
{: api}

When you provision a {{site.data.keyword.databases-for-mongodb}} deployment, an `admin` user is automatically created. 

Set the admin password before using it to connect.
{: important}

The biggest difference between the `admin` user and any other users you add to your deployment is the [`pg_monitor`](https://www.postgresql.org/docs/current/default-roles.html) and [`pg_signal_backend`](https://www.postgresql.org/docs/current/default-roles.html) roles. The `pg_monitor` role provides a set of permissions that makes the `admin` user appropriate for monitoring the database server. The `pg_signal_backend` role provides the `admin` user the ability to send signals to cancel queries and connections that are initiated by other users. It does not provide the ability to send signals to processes owned by superusers.

You can also use the admin user to grant these two roles to other users on your deployment.

To expose the ability to cancel queries to other database users, grant the `pg_signal_backend` role from the admin user. The command looks like:

```sql
GRANT pg_signal_backend TO joe;
```
{: pre}

You can also grant `pg_signal_backend` to all users with the `ibm-cloud-base-user` role with a command that looks like:

```sql
GRANT pg_signal_backend TO "ibm-cloud-base-user";
```
{: pre}

This privilege allows the user or users to terminate any connections to the database.
{: note}

To set up a specific monitoring user, `mary`, use a command like:

```sql
GRANT pg_monitor TO mary;
```
{: pre}

Grant `pg_signal_backend` to all the users with the `ibm-cloud-base-user` role with a command like:

```sql
GRANT pg_monitor TO "ibm-cloud-base-user";
```
{: pre}

### Setting the Admin Password in the UI
{: #user-management-set-admin-password-ui}
{: ui}

Set your Admin Password through the UI by selecting your instance from the Resource List in the [{{site.data.keyword.cloud_notm}} Dashboard](https://cloud.ibm.com/){: external}. Then, select **Settings**. Next, select *Change Database Admin Password*.

### Setting the Admin Password in the CLI
{: #user-management-set-admin-password-cli}
{: cli}

Use the `cdb user-password` command from the {{site.data.keyword.cloud_notm}} CLI {{site.data.keyword.databases-for}} plug-in to set the admin password.

For example, to set the admin password for a deployment named `example-deployment`, use the following command:

```sh
ibmcloud cdb user-password example-deployment admin <newpassword>
```
{: pre}

### Setting the Admin Password in the API
{: #user-management-set-admin-password-api}
{: api}

The Foundation Endpoint that is shown on the Overview panel Deployment Details section of your service provides the base URL to access this deployment through the API. Use it with the [Set specified user's password](https://cloud.ibm.com/apidocs/cloud-databases-api/cloud-databases-api-v5#changeuserpassword){: external} endpoint to set the admin password.

```sh
curl -X PATCH `https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/users/admin` \
-H `Authorization: Bearer <>` \
-H `Content-Type: application/json` \ 
-d `{"password":"newrootpasswordsupersecure21"}` \
```
{: pre}

## _Service Credential_ Users
{: #user-management-service-cred}
{: ui}

Users that you create through the [_Service Credentials_](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-connection-strings#creating-users-in-_service-credentials_) are members of `ibm-cloud-base-user`. They are able to log in, create users, and create databases.

When a user in a group creates a resource in a database, like a table, all users that are in the same group have access to that resource. Resources that are created by any of the users in `ibm-cloud-base-user` are accessible to other users in `ibm-cloud-base-user`, including the `admin` user.

## Users who are created through the CLI
{: #user-management-cli-users}
{: cli}

Users that you create through the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin) are members of `ibm-cloud-base-user`. They are able to log in, create users, and create databases.

When a user creates a resource in a database, like a table, all users that are in the same group have access to that resource. Resources that are created by any of the users in `ibm-cloud-base-user` are accessible to other users in `ibm-cloud-base-user`, including the admin user.

Users that are created directly from the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin) do not appear in _Service Credentials_, but you can add them.
{: important}

## Users who are created through the API
{: #user-management-api-users}
{: api}

Users that you create through the [{{site.data.keyword.databases-for}} API](https://cloud.ibm.com/apidocs/cloud-databases-api/cloud-databases-api-v5#introduction) are members of `ibm-cloud-base-user`. They are able to log in, create users, and create databases.

When a user creates a resource in a database, like a table, all users that are in the same group have access to that resource. Resources that are created by any of the users in `ibm-cloud-base-user` are accessible to other users in `ibm-cloud-base-user`, including the admin user.

Users that are created directly from the [{{site.data.keyword.databases-for}} API](https://cloud.ibm.com/apidocs/cloud-databases-api/cloud-databases-api-v5#introduction) do not appear in _Service Credentials_, but you can add them.
{: important}

## The read-only user
{: #user-management-read-only}
{: ui}
{: cli}
{: api}

The `ibm-cloud-base-user-ro` manages privileges for users that are created to access read-only replicas. For more information, see [Configuring Read-only Replicas](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-read-only-replicas).

## Other `ibm` Users
{: #user-management-other-ibm}
{: ui}
{: cli}
{: api}

If you run the `\du` command with your `admin` account, you see the `ibm`, `ibm-cloud-base-user`, and `ibm-replication` users.

The `ibm-cloud-base-user` is used as a template to manage group roles for other users. It is used to manage users who are created through the CLI and API. It also enables integration with the _Service Credentials_ user creation. A user that is a member of `ibm-cloud-base-user` inherits the create role and create database attributes from `ibm-cloud-base-user`. The `ibm-cloud-base-user` is not able to log in.

The `ibm` and the `ibm-replication` accounts are the only superusers on your deployment. A superuser account is not available for you to use. These users are internal administrative accounts that manage replication, metrics, and other functions that ensure the stability of your deployment.

## Users created with `psql`
{: #user-management-psql}
{: ui}
{: cli}
{: api}

You can bypass creating users through {{site.data.keyword.cloud_notm}} by creating users directly in EnterpriseDB with `psql`. `psql` makes use of PostgreSQL's native [role and user management](https://www.postgresql.org/docs/current/database-roles.html){: external}. Users and roles that are created in `psql` must have all of their privileges set manually, including privileges to the objects that they create.

Users that are created directly in {{site.data.keyword.databases-for-enterprisedb}} do not appear in _Service Credentials_, but you can [add them](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-connection-strings#adding-users-to-_service-credentials_). 

Note that these users are not integrated with IAM controls, even if added to _Service Credentials_.
{: .tip}

## The `emp_admin` user
{: #user-management-emp_admin}
{: ui}
{: cli}
{: api}

The `emp_admin` is an internal {{site.data.keyword.databases-for-enterprisedb}} user that is used by the EDB Migration Portal to communicate directly with EnterpriseDB databases. Connect to the EDB Migration Portal by using your [IAM](/docs/databases-for-enterprisedb?topic=cloud-databases-iam) login information.

Do no drop this user.
{: important}

## The `aq_administrator_role` user
{: #user-management-aq_administrator}
{: ui}
{: cli}
{: api}

The `aq_administrator_role` user is a system-defined privilege that allows a user to interact with queues. This user is provided by default from EnterpriseDB and is not managed by {{site.data.keyword.databases-for-enterprisedb}}. For more information, see [CREATE QUEUE](https://www.enterprisedb.com/docs/epas/latest/epas_compat_sql/30_create_queue/){: external}.

Do not drop this user.
{: important}
