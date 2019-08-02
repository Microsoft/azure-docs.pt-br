---
title: Azure Front Door Service | Microsoft Docs
description: Este artigo fornece uma visão geral do Azure Front Door. Descubra se ele é a escolha certa para balancear a carga do tráfego de usuário para seu aplicativo.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 20cfcea4a8b58c1c01a7c710163b7320ff96d65c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330829"
---
# <a name="what-is-azure-front-door-service"></a>O que é o Azure Front Door Service?
O Azure Front Door Service permite que você defina, gerencie e monitore o roteamento global para seu tráfego da Web otimizando para melhor desempenho e failover global instantâneo para ter alta disponibilidade. Com o Front Door, é possível transformar seus aplicativos consumidores e empresariais globais (de várias regiões) em modernos aplicativos robustos altamente personalizados e com alto desempenho, APIs e conteúdo que alcançam um público global com o Azure.

O Front Door funciona na Camada 7 ou na Camada HTTP/HTTPS e usa o protocolo anycast com TCP de divisão e a rede global da Microsoft para melhorar a conectividade global. Portanto, de acordo com a seleção do método de roteamento na configuração, é possível garantir que o Front Door esteja roteando suas solicitações de cliente para o back-end de aplicativo mais rápido e disponível. Um back-end de aplicativo é qualquer serviço voltado para a Internet hospedado dentro ou fora do Azure. O Front Door oferece uma variedade de [métodos de roteamento de tráfego](front-door-routing-methods.md) e [opções de monitoramento de integridade de back-end](front-door-health-probes.md) para atender às diferentes necessidades dos aplicativos e modelos de failover automático. De maneira semelhante ao [Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md), o Front Door é resiliente a falhas, incluindo a falha de uma região inteira do Azure.

>[!NOTE]
> O Azure fornece um conjunto de soluções de balanceamento de carga totalmente gerenciadas para seus cenários. Se você estiver procurando um roteamento global baseado em DNS e **não** tiver requisitos para encerramento de protocolo TLS ("descarregamento de SSL") ou para processamento de camada de aplicativo por solicitação HTTP/HTTPS, consulte [Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md). Se você estiver em busca de balanceamento de carga entre os servidores em uma região, para a camada de aplicativo, consulte [Gateway de Aplicativo](../application-gateway/application-gateway-introduction.md) e, para o balanceamento de carga de camada de rede, consulte [Load Balancer](../load-balancer/load-balancer-overview.md). Cenários de ponta a ponta podem se beneficiar da combinação dessas soluções conforme for necessário.

Os recursos a seguir estão incluídos com o Front Door:

## <a name="accelerate-application-performance"></a>Acelerar o desempenho do aplicativo
Usando o protocolo anycast baseado em TCP de divisão, o Front Door garante que seus usuários finais se conectem imediatamente ao POP (Ponto de Presença) do Front Door mais próximo. Usando a rede global da Microsoft para conectar-se aos back-ends de aplicativo de POPs do Front Door, garanta que haja maior disponibilidade e confiabilidade mantendo o desempenho. Essa conectividade com o back-end também se baseia na menor latência de rede. Saiba mais sobre técnicas de roteamento do Front Door como [TCP de divisão](front-door-routing-architecture.md#splittcp) e [Protocolo anycast](front-door-routing-architecture.md#anycast).

## <a name="increase-application-availability-with-smart-health-probes"></a>Aumentar a disponibilidade do aplicativo com investigações de integridade inteligentes

O Front Door oferece alta disponibilidade para seus aplicativos críticos que usam suas investigações de integridade inteligentes, monitorando a latência e a disponibilidade dos seus back-ends e fornecendo failover automático instantâneo quando um back-end fica inoperante. Portanto, é possível executar operações de manutenção planejada em seus aplicativos sem tempo de inatividade. O Front Door direciona o tráfego para back-ends alternativos enquanto a manutenção está em andamento.

## <a name="url-based-routing"></a>Roteamento baseado em URL
O Roteamento baseado em caminho de URL permite rotear o tráfego para pools de back-end com base nos caminhos de URL da solicitação. Um dos cenários possíveis é rotear as solicitações de tipos de conteúdo diferentes para pools de back-end diferentes.

Por exemplo, as solicitações para `http://www.contoso.com/users/*` são roteadas para UserProfilePool e para `http://www.contoso.com/products/*` são roteadas para ProductInventoryPool.  O Front Door permite cenários de correspondência de rotas mais complexos usando o melhor algoritmo de correspondência; portanto, se nenhum dos padrões de caminho for correspondente, então a regra de roteamento padrão para `http://www.contoso.com/*` será selecionada e o tráfego será direcionado à regra de roteamento padrão que captura tudo. Saiba mais em [Route Matching](front-door-route-matching.md) (Correspondência de rotas).

## <a name="multiple-site-hosting"></a>Hospedagem de vários sites
A hospedagem de vários sites permite configurar mais de um site na mesma configuração do Front Door. Esse recurso permite que você configure uma topologia mais eficiente para suas implantações adicionando diferentes sites a uma única configuração do Front Door. Com base na arquitetura do seu aplicativo, é possível configurar o Azure Front Door Service para direcionar cada site para o próprio pool de back-end ou fazer com que vários sites sejam direcionados para o mesmo pool de back-end. Por exemplo, o Front Door pode servir o tráfego para `images.contoso.com` e `videos.contoso.com` de dois pools de back-end chamados ImagePool e VideoPool. Ou é possível configurar os dois hosts de front-end para direcionar o tráfego para um único pool de back-end chamado MediaPool.

De maneira semelhante, é possível fazer com que dois domínios diferentes `www.contoso.com` e `www.fabrikam.com` sejam configurados no mesmo Front Door.

## <a name="session-affinity"></a>Afinidade de sessão
O recurso de afinidade de sessão baseada em cookies é útil quando você deseja manter uma sessão de usuário no mesmo back-end de aplicativo. Usando os cookies gerenciados pelo Front Door, o tráfego subsequente de uma sessão de usuário é direcionado para o back-end do mesmo aplicativo para processamento. Esse recurso é importante em casos em que o estado de sessão é salvo localmente no back-end para uma sessão de usuário.

## <a name="secure-sockets-layer-ssl-termination"></a>Encerramento do protocolo SSL
O Front Door é compatível com o encerramento do SSL na borda, ou seja, os usuários individuais podem configurar a conexão SSL com os ambientes do Front Door, em vez de estabelecê-la por meio de longas conexões com o back-end do aplicativo. Além disso, o Front Door é compatível com a conectividade HTTP e HTTPS entre os ambientes do Front Door e seus back-ends. Portanto, também é possível configurar a criptografia SSL de ponta a ponta. Por exemplo, se o Front Door da carga de trabalho do seu aplicativo receber mais de 5 mil solicitações por minuto, devido à reutilização de conexão quente, para serviços ativos, ele apenas estabelecerá cerca de 500 conexões com o back-end do seu aplicativo, reduzindo, assim, a carga significativa dos seus back-ends.

## <a name="custom-domains-and-certificate-management"></a>Gerenciamento de domínios e certificados personalizados
Quando você usa o Front Door para distribuir conteúdo, será necessário um domínio personalizado se você desejar que seu próprio nome de domínio esteja visível na URL do Front Door. Ter um nome de domínio visível pode ser conveniente para os clientes e útil para fins de identidade visual.
O Front Door também é compatível com HTTPS para nomes de domínio personalizados. Use esse recurso escolhendo certificados gerenciados pelo Front Door para seu tráfego ou carregando seu próprio certificado SSL personalizado.

## <a name="application-layer-security"></a>Segurança da camada de aplicativo
O Azure Front Door permite que você crie regras WAF (firewall do aplicativo Web) personalizadas para obter controle de acesso a fim de proteger sua carga de trabalho HTTP/HTTPS contra exploração com base em endereços IP do cliente, o código do país e parâmetros http. Além disso, o Front Door também permite que você crie regras de limitação de taxa para combater o tráfego de bot mal-intencionado. 

A plataforma do Front Door é protegida pela [Proteção contra DDoS do Azure](../virtual-network/ddos-protection-overview.md) Básica. Para aumentar a proteção, a Proteção contra DDoS do Azure Standard pode ser habilitada em suas VNETs e proteger recursos contra ataques de camada de rede (TCP/UDP) por meio do ajuste automático e atenuação. O Front Door é um proxy reverso de camada 7; ele apenas permite que o tráfego da Web seja passado para back-ends e bloqueie outros tipos de tráfego por padrão.

## <a name="url-redirection"></a>Redirecionamento de URL
Com a pressão forte do setor para oferecer suporte à comunicação somente segura, os aplicativos Web devem redirecionar automaticamente todo o tráfego HTTP para HTTPS. Isso garante que todas as comunicações entre os usuários e o aplicativo ocorram em um caminho criptografado. 

Tradicionalmente, os proprietários do aplicativo têm lidado com esse requisito criando um serviço dedicado, cujo único propósito era redirecionar as solicitações recebidas de HTTP para HTTPS. O Azure Front Door Service suporta a capacidade de redirecionar o tráfego de HTTP para HTTPS. Isso simplifica a configuração do aplicativo, otimiza o uso de recursos e dá suporte a novos cenários de redirecionamento, incluindo redirecionamento global e baseado no caminho. O redirecionamento de URL do Azure Front Door Service não é limitado apenas ao redirecionamento de HTTP para HTTPS, mas também redireciona de um nome de host diferente, redirecionando para um caminho diferente ou até mesmo redirecionando para uma nova cadeia de caracteres de consulta na URL.

Para obter mais informações, consulte [redirecionamento do tráfego](front-door-url-redirect.md) com o Azure Front Door Service.

## <a name="url-rewrite"></a>Regravação de URL
O Front Door é compatível com a [regravação de URL](front-door-url-rewrite.md) ao permitir que você configure um Caminho de Encaminhamento Personalizado opcional para ser usado ao construir a solicitação a ser encaminhada para o back-end. O Front Door permite, além disso, que você configure o cabeçalho do host para ser enviado ao encaminhar a solicitação para seu back-end.

## <a name="protocol-support---ipv6-and-http2-traffic"></a>Suporte ao protocolo – IPv6 e tráfego HTTP/2
O Azure Front Door dá suporte nativo à conectividade IPv6 de ponta a ponta e ao protocolo HTTP/2 também. 

O protocolo HTTP/2 permite uma comunicação full duplex entre back-ends de aplicativo e um cliente durante uma conexão TCP de longa execução. O HTTP/2 permite uma comunicação mais interativa entre o back-end e o cliente, que pode ser bidirecional, sem a necessidade de sondagem, necessária nas implementações baseadas em HTTP. O protocolo HTTP/2 tem baixa sobrecarga, ao contrário do HTTP e pode reutilizar a mesma conexão TCP para várias solicitações ou respostas, resultando em uma utilização mais eficiente de recursos. Saiba mais sobre [Suporte ao HTTP/2 no Azure Front Door Service](front-door-http2.md).

## <a name="pricing"></a>Preços

Para obter informações sobre preços, consulte [Preços do Front Door](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
