---
title: 'Cenário: isolando VNets'
titleSuffix: Azure Virtual WAN
description: Cenários para VNets de isolamento de roteamento
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: e5e2ce17be6d8a1fa82d8a92b9b788f0bd2a37b8
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424748"
---
# <a name="scenario-isolating-vnets"></a>Cenário: isolando VNets

Ao trabalhar com o roteamento de Hub virtual de WAN virtual, há alguns cenários disponíveis. Nesse cenário, o objetivo é impedir que o VNets seja capaz de acessar outros. Isso é conhecido como isolar VNets. Para obter informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

Nesse cenário, a carga de trabalho em uma determinada VNet permanece isolada e não é capaz de se comunicar com outras VNets. No entanto, os VNets são necessários para alcançar todos os branches (VPN, ER e VPN de usuário). Para descobrir quantas tabelas de rotas serão necessárias, você pode criar uma matriz de conectividade. Para esse cenário, ele se parecerá com a tabela a seguir, em que cada célula representa se uma origem (linha) pode se comunicar com um destino (coluna):

| De |   Para |  *VNets* | *Branches* |
| -------------- | -------- | ---------- | ---|
| VNets     | &#8594;| Direto |   Direto    |
| Branches   | &#8594;|  Direto  |   Direto    |

Cada uma das células na tabela anterior descreve se uma conexão de WAN virtual (o lado "de" do fluxo, os cabeçalhos de linha) se comunica com um prefixo de destino (o lado "para" do fluxo, os cabeçalhos de coluna em itálico). Nesse cenário, não há firewalls ou soluções de virtualização de rede, portanto, as comunicações fluem diretamente sobre a WAN virtual (portanto, a palavra "direta" na tabela).

Essa matriz de conectividade nos oferece dois padrões de linha diferentes, que são convertidos em duas tabelas de rotas. A WAN virtual já tem uma tabela de rotas padrão, portanto, precisaremos de outra tabela de rotas. Para este exemplo, nomearemos a tabela de rotas **RT_VNET**.

VNets será associado a esta **RT_VNET** tabela de rotas. Como eles precisam de conectividade com ramificações, as ramificações precisarão ser propagadas para **RT_VNET** (caso contrário, o VNets não aprenderia os prefixos de ramificação). Como as ramificações estão sempre associadas à tabela de rotas padrão, o VNets precisará se propagar para a tabela de rotas padrão. Como resultado, este é o design final:

* Redes virtuais:
  * Tabela de rotas associada: **RT_VNET**
  * Propagando para tabelas de rotas: **padrão**
* Filia
  * Tabela de rotas associada: **padrão**
  * Propagando para tabelas de rotas: **RT_VNET** e **padrão**

Observe que, como apenas branches se propagam para a tabela de rotas **RT_VNET**, esses serão os únicos prefixos que VNets aprenderão e não os de outros VNets.

Para obter informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

Para configurar esse cenário, leve em consideração as seguintes etapas:

1. Crie uma tabela de rotas personalizada em cada Hub. No exemplo, a tabela de rotas é **RT_VNet**. Para criar uma tabela de rotas, consulte [como configurar o roteamento de Hub virtual](how-to-virtual-hub-routing.md). Para obter mais informações sobre tabelas de rotas, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
2. Ao criar a tabela de rotas **RT_VNet** , defina as seguintes configurações:

   * **Associação**: selecione o VNets que você deseja isolar.
   * **Propagação**: selecione a opção para branches, que implicam conexões de ramificação (VPN/er/P2S) propagarão rotas para esta tabela de rotas.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="VNets isolado":::

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
