---
title: Configurar preferência de roteamento para uma VM - CLI do Azure
description: Saiba como criar uma VM com um endereço IP público com escolha de preferência de roteamento usando a CLI (interface de linha de comando) do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7673a42afa3b85a2aaf3f11f5e9b74fd46d48488
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945095"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Configurar preferência de roteamento para uma VM usando a CLI do Azure

Este artigo mostra como configurar a preferência de roteamento para uma máquina virtual. O tráfego associado à Internet que parte da VM será roteado por meio da rede do ISP quando você escolher **Internet** como opção de preferência de roteamento. O roteamento padrão é feito por meio da rede global da Microsoft.

Este artigo mostra como criar uma máquina virtual com um IP público definido para rotear o tráfego pela Internet pública usando a CLI do Azure.

> [!IMPORTANT]
> A preferência de roteamento está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
1. Se usar o Cloud Shell, vá para a etapa 2. Abra uma sessão de comando e entre no Azure com `az login`.
2. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos na região Leste do Azure dos EUA:

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>Criar um endereço IP público
Para acessar suas máquinas virtuais na Internet, você precisa criar um endereço IP público. Crie um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip). O exemplo a seguir cria um IP público do tipo *Internet* para a preferência de roteamento na região *Leste dos EUA*:

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

## <a name="create-network-resources"></a>Criar recursos da rede

Antes de implantar uma VM, você deve criar recursos da rede de suporte: grupo de segurança de rede, rede virtual e NIC virtual.

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede para as regras que controlarão a comunicação de entrada e saída em sua VNet com [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create)

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). O exemplo a seguir cria uma rede virtual chamada *myVNET* com sub-redes *mySubNet*:

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>Criar uma NIC

Crie uma NIC virtual para a VM com [az network nic create](/cli/azure/network/nic#az-network-nic-create). O exemplo a seguir cria uma NIC virtual, que será anexada à VM.

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). O exemplo a seguir cria uma VM do Windows Server 2019 e os componentes de rede virtual necessários, caso ainda não existam.

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos que ele contém:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [preferência de roteamento em endereços IP públicos](routing-preference-overview.md).
- Saiba mais sobre os [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) no Azure.
- Saiba mais sobre as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address).
