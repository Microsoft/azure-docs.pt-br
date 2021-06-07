---
title: Criar um IP público-portal do Azure
description: Saiba como criar um IP público no portal do Azure
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: c1ac3f2fa1ef3f1a24077064ad3ad3f3c30c5f3f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048350"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Criar um endereço IP público usando o portal do Azure

Este artigo mostra como criar um recurso de endereço IP público usando o portal do Azure. 

Para obter mais informações sobre os recursos aos quais esse IP público pode ser associado e a diferença entre os SKUs básico e Standard, consulte [endereços IP públicos](./public-ip-addresses.md). 

Este artigo se concentra em endereços IPv4. Para obter mais informações sobre endereços IPv6, consulte [IPv6 para VNet do Azure](./ipv6-overview.md).

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-create-public-ip-standard-zones)

Use as etapas a seguir para criar um endereço IP público com redundância de zona padrão chamado **myStandardZRPublicIP**.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, insira **endereço IP público**. Selecione **endereço IP público** nos resultados da pesquisa.
4. Na página **endereço IP público** , selecione **criar**.
5. Na página **criar endereço IP público** , insira ou selecione as seguintes informações: 

    | Setting                 | Valor                       |
    | ---                     | ---                         |
    | Versão IP              | Selecionar IPv4                 |    
    | SKU                     | Selecione **Standard**         |
    | Preço                   | Selecionar **regional**         |
    | Nome                    | Insira **myStandardZRPublicIP**          |
    | Atribuição de endereço IP   | Observe que essa seleção está bloqueada como "estática"                                        |
    | Preferência de roteamento      | Deixe o padrão da **rede da Microsoft**. </br> Para obter mais informações sobre preferências de roteamento, confira [O que é uma preferência de roteamento (versão prévia)?](./routing-preference-overview.md). |
    | Tempo limite de ociosidade (minutos)  | Deixe o padrão de **4**.        |
    | Rótulo do nome DNS          | Deixe o valor em branco.    |
    | Subscription            | Selecione sua assinatura.   |
    | Resource group          | Selecione **criar novo**, insira **MyResource**. </br> Selecione **OK**. |
    | Localização                | Selecione **Leste dos EUA 2**      |
    | Zona de disponibilidade       | Selecione **com redundância de zona**, sem zona ou selecione uma zona específica (veja a observação abaixo) |

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Criar endereço IP padrão no portal do Azure" border="false":::

> [!NOTE]
> Essas seleções são válidas em regiões com [zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones). </br>
Você pode selecionar uma zona específica nessas regiões, embora ela não seja resiliente a falhas zonais. </br> Para obter mais informações sobre zonas de disponibilidade, consulte [visão geral das zonas de disponibilidade](../availability-zones/az-overview.md).

\* = Camada relacionada à funcionalidade de [balanceador de carga entre regiões](../load-balancer/cross-region-overview.md) , atualmente em visualização.

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-create-public-ip-basic)

Nesta seção, crie um endereço IP público básico chamado **myBasicPublicIP**. 

> [!NOTE]
> Os IPs públicos básicos não dão suporte a zonas de disponibilidade.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, insira **endereço IP público**. Selecione **endereço IP público** nos resultados da pesquisa.
4. Na página **endereço IP público** , selecione **criar**.
5. Na página **criar endereço IP público** , insira ou selecione as seguintes informações: 

    | Setting                 | Valor                       |
    | ---                     | ---                         |
    | Versão IP              | Selecionar IPv4                 |    
    | SKU                     | Selecione **Básico**         |
    | Nome                    | Insira *myBasicPublicIP*          |
    | Atribuição de endereço IP   | Selecione **estático** (veja a observação abaixo)                                     |
    | Tempo limite de ociosidade (minutos)  | Deixe o padrão de **4**.       |
    | Rótulo do nome DNS          | Deixe o valor em branco    |
    | Subscription            | Selecione sua assinatura.   |
    | Resource group          | Selecione **criar novo**, insira **MyResource**. </br> Selecione **OK**. |
    | Localização                | Selecione **Leste dos EUA 2**      |

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Criar endereço IP padrão no portal do Azure" border="false":::

Se for aceitável que o endereço IP seja alterado ao longo do tempo, a atribuição de IP **dinâmico** poderá ser selecionada.

---

## <a name="additional-information"></a>Informações adicionais 

Para obter mais informações sobre os campos individuais listados acima, consulte [gerenciar endereços IP públicos](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Próximas etapas
- Associar um [endereço IP público a uma máquina virtual](./associate-public-ip-address-vm.md#azure-portal)
- Saiba mais sobre os [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) no Azure.
- Saiba mais sobre todas as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address).