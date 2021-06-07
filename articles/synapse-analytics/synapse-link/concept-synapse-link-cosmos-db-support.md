---
title: Recursos compatíveis do Link do Azure Synapse para Azure Cosmos DB
description: Entender a lista atual de ações compatíveis com o Link do Azure Synapse para Azure Cosmos DB
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: cdc9f344e108fc58399f9bcb2a9f02a4659ecfe1
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627669"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Recursos compatíveis do Link do Azure Synapse para Azure Cosmos DB

Este artigo descreve as funcionalidades atualmente compatíveis no Link do Azure Synapse para Azure Cosmos DB.

## <a name="azure-synapse-support"></a>Suporte do Azure Synapse

Há dois tipos de contêineres no Azure Cosmos DB:
* Contêiner HTAP - um contêiner com o Link do Synapse habilitado. Esse contêiner tem repositório transacional e repositório analítico. 
* Contêiner OLTP-um contêiner com o link Synaspe não habilitado. Esse contêiner tem apenas armazenamento transacional e nenhum repositório analítico.

> [!IMPORTANT]
> O link Synapse do Azure para Azure Cosmos DB tem suporte atualmente em espaços de trabalho do Synapse que não têm a rede virtual gerenciada habilitada. 

Você pode se conectar a um contêiner de Azure Cosmos DB sem habilitar o link Synapse. Nesse cenário, você só pode ler/gravar no repositório transacional. O que vem a seguir é uma lista dos recursos com suporte no momento no link do Synapse para Azure Cosmos DB. 

| Categoria              | Descrição |[Pool do Apache Spark](../sql/on-demand-workspace-overview.md) | [Pool de SQL sem servidor](../sql/on-demand-workspace-overview.md) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **Suporte para tempo de execução** |Tempo de execução do Azure Synapse com suporte para acessar Azure Cosmos DB| ✓ | ✓ |
| **Suporte para API do Azure Cosmos DB** | Tipo de API Azure Cosmos DB com suporte | SQL/MongoDB | SQL/MongoDB |
| **Objeto**  |Objetos, como uma tabela que pode ser criada, apontando diretamente para o contêiner do Azure Cosmos DB| Dataframe, exibição, tabela | Visualizar |
| **Leitura**    | Tipo de contêiner de Azure Cosmos DB que pode ser lido | OLTP/HTAP | HTAP  |
| **Gravar**   | O tempo de execução do Azure Synapse pode ser usado para gravar dados em um contêiner de Azure Cosmos DB | Sim | Não |

* Se você gravar dados em um contêiner de Azure Cosmos DB do Spark, esse processo ocorrerá por meio do armazenamento transacional de Azure Cosmos DB. Ele afetará o desempenho transacional de Azure Cosmos DB consumindo unidades de solicitação.
* A integração do pool de SQL dedicada por meio de tabelas externas não tem suporte no momento.
 
## <a name="supported-code-generated-actions-for-spark"></a>Ações geradas por código compatíveis para Spark

| Gesto              | Descrição |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Carregar em DataFrame** |Carregar e ler dados em um DataFrame do Spark |✓| ✓ |
| **Criar tabela do Spark** |Criar uma tabela apontando para um contêiner do Azure Cosmos DB|✓| ✓ |
| **Gravar DataFrame no contêiner** |Gravar dados em um contêiner|✓| ✓ |
| **Carregar DataFrame de streaming do contêiner** |Transmitir dados usando o feed de alterações do Azure Cosmos DB|✓| ✓ |
| **Gravar DataFrame de streaming no contêiner** |Transmitir dados usando o feed de alterações do Azure Cosmos DB|✓| ✓ |

## <a name="supported-code-generated-actions-for-serverless-sql-pool"></a>Ações geradas por código com suporte para o pool SQL sem servidor

| Gesto              | Descrição |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Explorar dados** |Explorar dados de um contêiner com sintaxe T-SQL familiar e inferência de esquema automática|X| ✓ |
| **Criar exibições e criar relatórios de BI** |Criar um modo de exibição SQL para ter acesso direto a um contêiner para BI por meio do pool SQL sem servidor |X| ✓ |
| **Unir fontes de dados distintas junto com Cosmos DB dados** | Armazenar os resultados da consulta lendo dados de contêineres Cosmos DB junto com os dados no armazenamento de BLOBs do Azure ou Azure Data Lake Storage usando CETAS |X| ✓ |

## <a name="next-steps"></a>Próximas etapas

* Confira como [conectar-se ao Link do Azure Synapse para Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Saiba como consultar o repositório analítico de Cosmos DB com o Spark](how-to-query-analytical-store-spark.md)