---
copyright:
  years: 2017, 2020
lastupdated: "2020-06-30"

keywords: postgresql drivers, python, java, javascript, certificate, edb, enterprisedb

subcollection: databases-for-enterprisedb

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:generic: .ph data-hd-programlang='generic'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:javascript: .ph data-hd-programlang='javascript'}
{:pre: .pre}

# Connecting an external application
{: #external-app}

Your applications and drivers use connection strings to make a connection to {{site.data.keyword.databases-for-enterprisedb_full}}. The service provides connection strings specifically for drivers and applications. Connection strings are displayed in the _Endpoints_ pane of your deployment's _Overview_, and can also be retrieved from the [cloud databases CLI plug-in](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-connections), and the [API](https://{DomainName}/apidocs/cloud-databases-api#discover-connection-information-for-a-deployment-f-e81026).

The connection strings can be used by any of the credentials you created on your deployment. While you can use the admin user for all of your connections and applications, it might be better to create users specifically for your applications to connect with. Documentation on generating credentials is on the [Creating Users and Getting Connection Strings](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-connection-strings) page.

## Connecting with a language's driver

All the information a driver needs to make a connection to your deployment is in the "postgres" section of your connection strings. The table contains a breakdown for reference.

Field Name|Index|Description
----------|-----|-----------
`Type`||Type of connection - for {{site.data.keyword.databases-for-enterprisedb}}, it is "URI"
`Scheme`||Scheme for a URI - for {{site.data.keyword.databases-for-enterprisedb}}, it is "postgresql"
`Path`||Path for a URI - for {{site.data.keyword.databases-for-enterprisedb}}, it is the database name. The default is `ibmclouddb`.
`Authentication`|`Username`|The username that you use to connect.
`Authentication`|`Password`|A password for the user - might be shown as `$PASSWORD`
`Authentication`|`Method`|How authentication takes place; "direct" authentication is handled by the driver.
`Hosts`|`0...`|A hostname and port to connect to
`Composed`|`0...`|A URI combining Scheme, Authentication, Host, and Path
`Certificate`|`Name`|The allocated name for the self-signed certificate for database deployment
`Certificate`|Base64|A base64 encoded version of the certificate.
{: caption="Table 1. `postgres`/`URI` connection information" caption-side="top"}

* `0...` Indicates that there might be one or more of these entries in an array.



Many PostgreSQL drivers are able to make a connection to your deployment when given the URI-formatted connection string found in the "composed" field of the connection information. For example,
{: generic}
```
postgres://ibm_cloud_30399dec_4835_4967_a23d_30587a08d9a8:$PASSWORD@981ac415-5a35-4ac7-b6bb-fb609326dc42.8f7bfd8f3faa4218aec56e069eb46187.databases.appdomain.cloud:32704/ibmclouddb?sslmode=verify-full
```
{: generic}



This example uses the information from your connection string and the Java driver [`jdbc`](https://jdbc.postgresql.org/documentation/head/index.html) to connect to your database.
{: java}

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;
import java.util.logging.*;

public class PGConnect {

    private final static Logger LOGGER = Logger.getLogger(Logger.GLOBAL_LOGGER_NAME);

    private Connection connect() {

        final String url = "jdbc:postgresql://host:port/ibmclouddb";

        Properties props = new Properties();
        props.setProperty("user","admin");
        props.setProperty("password","mypassword123");
        props.setProperty("ssl","true");
        props.setProperty("sslmode","verify-full");
        props.setProperty("sslrootcert", "/path/to/cert");

        Connection conn = null;
        while (conn == null) {
            try {
                conn = DriverManager.getConnection(url, props);
                System.out.println("Connected to PG");
            } catch (SQLException e) {
                System.out.printf("%s\n", e);
                LOGGER.info("Not connected, retying ...");
            }
        }

        return conn;

    }

    public static void main(String[] args) {

        PGConnect icd = new PGConnect();

        try {
            Connection connection = icd.connect();
            Statement stmt = connection.createStatement();

            ResultSet rs = stmt.executeQuery("SELECT * from pg_database");
            while (rs.next()) {
                System.out.println("DB Name: " + rs.getString(1));
            }

        } catch (SQLException e) {
            System.out.println(e.getMessage());
        }   
    }
}
```
{: java}



This example uses the information from your connection string and the Python driver [`Psycopg2`](https://wiki.postgresql.org/wiki/Psycopg2_Tutorial) to connect to your database. This is just a simple connection example, without error handling or retry logic and might not be suitable for production.
{: python}

```python
import psycopg2

try:
  conn = psycopg2.connect(
    host="hostname.databases.appdomain.cloud",
    port= 31525,
    user="username",
    password="password",
    sslmode="verify-full",
    sslrootcert="/path/to/cert/ca-certificate.crt",
    database="ibmclouddb")
except: 
  print("Unable to connect to database")

cur = conn.cursor()
cur.execute("SELECT datname FROM pg_database")
rows = cur.fetchall()

print("List of databases:")
for row in rows:
    print("  ",row[0])
```
{: python}


This example uses the information from your connection string and the Node driver [`node-postgres`](https://node-postgres.com/) to connect to your database.
{: javascript}

```javascript
const pg = require("pg");
const fs = require("fs");

let connectionString = "postgres://<username>:<password>@<host>:<port>/<database>?sslmode=verify-full";
let caCert = fs.readFileSync('/path/to/cert');

// set up a client with your {{site.data.keyword.databases-for-enterprisedb}} connection string
let client = new pg.Client({ connectionString: connectionString,
    // set up the TLS options
    ssl: {
        ca: caCert,
        rejectUnauthorized: true
    }
 });

 client.connect(function(err) {
    if (err) {
        console.log(err);
        process.exit(1);
    } else {
        // query for the names of the databases
        client.query(
            "SELECT datname FROM pg_database;",
            function(err, result) {
                if (err) {
                    console.log(err);
                }
                // return the names of the databases
                console.log(result.rows);
                client.end();
            }
        );
    }
});
```
{: javascript}


## Driver TLS and self-signed certificate support

All connections to {{site.data.keyword.databases-for-enterprisedb}} are TLS 1.2 enabled, so the driver you use to connect needs to be able to support encryption. Your deployment also comes with a self-signed certificate so the driver can verify the server upon connection. 

### Using the self-signed certificate

1. Copy the certificate information from the _Endpoints_ pane or the Base64 field of the connection information. 
2. If needed, decode the Base64 string into text. 
3. Save the certificate to a file. (You can use the name that is provided, or your own file name).
4. Provide the path to the certificate to the driver or client.

![CLI Endpoints panel](images/cli-endpoints-pane.png)
### CLI plug-in support for the self-signed certificate

You can display the decoded certificate for your deployment with the CLI plug-in with the command `ibmcloud cdb deployment-cacert "your-service-name"`. It decodes the base64 into text. Copy and save the command's output to a file and provide the file's path to the driver.

## Other Drivers

PostgreSQL has a vast array of language drivers that can also be used to connect to a {{site.data.keyword.databases-for-enterprisedb}} deployment. The table covers a few of the most common.

Language|Driver|Examples
----------|----------|-----------
PHP|`pgsql`|[Link](http://php.net/manual/en/pgsql.examples-basic.php)
Ruby|`ruby-pg`|[Link](https://github.com/ged/ruby-pg)
Ruby on Rails|Rails|[Rails Guide](http://edgeguides.rubyonrails.org/configuring.html#configuring-a-postgresql-database)
C#|`ODBC`|[Link](https://wiki.postgresql.org/wiki/Using_Microsoft_.NET_with_the_PostgreSQL_Database_Server_via_ODBC)
Go|`pq`|[Link](https://godoc.org/github.com/lib/pq)
Node|`node-postgres`|[Link](https://node-postgres.com/)
JDBC| EDB Postgres JDBC Connector|[JDBC Guide](https://www.enterprisedb.com/edb-docs/d/jdbc-connector/user-guides/jdbc-guide/42.2.8.1/using_the_advanced_server_jdbc_connector_with_java_applications.html)
{: caption="Table 2. PostgreSQL drivers" caption-side="top"}
