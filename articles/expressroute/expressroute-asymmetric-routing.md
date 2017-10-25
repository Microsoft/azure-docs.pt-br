---
title: "Roteamento assimétrico | Microsoft Docs"
description: "Este artigo aborda os problemas que um cliente pode enfrentar no roteamento assimétrico em uma rede que tem vários links para um destino."
documentationcenter: na
services: expressroute
author: osamazia
manager: carmonm
editor: 
ms.assetid: a754bff9-95c9-44b5-9796-377fc21e8322
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: osamam
ms.openlocfilehash: 8568c13d2834a0643e15ab1814a35c92123837d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Roteamento assimétrico com vários caminhos de rede
Este artigo explica como o encaminhamento e o retorno do tráfego de rede podem adotar rotas diferentes quando há vários caminhos disponíveis entre a origem e o destino da rede.

É importante entender os dois conceitos para compreender o roteamento assimétrico. Um deles é o efeito de vários caminhos de rede. O outro é como os dispositivos, como um firewall, mantêm o estado. Esses tipos de dispositivos são chamados de dispositivos com estado. Uma combinação desses dois fatores cria cenários nos quais o tráfego da rede é descartado por um dispositivo com estado porque tal dispositivo não detectou que o tráfego originou-se no próprio dispositivo.

## <a name="multiple-network-paths"></a>Vários caminhos de rede
Quando uma rede corporativa tem apenas um link com a Internet através de seu provedor de serviços de Internet, todo o tráfego para e da Internet percorre o mesmo caminho. Geralmente, as empresas adquirem vários circuitos, como caminhos redundantes, para melhorar o tempo de atividade de rede. Nesses casos, é possível que o tráfego que vai para fora da rede, em direção à Internet, passe por um link, enquanto o tráfego de retorno passa por um link diferente. Isso é conhecido como roteamento assimétrico. No roteamento assimétrico, tráfego de rede inversa pega um caminho diferente do fluxo original.

![Rede com vários caminhos](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Embora ocorra principalmente na Internet, o roteamento assimétrico também se aplica a outras combinações de vários caminhos. Por exemplo, ele se aplica a um caminho da Internet e a um caminho privado que vai para o mesmo destino, e a vários caminhos privados para vão para o mesmo destino.

Cada roteador no caminho, da origem ao destino, calcula o melhor caminho para alcançar um destino. A determinação do roteador para o melhor caminho possível baseia-se em dois fatores principais:

* O roteamento entre as redes externas é baseado em um protocolo de roteamento, BGP (Border Gateway Protocol). O BGP obtém os anúncios dos vizinhos e executa-os com várias etapas para determinar o melhor caminho para o destino pretendido. Ele armazena o melhor caminho em sua tabela de roteamento.
* O comprimento de uma máscara da sub-rede associada a uma rota influencia os caminhos de roteamento. Se um roteador receber vários anúncios para o mesmo endereço IP, mas com máscaras de sub-rede diferentes, o roteador irá preferir o anúncio com uma máscara de sub-rede maior, pois é considerada uma rota mais específica.

## <a name="stateful-devices"></a>Dispositivos com estado
Os roteadores examinam o cabeçalho IP de um pacote para fazer o roteamento. Alguns dispositivos verificam ainda mais dentro do pacote. Normalmente, esses dispositivos examinam os cabeçalhos da Camada4 (Protocolo de Controle de Transmissão - TCP; User Datagram Protocol - UDP) ou até mesmo da Camada7. Esses tipos de dispositivos são dispositivos de otimização da largura de banda ou dispositivos de segurança. 

O firewall é um exemplo comum de dispositivo com estado. O firewall permite ou nega a passagem de um pacote nas interfaces com base em vários campos, como protocolo, porta TCP/UDP e cabeçalhos de URL. Esse nível de inspeção de pacotes coloca uma pesada carga de processamento no dispositivo. Para melhorar o desempenho, o firewall inspeciona o primeiro pacote de um fluxo. Se ele permitir que o pacote prossiga, manterá as informações de fluxo em sua tabela de estado. Todos os pacotes subsequentes relacionados a esse fluxo serão permitidos com base na determinação inicial. Um pacote que faz parte de um fluxo existente pode chegar no firewall. Se o firewall não tiver nenhuma informação do estado anterior sobre ele, irá descartar o pacote.

## <a name="asymmetric-routing-with-expressroute"></a>Roteamento assimétrico com o ExpressRoute
Quando você conecta a Microsoft por meio do ExpressRoute do Azure, sua rede muda da seguinte maneira:

* Você tem vários links para a Microsoft. Um link é sua conexão com a Internet existente e o outro é via ExpressRoute. Parte do tráfego para a Microsoft pode passar pela Internet, mas voltar por meio do ExpressRoute ou vice-versa.
* Você recebe endereços IP mais específicos por meio do ExpressRoute. Portanto, o tráfego da rede para a Microsoft, para os serviços oferecidos por meio do ExpressRoute, sempre prefere o ExpressRoute.

Para entender o efeito que essas duas alterações têm em uma rede, iremos considerar alguns cenários. Como exemplo, você tem apenas um circuito para a Internet e consome todos os serviços da Microsoft via Internet. O tráfego de ida e volta de sua rede para a Microsoft atravessa o mesmo link da Internet e passa pelo firewall. O firewall registra o fluxo ao ver o primeiro pacote e os pacotes de retorno são permitidos porque existe fluxo na tabela de estados.

![Roteamento assimétrico com o ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Então, você ativa o ExpressRoute e consome os serviços oferecidos pela Microsoft por meio do ExpressRoute. Todos os outros serviços da Microsoft são consumidos por meio da Internet. Você pode implantar um firewall separado em sua borda conectada à ExpressRoute. A Microsoft anuncia os prefixos mais específicos para sua rede por meio do ExpressRoute dos serviços específicos. Sua infraestrutura de roteamento escolhe o ExpressRoute como o caminho preferido desses prefixos. Se você não estiver anunciando seus endereços IP públicos para a Microsoft no ExpressRoute, a Microsoft se comunicará com seus endereços IP públicos por meio da Internet. O tráfego de encaminhamento de sua rede para a Microsoft usa o ExpressRoute, enquanto o tráfego inverso da Microsoft usa a Internet. Quando o firewall na borda vir um pacote de resposta para um fluxo não encontrado na tabela de estados, ele descartará o tráfego de retorno.

Se você optar por usar o mesmo pool NAT (Conversão de Endereços de Rede) do ExpressRoute e da Internet, verá problemas semelhantes com os clientes nos endereços IP privados em sua rede. As solicitações dos serviços como o Windows Update passam pela Internet, pois os endereços IP para esses serviços não são divulgados via ExpressRoute. No entanto, o tráfego de retorno volta via ExpressRoute. Se a Microsoft receber um endereço IP com a mesma máscara de sub-rede da Internet e do ExpressRoute, preferirá o ExpressRoute na Internet. Se um firewall ou outro dispositivo com estado na borda da rede, voltado para o ExpressRoute, não tiver informações anteriores sobre o fluxo, descartará os pacotes que pertencem ao fluxo.

## <a name="asymmetric-routing-solutions"></a>Soluções do roteamento assimétrico
Você tem duas opções principais para resolver o problema do roteamento assimétrico. Uma é por meio do roteamento e a outra é usando a NAT com base na origem (SNAT).

### <a name="routing"></a>Roteamento
Verifique se seus endereços IP públicos são anunciados para os devidos links WAN. Por exemplo, se você quiser usar a Internet para o tráfego de autenticação e o ExpressRoute para o tráfego de email, não deverá anunciar seus endereços IP públicos dos Serviços de Federação do Active Directory (AD FS) no ExpressRoute. Da mesma forma, não exponha um servidor AD FS local para os endereços IP que o roteador recebe no ExpressRoute. As rotas recebidas no ExpressRoute são mais específicas, portanto, tornarão o ExpressRoute o caminho preferido do tráfego de autenticação para a Microsoft. Isso causa o roteamento assimétrico.

Se você quiser usar o ExpressRoute para a autenticação, verifique se está anunciando os endereços IP públicos do AD FS no ExpressRoute sem a NAT. Dessa forma, o tráfego que se origina na Microsoft e vai para um servidor AD FS local passa pelo ExpressRoute. O tráfego de retorno do cliente para a Microsoft usa o ExpressRoute porque é a rota preferida na Internet.

### <a name="source-based-nat"></a>NAT com base em origem
Outra maneira de solucionar os problemas do roteamento assimétrico é usando a SNAT. Por exemplo, você não anunciou o endereço IP público de um servidor SMTP (Simple Mail Transfer Protocol) local no ExpressRoute porque pretende usar a Internet para esse tipo de comunicação. Uma solicitação que se origina na Microsoft e vai para o servidor SMTP local atravessa a Internet. Você usa a SNAT na solicitação de entrada para um endereço IP interno. O tráfego reverso do servidor SMTP vai para o firewall da borda (que você usa para a NAT), em vez de passar pelo ExpressRoute. O tráfego de retorno volta via Internet.

![Configuração de rede da NAT com base na origem](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Detecção de roteamento assimétrico
O rastreamento de rotas é a melhor maneira de garantir que o tráfego de rede está atravessando o caminho esperado. Se você espera que o tráfego do servidor SMTP local para a Microsoft tome o caminho da Internet, o rastreamento de rotas esperado é do servidor SMTP para o Office 365. O resultado valida que o tráfego realmente sai de sua rede para a Internet, não na direção do ExpressRoute.

