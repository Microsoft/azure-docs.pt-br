---
title: Visão geral do serviço de emparelhamento do Azure
description: Saiba mais sobre a visão geral do serviço de emparelhamento do Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 198ba23920179e71e095e498ee2173d7f0111d42
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026722"
---
# <a name="azure-peering-service-overview"></a>Visão geral do serviço de emparelhamento do Azure

O Serviço de Emparelhamento do Azure é um serviço de rede que aprimora a conectividade do cliente com os serviços em nuvem da Microsoft, como o Microsoft 365, o Dynamics 365, os serviços de SaaS (software como serviço), o Azure ou qualquer serviço da Microsoft acessível por meio da Internet pública. A Microsoft estabeleceu um parceiro com provedores de serviços de Internet (ISPs), parceiros do Exchange da Internet (IXPs) e provedores de SDCI (interconexão de nuvem definido pelo software) em todo o mundo para fornecer conectividade pública confiável e de alto desempenho com o roteamento ideal do cliente para a rede da Microsoft.

Com o serviço de emparelhamento, os clientes podem selecionar um provedor de serviços de parceiro bem conectado em uma determinada região. A conectividade pública é otimizada para alta confiabilidade e latência mínima dos serviços de nuvem para o local do usuário final.

![Conectividade distribuída para a nuvem da Microsoft](./media/peering-service-about/peering-service-what.png)

Os clientes também podem optar pela telemetria de serviço de emparelhamento, como medidas de latência do usuário para a rede da Microsoft, monitoramento de rota BGP e alertas contra vazamentos e seqüestros registrando a conexão do serviço de emparelhamento no portal do Azure. 

Para usar o serviço de emparelhamento, os clientes não precisam se registrar na Microsoft. O único requisito é entrar em contato com um [parceiro de serviço de emparelhamento](location-partners.md) para obter o serviço. Para aceitar a telemetria de serviço de emparelhamento, os clientes devem registrá-lo no portal do Azure.

Para obter instruções sobre como registrar o serviço de emparelhamento, consulte [registrar serviço de emparelhamento usando o portal do Azure](azure-portal.md). 

> [!NOTE]
> Este artigo destina-se a arquitetos de rede encarregados de conectividade corporativa com a nuvem e com a Internet.


## <a name="what-is-peering-service"></a>O que é o Serviço de Emparelhamento?

O serviço de emparelhamento é:

- Um serviço de IP que usa a Internet pública. 
- Uma plataforma de colaboração com provedores de serviços e um serviço de valor agregado que tem o objetivo de oferecer um roteamento ideal e confiável para o cliente por meio de parceiros de provedor de serviços para a nuvem da Microsoft pela rede pública.

O serviço de emparelhamento não é um produto de conectividade privada como o Azure ExpressRoute ou um produto de VPN.

> [!NOTE]
> Para obter mais informações sobre o ExpressRoute, consulte a [documentação do expressroute](../expressroute/index.yml).
>

## <a name="background"></a>Tela de fundo

Microsoft 365, o Dynamics 365 e qualquer outro serviço SaaS da Microsoft são hospedados em vários data centers da Microsoft e podem ser acessados de qualquer local geográfico. A rede global da Microsoft tem locais de PoP (ponto de presença) do Microsoft Edge no mundo onde ele pode se conectar a um usuário final por meio de seus provedores de serviços. 

Os provedores de serviços de parceiros e da Microsoft garantem que o tráfego para os prefixos registrados com uma conexão de serviço de emparelhamento entre e saia dos locais PoP mais próximos do Microsoft Edge na rede global da Microsoft. A Microsoft garante que a saída do tráfego de rede dos prefixos registrados com conexões de serviço de emparelhamento leva os locais de PoP mais próximos do Microsoft Edge na rede global da Microsoft.

![Conectividade pública e de rede da Microsoft](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> Para obter mais informações sobre a rede global da Microsoft, consulte [Microsoft Global Network](../networking/microsoft-global-network.md).
>

## <a name="why-use-peering-service"></a>Por que usar o serviço de emparelhamento?

As empresas que procuram o acesso pela Internet à nuvem ou considerando a arquitetura SD-WAN ou com alto uso de serviços SaaS da Microsoft precisam de conectividade de Internet robusta e de alto desempenho. Os clientes podem fazer essa transição acontecer usando o serviço de emparelhamento. A Microsoft e os provedores de serviços fizeram parceria para fornecer conectividade pública centrada no desempenho e confiável para a nuvem da Microsoft. Alguns dos principais recursos do cliente estão listados aqui:

- Melhor roteamento público pela Internet para Serviços de Nuvem do Microsoft Azure para desempenho e confiabilidade ideais.
- Capacidade de selecionar o provedor de serviços preferencial para se conectar ao Microsoft Cloud.
- Informações de tráfego, como relatórios de latência e monitoramento de prefixo.
- Saltos de rede ideais (como saltos) da nuvem da Microsoft.
- Análise de rota e estatísticas: eventos de anomalias de rota ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) (detecção de vazamento ou de seqüestro) e roteamento abaixo do ideal.

### <a name="robust-reliable-peering"></a>Emparelhamento robusto e confiável

O serviço de emparelhamento usa dois tipos de redundância:

- **Redundância local**

   Os provedores de serviços e da Microsoft interconectam-se em vários locais PoP do Microsoft Edge para fornecer serviço de emparelhamento. Em cada local, a interconexão deve oferecer suporte a failover em dois roteadores.

   Cada local de emparelhamento é provisionado com links de emparelhamento redundantes e diversos.

- **Redundância geográfica**

   A Microsoft interconectado com provedores de serviço em vários locais metro para que, se um dos nós de borda tiver degradado o desempenho, o tráfego roteia para e da Microsoft por meio de sites alternativos. A Microsoft roteia o tráfego em sua rede global usando políticas de roteamento baseadas em SDN para otimizar o desempenho.

    Esse tipo de redundância usa o caminho de roteamento mais curto, sempre escolhendo o PoP do Microsoft Edge mais próximo ao usuário final e garante que o cliente seja um salto de rede (como saltos) da Microsoft.

   ![Redundância geográfica](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>Roteamento ideal

A seguinte técnica de roteamento é preferida:

-  **Roteamento de batata frio**

   A técnica de roteamento Cold-batata definida pelo software oferece controle sobre o tráfego de rede originado da nuvem da Microsoft. Ele garante que o tráfego permaneça na rede global da Microsoft de alta capacidade, baixa latência e altamente confiável até que seja o mais próximo possível do destino.
   
   O roteamento que não usa a técnica Cold-batata é chamado de roteamento de alta-batata. Com o roteamento de alta-batata, o tráfego originado da nuvem da Microsoft passa pela Internet.

   ![Roteamento de batata frio](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>Plataforma de monitoramento

   O monitoramento de serviço é oferecido para analisar o tráfego e o roteamento do cliente, além de fornecer os seguintes recursos: 

-  **Detecção de anomalias de rota BGP da Internet**
          
   Esse serviço é usado para detectar e alertar para qualquer evento de anomalia de rota como seqüestros de rota para os prefixos do cliente.

-  **Latência do cliente**

   Esse serviço monitora o desempenho de roteamento entre o local do cliente e a Microsoft. 
   
   O desempenho de roteamento é medido Validando o tempo de ida e volta do cliente para acessar o PoP do Microsoft Edge. Os clientes podem exibir os relatórios de latência para diferentes localizações geográficas.

   O monitoramento captura os eventos no caso de qualquer degradação do serviço.

   ![Plataforma de monitoramento para serviço de emparelhamento](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>Proteção de tráfego

O roteamento ocorre apenas por meio de um caminho preferencial que é definido quando o cliente é registrado com o serviço de emparelhamento.

A Microsoft garante que o tráfego seja roteado por meio de caminhos preferenciais, mesmo se uma atividade mal-intencionada for detectada.

As anomalias de rota BGP são relatadas no portal do Azure, se houver.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre conexões de serviço de emparelhamento, consulte [conexões de serviço de emparelhamento](connection.md).
- Para saber mais sobre telemetria de conexão do Serviço de Emparelhamento, confira [Telemetria de conexão do Serviço de Emparelhamento](connection-telemetry.md).
- Para encontrar um parceiro de provedor de serviços, consulte [parceiros e locais de serviço de emparelhamento](location-partners.md).
- Para carregar uma conexão de serviço de emparelhamento, consulte [modelo de serviço de emparelhamento de integração](onboarding-model.md).
- Para registrar uma conexão usando o portal do Azure, consulte [registrar uma conexão de serviço de emparelhamento usando o portal do Azure](azure-portal.md).
- Para medir a telemetria, confira [Medir telemetria de conexão](measure-connection-telemetry.md).