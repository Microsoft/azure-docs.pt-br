---
title: Criar e criptografar uma VM do Windows com a CLI do Azure
description: Neste início rápido, você aprende a usar a CLI do Azure para criar e criptografar uma máquina virtual Windows
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: e81d29922bee53ba9021c9c26816258f7922a59c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759697"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Início Rápido: Criar e criptografar uma VM do Windows com a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este início rápido mostra como usar a CLI do Azure para criar e criptografar uma VM (máquina virtual) do Windows Server 2016.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.30 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com [az vm create](/cli/azure/vm#az_vm_create). O exemplo a seguir cria uma VM chamada *myVM*. Este exemplo usa o *azureuser* para um nome de usuário administrativo e *myPassword12* como a senha.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

A criação da VM e dos recursos de suporte demora alguns minutos. O seguinte exemplo de saída mostra que a operação de criação de VM foi bem-sucedida.

```console
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Criar um Key Vault configurado para chaves de criptografia

A criptografia de disco do Azure armazena sua chave de criptografia em um Azure Key Vault. Crie um Key Vault com [az keyvault create](/cli/azure/keyvault#az_keyvault_create). Para habilitar o Key Vault para armazenar chaves de criptografia, use o parâmetro --enabled-for-disk-encryption.
> [!Important]
> Cada Key Vault deve ter um nome exclusivo. O exemplo a seguir cria um Key Vault chamado *myKV*, mas você deve colocar um nome diferente.

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Criptografar a máquina virtual

Criptografe sua VM com [az vm encryption](/cli/azure/vm/encryption), fornecendo um nome exclusivo ao Key Vault com o parâmetro --disk-encryption-keyvault.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Você pode verificar que a criptografia está habilitada na sua VM com [show az vm](/cli/azure/vm/encryption#az_vm_encryption_show)

```azurecli-interactive
az vm encryption show --name MyVM -g MyResourceGroup
```

Você verá o seguinte retornado na saída:

```console
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos, a VM e o Key Vault.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma máquina virtual, um Key Vault habilitado para chaves de criptografia e criptografou a VM.  Avance para o próximo artigo a fim de saber mais sobre os pré-requisitos do Azure Disk Encryption para VMs IaaS.

> [!div class="nextstepaction"]
> [Visão geral do Azure Disk Encryption](disk-encryption-overview.md)
