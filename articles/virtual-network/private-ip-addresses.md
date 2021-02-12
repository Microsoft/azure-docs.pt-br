---
title: Endereços IP privados no Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre endereços IP privados no Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 36db885cab734c037b0032c714de28b905595ef0
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223254"
---
# <a name="private-ip-addresses"></a>Endereços IP privados
Os IPs privados permitem a comunicação entre os recursos no Azure. 

Os recursos podem ser:
* Serviços do Azure, como:
    * Interfaces de rede de máquina virtual
    * ILBs (balanceadores de carga internos)
    * Gateways de aplicativo
* Em uma [rede virtual](virtual-networks-overview.md).
* Rede local por meio de um gateway de VPN ou circuito de ExpressRoute.

Os IPs privados permitem a comunicação com esses recursos sem o uso de um endereço IP público.

## <a name="allocation-method"></a>Método de alocação

O Azure atribui endereços IP privados a recursos do intervalo de endereços da sub-rede da rede virtual em que o recurso está.

O Azure reserva os primeiros quatro endereços em cada intervalo de endereços de sub-rede. Os endereços não podem ser atribuídos aos recursos. Por exemplo, se o intervalo de endereços da sub-rede for 10.0.0.0/16, os endereços 10.0.0.0-10.0.0.3 e 10.0.255.255 não estarão disponíveis. Endereços IP no intervalo de endereços da sub-rede só podem ser atribuídos a um recurso por vez. 

Há dois métodos em que um endereço IP privado é fornecido:

- **Dinâmico**: o Azure atribui o próximo endereço IP disponível não atribuído ou não reservado no intervalo de endereços da sub-rede. Por exemplo, o Azure atribui o 10.0.0.10 para um novo recurso se endereços 10.0.0.4-10.0.0.9 já estiverem atribuídos a outros recursos. 

    Dinâmico é o método de alocação padrão. Depois de atribuídos, os endereços IP dinâmicos serão liberados se uma interface de rede for:
    
    * Excluído
    * Reatribuído a uma sub-rede diferente dentro da mesma rede virtual.
    * O método de alocação é alterado para estático e um endereço IP diferente é especificado. 
    
    Por padrão, o Azure atribui o endereço anterior atribuído dinamicamente como o endereço estático quando você altera o método de alocação de dinâmico para estático.

- **Estático**: você seleciona e atribui qualquer endereço IP disponível não atribuído ou não reservado no intervalo de endereços da sub-rede. 

    Por exemplo, o intervalo de endereços de uma sub-rede é 10.0.0.0/16 e os endereços 10.0.0.4-10.0.0.9 são atribuídos a outros recursos. Você pode atribuir qualquer endereço entre 10.0.0.10-10.0.255.254. Os endereços estáticos só são liberados se um adaptador de rede é excluído. 
    
    O Azure atribui o IP estático como o IP dinâmico quando o método de alocação é alterado. A reatribuição ocorre mesmo que o endereço não seja o próximo disponível na sub-rede. O endereço muda quando a interface de rede é atribuída a uma sub-rede diferente.
    
    Para atribuir a interface de rede a uma sub-rede diferente, altere o método de alocação de estático para dinâmico. Atribua a interface de rede a uma sub-rede diferente e altere o método de alocação de volta para estático. Atribua um endereço IP do intervalo de endereços da nova sub-rede.
    
## <a name="virtual-machines"></a>Máquinas virtuais

Um ou mais endereços IP privados são atribuídos a uma ou mais **interfaces de rede**. As interfaces de rede são atribuídas a uma máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Você pode especificar o método de alocação como dinâmico ou estático para um endereço IP privado.

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Resolução do nome do host DNS interno (para máquinas virtuais)

As máquinas virtuais do Azure são configuradas com [servidores DNS gerenciados pelo Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) por padrão. Você pode configurar explicitamente servidores DNS personalizados. Esses servidores DNS fornecem resolução de nomes interna para máquinas virtuais que estão dentro da mesma rede virtual.

Um mapeamento para o nome de host para o endereço IP privado de uma máquina virtual é adicionado aos servidores DNS gerenciados pelo Azure. 

Um nome de host é mapeado para o IP primário da interface de rede principal quando uma VM tem:

* Várias interfaces de rede
* Vários endereços IP
* Ambos

As VMs configuradas com o DNS gerenciado pelo Azure resolvem os nomes de host na mesma rede virtual. Use um servidor DNS personalizado para resolver nomes de host de VMs em redes virtuais conectadas.

## <a name="internal-load-balancers-ilb--application-gateways"></a>Balanceadores de carga internos (ILB) e gateways de aplicativo

Você pode atribuir um endereço IP privado à configuração de **front-end** de um:

* [Balanceador de carga interno do Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB)
* [Gateway de Aplicativo do Azure](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

Esse endereço IP privado serve como um ponto de extremidade interno. O ponto de extremidade interno é acessível somente para os recursos em sua rede virtual e as redes remotas conectadas a ele. Um IP dinâmico ou estático pode ser atribuído.

## <a name="at-a-glance"></a>Imediato
A tabela a seguir mostra a propriedade por meio da qual um IP privado pode ser associado a um recurso. 

Os métodos de alocação possíveis que podem ser usados também são exibidos:

* Dinâmico
* Estático

| Recurso de nível superior | Associação de Endereço IP | Dinâmico | Estático |
| --- | --- | --- | --- |
| Máquina virtual |Adaptador de rede |Sim |Sim |
| Balanceador de carga |Configuração de front-end |Sim |Sim |
| Gateway de Aplicativo |Configuração de front-end |Sim |Sim |

## <a name="limits"></a>Limites
Os limites no endereçamento IP são encontrados no conjunto completo de [limites de rede](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) no Azure. Os limites são por região e por assinatura. [Entre em contato](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o suporte para aumentar os limites padrão até os limites máximos com base em suas necessidades de negócios.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o [endereços IP públicos no Azure](public-ip-addresses.md)
* [Implantar uma VM com um endereço IP privado estático](virtual-networks-static-private-ip-arm-pportal.md)