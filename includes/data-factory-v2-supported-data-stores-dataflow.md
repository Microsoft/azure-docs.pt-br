---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 05/24/2019
ms.author: jingwang
ms.openlocfilehash: 70ffc91477d4a77619ba7df3b2ab121fea5f8bac
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509807"
---
| Category | Armazenamento de dados | Com suporte como uma [atividade de cópia](../articles/data-factory/copy-activity-overview.md) fonte | Com suporte como uma [atividade de cópia](../articles/data-factory/copy-activity-overview.md) coletor | Suporte do [IR do Azure](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) | Suporte do [IR auto-hospedado](../articles/data-factory/concepts-integration-runtime.md#self-hosted-integration-runtime) | Com suporte pelo [fluxo de dados](../articles/data-factory/concepts-data-flow-overview.md)
|:--- |:--- |:--- |:--- |:--- |:--- |
| **As tabelas** |[Armazenamento de Blobs do Azure](../articles/data-factory/connector-azure-blob-storage.md) |✓ |✓ |✓ |✓  | ✓ <br> <small>Formatos com suporte: Texto delimitado, Parquet</small> |
| &nbsp; |[Azure Cosmos DB (API do SQL)](../articles/data-factory/connector-azure-cosmos-db.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[API do Azure Cosmos DB para MongoDB](../articles/data-factory/connector-azure-cosmos-db-mongodb-api.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Azure Data Explorer](../articles/data-factory/connector-azure-data-explorer.md) |✓ |✓ |✓ |✓ ||
| &nbsp; |[Azure Data Lake Storage Gen1](../articles/data-factory/connector-azure-data-lake-store.md) |✓ |✓ |✓ |✓  |✓ <br> <small>Formatos com suporte: Texto delimitado, Parquet</small> |
| &nbsp; |[Azure Data Lake Storage Gen2](../articles/data-factory/connector-azure-data-lake-storage.md) |✓ |✓ |✓ |✓  |✓ <br> <small>Formatos com suporte: Texto delimitado, Parquet</small> |
| &nbsp; |[Banco de Dados do Azure para MariaDB](../articles/data-factory/connector-azure-database-for-mariadb.md) |✓ | |✓ |✓  ||
| &nbsp; |[Banco de Dados do Azure para MySQL](../articles/data-factory/connector-azure-database-for-mysql.md) |✓ | |✓ |✓  ||
| &nbsp; |[Banco de Dados do Azure para PostgreSQL](../articles/data-factory/connector-azure-database-for-postgresql.md) |✓ | |✓ |✓  ||
| &nbsp; |[Armazenamento de Arquivos do Azure](../articles/data-factory/connector-azure-file-storage.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Banco de Dados SQL do Azure](../articles/data-factory/connector-azure-sql-database.md) |✓ |✓ |✓ |✓  |✓  |
| &nbsp; |[Instância Gerenciada do Banco de Dados SQL do Azure](../articles/data-factory/connector-azure-sql-database-managed-insance.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[SQL Data Warehouse do Azure](../articles/data-factory/connector-azure-sql-data-warehouse.md) |✓ |✓ |✓ |✓  |✓  |
| &nbsp; |[Índice do Azure Search](../articles/data-factory/connector-azure-search.md) | |✓ |✓ |✓  ||
| &nbsp; |[Armazenamento de Tabelas do Azure](../articles/data-factory/connector-azure-table-storage.md) |✓ |✓ |✓ |✓  ||
| **Banco de dados** |[Amazon Redshift](../articles/data-factory/connector-amazon-redshift.md) |✓ | |✓ |✓  ||
| &nbsp; |[DB2](../articles/data-factory/connector-db2.md) |✓ | |✓ |✓  ||
| &nbsp; |[Análise detalhada (versão prévia)](../articles/data-factory/connector-drill.md) |✓ | |✓ |✓  ||
| &nbsp; |[Google BigQuery](../articles/data-factory/connector-google-bigquery.md) |✓ | |✓ |✓  ||
| &nbsp; |[Greenplum](../articles/data-factory/connector-greenplum.md) |✓ | |✓ |✓  ||
| &nbsp; |[HBase](../articles/data-factory/connector-hbase.md) |✓ | |✓ |✓  ||
| &nbsp; |[Hive](../articles/data-factory/connector-hive.md) |✓ | |✓ |✓  ||
| &nbsp; |[Apache Impala (versão prévia)](../articles/data-factory/connector-impala.md) |✓ | |✓ |✓  ||
| &nbsp; |[Informix](../articles/data-factory/connector-odbc.md#ibm-informix-source) |✓ | | |✓  ||
| &nbsp; |[MariaDB](../articles/data-factory/connector-mariadb.md) |✓ | |✓ |✓  ||
| &nbsp; |[Microsoft Access](../articles/data-factory/connector-odbc.md#microsoft-access-source) |✓ | | |✓  ||
| &nbsp; |[MySQL](../articles/data-factory/connector-mysql.md) |✓ | |✓ |✓  ||
| &nbsp; |[Netezza](../articles/data-factory/connector-netezza.md) |✓ | |✓ |✓  ||
| &nbsp; |[Oracle](../articles/data-factory/connector-oracle.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Phoenix](../articles/data-factory/connector-phoenix.md) |✓ | |✓ |✓  ||
| &nbsp; |[PostgreSQL](../articles/data-factory/connector-postgresql.md) |✓ | |✓ |✓  ||
| &nbsp; |[Presto (versão prévia)](../articles/data-factory/connector-presto.md) |✓ | |✓ |✓  ||
| &nbsp; |[SAP Business Warehouse Open Hub](../articles/data-factory/connector-sap-business-warehouse-open-hub.md) |✓ | | |✓  ||
| &nbsp; |[SAP Business Warehouse via MDX](../articles/data-factory/connector-sap-business-warehouse.md) |✓ | | |✓  ||
| &nbsp; |[SAP HANA](../articles/data-factory/connector-sap-hana.md) |✓ |✓ | |✓  ||
| &nbsp; |[Tabela SAP](../articles/data-factory/connector-sap-table.md) |✓ | | |✓  ||
| &nbsp; |[Spark](../articles/data-factory/connector-spark.md) |✓ | |✓ |✓  ||
| &nbsp; |[SQL Server](../articles/data-factory/connector-sql-server.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Sybase](../articles/data-factory/connector-sybase.md) |✓ | | |✓  ||
| &nbsp; |[Teradata](../articles/data-factory/connector-teradata.md) |✓ | |✓ |✓  ||
| &nbsp; |[Vertica](../articles/data-factory/connector-vertica.md) |✓ | |✓ |✓  ||
| **NoSQL** |[Cassandra](../articles/data-factory/connector-cassandra.md) |✓ | |✓ |✓  ||
| &nbsp; |[Couchbase (versão prévia)](../articles/data-factory/connector-couchbase.md) |✓ | |✓ |✓  ||
| &nbsp; |[MongoDB](../articles/data-factory/connector-mongodb.md) |✓ | |✓ |✓  ||
| **Arquivo** |[Amazon S3](../articles/data-factory/connector-amazon-simple-storage-service.md) |✓ | |✓ |✓  ||
| &nbsp; |[Sistema de Arquivos](../articles/data-factory/connector-file-system.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[FTP](../articles/data-factory/connector-ftp.md) |✓ | |✓ |✓  ||
| &nbsp; |[Google Cloud Storage](../articles/data-factory/connector-google-cloud-storage.md) |✓ | |✓ |✓  ||
| &nbsp; |[HDFS](../articles/data-factory/connector-hdfs.md) |✓ | |✓ |✓  ||
| &nbsp; |[SFTP](../articles/data-factory/connector-sftp.md) |✓ | |✓ |✓  ||
| **Projeto genérico** |[ HTTP Genérico](../articles/data-factory/connector-http.md) |✓ | |✓ |✓  ||
| &nbsp; |[OData Genérico](../articles/data-factory/connector-odata.md) |✓ | |✓ |✓  ||
| &nbsp; |[ODBC Genérico](../articles/data-factory/connector-odbc.md) |✓ |✓ | |✓  ||
| &nbsp; |[REST Genérico](../articles/data-factory/connector-rest.md) |✓ | |✓ |✓  ||
| **Serviços e aplicativos** |[Serviço Web do Amazon Marketplace (versão prévia)](../articles/data-factory/connector-amazon-marketplace-web-service.md) |✓ | |✓ |✓  ||
| &nbsp; |[Common Data Service para aplicativos](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Concorrente (versão prévia)](../articles/data-factory/connector-concur.md) |✓ | |✓ |✓  ||
| &nbsp; |[Dynamics 365](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Dynamics AX (versão prévia)](../articles/data-factory/connector-dynamics-ax.md) |✓ | |✓ |✓  ||
| &nbsp; |[Dynamics CRM](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Google AdWords (Versão prévia)](../articles/data-factory/connector-google-adwords.md) |✓ | |✓ |✓  ||
| &nbsp; |[HubSpot (versão prévia)](../articles/data-factory/connector-hubspot.md) |✓ | |✓ |✓  ||
| &nbsp; |[Jira (versão prévia)](../articles/data-factory/connector-jira.md) |✓ | |✓ |✓  ||
| &nbsp; |[Magento (versão prévia)](../articles/data-factory/connector-magento.md) |✓ | |✓ |✓  ||
| &nbsp; |[Marketo (versão prévia)](../articles/data-factory/connector-marketo.md) |✓ | |✓ |✓  ||
| &nbsp; |[Office 365](../articles/data-factory/connector-office-365.md) |✓ | |✓ |✓  ||
| &nbsp; |[Oracle Eloqua (versão prévia)](../articles/data-factory/connector-oracle-eloqua.md) |✓ | |✓ |✓  ||
| &nbsp; |[Oracle Responsys (versão prévia)](../articles/data-factory/connector-oracle-responsys.md) |✓ | |✓ |✓  ||
| &nbsp; |[Serviço de Nuvem da Oracle (Versão prévia)](../articles/data-factory/connector-oracle-service-cloud.md) |✓ | |✓ |✓  ||
| &nbsp; |[PayPal (versão prévia)](../articles/data-factory/connector-paypal.md) |✓ | |✓ |✓  ||
| &nbsp; |[QuickBooks (versão prévia)](../articles/data-factory/connector-quickbooks.md) |✓ | |✓ |✓  ||
| &nbsp; |[Salesforce](../articles/data-factory/connector-salesforce.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Nuvem de serviço Salesforce](../articles/data-factory/connector-salesforce.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Nuvem de Marketing Salesforce (versão prévia)](../articles/data-factory/connector-salesforce-marketing-cloud.md) |✓ | |✓ |✓  ||
| &nbsp; |[SAP Cloud for Customer (C4C)](../articles/data-factory/connector-sap-cloud-for-customer.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[SAP ECC](../articles/data-factory/connector-sap-ecc.md) |✓ | |✓ |✓ ||
| &nbsp; |[ServiceNow](../articles/data-factory/connector-servicenow.md) |✓ | |✓ |✓  ||
| &nbsp; |[Shopify (versão prévia)](../articles/data-factory/connector-shopify.md) |✓ | |✓ |✓  ||
| &nbsp; |[Square (versão prévia)](../articles/data-factory/connector-square.md) |✓ | |✓ |✓  ||
| &nbsp; |[Tabela da Web (tabela HTML)](../articles/data-factory/connector-web-table.md) |✓ | | |✓  ||
| &nbsp; |[Xero (versão prévia)](../articles/data-factory/connector-xero.md) |✓ | |✓ |✓  ||
| &nbsp; |[Zoho (versão prévia)](../articles/data-factory/connector-zoho.md) |✓ | |✓ |✓  ||

> [!NOTE]
> Um conector marcado como *versão prévia* significa que você pode experimentá-lo e nos enviar comentários.  Se você quiser uma dependência de conectores em versão prévia em sua solução, entre em contato com [suporte do Azure](https://azure.microsoft.com/support/).
