---
title: "Criar um endereço IP público zoneado com a CLI do Azure | Microsoft Docs"
description: "Crie um IP público em uma zona de disponibilidade com a CLI do Azure."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ef93d43bbd0c58950027810c8c335d9076574326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-cli"></a>Criar um endereço IP público em uma zona de disponibilidade com a CLI do Azure

Você pode implantar um endereço IP público em uma zona de disponibilidade do Azure (versão prévia). Uma zona de disponibilidade é uma zona fisicamente separada em uma região do Azure. Saiba como:

> * Criar um endereço IP público em uma zona de disponibilidade
> * Identificar recursos relacionados criados na zona de disponibilidade

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute uma versão da CLI do Azure posterior a 2.0.17. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

> [!NOTE]
> As zonas de disponibilidade estão em versão prévia e prontas para seus cenários de desenvolvimento e teste. O suporte está disponível para famílias de tamanhos de VM, regiões e recursos do Azure selecionados. Para obter mais informações sobre como começar e com quais recursos, regiões e famílias de tamanhos de VM do Azure você pode experimentar as zonas de disponibilidade, confira [Visão geral das Zonas de Disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, entre em contato em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abra um tíquete de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md).

## <a name="create-a-zonal-public-ip-address"></a>Criar um endereço IP público zonal

Crie um endereço IP público em uma zona de disponibilidade usando o seguinte comando:


```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIp --zone 2 --location westeurope
```
> [!NOTE]
> Quando você atribui um endereço IP público de SKU Standard ao adaptador de rede de uma máquina virtual, você deve permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até que você crie e associe um grupo de segurança de rede e permita o tráfego desejado explicitamente.

## <a name="get-zone-information-about-a-public-ip-address"></a>Obter informações de zona sobre um endereço IP público

Obter as informações de zona de um endereço IP público usando o seguinte comando:

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIp
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview)
- Saiba mais sobre [endereços IP públicos](../virtual-network/virtual-network-public-ip-address.md) 
