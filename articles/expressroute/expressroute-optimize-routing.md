---
title: 'Otimizar o roteamento – Circuitos do ExpressRoute: Azure | Microsoft Docs'
description: Esta página fornece detalhes sobre como otimizar o roteamento quando houver mais de um circuito do ExpressRoute que se conecta entre a Microsoft e a rede corporativa.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 0bd8c0417b32e93a4f52b545c4d7fc532992a0b1
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854313"
---
# <a name="optimize-expressroute-routing"></a>Otimizar o roteamento do ExpressRoute
Quando você tem vários circuitos de ExpressRoute, tem mais de um caminho para se conectar à Microsoft. Portanto, pode ocorrer um roteamento abaixo do ideal, ou seja, o tráfego pode levar um caminho mais longo para acessar a Microsoft e esta para acessar a sua rede. Quanto maior o caminho de rede, maior será a latência. A latência tem impacto direto na experiência de usuário e no desempenho do aplicativo. Este artigo ilustra esse problema e explica como otimizar o roteamento usando tecnologias de roteamento padrão.

## <a name="path-selection-on-microsoft-and-public-peerings"></a>Seleção de caminho em emparelhamentos públicos e da Microsoft
É importante garantir que, ao utilizar o emparelhamento da Microsoft ou público, o tráfego flua sobre o caminho desejado se você tiver um ou mais circuitos do ExpressRoute, bem como caminhos para a Internet por meio de um Internet Exchange (IX) ou provedor de serviços de Internet (ISP). O BGP utiliza um melhor algoritmo de seleção de caminho com base em vários fatores, incluindo o LPM (correspondência de prefixo mais longo). Para garantir que o tráfego destinado ao Azure por meio da Microsoft ou do emparelhamento público Percorra o caminho do ExpressRoute, os clientes devem implementar o atributo de *preferência local* para garantir que o caminho seja sempre preferido no ExpressRoute. 

> [!NOTE]
> A preferência local padrão é normalmente 100. Preferências de local mais altas são mais preferenciais. 
>
>

Considere o seguinte cenário de exemplo:

![Problema de ExpressRoute, Caso 1 - qualidade inferior de roteamento do cliente para a Microsoft](./media/expressroute-optimize-routing/expressroute-localPreference.png)

No exemplo acima, para preferir caminhos do ExpressRoute, configure a preferência local da seguinte maneira. 

**Configuração do Cisco IOS-XE da perspectiva de R1:**

    R1(config)#route-map prefer-ExR permit 10
    R1(config-route-map)#set local-preference 150

    R1(config)#router BGP 345
    R1(config-router)#neighbor 1.1.1.2 remote-as 12076
    R1(config-router)#neighbor 1.1.1.2 activate
    R1(config-router)#neighbor 1.1.1.2 route-map prefer-ExR in

**Configuração do Junos da perspectiva do R1:**

    user@R1# set protocols bgp group ibgp type internal
    user@R1# set protocols bgp group ibgp local-preference 150



## <a name="suboptimal-routing-from-customer-to-microsoft"></a>Roteamento abaixo do ideal do cliente para a Microsoft
Vamos examinar o problema de roteamento usando um exemplo. Imagine que você tem dois escritórios nos EUA, um em Los Angeles e um em Nova York. Seus escritórios estão conectados em uma WAN (rede de longa distância), que pode ser sua própria rede backbone ou VPN de IP do provedor de serviços. Você tem dois circuitos de ExpressRoute, um no Oeste dos EUA e outro no Leste dos EUA, que também estão conectados à WAN. Obviamente, você tem dois caminhos para se conectar à rede da Microsoft. Agora imagine que você tem a implantação do Azure (por exemplo, o Serviço de Aplicativo do Azure) no Oeste dos EUA e no Leste dos EUA. Sua intenção é conectar seus usuários em Los Angeles ao Azure no Oeste dos EUA e seus usuários em Nova York ao Azure no Leste dos EUA porque seu administrador de serviço anuncia que os usuários em cada escritório acessarão os serviços do Azure mais próximos para uma melhor experiência. Infelizmente, o plano funciona bem para os usuários da Costa Leste, mas não para os usuários da Costa Oeste. A causa do problema é a seguinte: Em cada circuito do ExpressRoute, anunciamos a você tanto o prefixo do Azure no Leste dos EUA (23.100.0.0/16) quanto o prefixo do Azure no Oeste dos EUA (13.100.0.0/16). Se você não souber qual prefixo é de que região, não conseguirá tratá-los de maneira diferente. Sua rede WAN pode pensar que ambos os prefixos são mais próximos do Leste dos EUA que do Oeste dos EUA e, portanto, rotear os usuários de ambos os escritórios para o circuito de ExpressRoute no Leste dos EUA. No final, você terá muitos usuários insatisfeitos no escritório de Los Angeles.

![Problema de ExpressRoute, Caso 1 - qualidade inferior de roteamento do cliente para a Microsoft](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>Solução: usar comunidades BGP
Para otimizar o roteamento nos dois usuários, você precisa saber qual é o prefixo do Azure no Oeste dos EUA e no Leste dos EUA. Nós codificamos essas informações usando [Valores da Comunidade BGP](expressroute-routing.md). Atribuímos um valor de Comunidade BGP exclusivo para cada região do Azure, por exemplo, "12076:51004" para o Leste dos EUA, "12076:51006" para o Oeste dos EUA. Agora que você sabe que prefixo é de que região do Azure, pode configurar qual circuito de ExpressRoute deve ser preferencial. Como usamos o BGP para trocar informações de roteamento, você pode usar a preferência de local do BGP para influenciar o roteamento. Em nosso exemplo, você pode atribuir um valor maior de preferência de local a 13.100.0.0/16 no Oeste dos EUA que no Leste dos EUA e, da mesma forma, um valor mais alto de preferência de local a 23.100.0.0/16 no Leste dos EUA que no Oeste dos EUA. Essa configuração fará com que, quando ambos os caminhos para a Microsoft estiverem disponíveis, os usuários em Los Angeles usarão o circuito de ExpressRoute no Oeste dos EUA para se conectar ao Azure no Oeste dos EUA e os usuários em Nova York usarão o ExpressRoute no Leste dos EUA para se conectar ao Azure no Leste dos EUA. O roteamento é otimizado em ambos os lados. 

![Solução de ExpressRoute, Caso 1 - usar Comunidades BGP](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

> [!NOTE]
> A mesma técnica, usando a Preferência Local, pode ser aplicada ao roteamento de cliente para Rede Virtual do Azure. Não marcamos o valor da Comunidade BGP para os prefixos anunciados do Azure para a rede. No entanto, como você sabe qual implantação da Rede Virtual está próxima de qual escritório, pode configurar os roteadores adequadamente para preferir um circuito do ExpressRoute em vez do outro.
>
>

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>Roteamento abaixo do ideal da Microsoft para o cliente
Aqui está outro exemplo em que conexões da Microsoft usam um caminho mais longo para acessar a sua rede. Nesse caso, você usa servidores Exchange locais e o Exchange Online em um [ambiente híbrido](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx). Seus escritórios estão conectados a uma WAN. Você anuncia os prefixos dos seus servidores locais nos dois escritórios à Microsoft por meio de dois circuitos de ExpressRoute. O Exchange Online irá iniciar conexões com os servidores locais em situações como migração de caixa de correio. Infelizmente, a conexão com o escritório de Los Angeles é roteada para o circuito do ExpressRoute no Leste dos EUA antes de percorrer o continente inteiro de volta à Costa Oeste. A causa do problema é semelhante à do primeiro caso. Sem qualquer dica, a rede da Microsoft não pode determinar qual prefixo do cliente está próximo ao Leste dos EUA e qual está próximo ao Oeste dos EUA. Ela acaba escolhendo o caminho errado para seu escritório em Los Angeles.

![ExpressRoute, Caso 2 - qualidade inferior de roteamento da Microsoft para o cliente](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>Solução: usar precedência de caminho AS
Há duas soluções para o problema. A primeira é simplesmente anunciar o prefixo local para o escritório de Los Angeles, 177.2.0.0/31 no circuito de ExpressRoute no Oeste dos EUA e o prefixo local para o escritório de Nova York, 177.2.0.2/31, no circuito de ExpressRoute no Leste dos EUA. Como resultado, sobra apenas um caminho para a Microsoft se conectar a cada um dos seus escritórios. Não há nenhuma ambiguidade e o roteamento é otimizado. Com esse design, você precisa pensar sobre a estratégia de failover. Se o caminho para a Microsoft pelo ExpressRoute for interrompido, você precisa fazer com que o Exchange Online ainda possa se conectar aos servidores locais. 

A segunda solução é continuar a anunciar ambos os prefixos em ambos os circuitos do ExpressRoute e, além disso, fornecer uma dica de qual prefixo é mais próximo de qual escritório. Como damos suporte a precedência de caminho AS do BGP, você pode configurar do caminho AS para o prefixo a fim de influenciar o roteamento. Neste exemplo, você pode aumentar o caminho AS de 172.2.0.0/31 no Leste dos EUA para que passemos a preferir circuito do ExpressRoute no Oeste dos EUA para o tráfego destinado a esse prefixo (já que a nossa rede pensará que o caminho para esse prefixo é mais curto nesse lado). Da mesma forma, você pode aumentar o caminho AS de 172.2.0.2/31 no Oeste dos EUA para que vejamos o circuito do ExpressRoute no Leste dos EUA como preferencial. O roteamento é otimizado para ambos os escritórios. Com esse design, se um circuito do ExpressRoute for interrompido, o Exchange Online poderá ainda acessá-lo por meio de outro circuito do ExpressRoute e sua WAN. 

> [!IMPORTANT]
> Removemos números AS particulares no caminho AS para os prefixos recebidos no Microsoft Peering. Você precisa anexar números AS públicos como números no caminho AS para influenciar o roteamento para o Microsoft Peering.
> 
> 

![Solução de ExpressRoute, caso 2 - usar precedência de AS PATH](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!NOTE]
> Embora os exemplos fornecidos aqui sejam para a Microsoft e os emparelhamentos Públicos, oferecemos suporte para os mesmos recursos do emparelhamento Privado. Além disso, o prefixo AS Path funciona em um único circuito do ExpressRoute para influenciar a seleção dos caminhos primário e secundário.
> 
> 

## <a name="suboptimal-routing-between-virtual-networks"></a>Qualidade inferior de roteamento entre redes virtuais
Com o ExpressRoute, você pode habilitar a comunicação de Rede Virtual a Rede Virtual (que também é conhecida como "VNet") vinculando-as a um circuito do ExpressRoute. Quando você os vincula a vários circuitos do ExpressRoute, o roteamento abaixo do ideal pode ocorrer entre as redes virtuais. Vamos considerar um exemplo. Você tem dois circuitos de ExpressRoute, um no Oeste dos EUA e outro no Leste dos EUA. Em cada região, você tem duas VNets. Os servidores Web são implantados em uma VNet e os servidores de aplicativos na outra. Para redundância, você pode vincular as duas redes virtuais em cada região ao circuito do ExpressRoute local e ao circuito do ExpressRoute remoto. Como pode ser visto abaixo, de cada VNet, há dois caminhos para a outra VNet. As VNets não sabem qual circuito do ExpressRoute é local e qual é remoto. Consequentemente, como realizam o roteamento ECMP (Equal-Cost-Multi-Path) para fazer o balanceamento de carga de tráfego entre VNets, alguns fluxos de tráfego usarão o caminho mais longo e são roteados no circuito do ExpressRoute remoto.

![ExpressRoute, Caso 3 - qualidade inferior de roteamento entre redes virtuais](./media/expressroute-optimize-routing/expressroute-case3-problem.png)

### <a name="solution-assign-a-high-weight-to-local-connection"></a>Solução: atribuir um peso alto à conexão local
A solução é simples. Como você sabe onde as redes virtuais e os circuitos estão, pode indicar qual caminho cada VNet deve preferir. Especificamente para esse exemplo, você atribui um peso mais alto à conexão local do que à conexão remota (consulte o exemplo de configuração [aqui](expressroute-howto-linkvnet-arm.md#modify-a-virtual-network-connection)). Quando uma VNet receber o prefixo da outra VNet em várias conexões, ela preferirá a conexão com o peso mais alto para enviar o tráfego destinado a esse prefixo.

![Solução de ExpressRoute, Caso 3 - atribuir peso alto à conexão local](./media/expressroute-optimize-routing/expressroute-case3-solution.png)

> [!NOTE]
> Você também poderá influenciar o roteamento da VNet à rede local se tiver vários circuitos do ExpressRoute, configurando o peso de uma conexão em vez de aplicar AS PATH para prefixação, uma técnica descrita no segundo cenário acima. Para cada prefixo, sempre veremos o peso de conexão antes do comprimento do Caminho AS ao decidir como enviar o tráfego.
>
>
