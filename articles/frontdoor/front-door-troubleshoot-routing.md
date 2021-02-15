---
title: Solucionar problemas de configuração da porta frontal do Azure
description: Neste tutorial, você aprenderá a solucionar automaticamente alguns dos problemas comuns que você pode enfrentar para sua instância de porta de front do Azure.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 15cdcefe628a392704e650b560243e2f6a134ec2
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629981"
---
# <a name="troubleshooting-common-routing-problems"></a>Solucionando problemas comuns de roteamento

Este artigo descreve como solucionar problemas comuns de roteamento que você pode enfrentar para sua configuração de porta frontal do Azure.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 resposta da porta frontal do Azure após alguns segundos

### <a name="symptom"></a>Sintoma

* As solicitações regulares enviadas ao seu back-end sem passar pela porta frontal do Azure têm sucesso. Passar por meio do Azure front door resulta em respostas de erro 503.
* A falha da porta frontal do Azure normalmente é mostrada após cerca de 30 segundos.

### <a name="cause"></a>Causa

A causa desse problema pode ser uma das duas coisas:
 
* O back-end está demorando mais do que o tempo limite configurado (o padrão é 30 segundos) para receber a solicitação da porta frontal do Azure.
* O tempo necessário para enviar uma resposta para a solicitação da porta de frente do Azure está demorando mais do que o valor de tempo limite. 

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

* Envie a solicitação ao seu back-end diretamente (sem passar pela porta frontal do Azure). Veja quanto tempo o back-end geralmente leva para responder.
* Envie a solicitação por meio da porta frontal do Azure e veja se você está recebendo qualquer resposta de 503. Caso contrário, o problema pode não ser um problema de tempo limite. Entre em contato com o suporte.
* Se passar pela porta de frente do Azure resultar em um código de resposta de erro 503, configure o `sendReceiveTimeout` campo para a porta frontal do Azure. Você pode estender o tempo limite padrão de até 4 minutos (240 segundos). A configuração está sob `backendPoolSettings` e é chamada `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>As solicitações enviadas para o domínio personalizado retornam um código de status 400

### <a name="symptom"></a>Sintoma

* Você criou uma instância de porta frontal do Azure, mas uma solicitação ao host de domínio ou front-end está retornando um código de status HTTP 400.
* Você criou um mapeamento DNS para um domínio personalizado para o host de front-end que você configurou. No entanto, o envio de uma solicitação para o nome de host de domínio personalizado retorna um código de status HTTP 400. Ele não parece rotear para o back-end que você configurou.

### <a name="cause"></a>Causa

O problema ocorre se você não configurou uma regra de roteamento para o domínio personalizado que foi adicionado como o host de front-end. Uma regra de roteamento precisa ser adicionada explicitamente para esse host de front-end. Isso é verdadeiro mesmo que uma regra de roteamento já tenha sido configurada para o host de front-end no subdomínio da porta de entrada do Azure (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

Adicione uma regra de roteamento para o domínio personalizado para direcionar o tráfego para o pool de back-end selecionado.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>A porta frontal do Azure não redireciona HTTP para HTTPS

### <a name="symptom"></a>Sintoma

A porta frontal do Azure tem uma regra de roteamento que redireciona HTTP para HTTPS, mas o acesso ao domínio ainda mantém o HTTP como o protocolo.

### <a name="cause"></a>Causa

Esse comportamento pode acontecer se você não configurou as regras de roteamento corretamente para a porta frontal do Azure. Basicamente, a configuração atual não é específica e pode ter regras conflitantes.

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>A solicitação para um nome de host de front-end retorna um código de status 404

### <a name="symptom"></a>Sintoma

Você criou uma instância de porta frontal do Azure Configurando um host de front-end, um pool de back-ends com pelo menos um back-end e uma regra de roteamento que conecta o host de front-end ao pool de back-ends. Seu conteúdo não está disponível quando você faz uma solicitação para o host de front-end configurado. Como resultado, a solicitação retorna um código de status HTTP 404.

### <a name="cause"></a>Causa

Há várias causas possíveis para esse sintoma:

* O back-end não é um back-end voltado ao público e não é visível para a porta frontal do Azure.
* O back-end está configurado incorretamente, fazendo com que a porta frontal do Azure envie a solicitação errada. Em outras palavras, o back-end aceita apenas HTTP e você não permitiu o HTTPS. Portanto, a porta frontal do Azure está tentando encaminhar solicitações HTTPS.
* O back-end está rejeitando o cabeçalho de host que foi enviado com a solicitação ao back-end.
* A configuração para o back-end ainda não foi totalmente implantada.

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

* Verifique o tempo de implantação:
   * Certifique-se de ter aguardado pelo menos 10 minutos para que a configuração seja implantada.

* Verifique as configurações de back-end:
    * Vá para o pool de back-end para o qual a solicitação deve ser roteada. (Depende de como você tem a regra de roteamento configurada.) Verifique se o tipo de host back-end e o nome do host de back-end estão corretos Se o back-end for um host personalizado, verifique se você o escreveu corretamente. 

    * Verifique suas portas HTTP e HTTPS. Na maioria dos casos, 80 e 443 (respectivamente) estão corretos e nenhuma alteração é necessária. No entanto, há uma chance de que o back-end não seja configurado dessa maneira e esteja ouvindo em uma porta diferente.

    * Verifique o cabeçalho de host de back-end configurado para os back-ends para os quais o host front-end deve ser roteado. Na maioria dos casos, esse cabeçalho deve ser igual ao nome do host de back-end. No entanto, um valor incorreto poderá causar vários códigos de status HTTP 4xx se o back-end esperar algo diferente. Se você inserir o endereço IP do seu back-end, talvez seja necessário definir o cabeçalho de host de back-end para o nome de host do back-end.

* Verifique as configurações da regra de roteamento:
    * Vá para a regra de roteamento que deve rotear do nome de host front-end em questão para um pool de back-end. Verifique se os protocolos aceitos estão configurados corretamente quando a solicitação é encaminhada. O campo **protocolos aceitos** determina quais solicitações a porta frontal do Azure deve aceitar. O protocolo de encaminhamento determina qual protocolo a porta frontal do Azure deve usar para encaminhar a solicitação para o back-end.
      
      Por exemplo, se o back-end aceitar apenas solicitações HTTP, as seguintes configurações seriam válidas:
            
      * Os Protocolos aceitos são HTTP e HTTPS. O protocolo de encaminhamento é HTTP. Uma solicitação de correspondência não funcionará, pois HTTPS é um protocolo permitido. Se uma solicitação for enviada como HTTPS, a porta frontal do Azure tentará encaminhá-la usando HTTPS.

      * O protocolo aceito é HTTP. O protocolo de encaminhamento é uma solicitação de correspondência ou HTTP.
    - A **regravação de URL** é desabilitada por padrão. Esse campo só será usado se você quiser restringir o escopo de recursos hospedados por back-end que deseja disponibilizar. Quando esse campo estiver desabilitado, a porta frontal do Azure encaminhará o mesmo caminho de solicitação que receber. É possível configurar incorretamente esse campo. Assim, quando a porta frontal do Azure está solicitando um recurso do back-end que não está disponível, ele retornará um código de status HTTP 404.

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>A solicitação para o nome de host de front-end retorna um código de status 411

### <a name="symptom"></a>Sintoma

Você criou uma instância de porta frontal do Azure e configurou um host de front-end, um pool de back-ends com pelo menos um back-end e uma regra de roteamento que conecta o host de front-end ao pool de back-ends. Seu conteúdo parece não estar disponível quando uma solicitação vai para o host de front-end configurado porque um código de status HTTP 411 é retornado.

As respostas a essas solicitações também podem conter uma página de erro HTML no corpo da resposta que inclui uma instrução explicativa. Por exemplo: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Causa

Há várias causas possíveis para esse sintoma. O motivo geral é que sua solicitação HTTP não é totalmente compatível com RFC. 

Um exemplo de não conformidade é uma `POST` solicitação enviada sem um `Content-Length` ou um `Transfer-Encoding` cabeçalho (por exemplo, usando `curl -X POST https://example-front-door.domain.com` ). Essa solicitação não atende aos requisitos definidos no [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). A porta frontal do Azure a bloquearia com uma resposta HTTP 411.

Esse comportamento é separado da funcionalidade WAF (firewall do aplicativo Web) da porta frontal do Azure. Atualmente, não há como desabilitar esse comportamento. Todas as solicitações HTTP devem atender aos requisitos, mesmo se a funcionalidade WAF não estiver em uso.

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

- Verifique se suas solicitações estão em conformidade com os requisitos definidos nos RFCs necessários.

- Anote qualquer corpo de mensagem HTML retornado em resposta à sua solicitação. Um corpo de mensagem geralmente explica exatamente *como* sua solicitação não está em conformidade.
