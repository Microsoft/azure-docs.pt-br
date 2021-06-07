---
title: Criar e criptografar uma VM do Linux com a CLI do Azure
description: Neste guia de início rápido, aprenda a usar a CLI do Azure para criar e criptografar uma máquina virtual do Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5b98fde5e15a3c57b56ecc8aea60023ffb8c22a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774297"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Início Rápido: criar e criptografar uma VM do Linux com a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este início rápido mostra como usar a CLI do Azure para criar e criptografar uma VM (máquina virtual) do Linux.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se você optar por instalar e usar a CLI do Azure localmente, este início rápido exigirá a execução da CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com [az vm create](/cli/azure/vm#az_vm_create). O exemplo a seguir cria uma VM chamada *myVM*.

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

A criação da VM e dos recursos de suporte demora alguns minutos. O seguinte exemplo de saída mostra que a operação de criação de VM foi bem-sucedida.

```
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
> Cada cofre de chaves deve ter um nome exclusivo no Azure. Nos exemplos a seguir, substitua <seu-nome-de-cofre-de-chaves-exclusivo> pelo nome que você escolher.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Criptografar a máquina virtual

Criptografe sua VM com [az vm encryption](/cli/azure/vm/encryption), fornecendo um nome exclusivo ao Key Vault com o parâmetro --disk-encryption-keyvault.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

Após alguns instantes, o processo retorna a mensagem "A solicitação de criptografia foi aceita. Use o comando 'show' para monitorar o progresso.". O comando "show" é [az vm show](/cli/azure/vm/encryption#az_vm_encryption_show).

```azurecli-interactive
az vm encryption show --name "myVM" -g "MyResourceGroup"
```

Quando a criptografia estiver ativada, você verá o seguinte na saída retornada:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos, a VM e o Key Vault. 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma máquina virtual, um Key Vault habilitado para chaves de criptografia e criptografou a VM.  Avance para o próximo artigo a fim de saber mais sobre o Azure Disk Encryption para VMs Linux.

> [!div class="nextstepaction"]
> [Visão geral do Azure Disk Encryption](disk-encryption-overview.md)
