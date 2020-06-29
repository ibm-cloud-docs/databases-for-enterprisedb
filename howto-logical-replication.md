---

Copyright:
  years: 2019, 2020
lastupdated: "2020-06-25"

keywords: postgresql, databases

subcollection: databases-for-enterprisedb

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}


# Configuring {{site.data.keyword.databases-for-enterprisedb}} as a Logical Replication Destination
{: #logical-replication}

{{site.data.keyword.databases-for-enterprisedb_full}} supports [logical replication](https://www.postgresql.org/docs/current/logical-replication.html) from an external PostgreSQL instance to your deployment. You can set up your external PostgreSQL as a publisher, your {{site.data.keyword.databases-for-enterprisedb}} deployment as a subscriber, and replicate your data across from an external database into your deployment.

## Configuring the Publisher

The external PostgreSQL instance is the publisher, and needs to be configured in order for your {{site.data.keyword.databases-for-enterprisedb}} deployment to connect and be able to pull the data in correctly.

1. Every table that is selected for replication needs to contain a [Primary Key](https://www.postgresql.org/docs/current/ddl-constraints.html#DDL-CONSTRAINTS-PRIMARY-KEYS), or have [`REPLICA IDENTITY`](https://www.postgresql.org/docs/current/sql-altertable.html#replica-identity) set.
2. Your external (publisher) PostgreSQL needs to have a replication user that has the [PostgreSQL privilege `REPLICATION`](https://www.postgresql.org/docs/current/sql-createrole.html#replication), and that user needs to have privileges on the databases you would like replicate.
3. The PostgreSQL you are replicating from needs to have [TLS/SSL enabled](https://www.postgresql.org/docs/current/ssl-tcp.html).

There are inherent limitations and some restrictions to logical replication outlined in the [PostgreSQL documentation](https://www.postgresql.org/docs/current/logical-replication-restrictions.html). You should review these before deciding that logical replication is appropriate for your use-case.
{: .tip}

## Configuring the Subscriber

To configure your {{site.data.keyword.databases-for-enterprisedb}} deployment and to make sure that your data is replicated across correctly,

1. You need to create a database in your deployment with same name as the database you intend to replicate.
2. Logical Replication works at the table level, so every table you select to publish, you need to create in the subscriber before starting the logical replication process. (You can use [`pg_dump`](https://www.postgresql.org/docs/current/app-pgdump.html) to help.) The table on the subscriber does not need to be identical to its publisher counterpart. However, the table on the subscriber must contain at least every  column present in the table on the publisher. Additional columns present in the subscriber must not have NOT NULL or other constraints. If they do, replication fails.

**Note** Native PostgreSQL subscription commands require superuser privileges, which are not available on {{site.data.keyword.databases-for-enterprisedb}} deployments. Instead, your deployment includes a set of functions that can be used to set and manage logical replication for the subscription. 

Only the admin user that is provided by {{site.data.keyword.databases-for-enterprisedb}} has permissions to run the following replication commands that allow you to subscribe and replicate content from an external PostgreSQL publisher.
{: .tip}

### Subscriber Functions

**`create_subscription`**
```bash
Arguments:
    subscription_name   Unique name to create the subscription channel with
    host_ip             Publisher hostname or public IP address
    port                Port number publisher is running on
    username            `admin` user created on the publisher
    password            Password of the `admin` user on the publisher
    db_name             The name of the database to be replicated
    publisher_name      The name of publisher channel on the publisher

Usage:
    exampledb=> SELECT create_subscription('subs1','130.215.223.184','5432','password','admin','exampledb','my_publication');
```

**`delete_subscription`**
```bash
Arguments:
    subscription_name   Name the subscription channel to delete
    db_name             The name of the replicated database

Usage:
exampledb=> SELECT delete_subscription('subs1', 'exampledb');
```

**`list_subscriptions`**
```bash
Arguments:
    None

Usage:
    exampledb=> SELECT * FROM list_subscriptions();
```

**`disable_subscription`**
```bash
Arguments:
    subscription_name   Name the subscription channel to disable
    db_name             The name of the replicated database

Usage:
    exampledb=> SELECT disable_subscription('subs1','exampledb');
```

**`enable_subscription`**
```bash
Arguments:
    subscription_name   Name the subscription channel to enable
    db_name             The name of the replicated database

Usage:
    exampledb=> SELECT enable_subscription('subs1','exampledb');
```

**`refresh_subscription`**  

This function is used to refresh a subscription on the subscriber after changes are made on the publisher, like adding or removing a table.
```bash
Arguments:
    subscription_name   Name the subscription channel to refresh
    db_name             The name of the replicated database

Usage:
    exampledb=> SELECT refresh_subscription('subs1','exampledb');
```

## Setting up Logical Replication on the Publisher

To configure your external PostgreSQL as a publisher,

- Edit your local `pg_hba.conf` and add the following 
    ```text
    hostssl    replication            replicator         0.0.0.0/0      md5
    hostssl    all                    replicator         0.0.0.0/0      md5
    ```
    The "replicator" field is the user you set up with the [PostgreSQL privilege `REPLICATION`](https://www.postgresql.org/docs/current/sql-createrole.html#replication).

- Edit your local `postgresql.conf` with the required [logical replication configuration](https://www.postgresql.org/docs/current/logical-replication-config.html). Set `wal_level` to 'logical', and set `listen_addresses='*'` to accept connections from any host.  
    ```text
    listen_addresses='*'
    wal_level = logical                   
    ```

- Restart your PostgreSQL server.

Now you can define a publisher on the database and add the tables you want to replicate to the subscriber.

- Log in to database you want to publish from with your replication user.
    ```bash
        psql -U replicator -d exampledb
    ```
- Create the publication channel.
    ```bash
        exampledb=> CREATE PUBLICATION my_publication;
    ``` 
- Add tables to publisher.
    ```bash
       exampledb=> ALTER PUBLICATION my_publication ADD TABLE my_table;
    ```

## Setting up Logical Replication on the Subscriber

To configure your {{site.data.keyword.databases-for-enterprisedb}} deployment as a subscriber,
- Log in to the database created for replication with `admin` user.
    ```bash
    psql -U admin -d exampledb
    ```
- Run the following query to call the `create_subscription` function and create the subscriber channel. 
    ```bash
        exampledb=> SELECT create_subscription('subs1','130.215.223.184','5432','admin','password','exampledb','my_publication');
    ```

## Monitoring Replication

You can monitor the status of logical replication from both the publisher and the subscriber by running the following query on either.
```bash
    exampledb=> SELECT * FROM pg_stat_replication;
```
