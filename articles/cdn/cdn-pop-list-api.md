---
title: Recuperar a lista de IP POP atual para a CDN do Azure | Microsoft Docs
description: Saiba como obter servidores POP usando a API REST. Os servidores POP fazem solicitações aos servidores de origem associados aos pontos de extremidade da rede de distribuição de conteúdo do Azure.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 4197b1a5f047190872d055dc2ba8ccaa11efbe6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376336"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Recuperar a lista de IP POP atual para a CDN do Azure

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Recuperar a lista de IPS POP do Verizon atual para a CDN do Azure

Você pode usar a API REST para recuperar o conjunto de IPs para o ponto da Verizon dos servidores de presença (POP). Esses servidores POP fazem solicitações para servidores de origem que estão associados com os pontos de extremidade do Azure Content Delivery Network (CDN) em um perfil Verizon (**Azure CDN padrão da Verizon** ou **Premium do Azure CDN da Verizon**). Observe que esse conjunto de IPs é diferente dos IPs que um cliente veria ao fazer solicitações aos POPs. 

Para obter a sintaxe da operação da API REST para recuperar a lista POP, consulte [Nós de Borda - Lista](/rest/api/cdn/cdn/edgenodes/list).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Recuperar a lista de IP POP atual da Microsoft para a CDN do Azure

Para bloquear seu aplicativo para aceitar o tráfego somente da CDN do Azure da Microsoft, você precisará configurar ACLs de IP para seu back-end. Você também pode restringir o conjunto de valores aceitos para o cabeçalho ' X-Forwarded-host ' enviado pela CDN do Azure da Microsoft. Essas etapas são detalhadas como a seguir:

Configure o IP atuação para seus back-ends para aceitar o tráfego da CDN do Azure do espaço de endereço IP de back-end da Microsoft e dos serviços de infraestrutura do Azure apenas. 

* CDN do Azure do espaço IP de back-end IPv4 da Microsoft: 147.243.0.0/16
* CDN do Azure do espaço IP de back-end IPv6 da Microsoft: 2a01:111:2050::/44

Para usar marcas de serviço com a CDN do Azure da Microsoft, use a marca de porta frontal do Azure. Os intervalos de IP e as marcas de serviço para os serviços da Microsoft podem ser encontrados [aqui](https://www.microsoft.com/download/details.aspx?id=56519)


## <a name="typical-use-case"></a>Caso de uso típico

Por motivos de segurança, use essa lista de IPs para impor que as solicitações para o seu servidor de origem sejam feitas apenas a partir de um POP válido da Verizon. Por exemplo, se alguém descobrir o nome do host ou o endereço IP do servidor de origem de um ponto de extremidade de CDN, seria possível fazer solicitações diretamente ao servidor de origem, ignorando os recursos de dimensionamento e segurança fornecidos pela CDN do Azure. Ao definir os IPs na lista retornada como os únicos IPs permitidos em um servidor de origem, esse cenário pode ser evitado. Para garantir que você tenha a lista POP mais recente, recupere-a pelo menos uma vez por dia. 

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre a API REST, consulte [Azure CDN REST API](/rest/api/cdn/).