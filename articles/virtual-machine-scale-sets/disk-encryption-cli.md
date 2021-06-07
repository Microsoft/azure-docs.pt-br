---
title: Criptografar discos para conjuntos de dimensionamento do Azure com a CLI do Azure
description: Saiba como usar a CLI do Azure para criptografar as instâncias de VM e discos conectados em um conjunto de dimensionamento de máquinas virtuais do Windows
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d347be4e6727cdda659620befe20824678160020
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792427"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Criptografar os discos de sistema operacional e de dados anexados em um conjunto de dimensionamento de máquinas virtuais com a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este início rápido mostra como usar a CLI do Azure para criar e criptografar um conjunto de dimensionamento de máquinas virtuais. Para saber mais sobre como aplicar a criptografia de disco do Azure em um conjunto de dimensionamento de máquinas virtuais, confira [Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais](disk-encryption-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.31 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-scale-set"></a>Criar um conjunto de escala

Antes de criar uma máquina virtual, crie um grupo de recursos com o [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie um conjunto de dimensionamento de máquinas virtuais com [az vmss create](/cli/azure/vmss). O exemplo a seguir cria um conjunto de dimensionamento chamado *myScaleSet* que está definido para atualizar automaticamente à medida que alterações são aplicadas e gera chaves SSH caso elas não existam em *~/.ssh/id_rsa*. Um disco de dados de 32Gb é anexado a cada instância VM e a [extensão de Script personalizado](../virtual-machines/extensions/custom-script-linux.md) do Azure é usada para preparar os discos de dados com [az vmss extension set](/cli/azure/vmss/extension):

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Criar um cofre de chaves do Azure habilitado para criptografia de disco

O Cofre de Chaves do Azure pode armazenar chaves, segredos ou senhas que permitem implementá-los de forma segura em seus aplicativos e serviços. Chaves e criptográficas são armazenadas no Cofre de chaves do Azure usando a proteção de software ou você pode importar ou gerar as chaves em Módulos de segurança de Hardware (HSMs) certificados para padrões de nível 2 de FIPS 140-2. Essas chaves criptográficas são usadas para criptografar e descriptografar os discos virtuais conectados à sua VM. Você mantém o controle dessas chaves criptográficas e pode auditar seu uso.

Defina seu próprio *keyvault_name* exclusivo. Em seguida, crie um cofre de chaves com [az keyvault create](/cli/azure/keyvault#ext-keyvault-preview-az-keyvault-create) na mesma assinatura e região do conjunto de dimensionamento e defina a política de acesso *--enabled-for-disk-encryption*.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Usar um Key Vault existente

Essa etapa só é necessária se você tiver um Key Vault existente que você deseje usar com a criptografia de disco. Ignore esta etapa se você tiver criado um Key Vault na seção anterior.

Defina seu próprio *keyvault_name* exclusivo. Em seguida, atualize o KeyVault com [az keyvault update](/cli/azure/keyvault#ext-keyvault-preview-az-keyvault-update) e defina a política de acesso *--enabled-for-disk-encryption*.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Habilitar criptografia

Para criptografar as instâncias de VM em um conjunto de dimensionamento, primeiro obtenha algumas informações sobre a ID de recursos do Key Vault com [az keyvault show](/cli/azure/keyvault#ext-keyvault-preview-az-keyvault-show). Essas variáveis são usadas para iniciar o processo de criptografia com [az vmss encryption enable](/cli/azure/vmss/encryption#az_vmss_encryption_enable):

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Pode levar um minuto ou dois para iniciar o processo de criptografia.

Como o conjunto de dimensionamento atualiza a política no conjunto de dimensionamento criado em uma etapa anterior definida como *automática*, as instâncias de VM iniciam automaticamente o processo de criptografia. Em conjuntos de dimensionamento em que a política de atualização é manual, inicie a política de criptografia nas instâncias de VM com [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Habilitar criptografia usando KEK para encapsular a chave

Também é possível usar uma Chave de Criptografia de Chave para aumentar a segurança ao criptografar o conjunto de dimensionamento de máquinas virtuais.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  A sintaxe para o valor do parâmetro disk-encryption-keyvault é a cadeia de caracteres completa do identificador:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para o KEK, como em:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Verificar o andamento da criptografia

Para verificar o status da criptografia de disco, use [az vmss encryption show](/cli/azure/vmss/encryption#az_vmss_encryption_show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Quando instâncias de VM são criptografadas, o código relata *EncryptionState/encrypted*, conforme mostrado no seguinte exemplo de saída:

```console
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Desabilitar criptografia

Se você não quiser mais usar discos de instâncias de VM criptografadas, você pode desabilitar a criptografia com [az vmss encryption disable](/cli/azure/vmss/encryption#az_vmss_encryption_disable) da seguinte maneira:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Próximas etapas

- Neste artigo, você usou a CLI do Azure para criptografar um conjunto de dimensionamento de máquinas virtuais. Também é possível usar o [Azure PowerShell](disk-encryption-powershell.md) ou os [modelos do Azure Resource Manager](disk-encryption-azure-resource-manager.md).
- Caso queira aplicar Azure Disk Encryption depois que outra extensão for provisionada, você poderá usar o [sequenciamento de extensão](virtual-machine-scale-sets-extension-sequencing.md). 
- Um exemplo de arquivo em lotes de ponta a ponta para criptografia de disco de dados do conjunto de dimensionamento Linux pode ser encontrado [aqui](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). Este exemplo cria um grupo de recursos e o conjunto de dimensionamento Linux, monta um disco de dados de 5 GB e criptografa o conjunto de dimensionamento de máquinas virtuais.
