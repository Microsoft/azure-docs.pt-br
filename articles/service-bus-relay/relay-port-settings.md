---
title: Configurações de porta da Retransmissão do Azure | Microsoft Docs
description: Detalhes sobre os valores de porta da Retransmissão do Azure.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: spelluru
ms.openlocfilehash: 9d11179a8518ebf48f68f8607f94e0253d4edb80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789919"
---
# <a name="azure-relay-port-settings"></a>Configurações de porta de Retransmissão do Azure

A tabela a seguir descreve a configuração necessária dos valores de porta para uma Retransmissão do Azure.

## <a name="hybrid-connections"></a>Conexões Híbridas

Conexões Híbridas usam WebSockets na porta 443 com SSL como o mecanismo de transporte subjacente, que usa **HTTPS**. 

## <a name="wcf-relays"></a>Retransmissões de WCF
  
|Associação|Segurança de transporte|Porta|  
|-------------|------------------------|----------|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (cliente)|Sim|HTTPS| 
|" |Não |HTTP|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (serviço)|Você pode usar o|9351/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (cliente)|Sim|9351/HTTPS|  
|" |Não |9350/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (serviço)|Você pode usar o|9351/HTTP|  
|[Classe NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (cliente/serviço)|Você pode usar o|5671/9352/HTTP (9352/9353 se híbrido for usado)|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (cliente)|Sim|9351/HTTPS|  
|" |Não |9350/HTTP|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (serviço)|Você pode usar o|9351/HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (cliente)|Sim|HTTPS|  
|" |Não |HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (serviço)|Você pode usar o|9351/HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (cliente)|Sim|HTTPS|  
|" |Não |HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (serviço)|Você pode usar o|9351/HTTP|

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Retransmissão do Azure, visite estes links:
* [O que é Retransmissão do Azure?](relay-what-is-it.md)
* [Perguntas frequentes sobre retransmissão](relay-faq.md)