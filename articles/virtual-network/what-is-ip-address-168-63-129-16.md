---
title: O que é o endereço IP 168.63.129.16? | Microsoft Docs
description: Saiba mais sobre o endereço IP 168.63.129.16, especificamente que ele é usado para facilitar um canal de comunicação para os recursos da plataforma Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 1e304bc30a48c92fdff576723dae6af1e26ef3da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222574"
---
# <a name="what-is-ip-address-1686312916"></a>O que é o endereço IP 168.63.129.16?

O endereço IP 168.63.129.16 é um endereço IP público virtual usado para facilitar um canal de comunicação com os recursos da plataforma do Azure. Os clientes podem definir qualquer espaço de endereço para sua rede virtual privada no Azure. Portanto, os recursos da plataforma do Azure devem ser apresentados como um endereço IP público exclusivo. Esse endereço IP público virtual facilita o seguinte:

- Habilita o agente de VM a se comunicar com a plataforma do Azure para sinalizar que ele está em um estado "Pronto".
- Habilita a comunicação com o servidor virtual do DNS para fornecer a resolução de nome filtrada para os recursos (como VM) que não têm um servidor DNS personalizado. Essa filtragem garante que os clientes possam resolver os nomes de host de seus recursos.
- Habilita [investigações de integridade do Azure Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) para determinar o estado de integridade das VMs.
- Permite que a VM obtenha um endereço IP dinâmico do serviço DHCP no Azure.
- Habilita mensagens de pulsação de Agente Convidado para a função de PaaS.

> [!NOTE]
> Em um cenário de rede não virtual (clássico), um endereço IP privado é usado em vez de 168.63.129.16. Esse endereço IP privado é descoberto dinamicamente por meio do DHCP. Regras de firewall específicas para 168.63.129.16 precisam ser ajustadas conforme apropriado.

## <a name="scope-of-ip-address-1686312916"></a>Escopo de endereço IP 168.63.129.16

O endereço IP público 168.63.129.16 é usado em todas as regiões e em todas as nuvens nacionais. Esse endereço IP público especial pertence à Microsoft e não será alterado. Recomendamos que você permita esse endereço IP em qualquer política de firewall local (na VM) (direção de saída). A comunicação entre esse endereço IP especial e os recursos é segura porque a plataforma interna do Azure pode originar uma mensagem desse endereço IP. Se esse endereço for bloqueado, um comportamento inesperado poderá ocorrer em uma variedade de cenários. 168.63.129.16 é um [IP virtual do nó de host](./network-security-groups-overview.md#azure-platform-considerations) e, como tal, não está sujeito a rotas definidas pelo usuário.

- O agente de VM requer comunicação de saída nas portas 80/TCP e 32526/TCP com WireServer (168.63.129.16). Eles devem ser abertos no firewall local na VM. A comunicação nessas portas com o 168.63.129.16 não está sujeita aos grupos de segurança de rede configurados.

- o 168.63.129.16 pode fornecer serviços DNS para a VM. Se isso não for desejado, o tráfego de saída para as portas 168.63.129.16 53/UDP e 53/TCP poderá ser bloqueado no firewall local na VM.

  Por padrão, a comunicação DNS não está sujeita aos grupos de segurança de rede configurados, a menos que especificamente direcionado para a marca de serviço [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) . Para bloquear o tráfego DNS para o DNS do Azure por meio do NSG, crie uma regra de saída para negar o tráfego para [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags)e especifique "*" como "intervalos de porta de destino" e "qualquer" como protocolo.

- Quando a VM faz parte de um pool de back-end do balanceador de carga, a comunicação de [investigação de integridade](../load-balancer/load-balancer-custom-probe-overview.md) deve ter permissão para ser originada em 168.63.129.16. A configuração padrão do grupo de segurança de rede tem uma regra que permite essa comunicação. Essa regra aproveita a marca de serviço [AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags) . Se desejar, esse tráfego pode ser bloqueado Configurando o grupo de segurança de rede, no entanto, isso resultará em investigações que falham.

## <a name="next-steps"></a>Próximas etapas

- [Grupos de segurança](./network-security-groups-overview.md)
- [Criar, alterar ou excluir um Grupo de Segurança de Rede](manage-network-security-group.md)