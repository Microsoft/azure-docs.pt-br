---
title: 'Cenário: rotear o tráfego por meio de uma solução de virtualização de rede (NVA)'
titleSuffix: Azure Virtual WAN
description: Rotear o tráfego por meio de uma NVA
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 9d4eb90d49e8cc671156833f22a85e7c2b4dd15b
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626653"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Cenário: Rotear o tráfego por meio de uma NVA

Ao trabalhar com o roteamento de Hub virtual de WAN virtual, há alguns cenários disponíveis. Nesse cenário de NVA, o objetivo é rotear o tráfego por meio de um NVA (solução de virtualização de rede) para Branch para VNet e VNet para Branch. Para obter informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

> [!NOTE]
> Se você já tiver uma configuração com rotas anteriores aos novos recursos [como configurar o roteamento de Hub virtual](how-to-virtual-hub-routing.md) ficando disponível, use as etapas nestas versões dos artigos:
>* [portal do Azure artigo](virtual-wan-route-table-nva-portal.md)
>* [Artigo do PowerShell](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>Design

Neste cenário, usaremos a Convenção de nomenclatura:

* "NVA VNets" para redes virtuais em que os usuários implantaram um NVA e se conectaram a outras redes virtuais como spokes (VNet 2 e VNet 4 na **matriz de conectividade**, abaixo).
* "NVA spokes" para redes virtuais conectadas a uma VNet NVA (VNet 5, VNet 6, VNet 7 e VNet 8 na **matriz de conectividade**, abaixo).
* "Não NVA VNets" para redes virtuais conectadas à WAN virtual que não têm um NVA ou outros VNets emparelhadas com elas (VNet 1 e VNet 3 na matriz de **conectividade**, abaixo).
* "Hubs" para hubs de WAN virtual gerenciados pela Microsoft, nos quais NVA VNets estão conectados. NVA spoke VNets não precisa estar conectado a hubs de WAN virtual, somente ao NVA VNets.

A matriz de conectividade a seguir resume os fluxos com suporte neste cenário:

**Matriz de conectividade**

| De             | Para:|   *NVA spokes*|*NVA VNets*|*VNets não NVA*|*Branches*|
|---|---|---|---|---|---|
| **NVA spokes**   | &#8594; | Sobre VNet NVA | Emparelhamento | Sobre VNet NVA | Sobre VNet NVA |
| **NVA VNets**    | &#8594; | Emparelhamento | Direto | Direto | Direto |
| **VNets não NVA**| &#8594; | Sobre VNet NVA | Direto | Direto | Direto |
| **Branches**     | &#8594; | Sobre VNet NVA | Direto | Direto | Direto |

Cada uma das células na matriz de conectividade descreve como uma VNet ou ramificação (o lado "de" do fluxo, os cabeçalhos de linha na tabela) se comunica com uma VNet ou ramificação de destino (o lado "para" do fluxo, os cabeçalhos de coluna em itálico na tabela). "Direto" significa que a conectividade é fornecida nativamente pela WAN virtual, "emparelhamento" significa que a conectividade é fornecida por uma User-Defined rota na VNet, "via VNet NVA" significa que a conectividade atravessa o NVA implantado na VNet NVA. Considere o seguinte:

* Os spokes NVA não são gerenciados pela WAN virtual. Como resultado, os mecanismos com os quais eles se comunicarão com outros VNets ou branches serão mantidos pelo usuário. A conectividade com a VNet NVA é fornecida por um emparelhamento de VNet e uma rota padrão para 0.0.0.0/0 apontando para o NVA como o próximo salto deve abranger a conectividade com a Internet, com outros spokes e para branches
* NVA VNets saberá sobre seus próprios spokes de NVA, mas não sobre os raios de NVA conectados a outros NVA VNets. Por exemplo, na tabela 1, a VNet 2 sabe sobre VNet 5 e VNet 6, mas não sobre outros spokes como VNet 7 e VNet 8. Uma rota estática é necessária para injetar os prefixos dos outros spokes em NVA VNets
* Da mesma forma, branches e VNets não NVA não conhecem nenhum spoke NVA, já que os raios de NVA não estão conectados aos hubs de WAN virtuais. Como resultado, as rotas estáticas também serão necessárias aqui.

Levando em conta que os spokes NVA não são gerenciados pela WAN virtual, todas as outras linhas mostram o mesmo padrão de conectividade. Como resultado, uma única tabela de rotas (a padrão) fará:

* Redes virtuais (VNets não Hub e VNets do Hub do usuário):
  * Tabela de rotas associada: **padrão**
  * Propagando para tabelas de rotas: **padrão**
* Filia
  * Tabela de rotas associada: **padrão**
  * Propagando para tabelas de rotas: **padrão**

No entanto, neste cenário, precisamos pensar em quais rotas estáticas configurar. Cada rota estática terá dois componentes, uma parte no Hub de WAN virtual informando os componentes WAN virtuais que a conexão usará para cada spoke e outro nessa conexão específica apontando para o endereço IP concreto atribuído ao NVA (ou a um balanceador de carga na frente de vários NVAs), como mostra a **Figura 1** :

**Figura 1**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="Figura 1":::

Com isso, as rotas estáticas que precisamos na tabela padrão para enviar o tráfego para os spokes NVA por trás da VNet NVA são as seguintes:

| Descrição | Tabela de rotas | Rota estática              |
| ----------- | ----------- | ------------------------- |
| VNet 2       | Padrão     | 10.2.0.0/16-> eastusconn |
| VNet 4       | Padrão     | 10.4.0.0/16-> weconn     |

Agora, a WAN virtual sabe a qual conexão enviar os pacotes, mas a conexão precisa saber o que fazer ao receber esses pacotes: é aqui que as tabelas de rotas de conexão são usadas. Aqui, usaremos os prefixos mais curtos (/24 em vez de mais longos/16) para garantir que essas rotas tenham preferência sobre as rotas importadas do NVA VNets (VNet 2 e VNet 4):

| Descrição | Conexão | Rota estática            |
| ----------- | ---------- | ----------------------- |
| VNet 5       | eastusconn | 10.2.1.0/24-> 10.2.0.5 |
| VNet 6       | eastusconn | 10.2.2.0/24-> 10.2.0.5 |
| VNet 7       | weconn     | 10.4.1.0/24-> 10.4.0.5 |
| VNet 8       | weconn     | 10.4.2.0/24-> 10.4.0.5 |

Agora NVA VNets, VNets não NVA e branches sabem como alcançar todos os spokes de NVA. Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Arquitetura

Na **Figura 2**, há dois hubs; **Hub1** e **HUB2**.

* **Hub1** e **HUB2** estão diretamente conectados a NVA VNets **vnet 2** e **vnet 4**.

* **Vnet 5** e **vnet 6** são emparelhadas com **vnet 2**.

* **Vnet 7** e **vnet 8** são emparelhadas com **vnet 4**.

* **VNets 5, 6, 7, 8** são spokes indiretos, não conectados diretamente a um hub virtual.

**Figura 2**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Figura 2" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho do cenário

Para configurar o roteamento via NVA, aqui estão as etapas a serem consideradas:

1. Identifique a conexão VNet do NVA spoke. Na **Figura 2**, eles são **conexão vnet 2 (Eastusconn)** e **conexão vnet 4 (weconn)**.

   Certifique-se de que há UDRs configurado:
   * De VNet 5 e VNet 6 para IP NVA VNet 2
   * Da vnet 7 e da VNet 8 para o IP NVA VNet 4 
   
   Você não precisa conectar o VNets 5, 6, 7, 8 aos hubs virtuais diretamente. Verifique se NSGs no VNets 5, 6, 7, 8 permitem o tráfego para Branch (VPN/ER/P2S) ou VNets conectado à sua VNets remota. Por exemplo, VNets 5, 6 deve garantir que o NSGs permita o tráfego para prefixos de endereço local e VNets 7, 8 que estejam conectados ao Hub remoto 2.

A WAN virtual não oferece suporte a um cenário em que o VNets 5, 6 Conecte-se ao Hub virtual e se comunique por meio do IP de VNet 2 NVA; Portanto, a necessidade de conectar VNets 5, 6 a VNet2 e, da mesma forma, a VNet 7, 8 a VNet 4.

2. Adicione uma entrada de rota estática agregada para VNets 2, 5, 6 à tabela de rotas padrão do hub 1.

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Exemplo":::

3. Configure uma rota estática para o VNets 5, 6 na conexão de rede virtual da VNet 2. Para configurar a configuração de roteamento para uma conexão de rede virtual, consulte [Roteamento de Hub virtual](how-to-virtual-hub-routing.md#routing-configuration).

4. Adicione uma entrada de rota estática agregada para VNets 4, 7, 8 à tabela de rotas padrão do hub 1.

5. Repita as etapas 2, 3 e 4 para a tabela de rotas padrão do Hub 2.

Isso resultará nas alterações de configuração de roteamento, conforme mostrado na **Figura 3**, abaixo.

**Figura 3**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Figura 3" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
