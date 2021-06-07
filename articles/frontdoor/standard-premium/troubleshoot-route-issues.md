---
title: Solucionar problemas de configuração padrão/Premium do Azure front door
description: Neste tutorial, você aprenderá a solucionar alguns dos problemas comuns que podem ser enfrentados para a instância Standard/Premium da porta de front-end do Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 4690a513494d794377ee0c2e8cfb101e8fd66a0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101098537"
---
# <a name="troubleshooting-common-routing-problems-with-azure-front-door-standardpremium"></a>Solucionando problemas comuns de roteamento com o Azure front door Standard/Premium

Este artigo descreve como solucionar problemas comuns de roteamento que você pode enfrentar para sua configuração de porta frontal do Azure.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 resposta da porta frontal do Azure após alguns segundos

### <a name="symptom"></a>Sintoma

* As solicitações regulares enviadas ao seu back-end sem passar pela porta frontal do Azure têm sucesso. Passar por meio do Azure front door resulta em respostas de erro 503.
* A falha da porta frontal do Azure normalmente é mostrada após cerca de 30 segundos.

### <a name="cause"></a>Causa

A causa desse problema pode ser uma das duas coisas:
 
* Sua origem está demorando mais do que o tempo limite configurado (o padrão é 30 segundos) para receber a solicitação da porta frontal do Azure.
* O tempo necessário para enviar uma resposta para a solicitação da porta de frente do Azure está demorando mais do que o valor de tempo limite. 

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

* Envie a solicitação ao seu back-end diretamente (sem passar pela porta frontal do Azure). Veja quanto tempo o back-end geralmente leva para responder.
* Envie a solicitação por meio da porta frontal do Azure e veja se você está recebendo qualquer resposta de 503. Caso contrário, o problema pode não ser um problema de tempo limite. Entre em contato com o suporte.
* Se passar pela porta de frente do Azure resultar em um código de resposta de erro 503, configure o `sendReceiveTimeout` campo para a porta frontal do Azure. Você pode estender o tempo limite padrão de até 4 minutos (240 segundos). A configuração está sob `Endpoint Setting` e é chamada `Origin response timeout` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>As solicitações enviadas para o domínio personalizado retornam um código de status 400

### <a name="symptom"></a>Sintoma

* Você criou uma instância de porta frontal do Azure, mas uma solicitação ao host de domínio ou front-end está retornando um código de status HTTP 400.
* Você criou um mapeamento DNS para um domínio personalizado para o host de front-end que você configurou. No entanto, o envio de uma solicitação para o nome de host de domínio personalizado retorna um código de status HTTP 400. Ele não parece rotear para o back-end que você configurou.

### <a name="cause"></a>Causa

O problema ocorre se você não configurou uma regra de roteamento para o domínio personalizado que foi adicionado como o host de front-end. Uma regra de roteamento precisa ser adicionada explicitamente para esse host de front-end. Isso é verdadeiro mesmo que uma regra de roteamento já tenha sido configurada para o host de front-end no subdomínio da porta de entrada do Azure (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

Adicione uma regra de roteamento para o domínio personalizado para direcionar o tráfego para o grupo de origem selecionado.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>A porta frontal do Azure não redireciona HTTP para HTTPS

### <a name="symptom"></a>Sintoma

A porta frontal do Azure tem uma regra de roteamento que redireciona HTTP para HTTPS, mas o acesso ao domínio ainda mantém o HTTP como o protocolo.

### <a name="cause"></a>Causa

Esse comportamento pode acontecer se você não configurou as regras de roteamento corretamente para a porta frontal do Azure. Basicamente, a configuração atual não é específica e pode ter regras conflitantes.

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas


## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>A solicitação para o nome de host de front-end retorna um código de status 411

### <a name="symptom"></a>Sintoma

Você criou uma instância Standard/Premium da porta frontal do Azure e configurou um host de front-end, um grupo de origem com pelo menos uma origem e uma regra de roteamento que conecta o host de front-end ao grupo de origem. Seu conteúdo parece não estar disponível quando uma solicitação vai para o host de front-end configurado porque um código de status HTTP 411 é retornado.

As respostas a essas solicitações também podem conter uma página de erro HTML no corpo da resposta que inclui uma instrução explicativa. Por exemplo: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Causa

Há várias causas possíveis para esse sintoma. O motivo geral é que sua solicitação HTTP não é totalmente compatível com RFC. 

Um exemplo de não conformidade é uma `POST` solicitação enviada sem um `Content-Length` ou um `Transfer-Encoding` cabeçalho (por exemplo, usando `curl -X POST https://example-front-door.domain.com` ). Essa solicitação não atende aos requisitos definidos no [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). A porta frontal do Azure a bloquearia com uma resposta HTTP 411.

Esse comportamento é separado da funcionalidade WAF (firewall do aplicativo Web) da porta frontal do Azure. Atualmente, não há como desabilitar esse comportamento. Todas as solicitações HTTP devem atender aos requisitos, mesmo que a funcionalidade WAF não esteja em uso.

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

- Verifique se suas solicitações estão em conformidade com os requisitos definidos nos RFCs necessários.

- Anote qualquer corpo de mensagem HTML retornado em resposta à sua solicitação. Um corpo de mensagem geralmente explica exatamente *como* sua solicitação não está em conformidade.

## <a name="next-steps"></a>Próximas etapas

Saiba como [criar uma porta de front-Standard/Premium](create-front-door-portal.md).
