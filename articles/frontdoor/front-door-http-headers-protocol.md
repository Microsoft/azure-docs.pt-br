---
title: Protocolo oferecem suporte para cabeçalhos HTTP no serviço de porta da frente do Azure | Microsoft Docs
description: Este artigo descreve os protocolos de cabeçalho HTTP que dá suporte ao serviço de porta da frente.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 92e8435e4336c68982e4becc2a95f99b2c776c0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736626"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>Suporte de protocolo para cabeçalhos HTTP no serviço de porta da frente do Azure
Este artigo descreve o protocolo com suporte do serviço de porta da frente com partes do caminho da chamada (consulte a imagem). As seções a seguir fornecem mais informações sobre cabeçalhos HTTP com suporte pelo serviço de porta da frente.

![Protocolo de cabeçalhos HTTP do Azure Front Door Service][1]

>[!IMPORTANT]
>Serviço de porta da frente não certificar todos os cabeçalhos HTTP que não estão documentados aqui.

## <a name="client-to-front-door-service"></a>Cliente para o serviço de porta da frente
Serviço de porta da frente aceita a maioria dos cabeçalhos da solicitação de entrada sem modificá-los. Alguns cabeçalhos reservados são removidos da solicitação de entrada se enviada, incluindo os cabeçalhos com o X - FD-* prefixo.

## <a name="front-door-service-to-backend"></a>Serviço de porta da frente para back-end

Serviço de porta da frente inclui cabeçalhos de uma solicitação de entrada, a menos que removido devido às restrições. Porta da frente também adiciona os seguintes cabeçalhos:

| Cabeçalho  | Exemplo e descrição |
| ------------- | ------------- |
| Através de |  Via: 1.1 azure </br> Porta da frente adiciona a versão HTTP do cliente seguido *Azure* como o valor para o cabeçalho Via. Isso indica a versão do HTTP do cliente e essa porta da frente era um destinatário intermediário para a solicitação entre o cliente e o back-end.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Representa o endereço IP do cliente associado com a solicitação que está sendo processada. Por exemplo, uma solicitação proveniente de um proxy pode adicionar o cabeçalho X-Forwarded-For para indicar o endereço IP do chamador original. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Representa o endereço IP do soquete associado com a conexão TCP que a solicitação atual foi originada. Endereço IP do cliente da solicitação não pode ser igual ao seu endereço IP de soquete porque ele pode ser arbitrariamente substituído por um usuário.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Uma cadeia de caracteres de referência exclusivo que identifica uma solicitação no servidas por porta da frente. Ele é usado para pesquisar os logs de acesso e críticos para solução de problemas.|
| X-Azure-RequestChain |  X-Azure-RequestChain: hops=1 </br> Um cabeçalho que usa a porta da frente para detectar loops de solicitação e os usuários não devem usar uma dependência nele. |
| X-Forwarded-For | X-Forwarded-For: 127.0.0.1 </br> O campo de cabeçalho HTTP X-Forwarded-For (XFF) geralmente identifica o endereço IP de origem de um cliente conectado a um servidor web por meio de um balanceador de carga ou proxy HTTP. Se houver um cabeçalho XFF existente, porta da frente acrescenta o IP de soquete de cliente a ele ou adiciona o cabeçalho XFF com o IP de soquete de cliente. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> O campo de cabeçalho HTTP X-Forwarded-Host é um método comum usado para identificar o host original solicitado pelo cliente no cabeçalho da solicitação HTTP de Host. Isso ocorre porque o nome do host de porta de entrada pode ser diferente para o servidor de back-end que está tratando a solicitação. |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> O campo de cabeçalho HTTP X-Forwarded-Proto geralmente é usado para identificar o protocolo de origem de uma solicitação HTTP, porque a porta da frente, com base na configuração, pode se comunicar com o back-end usando HTTPS. Isso é verdadeiro mesmo se a solicitação para o proxy reverso é HTTP. |

## <a name="front-door-service-to-client"></a>Serviço de porta da frente para cliente

Quaisquer cabeçalhos enviados para a porta da frente do back-end também são passados para o cliente. Estes são os cabeçalhos enviados da porta da frente aos clientes.

| Cabeçalho  | Exemplo |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Esse é uma cadeia de caracteres de referência exclusiva que identifica uma solicitação atendida pelo Front Door. Isso é essencial para solucionar problemas de como ele é usado para pesquisar os logs de acesso.|

## <a name="next-steps"></a>Próximas etapas

- [Criar uma porta da frente](quickstart-create-front-door.md)
- [Como funciona a porta da frente](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png