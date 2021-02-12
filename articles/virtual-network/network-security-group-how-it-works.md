---
title: Grupo de segurança de rede-como funciona
titlesuffix: Azure Virtual Network
description: Saiba como os grupos de segurança de rede ajudam a filtrar o tráfego de rede entre os recursos do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 9510c4b0940a0a03ae9a232c3329817468aaf358
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537891"
---
# <a name="how-network-security-groups-filter-network-traffic"></a>Como os grupos de segurança de rede filtram o tráfego de rede
<a name="network-security-groups"></a>

Você pode usar um grupo de segurança de rede do Azure para filtrar o tráfego de rede de e para recursos do Azure em uma rede virtual do Azure. Um grupo de segurança de rede contém [regras de segurança](./network-security-groups-overview.md#security-rules) que permitem ou negam o tráfego de rede de entrada ou de saída em relação a vários tipos de recursos do Azure. Para cada regra, você pode especificar origem e destino, porta e protocolo.

Você pode implantar recursos de vários serviços do Azure em uma rede virtual do Azure. Para obter uma lista completa, consulte [Serviços que podem ser implantados em uma rede virtual](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Você pode associar um, ou nenhum, grupo de segurança de rede a cada [sub-rede](virtual-network-manage-subnet.md#change-subnet-settings) e [adaptador de rede](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) de uma rede virtual em uma máquina virtual. O mesmo grupo de segurança de rede pode ser associado ao número de sub-redes e adaptadores de rede desejado.

A imagem abaixo ilustra os diferentes cenários de como os grupos de segurança de rede podem ser implantados para permitir o tráfego de rede da Internet pela porta TCP 80:

![Processamento de NSG](./media/network-security-group-how-it-works/network-security-group-interaction.png)

Consulte a imagem anterior com o texto a seguir para entender como o Azure processa as regras de entrada e saída para os grupos de segurança de rede:

## <a name="inbound-traffic"></a>Tráfego de entrada

Em relação ao tráfego de entrada, o Azure processa as regras em um grupo de segurança de rede associado a uma sub-rede em primeiro lugar, se houver uma, e, em seguida, as regras em um grupo de segurança de rede associado ao adaptador de rede, se houver um.

- **VM1**: as regras de segurança no *NSG1* são processadas, já que estão associadas à *Subnet1* e a *VM1* está na *Subnet1*. A menos que você tenha criado uma regra que permite a entrada pela porta 80, o tráfego será negado pela regra de segurança padrão [DenyAllInbound](./network-security-groups-overview.md#denyallinbound) e nunca será avaliado pelo *NSG2*, já que o *NSG2* está associado ao adaptador de rede. Se *NSG1* tem uma regra de segurança que permite a porta 80, o tráfego é processado por *NSG2*. Para permitir a porta 80 para a máquina virtual, tanto *NSG1* quanto *NSG2* devem ter uma regra que permite a porta 80 da Internet.
- **VM2**: as regras no *NSG1* são processadas porque a *VM2* também está na *Subnet1*. Já que a *VM2* têm um grupo de segurança de rede associado a seu adaptador de rede, ela recebe todo o tráfego permitido pelo *NSG1* ou tem todo o tráfego negado por *NSG1* também negado. O tráfego é permitido ou negado para todos os recursos na mesma sub-rede quando um grupo de segurança de rede está associado a uma sub-rede.
- **VM3**: já que não há nenhum grupo de segurança de rede associado à *Subnet2*, o tráfego é permitido para a sub-rede e processado pelo *NSG2*, pois o *NSG2* está associado ao adaptador de rede anexado à *VM3*.
- **VM4**: o tráfego é permitido para a *VM4* porque um grupo de segurança de rede não está associado à *Subnet3* ou ao adaptador de rede na máquina virtual. Todo o tráfego será permitido por meio de um adaptador de rede e sub-rede se não tiver um grupo de segurança de rede associado a eles.

## <a name="outbound-traffic"></a>Tráfego de saída

Em relação ao tráfego de saída, o Azure processa as regras em um grupo de segurança de rede associado a um adaptador de rede em primeiro lugar, se houver um, e, em seguida, as regras em um grupo de segurança de rede associado à sub-rede, se houver uma.

- **VM1**: as regras de segurança no *NSG2* são processadas. A menos que você crie uma regra de segurança que nega a porta 80 de saída para a Internet, o tráfego será permitido pela regra de segurança padrão [AllowInternetOutbound](./network-security-groups-overview.md#allowinternetoutbound) tanto no *NSG1* quanto no *NSG2*. Se *NSG2* tem uma regra de segurança que nega a porta 80, o tráfego é negado e nunca é avaliado pelo *NSG1*. Para negar a porta 80 na máquina virtual, um ou ambos os grupos de segurança da rede devem ter uma regra que nega a porta 80 para a Internet.
- **VM2**: todo o tráfego é enviado por meio do adaptador de rede na sub-rede, uma vez que o adaptador de rede conectado à *VM2* não tem um grupo de segurança de rede associado a ele. As regras no *NSG1* são processadas.
- **VM3**: se *NSG2* tem uma regra de segurança que nega a porta 80, o tráfego é negado. Se *NSG2* tem uma regra de segurança que permite a porta 80, está tem permissão de saída para a Internet, uma vez que um grupo de segurança de rede não está associado à *Subnet2*.
- **VM4**: todo o tráfego é permitido da *VM4*, pois um grupo de segurança de rede não está associado ao adaptador de rede anexado à máquina virtual, ou para a *Subnet3*.


## <a name="intra-subnet-traffic"></a>Tráfego de Intra-Subnet

É importante observar que as regras de segurança em um NSG associado a uma sub-rede podem afetar a conectividade entre as VMs dentro dela. Por exemplo, se uma regra for adicionada a *NSG1* que nega todo o tráfego de entrada e de saída, *VM1* e *VM2* não poderão se comunicar entre si. Outra regra teria que ser adicionada especificamente para permitir isso. 

É possível exibir facilmente as regras de agregação aplicadas a um adaptador de rede exibindo as [regras de segurança efetiva](virtual-network-network-interface.md#view-effective-security-rules) para determinado adaptador de rede. Você também pode usar o recurso [Verificar o fluxo de IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) no Observador de Rede do Azure para determinar se a comunicação é permitida na entrada ou saída de um adaptador de rede. A verificação de fluxo de IP informa se uma comunicação é permitida ou negada e qual regra de segurança de rede permite ou nega o tráfego.

> [!NOTE]
> Os grupos de segurança de rede são associados a sub-redes ou a máquinas virtuais e serviços de nuvem implantados no modelo de implantação clássico e a sub-redes ou interfaces de rede no modelo de implantação do Resource Manager. Para saber mais sobre os modelos de implantação do Azure, confira [Entender os modelos de implantação do Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> A menos que você tenha um motivo específico para o, é recomendável associar um grupo de segurança de rede a uma sub-rede ou um adaptador de rede, mas não ambos. Como as regras em um grupo de segurança de rede associado a uma sub-rede podem entrar em conflito com as regras em um grupo de segurança de rede associado a um adaptador de rede, você pode ter problemas de comunicação inesperados que exigem a solução de problemas.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre quais recursos do Azure podem ser implantados em uma rede virtual e ter grupos de segurança de rede associados a eles, consulte [Integração de rede virtual para serviços do Azure](virtual-network-for-azure-services.md).
* Se você nunca criou um grupo de segurança de rede, pode concluir um [tutorial](tutorial-filter-network-traffic.md) rápido para obter alguma experiência.
* Se você estiver familiarizado com os grupos de segurança de rede e a necessidade de gerenciá-los, consulte [Gerenciar um grupo de segurança de rede](manage-network-security-group.md). 
* Se você estiver tendo problemas de comunicação e precisa solucionar problemas de grupos de segurança de rede, consulte [Diagnosticar um problema de filtro de tráfego de rede em máquina virtual](diagnose-network-traffic-filter-problem.md). 
* Saiba como habilitar [os logs de fluxo do grupo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para analisar o tráfego de rede de e para recursos que têm um grupo de segurança de rede associado.