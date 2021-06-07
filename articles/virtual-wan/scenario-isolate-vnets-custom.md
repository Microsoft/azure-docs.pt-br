---
title: 'Cenário: isolamento personalizado para VNets'
titleSuffix: Azure Virtual WAN
description: Cenários para roteamento – impedir que o VNets selecionado seja capaz de alcançar um ao outro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ca1ee8418bc08d70a031d81a15dc1b4ace2f1a3a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92461814"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Cenário: isolamento personalizado para VNets

Ao trabalhar com o roteamento de Hub virtual de WAN virtual, há alguns cenários disponíveis. Em um cenário de isolamento personalizado para VNets, o objetivo é impedir que um conjunto específico de VNets seja capaz de alcançar outro conjunto específico de VNets. No entanto, os VNets são necessários para alcançar todos os branches (VPN/ER/usuário VPN). Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

Para descobrir quantas tabelas de rotas serão necessárias, você pode criar uma matriz de conectividade. Para esse cenário, ele será semelhante ao seguinte, onde cada célula representa se uma origem (linha) pode se comunicar com um destino (coluna):

| De | Para:| *VNets azul* | *VNets vermelho* | *Branches*|
|---|---|---|---|---|
| **VNets azul** |   &#8594;|   Direto     |           |  Direto |
| **VNets vermelho**  |   &#8594;|              |   Direto  |  Direto |
| **Branches**   |   &#8594;|   Direto     |   Direto  |  Direto |

Cada uma das células na tabela anterior descreve se uma conexão de WAN virtual (o lado "de" do fluxo, os cabeçalhos de linha) se comunica com um destino (o lado "para" do fluxo, os cabeçalhos de coluna em itálico). Nesse cenário, não há firewalls ou soluções de virtualização de rede, portanto, as comunicações fluem diretamente sobre a WAN virtual (portanto, a palavra "direta" na tabela).

O número de padrões de linha diferentes será o número de tabelas de rotas que será necessário neste cenário. Nesse caso, três tabelas de rota de rota que chamaremos **RT_BLUE** e **RT_RED** para as redes virtuais e o **padrão** para as ramificações. Lembre-se de que as ramificações sempre precisam ser associadas à tabela de roteamento padrão.

Os branches precisarão aprender os prefixos de VNets vermelho e azul, portanto, todos os VNets precisarão se propagar para o padrão (além de **RT_BLUE** ou **RT_RED**). O VNets azul e o vermelho precisarão aprender os prefixos de ramificações, portanto, as ramificações serão propagadas para ambas as tabelas de rotas **RT_BLUE** e **RT_RED** também. Como resultado, este é o design final:

* Redes virtuais azuis:
  * Tabela de rotas associada: **RT_BLUE**
  * Propagando para tabelas de rotas: **RT_BLUE** e **padrão**
* Redes virtuais vermelhas:
  * Tabela de rotas associada: **RT_RED**
  * Propagando para tabelas de rotas: **RT_RED** e **padrão**
* Filia
  * Tabela de rotas associada: **padrão**
  * Propagando para tabelas de rotas: **RT_BLUE**, **RT_RED** e **padrão**

> [!NOTE]
> Como todas as ramificações precisam ser associadas à tabela de rotas padrão, bem como se propagarem para o mesmo conjunto de tabelas de roteamento, todas as ramificações terão o mesmo perfil de conectividade. Em outras palavras, o conceito vermelho/azul para VNets não pode ser aplicado a branches.

> [!NOTE]
> Se sua WAN virtual for implantada em várias regiões, você precisará criar as **RT_BLUE** e **RT_RED** tabelas de rotas em cada Hub, e as rotas de cada conexão de VNet precisam ser propagadas para as tabelas de rotas em cada Hub virtual usando rótulos de propagação.

Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="architecture"></a>Fluxo de trabalho

Na **Figura 1**, há conexões VNet azuis e vermelhas.

* Os VNets conectados à Blue podem alcançar um ao outro, bem como acessar todas as conexões de branches (VPN/ER/P2S).
* O VNets vermelho pode alcançar um ao outro, bem como acessar todas as conexões de branches (VPN/ER/P2S).

Considere as etapas a seguir ao configurar o roteamento.

1. Crie duas tabelas de rotas personalizadas na portal do Azure, **RT_BLUE** e **RT_RED**.
2. Para a tabela de rotas **RT_BLUE**, para as seguintes configurações:
   * **Associação**: selecione todos os VNets azuis.
   * **Propagação**: para branches, selecione a opção para branches, que implicam conexões de ramificação (VPN/er/P2S) propagarão rotas para esta tabela de rotas.
3. Repita as mesmas etapas para **RT_RED** tabela de rotas para VNets vermelho e branches (VPN/er/P2S).

Isso resultará nas alterações de configuração de roteamento, como mostra a figura abaixo

**Figura 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="Figura 1":::

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
