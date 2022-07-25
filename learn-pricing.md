---
copyright:
  years: 2017, 2022
lastupdated: "2022-07-25"

keywords: postgresql, databases, pricing, resources, scaling, edb, enterprisedb

subcollection: databases-for-enterprisedb

---

{:external: .external target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}


# Pricing
{: #pricing}

A {{site.data.keyword.databases-for-enterprisedb_full}} Standard plan deploys as one highly available cluster with three data members. Your data is replicated on all three members. The Standard plan is priced based on the total amount of disk storage, RAM, dedicated cores, and backup storage that is allocated to deployments, prorated hourly. {{site.data.keyword.databases-for-enterprisedb}} deployments have a minimum of 20 GB of disk and 1 GB of RAM per data member.

## Cost Breakdown
{: #cost}

**Disk storage per data member** - gigabytes of disk that are allocated to a {{site.data.keyword.databases-for-enterprisedb}} data member, or the size of your data.  
**RAM per data member** - gigabytes of RAM that are allocated to a {{site.data.keyword.databases-for-enterprisedb}} data member.  
**Backup storage** - amount of storage used for backups by a {{site.data.keyword.databases-for-enterprisedb}} deployment.  
**Virtual processor core** - number of virtual processor cores that are allocated to a {{site.data.keyword.databases-for-enterprisedb}} data member.

![Standard pricing](images/standard-pricing.png){: caption="Figure 1. Standard pricing" caption-side="bottom"}

| Resources | Breakdown | Price |
| ------- | ------- | ------- |
| 20 GB-Month disk | 3 members x 20 GB x $0.58 | $34.80 |
| 20 GB-Month backup| 3 members x 20 GB x $0.03| $1.80 |
| 1 GB-Month RAM | 3 members x 1 GB x $11.50 | $34.50 |
| 3 Virtual processor Cores | 3 members x 3 cores x $100 | $900 |
{: caption="Table 1. Pricing example for two data members" caption-side="top"}

Total per month = $971.10/Month  
Total per hour = $1.35/Hour

All prices here are in US dollars. To see pricing in your local currency, you can to use the pricing calculator.
{: .tip}

## Using the Pricing Calculator
{: #pricing-calc}

Templates are provided for ease of use and provide balanced resource allocations appropriate for general purpose workloads. The **Custom** tab can be used to configure Disk, RAM, and vCPU, as desired.

For pricing estimation, use the **Add to Estimate** button on the [{{site.data.keyword.databases-for-enterprisedb}} catalog page](https://cloud.ibm.com/catalog/databases-for-enterprisedb). Input your total consumption across three data members into the calculator. This is roughly tripled the size of your data because your data is replicated to all three members. For example, 20 GB of disk and 1 GB of RAM across two data members would be priced at 60 GB of disk and 3 GB of RAM respectively. 

![Pricing calculator estimation with 20 GB of disk and 1 GB of RAM, per member](images/pricing-estimate.png){: caption="Figure 2. Pricing calculator estimation" caption-side="bottom"}

## Backups Pricing
{: #pricing-backup}

Users also receive their total disk space purchased, per database, in free backup storage. For example, in a given month, if you have a {{site.data.keyword.databases-for-enterprisedb}} deployment that has 20 GB of disk per member, and has three data members, you receive 60 GB of backup storage free for that month. If your backup storage utilization is greater than 60 GB for the month in this scenario, each gigabyte is charged at an overage $0.03/month. Most deployments will not exceed the allotted credit.

## Dedicated Cores Pricing
{: #pricing-cores}

With dedicated cores, your resource group is given a single-tenant host with a guaranteed minimum reserve of cpu shares. Your deployments are then allocated the number of CPUs you specify. The cost of dedicated cores is $100 per core per month, and each member gets the selected number of cores. For example, if you provision a deployment with 3 dedicated cores per member, that is a total of 9 cores, and billed at $900 per month. 

## Scaling per Member
{: #pricing-member}

{{site.data.keyword.databases-for-enterprisedb}} deployments have minimum and maximum allocation for disk and RAM as shown. Scaling deployments through the API/CLI provides more granularity and also allows a user to scale a database instance up to 4 TB of disk per member.

| Resource | Minimum | Maximum | Scaling Granularity (API/CLI) |
| ---------- | ----- | ----- | ------- |
| Disk | 20 GB per member | 4 TB per member | 1024 MB per member |
| RAM | 1 GB per member | 112 GB per member | 128 MB per member |
| CPU (if enabled) | 3 CPUs per member | 28 CPUs per member| 1 CPU per member |
{: caption="Table 2. Per Member Scaling Limits" caption-side="top"}
