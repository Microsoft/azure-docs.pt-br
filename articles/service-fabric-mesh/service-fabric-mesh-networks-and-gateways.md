---
title: Introdução à rede do Azure Service Fabric | Microsoft Docs
description: Saiba mais sobre redes, gateways e roteamento de tráfego inteligente na Malha do Service Fabric.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/26/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: b0e1047c5bbd7d8caaf2afd8b002be1c46837852
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811069"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Introdução à rede nos aplicativos da Malha do Service Fabric
Este artigo descreve os diferentes tipos de balanceadores de carga, como gateways conectam a rede com os aplicativos às outras redes e como o tráfego é roteado entre os serviços nos aplicativos.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Balanceadores de carga de camada 4 vs camada 7
O balanceamento de carga pode ser executado em diferentes camadas no [modelo OSI](https://en.wikipedia.org/wiki/OSI_model) para rede, frequentemente na camada 4 (L4) e na camada 7 (L7).  Geralmente, há dois tipos de balanceadores de carga:

- Um balanceador de carga L4 funciona na camada de transporte de rede, que lida com a entrega de pacotes sem considerar o conteúdo dos pacotes. Somente os cabeçalhos de pacote são inspecionados pelo balanceador de carga, portanto, os critérios de balanceamento são limitados a endereços IP e portas. Por exemplo, um cliente faz uma conexão TCP para o balanceador de carga. O balanceador de carga termina a conexão (respondendo diretamente ao SYN), seleciona um back-end e faz uma nova conexão TCP para o back-end (envia um novo SYN). Geralmente, um balanceador de carga L4 opera apenas no nível da sessão ou conexão TCP/UDP L4. Portanto, o balanceamento de carga redireciona bytes ao redor e garante que os bytes da mesma sessão acabem no mesmo back-end. O balanceador de carga L4 não reconhece quaisquer detalhes de aplicativo dos bytes que está movendo. Os bytes pode ser qualquer protocolo de aplicativo.

- Um balanceador de carga L7 funciona na camada de aplicativo, que lida com o conteúdo de cada pacote. Ele inspeciona o conteúdo do pacote porque reconhece protocolos como HTTP, HTTPS ou WebSockets. Isso provê ao balanceador de carga a capacidade de executar o roteamento avançado. Por exemplo, um cliente faz uma conexão TCP HTTP/2 única para o balanceador de carga. O balanceador de carga então realiza duas conexões de back-end. Quando o cliente envia dois fluxos HTTP/2 para o balanceador de carga, o primeiro fluxo é enviado ao back-end um e o segundo fluxo é enviado ao back-end dois. Assim, até mesmo os clientes de multiplexação que tenham cargas de solicitação muito diferentes serão balanceados com eficiência em todos os back-ends. 

## <a name="networks-and-gateways"></a>Redes e gateways
No [Modelo de Recurso do Service Fabric](service-fabric-mesh-service-fabric-resources.md), um recurso de rede é um recurso individualmente implantável, independente de um recurso de aplicativo ou serviço que pode fazer referência a ele como sua dependência. Ele é usado para criar uma rede para os aplicativos que é aberta para a Internet. Vários serviços de diferentes aplicativos podem fazer parte da mesma rede. Essa rede privada é criada e gerenciada pelo Service Fabric e não é uma VNET (rede virtual) do Azure. Aplicativos podem ser dinamicamente adicionados e removidos do recurso de rede para habilitar e desabilitar a conectividade da VNET. 

Um gateway é usado para interligar duas redes. O recurso de Gateway implanta um [proxy Envoy](https://www.envoyproxy.io/) que fornece o roteamento L4 para qualquer protocolo e roteamento L7 para roteamento de aplicativo HTTP(S) avançado. O gateway roteia o tráfego na rede a partir de uma rede externa e determina qual serviço será roteado para o tráfego.  A rede externa pode ser uma rede aberta (essencialmente, a rede pública) ou uma rede virtual do Azure, permitindo conectar aos outros aplicativos e recursos do Azure. 

![Rede e gateway][Image1]

Quando o recurso de rede é criado com `ingressConfig`, um IP público é atribuído ao recurso de rede. O IP público será vinculado ao tempo de vida do recurso de rede.

Quando um aplicativo de malha é criado, ele deve fazer referência a um recurso de rede existente. Novas portas públicas podem ser adicionadas ou portas existentes podem ser removidas da configuração de entrada. Uma exclusão para um recurso de rede falhará se um recurso de aplicativo estiver fazendo referência a ele. Quando o aplicativo é excluído, o recurso de rede será removido.

## <a name="next-steps"></a>Próximas etapas 
Para saber mais sobre Malha do Service Fabric, leia a visão geral:
- [Visão geral da Malha do Service Fabric](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png