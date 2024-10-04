---

copyright:
  years: 2019, 2024
lastupdated: "2024-09-30"

keywords: postgresql, databases, connection limits, terminating connections, connection pooling, edb, enterprisedb

subcollection: databases-for-enterprisedb

---

{{site.data.keyword.attribute-definition-list}}

# Managing {{site.data.keyword.databases-for-enterprisedb}} Connections
{: #managing-connections}

Connections to your {{site.data.keyword.databases-for-enterprisedb_full}} deployment use resources, so it is important to consider how many connections you need when tuning your deployment's performance. {{site.data.keyword.databases-for-enterprisedb}} uses a `max_connections` setting to limit the number of connections (and resources that are consumed by connections) to prevent run-away connection behavior from overwhelming your deployment's resources.

You can check the value of `max_connections` with your [admin user](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-user-management#the-admin-user) and [`psql`](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-connecting-psql).
```sh
ibmclouddb=> SHOW max_connections;
 max_connections
-----------------
 115
(1 row)
```

## {{site.data.keyword.databases-for-enterprisedb}} Connection Limits 
{: #managing-connections-connection-limits}

At provision, {{site.data.keyword.databases-for-enterprisedb}} sets the maximum number of connections to your {{site.data.keyword.databases-for-enterprisedb}} database to **115**. 15 connections are reserved for the superuser to maintain the state and integrity of your database, and 100 connections are available for you and your applications. If the number of connections to the database exceeds the 100 connection limit, new connections fail and return an error.
```sh
FATAL: remaining connection slots are reserved for
non-replication superuser connections
```
Exceeding the connection limit for your deployment can cause your database to be unreachable by your applications.

You can check the number of connections to your deployment with the admin user, `psql`, and `pg_stat_database`.
```sql
SELECT count(distinct(numbackends)) FROM pg_stat_database;
```

If you need to figure out where the connections are going, you can break down the connections by database.
```sql
SELECT datname, numbackends FROM pg_stat_database;
```

To further investigate connections to a specific database, query `pg_stat_activity`.
```sql
SELECT * FROM pg_stat_activity WHERE datname='ibmclouddb';
```

## Terminating Connections
{: #managing-connections-terminating-connections}

Your admin user has the `pg_signal_backend` role. If you find connections that need to be reset or closed, the admin user can use both [`pg_cancel_backend` and `pg_terminate_backend`](https://www.postgresql.org/docs/current/functions-admin.html#FUNCTIONS-ADMIN-SIGNAL-TABLE). The `pid` of a process is found from the `pg_stat_activity` table.

   - `pg_cancel_backend` Cancels a connection's current query without terminating the connection, and without stopping any other queries that it might be running.
     ```sql
     SELECT pg_cancel_backend(pid);
     ```

   - `pg_terminate_backend` Stops the entire process and closes the connection. 
     ```sql
     SELECT pg_terminate_backend(pid);
     ```

The admin user does have the power to reset or close the connections for any user on the deployment except superusers. Be careful not to terminate replication connections from the `ibm-replication` user, as it interferes with the high-availability of your deployment.

### End Connections
{: #managing-connections-end-connections}

If your deployment reaches the connection limit or you are having trouble connecting to your deployment and suspect that a high number of connections is a problem, you can disconnect (or end) all of the connections to your deployment. 

In the UI, on the _Settings_ tab, there is a button to `End Connections` to your deployment. Use caution, as it disrupts anything that is connected to your deployment.

![End Connections UI](images/settings-end-connections.png){: caption="Figure 1. End Connections UI" caption-side="bottom"}

The CLI command to end connections to the deployment is 
```sh
ibmcloud cdb deployment-kill-connections <deployment name or CRN>
```

You can also use the [{{site.data.keyword.databases-for}} API](https://cloud.ibm.com/apidocs/cloud-databases-api#kill-connections-to-a-postgresql-deployment) to perform the end all connections operation.

## Connection Pooling
{: #managing-connections-connection-pooling}

One way to prevent exceeding the connection limit and ensure that connections from your applications are being handled efficiently is through connection pooling.

Many PostgreSQL driver libraries have connection pooling classes and functions. You need to consult your driver's documentation to implement connection pooling that is optimal for your use case. For example, the Python driver Psycopg2 has [classes to handle connection pooling in your application](http://initd.org/psycopg/docs/pool.html). The Java PostgreSQL JDBC driver has methods for [connection pooling at both the application and application server level](https://jdbc.postgresql.org/documentation/head/datasource.html).

Alternatively, you can use a third-party tool such as [PgBouncer](https://pgbouncer.github.io/) to manage your application's connections.

## Raising the Connection Limit
{: #managing-connections-raising-limit}

{{site.data.keyword.databases-for-enterprisedb}} allocates some amount of memory on a per connection basis. It is important to consider the total amount of memory that is available to your deployment before increasing the connection limit. To raise the connection limit, first you might want to [scale your deployment](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-resources-scaling) to ensure that you have enough memory to accommodate more connections.

Next, change the value of `max_connections` on your deployment. To make permanent changes to the [{{site.data.keyword.databases-for-enterprisedb}} configuration](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-changing-configuration#changing-configuration), you want to use the {{site.data.keyword.databases-for}} [cli-plugin](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-configuration) or [API](https://{DomainName}/apidocs/cloud-databases-api#change-your-database-configuration) to write the changes to the configuration file for your deployment. 

For example, to raise `max_connections` to 215, it might be a good idea to scale your deployment to at least 2 GB of RAM per data member, for a total of 6 GB of RAM for your deployment. Once the scaling operation has finished, then set the connection limit. In the CLI,
```sh
ibmcloud cdb deployment-groups-set example-deployment member --memory 6144

ibmcloud cdb deployment-configuration example-deployment '{"configuration":{"max_connections":215}}'
```

To make the changes by using the API,
```sh
curl -X PATCH `https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/groups/member' \
-H "Authorization: Bearer $APIKEY" \
-H "Content-Type: application/json" \
-d '{"memory": {
        "allocation_mb": 6144
      }
    }'

curl -X PATCH 'https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/configuration' \
-H "Authorization: Bearer $APIKEY" \
-H "Content-Type: application/json" \
-d '{"configuration":{
        "max_connections":215
      }
    }'
```
