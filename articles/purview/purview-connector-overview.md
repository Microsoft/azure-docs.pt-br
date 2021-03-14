---
title: Visão geral do conector do alcance
description: Este artigo descreve os diferentes armazenamentos de dados e funcionalidades com suporte no alcance
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 08b22af8743082bab1d547205e51917cb9d92a11
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695763"
---
# <a name="supported-data-stores"></a>Armazenamento de dados com suporte

O alcance dá suporte aos seguintes armazenamentos de dados. Clique em cada armazenamento de dados para aprender os recursos com suporte e as configurações correspondentes em detalhes.

## <a name="purview-data-sources"></a>Fontes de dados do alcance

|**Categoria**|  **Armazenamento de dados**  |**Extração de metadados**|**Verificação completa**|**Verificação incremental**|**Verificação com escopo**|**Classificação**|**Linhagem**|
|---|---|---|---|---|---|---|---|
| Azure | [Armazenamento de Blobs do Azure](register-scan-azure-blob-storage-source.md)| Sim| Sim| Sim| Sim| Sim| Sim|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Sim| Sim| Sim| Sim| Sim| Sim|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|Sim| Sim| Sim| Sim| Sim| Sim|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|Sim| Sim| Sim| Sim| Sim| Sim|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|Sim| Sim| Sim| Sim| Sim| Sim|
||[Banco de Dados SQL do Azure](register-scan-azure-sql-database.md)|Sim| Sim| Não| Sim| Sim| Sim|
||[Instância Gerenciada do Banco de Dados SQL do Azure](register-scan-azure-sql-database-managed-instance.md)|Sim| Sim| Não| Sim| Sim| Sim|
||[Azure Synapse Analytics (antigo SQL DW)](register-scan-azure-synapse-analytics.md)|Sim| Sim| Não| Sim| Sim| Sim|
|Banco de dados|[Oracle DB](register-scan-oracle-source.md)|Sim| Sim| Não| Não| Não| Sim|
||[SQL Server](register-scan-on-premises-sql-server.md)|Sim| Sim| Não| Sim| Sim| Sim|
||[Teradata](register-scan-teradata-source.md)|Sim| Sim| Não| Não| Não| Sim|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Sim| Sim| Não| Não| Não| Sim|
|Serviços e aplicativos|[SAP ECC](register-scan-sapecc-source.md)|Sim| Sim| Não| Sim| Sim| Sim|
||[S4HANA SAP](register-scan-saps4hana-source.md)|Sim| Sim| Não| Sim| Sim| Sim|

## <a name="next-steps"></a>Próximas etapas

- [Registrar e verificar a origem do armazenamento de BLOBs do Azure](register-scan-azure-blob-storage-source.md)