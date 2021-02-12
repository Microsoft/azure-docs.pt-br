---
title: Criar um IP público-portal do Azure
description: Saiba como criar um IP público no portal do Azure
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 7d0c83f1ae18d36557a7a5b0222aee2905e05cb7
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550228"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Início rápido: criar um endereço IP público usando o portal do Azure

Este artigo mostra como criar um recurso de endereço IP público usando o portal do Azure. Para obter mais informações sobre a quais recursos isso pode ser associado, a diferença entre o SKU básico e Standard e outras informações relacionadas, consulte [endereços IP públicos](./public-ip-addresses.md).  Para este exemplo, nos concentraremos somente nos endereços IPv4; para obter mais informações sobre endereços IPv6, consulte [IPv6 para VNet do Azure](./ipv6-overview.md).

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-create-public-ip-standard-zones)

Use as etapas a seguir para criar um endereço IP público com redundância de zona padrão chamado **myStandardZRPublicIP**.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, digite *Endereço IP público*.
4. Nos resultados da pesquisa, selecione **Endereço IP público**. Em seguida, na página **Endereço IP público**, selecione **Criar**.
5. Na página **criar endereço IP público** , insira ou selecione as seguintes informações: 

    | Setting                 | Valor                       |
    | ---                     | ---                         |
    | Versão IP              | Selecionar IPv4                 |    
    | SKU                     | Selecione **Standard**         |
    | Camada (se mostrado *)                  | Selecionar **regional**         |
    | Name                    | Insira *myStandardZRPublicIP*          |
    | Atribuição de endereço IP   | Observe que isso será bloqueado como "estático"                                        |
    | Tempo limite de ociosidade (minutos)  | Deixe o valor em 4        |
    | Rótulo do nome DNS          | Deixe o valor em branco    |
    | Subscription            | Selecione sua assinatura.   |
    | Resource group          | Selecione **criar novo** , insira MyResource e, em seguida, selecione **OK** |
    | Location                | Selecione **Leste dos EUA 2**      |
    | Zona de disponibilidade       | Selecione **com redundância de zona**, sem zona ou selecione uma zona específica (veja a observação abaixo) |

Observe que essas são apenas seleções válidas em regiões com [zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).  (Você também pode selecionar uma zona específica nessas regiões, embora ela não seja resiliente a falhas zonais.)  Para obter mais informações sobre zonas de disponibilidade, consulte [visão geral das zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview).

\* = Camada relacionada à funcionalidade de [Load Balancer entre regiões](../load-balancer/cross-region-overview.md) , atualmente em visualização.

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-create-public-ip-basic)

Use as etapas a seguir para criar um endereço IP público estático básico chamado **myBasicPublicIP**.  Os IPs públicos básicos não têm o conceito de zonas de disponibilidade.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, digite *Endereço IP público*.
4. Nos resultados da pesquisa, selecione **Endereço IP público**. Em seguida, na página **Endereço IP público**, selecione **Criar**.
5. Na página **criar endereço IP público** , insira ou selecione as seguintes informações: 

    | Setting                 | Valor                       |
    | ---                     | ---                         |
    | Versão IP              | Selecionar IPv4                 |    
    | SKU                     | Selecione **Standard**         |
    | Name                    | Insira *myBasicPublicIP*          |
    | Atribuição de endereço IP   | Escolha **estático** (veja a observação abaixo)                                     |
    | Tempo limite de ociosidade (minutos)  | Deixe o valor em 4        |
    | Rótulo do nome DNS          | Deixe o valor em branco    |
    | Subscription            | Selecione sua assinatura.   |
    | Resource group          | Selecione **criar novo** , insira MyResource e, em seguida, selecione **OK** |
    | Location                | Selecione **Leste dos EUA 2**      |

Se for aceitável que o endereço IP seja alterado ao longo do tempo, a atribuição de IP **dinâmico** poderá ser selecionada.

---

## <a name="additional-information"></a>Informações adicionais 

Para obter mais detalhes sobre os campos individuais listados acima, consulte [gerenciar endereços IP públicos](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Próximas etapas
- Associar um [endereço IP público a uma máquina virtual](./associate-public-ip-address-vm.md#azure-portal)
- Saiba mais sobre os [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) no Azure.
- Saiba mais sobre todas as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address).
