---

copyright:
  years: 2025
lastupdated: "2025-01-17"

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
