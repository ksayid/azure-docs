---
title: Copy data from MySQL
titleSuffix: Azure Data Factory & Azure Synapse
description: Learn about MySQL connector in Azure Data Factory and Synapse Analytics that lets you copy data from a MySQL database to a data store supported as a sink.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 10/20/2023
ms.author: jianleishen
---

# Copy data from MySQL using Azure Data Factory or Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

This article outlines how to use the Copy Activity in Azure Data Factory and Synapse Analytics pipelines to copy data from a MySQL database. It builds on the [copy activity overview](copy-activity-overview.md) article that presents a general overview of copy activity.

>[!NOTE]
>To copy data from or to [Azure Database for MySQL](../mysql/overview.md) service, use the specialized [Azure Database for MySQL connector](connector-azure-database-for-mysql.md).

## Supported capabilities

This MySQL connector is supported for the following capabilities:

| Supported capabilities|IR |
|---------| --------|
|[Copy activity](copy-activity-overview.md) (source/-)|&#9312; &#9313;|
|[Lookup activity](control-flow-lookup-activity.md)|&#9312; &#9313;|

*&#9312; Azure integration runtime &#9313; Self-hosted integration runtime*

For a list of data stores that are supported as sources/sinks by the copy activity, see the [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats) table.

Specifically, this MySQL connector supports MySQL **version 5.6, 5.7 and 8.0**.

## Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

The Integration Runtime provides a built-in MySQL driver starting from version 3.7, therefore you don't need to manually install any driver.

## Getting started

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## Create a linked service to MySQL using UI

Use the following steps to create a linked service to MySQL in the Azure portal UI.

1. Browse to the Manage tab in your Azure Data Factory or Synapse workspace and select Linked Services, then click New:

    # [Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Create a new linked service with Azure Data Factory UI.":::

    # [Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Create a new linked service with Azure Synapse UI.":::

2. Search for MySQL and select the MySQL connector.

    :::image type="content" source="media/connector-mysql/mysql-connector.png" alt-text="Select the MySQL connector.":::    

1. Configure the service details, test the connection, and create the new linked service.

    :::image type="content" source="media/connector-mysql/configure-mysql-linked-service.png" alt-text="Configure a linked service to MySQL.":::

## Connector configuration details

The following sections provide details about properties that are used to define Data Factory entities specific to MySQL connector.

## Linked service properties

The following properties are supported for MySQL linked service:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property must be set to: **MySql** | Yes |
| connectionString | Specify information needed to connect to the Azure Database for MySQL instance.<br/> You can also put password in Azure Key Vault and pull the `password` configuration out of the connection string. Refer to the following samples and [Store credentials in Azure Key Vault](store-credentials-in-key-vault.md) article with more details. | Yes |
| connectVia | The [Integration Runtime](concepts-integration-runtime.md) to be used to connect to the data store. Learn more from [Prerequisites](#prerequisites) section. If not specified, it uses the default Azure Integration Runtime. |No |

A typical connection string is `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. More properties you can set per your case:

| Property | Description | Options | Required |
|:--- |:--- |:--- |:--- |
| SSLMode | This option specifies whether the driver uses TLS encryption and verification when connecting to MySQL. E.g.,  `SSLMode=<0/1/2/3/4>`.| DISABLED (0) / PREFERRED (1) **(Default)** / REQUIRED (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | No |
| SSLCert | The full path and name of a .pem file containing the SSL certificate used for proving the identity of the client. <br/> To specify a private key for encrypting this certificate before sending it to the server, use the `SSLKey` property.| | Yes, if using two-way SSL verification. |
| SSLKey | The full path and name of a file containing the private key used for encrypting the client-side certificate during two-way SSL verification.|  | Yes, if using two-way SSL verification. |
| UseSystemTrustStore | This option specifies whether to use a CA certificate from the system trust store, or from a specified PEM file. E.g. `UseSystemTrustStore=<0/1>;`| Enabled (1) / Disabled (0) **(Default)** | No |

**Example:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Example: store password in Azure Key Vault**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

If you were using MySQL linked service with the following payload, it is still supported as-is, while you are suggested to use the new one going forward.

**Previous payload:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## Dataset properties

For a full list of sections and properties available for defining datasets, see the [datasets](concepts-datasets-linked-services.md) article. This section provides a list of properties supported by MySQL dataset.

To copy data from MySQL, the following properties are supported:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property of the dataset must be set to: **MySqlTable** | Yes |
| tableName | Name of the table in the MySQL database. | No (if "query" in activity source is specified) |

**Example**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "MySqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

If you were using `RelationalTable` typed dataset, it is still supported as-is, while you are suggested to use the new one going forward.

## Copy activity properties

For a full list of sections and properties available for defining activities, see the [Pipelines](concepts-pipelines-activities.md) article. This section provides a list of properties supported by MySQL source.

### MySQL as source

To copy data from MySQL, the following properties are supported in the copy activity **source** section:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property of the copy activity source must be set to: **MySqlSource** | Yes |
| query | Use the custom SQL query to read data. For example: `"SELECT * FROM MyTable"`. | No (if "tableName" in dataset is specified) |

**Example:**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "MySqlSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

If you were using `RelationalSource` typed source, it is still supported as-is, while you are suggested to use the new one going forward.

## Data type mapping for MySQL

When copying data from MySQL, the following mappings are used from MySQL data types to interim data types used by the service internally. See [Schema and data type mappings](copy-activity-schema-and-type-mapping.md) to learn about how copy activity maps the source schema and data type to the sink.

| MySQL data type | Interim service data type |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int` |


## Lookup activity properties

To learn details about the properties, check [Lookup activity](control-flow-lookup-activity.md).

## Next steps
For a list of data stores supported as sources and sinks by the copy activity, see [supported data stores](copy-activity-overview.md#supported-data-stores-and-formats).
