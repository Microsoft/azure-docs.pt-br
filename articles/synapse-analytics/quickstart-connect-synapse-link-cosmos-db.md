---
title: 'Início Rápido: Conectar-se ao Link do Azure Synapse para Azure Cosmos DB'
description: Como conectar um Azure Cosmos DB a um espaço de trabalho Synapse com o Link do Synapse
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.subservice: synapse-link
ms.topic: quickstart
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 7462c78ecad718c30980a98ff7659a2a4d2ea020
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222149"
---
# <a name="quickstart-connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Início Rápido: Conectar-se ao Link do Azure Synapse para Azure Cosmos DB

Este artigo descreve como acessar um banco de dados Azure Cosmos DB a partir do Azure Synapse Analytics Studio com o Link do Synapse. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de conectar uma conta Azure Cosmos DB ao espaço de trabalho, algumas coisas são necessárias.

* Conta do Azure Cosmos DB existente ou criar uma nova conta após este [início rápido](../cosmos-db/how-to-manage-database-account.md)
* Espaço de trabalho Synapse existente ou criar um novo espaço de trabalho após este [início rápido](./quickstart-create-workspace.md) 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Habilitar o repositório analítico do Azure Cosmos DB

Para executar a análise em larga escala no Azure Cosmos DB sem afetar o desempenho operacional, recomendamos habilitar o Link do Synapse para o Azure Cosmos DB. Essa função traz o recurso HTAP para um contêiner e o suporte interno no Azure Synapse. Siga este início rápido para habilitar o Link do Synapse para os contêineres do Cosmos DB.

## <a name="navigate-to-synapse-studio"></a>Navegar até o Synapse Studio

Em seu espaço de trabalho do Synapse, selecione **Inicializar Synapse Studio**. Na home page do Synapse Studio, selecione **Dados, o que o levará para o **Pesquisador de Objetos de Dados**.

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Conectar um banco de dados Azure Cosmos DB a um espaço de trabalho Synapse

A conexão de um banco de dados Azure Cosmos DB é feita como um serviço vinculado. Um serviço vinculado Cosmos DB permite que os usuários naveguem e explorem dados, leiam e gravem do Apache Spark para Azure Synapse Analytics ou SQL no Azure Cosmos DB.

No Pesquisador de Objetos de Dados, você pode conectar diretamente um banco de dados Azure Cosmos DB executando as seguintes etapas:

1. Selecione o ícone **_+_* _ ao lado de Dados
2. Selecione _ *Conectar a dados externos**
3. Selecione a API à qual deseja se conectar: SQL ou MongoDB
4. Escolha **_Continuar_* _
5. Nomeie o serviço vinculado. O nome será exibido no Pesquisador de Objetos e usado pelos tempos de execução do Synapse para conectar ao banco de dados e aos contêineres. Recomendamos usar um nome amigável.
6. Selecione o _ *nome da conta do Cosmos DB** e o **nome do banco de dados**
7. (Opcional) Se nenhuma região for especificada, as operações de tempo de execução do Synapse serão roteadas para a região mais próxima na qual o repositório analítico estiver habilitado. No entanto, você pode definir manualmente qual região deseja que os usuários acessem o repositório analítico do Cosmos DB. Selecione **Propriedades de conexão adicionais** e, em seguida, **Novo**. Em **Nome da Propriedade**, grave as **_PreferredRegions_ *_ e defina o _* Valor** com a região desejada (exemplo: WestUS2, não há espaço entre palavras e números)
8. Selecione **_Criar_* _

Os bancos de dados do Azure Cosmos DB estão visíveis na guia _ *Vinculado**, na seção Azure Cosmos DB. Você pode diferenciar um contêiner Azure Cosmos DB habilitado para HTAP de um contêiner somente OLTP com os seguintes ícones:

**Contêiner Synapse** :

![Contêiner HTAP](./media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

**Contêiner somente OLTP**:

![Contêiner OLTP](./media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interagir rapidamente com ações geradas por código

Ao clicar com o botão direito em um contêiner, você terá uma lista de gestos que inicializarão uma execução do Spark ou do SQL. A gravação em um contêiner ocorrerá por meio do repositório transacional do Azure Cosmos DB e consumirá Unidades de Solicitação.  

## <a name="next-steps"></a>Próximas etapas

* [Saiba o que é compatível entre o Synapse e o Azure Cosmos DB](./synapse-link/concept-synapse-link-cosmos-db-support.md)
* [Saiba como consultar um repositório analítico com o Apache Spark para Azure Synapse Analytics](synapse-link/how-to-query-analytical-store-spark.md)