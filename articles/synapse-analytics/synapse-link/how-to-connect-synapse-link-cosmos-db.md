---
title: Conectar-se ao Link do Azure Synapse (versão prévia) para o Azure Cosmos DB
description: Saiba como conectar um banco de dados do Azure Cosmos DB a um espaço de trabalho do Azure Synapse com o Link do Azure Synapse.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 5316f74ee38f597592ae4582aef31837f0f05fda
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119825"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Conectar-se ao Link do Azure Synapse para Azure Cosmos DB

Este artigo descreve como acessar um banco de dados Azure Cosmos DB pelo Azure Synapse Analytics Studio com o Link do Azure Synapse.

## <a name="prerequisites"></a>Pré-requisitos

Antes de conectar um banco de dados do Azure Cosmos DB ao workspace, você precisará de:

* Banco de dados do Azure Cosmos DB existente ou criar uma nova conta ao seguir estas etapas no [Início rápido: Gerenciar uma conta do Azure Cosmos DB](../../cosmos-db/how-to-manage-database-account.md).
* Ter um workspace do Azure Synapse ou criar um workspace seguindo as etapas descritas em [Guia de início rápido: Criar um workspace do Synapse](../quickstart-create-workspace.md).

> [!IMPORTANT]
> O Link do Azure Synapse para o Azure Cosmos DB é compatível atualmente com workspaces que não têm uma rede virtual gerenciada habilitada.

## <a name="enable-synapse-link-on-an-azure-cosmos-db-database-account"></a>Habilitar o link Synapse em uma conta de banco de dados Azure Cosmos DB

Para executar a análise em larga escala no Azure Cosmos DB sem afetar o desempenho operacional, recomendamos habilitar o Link do Synapse para o Azure Cosmos DB. O Link do Synapse traz o recurso HTAP para um contêiner e suporte interno no Azure Synapse.

## <a name="go-to-synapse-studio"></a>Acessar o Synapse Studio

No seu workspace do Azure Synapse, selecione **Inicializar o Synapse Studio**. Na home page do Synapse Studio, selecione **Dados**, o que o leva ao Pesquisador de Objetos de Dados.

## <a name="connect-an-azure-cosmos-db-database-to-an-azure-synapse-workspace"></a>Conectar um banco de dados Azure Cosmos DB a um workspace Azure Synapse

A conexão de um banco de dados Azure Cosmos DB é feita como um serviço vinculado. Com um serviço vinculado do Azure Cosmos DB, você pode procurar e explorar dados, ler e gravar do Apache Spark para Azure Synapse Analytics ou SQL no Azure Cosmos DB.

No Pesquisador de Objetos de Dados, você pode se conectar diretamente a um banco de dados Azure Cosmos DB executando essas etapas:

1. Selecione o ícone **+** perto de **Dados**.
1. Selecione **Conectar a dados externos**.
1. Selecione a API à qual você deseja se conectar, por exemplo, **API do SQL** ou **API para MongoDB**.
1. Selecione **Continuar**.
1. Use um nome amigável para nomear o serviço vinculado. O nome será exibido no Pesquisador de Objetos de Dados e será usado pelos runtimes do Azure Synapse para se conectar ao banco de dados e contêineres.
1. Selecione o **nome da conta do Azure Cosmos DB** e o **nome do banco de dados**.
1. (Opcional) Se nenhuma região for especificada, as operações de tempo de execução do Azure Synapse serão roteadas para a região mais próxima na qual o repositório analítico estiver habilitado. Você pode definir manualmente em qual região deseja que os usuários acessem o repositório analítico do Azure Cosmos DB. Selecione **Propriedades de conexão adicionais** e, em seguida, **Novo**. Em **Nome da propriedade**, insira **PreferredRegions**. Defina o **Valor** para a região que deseja, por exemplo, **WestUS2**. (Não há espaços entre as palavras e o número.)
1. Selecione **Criar**.

Os bancos de dados do Azure Cosmos DB aparecem na guia **Vinculado** na seção **Azure Cosmos DB**. Com o Azure Cosmos DB, você pode diferenciar um contêiner habilitado para HTAP de um contêiner somente OLTP pelos seguintes ícones:

**Contêiner somente OLTP**:

![Visualização que mostra o ícone de contêiner OLTP.](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Contêiner habilitado para HTAP**:

![Visualização que mostra o ícone de contêiner HTAP.](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interagir rapidamente com ações geradas por código

Ao clicar com o botão direito em um contêiner, você terá uma lista de gestos que dispararão um tempo de execução do Spark ou do SQL. A gravação em um contêiner ocorrerá por meio do repositório transacional do Azure Cosmos DB e consumirá Unidades de Solicitação.  

## <a name="next-steps"></a>Próximas etapas

* [Saiba o que é compatível entre o Azure Synapse e o Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Saiba como consultar o repositório analítico com o Spark](./how-to-query-analytical-store-spark.md)