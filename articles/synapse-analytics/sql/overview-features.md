---
title: Diferenças de recursos do T-SQL no SQL do Synapse
description: Lista de recursos do Transact-SQL que estão disponíveis no Synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 55639a8d36000af00e23391f39e9e1c7364c8b71
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076416"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Recursos do Transact-SQL compatíveis com o Azure Synapse SQL

O Azure Synapse SQL é um serviço analítico de Big Data que permite consultar e analisar dados usando a linguagem T-SQL. Use o dialeto padrão em conformidade com ANSI da linguagem SQL usado no SQL Server e no Banco de Dados SQL do Azure para análise de dados. 

A linguagem Transact-SQL é usada no pool de SQL sem servidor e o modelo dedicado pode referenciar objetos distintos e tem algumas diferenças no conjunto de recursos compatíveis. Nesta página, você poderá encontrar diferenças de alto nível da linguagem Transact-SQL entre os modelos de consumo do Synapse SQL.

## <a name="database-objects"></a>Objetos de banco de dados

Os modelos de consumo do SQL do Synapse permitem que você use diferentes objetos de banco de dados. A comparação dos tipos de objeto compatíveis é mostrada na seguinte tabela:

|   | Dedicado | Sem servidor |
| --- | --- | --- |
| **Tabelas** | [Sim](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Não, o modelo sem servidor pode consultar somente dados externos colocados no [Armazenamento do Azure](#storage-options) |
| **Exibições** | [Sim](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true). As exibições podem usar [elementos da linguagem de consulta](#query-language) que estão disponíveis no modelo dedicado. | [Sim](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true). As exibições podem usar [elementos de linguagem de consulta](#query-language) que estão disponíveis no modelo sem servidor. |
| **Esquemas** | [Sim](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Sim](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| **Tabelas temporárias** | [Sim](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **Procedimentos** | [Sim](/sql/t-sql/statements/create-procedure-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Sim |
| **Funções** | [Sim](/sql/t-sql/statements/create-function-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Sim, somente funções com valor de tabela embutidas. |
| **Gatilhos** | Não | Não |
| **Tabelas externas** | [Sim](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true). Confira os [formatos de dados](#data-formats) compatíveis. | [Sim](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true). Confira os [formatos de dados](#data-formats) compatíveis. |
| **Consultas de cache** | Sim, vários formatos (cache baseado em SSD, em memória, cache do conjunto de resultados). Além disso, há suporte para a exibição materializada | Não |
| **Variáveis de tabela** | [Não](/sql/t-sql/data-types/table-transact-sql?view=azure-sqldw-latest&preserve-view=true), use tabelas temporárias | Não |
| **[Distribuição de tabelas](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Sim | Não |
| **[Índices da tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Sim | Não |
| **[Partições de tabela](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Sim | Não |
| **[Estatísticas](develop-tables-statistics.md)**            | Sim | Sim |
| **[Gerenciamento de carga de trabalho, classes de recursos e controle de simultaneidade](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Sim    | Não |
| **Controle de custo** | Sim, usando ações de escala e redução verticais. | Sim, usando [o portal do Azure ou um procedimento T-SQL](./data-processed.md#cost-control). |

## <a name="query-language"></a>Idioma de consulta

As linguagens de consulta usadas no Synapse SQL podem ter diferentes recursos compatíveis, dependendo do modelo de consumo. A seguinte tabela descreve as diferenças mais importantes de linguagem de consulta nos dialetos Transact-SQL:

|   | Dedicado | Sem servidor |
| --- | --- | --- |
| **Instrução SELECT** | Sim. Não há suporte para as cláusulas de consulta Transact-SQL [FOR XML/FOR JSON](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true), [MATCH](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true), OFFSET/FETCH. | Sim. Não há suporte para as cláusulas de consulta Transact-SQL [FOR XML](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true), [MATCH](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true), [PREDICT](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true), GROUPNG SETS nem para dicas de consulta. |
| **Instrução INSERT** | Sim | Não |
| **Instrução UPDATE** | Sim | Não |
| **Instrução DELETE** | Sim | Não |
| **Instrução MERGE** | Sim ([versão prévia](/sql/t-sql/statements/merge-transact-sql?view=azure-sqldw-latest&preserve-view=true)) | Não |
| **[Transações](develop-transactions.md)** | Sim | Sim, aplicável em objetos de metadados. |
| **[Rótulos](develop-label.md)** | Sim | Não |
| **Carregamento de dados** | Sim. O utilitário preferencial é a instrução [COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true), mas o sistema dá suporte ao carregamento BULK (BCP) e à instrução [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) para o carregamento de dados. | Não |
| **Exportação de dados** | Sim. Usando a instrução [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true). | Sim. Usando a instrução [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true). |
| **Types** | Sim, todos os tipos Transact-SQL, exceto [cursor](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true), [ntext, text e image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true), [Spatial Types](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true) e [xml](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Sim, todos os tipos Transact-SQL, exceto os [cursor](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true), [ntext, text e image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true), [Spatial Types](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true), [xml](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true) e Table |
| **Consultas entre bancos de dados** | Não | Sim, incluindo a instrução [USE](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true). |
| **Funções internas (análise)** | Sim, todas as funções [analíticas](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), de conversão, de [data e hora](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), lógicas e [matemáticas](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) do Transact-SQL, exceto por [CHOOSE](/sql/t-sql/functions/logical-functions-choose-transact-sql?view=azure-sqldw-latest&preserve-view=true) e [PARSE](/sql/t-sql/functions/parse-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Sim, todas as funções [analíticas](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), de conversão, de [data e hora](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), lógicas e [matemáticas](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) do Transact-SQL. |
| **Funções internas ([cadeia de caracteres](https://docs.microsoft.com/sql/t-sql/functions/string-functions-transact-sql))** | Sim. Todas as funções Transact-SQL de [cadeia de caracteres](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) e de ordenação, exceto [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?view=azure-sqldw-latest&preserve-view=true) e [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Sim. Todas as funções Transact-SQL de [cadeia de caracteres](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) e de ordenação. |
| **Funções internas ([criptografia](https://docs.microsoft.com/sql/t-sql/functions/cryptographic-functions-transact-sql))** | Alguns | Não |
| **Funções internas com valor de tabela** | Sim, as [funções de conjunto de linhas do Transact-SQL](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions), exceto [OPENXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true) e [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Sim, as [funções de conjunto de linhas do Transact-SQL](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions), exceto [OPENXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true) e [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true)  |
| **Agregações** |  Agregações internas do Transact-SQL, exceto [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?view=azure-sqldw-latest&preserve-view=true) e [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Agregações internas do Transact-SQL. |
| **Operadores** | Sim, todos os [operadores Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true), exceto [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?view=azure-sqldw-latest&preserve-view=true) e [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Sim, todos os [operadores Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true)  |
| **Controle do fluxo** | Sim. Todas as [instruções Transact-SQL de controle de fluxo](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true), exceto [CONTINUE](/sql/t-sql/language-elements/continue-transact-sql?view=azure-sqldw-latest&preserve-view=true), [GOTO](/sql/t-sql/language-elements/goto-transact-sql?view=azure-sqldw-latest&preserve-view=true), [RETURN](/sql/t-sql/language-elements/return-transact-sql?view=azure-sqldw-latest&preserve-view=true), [USE](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true) e [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Sim. Toda a consulta SELECT da [instrução Transact-SQL de controle de fluxo](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true) na condição `WHILE (...)` |
| **Instruções DDL (CREATE, ALTER e DROP)** | Sim. Todas as instruções DDL Transact-SQL aplicáveis aos tipos de objeto compatíveis | Sim. Todas as instruções DDL Transact-SQL aplicáveis aos tipos de objeto compatíveis |

## <a name="security"></a>Segurança

O Synapse SQL permite que você use recursos de segurança internos para proteger seus dados e controlar o acesso. A tabela a seguir compara as diferenças de alto nível entre os modelos de consumo do Synapse SQL.

|   | Dedicado | Sem servidor |
| --- | --- | --- |
| **Logons** | N/D (há suporte apenas a usuários independentes em bancos de dados) | Sim |
| **Usuários** |  N/D (há suporte apenas a usuários independentes em bancos de dados) | Sim |
| **[Usuários independentes](/sql/relational-databases/security/contained-database-users-making-your-database-portable?view=azure-sqldw-latest&preserve-view=true)** | Sim. **Observação:** apenas um usuário do Azure AD pode ser um administrador irrestrito | Não |
| **Autenticação de nome de usuário/senha do SQL**| Sim | Sim |
| **Autenticação do Azure AD (Azure Active Directory)**| Sim, usuários do Azure AD | Sim, logons e usuários do Azure AD |
| **Autenticação de passagem do Azure AD (Azure Active Directory) de armazenamento** | Sim | Sim |
| **Autenticação de token SAS de armazenamento** | Não | Sim, usando [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) em [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) ou [CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) no nível da instância. |
| **Autenticação da chave de acesso de armazenamento** | Sim, usando [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) em [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Não |
| **Autenticação da [Identidade Gerenciada](../security/synapse-workspace-managed-identity.md) de armazenamento** | Sim, usando a [credencial da identidade de serviço gerenciado](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&preserve-view=true&toc=%2fazure%2fsynapse-analytics%2ftoc.json&view=azure-sqldw-latest&preserve-view=true) | Sim, usando a credencial da `Managed Identity`. |
| **Autenticação da identidade de aplicativo de armazenamento** | [Sim](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Não |
| **Permissões – nível do objeto** | Sim, incluindo a capacidade de CONCEDER, NEGAR e REVOGAR permissões aos usuários | Sim, incluindo a capacidade de CONCEDER, NEGAR e REVOGAR permissões aos usuários/aos logons nos objetos do sistema que são compatíveis |
| **Permissões – nível do esquema** | Sim, incluindo a capacidade de CONCEDER, NEGAR e REVOGAR permissões aos usuários/aos logons no esquema | Sim, incluindo a capacidade de CONCEDER, NEGAR e REVOGAR permissões aos usuários/aos logons no esquema |
| **Permissões – [nível do banco de dados](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true)** | Sim | Sim |
| **Permissão – [nível do servidor](/sql/relational-databases/security/authentication-access/server-level-roles)** | Não | Sim, há suporte para sysadmin e outras funções de servidor |
| **Permissões: [Segurança no Nível da Coluna](../sql-data-warehouse/column-level-security.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)** | Sim | Sim |
| **Funções/grupos** | Sim (no escopo do banco de dados) | Sim (no escopo do servidor e do banco de dados) |
| **Funções de &amp; identidade e segurança** | Algumas funções e alguns operadores de segurança Transact-SQL: `CURRENT_USER`, `HAS_DBACCESS`, `IS_MEMBER`, `IS_ROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS` e `OPEN/CLOSE MASTER KEY` | Alguns operadores e funções de segurança Transact-SQL: `CURRENT_USER`, `HAS_DBACCESS`, `HAS_PERMS_BY_NAME`, `IS_MEMBER', 'IS_ROLEMEMBER`, `IS_SRVROLEMEMBER`, `SESSION_USER`, `SESSION_CONTEXT`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS` e `REVERT`. As funções de segurança não podem ser usadas para consultar dados externos (armazene o resultado em uma variável que possa ser usada na consulta).  |
| **DATABASE SCOPED CREDENTIAL** | Sim | Sim |
| **SERVER SCOPED CREDENTIAL** | Não | Sim |
| **Segurança em nível de linha** | [Sim](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Não |
| **TDE (Transparent Data Encryption)** | [Sim](../../azure-sql/database/transparent-data-encryption-tde-overview.md) | Não | 
| **Descoberta e classificação de dados** | [Sim](../../azure-sql/database/data-discovery-and-classification-overview.md) | Não |
| **Avaliação de Vulnerabilidade** | [Sim](../../azure-sql/database/sql-vulnerability-assessment.md) | Não |
| **Proteção Avançada contra Ameaças** | [Sim](../../azure-sql/database/threat-detection-overview.md)
| **Auditoria** | [Sim](../../azure-sql/database/auditing-overview.md) | Não |
| **[Regras de firewall](../security/synapse-workspace-ip-firewall.md)**| Sim | Sim |
| **[Ponto de extremidade privado](../security/synapse-workspace-managed-private-endpoints.md)**| Sim | Sim |

O pool de SQL dedicado e o pool de SQL sem servidor usam a linguagem Transact-SQL padrão para consultar dados. Para obter as diferenças detalhadas, confira a [Referência da linguagem Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Ferramentas

Use várias ferramentas para se conectar ao Synapse SQL e consultar dados.

|   | Dedicado | Sem servidor |
| --- | --- | --- |
| **Synapse Studio** | Sim, scripts SQL | Sim, scripts SQL |
| **Power BI** | Sim | [Sim](tutorial-connect-power-bi-desktop.md) |
| **Azure Analysis Services** | Sim | Sim |
| **Azure Data Studio** | Sim | Sim, versão 1.18.0 ou superior. Há suporte para scripts SQL e SQL Notebooks. |
| **SQL Server Management Studio** | Sim | Sim, versão 18.5 ou superior |

> [!NOTE]
> Você pode usar o SSMS para se conectar ao pool de SQL sem servidor e consultar. Ele tem compatibilidade parcial da versão 18.5 em diante. Você pode usá-lo somente para se conectar e consultar.

A maioria dos aplicativos que usa a linguagem Transact-SQL padrão pode consultar os modelos de consumo dedicado e sem servidor do SQL do Synapse.

## <a name="storage-options"></a>Opções de armazenamento

Os dados analisados podem ser armazenados em vários tipos de armazenamento. A seguinte tabela lista todas as opções de armazenamento disponíveis:

|   | Dedicado | Sem servidor |
| --- | --- | --- |
| **Armazenamento interno** | Sim | Não |
| **Azure Data Lake v2** | Sim | Sim |
| **Armazenamento de Blobs do Azure** | Sim | Sim |
| **SQL do Azure (remoto)** | Não | Não |
| **Armazenamento transacional do Azure Cosmos DB** | Não | Não |
| **Armazenamento analítico do Azure Cosmos DB** | Não | Sim, usando o [Link do Synapse (versão prévia)](../../cosmos-db/synapse-link.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) ([versão prévia pública](../../cosmos-db/synapse-link.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json#limitations)) |
| **Tabelas do Apache Spark (no workspace)** | Não | Tabelas PARQUET somente usando [sincronização de metadados](develop-storage-files-spark-tables.md) |
| **Tabelas do Apache Spark (remotas)** | Não | Não |
| **Tabelas do Databricks (remotas)** | Não | Não |

## <a name="data-formats"></a>Formatos de dados

Os dados analisados podem ser armazenados em vários formatos de armazenamento. A seguinte tabela lista todos os formatos de dados disponíveis que podem ser analisados:

|   | Dedicado | Sem servidor |
| --- | --- | --- |
| **Delimitado** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Sim](query-single-csv-file.md) |
| **CSV** | Sim (não há suporte a delimitadores de vários caracteres) | [Sim](query-single-csv-file.md) |
| **Parquet** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Sim](query-parquet-files.md), incluindo arquivos com [tipos aninhados](query-parquet-nested-types.md) |
| **Hive ORC** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Não |
| **Hive RC** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Não |
| **JSON** | Sim | [Sim](query-json-files.md) |
| **Avro** | Não | Não |
| **[Delta-lake](https://delta.io/)** | Não | Não |
| **[CDM](/common-data-model/)** | Não | Não |

## <a name="next-steps"></a>Próximas etapas
Encontre mais informações sobre as melhores práticas do pool de SQL dedicado e do pool de SQL sem servidor nos seguintes artigos:

- [Melhores práticas do pool de SQL dedicado](best-practices-dedicated-sql-pool.md)
- [Melhores práticas do pool de SQL sem servidor](best-practices-serverless-sql-pool.md)
