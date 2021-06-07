---
title: Suporte de protocolo para cabeçalhos HTTP na porta frontal do Azure | Microsoft Docs
description: Este artigo descreve os protocolos de cabeçalho HTTP aos quais a porta frontal dá suporte.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2020
ms.author: duau
ms.openlocfilehash: 7f40b48473c04238d504288307039948fcacf90a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97511137"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Suporte de protocolo para cabeçalhos HTTP na porta frontal do Azure
Este artigo descreve o protocolo que a porta frontal dá suporte com partes do caminho de chamada (consulte a imagem). As seções a seguir fornecem mais informações sobre os cabeçalhos HTTP com suporte da porta frontal.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Protocolo de cabeçalhos HTTP de porta frontal do Azure":::

>[!IMPORTANT]
>A porta frontal não certifica nenhum cabeçalho HTTP que não esteja documentado aqui.

## <a name="client-to-front-door"></a>Cliente para o Front Door
A porta frontal aceita a maioria dos cabeçalhos para a solicitação de entrada sem modificá-los. Alguns cabeçalhos reservados são removidos da solicitação de entrada se forem enviados, incluindo cabeçalhos com o prefixo X-FD-*.

## <a name="front-door-to-backend"></a>Front Door para o back-end

A porta frontal inclui cabeçalhos para uma solicitação de entrada, a menos que sejam removidas devido a restrições. A porta frontal também adiciona os seguintes cabeçalhos:

| parâmetro  | Exemplo e descrição |
| ------------- | ------------- |
| Através de |  *Via: 1,1 Azure* </br> Porta frontal adiciona a versão HTTP do cliente seguida pelo *Azure* como o valor para o cabeçalho via. Esse cabeçalho indica a versão HTTP do cliente e essa porta frontal era um destinatário intermediário da solicitação entre o cliente e o back-end.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> Representa o endereço IP do cliente associado à solicitação que está sendo processada. Por exemplo, uma solicitação proveniente de um proxy pode adicionar o cabeçalho X-Forwardd-for para indicar o endereço IP do chamador original. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Representa o endereço IP do soquete associado à conexão TCP da qual a solicitação atual foi originada. O endereço IP do cliente de uma solicitação pode não ser igual ao seu endereço IP de soquete porque pode ser substituído arbitrariamente por um usuário.|
| X-Azure-ref | *X-Azure-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Uma cadeia de caracteres de referência exclusiva que identifica uma solicitação servida pela porta da frente. Ele é usado para pesquisar logs de acesso e crítico para solução de problemas.|
| X-Azure-RequestChain | *X-Azure-RequestChain: saltos = 1* </br> Um cabeçalho que o front door usa para detectar loops de solicitação e os usuários não devem assumir uma dependência dele. |
| X-Azure-FDID | *X-Azure-FDID: 55ce4ed1-4B06-4bf1-B40E-4638452104da* <br/> Uma cadeia de caracteres de referência que identifica a solicitação provém de um recurso de porta frontal específico. O valor pode ser visto no portal do Azure ou recuperado usando a API de gerenciamento. Você pode usar esse cabeçalho em combinação com ACLs de IP para bloquear seu ponto de extremidade para aceitar somente solicitações de um recurso de porta frontal específico. Consulte as perguntas frequentes para obter [mais detalhes](front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door) |
| X-Forwarded-For | *X-Forwarded-for: 127.0.0.1* </br> O campo de cabeçalho HTTP X-Forwarded-for (XFF) geralmente identifica o endereço IP de origem de um cliente que se conecta a um servidor Web por meio de um proxy HTTP ou balanceador de carga. Se houver um cabeçalho XFF existente, a porta frontal acrescentará o IP de soquete do cliente a ele ou adicionará o cabeçalho XFF com o IP de soquete do cliente. |
| X-Forwarded-Host | *X-Forwarded-host: contoso.azurefd.net* </br> O campo de cabeçalho HTTP do host X encaminhado é um método comum usado para identificar o host original solicitado pelo cliente no cabeçalho de solicitação HTTP do host. Isso ocorre porque o nome do host da porta frontal pode ser diferente para o servidor de back-end que manipula a solicitação. |
| X-Forwarded-Proto | *X-encaminhar-proto: http* </br> O campo de cabeçalho HTTP de proto X encaminhadas é usado frequentemente para identificar o protocolo de origem de uma solicitação HTTP. A porta frontal com base na configuração pode se comunicar com o back-end usando HTTPS. Isso é verdadeiro mesmo que a solicitação para o proxy reverso seja HTTP. |
| X-FD-HealthProbe | O campo de cabeçalho HTTP X-FD-HealthProbe é usado para identificar a investigação de integridade da porta frontal. Se esse cabeçalho for definido como 1, a solicitação será investigação de integridade. Você pode usar quando deseja restringir o acesso de uma porta de frente específica com o campo de cabeçalho de host X/encaminhado. |
| X-Azure-FDID | *Cabeçalho X-Azure-FDID: 437c82cd-360A-4A54-94c3-5ff707647783* </br> Este campo contém frontdoorID que pode ser usado para identificar em qual porta frontal a solicitação de entrada é. Esse campo é preenchido pelo serviço de porta frontal. | 

## <a name="front-door-to-client"></a>Front Door para o cliente

Todos os cabeçalhos enviados para a porta de front-end também são passados para o cliente. Os cabeçalhos a seguir são enviados da porta frontal para os clientes.

| parâmetro  | Exemplo e descrição |
| ------------- | ------------- |
| X-Azure-ref |  *X-Azure-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Essa é uma cadeia de caracteres de referência exclusiva que identifica uma solicitação servida pela porta da frente, que é essencial para a solução de problemas, pois é usada para pesquisar logs de acesso.|
| Cache X | *X-cache: TCP_HIT* </br> Esse cabeçalho descreve o status do cache da solicitação, que permite que você identifique se o conteúdo da resposta está sendo servido do cache da porta frontal. |

Você precisa enviar o cabeçalho de solicitação "X-Azure-DebugInfo: 1" para habilitar os seguintes cabeçalhos de resposta opcionais.

| parâmetro  | Exemplo e descrição |
| ------------- | ------------- |
| X-Azure-OriginStatusCode |  *X-Azure-OriginStatusCode: 503* </br> Esse cabeçalho contém o código de status HTTP retornado pelo back-end. Usando esse cabeçalho, você pode identificar o código de status HTTP retornado pelo aplicativo em execução no back-end sem passar pelos logs de back-end. Esse código de status pode ser diferente do código de status HTTP na resposta enviada ao cliente pela porta frontal. Esse cabeçalho permite que você determine se o back-end está com comportamento inadequado ou se o problema é com o serviço de porta frontal. |
| X-Azure-InternalError | Esse cabeçalho conterá o código de erro que a porta frontal entra ao processar a solicitação. Esse erro indica que o problema é interno ao serviço/infraestrutura da porta de front-end. Relatar problema para dar suporte ao.  |
| X-Azure-ExternalError | *X-Azure-ExternalError: 0x830c1011, a autoridade de certificação não é familiar.* </br> Esse cabeçalho mostra o código de erro que os servidores de porta frontal chegam, ao mesmo tempo em que estabelecem conectividade com o servidor de back-end para processar uma solicitação. Esse cabeçalho ajudará a identificar problemas na conexão entre a porta da frente e o aplicativo de back-end. Esse cabeçalho incluirá uma mensagem de erro detalhada para ajudá-lo a identificar problemas de conectividade para seu back-end (por exemplo, resolução DNS, certificado inválido e assim por diante). |

## <a name="next-steps"></a>Próximas etapas

- [Criar um Front Door](quickstart-create-front-door.md)
- [Como funciona a porta frontal](front-door-routing-architecture.md)
