---
title: Restringir a exfiltração dos dados no Armazenamento do Azure – CLI do Azure
description: Neste artigo, você aprenderá a limitar e restringir a exfiltração dos dados de rede virtual nos recursos do Armazenamento do Azure com políticas de ponto de extremidade de serviço de rede virtual usando a CLI do Azure.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: fb48cbeb842fd9251067f9b8c4c83694e9749505
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063722"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Gerenciar a exfiltração dos dados em contas do Armazenamento do Azure com políticas de ponto de extremidade de serviço de rede virtual usando a CLI do Azure

As políticas de ponto de extremidade de serviço de rede virtual permitem que você aplique o controle de acesso às contas do Armazenamento do Azure em uma rede virtual nos pontos de extremidade de serviço. Essa é uma chave para proteger suas cargas de trabalho, gerenciar quais contas de armazenamento são permitidas e em que local a exfiltração dos dados é permitida.
Neste artigo, você aprenderá como:

* Criar uma rede virtual e adicionar uma sub-rede.
* Habilitar o ponto de extremidade de serviço para o Armazenamento do Azure.
* Criar duas contas do Armazenamento do Azure e permitir o acesso à rede a ele pela sub-rede criada acima.
* Criar uma política de ponto de extremidade de serviço para permitir o acesso somente a uma das contas de armazenamento.
* Implantar uma VM (máquina virtual) na sub-rede.
* Confirmar o acesso à conta de armazenamento permitida na sub-rede.
* Confirmar se o acesso foi negado à conta de armazenamento não permitida na sub-rede.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Antes de criar uma rede virtual, será necessário criar um grupo de recursos para a rede virtual e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Crie uma rede virtual com uma sub-rede com [az network vnet create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Habilitar um ponto de extremidade de serviço 

Neste exemplo, um ponto de extremidade de serviço para *Microsoft.Storage* é criado para a sub-rede *Private*: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Restringir o acesso à rede de uma sub-rede

Crie um grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg). O seguinte exemplo cria um grupo de segurança de rede chamado *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Associe o grupo de segurança de rede à sub-rede *Privada* com [az network vnet subnet update](/cli/azure/network/vnet/subnet). O exemplo a seguir associa o grupo de segurança de rede *myNsgPrivate* à sub-rede *Privada*:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Crie regras de segurança com [az network nsg rule create](/cli/azure/network/nsg/rule). A regra que segue permite o acesso de saída para os endereços IP públicos atribuídos ao serviço de Armazenamento do Microsoft Azure: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

Cada grupo de segurança de rede contém várias [regras de segurança padrão](./network-security-groups-overview.md#default-security-rules). A regra a seguir substitui uma regra de segurança padrão que permite o acesso de saída a todos os endereços IP públicos. A opção `destination-address-prefix "Internet"` nega o acesso de saída a todos os endereços IP públicos. A regra anterior substitui essa regra, devido à sua prioridade mais alta, o que permite acesso aos endereços IP públicos do Armazenamento do Microsoft Azure.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

A regra a seguir permite o tráfego SSH de entrada para a sub-rede de qualquer lugar. A regra substitui uma regra de segurança padrão que nega todo o tráfego da Internet. O SSH é permitido na sub-rede para que a conectividade possa ser testada em uma etapa posterior.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Restringir o acesso à rede nas contas do Armazenamento do Azure

Esta seção lista as etapas necessárias para restringir o acesso à rede em uma conta do Armazenamento do Azure na sub-rede especificada em uma rede virtual por meio do ponto de extremidade de serviço.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie duas contas de armazenamento do Azure com [az storage account create](/cli/azure/storage/account).

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Depois que as contas de armazenamento forem criadas, recupere a cadeia de conexão para a conta de armazenamento em uma variável com [az storage account show-connection-string](/cli/azure/storage/account). A cadeia de conexão é usada para criar um compartilhamento de arquivos em uma etapa posterior.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Exiba o conteúdo da variável e observe o valor para **AccountKey** retornado na saída, porque ele é usado em uma etapa posterior.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Criar um compartilhamento de arquivos na conta de armazenamento

Crie um compartilhamento de arquivos na conta de armazenamento com [az storage share create](/cli/azure/storage/share). Em uma etapa posterior, esse compartilhamento de arquivos é montado para confirmar o acesso à rede para ele.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>Negar todo acesso à rede na conta de armazenamento

Por padrão, as contas de armazenamento aceitam conexões de clientes em qualquer rede. Para limitar o acesso às redes selecionadas, altere a ação padrão para *Negar* com [az storage account update](/cli/azure/storage/account). Depois que o acesso à rede for negado, a conta de armazenamento não estará acessível em nenhuma rede.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Habilitar o acesso à rede em uma sub-rede de rede virtual

Permita o acesso à rede para a conta de armazenamento a partir da sub-rede *Privada* com [az storage account network-rule add](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Aplicar a política para permitir o acesso a uma conta de armazenamento válida

As políticas de ponto de extremidade de serviço do Azure só estão disponíveis para o Armazenamento do Azure. Portanto, habilitaremos o ponto de extremidade de serviço para *Microsoft.Storage* nessa sub-rede para este exemplo de instalação.

As políticas de ponto de extremidade de serviço são aplicadas em pontos de extremidades de serviço. Começaremos criando uma política de ponto de extremidade de serviço. Em seguida, criaremos as definições de política nessa política para que as contas do Armazenamento do Azure sejam aprovadas para essa sub-rede

Criar uma política de ponto de extremidade de serviço

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Salve o URI de recurso da conta de armazenamento permitida em uma variável. Antes de executar o comando abaixo, substitua *\<your-subscription-id>* pelo valor real da ID da assinatura.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Crie e adicione uma definição de política para permitir a conta do Armazenamento do Azure acima na política de ponto de extremidade de serviço

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

Além disso, atualize a sub-rede de rede virtual para associá-la à política de ponto de extremidade de serviço criada na etapa anterior

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Validar a restrição de acesso às contas de armazenamento do Azure

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Para testar o acesso à rede em uma conta de armazenamento, implante uma VM na sub-rede.

Crie uma VM na sub-rede *Private* com [az vm create](/cli/azure/vm). Se as chaves SSH ainda não existirem em uma localização de chave padrão, o comando criará. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

A VM demora alguns minutos para criar. Após a criação, anote o **publicIpAddress** na saída retornada. Esse endereço será usado para acessar a VM da internet em uma etapa posterior.

### <a name="confirm-access-to-storage-account"></a>Confirmar acesso à conta de armazenamento

SSH para a VM *myVmPrivate*. Substitua *\<publicIpAddress>* pelo endereço IP público de sua VM *myVmPrivate*.

```bash 
ssh <publicIpAddress>
```

Crie uma pasta para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Monte o compartilhamento de arquivos do Azure para o diretório que você criou. Antes de executar o comando abaixo, substitua *\<storage-account-key>* pelo valor de *AccountKey* em **$saConnectionString1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Você receberá o aviso `user@myVmPrivate:~$`. O compartilhamento de arquivos do Azure montado com êxito para */mnt/Retention/ MyAzureFileShare*.

### <a name="confirm-access-is-denied-to-storage-account"></a>Confirmar que o acesso é negado para a conta de armazenamento

Na mesma VM *myVmPrivate*, crie um diretório para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Tente montar o compartilhamento de arquivo do Azure da conta de armazenamento *notallowedstorageacc* no diretório criado. Este artigo presume que você implantou a versão mais recente do Ubuntu. Se você estiver usando versões anteriores do Ubuntu, consulte [Montar no Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter instruções adicionais sobre como montar compartilhamentos de arquivos. 

Antes de executar o comando abaixo, substitua *\<storage-account-key>* pelo valor de *AccountKey* em **$saConnectionString2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

O acesso é negado, e você recebe um erro `mount error(13): Permission denied`, pois essa conta de armazenamento não está na lista de permitidos da política de ponto de extremidade de serviço que aplicamos à sub-rede. 

Saia da sessão SSH para a VM *myVmPublic*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, use [az group delete](/cli/azure) para remover o grupo de recursos e todos os recursos que ele contém.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aplicou uma política de ponto de extremidade de serviço em um ponto de extremidade de serviço de rede virtual do Azure ao Armazenamento do Azure. Você criou contas do Armazenamento do Azure e limitou o acesso à rede a apenas determinadas contas de armazenamento (e, assim, negou o acesso a outras) em uma sub-rede de rede virtual. Para saber mais sobre as políticas de ponto de extremidade de serviço, confira [Visão geral das políticas de pontos de extremidades de serviço](virtual-network-service-endpoint-policies-overview.md).