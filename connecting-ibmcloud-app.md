---

copyright:
  years: 2018, 2025
lastupdated: "2025-01-10"

keywords: postgresql, databases, edb, enterprisedb connection strings, enterprisedb

subcollection: databases-for-enterprisedb

---

{{site.data.keyword.attribute-definition-list}}

# Connecting an {{site.data.keyword.cloud_notm}} application
{: #ibmcloud-app}

{{site.data.keyword.databases-for-enterprisedb}} is deprecated. As of 16 June 2025 you can't deploy new instances. Existing instances are supported until 15 October 2025. Any instances that still exist on that date will be deleted. For more information, see [Deprecation of {{site.data.keyword.databases-for-enterprisedb}}](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-deprecation).
{: deprecated}

Applications running in {{site.data.keyword.cloud_notm}} can be bound to your {{site.data.keyword.databases-for-enterprisedb_full}} deployment. 

## Connecting a Kubernetes Service application
{: #kub-app}

There are two steps to connecting a Cloud databases deployment to a Kubernetes Service application. First, your deployment needs to be bound to your cluster and its connection strings that are stored in a secret. Second, configure your application to use the connection strings.

The sample app in the [Connecting a Kubernetes Service Tutorial](/docs/databases-for-enterprisedb?topic=cloud-databases-tutorial-k8s-app) provides a sample application that uses Node.js and demonstrates how to bind the sample application to a {{site.data.keyword.databases-for}} deployment.
{: .tip}

Before connecting your Kubernetes Service application to a deployment, make sure that the deployment and cluster are both in the same region and resource group.

### Binding your deployment
{: #binding-deployment}

**Public Endpoints** -  If you are using the default public service endpoint to connect to your deployment, you can run the `cluster service bind` command with your cluster name, the resource group, and your deployment name.
```sh
ibmcloud ks cluster service bind <your_cluster_name> <resource_group> <your_database_deployment>
```
OR
**Private Endpoints** - If you want to use a private endpoint (if one is enabled on your deployment), then first you need to create a service key for your database so Kubernetes can use it when binding to the database. 
```sh
ibmcloud resource service-key-create <your-private-key> --instance-name <your_database_deployment> --service-endpoint private  
```
The private service endpoint is selected with `--service-endpoint private`. After that, you bind the database to the Kubernetes cluster through the private endpoint with the `cluster service bind` command.
```sh
ibmcloud ks cluster service bind <your_cluster_name> <resource_group> <your_database_deployment> --key <your-private-key>
```

**Verify** - Verify that the Kubernetes secret was created in your cluster namespace. Running the following command, you get the API key for accessing the instance of your deployment provisioned in your account.
```sh
kubectl get secrets --namespace=default
```
More information on binding services is found in the [Kubernetes Service documentation](/docs/containers?topic=containers-service-binding#bind-services).

### Configuring in your Kubernetes app 
{: #config-kub-app}

When you bind your application to Kubernetes Service, it creates an environment variable from the cluster's secrets. Your deployment's connection information lives in `BINDING` as a JSON object. Load and parse the JSON object into your application to retrieve the information your application's driver needs to make a connection to the database. 

The [Connection Strings](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-connection-strings#connection-string-breakdown) page contains a reference of the JSON fields.

For more information, see the [Kubernetes Service docs](https://cloud.ibm.com/docs/containers?topic=containers-service-binding#reference_secret).
