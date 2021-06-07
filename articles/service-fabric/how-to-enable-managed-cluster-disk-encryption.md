---
title: Habilitar a criptografia de disco para Service Fabric nós de cluster gerenciado (versão prévia)
description: Saiba como habilitar a criptografia de disco para o Azure Service Fabric nós de cluster gerenciados no Windows usando um modelo ARM.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b7e56ff8db9f94b8c6681a1a7d69a4751b3f43a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100642324"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-preview-nodes"></a>Habilitar a criptografia de disco para Service Fabric nós de cluster gerenciado (versão prévia)

Neste guia, você aprenderá a habilitar a criptografia de disco em Service Fabric nós de cluster gerenciados no Windows usando a capacidade de [Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md) para [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md) por meio de modelos de Azure Resource Manager (ARM).

> [!IMPORTANT]
> O conjunto de dimensionamento de máquinas virtuais visualização de criptografia de disco ainda não dá suporte à atualização ou à reimagem da imagem. Não use se for necessário atualizar a imagem do sistema operacional.

## <a name="register-for-azure-disk-encryption"></a>Registrar-se para Azure Disk Encryption

A visualização de criptografia de disco para o conjunto de dimensionamento de máquinas virtuais requer o auto-registro. Execute o comando a seguir:

```powershell
Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
```

Verifique o status do Registro executando:

```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
```

Depois que o status for alterado para *registrado*, você estará pronto para continuar.

## <a name="provision-a-key-vault-for-disk-encryption"></a>Provisionar um Key Vault para a criptografia de disco

O Azure Disk Encryption requer um Azure Key Vault para ajudar você a controlar e gerenciar os segredos e chaves de criptografia de disco. Seu Key Vault e Service Fabric cluster gerenciado devem residir na mesma região e assinatura do Azure. Contanto que esses requisitos sejam atendidos, você pode usar um Key Vault novo ou existente habilitando-o para a criptografia de disco.

### <a name="create-key-vault-with-disk-encryption-enabled"></a>Criar Key Vault com a criptografia de disco habilitada

Execute os comandos a seguir para criar um novo Key Vault para a criptografia de disco. Verifique se a região do seu Key Vault tem [suporte para Service Fabric clusters gerenciados](faq-managed-cluster.md#what-regions-are-supported-in-the-preview) e está na mesma região que o cluster.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
```

---

### <a name="update-existing-key-vault-to-enable-disk-encryption"></a>Atualizar Key Vault existentes para habilitar a criptografia de disco

Execute os comandos a seguir para habilitar a criptografia de disco para um Key Vault existente.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# ps 

Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az keyvault update --name keyvaultName --enabled-for-disk-encryption 
```

---

## <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>Atualizar o modelo e os arquivos de parâmetros para a criptografia de disco

A etapa a seguir explicará as alterações de modelo necessárias para habilitar a criptografia de disco em um [cluster gerenciado existente](tutorial-managed-cluster-deploy.md). Como alternativa, você pode implantar um novo Service Fabric cluster gerenciado com a criptografia de disco habilitada com este modelo: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. Adicione os seguintes parâmetros ao modelo, substituindo sua própria assinatura, o nome do grupo de recursos e o nome do cofre em `keyVaultResourceId` :

```json
"parameters": { 
…
    "keyVaultResourceId": { 
        "type": "string", 
        "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
        "metadata": { 
            "description": "Full resource id of the Key Vault used for disk encryption." 
        } 
    },
    "volumeType": { 
        "type": "string", 
        "defaultValue": "All", 
        "metadata": { 
            "description": "Type of the volume OS or Data to perform encryption operation" 
        }
    }
}, 
```

2. Em seguida, adicione a `AzureDiskEncryption` extensão de VM aos tipos de nó de cluster gerenciado no modelo:

```json
"properties": { 
    "vmExtensions": [ 
        { 
            "name": "AzureDiskEncryption", 
            "properties": { 
                "publisher": "Microsoft.Azure.Security", 
                "type": "AzureDiskEncryption", 
                "typeHandlerVersion": "2.1", 
                "autoUpgradeMinorVersion": true, 
                "settings": {                     
                    "EncryptionOperation": "EnableEncryption", 
                    "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
                    "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
                    "VolumeType": "[parameters('volumeType')]" 
                } 
            } 
        } 
    ] 
} 
```

3. Por fim, atualize o arquivo de parâmetros, substituindo sua própria assinatura, grupo de recursos e nome do cofre de chaves em *keyVaultResourceId*:

```json
"parameters": { 
    … 
    "keyVaultResourceId": { 
        "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
        "value": "All" 
    }    
} 
```

## <a name="deploy-and-verify-the-changes"></a>Implantar e verificar as alterações

Quando estiver pronto, implante as alterações para habilitar a criptografia de disco em seu cluster gerenciado.

```powershell
$clusterName = "<clustername>" 

New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\template_diskEncryption.json -TemplateParameterFile \.parameters_diskEncryption.json -Debug -Verbose 
```

Você pode verificar o status de criptografia do disco no conjunto de dimensionamento subjacente de um tipo de nó usando o `Get-AzVmssDiskEncryption` comando. Primeiro, você precisará encontrar o nome do grupo de recursos de suporte do seu cluster gerenciado (que contém a rede virtual subjacente, balanceador de carga, IP público, NSG, conjuntos de dimensionamento e contas de armazenamento). Certifique-se de modificar `VmssName` para qualquer nome de tipo de nó de cluster que você deseja verificar (conforme especificado no seu modelo de implantação).

```powershell
$VmssName = "NT1"
$supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName).ClusterId
Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
```

A saída deve ser semelhante a esta:

```console
ResourceGroupName            : SFC_########-####-####-####-############
VmScaleSetName               : NT1
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="next-steps"></a>Próximas etapas

[Exemplo: SKU padrão Service Fabric cluster gerenciado, 1 tipo de nó com criptografia de disco habilitada](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Azure Disk Encryption para VMs do Windows](../virtual-machines/windows/disk-encryption-overview.md)

[Criptografar os Conjuntos de Dimensionamento de Máquinas Virtuais com o Azure Resource Manager](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
