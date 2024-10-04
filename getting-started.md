---

copyright:
  years: 2018, 2024
lastupdated: "2024-09-30"

keywords: pgAdmin, postgresql gui, edb, enterprisedb connection information, enterprisedb

subcollection: databases-for-enterprisedb

content-type: tutorial
account-plan: paid
completion-time: 30m

---

{{site.data.keyword.attribute-definition-list}}

# Getting Started
{: #getting-started}
{: toc-content-type="tutorial"}
{: toc-completion-time="30m"}

This tutorial is a short introduction to using an {{site.data.keyword.databases-for-enterprisedb_full}} deployment. [pgAdmin](https://www.pgadmin.org/){: external} is an open source administration platform for PostgreSQL, and provides many tools for managing your data and databases. [Download and install](https://www.pgadmin.org/download/){: external} the version that is appropriate to your environment, and then follow the steps to connect it to your {{site.data.keyword.databases-for-enterprisedb}} deployment.

## Before you begin
{: #before-begin}

- You need to have an [{{site.data.keyword.cloud_notm}} account](https://cloud.ibm.com/registration){: .external}.
- You also need a {{site.data.keyword.databases-for-enterprisedb}} deployment. You can provision one from the [{{site.data.keyword.cloud_notm}} catalog](https://cloud.ibm.com/catalog/services/databases-for-enterprisedb){: external}. Give your deployment a memorable name that appears in your account's Resource List.
- [Set the Admin Password](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-user-management&interface=ui#user-management-set-admin-password-ui) for your deployment.
- Install [pgAdmin4](https://www.pgadmin.org/download/){: external}.
- Review [`Getting to production`](/docs/cloud-databases?topic=cloud-databases-best-practices) for general guidance on setting up a basic {{site.data.keyword.databases-for-enterprisedb_full}} deployment.

## Connect with pgAdmin
{: #connecting-pqadmin}
{: step}

pgAdmin runs as a server and you connect to it through a browser. When the server is started, it runs on localhost at default `http://127.0.0.1:53113/browser/`.

When you first open pgAdmin, you are prompted to set a `primary password`. This password differs from your deployment's password and is used specifically by pgAdmin to store passwords to your {{site.data.keyword.databases-for-enterprisedb}} servers or deployments.

From the _Dashboard_ on the _Welcome_ screen, click _Add New Server_.

Provide pgAdmin with the information needed to connect to your deployment. 

First, complete the _Connection_ information, 
- For _Host name/address_, use the _Hostname_ of your deployment.
- For the _Port_, use the _Port_ of your deployment.
- The _Maintenance database_ remains `postgres`.
- For _Username_ and _Password_, use the `admin` credentials that you set after provisioning your deployment. You can choose to have pgAdmin save the password.
- The _Role_ and _Service_ fields can be left empty.

Then, configure the _SSL_ settings.
- Copy the certificate information from the _Endpoints_ pane.
- Save the certificate to a file. (You can use the name that is provided in the download, or your own file name.)
- Set the _SSL mode_ field to _Verify-Full_.
- In the _Root certificate_ field, select the file where you saved your deployment's certificate.

Back in your deplyment's _General_ tab, give your deployment a name and add any comments to describe or identify your deployment in pgAdmin.

If the _Connect now?_ field is checked, pgAdmin attempts to connect to your deployment when you click **Save**.

## Use pgAdmin
{: #using-pqadmin}
{: step}

When pgAdmin connects, your deployment appears in the _Servers_ list and you get a _Dashboard_ with information and statistics. 

In the list of databases in the _Browser_, you see both the `postgres` database, which you are connected to, and the `ibmclouddb` database, which is the default database for all {{site.data.keyword.databases-for-enterprisedb}} deployments. Click `ibmclouddb` to connect to it and expand the information about it.

Now you can use pgAdmin to view, administer, and manage your data and databases in your {{site.data.keyword.databases-for-enterprisedb}} deployment. A complete guide can be found in the [pgAdmin documentation](https://www.pgadmin.org/docs/pgadmin4/latest/index.html){: external}.

Administrative features that require a superuser are not available through pgAdmin because superuser access is not available to users of a {{site.data.keyword.databases-for-enterprisedb}} deployment.
{: .tip}

## Next Steps
{: #getting-started-next-steps}

If you are using PostgreSQL for the first time, take a tour through the official [PostgreSQL documentation](https://www.postgresql.org/docs/){: external}. 

You can connect to and manage your databases and data with PostgreSQL's command-line tool [`psql`](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-connecting-psql).

Looking for more tools on managing your deployment? Connect to your deployment with the [IBM Cloud CLI](/docs/cli?topic=cli-install-ibmcloud-cli) using the [Cloud Databases CLI plug-in](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference), or the [Cloud Databases API](https://cloud.ibm.com/apidocs/cloud-databases-api).

If you plan to use {{site.data.keyword.databases-for-enterprisedb}} for your applications, check out [Connecting an external application](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-external-app) and [Connecting an IBM Cloud application](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-ibmcloud-app).

To ensure the stability of your applications and your database, check out  [High-Availability](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-high-availability) and [Performance](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-performance).
