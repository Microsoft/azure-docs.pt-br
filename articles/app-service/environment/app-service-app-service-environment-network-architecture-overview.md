---
title: Visão geral da arquitetura de rede dos Ambientes de Serviço de Aplicativo – Azure
description: Visão geral da arquitetura da topologia de rede dos Ambientes de Serviço de Aplicativo.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 0d7d4af46e54ad89e0d084cb15af13e56115e996
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765241"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Visão geral da arquitetura de rede dos Ambientes de Serviço de Aplicativo
## <a name="introduction"></a>Introdução
Os Ambientes do Serviço de Aplicativo sempre são criados em uma sub-rede de uma [rede virtual][virtualnetwork]. Aplicativos em execução em um Ambiente do Serviço de Aplicativo podem se comunicar com pontos de extremidade privados localizados na mesma topologia de rede virtual.  Uma vez que os clientes podem bloquear partes da respectiva infraestrutura de rede virtual, é importante entender os tipos de fluxo de comunicação de rede que ocorrem com um Ambiente de Serviço de Aplicativo.

## <a name="general-network-flow"></a>Fluxo de rede geral
Quando um Ambiente de Serviço de Aplicativo (ASE) usa um endereço IP virtual (VIP) público para aplicativos, todo o tráfego de entrada chega nesse VIP público.  Isso inclui o tráfego HTTP e HTTPS para aplicativos, bem como outro tráfego para FTP, funcionalidade de depuração remota e operações de gerenciamento do Azure.  Para obter uma lista completa de portas específicas (necessárias e opcionais) que estão disponíveis no VIP público, confira o artigo sobre [Como controlar o tráfego de entrada][controllinginboundtraffic] para um Ambiente do Serviço de Aplicativo. 

Os Ambientes de Serviço de Aplicativo também dão suporte a aplicativos em execução que são associados apenas a um endereço interno de rede virtual, também conhecido como endereço ILB (balanceador de carga interno).  Em um ASE habilitado para ILB, o tráfego HTTP e HTTPS para aplicativos, bem como as chamadas remotas de depuração, chegam no endereço ILB.  Para as configurações mais comuns de ILB-ASE, o tráfego de FTP/FTPS também chegará no endereço ILB.  No entanto, as operações de gerenciamento do Azure continuarão fluindo para as portas 454/455 do VIP público em ASE habilitado para ILB.

O diagrama a seguir mostra uma visão geral dos vários fluxos de rede de entrada e saída para um Ambiente de Serviço de Aplicativo no qual os aplicativos associados a um endereço IP virtual público:

![Fluxos de rede geral][GeneralNetworkFlows]

Um Ambiente de Serviço de Aplicativo pode se comunicar com uma variedade de pontos de extremidade privados do cliente.  Por exemplo, aplicativos que são executados no Ambiente de Serviço de Aplicativo podem se conectar a servidores de banco de dados em execução em máquinas virtuais de IaaS na mesma topologia de rede virtual.

> [!IMPORTANT]
> Examinando o diagrama de rede, os "Outros recursos do computador" são implantados em uma sub-rede diferente do Ambiente do Serviço de Aplicativo. A implantação de recursos na mesma sub-rede com o ASE bloqueará a conectividade do ASE para esses recursos (com exceção do roteamento específico intra-ASE). Em vez disso, implante em uma sub-rede diferente (na mesma rede virtual). Assim, o Ambiente do Serviço de Aplicativo será capaz de se conectar. Nenhuma configuração adicional é necessária.
> 
> 

Os Ambientes de Serviço de Aplicativo também se comunicam com recursos do Banco de Dados SQL e Armazenamento do Azure necessários para gerenciar e operar um Ambiente de Serviço de Aplicativo.  Alguns dos recursos do SQL e Armazenamento com os quais o Ambiente de Serviço de Aplicativo se comunica estão localizados na mesma região do Ambiente de Serviço de Aplicativo, enquanto outros estão localizados em regiões remotas do Azure.  Consequentemente, a conectividade de saída com a Internet sempre é exigida para que um Ambiente de Serviço de Aplicativo funcione corretamente. 

Uma vez que o Ambiente de Serviço de Aplicativo é implantado em uma sub-rede, os grupos de segurança de rede podem ser usados para controlar o tráfego de entrada para a sub-rede.  Para obter detalhes sobre como controlar o tráfego de entrada para um Ambiente do Serviço de Aplicativo, consulte o [artigo][controllinginboundtraffic] a seguir.

Para obter detalhes sobre como permitir a conectividade de saída da Internet de um Ambiente do Serviço de Aplicativo, consulte o seguinte artigo sobre como trabalhar com o [ExpressRoute][ExpressRoute].  A mesma abordagem descrita no artigo se aplica ao trabalhar com conectividade site a site e usar túnel forçado.

## <a name="outbound-network-addresses"></a>Endereços de rede de saída
Quando um Ambiente de Serviço de Aplicativo faz chamadas de saída, um endereço IP sempre é associado às chamadas de saída.  O endereço IP específico que é usado depende de onde o ponto de extremidade que está sendo chamado está localizado, se dentro ou fora da topologia de rede virtual.

Se o ponto de extremidade que está sendo chamado estiver **fora** da topologia de rede virtual, o endereço de saída (também conhecido como endereço NAT de saída) que será usado é o VIP público do Ambiente de Serviço de Aplicativo.  Esse endereço pode ser encontrado na interface do usuário do portal para o Ambiente de Serviço de Aplicativo na folha Propriedades.

![Endereço IP de saída][OutboundIPAddress]

Esse endereço também pode ser determinado para ASEs que só têm um VIP público ao criar um aplicativo no Ambiente de Serviço de Aplicativo e executar um *nslookup* no endereço do aplicativo. O endereço IP resultante é o VIP público, bem como o endereço NAT de saída do Ambiente de Serviço de Aplicativo.

Se o ponto de extremidade que está sendo chamado estiver **dentro** da topologia de rede virtual, o endereço de saída do aplicativo que está chamando será o endereço IP interno do recurso de computação individual que está executando o aplicativo.  No entanto, não há um mapeamento persistente de endereços IP internos de rede virtual para aplicativos.  Os aplicativos podem se mover entre os diferentes recursos de computação e o pool de recursos de computação disponíveis em um Ambiente de Serviço de Aplicativo pode mudar devido às operações de dimensionamento.

No entanto, uma vez que o Ambiente de Serviço de Aplicativo está sempre localizado em uma sub-rede, você tem a garantia de que o endereço IP interno de um recurso de computação executando um aplicativo sempre estará no intervalo CIDR da sub-rede.  Como resultado, quando ACLs refinadas ou grupos de segurança de rede são usados para proteger o acesso a outros pontos de extremidade na rede virtual, o intervalo de sub-rede que contém o Ambiente de Serviço de Aplicativo precisa ter acesso.

O diagrama a seguir mostra esses conceitos em mais detalhes:

![Endereços de rede de saída][OutboundNetworkAddresses]

No diagrama acima:

* Como o VIP público do Ambiente de Serviço de Aplicativo é 192.23.1.2, esse é o endereço IP de saída usado ao fazer chamadas para pontos de extremidade de "Internet".
* O intervalo CIDR da sub-rede contida para o Ambiente de Serviço de Aplicativo é 10.0.1.0/26.  Outros pontos de extremidade na mesma infraestrutura de rede virtual verá chamadas de aplicativos como originados de algum lugar dentro desse intervalo de endereços.

## <a name="calls-between-app-service-environments"></a>Chamadas entre Ambientes de Serviço de Aplicativo
Um cenário mais complexo pode ocorrer se você implantar vários Ambientes de Serviço de Aplicativo na mesma rede virtual e fizer chamadas de saída de um Ambiente de Serviço de Aplicativo para outro.  Esses tipos de chamadas entre Ambientes de Serviço de Aplicativo também serão tratadas como chamadas de "Internet".

O diagrama a seguir mostra um exemplo de uma arquitetura em camadas com aplicativos em um Ambiente de Serviço de Aplicativo (por exemplo, aplicativos Web de "Porta") chamando aplicativos em um segundo Ambiente de Serviço de Aplicativo (por exemplo, aplicativos internos de API de back-end que não pretendem ser acessíveis pela Internet). 

![Chamadas entre Ambientes de Serviço de Aplicativo][CallsBetweenAppServiceEnvironments] 

No exemplo acima, o Ambiente de Serviço de Aplicativo "ASE One" tem um endereço IP de saída de 192.23.1.2.  Se um aplicativo em execução no Ambiente de Serviço de Aplicativo faz uma chamada de saída a um aplicativo em execução em um segundo Ambiente de Serviço de Aplicativo ("ASE Two") localizado na mesma rede virtual, a chamada de saída será tratada como uma chamada de "Internet".  Como resultado do tráfego de rede que chega, o segundo Ambiente de Serviço de Aplicativo será mostrado como originário de 192.23.1.2 (ou seja, não é o intervalo de endereços de sub-rede do primeiro Ambiente de Serviço de Aplicativo).

Embora chamadas entre diferentes Ambientes de Serviço de Aplicativo sejam tratadas como chamadas de "Internet", quando ambos os Ambientes de Serviço de Aplicativo estão localizados na mesma região do Azure, o tráfego de rede permanece na rede regional do Azure e não flui fisicamente para a Internet pública.  Como resultado, você pode usar um grupo de segurança de rede na sub-rede do segundo Ambiente de Serviço de Aplicativo para permitir somente chamadas de entrada do primeiro Ambiente de Serviço de Aplicativo (aqueles cujo endereço IP de saída é 192.23.1.2), garantindo assim a comunicação segura entre os Ambientes de Serviço de Aplicativo.

## <a name="additional-links-and-information"></a>Informações e links adicionais
Os detalhes sobre as portas de entrada usadas pelos Ambientes do Serviço de Aplicativo e como usar grupos de segurança de rede para controlar o tráfego de entrada estão disponíveis [aqui][controllinginboundtraffic].

Os detalhes sobre como usar rotas definidas pelo usuário para conceder acesso à Internet de saída aos Ambientes do Serviço de Aplicativo estão disponíveis neste [artigo][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

