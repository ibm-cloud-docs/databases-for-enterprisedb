---

copyright:
  years: 2018, 2025
lastupdated: 2025-03-19

keywords: databases-for-enterprisedb release notes

subcollection: databases-for-enterprisedb

content-type: release-note

---

{{site.data.keyword.attribute-definition-list}}

# Release notes
{: #enterprisedb-relnotes}

Use these release notes to learn about the latest updates to {{site.data.keyword.databases-for-enterprisedb_full}} that are grouped by _date_ or _build number_.
{: shortdesc}

## 10 March 2025
{: #databases-for-enterprisedb-10mar2025}
{: release-note}

{{site.data.keyword.databases-for-enterprisedb}} {{site.data.keyword.satelliteshort}} plan is deprecated
:   {{site.data.keyword.satellitelong}} is now deprecated due to changes in market expectations, client fit, and lack of adoption. As of March 10 2025, all documentation relating to Satellite has been removed, as well as the ability to select {{site.data.keyword.databases-for-enterprisedb}} {{site.data.keyword.satelliteshort}} plan in the Cloud console.

## 17 January 2025
{: #databases-for-enterprisedb-16jan2025}
{: release-note}

Deprecation
:  {{site.data.keyword.databases-for-enterprisedb}} is deprecated. As of 16 June 2025 you can't deploy new instances. Existing instances are supported until 15 October 2025. Any instances that still exist on that date will be deleted. For more information, see [Deprecation of {{site.data.keyword.databases-for-enterprisedb}}](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-deprecation).


## 15 November 2024
{: #databases-for-enterprisedb-15nov2024}
{: release-note}

{{site.data.keyword.databases-for}} logs and events are now available on {{site.data.keyword.logs_full}}
: {{site.data.keyword.databases-for}} has onboarded {{site.data.keyword.logs_full_notm}}, a scalable logging service that persists logs and provides users with capabilities for querying, tailing, and visualizing logs. Customers are expected to use {{site.data.keyword.logs_full_notm}} to review their database logs and events starting **November 15, 2024**. For more information, see [Set up logging and monitoring](/docs/databases-for-elasticsearch?topic=databases-for-enterprisedb-getting-started-cdb-logging-monitoring) and [About IBM Cloud Logs](/docs/cloud-logs?topic=cloud-logs-about-cl).

## 16 September 2024
{: #databases-for-enterprisedb-16sept2024}
{: release-note}

Private endpoints as new default
:  To ensure best possible security for your databases, private endpoints are now the default in the {{site.data.keyword.cloud}} console. CLI and Terraform now require the endpoint type to be provided as part of creating an instance.

## 1 May 2024
{: #databases-for-enterprisedb-01may2024}
{: release-note}

New hosting models
:  You can choose between two hosting models: Isolated Compute and Shared Compute. Isolated Compute is a secure single-tenant offering for complex, highly performant enterprise workloads. Shared Compute is a flexible multi-tenant offering for dynamic, fine-tuned, and decoupled capacity selections. For more information, see [Hosting models](/docs/cloud-databases?topic=cloud-databases-hosting-types){: external}.

## 22 November 2023
{: #databases-for-enterprisedb-22nov2023}
{: release-note}

Monitoring Integration documentation updated
:  Monitoring Integration documentation now lists metrics for all {{site.data.keyword.databases-for}} services. For more information, see [Monitoring Integration](/docs/cloud-databases?topic=cloud-databases-monitoring){: external}.

## 23 May 2023
{: #databases-for-enterprisedb-23may2023}
{: release-note}

Setting up disk alerts for disk utilization tutorial
:  In this tutorial, you use the {{site.data.keyword.cloud_notm}} API and the [{{site.data.keyword.cloud_notm}} CLI](https://cloud.ibm.com/docs/cli?topic=cli-getting-started){: external} to set up an alert that emails you whenever the disk utilization of your database exceeds 90%. This specific example creates an alert on a {{site.data.keyword.databases-for-elasticsearch}} deployment, but it is applicable to all the databases in the IBM {{site.data.keyword.databases-for}} catalog. For more information, see [Setting up disk alerts for disk utilization](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-disk-util-alert-tutorial).

## 19 October 2022
{: #databases-for-enterprisedb-19oct2022}
{: release-note}

Deploying and Connecting a Cloud Databases Instance Tutorial
:  This tutorial guides you through the process of deploying a {{site.data.keyword.databases-for}} instance and connecting it to a web front end by creating a webpage that allows visitors to input a word and its definition. These values are then stored in a database running on {{site.data.keyword.databases-for}}. You install the database infrastructure by using Terraform and your web application uses the popular Express framework. The application can then be run locally, or by using Docker. For more information, see [Deploying and Connecting a {{site.data.keyword.databases-for}} Instance](/docs/databases-for-enterprisedb?topic=cloud-databases-create-instance-tutorial).

## 11 October 2022
{: #databases-for-enterprisedb-11oct2022}
{: release-note}

Protecting {{site.data.keyword.databases-for-enterprisedb_full}} resources with context-based restrictions
:  Context-based restrictions (CBR) give account owners and administrators the ability to define and enforce access restrictions for {{site.data.keyword.cloud}} resources based on the context of access requests. Access to {{site.data.keyword.databases-for}} resources can be controlled with CBR and identity and access management (IAM) policies. For more information, see [Protecting Cloud Databases resources with context-based restrictions](/docs/databases-for-enterprisedb?topic=cloud-databases-cbr&interface=ui).

## 29 March 2022
{: #databases-for-enterprisedb-29mar2022}
{: release-note}

{{site.data.keyword.databases-for-enterprisedb_full}} supports synchronous replication
:  {{site.data.keyword.databases-for-enterprisedb_full}} now supports synchronous replication. See documentation [here](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-high-availability#sync-repl).

## 06 July 2020
{: #databases-for-enterprisedb-06jul2020}
{: release-note}

General Availability of {{site.data.keyword.databases-for-enterprisedb_full}}
:  {{site.data.keyword.databases-for-enterprisedb_full}} added to the [IBM Cloud Databases](https://www.ibm.com/cloud/databases) family. See blog post announcement [here](https://www.ibm.com/cloud/blog/announcements/ibm-cloud-databases-for-enterprisedb).
