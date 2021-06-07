---
title: Impedir que Azure Cosmos DB recursos sejam excluídos ou alterados
description: Use bloqueios de recursos do Azure para impedir que Azure Cosmos DB recursos sejam excluídos ou alterados.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/06/2020
ms.author: mjbrown
ms.openlocfilehash: 1c8c766208132aec115e1fbeb15af3a057c3de3e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94636683"
---
# <a name="prevent-azure-cosmos-db-resources-from-being-deleted-or-changed"></a>Impedir que Azure Cosmos DB recursos sejam excluídos ou alterados
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Como administrador, talvez seja necessário bloquear uma conta do Azure Cosmos, um banco de dados ou um contêiner para impedir que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos. É possível definir o nível de bloqueio como CanNotDelete ou ReadOnly.

- **CanNotDelete** significa que os usuários autorizados ainda poderão ler e modificar um recurso, mas não poderão excluir o recurso.
- **ReadOnly** significa que os usuários autorizados poderão ler um recurso, mas não poderão excluir ou atualizar o recurso. Aplicar esse bloqueio é semelhante ao restringir todos os usuários autorizados para as permissões concedidas pela função Leitor.

## <a name="how-locks-are-applied"></a>Como os bloqueios são aplicados

Quando você aplica um bloqueio a um escopo pai, todos os recursos filho herdam o mesmo bloqueio. Até mesmo os recursos que você adiciona posteriormente herdam o bloqueio do pai. O bloqueio mais restritivo na herança terá precedência.

Ao contrário do controle de acesso baseado em função do Azure, você usa bloqueios de gerenciamento para aplicar uma restrição em todos os usuários e funções. Para saber mais sobre o RBAC do Azure para Azure Cosmos DB consulte [controle de acesso baseado em função do Azure no Azure Cosmos DB](role-based-access-control.md).

Bloqueios do Resource Manager se aplicam apenas às operações que ocorrem no plano de gerenciamento, que consistem em operações enviadas para https://management.azure.com. Os bloqueios não restringem a maneira como os recursos executam suas próprias funções. Alterações de recursos são restritas, mas as operações de recursos não são. Por exemplo, um bloqueio ReadOnly em um contêiner Cosmos do Azure impede que você exclua ou modifique o contêiner. Ele não impede que você crie, atualize ou exclua dados no contêiner. As transações de dados são autorizadas porque essas operações não são enviadas para https://management.azure.com.

## <a name="manage-locks"></a>Gerenciar bloqueios

> [!WARNING]
> Os bloqueios de recurso não funcionam para alterações feitas por usuários que acessam Azure Cosmos DB usando chaves de conta, a menos que a conta do Azure Cosmos seja bloqueada primeiro, habilitando a propriedade disableKeyBasedMetadataWriteAccess. Deve-se ter cuidado antes de habilitar essa propriedade para garantir que ela não interrompa os aplicativos existentes que façam alterações nos recursos usando qualquer SDK, portal do Azure ou ferramentas de terceiros que se conectem por meio de chaves de conta e modifiquem recursos, como alteração de taxa de transferência, atualização de políticas de índice, etc. Para saber mais e passar por uma lista de verificação para garantir que seus aplicativos continuem a funcionar, consulte [impedindo alterações dos SDKs de Azure Cosmos DB](role-based-access-control.md#prevent-sdk-changes)

### <a name="powershell"></a>PowerShell

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "my-cosmos-account"
$lockName = "$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName -DisableKeyBasedMetadataWriteAccess true

# Create a Delete Lock on an Azure Cosmos account resource and all child resources
New-AzResourceLock `
    -ApiVersion "2020-04-01" `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ResourceGroupName $resourceGroupName `
    -ResourceName $accountName `
    -LockName $lockName `
    -LockLevel "CanNotDelete" # CanNotDelete or ReadOnly
```

### <a name="azure-cli"></a>CLI do Azure

```bash
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
$lockName="$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
az cosmosdb update  --name $accountName --resource-group $resourceGroupName  --disable-key-based-metadata-write-access true

# Create a Delete Lock on an Azure Cosmos account resource
az lock create --name $lockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/databaseAccount \
    --lock-type 'CanNotDelete' # CanNotDelete or ReadOnly \
    --resource $accountName
```

### <a name="template"></a>Modelo

Ao aplicar um bloqueio a um recurso de Azure Cosmos DB, use os seguintes formatos:

- nome - `{resourceName}/Microsoft.Authorization/{lockName}`
- tipo - `{resourceProviderNamespace}/{resourceType}/providers/locks`

> [!IMPORTANT]
> Ao modificar uma conta existente do Azure Cosmos, certifique-se de incluir as outras propriedades de sua conta e recursos filho quando redploying com essa propriedade. Não implante este modelo como está ou ele redefinirá todas as suas propriedades de conta.

```json
"resources": [
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-04-01",
        "kind": "GlobalDocumentDB",
        "location": "[parameters('location')]",
        "properties": {
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]",
            "disableKeyBasedMetadataWriteAccess": true
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/providers/locks",
        "apiVersion": "2020-04-01",
        "name": "[concat(variables('accountName'), '/Microsoft.Authorization/siteLock')]",
        "dependsOn": [
        "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]"
        ],
        "properties": {
        "level": "CanNotDelete",
        "notes": "Cosmos account should not be deleted."
        }
    }
]
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral de bloqueios de Azure Resource Manager](../azure-resource-manager/management/lock-resources.md)
