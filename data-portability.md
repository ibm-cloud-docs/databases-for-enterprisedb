---

copyright:
  years: 2024
lastupdated: "2024-11-12"

keywords: data export, portability, pg_dump, pg_restore

subcollection: databases-for-enterprisedb

---

{{site.data.keyword.attribute-definition-list}}



# Understanding data portability for {{site.data.keyword.databases-for-enterprisedb}}
{: #data-portability}

[Data Portability](#x2113280){: term} involves a set of tools, and procedures that enable customers to export the digital artifacts that would be needed to implement similar workload and data processing on different service providers or on-prem software. It includes procedures for copying and storing the service customer's content, including the related configuration used by the service to store and process the data, on customer's own location.
{: shortdesc}

## Responsibilities
{: #data-portability-responsibilities}

IBM Cloud services provide interfaces and instructions to guide the customer to copy and store the service customer content, including the related configuration, on their own selected location.

The customer then is responsible for the use of the exported data and configuration for the purpose of data portability to other infrastructures. This can involve the following:

- Planning and execution for setting up alternate infrastructure on on different cloud providers or on-prem software that provide similar capabilities to the IBM services.
- Planning and execution for the porting of the required application code on the alternate infrastructure, including the adaptation of customer's application code, and deployment automation.
- Conversion of the exported data and configuration to format required by the alternate infrastructure and adapted applications,

For more information about your responsibilities when using {{site.data.keyword.databases-for-enterprisedb}}, see [Shared responsibilities for {{site.data.keyword.databases-for-enterprisedb}}](/docs/cloud-databases?topic=cloud-databases-responsibilities-cloud-databases).

## Data export procedures
{: #data-portability-procedures}

{{site.data.keyword.databases-for-enterprisedb}} provides mechanisms to export your content that has been uploaded, stored, and processed using the service.

## Migrating data from {{site.data.keyword.databases-for-enterprisedb}}
{: #data-portability-migrating-data-enterprisedb}

You can use the following methods to export data from {{site.data.keyword.databases-for-enterprisedb}}.

Connect to your {{site.data.keyword.cloud}} deployment: 

To access your {{site.data.keyword.databases-for-enterprisedb}} deployment and its tools, follow the connection instructions in our documentation. You have access to the `psql` and `pg_dump` commands when connected. You can also use PGadmin to export data. For more information, see the [Getting started page](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-best-practices&interface=ui).

Ensure that you are connected to the deployment containing the database that you want to export. Replace `<<CRN>>` with your actual Cloud Resource Name.

```sh
ibmcloud cdb cxn <<CRN>> -s
```
{: pre}

## Using `pg_dump`
{: #ata-portability-pg_dump}

Run `pg_dump` on your database to create an SQL file that can be used to re-create the database. At a minimum, `pg_dump` takes a hostname (`-h` flag), port number (`-p` flag), database name (`-d` flag), username (`-U` flag), and a file (or directory name) to write the dump to (`-f` flag). 

For example, the following command dumps the {{site.data.keyword.databases-for-enterprisedb}} "compose" database that is hosted on `sl-eu-lon-2-portal.4.dblayer.com`, `port 17980`, using the `admin user` and saves the results in `dump.sql`.

```sh
pg_dump -h sl-eu-lon-2-portal.4.dblayer.com -p 17980 -d compose -U admin -f dump.sql
```
{: pre}

Further options:

The `pg_dump` command offers more capabilities. For a complete list and detailed explanation, see the [pg_dump documentation](https://www.postgresql.org/docs/current/backup-dump.html) and [command reference](https://www.postgresql.org/docs/current/app-pgdump.html). You can export specific parts of your database instead of the entire structure using the documented options.
  
Dumps can be generated as either script or archive files (use `t` option). Script dumps are plain-text SQL commands intended to be read with `psql`, while archive file dumps require `pg_restore` for reconstruction. Archive formats offer greater flexibility, allowing for selective restoration.
{: .note}

 ### Additional migration option by using `pg_restore`
{: #pg_restore}
  
 For TAR files containing separate SQL and data files, the `pg_restore` command provides a more flexible approach to database migration.
 An example of the `pg_restore` command is:

```sh
 PGPASSWORD=yourpasswordhere PGSSLROOTCERT=cert.crt pg_restore -h c7798cf6-e5d2-4513-b17f-3d3fa67d8291.8f7bfd8f3faa4218aec56e069eb46187.databases.appdomain.cloud -p 32484 -U admin -F t -d ibmclouddb tarfile.tar
```
{: .pre}

## Exported data formats
{: #data-portability-data-formats}

The exported data can be in plain text (`sql`) or archive files (`tar`), and based on the file formats, data can be migrated to any other EnterpriseDB or Postgresql instance using either `psql` or `pg_restore` commands. To restore data from a TAR file, see the [pg_restore documentation](https://www.postgresql.org/docs/current/app-pgrestore.html).

## Data ownership
{: #data-ownership}

All exported data are classified as Customer content and therefore apply to them the full customer ownership and licensing rights, as stated in [IBM Cloud Service Agreement](https://www.ibm.com/terms/?id=Z126-6304_WS).
