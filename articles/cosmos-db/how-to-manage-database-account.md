---
title: Aprender a gerenciar contas de banco de dados no Azure Cosmos DB
description: Saiba como gerenciar recursos de Azure Cosmos DB usando os modelos portal do Azure, PowerShell, CLI e Azure Resource Manager
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/18/2020
ms.author: mjbrown
ms.openlocfilehash: d8763a794d2fb96d0c464fb1249b9eb400fd23e7
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339862"
---
# <a name="manage-an-azure-cosmos-account"></a>Gerenciar uma conta do Azure Cosmos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo descreve como gerenciar várias tarefas em uma conta do Azure Cosmos usando o portal do Azure, o Azure PowerShell, a CLI do Azure e modelos do Azure Resource Manager.

## <a name="create-an-account"></a>Criar uma conta

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Portal do Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>Azure CLI

Consulte [criar uma conta de Azure Cosmos DB com CLI do Azure](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

Consulte [criar uma conta de Azure Cosmos DB com o PowerShell](manage-with-powershell.md#create-account)

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Modelo do Azure Resource Manager

Consulte [criar conta de Azure Cosmos DB com modelos de Azure Resource Manager](./manage-with-templates.md)

## <a name="addremove-regions-from-your-database-account"></a>Adicionar ou remover regiões de sua conta de banco de dados

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Vá até sua conta do Azure Cosmos e abra o menu **Replicar dados globalmente**.

1. Para adicionar regiões, selecione os hexágonos no mapa com o **+** rótulo que corresponde às regiões desejadas. Como alternativa, para adicionar uma região, escolha a opção **+Adicionar região** e escolha uma região no menu suspenso.

1. Para remover regiões, desmarque uma ou mais regiões do mapa marcando os hexágonos azuis com marcas de seleção. Ou escolha o ícone de "cesto de lixo" (🗑) ao lado da região no canto direito.

1. Para salvar suas alterações, escolha **OK**.

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="Adicionar ou remover o menu de regiões":::

Em um modo de gravação de região única, não é possível remover a região de gravação. Você deverá realizar o failover para uma região diferente antes de excluir a região de gravação atual.

Em um modo de gravação de várias regiões, você poderá adicionar ou remover qualquer região, se você tiver pelo menos uma região.

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>Azure CLI

Consulte [Adicionar ou remover regiões com CLI do Azure](manage-with-cli.md#add-or-remove-regions)

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

Consulte [Adicionar ou remover regiões com o PowerShell](manage-with-powershell.md#update-account)

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Configurar várias regiões de gravação

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Portal do Azure

Abra a guia **Replicar Dados Globalmente** e selecione **Habilitar** para habilitar gravações de várias regiões. Depois que você habilitar gravações de várias regiões, todas as regiões de leitura que você tiver na conta no momento se tornarão regiões de leitura e gravação.

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="A conta do Azure Cosmos configura a captura de tela de gravações de várias regiões":::

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>Azure CLI

Consulte [habilitar regiões de várias gravações com CLI do Azure](manage-with-cli.md#enable-multiple-write-regions)

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Consulte [habilitar regiões de várias gravações com o PowerShell](manage-with-powershell.md#multi-region-writes)

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Modelo do Resource Manager

Uma conta pode ser migrada de uma única região de gravação para várias regiões de gravação implantando o modelo do Resource Manager usado para criar a conta e a configuração `enableMultipleWriteLocations: true` . O modelo do Azure Resource Manager a seguir é um modelo mínimo possível que implantará uma conta do Azure Cosmos para a API do SQL com duas regiões e vários locais de gravação habilitados.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Habilitar failover automático para conta do Azure Cosmos

A opção Failover automático permite que o Azure Cosmos DB faça failover para a região com a prioridade mais alta de failover sem nenhuma ação do usuário, caso uma região fique indisponível. Quando o failover automático estiver habilitado, a prioridade de região poderá ser modificada. A conta precisa ter duas ou mais regiões para habilitar o failover automático.

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Portal do Azure

1. Na sua conta do Azure Cosmos, abra o painel **Replicar dados globalmente**.

2. Escolha **Failover Automático** na parte superior do painel.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menu Replicar dados globalmente":::

3. No painel **Failover Automático** , verifique se a opção **Habilitar Failover Automático** está definida como **ATIVADO**. 

4. Selecione **Salvar**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Menu do portal Failover automático":::

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>Azure CLI

Consulte [habilitar failover automático com CLI do Azure](manage-with-cli.md#enable-automatic-failover)

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Consulte [habilitar failover automático com o PowerShell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Definir prioridades de failover para a conta do Azure Cosmos

Depois que uma conta do Cosmos for configurada para failover automático, a prioridade de failover para regiões poderá ser alterada.

> [!IMPORTANT]
> Não é possível modificar a região de gravação (prioridade de failover igual a zero) quando a conta está configurada para failover automático. Para alterar a região de gravação, é necessário desabilitar o failover automático e fazer um failover manual.

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Portal do Azure

1. Na sua conta do Azure Cosmos, abra o painel **Replicar dados globalmente**.

2. Escolha **Failover Automático** na parte superior do painel.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menu Replicar dados globalmente":::

3. No painel **Failover Automático** , verifique se a opção **Habilitar Failover Automático** está definida como **ATIVADO**.

4. Para modificar a prioridade de failover, arraste as regiões de leitura nos três pontos à esquerda da linha que aparecem quando o mouse é passado sobre ela.

5. Selecione **Salvar**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Menu do portal Failover automático":::

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>Azure CLI

Consulte [definir a prioridade de failover com CLI do Azure](manage-with-cli.md#set-failover-priority)

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Consulte [definir a prioridade de failover com o PowerShell](manage-with-powershell.md#modify-failover-priority)

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Fazer um failover manual em uma conta do Azure Cosmos

> [!IMPORTANT]
> A conta do Azure Cosmos precisa ser configurada para failover manual para essa operação ser bem-sucedida.

O processo de execução de um failover manual envolve a alteração da região de gravação da conta (prioridade de failover = 0) para outra região configurada para a conta.

> [!NOTE]
> Não é possível fazer failover manual de contas com várias regiões de gravação. Para aplicativos que usam o SDK do Azure Cosmos, o SDK detectará quando uma região ficar indisponível e, em seguida, fará o redirecionamento automático para a próxima região mais perto se a API de hospedagem múltipla estiver sendo usada no SDK.

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Portal do Azure

1. Vá até sua conta do Azure Cosmos e abra o menu **Replicar dados globalmente**.

2. Na parte superior do menu, escolha **Failover Manual**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menu Replicar dados globalmente":::

3. No menu **Failover Manual** , escolha a nova região de gravação. Marque a caixa de seleção para indicar que você entende que essa opção alterará sua região de gravação.

4. Para disparar o failover, escolha **OK**.

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="Menu do portal de failover manual":::

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>Azure CLI

Consulte [disparar failover manual com CLI do Azure](manage-with-cli.md#trigger-manual-failover)

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Consulte [disparar failover manual com o PowerShell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações e exemplos sobre como gerenciar a conta do Azure Cosmos, bem como o banco de dados e contêineres, leia os seguintes artigos:

* [Gerenciar o Azure Cosmos DB usando o Azure PowerShell](manage-with-powershell.md)
* [Gerenciar o Azure Cosmos DB usando a CLI do Azure](manage-with-cli.md)