---
title: Visão geral dos grupos de segurança de aplicativo do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre o uso de grupos de segurança de aplicativo.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 4d8ffe8451b2b2a08ab30761eaf3a928b5e117b3
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537621"
---
# <a name="application-security-groups"></a>Grupos de segurança do aplicativo

Os grupos de segurança de aplicativo permitem a você configurar a segurança de rede como uma extensão natural da estrutura de um aplicativo, permitindo o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos. Você pode reutilizar sua política de segurança em escala sem precisar manter endereços IP explícitos manualmente. A plataforma lida com a complexidade de endereços IP explícitos e vários conjuntos de regras, permitindo que você se concentre na sua lógica de negócios. Para entender melhor os grupos de segurança de aplicativo, considere o exemplo abaixo:

![Grupos de segurança do aplicativo](./media/security-groups/application-security-groups.png)

Na figura anterior, *NIC1* e *NIC2* são membros do grupo de segurança de aplicativo *AsgWeb*. *NIC3* é um membro do grupo de segurança de aplicativo *AsgLogic*. *NIC4* é um membro do grupo de segurança de aplicativo *AsgDb*. Embora cada interface de rede neste exemplo seja membro de apenas um grupo de segurança de rede, uma interface de rede pode ser membro de vários grupos de segurança de aplicativos, até os [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Nenhum dos adaptadores de rede tem um grupo de segurança de rede associado. *NSG1* está associado a ambas as sub-redes e contém as seguintes regras:

## <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Essa regra é necessária para permitir o tráfego da Internet para os servidores Web. Como o tráfego de entrada da Internet é negado pela regra de segurança padrão **DenyAllInbound**, nenhuma regra adicional é necessária para os grupos de segurança do aplicativo *AsgLogic* ou *AsgDb*.

|Prioridade|Fonte|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Allow |

## <a name="deny-database-all"></a>Deny-Database-All

Já que a regra de segurança padrão **AllowVNetInBound** permite toda a comunicação entre recursos na mesma rede virtual, essa regra é necessária para negar o tráfego de todos os recursos.

|Prioridade|Fonte|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Qualquer | Negar |

## <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Essa regra permite o tráfego do grupo de segurança de aplicativo *AsgLogic* para o grupo de segurança de aplicativo *AsgDb*. A prioridade para essa regra é mais alta do que a prioridade para a regra *Deny-Database-All*. Como resultado, essa regra é processada antes da regra *Deny-Database-All* e, portanto, o tráfego do grupo de segurança de aplicativo *AsgLogic* é permitido enquanto todos os outros tráfegos são bloqueados.

|Prioridade|Fonte|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Allow |

As regras que especificam um grupo de segurança de aplicativo como origem ou destino são aplicadas apenas aos adaptadores de rede que são membros do grupo de segurança de aplicativo. Se o adaptador de rede não for membro de um grupo de segurança de aplicativo, a regra não será aplicada ao adaptador de rede, mesmo que o grupo de segurança de rede esteja associado à sub-rede.

Os grupos de segurança do aplicativo têm as seguintes restrições:

-    Há limites ao número de grupos de segurança de aplicativo que você pode ter em uma assinatura, bem como outros limites relacionados aos grupos de segurança de aplicativo. Para obter detalhes, confira [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Você pode especificar um grupo de segurança do aplicativo como a origem e o destino em uma regra de segurança. Você não pode especificar vários grupos de segurança de aplicativos na origem ou no destino.
- Todas as interfaces de rede atribuídas a um grupo de segurança do aplicativo precisam existir na mesma rede virtual que a primeira interface de rede atribuída ao grupo de segurança que o aplicativo está. Por exemplo, se o primeiro adaptador de rede atribuído a um grupo de segurança de aplicativo chamado *AsgWeb* estiver na rede virtual denominada *VNet1*, todos os adaptadores de rede subsequentes atribuídos a *ASGWeb* deverão existir na *VNet1*. Você não pode adicionar interfaces de rede de redes virtuais diferentes ao mesmo grupo de segurança do aplicativo.
- Se você especificar um grupo de segurança do aplicativo como a origem e o destino em uma regra de segurança, as interfaces de rede em ambos os grupos de segurança do aplicativo deverão existir na mesma rede virtual. Por exemplo, se *AsgLogic* contiver adaptadores de rede da *VNet1* e *AsgDb* contiver adaptadores de rede da *VNet2*, você não poderá atribuir *AsgLogic* como a origem e o *AsgDb* como o destino em uma regra. Todos os adaptadores de rede dos grupos de segurança de aplicativo de origem e de destino precisam existir na mesma rede virtual.

> [!TIP]
> Para minimizar o número de regras de segurança necessárias e a necessidade de alterar as regras, planeje os grupos de segurança do aplicativo que serão necessários e crie regras usando marcas de serviço ou grupos de segurança de aplicativo em vez de endereços IP individuais ou intervalos de endereços IP, sempre que possível.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar um grupo de segurança de rede](tutorial-filter-network-traffic.md).
