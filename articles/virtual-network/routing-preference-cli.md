---
title: Configurar a preferência de roteamento para um endereço IP público usando a CLI do Azure
titlesuffix: Azure Virtual Network
description: Saiba como criar um IP público com uma preferência de roteamento de tráfego de Internet usando o CLI do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 2291767c162953f8339fb8cc27e55b96290ef795
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101665956"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Configurar a preferência de roteamento para um endereço IP público usando a CLI do Azure

Este artigo mostra como configurar a preferência de roteamento via rede ISP (opção **Internet**) para um endereço IP público usando a CLI do Azure. Depois de criar o endereço IP público, você pode associá-lo aos seguintes recursos do Azure para os tráfegos de entrada e saída para a Internet:

* Máquina virtual
* Conjunto de escala de máquina virtual
* AKS (Serviço de Kubernetes do Azure)
* Balanceador de carga voltado para a Internet
* Gateway de Aplicativo
* Firewall do Azure

Por padrão, a preferência de roteamento para o endereço IP público é definida para a rede global da Microsoft para todos os serviços do Azure e pode ser associada a qualquer serviço do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.49 ou posterior do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos na região **Leste dos EUA** do Azure:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Crie um endereço IP público com preferência de roteamento de tipo de **Internet** usando o comando [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create), com o formato conforme mostrado abaixo.

O comando a seguir cria um novo IP público com a preferência de roteamento **Internet** na região **Leste dos EUA** do Azure.

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

> [!NOTE]
>  Atualmente, a preferência de roteamento dá suporte apenas a endereços IP públicos IPV4.

Você pode associar o endereço IP público criado acima a uma máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Use a seção CLI na página do tutorial: [Associe um endereço IP público a uma máquina virtual](associate-public-ip-address-vm.md#azure-cli) para associar o IP Público à sua VM. Você também pode associar o endereço IP público criado acima a um [Azure Load Balancer](../load-balancer/load-balancer-overview.md), atribuindo-o à configuração **front-end** do balanceador de carga. O endereço IP público serve como um endereço IP virtual de balanceamento de carga (VIP).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [preferência de roteamento em endereços IP públicos](routing-preference-overview.md). 
- [Configure a preferência de roteamento para uma VM usando a CLI do Azure](configure-routing-preference-virtual-machine-cli.md).

