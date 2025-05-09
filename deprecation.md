---

copyright:
  years: 2025
lastupdated: "2025-05-09"

keywords: deprecation, migration

subcollection: databases-for-enterprisedb

---

{{site.data.keyword.attribute-definition-list}}

# Deprecation of {{site.data.keyword.databases-for-enterprisedb}}
{: #deprecation}

{{site.data.keyword.cloud}} is announcing the full deprecation of {{site.data.keyword.databases-for-enterprisedb_full}} on 15 October 2025. After this date, any deployments of {{site.data.keyword.databases-for-enterprisedb}} that are still running will be permanently disabled and deprovisioned.

The following describes the details of the deprecation, possible migration targets for your applications, and additional information.
{: shortdesc}

## Important dates
{: #deprecation-timeline}

| Stage | Date | Description |
| ---------------- | ----------------- | ------------------------------------------------------------ |
| Deprecation announcement | 17 January 2025 | Announcement of the {{site.data.keyword.databases-for-enterprisedb}} deprecation. Existing instances will continue to run. |
| End of marketing | 16 June 2025 | No new instances of {{site.data.keyword.databases-for-enterprisedb}} can be created or purchased. Existing instances will continue to run. |
| End of support   | 15 October 2025 | Support is no longer available. Running instances of {{site.data.keyword.databases-for-enterprisedb}} are permanently disabled and deprovisioned. |
{: caption="Deprecation timeline" caption-side="bottom"}

## Deprecation details
{: #deprecation-details}

Review the following details about the {{site.data.keyword.databases-for-enterprisedb}} deprecation:

- A *deprecation* is a process, and we announced the beginning of that process. It begins with the *announcement* (this page), continues with *end of marketing,* 
and ends with *end of support.* Nothing happens until those announced dates above.

- At the *end of marketing,* users who don't have {{site.data.keyword.databases-for-enterprisedb}} running workloads will be blocked from deploying new applications. 
However, all users {{site.data.keyword.databases-for-enterprisedb}} instances deployed can keep using them and restore from backups if necessary.

- At the *end of support* stage, all {{site.data.keyword.databases-for-enterprisedb}} deployments will be disabled and deleted.

- All other services you have been using in {{site.data.keyword.cloud_notm}} are unaffected — the {{site.data.keyword.databases-for-enterprisedb}} deprecation refers only to the specific 
{{site.data.keyword.databases-for-enterprisedb}} deployed instances.

- Reminders of the approaching *end of support* date will continue to be sent periodically by email to all users that have {{site.data.keyword.databases-for-etcd}} deployments.

## Migration to {{site.data.keyword.databases-for-postgresql}}
{: #migration-pg}

Migrating to {{site.data.keyword.databases-for-postgresql}} using a logical backup and restore approach is the supported method for migrating data from {{site.data.keyword.databases-for-enterprisedb}} to {{site.data.keyword.databases-for-postgresql}}. The simplest and most effective method is creating a plain SQL dump using pg_dump and restoring it with psql. No special tooling is required, and the same approach can be used whether migrating between self-hosted or cloud-managed environments.

### Before you start
{: #migration-pg-before-start}

- Target PostgreSQL version(s): Ensure your target PostgreSQL instance is version 14 or higher. PostgreSQL 13 has already been announced as deprecated. Hence, this strategy applies when migrating from {{site.data.keyword.databases-for-enterprisedb}} 12 to {{site.data.keyword.databases-for-postgresql}} versions 14 through 17.
- ICD backup incompatibility: This migration approach is incompatible with proprietary ICD backups. Refer to the supplementary notes below for guidance on the logical backup and restore approach.
- Ensure that the source database does not use Oracle-style compatibility features or EDB proprietary objects.

### `pg_dump`
{: #migration-pg_dump}

On your source {{site.data.keyword.databases-for-enterprisedb} database, run `pg_dump` to create a plain SQL file that can be used to recreate the schema and data.

At a minimum, `pg_dump` takes:

- Hostname (-h)
- Port (-p)
- Database name (-d)
- Username (-U)
- File output path (-f)
  
Additional options:

- Exclude system schema (-N sys): In EDB, the sys schema contains system-level Oracle-compatible functions and views (such as dual and dbms_output), which:

  - Don’t exist in PostgreSQL.
  - Will cause errors when restoring the dump to a plain PostgreSQL instance.

Example:

```
pg_dump -h <edb-source-instance>.databases.appdomain.cloud -p 51030 -U admin -d ibmclouddb -f edb_dump.sql -N sys
```

The `pg_dump` command has many options, and it is recommended that you refer to the [official documentation](https://www.postgresql.org/docs/current/app-pgdump.html) for a complete list of capabilities.

### Restoring `pg_dump`'s output
{: #migration-restoring-pg_dump}

The resulting output of `pg_dump` (i.e., edb_dump.sql) can then be uploaded into a PostgreSQL deployment.
Since it is plain SQL, it can be streamed directly to the database via psql.

Execute dump with psql, as in the following example:

```
psql -h <pg-target-instance>.databases.appdomain.cloud -p 32012 -U admin -d ibmclouddb -f edb_dump.sql
```

The psql tool reads and executes the SQL statements in the file. Ensure that you have proper privileges.

#### Notes
{: #migration-restoring-pg_dump-notes}

- This example assumes no Oracle-style compatibility or proprietary EDB objects.
- For complex schemas and fine-grained access control, consider reviewing --no-owner, --no-privileges, and role creation separately. Refer to the official documentation for more details.
- If you prefer other dump formats, `pg_dump` also supports TAR format (-F t) and others.

For more information, see [pg_dump documentation](https://www.postgresql.org/docs/current/app-pgdump.html).

### Using pg_restore for restoring the pg_dump's Output

If you used TAR or Directory formats (-F t or -F d), you must use pg_restore instead of psql.

```
pg_restore -h <pg-target-instance>.databases.appdomain.cloud -p 32012 -U admin -d ibmclouddb -F t edb_dump.tar
```

For advanced options, see the [pg_restore documentation](https://www.postgresql.org/docs/current/app-pgrestore.html).

Submit a [support ticket](https://cloud.ibm.com/login?redirect=%2Funifiedsupport%2Fsupportcenter) if you have any questions.

## Next steps for current users
{: #deprecation-next-steps}

The following is a suggested checklist to plan your migration:

- Understand and plan for the timeline and key milestone dates.

- Evaluate the {{site.data.keyword.cloud_notm}} database migration options.

- Pick an {{site.data.keyword.cloud_notm}} service migration database target that best meets your requirements and goals.

- IBM recommends [ICD PostgreSQL](https://cloud.ibm.com/databases/databases-for-postgresql/create) for customer consideration. 
Also, refer to our [blog post](https://www.ibm.com/blog/announcement/breaking-boundaries-postgresql-16-is-now-available-on-ibm-cloud/) for detailed information on the latest PostgreSQL version.

- Migrate your data to the target {{site.data.keyword.cloud_notm}} service and compare the operation of your applications.

- When ready, move application traffic as appropriate.
