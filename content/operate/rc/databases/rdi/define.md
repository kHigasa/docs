---
Title: Define data pipeline
alwaysopen: false
categories:
- docs
- operate
- rc
description: Define the source connction and data pipeline.
hideListLinks: true
weight: 2
---

After you have [prepared your source database]({{<relref "/operate/rc/databases/rdi/setup">}}) and connection information, you can set up your new pipeline. To do this:

1. [Define the source connection](#define-source-connection) by entering all required source database information.
2. [Define the data pipeline](#define-data-pipeline) by selecting the data that you want to sync from your source database to the target database.

## Define source connection

1. In the [Redis Cloud console](https://cloud.redis.io/), go to your target database and select the **Data Pipeline** tab.
1. Select **Define source database**.
    {{<image filename="images/rc/rdi/rdi-define-source-database.png" alt="ADD ALT TEXT" >}}
1. Enter a **Pipeline name**. This pipeline name will be the prefix to all keys generated by this pipeline in the target database.
    {{<image filename="images/rc/rdi/rdi-define-pipeline-cidr.png" alt="ADD ALT TEXT" >}}
1. Enter the **Deployment CIDR** for your pipeline, or use the one generated for you. This CIDR should not conflict with your apps or other databases.
1. In the **Source database connectivity** section, enter the **PrivateLink service name** of the [PrivateLink connected to your source database]({{< relref "/operate/rc/databases/rdi/setup#set-up-connectivity" >}}).
    {{<image filename="images/rc/rdi/rdi-define-connectivity.png" alt="ADD ALT TEXT" >}}
1. Enter your database details. This depends on your database type, and includes:
    - **Port**: The database's port
    - **Database**: Your database's name, or the root database *(PostgreSQL, Oracle only)*, or a comma-separated list of one or more databases you want to connect to *(SQL Server only)*
    - **Database Server ID**: Unique ID for the replication client. Leave as default if you don't use replication *(mySQL and mariaDB only)*
    - **PDB**: Name of the Oracle pluggable database *(Oracle only)*
1. Enter the ARN of your [database credentials secret]({{< relref "/operate/rc/databases/rdi/setup#share-source-database-credentials" >}}) in the **Source database secrets ARN** field.
1. Select **Start pipeline setup**.
1. Redis Cloud will attempt to connect to PrivateLink. Accept the incoming connection on AWS PrivateLink to proceed. See [Accept or Reject PrivateLink connection requests](https://docs.aws.amazon.com/vpc/latest/privatelink/configure-endpoint-service.html#accept-reject-connection-requests).

    If Redis Cloud can't find your PrivateLink connection, make sure that the PrivateLink service name is correct and that Redis Cloud is listed as an Allowed Principal for your VPC. See [Set up connectivity]({{<relref "/operate/rc/databases/rdi/setup#set-up-connectivity">}}) for more info.

At this point, Redis Cloud will provision the pipeline infrastructure that will allow you to define your data pipeline. 

{{<image filename="images/rc/rdi/rdi-pipeline-setup-in-progress.png" alt="ADD ALT TEXT" >}}

Pipelines are provisioned in the background. You aren't allowed to make changes to your data pipeline or to your database during provisioning. This process will take a long time, so you can close the window and come back later.

When your pipeline is provisioned, select **Complete setup**. You will then [define your data pipeline](#define-data-pipeline).

{{<image filename="images/rc/rdi/rdi-complete-setup.png" alt="ADD ALT TEXT" >}}

## Define data pipeline

After your pipeline is provisioned, you will be able to define your pipeline. You will select the database schemas and columns that you want to import and synchronize with your primary database.

### Configure a new pipeline

1. In the [Redis Cloud console](https://cloud.redis.io/), go to your target database and select the **Data Pipeline** tab. If your pipeline is already provisioned, select **Complete setup** to go to the **Pipeline definition** section.
    {{<image filename="images/rc/rdi/rdi-complete-setup.png" alt="ADD ALT TEXT" >}}
1. For the **Configure a new pipeline** option, select the Redis data type to write keys to the target. You can choose **Hash** or **JSON**. 
    {{<image filename="images/rc/rdi/rdi-configure-new-pipeline.png" alt="ADD ALT TEXT" >}}
    Select **Continue**.
    {{<image filename="images/rc/rdi/rdi-continue-button.png" alt="ADD ALT TEXT" >}}
1. Select the Schema and Tables you want to migrate to the target database from the **Source data selection** list. 
    {{<image filename="images/rc/rdi/rdi-select-source-data.png" alt="ADD ALT TEXT" >}}

    You can select any number of columns from a table.

    {{<image filename="images/rc/rdi/rdi-select-columns.png" alt="ADD ALT TEXT" >}}

    If any tables are missing a unique constraint, the **Missing unique constraint** list will appear. Select the columns that define a unique constraint for those tables from the list.

    {{<image filename="images/rc/rdi/rdi-missing-unique-constraint.png" alt="ADD ALT TEXT" >}}

    {{<image filename="images/rc/rdi/rdi-select-constraints.png" alt="ADD ALT TEXT" >}}

    Select **Add schema** to add additional database schemas.

    {{<image filename="images/rc/rdi/rdi-add-schema.png" alt="ADD ALT TEXT" >}}
    
    Select **Delete** to delete a schema. You must have at least one schema to continue.

    {{<image filename="images/rc/rdi/rdi-delete-schema.png" alt="ADD ALT TEXT" >}}

    After you've selected the schemas and tables you want to sync, select **Continue**.

    {{<image filename="images/rc/rdi/rdi-continue-button.png" alt="ADD ALT TEXT" >}}

1. Review the tables you selected in the **Summary**. If everything looks correct, select **Start ingest** to start ingesting data from your source database.

    {{<image filename="images/rc/rdi/rdi-start-ingest.png" alt="ADD ALT TEXT" >}}

At this point, the data pipeline will ingest data from the source database to your target Redis database. This process will take time, especially if you have a lot of records in your source database. 

After this initial sync is complete, the data pipeline enters the *change streaming* phase, where changes are captured as they happen. Changes in the source database are added to the target within a few seconds of capture. 

You can view the status of your data pipeline in the **Data pipeline** tab of your database. See [View and edit data pipeline]({{<relref "/operate/rc/databases/rdi/view-edit">}}) to learn more.