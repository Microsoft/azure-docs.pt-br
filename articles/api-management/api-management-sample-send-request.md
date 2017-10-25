---
title: "Usando o serviço Gerenciamento de API para gerar solicitações HTTP"
description: "Saiba como usar políticas de solicitação e de resposta no Gerenciamento de API para chamar serviços externos de sua API"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 6b7f1268ea4893307713931e7288f5d38c5ee080
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Uso dos serviços externos do serviço de Gerenciamento de API do Azure
As políticas disponíveis no serviço de Gerenciamento de API do Azure permitem uma ampla variedade de trabalhos úteis com base apenas na solicitação de entrada, na resposta de saída e em informações básicas de configuração. No entanto, a capacidade de interagir com serviços externos das políticas de Gerenciamento de API abre muitas outras oportunidades.

Vimos anteriormente como podemos interagir com o [serviço Hub de Eventos do Azure para registro em log, monitoramento e análise](api-management-log-to-eventhub-sample.md). Neste artigo, demonstraremos as políticas que permitem a interação com qualquer serviço externo baseado em HTTP. Essas políticas podem ser usadas para disparar eventos remotos ou para recuperar informações que serão usadas para manipular a solicitação e resposta originais de alguma forma.

## <a name="send-one-way-request"></a>Send-One-Way-Request
Possivelmente, a interação externa mais simples é o estilo de solicitação "disparar e esquecer" que permite que um serviço externo seja notificado sobre algum tipo de evento importante. Podemos usar a política de fluxo de controle `choose` para detectar qualquer tipo de condição de nosso interesse; se a condição for atendida, poderemos fazer uma solicitação HTTP externa usando a política [send-one-way-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) . Isso inclui uma solicitação para um sistema de mensagens como Hipchat ou Slack, ou uma API de email como SendGrid ou MailChimp, ou para incidentes de suporte críticos, algo como o PagerDuty. Todos esses sistemas de mensagens têm APIs HTTP simples que podemos facilmente invocar.

### <a name="alerting-with-slack"></a>Alertas com Slack
O exemplo a seguir demonstra como enviar uma mensagem a uma sala de bate-papo do Slack, se o código de status de resposta HTTP for maior ou igual a 500. Um erro de intervalo 500 indica um problema com nossa API de back-end que o cliente de nossa API não consegue resolver sozinho. Isso geralmente exige algum tipo de intervenção de nossa parte.  

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

O Slack tem a noção de ganchos de entrada da Web. Ao configurar um gancho de entrada da Web, o Slack gera uma URL especial que permite a realização de uma solicitação POST simples e a transmissão de uma mensagem para o canal do Slack. O corpo JSON que criamos se baseia em um formato definido pelo Slack.

![Gancho da Web do Slack](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>O método "disparar e esquecer" é o suficiente?
Há certas compensações ao usar um estilo de solicitação "disparar e esquecer". Se, por algum motivo, a solicitação falhar, a falha não será registrada. Nessa situação específica, a complexidade de ter um sistema de relatório de falhas secundário e o custo adicional de desempenho de ter que espera por uma resposta não oferecem garantias. Para cenários nos quais é essencial verificar a resposta, a política [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) é uma opção mais adequada.

## <a name="send-request"></a>send-request
A política `send-request` permite o uso de um serviço externo para executar funções complexas de processamento e retornar dados para o serviço de gerenciamento de API, que pode ser usado para um processamento adicional da política.

### <a name="authorizing-reference-tokens"></a>Autorizando tokens de referência
Uma função importante do Gerenciamento de API é proteger os recursos de back-end. Se o servidor de autorização usado pela sua API criar [tokens JWT](http://jwt.io/) como parte de seu fluxo do OAuth2, assim como faz o [Azure Active Directory](../active-directory/active-directory-aadconnect.md), você poderá usar a política `validate-jwt` para verificar a validade do token. No entanto, alguns servidores de autorização criam algo chamado [tokens de referência](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) que não podem ser verificados sem a realização de uma chamada para o servidor de autorização.

### <a name="standardized-introspection"></a>Introspecção padronizada
No passado, não havia uma forma padronizada de verificar um token de referência com um servidor de autorização. No entanto, um padrão proposto recentemente, o [RFC 7662](https://tools.ietf.org/html/rfc7662) , foi publicado pela IETF definindo como um servidor de recursos pode verificar a validade de um token.

### <a name="extracting-the-token"></a>Extração do token
A primeira etapa é extrair o token do cabeçalho de Autorização. O valor do cabeçalho deve ser formatado com o esquema de autorização `Bearer` , um espaço único e o token de autorização, conforme a [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1). Infelizmente, há casos nos quais o esquema de autorização é omitido. Para levar isso em conta durante a análise, dividimos o valor do cabeçalho em um espaço e selecionamos a última cadeia de caracteres da matriz de cadeias de caracteres retornada. Isso fornece uma solução alternativa para cabeçalhos de autorização formatados incorretamente.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Realização da solicitação de validação
Após a obtenção do token de autorização, podemos fazer a solicitação para validar o token. A RFC 7662 chama esse processo de introspecção e exige que você aplique `POST` a um formulário HTML para o recurso de introspecção. O formulário HTML deve conter pelo menos um par de chave/valor com a chave `token`. Essa solicitação para o servidor de autorização também deve ser autenticada a fim de garantir que os clientes mal-intencionados não possam vasculhar os tokens válidos.

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

Do objeto de resposta, podemos recuperar o corpo e a RFC 7622 nos informa de que a resposta deve ser um objeto JSON e deve conter pelo menos uma propriedade chamada `active` , que é um valor booliano. Quando `active` é verdadeiro, o token é considerado válido.

### <a name="reporting-failure"></a>Indicação de falha
Usamos uma política `<choose>` para detectar se o token é inválido e, em caso positivo, retornar uma resposta 401.

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

Conforme a [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3), que descreve como os tokens `bearer` devem ser usados, também retornamos um cabeçalho `WWW-Authenticate` com a resposta 401. O WWW-Authenticate tem como intenção instruir um cliente sobre como construir uma solicitação devidamente autorizada. Devido à ampla variedade de abordagens possíveis com a estrutura OAuth2, é difícil comunicar todas as informações necessárias. Felizmente, há esforços sendo realizados para ajudar os [clientes a descobrirem como autorizar corretamente as solicitações para um servidor de recursos](http://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Solução final
Juntando todas as peças, temos a seguinte política:

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
A política `send-request` pode ser usada para melhorar a uma solicitação primária para um sistema back-end, como vimos no exemplo anterior, ou pode ser usada como uma substituição completa para a chamada back-end. Com essa técnica podemos criar facilmente recursos de composição agregados de vários sistemas diferentes.

### <a name="building-a-dashboard"></a>Criando um painel
Às vezes, você quer expor as informações existentes em vários sistemas de back-end, por exemplo, para gerar um painel. Os KPIs vêm de todos os back-ends diferentes, mas convém não fornecer acesso direto a eles, e seria bom se todas as informações pudessem ser recuperadas em uma única solicitação. Talvez algumas informações de back-end precisem de um pouco de organização e uma pequena limpeza primeiro! Ser capaz de armazenar em cache o recurso composto é útil para reduzir a carga back-end, pois você sabe que os usuários têm o hábito de pressionar sem parar a tecla F5 para ver se suas métricas com baixo desempenho mudam.    

### <a name="faking-the-resource"></a>Falsificando o recurso
A primeira etapa da construção de nosso recurso de painel é configurar uma nova operação no portal de editor do Gerenciamento de API. Essa será uma operação de espaço reservado usada para configurar nossa política de composição a fim de criar nosso recurso dinâmico.

![Operação de painel](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Fazendo as solicitações
Após a criação da operação `dashboard` , podemos configurar uma política especificamente para essa operação. 

![Operação de painel](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

A primeira etapa é extrair parâmetros de consulta da solicitação de entrada, para que possamos encaminhá-los ao nosso back-end. Neste exemplo, nosso painel está mostrando informações baseadas em um período; portanto, ele apresenta os parâmetros `fromDate` e `toDate`. Podemos usar a política `set-variable` para extrair as informações da URL de solicitação.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Assim que tivermos essas informações poderemos fazer solicitações para todos os sistemas back-end. Cada solicitação constrói uma nova URL com as informações de parâmetro, chama seu respectivo servidor e armazena a resposta em uma variável de contexto.

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
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Essas solicitações serão executadas em sequência, o que não é ideal. Em uma versão futura apresentaremos uma nova política chamada `wait` , que permitirá a execução em paralelo de todas essas solicitações.

### <a name="responding"></a>Respondendo
Para construir a resposta composta, podemos usar a política [return-response](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) . O elemento `set-body` pode usar uma expressão para construir um novo `JObject` com todas as representações de componente incorporadas como propriedades.

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
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
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

Na configuração da operação de espaço reservado podemos configurar o recurso de painel para ser armazenado em cache durante pelo menos uma hora, pois entendemos que a natureza dos dados significa que mesmo se estiver uma hora desatualizado, ainda será suficientemente eficaz para transmitir as informações valiosas aos usuários.

## <a name="summary"></a>Resumo
O serviço de Gerenciamento de API do Azure fornece políticas flexíveis que podem ser aplicadas seletivamente ao tráfego HTTP e permite a composição de serviços back-end. Se você quiser aprimorar seu gateway de API com funções de alerta, verificação e recursos de validação, ou criar novos recursos compostos baseados em vários serviços back-end, a política `send-request` e as políticas relacionadas abrirão um mundo de oportunidades.

## <a name="watch-a-video-overview-of-these-policies"></a>Assista a uma visão geral dessas políticas em vídeo
Para obter mais informações sobre as políticas [send-one-way-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest), [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) e [return-response](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) abordadas neste artigo, assista ao vídeo a seguir.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Send-Request-and-Return-Response-Policies/player]
> 
> 

