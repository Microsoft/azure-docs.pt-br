---
title: Configure pontos de extremidade privados para Azure Cosmos DB repositório analítico.
description: Saiba como configurar pontos de extremidade privados gerenciados para Azure Cosmos DB repositório analítico para restringir o acesso à rede.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: 2f15b397fbceb9e097d94080ba03fba50a96ed06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048498"
---
# <a name="configure-private-endpoints-for-azure-cosmos-db-analytical-store"></a>Configurar pontos de extremidade privados para o repositório analítico do Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Neste artigo, você aprenderá a configurar pontos de extremidade privados gerenciados para Azure Cosmos DB repositório analítico. Se você estiver usando o armazenamento transacional, consulte [pontos de extremidade privados para o artigo armazenamento transacional](how-to-configure-private-endpoints.md) . Usando pontos de extremidade privados gerenciados, você pode restringir o acesso à rede de Azure Cosmos DB repositório analítico para a rede virtual gerenciada do Azure Synapse. Pontos de extremidade privados gerenciados estabelecem um link privado para seu repositório analítico.

## <a name="enable-private-endpoint-for-the-analytical-store"></a>Habilitar ponto de extremidade privado para o repositório analítico

### <a name="set-up-an-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Configurar um espaço de trabalho do Azure Synapse Analytics com uma rede virtual gerenciada

[Crie um espaço de trabalho no Azure Synapse Analytics com o data-vazamento habilitado.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) Com a [proteção vazamento de dados](../synapse-analytics/security/workspace-data-exfiltration-protection.md), você pode garantir que os usuários mal-intencionados não possam copiar ou transferir dados dos recursos do Azure para locais fora do escopo da sua organização.

As seguintes restrições de acesso são aplicáveis quando a proteção de dados-vazamento está ativada para um espaço de trabalho do Azure Synapse Analytics:

* Se você estiver usando o Azure Spark para o Azure Synapse Analytics, o acesso só será permitido para os pontos de extremidade privados gerenciados para Azure Cosmos DB repositório analítico.

* Se você estiver usando pools SQL sem servidor Synapse, poderá consultar qualquer conta de Azure Cosmos DB usando o link Synapse do Azure. No entanto, as solicitações de gravação que [criam tabelas externas como SELECT (CETAS)](../synapse-analytics/sql/develop-tables-cetas.md) só são permitidas para os pontos de extremidade privados de gerenciamento aprovados na rede virtual do espaço de trabalho.

> [!NOTE]
> Você não pode alterar a rede virtual gerenciada e a configuração de vazamento de dados depois que o espaço de trabalho é criado.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Adicionar um ponto de extremidade privado gerenciado para Azure Cosmos DB repositório analítico

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

1. No portal do Azure, navegue até o espaço de trabalho do Synapse Analytics e abra o painel **visão geral** .

1. Inicie o Synapse Studio navegando até o painel **introdução** e selecione **abrir** em **abrir o Synapse Studio**.

1. No Synapse Studio, abra a guia **gerenciar** .

1. Navegue até **pontos de extremidade privados gerenciados** e selecione **novo**

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Crie um novo ponto de extremidade privado para o repositório analítico." border="true":::

1. Selecione o tipo **de conta Azure Cosmos dB (API do SQL)** > **continuar**.

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Selecione Azure Cosmos DB API do SQL para criar um ponto de extremidade privado." border="true":::

1. Preencha o novo formulário de **ponto de extremidade privado gerenciado** com os seguintes detalhes:

   * **Nome** -nome do ponto de extremidade privado gerenciado. Esse nome não pode ser atualizado depois de ser criado.
   * **Descrição** – forneça uma descrição amigável para identificar seu ponto de extremidade privado.
   * **Assinatura do Azure** – selecione uma conta de Azure Cosmos DB na lista de contas disponíveis em suas assinaturas do Azure.
   * **Nome da conta de Azure Cosmos DB** – selecione uma conta de Azure Cosmos DB existente do tipo SQL ou MongoDB.
   * **Subchave de destino** – selecione uma das seguintes opções: **analítica**: se você quiser adicionar o ponto de extremidade privado para Azure Cosmos DB repositório analítico.
     **SQL** (ou **MongoDB**): se você deseja adicionar o OLTP ou o ponto de extremidade da conta transacional.

   > [!NOTE]
   > Você pode adicionar os pontos de extremidade privados do repositório transacional e do repositório analítico à mesma conta de Azure Cosmos DB em um espaço de trabalho do Azure Synapse Analytics. Se você quiser apenas executar consultas analíticas, convém apenas mapear o ponto de extremidade privado analítico.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Escolha análise para o subrecurso de destino." border="true":::

1. Depois de criar, vá para o nome do ponto de extremidade privado e selecione **gerenciar aprovações em portal do Azure**.

1. Navegue até sua conta do Azure Cosmos DB, selecione o ponto de extremidade privado e selecione **aprovar**.

1. Navegue de volta para o espaço de trabalho do Synapse Analytics e clique em **Atualizar** no painel **pontos de extremidade privados gerenciados** . Verifique se o ponto de extremidade privado está no estado **aprovado** .

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Verifique se o ponto de extremidade privado foi aprovado." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Usar Apache Spark para o Azure Synapse Analytics

Se você criou um espaço de trabalho do Azure Synapse com a proteção de vazamento de dados ativada, o acesso de saída do Synapse Spark para Azure Cosmos DB contas será bloqueado, por padrão. Além disso, se o Azure Cosmos DB já tiver um ponto de extremidade privado existente, o Synapse Spark será impedido de acessá-lo.

Para permitir o acesso a Azure Cosmos DB dados:

* Se você estiver usando o link Synapse do Azure para consultar Azure Cosmos DB dados, adicione um ponto de extremidade de **análise** privada gerenciada para a conta de Azure Cosmos DB.

* Se você estiver usando gravações/leituras em lote e/ou gravações/leituras de streaming no repositório transacional, adicione um ponto de extremidade privado do *SQL* ou *MongoDB* gerenciado para a conta de Azure Cosmos DB. Além disso, você também deve definir *ConnectionMode* como *Gateway* , conforme mostrado no seguinte trecho de código:

  ```python
  # Write a Spark DataFrame into an Azure Cosmos DB container
  # To select a preferred lis of regions in a multi-region account, add .option("spark.cosmos.preferredRegions", "<Region1>, <Region2>")
  
  YOURDATAFRAME.write\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<your-Cosmos-DB-linked-service-name>")\
    .option("spark.cosmos.container","<your-Cosmos-DB-container-name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .option("spark.cosmos.connection.mode", "Gateway")\
    .mode('append')\
    .save()
  
  ```

## <a name="using-synapse-serverless-sql-pools"></a>Usando pools SQL sem servidor Synapse

Os pools SQL sem servidor Synapse usam recursos multilocatário que não são implantados na rede virtual gerenciada. Se a conta de Azure Cosmos DB tiver um ponto de extremidade privado existente, o pool SQL sem servidor Synapse será impedido de acessar a conta, devido a verificações de isolamento de rede na conta de Azure Cosmos DB.

Para configurar o isolamento de rede para essa conta de um espaço de trabalho Synapse:

1. Permita que o espaço de trabalho do Synapse acesse a conta de Azure Cosmos DB especificando a `NetworkAclBypassResourceId` configuração na conta.

   **Como usar o PowerShell**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Usando a CLI do Azure**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Azure Cosmos DB conta e o espaço de trabalho do Azure Synapse Analytics devem estar sob o mesmo locatário do Azure Active Directory (AD).

2. Agora você pode acessar a conta de pools SQL sem servidor, usando consultas T-SQL no link Synapse do Azure. No entanto, para garantir o isolamento de rede para os dados no repositório analítico, você deve adicionar um ponto de extremidade privado gerenciado **analítico** para essa conta. Caso contrário, os dados no repositório analítico não serão bloqueados do acesso público.

> [!IMPORTANT]
> Se você estiver usando o link Synapse do Azure e precisar de isolamento de rede para seus dados no repositório analítico, deverá mapear a conta de Azure Cosmos DB para o espaço de trabalho Synapse usando o ponto de extremidade privado gerenciado **analítico** .

## <a name="next-steps"></a>Próximas etapas

* Introdução à [consulta de repositório analítico com o Azure Synapse Spark](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Introdução à [consulta de repositório analítico com pools SQL sem servidor do Azure Synapse](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
