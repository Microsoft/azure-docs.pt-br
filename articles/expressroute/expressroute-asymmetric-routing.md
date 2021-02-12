---
title: 'Azure ExpressRoute: roteamento assimétrico'
description: Este artigo aborda os problemas que você pode enfrentar com o roteamento assimétrico em uma rede que tem vários links para um destino.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 0713c52c7f07db93d9ae9084062ef2c3b25a9074
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560501"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Roteamento assimétrico com vários caminhos de rede
Este artigo explica como o tráfego de rede pode levar caminhos diferentes quando várias rotas estão disponíveis entre a origem e o destino da rede.

Há dois conceitos que você precisa saber para entender o roteamento assimétrico. O primeiro é o efeito de vários caminhos de rede. A outra é como os dispositivos, como um firewall, mantêm o estado. Esses tipos de dispositivos são chamados de dispositivos com estado. Quando esses dois fatores são combinados, eles podem criar um cenário no qual o tráfego de rede é Descartado pelo dispositivo com estado.  O tráfego é Descartado porque não detectou que o tráfego se originou de si mesmo.

## <a name="multiple-network-paths"></a>Vários caminhos de rede
Quando uma rede corporativa tem apenas um link para a Internet por meio de um provedor de serviços de Internet, todo o tráfego de e para a internet viaja para o mesmo caminho. É comum que as empresas adquiram vários circuitos para criar caminhos redundantes para melhorar o tempo de atividade da rede. Com esse tipo de configuração, é possível que o tráfego saia de um link para a Internet e retorne por meio de um link diferente. Esse cenário é comumente conhecido como roteamento assimétrico. No roteamento assimétrico, o tráfego de rede de retorno usa um caminho diferente do fluxo de saída original.

![Rede com vários caminhos](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Embora o roteamento assimétrico geralmente ocorra ao ir para a Internet. Isso também acontece quando uma combinação de vários caminhos é introduzida. O primeiro exemplo é quando você tem um caminho de Internet e um caminho privado que vão para o mesmo destino. O segundo exemplo é quando você tem vários caminhos privados que também vão para o mesmo destino.

Cada roteador ao longo do caminho entre a origem e o destino calculará o melhor caminho a ser levado para alcançar o destino. O roteador determina o melhor caminho possível com base em dois fatores principais:

* O roteamento entre as redes externas é baseado em um protocolo de roteamento, BGP (Border Gateway Protocol). O BGP obtém os anúncios dos vizinhos e executa-os com várias etapas para determinar o melhor caminho para o destino pretendido. Ele armazena o melhor caminho em sua tabela de roteamento.
* O comprimento de uma máscara da sub-rede associada a uma rota influencia os caminhos de roteamento. Se um roteador receber vários anúncios para o mesmo endereço IP, o roteador selecionará o caminho com a máscara de sub-rede mais longa porque é considerada uma rota mais específica.

## <a name="stateful-devices"></a>Dispositivos com estado
Os roteadores examinam o cabeçalho IP de um pacote para fazer o roteamento. Alguns dispositivos verificam ainda mais dentro do pacote. Normalmente, esses dispositivos examinam a camada 4-protocolo TCP ou UDP ou até mesmo cabeçalhos de camada 7 (camada de aplicativo). Esses tipos de dispositivos são dispositivos de otimização da largura de banda ou dispositivos de segurança. 

O firewall é um exemplo comum de dispositivo com estado. Um firewall permite ou rejeita pacotes para passar pelas interfaces com base em vários critérios. Esses critérios incluem, mas não se limitam a protocolo, porta TCP/UDP e cabeçalhos de URL. Esse nível de inspeção de pacote pode colocar uma carga de processamento pesada no dispositivo. 

Para melhorar o desempenho, o firewall inspeciona o primeiro pacote de um fluxo. Se ele permitir que o pacote passe por suas interfaces, ele manterá as informações de fluxo em sua tabela de estado. Em seguida, qualquer pacote que tenha sido relatado relacionado a esse fluxo é permitido com base na determinação inicial. Um pacote que faz parte de um fluxo existente pode chegar ao firewall do qual ele não foi originado. Como não tem informações de estado anterior sobre o fluxo inicial, o firewall descarta o pacote.

## <a name="asymmetric-routing-with-expressroute"></a>Roteamento assimétrico com o ExpressRoute
Quando você conecta a Microsoft por meio do ExpressRoute do Azure, sua rede muda da seguinte maneira:

* Você tem vários links para a Microsoft. Um link é a conexão com a Internet existente e o outro é por meio de sua conexão do ExpressRoute. Determinado tráfego destinado à Microsoft pode passar pela conexão com a Internet, mas retornar sobre a conexão do ExpressRoute. O mesmo também pode acontecer quando o tráfego passa pelo ExpressRoute, mas retorna pelo caminho da Internet.
* Você recebeu endereços IP mais específicos do circuito do ExpressRoute. Assim, quando o tráfego de sua rede vai para a Microsoft para serviços oferecidos por meio do ExpressRoute, seus roteadores sempre preferem a conexão do ExpressRoute.

Para entender o efeito de como essas duas alterações têm em uma rede, vamos considerar alguns cenários. Por exemplo, você tem um circuito para a Internet e consome todos os serviços da Microsoft por meio da Internet. O tráfego de sua rede para e da Microsoft atravessa o mesmo vínculo de Internet e passa por um firewall. O firewall registra o fluxo quando vê o primeiro pacote. Todos os pacotes que podem ser revistos dessa conversa são permitidos porque o fluxo existe na tabela de estado.

![Roteamento assimétrico com o ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Em seguida, você coloca um circuito de ExpressRoute para consumir serviços oferecidos pela Microsoft por meio do ExpressRoute. Todos os outros serviços da Microsoft são consumidos pela Internet. Você implanta um firewall separado na borda que está conectado à conexão do ExpressRoute. A Microsoft anuncia prefixos mais específicos para sua rede por meio do ExpressRoute para determinados serviços. Sua infraestrutura de roteamento escolhe o ExpressRoute como o caminho preferido desses prefixos. 

Se você não anunciar seus endereços IP públicos para a Microsoft por meio do ExpressRoute. A Microsoft se comunicará com seus endereços IP públicos pela Internet. O tráfego enviado de sua rede para a Microsoft usa a conexão do ExpressRoute, mas o tráfego de retorno da Microsoft usará o caminho da Internet. Quando o firewall na sua borda vê um pacote de resposta para um fluxo que ele não conhece, ele descarta esses pacotes.

Se você optar por anunciar o mesmo pool de NAT (conversão de endereços de rede) para o ExpressRoute e para a Internet. Você verá problemas semelhantes com os clientes em sua rede em endereços IP privados. Solicitações de serviços como Windows Update passarão pela Internet, porque os endereços IP para esses serviços não são anunciados pelo ExpressRoute. No entanto, o tráfego de retorno será retornado por meio do ExpressRoute. Como a Microsoft recebeu um endereço IP com a mesma máscara de sub-rede da Internet e do ExpressRoute, o caminho preferencial é sempre ExpressRoute. Se um firewall ou outro dispositivo com estado na borda da sua rede voltada para a conexão do ExpressRoute não tiver informações anteriores sobre um fluxo, ele descartará esses pacotes.

## <a name="asymmetric-routing-solutions"></a>Soluções do roteamento assimétrico
Você tem duas opções disponíveis para resolver o problema do roteamento assimétrico. A primeira é por meio do roteamento, e a segunda é usar um NAT baseado em origem (SNAT).

### <a name="routing"></a>Roteamento
Verifique se seus endereços IP públicos são anunciados para links de WAN (rede de longa distância) apropriados. Por exemplo, se você quiser usar a Internet para tráfego de autenticação e ExpressRoute para seu tráfego de email. Não Anuncie seus endereços IP públicos de Serviços de Federação do Active Directory (AD FS) (AD FS) por meio do ExpressRoute. Certifique-se também de não expor seu servidor de AD FS local para endereços IP que o roteador recebe pelo ExpressRoute. As rotas recebidas no ExpressRoute são mais específicas, portanto, tornarão o ExpressRoute o caminho preferido do tráfego de autenticação para a Microsoft. Se você não estiver atento à forma como o roteamento é feito em seus problemas de roteamento assimétrico de rede podem surgir.

Se você quiser usar o ExpressRoute para autenticação, verifique se está anunciando AD FS endereços IP públicos no ExpressRoute sem NAT. Quando configurado dessa forma, o tráfego originado da Microsoft vai para seu servidor de AD FS local passará pelo ExpressRoute. O tráfego de retorno da rede que vai para a Microsoft usará o ExpressRoute, pois é a rota preferida pela Internet.

### <a name="source-based-nat"></a>NAT com base em origem
Outra maneira de resolver o problema de roteamento assimétrico é usando SNAT. Por exemplo, você opta por não anunciar o endereço IP público de um servidor SMTP (Simple Mail Transfer Protocol) local no ExpressRoute. Em vez disso, você pretende usar a Internet para esse tipo de comunicação. Uma solicitação originada da Microsoft que vai para o servidor SMTP local atravessa a Internet. Você usa a SNAT na solicitação de entrada para um endereço IP interno. O tráfego de retorno do servidor SMTP vai para o firewall de borda (que você usa para NAT) em vez de por meio do ExpressRoute. Como resultado, o tráfego de retorno usará o caminho da Internet.

![Configuração de rede da NAT com base na origem](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Detecção de roteamento assimétrico
O rastreamento de rotas é a melhor maneira de garantir que o tráfego de rede está atravessando o caminho esperado. Se você espera o tráfego do seu servidor SMTP local para a Microsoft para pegar o caminho da Internet, os traceroute esperados são do servidor SMTP para Microsoft 365. O resultado valida que o tráfego está realmente saindo da rede para a Internet e não para o ExpressRoute.

