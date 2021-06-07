---
title: Guia de início rápido para criar um cofre dos Serviços de Recuperação do Azure usando um modelo do Azure Resource Manager.
description: Neste guia de início rápido, você aprende a criar um cofre dos Serviços de Recuperação do Azure usando um modelo do ARM (Azure Resource Manager).
ms.date: 04/29/2020
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 11a88b5485ad970802a65af31daccdb30a1c86df
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533260"
---
# <a name="quickstart-create-a-recovery-services-vault-using-an-arm-template"></a>Início Rápido: Criar um cofre dos Serviços de Recuperação usando um modelo do ARM

Este guia de início rápido descreve como configurar um cofre dos Serviços de Recuperação usando um modelo do ARM (Azure Resource Manager). O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de BCDR (continuidade dos negócios e recuperação de desastres) para que seus aplicativos de negócios se mantenham online durante interrupções planejadas e não planejadas. O Site Recovery gerencia a recuperação de desastre de máquinas locais e de VMs (máquinas virtuais) do Azure, incluindo replicação, failover e recuperação.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura ativa do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/).

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json":::

Há dois recursos do Azure definidos no modelo:

- [Microsoft.RecoveryServices vaults](/azure/templates/microsoft.recoveryservices/vaults): cria o cofre.
- [Microsoft.RecoveryServices/vaults/backupstorageconfig](/rest/api/backup/backupresourcestorageconfigs): define as configurações de redundância de backup do cofre.

O modelo inclui parâmetros opcionais para a configuração do backup do cofre. As configurações de redundância de armazenamento são LRS (armazenamento com redundância local) ou GRS (armazenamento com redundância geográfica). Para obter mais informações, confira [Definir redundância de armazenamento](../backup/backup-create-rs-vault.md#set-storage-redundancy).

Para obter mais modelos de Serviços de Recuperação do Azure, confira [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implantar o modelo

Para implantar o modelo, a **Assinatura**, o **Grupo de recursos** e o **Nome do cofre** são necessários.

1. Para entrar no Azure e abrir o modelo, selecione a imagem **Implantar no Azure**.

   [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. Selecione ou insira os valores a seguir:

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Modelo para criar um cofre dos Serviços de Recuperação.":::

   - **Assinatura**: selecione sua assinatura do Azure.
   - **Grupo de recursos**: selecione um grupo existente ou selecione **Criar** para adicionar um grupo.
   - **Localização**: o padrão é a localização do grupo de recursos e fica não disponível após a seleção de um grupo de recursos.
   - **Nome do cofre**: Forneça um nome para o cofre.
   - **Alterar o Tipo de Armazenamento**: O padrão é **false**. Selecione **true** somente se você precisar alterar o tipo de armazenamento do cofre.
   - **Tipo de Armazenamento do Cofre**: o padrão é **GloballyRedundant**. Se o tipo de armazenamento tiver sido definido como **true**, selecione **LocallyRedundant**.
   - **Localização**: a função `[resourceGroup().location]` usa como padrão a localização do grupo de recursos. Para alterar a localização, insira um valor, como **westus**.
   - Marque a caixa de seleção **Concordo com os termos e condições declarados acima**.

1. Para iniciar a implantação do cofre, selecione o botão **Comprar**. Após uma implantação bem-sucedida, uma notificação é exibida.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="A implantação do cofre foi bem-sucedida.":::

## <a name="validate-the-deployment"></a>Validar a implantação

Para confirmar a criação do cofre, use a CLI do Azure ou o Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the vault name:" &&
read vaultName &&
az backup vault show --name $vaultName --resource-group $resourceGroupName &&
az backup vault backup-properties show --name $vaultName --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
$vaultName = Read-Host -Prompt "Enter the vault name"
$vaultBackupConfig = Get-AzRecoveryServicesVault -Name $vaultName
Get-AzRecoveryServicesVault -ResourceGroupName $resouceGroupName -Name $vaultName
Get-AzRecoveryServicesBackupProperty -Vault $vaultBackupConfig
Write-Host "Press [ENTER] to continue..."
```

---

A seguinte saída é um excerto das informações do cofre:

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
"id": "/subscriptions/<Subscription Id>/resourceGroups/myResourceGroup
         /providers/Microsoft.RecoveryServices/vaults/myVault"
"location": "eastus"
"name": "myVault"
"resourceGroup": "myResourceGroup"

"storageModelType": "GeoRedundant"
"storageType": "GeoRedundant"
"type": "Microsoft.RecoveryServices/vaults/backupstorageconfig"
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : myVault
Type              : Microsoft.RecoveryServices/vaults
Location          : eastus
ResourceGroupName : myResourceGroup
SubscriptionId    : <Subscription Id>

BackupStorageRedundancy
-----------------------
GeoRedundant
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Se você planeja usar os novos recursos, nenhuma ação é necessária. Caso contrário, você pode remover o grupo de recursos e o cofre criados neste guia de início rápido. Para excluir o grupo de recursos e seus recursos, use a CLI do Azure ou o Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resouceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um cofre dos Serviços de Recuperação. Para saber mais sobre a recuperação de desastre, prossiga para o próximo artigo de início rápido.

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastre](azure-to-azure-quickstart.md)
