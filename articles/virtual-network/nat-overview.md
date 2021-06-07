---
title: O que é a NAT de Rede Virtual do Azure?
titlesuffix: Azure Virtual Network
description: Visão geral de recursos, da arquitetura e da implementação da NAT de Rede Virtual. Saiba como funciona a NAT de Rede Virtual e como usar recursos de gateway da NAT na nuvem.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: allensu
ms.openlocfilehash: 75a2bb187b2ed7a234e99d8cd293cb30148bcb1f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91667190"
---
# <a name="what-is-virtual-network-nat"></a>O que é NAT de Rede Virtual?

A NAT de Rede Virtual (conversão de endereços de rede) simplifica a conectividade de Internet somente de saída para redes virtuais. Quando configurado em uma sub-rede, toda a conectividade de saída usa seus endereços IP públicos estáticos especificados.  A conectividade de saída é possível sem endereços IP públicos ou de balanceador de carga conectados diretamente a máquinas virtuais. A NAT é totalmente gerenciada e altamente resiliente.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" alt="Figure depicts a NAT receiving traffic from internal subnets and directing it to a public IP (PIP) and an IP prefix." width="256" title="NAT de Rede Virtual">
</p>



*Figura: NAT de Rede Virtual*

## <a name="static-ip-addresses-for-outbound-only"></a>Endereços IP estáticos para somente saída

A conectividade de saída pode ser definida para cada sub-rede com a NAT.  Várias sub-redes dentro da mesma rede virtual podem ter NATs diferentes. Uma sub-rede é configurada especificando qual recurso de Gateway da NAT deverá ser usado. Todos os fluxos de saída UDP e TCP de qualquer instância de máquina virtual usarão a NAT. 

O NAT é compatível com recursos de endereço IP público ou com recursos de prefixo IP público do SKU Standard ou uma combinação de ambos.  Você pode usar um prefixo IP público diretamente ou distribuir os endereços IP públicos do prefixo entre vários recursos do gateway da NAT. A NAT limpará todo o tráfego para o intervalo de endereços IP do prefixo.  Qualquer filtragem de IP de suas implantações agora é fácil.

Todo o tráfego de saída da sub-rede é processado pela NAT automaticamente sem nenhuma configuração do cliente.  Rotas definidas pelo usuário não são necessárias. O NAT tem precedência sobre outros cenários de saída e substitui o destino de Internet padrão de uma sub-rede.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>SNAT sob demanda com vários endereços IP para escala

A NAT usa a PNAT ou PAT (“conversão de endereços de rede de porta”) e é recomendável para a maioria das cargas de trabalho. Cargas de trabalho dinâmicas ou divergentes podem ser facilmente acomodadas com alocação de fluxo de saída sob demanda. Deve-se evitar o pré-planejamento, a pré-alocação e, em último caso, o excesso de provisionamento extensivos de recursos de saída. Os recursos de porta SNAT são compartilhados e estão disponíveis em todas as sub-redes usando um recurso de gateway NAT específico e são fornecidos quando necessário.

Um endereço IP público anexado ao NAT fornece até 64.000 fluxos simultâneos para UDP e TCP, respectivamente. Você pode começar com um único endereço IP e escalar verticalmente até 16 endereços IP usando endereços IP públicos ou prefixos IP públicos ou ambos.  Um recurso de gateway NAT usará todos os endereços IP associados ao recurso para conexões de saída de todas as sub-redes configuradas com o mesmo recurso de gateway NAT.

A NAT permite que os fluxos sejam criados com base na rede virtual para a Internet. O tráfego de retorno da Internet só é permitido em resposta a um fluxo ativo.

Diferentemente do SNAT de saída do balanceador de carga, a NAT não tem restrições sobre qual IP privado de uma instância de máquina virtual pode realizar conexões de saída.  As configurações de IP primário e secundário podem criar conexão de Internet de saída com NAT.

## <a name="coexistence-of-inbound-and-outbound"></a>Coexistência de entrada e saída

A NAT é compatível com os seguintes recursos de SKU padrão:

- Balanceador de carga
- Endereço IP público
- Prefixo de IP público

Quando usados em conjunto com a NAT, esses recursos fornecem conectividade de Internet de entrada para suas sub-redes. A NAT fornece toda a conectividade da Internet de saída de suas sub-redes.

A NAT e os recursos de SKU Standard compatíveis reconhecem a direção em que o fluxo foi iniciado. Cenários de entrada e saída podem coexistir. Esses cenários receberão as conversões de endereço de rede corretas, pois esses recursos reconhecem a direção do fluxo. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="512" title="Direção do fluxo da NAT de Rede Virtual">
</p>

*Figura: Direção do fluxo da NAT de Rede Virtual*

## <a name="fully-managed-highly-resilient"></a>Totalmente gerenciado, altamente resiliente

A NAT é totalmente expandido desde o início. Não há nenhuma operação de aumento ou expansão necessária.  O Azure gerencia a operação da NAT para você.  A NAT sempre tem vários domínios de falha e pode manter várias falhas sem interrupção do serviço.

## <a name="tcp-reset-for-unrecognized-flows"></a>Redefinição de TCP para fluxos não reconhecidos

O lado privado da NAT envia pacotes de Redefinição de TCP para tentativas de se comunicar em uma conexão TCP que não existe. Um exemplo são conexões cujo tempo limite de ociosidade foi atingido. O próximo pacote recebido retornará uma Redefinição de TCP para o endereço IP privado a fim de sinalizar e forçar o fechamento da conexão.

O lado público da NAT não gera pacotes de Redefinição de TCP nem nenhum outro tráfego.  Somente o tráfego produzido pela rede virtual do cliente é emitido.

## <a name="configurable-tcp-idle-timeout"></a>Tempo limite de ociosidade de TCP configurável

Um tempo limite de ociosidade TCP padrão de 4 minutos é usado e pode ser aumentado para até 120 minutos. Qualquer atividade em um fluxo também pode redefinir o temporizador de ociosidade, incluindo keepalives TCP.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Isolamento regional ou de zona com zonas de disponibilidade

A NAT é regional por padrão. Ao criar cenários de [zonas de disponibilidade](../availability-zones/az-overview.md), a NAT pode ser isolada em uma zona específica (implantação de zona).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet." width="512" title="NAT de Rede Virtual com zonas de disponibilidade">
</p>

*Figura: NAT de Rede Virtual com zonas de disponibilidade*

## <a name="multi-dimensional-metrics-for-observability"></a>Métricas multidimensionais para observabilidade

Você pode monitorar a operação da NAT por meio de métricas multidimensionais expostas no Azure Monitor. Essas métricas podem ser usadas para observar o uso e para solucionar problemas.  Os recursos do gateway da NAT expõem as seguintes métricas:
- Bytes
- Pacotes
- Pacotes descartados
- Total de conexões SNAT
- Transições de estado de conexão SNAT por intervalo.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>Contrato de Nível de Serviço

Em disponibilidade geral, o caminho de dados da NAT tem pelo menos 99,9% de disponibilidade.

## <a name="pricing"></a>Preços

Para obter detalhes de preços, consulte [preços de rede virtual](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="availability"></a>Disponibilidade

O NAT de rede virtual e o recurso de gateway de NAT estão disponíveis em todas as regiões de todas as [regiões](https://azure.microsoft.com/global-infrastructure/regions/)de nuvens do Azure.

## <a name="suggestions"></a>Sugestões

Desejamos saber como podemos aprimorar o serviço. Proponha e vote em quais itens deveríamos criar em seguida, no [UserVoice para NAT](https://aka.ms/natuservoice).

## <a name="limitations"></a>Limitações

* A NAT é compatível com recursos de prefixo de IP público, IP público de SKU e balanceador de carga de nível Standard. Os recursos Básicos, como um balanceador de carga básico, e produtos derivados deles não são compatíveis com a NAT.  Os recursos básicos precisam ser colocados em uma sub-rede não configurada com NAT.
* A família de endereços IPv4 é compatível.  A NAT não interage com a família de endereços IPv6.  A NAT não pode ser implantada em uma sub-rede com o prefixo IPv6.
* A NAT não pode abranger várias redes virtuais.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [recurso de gateway da NAT](./nat-gateway-resource.md).
* [Diga-nos o que criar em seguida para a NAT de Rede Virtual no UserVoice](https://aka.ms/natuservoice).
