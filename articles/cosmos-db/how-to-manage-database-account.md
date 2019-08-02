---
title: Aprender a gerenciar contas de banco de dados no Azure Cosmos DB
description: Aprender a gerenciar contas de banco de dados no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 0abeb3235f296e2dc873bcfe88910cdd12555d71
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476207"
---
# <a name="manage-an-azure-cosmos-account"></a>Gerenciar uma conta do Azure Cosmos

Este artigo descreve como gerenciar várias tarefas em uma conta do Azure Cosmos usando o portal do Azure, o Azure PowerShell, a CLI do Azure e modelos do Azure Resource Manager.

## <a name="create-an-account"></a>Criar uma conta

### <a id="create-database-account-via-portal"></a>Portal do Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```azurecli-interactive
# Create an account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case.

az cosmosdb create \
   --name $accountName \
   --resource-group $resourceGroupName \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=WestUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=EastUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations true
```

### <a id="create-database-account-via-ps"></a>PowerShell do Azure
```azurepowershell-interactive
# Create an Azure Cosmos account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="create-database-account-via-arm-template"></a>Modelo do Azure Resource Manager

Este modelo do Azure Resource Manager criará uma conta do Azure Cosmos para qualquer API compatível configurada com duas regiões e as opções para selecionar o nível de consistência, o failover automático e vários mestres. Para implantar esse modelo, clique em Implantar no Azure na página do arquivo Leiame, [Criar uma conta do Azure Cosmos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-create-multi-region-account)

## <a name="addremove-regions-from-your-database-account"></a>Adicionar ou remover regiões de sua conta de banco de dados

### <a id="add-remove-regions-via-portal"></a>Portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com). 

1. Vá até sua conta do Azure Cosmos e abra o menu **Replicar dados globalmente**.

1. Para adicionar regiões, selecione os hexágonos no mapa com o rótulo **+** que corresponde às regiões desejadas. Como alternativa, para adicionar uma região, escolha a opção **+Adicionar região** e escolha uma região no menu suspenso.

1. Para remover regiões, desmarque uma ou mais regiões do mapa marcando os hexágonos azuis com marcas de seleção. Ou escolha o ícone de "cesto de lixo" (🗑) ao lado da região no canto direito.

1. Para salvar suas alterações, escolha **OK**.

   ![Adicionar ou remover o menu de regiões](./media/how-to-manage-database-account/add-region.png)

Em um modo de gravação de região única, não é possível remover a região de gravação. Você deverá realizar o failover para uma região diferente antes de excluir a região de gravação atual.

Em um modo de gravação de várias regiões, você poderá adicionar ou remover qualquer região, se você tiver pelo menos uma região.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

# Create an account with 1 region
az cosmosdb create --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False --locations regionName=EastUS failoverPriority=1 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False
```

### <a id="add-remove-regions-via-ps"></a>PowerShell do Azure

```azurepowershell-interactive
# Create an account with 1 region
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( 
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East Us"; "failoverPriority"=1 } 
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="configure-multiple-write-regions"></a>Configurar várias regiões de gravação

### <a id="configure-multiple-write-regions-portal"></a>Portal do Azure

Abra a guia **Replicar Dados Globalmente** e selecione **Habilitar** para habilitar gravações de várias regiões. Depois que você habilitar gravações de várias regiões, todas as regiões de leitura que você tiver na conta no momento se tornarão regiões de leitura e gravação. 

> [!NOTE]
> Depois de habilitar gravações de várias regiões, você não poderá desabilitá-las. 

![Captura de tela da configuração de vários mestres da conta do Azure Cosmos](./media/how-to-manage-database-account/single-to-multi-master.png)

Contate o alias askcosmosdb@microsoft.com em caso de outras dúvidas sobre esse recurso. 

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'
az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-ps"></a>PowerShell do Azure

```azurepowershell-interactive
# Update an Azure Cosmos account from single to multi-master

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="configure-multiple-write-regions-arm"></a>modelo do Resource Manager

Uma conta pode ser migrada de mestre único para vários mestres com a implantação do modelo do Resource Manager usado para criar a conta e a configuração `enableMultipleWriteLocations: true`. O modelo do Azure Resource Manager a seguir é um modelo mínimo possível que implantará uma conta do Azure Cosmos para a API do SQL com uma única região e vários mestres habilitados.

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
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations": [
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Habilitar failover automático a conta do Azure Cosmos

A opção Failover automático permite que o Azure Cosmos DB faça failover para a região com a prioridade mais alta de failover sem nenhuma ação do usuário, caso uma região fique indisponível. Quando o failover automático estiver habilitado, a prioridade de região poderá ser modificada. A conta precisa ter duas ou mais regiões para habilitar o failover automático.

### <a id="enable-automatic-failover-via-portal"></a>Portal do Azure

1. Na sua conta do Azure Cosmos, abra o painel **Replicar dados globalmente**.

2. Escolha **Failover Automático** na parte superior do painel.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **Failover Automático**, verifique se a opção **Habilitar Failover Automático** está definida como **ATIVADO**. 

4. Clique em **Salvar**.

   ![Menu do portal Failover automático](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```azurecli-interactive
# Enable automatic failover on an existing account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-automatic-failover true
```

### <a id="enable-automatic-failover-via-ps"></a>PowerShell do Azure

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Definir prioridades de failover para a conta do Azure Cosmos

Depois que uma conta do Cosmos for configurada para failover automático, a prioridade de failover para regiões poderá ser alterada.

> [!IMPORTANT]
> Não é possível modificar a região de gravação (prioridade de failover igual a zero) quando a conta está configurada para failover automático. Para alterar a região de gravação, é necessário desabilitar o failover automático e fazer um failover manual.

### <a id="set-failover-priorities-via-portal"></a>Portal do Azure

1. Na sua conta do Azure Cosmos, abra o painel **Replicar dados globalmente**.

2. Escolha **Failover Automático** na parte superior do painel.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **Failover Automático**, verifique se a opção **Habilitar Failover Automático** está definida como **ATIVADO**.

4. Para modificar a prioridade de failover, arraste as regiões de leitura nos três pontos à esquerda da linha que aparecem quando o mouse é passado sobre ela.

5. Clique em **Salvar**.

   ![Menu do portal Failover automático](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```azurecli-interactive
# Assume region order is initially eastus=0 westus=1 southeastasia=2 on account creation
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb failover-priority-change --name $accountName --resource-group $resourceGroupName --failover-policies eastus=0 southeastasia=1 westus=2
```

### <a id="set-failover-priorities-via-ps"></a>PowerShell do Azure

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1, Southeast Asia = 2
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$failoverPolicies = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="Southeast Asia"; "failoverPriority"=1 },
    @{ "locationName"="East US"; "failoverPriority"=2 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a id="manual-failover"></a>Fazer um failover manual em uma conta do Azure Cosmos

> [!IMPORTANT]
> A conta do Azure Cosmos precisa ser configurada para failover manual para essa operação ser bem-sucedida.

O processo de execução de um failover manual envolve a alteração da região de gravação da conta (prioridade de failover = 0) para outra região configurada para a conta.

> [!NOTE]
> As contas de vários mestres não podem ter um failover manual. Para aplicativos que usam o SDK do Azure Cosmos, o SDK detectará quando uma região ficar indisponível e, em seguida, fará o redirecionamento automático para a próxima região mais perto se a API de hospedagem múltipla estiver sendo usada no SDK.

### <a id="enable-manual-failover-via-portal"></a>Portal do Azure

1. Vá até sua conta do Azure Cosmos e abra o menu **Replicar dados globalmente**.

2. Na parte superior do menu, escolha **Failover Manual**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No menu **Failover Manual**, escolha a nova região de gravação. Marque a caixa de seleção para indicar que você entende que essa opção alterará sua região de gravação.

4. Para disparar o failover, escolha **OK**.

   ![Menu do portal de failover manual](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```azurecli-interactive
# Assume account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False --locations regionName=eastus failoverPriority=1 isZoneRedundant=False
```

### <a id="enable-manual-failover-via-ps"></a>PowerShell do Azure

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

$account.Properties.locations=$locations;
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações e exemplos sobre como gerenciar a conta do Azure Cosmos, bem como o banco de dados e contêineres, leia os seguintes artigos:

* [Gerenciar o Azure Cosmos DB usando o Azure PowerShell](manage-with-powershell.md)
* [Gerenciar o Azure Cosmos DB usando a CLI do Azure](manage-with-cli.md)
