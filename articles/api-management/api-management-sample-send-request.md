---
title: Usando o serviço Gerenciamento de API para gerar solicitações HTTP
description: Saiba como usar políticas de solicitação e de resposta no Gerenciamento de API para chamar serviços externos de sua API
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 12/15/2016
ms.author: v-yiso
ms.date: 04/22/2019
ms.openlocfilehash: 2c4e5d0117f046343b140ef2b2c46c074c835075
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557898"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Uso dos serviços externos do serviço de Gerenciamento de API do Azure
As políticas disponíveis no serviço de Gerenciamento de API do Azure permitem uma ampla variedade de trabalhos úteis com base apenas na solicitação de entrada, na resposta de saída e em informações básicas de configuração. No entanto, a capacidade de interagir com serviços externos das políticas de Gerenciamento de API abre muitas outras oportunidades.

Você viu como interagir com o [serviço de Hub de Eventos do Azure para registro em log, monitoramento e análise](api-management-log-to-eventhub-sample.md). Este artigo demonstra políticas que permitem interagir com qualquer serviço externo baseado em HTTP. Essas políticas podem ser usadas para disparar eventos remotos ou para recuperar informações que são usadas para manipular a solicitação e resposta originais de alguma forma.

## <a name="send-one-way-request"></a>Send-One-Way-Request
Possivelmente, a interação externa mais simples é o estilo de solicitação "disparar e esquecer" que permite que um serviço externo seja notificado sobre algum tipo de evento importante. A política de fluxo de controle `choose` pode ser usada para detectar qualquer tipo de condição que você esteja interessado.  Se as condições forem atendidas, será possível fazer uma solicitação HTTP externa usando a política [send-one-way-request](/azure/api-management/api-management-advanced-policies#SendOneWayRequest). Isso inclui uma solicitação para um sistema de mensagens como Hipchat ou Slack, ou uma API de email como SendGrid ou MailChimp, ou para incidentes de suporte críticos, algo como o PagerDuty. Todos esses sistemas de mensagens possuem APIs HTTP simples que podem ser invocadas.

### <a name="alerting-with-slack"></a>Alertas com Slack
O exemplo a seguir demonstra como enviar uma mensagem a uma sala de bate-papo do Slack, se o código de status de resposta HTTP for maior ou igual a 500. Um erro de intervalo 500 indica um problema com a API de back-end que o cliente da API não pode resolver sozinho. Geralmente, isso requer algum tipo de intervenção na parte de Gerenciamento de API.  

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>
```

O Slack tem a noção de ganchos de entrada da Web. Ao configurar um webhook de entrada, o Slack gera uma URL especial, permitindo que você faça uma solicitação POST simples e passe uma mensagem para o canal do Slack. O corpo JSON que você cria é baseado em um formato definido pelo Slack.

![Gancho da Web do Slack](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>O método "disparar e esquecer" é o suficiente?
Há certas compensações ao usar um estilo de solicitação "disparar e esquecer". Se por algum motivo a solicitação falhar, então, a falha não será relatada. Nessa situação específica, a complexidade de ter um sistema de relatório de falhas secundário e o custo adicional de desempenho de ter que espera por uma resposta não oferecem garantias. Para cenários nos quais é essencial verificar a resposta, a política [send-request](/azure/api-management/api-management-advanced-policies#SendRequest) é uma opção mais adequada.

## <a name="send-request"></a>send-request
A política `send-request` permite o uso de um serviço externo para executar funções complexas de processamento e retornar dados para o serviço de gerenciamento de API, que pode ser usado para um processamento adicional da política.

### <a name="authorizing-reference-tokens"></a>Autorizando tokens de referência
Uma função importante do Gerenciamento de API é proteger os recursos de back-end. Se o servidor de autorização usado pela sua API criar [tokens JWT](https://jwt.io/) como parte de seu fluxo do OAuth2, assim como faz o [Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), você poderá usar a política `validate-jwt` para verificar a validade do token. Alguns servidores de autorização criam algo chamado [tokens de referência](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) que não podem ser verificados sem a realização de uma chamada para o servidor de autorização.

### <a name="standardized-introspection"></a>Introspecção padronizada
No passado, não havia uma forma padronizada de verificar um token de referência com um servidor de autorização. No entanto, um padrão proposto recentemente, o [RFC 7662](https://tools.ietf.org/html/rfc7662) , foi publicado pela IETF definindo como um servidor de recursos pode verificar a validade de um token.

### <a name="extracting-the-token"></a>Extração do token
A primeira etapa é extrair o token do cabeçalho de Autorização. O valor do cabeçalho deverá ser formatado com o esquema de autorização `Bearer`, um espaço simples e, em seguida, o token de autorização de acordo com a [RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1). Infelizmente, há casos nos quais o esquema de autorização é omitido. Para levar isso em conta durante a análise, o Gerenciamento de API divide o valor do cabeçalho em um espaço e seleciona a última cadeia de caracteres da matriz de cadeias de caracteres retornada. Isso fornece uma solução alternativa para cabeçalhos de autorização formatados incorretamente.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Realização da solicitação de validação
Quando o Gerenciamento de API tiver o token de autorização, o Gerenciamento de API poderá fazer a solicitação para validar o token. A RFC 7662 chama esse processo de introspecção e exige que você aplique `POST` a um formulário HTML para o recurso de introspecção. O formulário HTML deve conter pelo menos um par de chave/valor com a chave `token`. Essa solicitação ao servidor de autorização também deve ser autenticada para garantir que clientes mal-intencionados não possam vasculhar os tokens válidos.

```xml
<send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
  <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
  <set-method>POST</set-method>
  <set-header name="Authorization" exists-action="override">
    <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
  </set-header>
  <set-header name="Content-Type" exists-action="override">
    <value>application/x-www-form-urlencoded</value>
  </set-header>
  <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
</send-request>
```

### <a name="checking-the-response"></a>Verificação da resposta
O atributo `response-variable-name` é usado para dar acesso à resposta retornada. O nome definido nessa propriedade pode ser usado como uma chave para o dicionário `context.Variables` a fim de acessar o objeto `IResponse`.

A partir do objeto de resposta, é possível recuperar o corpo e a RFC 7622 informa Gerenciamento de API que a resposta deve ser um objeto JSON e conter pelo menos uma propriedade chamada `active` que seja um valor booliano. Quando `active` é verdadeiro, o token é considerado válido.

### <a name="reporting-failure"></a>Indicação de falha
É possível usar `<choose>` para detectar se o token é inválido e, em caso afirmativo, retornar uma resposta 401.

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>
```

De acordo com a [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) que descreve como os tokens `bearer` devem ser utilizados, o Gerenciamento de API também retorna um cabeçalho `WWW-Authenticate` com a resposta 401. O WWW-Authenticate tem como intenção instruir um cliente sobre como construir uma solicitação devidamente autorizada. Devido à ampla variedade de abordagens possíveis com a estrutura OAuth2, é difícil comunicar todas as informações necessárias. Felizmente, há esforços sendo realizados para ajudar os [clientes a descobrirem como autorizar corretamente as solicitações para um servidor de recursos](https://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Solução final
No final, você obtém a seguinte política:

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
          <!-- Check active property in response -->
          <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
              <!-- Return 401 Unauthorized with http-problem payload -->
              <return-response response-variable-name="existing response variable">
                  <set-status code="401" reason="Unauthorized" />
                  <set-header name="WWW-Authenticate" exists-action="override">
                      <value>Bearer error="invalid_token"</value>
                  </set-header>
              </return-response>
          </when>
      </choose>
  <base />
</inbound>
```

Este é apenas um dos muitos exemplos de como a política `send-request` pode ser usada para integrar serviços externos úteis ao processo de solicitações e respostas que fluem pelo serviço Gerenciamento de API.

## <a name="response-composition"></a>Composição da resposta
A política `send-request` pode ser usada para melhorar a uma solicitação primária para um sistema back-end, como visto no exemplo anterior, ou pode ser usada como uma substituição completa para a chamada back-end. Usando essa técnica, é possível criar facilmente recursos compostos que são agregados de vários sistemas diferentes.

### <a name="building-a-dashboard"></a>Criando um painel
Às vezes, você quer expor as informações existentes em vários sistemas de back-end, por exemplo, para gerar um painel. Os KPIs vêm de todos os back-ends diferentes, mas convém não fornecer acesso direto a eles, e seria bom se todas as informações pudessem ser recuperadas em uma única solicitação. Talvez algumas informações de back-end precisem de um pouco de organização e uma pequena limpeza primeiro! Ser capaz de armazenar em cache o recurso composto é útil para reduzir a carga back-end, pois você sabe que os usuários têm o hábito de pressionar sem parar a tecla F5 para ver se suas métricas com baixo desempenho mudam.    

### <a name="faking-the-resource"></a>Falsificando o recurso
A primeira etapa para a construção do recurso do painel é configurar uma nova operação no Portal do Azure. Essa é uma operação de espaço reservado usada para configurar uma política de composição para compilar o recurso dinâmico.

![Operação de painel](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Fazendo as solicitações
Quando a operação for criada, você poderá configurar uma política especificamente para essa operação. 

![Operação de painel](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

A primeira etapa é extrair os parâmetros de consulta da solicitação de entrada, para que você possa encaminhá-los ao back-end. Nesse exemplo, o painel exibe informações baseadas em um período de tempo e, portanto, tem um parâmetro `fromDate` e `toDate`. É possível usar `set-variable` para extrair as informações da URL da solicitação.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Assim que tiver essas informações, você poderá fazer solicitações a todos os sistemas back-end. Cada solicitação constrói uma nova URL com as informações de parâmetro, chama seu respectivo servidor e armazena a resposta em uma variável de contexto.

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Essas solicitações executam em sequência, o que não é ideal. 

### <a name="responding"></a>Respondendo
Para construir a resposta composta, é possível usar a política [return-response](/azure/api-management/api-management-advanced-policies#ReturnResponse). O elemento `set-body` pode usar uma expressão para construir um novo `JObject` com todas as representações de componente incorporadas como propriedades.

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

A política completa tem a seguinte aparência:

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

Na configuração da operação de espaço reservado, você pode configurar o recurso do painel para ser armazenado em cache durante pelo menos uma hora. 

## <a name="summary"></a>Resumo
O serviço de Gerenciamento de API do Azure fornece políticas flexíveis que podem ser aplicadas seletivamente ao tráfego HTTP e permite a composição de serviços back-end. Se você quiser aprimorar seu gateway de API com funções de alerta, verificação e recursos de validação, ou criar novos recursos compostos baseados em vários serviços back-end, a política `send-request` e as políticas relacionadas abrirão um mundo de oportunidades.


