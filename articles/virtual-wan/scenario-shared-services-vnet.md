---
title: 'Cenário: rota para serviços compartilhados VNets'
titleSuffix: Azure Virtual WAN
description: Cenários para roteamento – configure rotas para acessar uma VNet de serviço compartilhado com uma carga de trabalho que você deseja que cada VNet e Branch acessem.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 03c71664769f1518ba80d36867c71ef35b2ca026
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461457"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Cenário: rota para serviços compartilhados VNets

Ao trabalhar com o roteamento de Hub virtual de WAN virtual, há alguns cenários disponíveis. Nesse cenário, o objetivo é configurar rotas para acessar uma VNet de **serviço compartilhado** com cargas de trabalho que você deseja que cada VNet e Branch (VPN/er/P2S) acessem. Exemplos dessas cargas de trabalho compartilhadas podem incluir máquinas virtuais com serviços como controladores de domínio ou compartilhamentos de arquivos ou serviços do Azure expostos internamente por meio de [pontos de extremidade privados do Azure](../private-link/private-endpoint-overview.md).

Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

Podemos usar uma matriz de conectividade para resumir os requisitos deste cenário:

**Matriz de conectividade**

| De             | Para:   |*VNets isolado*|*VNet compartilhada*|*Branches*|
|---|---|---|---|---|
|**VNets isolado**|&#8594;|        | Direto | Direto |
|**VNets compartilhado**  |&#8594;| Direto | Direto | Direto |
|**Branches**      |&#8594;| Direto | Direto | Direto |

Cada uma das células na tabela anterior descreve se uma conexão de WAN virtual (o lado "de" do fluxo, os cabeçalhos de linha) se comunica com um destino (o lado "para" do fluxo, os cabeçalhos de coluna em itálico). Nesse cenário, não há firewalls nem soluções de virtualização de rede, portanto, os fluxos de comunicação diretamente pela WAN virtual (portanto, a palavra "direta" na tabela).

Da mesma forma que o [cenário de VNet isolado](scenario-isolate-vnets.md), essa matriz de conectividade nos dá dois padrões de linha diferentes, que são convertidos em duas tabelas de rotas (os serviços compartilhados VNets e as ramificações têm os mesmos requisitos de conectividade). A WAN virtual já tem uma tabela de rotas padrão, portanto, precisaremos de outra tabela de rotas personalizada, que chamaremos de **RT_SHARED** neste exemplo.

VNets será associado à tabela de rotas **RT_SHARED** . Como eles precisam de conectividade com ramificações e para o serviço compartilhado VNets, a VNet do serviço compartilhado e ramificações precisarão se propagar para **RT_SHARED** (caso contrário, o VNets não aprenderia a ramificação e os prefixos de VNet compartilhados). Como as ramificações sempre estão associadas à tabela de rotas padrão, e os requisitos de conectividade são os mesmos para VNets de serviços compartilhados, também associaremos o serviço compartilhado VNets à tabela de rotas padrão.

Como resultado, este é o design final:

* Redes virtuais isoladas:
  * Tabela de rotas associada: **RT_SHARED**
  * Propagando para tabelas de rotas: **padrão**
* Redes virtuais de serviços compartilhados:
  * Tabela de rotas associada: **padrão**
  * Propagando para tabelas de rotas: **RT_SHARED** e **padrão**
* Filia
  * Tabela de rotas associada: **padrão**
  * Propagando para tabelas de rotas: **RT_SHARED** e **padrão**

> [!NOTE]
> Se sua WAN virtual for implantada em várias regiões, você precisará criar a tabela de rotas **RT_SHARED** em cada Hub, e as rotas de cada VNet de serviços compartilhados e conexão de ramificação precisarão ser propagadas para as tabelas de rotas em cada Hub virtual usando rótulos de propagação.

Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

Para configurar o cenário, considere as seguintes etapas:

1. Identifique a VNet **dos serviços compartilhados** .
2. Crie uma tabela de rotas personalizada. No exemplo, nos referimos à tabela de rotas como **RT_SHARED**. Para obter as etapas para criar uma tabela de rotas, consulte [como configurar o roteamento de Hub virtual](how-to-virtual-hub-routing.md). Use os seguintes valores como uma diretriz:

   * **Associação**
     * Para **VNets, *exceto* a VNet dos serviços compartilhados**, selecione o VNets a ser isolado. Isso implicará que todos esses VNets (exceto a VNet dos serviços compartilhados) poderão alcançar o destino com base nas rotas de RT_SHARED tabela de rotas.

   * **Propagação**
      * Para **branches**, propagar rotas para esta tabela de rotas, além de outras tabelas de rotas que você já tenha selecionado. Devido a essa etapa, a tabela de rotas RT_SHARED aprenderá as rotas de todas as conexões de ramificação (VPN/ER/VPN de usuário).
      * Para **VNets**, selecione a **VNet dos serviços compartilhados**. Por causa desta etapa, RT_SHARED tabela de rotas aprenderá as rotas da conexão VNet de serviços compartilhados.

Isso fará com que a configuração de roteamento mostrada na figura a seguir:

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="VNet de serviços compartilhados" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
