---
title: 'Cenário: isolamento personalizado para redes virtuais e branches'
titleSuffix: Azure Virtual WAN
description: Cenários para roteamento-impedir que VNets e branches selecionados sejam capazes de alcançar uns aos outros
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: wellee
ms.openlocfilehash: e8e5a5a1b9325f40fdd51133155a0daffaa55a7b
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99408465"
---
# <a name="scenario-custom-isolation-for-virtual-networks-and-branches"></a>Cenário: isolamento personalizado para redes virtuais e branches

Ao trabalhar com o roteamento de Hub virtual de WAN virtual, há alguns cenários disponíveis. Em um cenário de isolamento personalizado para redes virtuais (VNets) e branches, o objetivo é impedir que um conjunto específico de VNets atinja outro conjunto de VNets. Da mesma forma, as ramificações (VPN/ER/VPN de usuário) só têm permissão para alcançar determinados conjuntos de VNets.

Também apresentamos o requisito adicional que o Firewall do Azure deve inspecionar a ramificação na vnet e a ramificação para o tráfego de vnet, mas **não**  o tráfego de vnet para vnet.  

Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

Para descobrir quantas tabelas de rotas serão necessárias, você pode criar uma matriz de conectividade. Para esse cenário, ele será semelhante ao seguinte, onde cada célula representa se uma origem (linha) pode se comunicar com um destino (coluna):

| De | Para:| *VNets azul* | *VNets vermelho* | *Ramificações vermelhas*| *Ramificações azuis*| 
|---|---|---|---|---|---|
| **VNets azul** |   &#8594;|   Direto     |           |   |  AzFW|
| **VNets vermelho**  |   &#8594;|              |   Direto  |  AzFW  | 
| **Ramificações vermelhas**   |   &#8594;|   |   AzFW  |  Direto | Direto
| **Ramificações azuis**| &#8594;| AzFW  |   |Direto   | Direto

Cada uma das células na tabela anterior descreve se uma conexão de WAN virtual (o lado "de" do fluxo, os cabeçalhos de linha) se comunica com um destino (o lado "para" do fluxo, os cabeçalhos de coluna em itálico). O **Direct** implica que o tráfego flui diretamente por meio da WAN virtual enquanto **AzFW** implica que o tráfego é inspecionado pelo firewall do Azure antes de ser encaminhado para o destino. Uma entrada em branco significa que o fluxo está bloqueado na configuração.

Nesse caso, as duas tabelas de rotas para o VNets são necessárias para atingir o objetivo do isolamento de VNet sem o Firewall do Azure no caminho. Iremos chamar essas tabelas de rotas **RT_BLUE** e **RT_RED**.

Além disso, as ramificações sempre devem estar associadas à tabela de rotas  **padrão** . Para garantir que o tráfego de e para os branches seja inspecionado pelo firewall do Azure, adicionamos rotas estáticas no **padrão**, **RT_RED** e **RT_BLUE** as tabelas de rotas apontam o tráfego para o Firewall do Azure e configuram as regras de rede para permitir o tráfego desejado. Também garantimos que as ramificações **não** se propaguem para **RT_BLUE** e **RT_RED**.

Como resultado, este é o design final:

* Redes virtuais azuis:
  * Tabela de rotas associada: **RT_BLUE**
  * Propagando para tabelas de rotas: **RT_BLUE**
* Redes virtuais vermelhas:
  * Tabela de rotas associada: **RT_RED**
  * Propagando para tabelas de rotas: **RT_RED** 
* Filia
  * Tabela de rotas associada: **padrão**
  * Propagando para tabelas de rotas: **padrão**
* Rotas estáticas:
    * **Tabela de rotas padrão**: espaços de endereço de rede virtual com o próximo salto do firewall do Azure
    * **RT_RED**: 0.0.0.0/0 com o próximo salto do firewall do Azure
    * **RT_BLUE**: 0.0.0.0/0 com o próximo salto do firewall do Azure
* Regras de rede do firewall:
    *  **Prefixo de origem** de regra de permissão: prefixo de endereço de ramificação azul **prefixo de destino**: prefixos de VNet azul 
    * **Permitir**  **prefixo de origem** de regra: prefixo de endereço de ramificação vermelha **prefixo de destino**: prefixos de vnet vermelha

> [!NOTE]
> Como todas as ramificações precisam ser associadas à tabela de rotas padrão, bem como se propagarem para o mesmo conjunto de tabelas de roteamento, todas as ramificações terão o mesmo perfil de conectividade. Em outras palavras, o conceito vermelho/azul para VNets não pode ser aplicado a branches. No entanto, para obter roteamento personalizado para branches, podemos encaminhar o tráfego das ramificações para o Firewall do Azure.

> [!NOTE]
> Por padrão, o Firewall do Azure nega o tráfego em um modelo de confiança zero. Se não houver nenhuma regra de **permissão** explícita que corresponda ao pacote inspecionado, o Firewall do Azure removerá o pacote.

Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).



## <a name="workflow"></a><a name="architecture"></a>Fluxo de trabalho

Na **Figura 1**, há uma VNets azul e vermelha, bem como ramificações que podem acessar VNets azul ou vermelho.

* Os VNets conectados à Blue podem alcançar um ao outro e podem acessar todas as conexões de branches azuis (VPN/ER/P2S). No diagrama, a ramificação azul é o site VPN site a site.
* Os VNets conectados em vermelho podem alcançar um ao outro e podem acessar todas as conexões de branches vermelhos (VPN/ER/P2S). No diagrama, a ramificação vermelha é a de usuários de VPN ponto a site.

Considere as etapas a seguir ao configurar o roteamento.

1. Crie duas tabelas de rotas personalizadas na portal do Azure, **RT_BLUE** e **RT_RED** para personalizar o tráfego para esses VNets.
2. Para **RT_BLUE** de tabela de rotas, aplique as seguintes configurações para garantir que o VNets azul saiba os prefixos de endereço de todos os outros VNets azuis.:
   * **Associação**: selecione todos os VNets azuis.
   * **Propagação**: selecione todos os VNets azuis.
3. Repita as mesmas etapas para **RT_RED** tabela de rotas para vermelho VNets.
4. Provisionar um firewall do Azure na WAN virtual. Para obter mais informações sobre o Firewall do Azure no Hub WAN virtual, consulte [Configurando o Firewall do Azure no Hub Wan virtual](howto-firewall.md).
5. Adicione uma rota estática à tabela de rotas **padrão** do Hub virtual, direcionando todo o tráfego destinado aos espaços de endereço da vnet (azul e vermelho) para o Firewall do Azure. Esta etapa garante que todos os pacotes de seus branches serão enviados ao firewall do Azure para inspeção.
    * Exemplo: **prefixo de destino**: 10.0.0.0/24 **próximo salto**: Firewall do Azure
    >[!NOTE]
    > Essa etapa também pode ser feita por meio do Gerenciador de firewall selecionando a opção "proteger o tráfego privado". Isso adicionará uma rota para todos os endereços IP privados RFC1918 aplicáveis a VNets e branches. Você precisará adicionar manualmente qualquer ramificação ou rede virtual que não esteja em conformidade com o RFC1918. 

6. Adicione uma rota estática para **RT_RED** e **RT_BLUE** direcionando todo o tráfego para o Firewall do Azure. Essa etapa garante que o VNets não seja capaz de acessar ramificações diretamente. Esta etapa não pode ser feita por meio do Gerenciador de firewall porque essas redes virtuais não estão associadas à tabela de rotas padrão.
    * Exemplo: **prefixo de destino**: 0.0.0.0/0 **próximo salto**: Firewall do Azure

    > [!NOTE]
    > O roteamento é executado usando o LPM (correspondência de prefixo mais longo). Como resultado, as rotas estáticas 0.0.0.0/0 **não** serão preferenciais sobre os prefixos exatos que existem em **BLUE_RT** e **RED_RT**. Como resultado, o tráfego intra-vnet não será inspecionado pelo firewall do Azure.

Isso resultará nas alterações de configuração de roteamento, como mostrado na figura abaixo.

**Figura 1** 
 [ ![ Figura 1 ](./media/routing-scenarios/custom-branch-vnet/custom-branch.png)](./media/routing-scenarios/custom-branch-vnet/custom-branch.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
