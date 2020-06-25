---
copyright:
  years: 2017, 2019
lastupdated: "2020-06-22"

keywords: postgresql, databases

subcollection: databases-for-enterprisedb

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Managing PostgreSQL Extensions
{: #extensions}

In PostgreSQL, extensions are modules that supply extra functions, operators, or types. Many extensions are available in {{site.data.keyword.databases-for-enterprisedb_full}}. In order to use them, you need to [set the admin password](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-admin-password) for your service and use it to [connect with `psql` ](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-connecting-psql).

## Listing Installed Extensions

Get a list of all the extensions installed on a database by using the `\dx` command.

For example, the output for `\dx` when run on the {{site.data.keyword.databases-for-enterprisedb}} default database shows the only installed extension.
```
ibmclouddb=> \dx
                 List of installed extensions
  Name   | Version |   Schema   |         Description
---------+---------+------------+------------------------------
 plpgsql | 1.0     | pg_catalog | PL/pgSQL procedural language
(1 row)
```

## Installing Extensions

To install an extension on to a database use [`CREATE EXTENSION`](https://www.postgresql.org/docs/current/static/sql-createextension.html). For example, to install `pg_stat_statements` on the `ibmclouddb` database, 

```
ibmclouddb=> CREATE EXTENSION pg_stat_statements;
CREATE EXTENSION
```

If you run the `\dx` command after installing an extension, it appears in the table.
```
ibmclouddb=> \dx
                                     List of installed extensions
        Name        | Version |   Schema   |                        Description
--------------------+---------+------------+-----------------------------------------------------------
 pg_stat_statements | 1.5     | public     | track execution statistics of all SQL statements executed
 plpgsql            | 1.0     | pg_catalog | PL/pgSQL procedural language
(2 rows)
```

Database extensions in PostgreSQL are managed per database. If you have multiple databases that you need to install an extension on, you run the `CREATE` command on each database.

## Upgrading Extensions

If there is a newer version of an extension available than the one you currently have installed, use the `ALTER EXTENSION` to upgrade it.

## Extension-Specific Notes

### pg_repack
- [The `pg_repack` documentation](http://reorg.github.io/pg_repack/)
- When you run the `pg_repack` command, you need to pass the -k flag in to bypass the check for superuser. Example,
  ```
  pg_repack -k [OPTION]... [DBNAME]
  ```
- For `pg_repack` to run reliably, your deployment should be on PostgreSQL 9.6 and above.
- Any user can run `pg_repack`, but the command is only be able to repack a table that they have permissions on.
- `pg_repack` needs to take an exclusive lock on objects it is reorganizing at the end of the reorganization. If it can't get this lock after a certain period, it cancels all conflicting queries. If it can't do so, the reorg will fail. By default, only the admin user on PostgreSQL 9.6 and greater has the ability to cancel conflicting queries. If you want to expose the ability to cancel queries to other database users, you can grant the `pg_signal_backend` role [from the admin user](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-user-management#the-admin-user).

## Available Extensions

This list is what is returned from a {{site.data.keyword.databases-for-enterprisedb}} deployment running PostgreSQL version 10. For a list of available extensions on your deployment, use `SELECT name FROM pg_available_extensions;` in `psql`.

```
ibmclouddb=> SELECT name FROM pg_available_extensions;
              name
------------------------------
 refint
 uuid-ossp
 sslinfo
 pg_stat_statements
 unaccent
 hstore_plperlu
 pg_freespacemap
 pg_visibility
 moddatetime
 cube
 fuzzystrmatch
 earthdistance
 pageinspect
 tablefunc
 tsm_system_time
 plperl
 postgres_fdw
 intarray
 ltree
 pgrowlocks
 dict_int
 file_fdw
 hstore_plperl
 lo
 seg
 isn
 plpgsql
 chkpass
 insert_username
 btree_gist
 tsm_system_rows
 autoinc
 dblink
 bloom
 pg_trgm
 btree_gin
 xml2
 pg_prewarm
 intagg
 hstore
 amcheck
 pgcrypto
 timetravel
 tcn
 pg_buffercache
 citext
 dict_xsyn
 pgstattuple
 postgis_topology
 postgis
 address_standardizer
 postgis_tiger_geocoder
 address_standardizer_data_us
 pgrouting
(54 rows)
 ```