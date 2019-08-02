---
title: Cabeçalhos HTTP Verizon específicos para o mecanismo de regras de CDN do Microsoft Azure | Microsoft Docs
description: Este artigo descreve como usar cabeçalhos HTTP Verizon específicos com o mecanismo de regras de CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magattus
ms.openlocfilehash: a5881bea578f2791f8dc0d6e760fd15c6f47e435
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593258"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Cabeçalhos HTTP Verizon específicos para o mecanismo de regras de CDN do Microsoft Azure

Para os produtos **Premium CD do Azure da Verizon**, quando uma solicitação HTTP é enviada ao servidor de origem, o servidor do ponto de presença (POP) pode adicionar um ou mais cabeçalhos reservados (ou cabeçalhos especiais de proxy) na solicitação do cliente ao POP. Esses cabeçalhos estão além dos cabeçalhos de encaminhamento padrão recebidos. Para obter informações sobre cabeçalhos de solicitação padrão, consulte [Campos de solicitação](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Se você quiser impedir que um desses cabeçalhos reservado seja adicionado na solicitação POP de CDN do Microsoft Azure (Rede de Fornecimento de Conteúdo) para o servidor de origem, você deve criar uma regra com o [recurso Cabeçalhos Especiais de Proxy](cdn-verizon-premium-rules-engine-reference-features.md#proxy-special-headers) no mecanismo de regras. Nessa regra, exclua o cabeçalho que você deseja remover da lista padrão de cabeçalhos no campo cabeçalhos. Se você tiver habilitado o [recurso Cabeçalhos de Resposta do Cache de depuração](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers), certifique-se de adicionar os cabeçalhos `X-EC-Debug` necessários. 

Por exemplo, para remover o `Via` cabeçalho, o campo de cabeçalhos da regra deve incluir a seguinte lista de cabeçalhos: *X-Forwarded-For, X-Forwarded-Proto, X-Host, X-Midgress, X-Gateway-List, X-EC-Name, Host*. 

![Regra de Cabeçalhos Especiais de Proxy](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

A tabela a seguir descreve os cabeçalhos que podem ser adicionados pelo Verizon CDN POP na solicitação:

Cabeçalho da solicitação | DESCRIÇÃO | Exemplo
---------------|-------------|--------
[Através de](#via-request-header) | Identifica o servidor POP que faz o proxy da solicitação para um servidor de origem. | HTTP/1.1 ECS (dca/1A2B)
X-Forwarded-For | Indica o endereço IP do solicitante.| 10.10.10.10
X-Forwarded-Proto | Indica o endereço IP do solicitante. | http
X-Host | Indica o nome do host da solicitação. | cdn.mydomain.com
X-Midgress | Indica se a solicitação foi proxy por meio de um servidor adicional de CDN. Por exemplo, um servidor de proteção do servidor de origem POP ou um servidor POP de gateway de servidor para ADN. <br />Esse cabeçalho é adicionado à solicitação somente quando o tráfego de midgress ocorrer. Nesse caso, o cabeçalho é definido como 1 para indicar que a solicitação foi proxy por meio de um servidor adicional de CDN.| 1
[Host](#host-request-header) | Identifica o host e a porta em que o conteúdo solicitado pode ser encontrado. | marketing.mydomain.com:80
[X-Gateway-List](#x-gateway-list-request-header) | ADN: Identifica a lista de failover de servidores de Gateway ADN atribuído a uma origem de cliente. <br />Blindagem da origem: Indica o conjunto de servidores de escudo de origem atribuído a uma origem de cliente. | `icn1,hhp1,hnd1`
X-EC- _&lt;name&gt;_ | Cabeçalhos de solicitação que começam com *X EC* (por exemplo, X-EC-Tag, [EC-X-Debug](cdn-http-debug-headers.md)) são reservados para uso pela CDN.| waf-production

## <a name="via-request-header"></a>Através do cabeçalho da solicitação
O formato por meio do qual o cabeçalho de solicitação `Via` identifica um servidor POP é especificado pela seguinte sintaxe:

`Via: Protocol from Platform (POP/ID)` 

Os termos usados na sintaxe são definidos da seguinte maneira:
- Protocolo: Indica que a versão do protocolo (por exemplo, HTTP/1.1) usada para o proxy da solicitação. 

- Plataforma: Indica a plataforma na qual o conteúdo foi solicitado. Os códigos a seguir são válidos para este campo: 

    Código | Plataforma
    -----|---------
    ECAcc | HTTP grande
    ECS   | HTTP Pequeno
    ECD   | Rede de entrega de aplicativo (ADN)

- POP: Indica o [POP](cdn-pop-abbreviations.md) que tratou a solicitação. 

- ID: Apenas para uso interno.

### <a name="example-via-request-header"></a>Exemplo através do cabeçalho da solicitação

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Cabeçalho da solicitação do host
Os servidores POP substituirão o `Host` cabeçalho quando ambas das seguintes condições forem verdadeiras:
- A fonte para o conteúdo solicitado é um servidor de origem do cliente.
- Opção de cabeçalho de Host HTTP da origem do cliente correspondente não está em branco.

O `Host` cabeçalho da solicitação será substituído para refletir o valor definido na opção de cabeçalho de Host HTTP.
Se a opção de cabeçalho de Host HTTP da origem do cliente é definida como em branco, então o `Host` cabeçalho de solicitação enviado pelo solicitante será encaminhado para o servidor de origem do cliente.

## <a name="x-gateway-list-request-header"></a>Cabeçalho de solicitação X-Gateway-List
Um servidor POP adiciona/substituirá o cabeçalho da solicitação `X-Gateway-List quando qualquer uma das seguintes condições forem atendidas:
- A solicitação aponta para a plataforma ADN.
- A solicitação é encaminhada para um servidor de origem do cliente que está protegido pelo recurso de proteção de origem.

