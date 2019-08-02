---
title: Políticas de transformação de Gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas de transformação disponíveis para uso no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: b55502bbc24868b6d8b0352f581bbf4adc81e53a
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442242"
---
# <a name="api-management-transformation-policies"></a>Políticas de transformação de Gerenciamento de API
Este tópico fornece uma referência para as políticas de Gerenciamento de API a seguir. Para obter mais informações sobre como adicionar e configurar políticas, consulte [Políticas de Gerenciamento de API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="TransformationPolicies"></a> Políticas de transformação

-   [Converter JSON para XML](api-management-transformation-policies.md#ConvertJSONtoXML) - Converte o corpo da solicitação ou da resposta de JSON para XML.

-   [Converter XML para JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - Converte o corpo da solicitação ou da resposta de XML para JSON.

-   [Localizar e substituir cadeia no corpo](api-management-transformation-policies.md#Findandreplacestringinbody) - Encontra uma subcadeia de uma solicitação ou resposta e a substitui por outra subcadeia.

-   [Mascarar URLs no conteúdo](api-management-transformation-policies.md#MaskURLSContent) – Reescreve (mascara) os links no corpo da resposta, para que eles apontem para o link equivalente por meio do gateway.

-   [Definir o serviço de back-end](api-management-transformation-policies.md#SetBackendService) - Altera o serviço de back-end para uma solicitação de entrada.

-   [Definir corpo](api-management-transformation-policies.md#SetBody) - Define o corpo da mensagem para solicitações de entrada e saída.

-   [Definir cabeçalho HTTP](api-management-transformation-policies.md#SetHTTPheader) - Atribui um valor a uma resposta e/ou cabeçalho de resposta existente ou adiciona uma nova resposta e/ou cabeçalho de resposta.

-   [Definir parâmetro de cadeia de consulta](api-management-transformation-policies.md#SetQueryStringParameter) - Adiciona, substitui o valor ou exclui parâmetros de cadeias de consulta de solicitação.

-   [Reescrever URL](api-management-transformation-policies.md#RewriteURL) - Converte a URL de uma solicitação de sua forma pública em sua forma esperada pelo serviço Web.

-   [Transformar XML usando um XSLT](api-management-transformation-policies.md#XSLTransform) – aplica uma transformação XSL para XML no corpo da solicitação ou resposta.

##  <a name="ConvertJSONtoXML"></a> Converter JSON para XML
 A política `json-to-xml` converte o corpo da solicitação ou da resposta de JSON para XML.

### <a name="policy-statement"></a>Declaração de política

```xml
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false" parse-date="true | false"/>
```

### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <json-to-xml apply="always" consider-accept-header="false" parse-date="false"/>
    </outbound>
</policies>
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|json-to-xml|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Padrão|
|----------|-----------------|--------------|-------------|
|aplicar|O atributo deve ser definido como um dos valores a seguir.<br /><br /> –  always – sempre aplicar conversão.<br />–  content-type-json – converter somente se o cabeçalho Content-Type da resposta indica a presença de JSON.|Sim|N/D|
|consider-accept-header|O atributo deve ser definido como um dos valores a seguir.<br /><br /> –  true – aplica conversão se JSON é solicitado no cabeçalho Accept da solicitação.<br />–  false – sempre aplicar conversão.|Não|true|
|parse-date|Quando definido como `false`, os valores de data são simplesmente copiados durante a transformação|Não|true|

### <a name="usage"></a>Uso
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.

-   **Seções de política:** de entrada, de saída, em caso de erro

-   **Escopos da política:** todos os escopos

##  <a name="ConvertXMLtoJSON"></a> Converter XML para JSON
 A política `xml-to-json` converte o corpo da solicitação ou da resposta de XML para JSON. Esta política pode ser usada para modernizar APIs baseadas em serviços Web de back-end somente XML.

### <a name="policy-statement"></a>Declaração de política

```xml
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>
```

### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|xml-to-json|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Padrão|
|----------|-----------------|--------------|-------------|
|kind|O atributo deve ser definido como um dos valores a seguir.<br /><br /> – javascript-friendly – o JSON convertido tem um formato amigável para desenvolvedores de JavaScript.<br />– direct – o JSON convertido reflete a estrutura do documento XML original.|Sim|N/D|
|aplicar|O atributo deve ser definido como um dos valores a seguir.<br /><br /> – always – converter sempre.<br />–  content-type-xml – converter somente se o cabeçalho Content-Type da resposta indica a presença de XML.|Sim|N/D|
|consider-accept-header|O atributo deve ser definido como um dos valores a seguir.<br /><br /> –  true – aplica conversão se XML é solicitado no cabeçalho Accept da solicitação.<br />–  false – sempre aplicar conversão.|Não|true|

### <a name="usage"></a>Uso
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.

-   **Seções de política:** de entrada, de saída, em caso de erro

-   **Escopos da política:** todos os escopos

##  <a name="Findandreplacestringinbody"></a> Localizar e substituir cadeia de caracteres no corpo
 A política `find-and-replace` Encontra uma subcadeia de caracteres de uma solicitação ou resposta e a substitui por outra subcadeia de caracteres.

### <a name="policy-statement"></a>Declaração de política

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Exemplo

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|find-and-replace|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Padrão|
|----------|-----------------|--------------|-------------|
|from|A cadeia a ser pesquisada.|Sim|N/D|
|para|A cadeia de caracteres de substituição. Especifique uma cadeia de substituição de comprimento zero para remover a cadeia de caracteres de pesquisa.|Sim|N/D|

### <a name="usage"></a>Uso
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.

-   **Seções da política:** entrada, saída, back-end, em caso de erro

-   **Escopos da política:** todos os escopos

##  <a name="MaskURLSContent"></a> Mascarar URLs no conteúdo
 A política `redirect-content-urls` reescreve (mascara) os links no corpo da resposta para que eles apontem para o link equivalente por meio do gateway. Use na seção de saída para regravar links de corpo da resposta para que apontem para o gateway. Use na seção de entrada para obter um efeito oposto.

> [!NOTE]
>  Essa política não altera nenhum valor de cabeçalho como cabeçalhos `Location`. Para alterar valores de cabeçalho, use a política [set-header](api-management-transformation-policies.md#SetHTTPheader).

### <a name="policy-statement"></a>Declaração de política

```xml
<redirect-content-urls />
```

### <a name="example"></a>Exemplo

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|redirect-content-urls|Elemento raiz.|Sim|

### <a name="usage"></a>Uso
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.

-   **Seções de política:** de entrada, de saída

-   **Escopos da política:** todos os escopos

##  <a name="SetBackendService"></a> Definir o serviço de back-end
 Use a política `set-backend-service` para redirecionar uma solicitação de entrada para um back-end diferente daquele especificado nas configurações de API para essa operação. Essa política altera a URL base do serviço de back-end da solicitação de entrada para aquele especificado na política.

### <a name="policy-statement"></a>Declaração de política

```xml
<set-backend-service base-url="base URL of the backend service" />
```

ou

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> Entidades de back-end podem ser gerenciadas por meio da [API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) de gerenciamento e do [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).

### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <choose>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">
                <set-backend-service base-url="http://contoso.com/api/8.2/" />
            </when>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">
                <set-backend-service base-url="http://contoso.com/api/9.1/" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
Neste exemplo, a política de serviço de back-end do conjunto roteia solicitações com base no valor da versão passado na cadeia de consulta para um serviço de back-end diferente daquele especificado na API.

Inicialmente, a URL base do serviço de back-end é derivada das configurações de API. Assim, a URL da solicitação `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` se torna `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` em que `http://contoso.com/api/10.4/` é a URL do serviço de back-end especificada nas configurações de API.

Quando a instrução de política [<choose\>](api-management-advanced-policies.md#choose) é aplicada, a URL base do serviço de back-end pode se alterar novamente para `http://contoso.com/api/8.2` ou `http://contoso.com/api/9.1`, dependendo do valor do parâmetro de consulta de solicitação de versão. Por exemplo, se o valor for `"2013-15"`, a URL da solicitação final se tornará `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`.

Se ainda mais transformação da solicitação for desejada, outras [políticas de transformação](api-management-transformation-policies.md#TransformationPolicies) poderão ser usadas. Por exemplo, para remover o parâmetro de consulta de versão agora que a solicitação está sendo roteada para um back-end específico da versão, a política [Definir parâmetro de cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) pode ser usada para remover o atributo de versão agora redundante.

### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
Neste exemplo, a política encaminha a solicitação para um back-end de Service Fabric usando a cadeia de caracteres de consulta userId como a chave de partição e usando a réplica primária da partição.

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|set-backend-service|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Padrão|
|----------|-----------------|--------------|-------------|
|base-url|Nova URL base do serviço de back-end.|`base-url` Um ou `backend-id` deve estar presente.|N/D|
|backend-id|Identificador do back-end para o qual encaminhar. (As entidades de back-end são gerenciadas por meio da [API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) e do [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).)|`base-url` Um ou `backend-id` deve estar presente.|N/D|
|sf-partition-key|Aplicável somente quando o back-end é um serviço do Service Fabric e é especificado usando 'backend-id'. Usado para resolver uma partição específica do serviço de resolução de nome.|Não|N/D|
|sf-replica-type|Aplicável somente quando o back-end é um serviço do Service Fabric e é especificado usando 'backend-id'. Controla se a solicitação deve ir para a réplica primária ou secundária de uma partição. |Não|N/D|
|sf-resolve-condition|Aplicável somente quando o back-end é um serviço do Service Fabric. Condição que identifica se a chamada para o back-end do Service Fabric deve ser repetida com a nova resolução.|Não|N/D|
|sf-service-instance-name|Aplicável somente quando o back-end é um serviço do Service Fabric. Permite alterar as instâncias de serviço em tempo de execução. |Não|N/D|
|sf-listener-name|Aplicável somente quando o back-end é um serviço do Service Fabric e é especificado usando "backend-id". O Reliable Services do Service Fabric permite que você crie vários ouvintes em um serviço. Este atributo é usado para selecionar um ouvinte específico quando um Reliable Service de back-end tiver mais de um ouvinte. Se esse atributo não for especificado, o Gerenciamento de API tentará usar um ouvinte sem nome. Um ouvinte sem nome é comum para o Reliable Services, que têm apenas um ouvinte. |Não|N/D|

### <a name="usage"></a>Uso
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.

-   **Seções de política:** de entrada, back-end

-   **Escopos da política:** todos os escopos

##  <a name="SetBody"></a> Definir corpo
 Use a política `set-body` para definir o corpo da mensagem para solicitações de entrada e saída. Para acessar o corpo da mensagem, você pode usar a propriedade `context.Request.Body` ou a `context.Response.Body`, dependendo da seção em que a política está: de entrada ou de saída.

> [!IMPORTANT]
>  Observe que, por padrão, quando você acessa o corpo da mensagem usando `context.Request.Body` ou `context.Response.Body`, o corpo da mensagem original é perdido e deve ser definido por meio do retorno do corpo de volta na expressão. Para preservar o conteúdo do corpo, defina o parâmetro `preserveContent` para `true` ao acessar a mensagem. Se `preserveContent` é definido para `true` e um corpo diferente é retornado pela expressão, o corpo retornado é usado.
>
>  Observe as seguintes considerações ao usar a política `set-body`.
>
> - Se você estiver usando a política `set-body` para retornar um corpo novo ou atualizado, você não precisará definir `preserveContent` para `true` porque você estará fornecendo explicitamente o novo conteúdo do corpo.
>   -   Preservar o conteúdo de uma resposta no pipeline de entrada não faz sentido, porque ainda não há nenhuma resposta.
>   -   Preservar o conteúdo de uma solicitação no pipeline de saída não faz sentido porque nesse momento a solicitação já foi enviada para o back-end.
>   -   Se essa política é usada quando não há nenhum corpo da mensagem, por exemplo em um GET de entrada, uma exceção é lançada.

 Para obter mais informações, consulte as seções `context.Request.Body`, `context.Response.Body` e `IMessage` na tabela [Variável de contexto](api-management-policy-expressions.md#ContextVariables).

### <a name="policy-statement"></a>Declaração de política

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Exemplos

#### <a name="literal-text-example"></a>Exemplo de texto literal

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Exemplo de acesso ao corpo como uma cadeia de caracteres. Observe que estamos preservando o corpo da solicitação original, de forma que possamos acessá-lo mais tarde no pipeline.

```xml
<set-body>
@{ 
    string inBody = context.Request.Body.As<string>(preserveContent: true); 
    if (inBody[0] =='c') { 
        inBody[0] = 'm'; 
    } 
    return inBody; 
}
</set-body>
```

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Exemplo de acesso ao corpo como um JObject. Observe que, como não estamos reservando o corpo da solicitação original, acessá-lo mais tarde no pipeline resultará em uma exceção.

```xml
<set-body> 
@{ 
    JObject inBody = context.Request.Body.As<JObject>(); 
    if (inBody.attribute == <tag>) { 
        inBody[0] = 'm'; 
    } 
    return inBody.ToString(); 
} 
</set-body>

```

#### <a name="filter-response-based-on-product"></a>Resposta de filtro com base no produto
 Este exemplo mostra como executar a filtragem de conteúdo removendo elementos de dados da resposta recebida do serviço de back-end ao usar o produto `Starter`. Para obter uma demonstração de como configurar e usar essa política, assista ao vídeo [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) (Cloud Cover, episódio 3430: Mais recursos de Gerenciamento de API com Vlad Vinogradsky) e avance para 34min30s. Inicie em 31:50 para uma visão geral da [API da Previsão de Céu Escuro](https://developer.forecast.io/) usada para esta demonstração.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="using-liquid-templates-with-set-body"></a>Usando modelos Líquidos com o corpo definido
A política `set-body` pode ser configurada para usar a linguagem de modelagem [Líquida](https://shopify.github.io/liquid/basics/introduction/) para transformar o corpo de uma solicitação ou resposta. Isso poderá ser muito eficaz se você precisar remodelar por completo o formato da mensagem.

> [!IMPORTANT]
> A implementação de Líquido usado na política `set-body` é configurada no “modo C#”. Isso é particularmente importante ao executar ações como filtragem. Por exemplo, o uso de um filtro de data exige o uso de maiúsculas Pascal e da formatação das datas do C#, por exemplo:
>
> {{body.foo.startDateTime| Date:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Para associar corretamente a um corpo XML usando o modelo Líquido, use uma política `set-header` para definir Content-Type como application/xml, text/xml (ou qualquer tipo que termina com +xml); para um corpo JSON, ele deve ser application/json, text/json (ou qualquer tipo que termina com +json).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Converter JSON em SOAP usando um modelo Líquido
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="transform-json-using-a-liquid-template"></a>Transformar JSON usando um modelo líquido
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|set-body|Elemento raiz. Contém o texto do corpo ou expressões que retornam um corpo.|Sim|

### <a name="properties"></a>Propriedades

|Nome|Descrição|Necessário|Padrão|
|----------|-----------------|--------------|-------------|
|modelo|Usado para alterar o modo de modelagem no qual a política de corpo definida será executada. Atualmente, o único valor aceito é:<br /><br />- liquid – a política de corpo definida usará o mecanismo de modelagem líquida |Não||

Para acessar informações sobre a solicitação e a resposta, o modelo Líquido pode ser associado a um objeto de contexto com as seguintes propriedades: <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>Uso
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.

-   **Seções de política:** entrada, saída, back-end

-   **Escopos da política:** todos os escopos

##  <a name="SetHTTPheader"></a> Definir cabeçalho HTTP
 A política `set-header` atribui um valor a uma resposta e/ou cabeçalho de resposta existente ou adiciona uma nova resposta e/ou cabeçalho de resposta.

 Insere uma lista de cabeçalhos HTTP em uma mensagem HTTP. Quando colocada em um pipeline de entrada, esta política define os cabeçalhos HTTP para a solicitação que está sendo passada para o serviço alvo. Quando colocada em um pipeline de saída, esta política define os cabeçalhos HTTP para a resposta que está sendo passada para o cliente do gateway.

### <a name="policy-statement"></a>Declaração de política

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```

#### <a name="forward-context-information-to-the-backend-service"></a>Encaminhar informações de contexto para o serviço de back-end
 Este exemplo mostra como aplicar a política no nível da API para fornecer informações de contexto para o serviço de back-end. Para obter uma demonstração de como configurar e usar essa política, assista ao vídeo [Cloud Cover Episode 177: Mais recursos de Gerenciamento de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avance para 10:30. Em 12:10, há uma demonstração de chamada de uma operação no portal do desenvolvedor na qual você pode ver a política em ação.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Para obter mais informações, veja [Expressões de política](api-management-policy-expressions.md) e [Variável de contexto](api-management-policy-expressions.md#ContextVariables).

> [!NOTE]
> Vários valores de um cabeçalho são concatenados a uma cadeia de caracteres CSV, por exemplo:`headerName: value1,value2,value3`
>
> As exceções incluem cabeçalhos padronizados, cujos valores:
> - podem conter vírgulas (`User-Agent`, `WWW-Authenticate`, `Proxy-Authenticate`),
> - podem conter a data (`Cookie`, `Set-Cookie`, `Warning`),
> - contêm a data (`Date`, `Expires`, `If-Modified-Since`, `If-Unmodified-Since`, `Last-Modified`, `Retry-After`).
>
> No caso dessas exceções, vários valores de cabeçalho não serão concatenados em uma cadeia de caracteres e serão passados como cabeçalhos separados, por exemplo:`User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|set-header|Elemento raiz.|Sim|
|value|Especifica o valor do cabeçalho a ser definido. Para adicionar vários cabeçalhos com o mesmo nome, adicione elementos `value` adicionais.|Sim|

### <a name="properties"></a>Propriedades

|Nome|Descrição|Necessário|Padrão|
|----------|-----------------|--------------|-------------|
|exists-action|Especifica a ação a ser adotada quando o cabeçalho já foi especificado. Este atributo deve ter um dos valores a seguir.<br /><br /> – override – substitui o valor do cabeçalho existente.<br />– skip – não substitui o valor do cabeçalho existente.<br />– append – acrescenta o valor ao valor do cabeçalho existente.<br />– delete – remove o cabeçalho da solicitação.<br /><br /> Quando definido como `override`, listar diversas entradas com o mesmo nome faz com que o cabeçalho seja definido de acordo com todas as entradas (que serão listadas várias vezes); somente valores listados serão definidos no resultado.|Não|override|
|name|Especifica o nome do cabeçalho a ser definido.|Sim|N/D|

### <a name="usage"></a>Uso
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.

-   **Seções da política:** entrada, saída, back-end, em caso de erro

-   **Escopos da política:** todos os escopos

##  <a name="SetQueryStringParameter"></a> Definir parâmetro de cadeia de consulta
 A política `set-query-parameter` adiciona, substitui o valor ou exclui parâmetros de cadeias de consulta de solicitação. Pode ser usada para transmitir parâmetros de consulta esperados pelo serviço de back-end que são opcionais ou nunca estão presentes na solicitação.

### <a name="policy-statement"></a>Declaração de política

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

```xml

<set-query-parameter>
  <parameter name="api-key" exists-action="skip">
    <value>12345678901</value>
  </parameter>
  <!-- for multiple parameters with the same name add additional value elements -->
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Encaminhar informações de contexto para o serviço de back-end
 Este exemplo mostra como aplicar a política no nível da API para fornecer informações de contexto para o serviço de back-end. Para obter uma demonstração de como configurar e usar essa política, assista ao vídeo [Cloud Cover Episode 177: Mais recursos de Gerenciamento de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avance para 10:30. Em 12:10, há uma demonstração de chamada de uma operação no portal do desenvolvedor na qual você pode ver a política em ação.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Para obter mais informações, veja [Expressões de política](api-management-policy-expressions.md) e [Variável de contexto](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|set-query-parameter|Elemento raiz.|Sim|
|value|Especifica o valor do parâmetro de consulta a ser definido. Para adicionar vários parâmetros de consulta com o mesmo nome, adicione elementos `value` adicionais.|Sim|

### <a name="properties"></a>Propriedades

|Nome|Descrição|Necessário|Padrão|
|----------|-----------------|--------------|-------------|
|exists-action|Especifica a ação a ser adotada quando o parâmetro de consulta já foi especificado. Este atributo deve ter um dos valores a seguir.<br /><br /> – override – substitui o valor do parâmetro existente.<br />– skip – não substitui o valor do parâmetro de consulta existente.<br />– append – acrescenta o valor ao valor do parâmetro de consulta existente.<br />– delete – remove o parâmetro de consulta da solicitação.<br /><br /> Quando definido como `override`, listar diversas entradas com o mesmo nome faz com que o parâmetro de consulta seja definido de acordo com todas as entradas (que serão listadas várias vezes); somente valores listados serão definidos no resultado.|Não|override|
|name|Especifica o nome do parâmetro de consulta a ser definido.|Sim|N/D|

### <a name="usage"></a>Uso
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.

-   **Seções de política:** de entrada, back-end

-   **Escopos da política:** todos os escopos

##  <a name="RewriteURL"></a> Reescrever URL
 A política `rewrite-uri` converte a URL de uma solicitação de sua forma pública para a forma esperada pelo serviço Web, conforme mostrado no exemplo a seguir.

- URL pública – `http://api.example.com/storenumber/ordernumber`

- URL de Solicitação – `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Essa política pode ser usada quando uma URL simplificada para humanos e/ou navegadores precisar ser transformado na URL esperada pelo serviço Web. Essa política só precisa ser aplicada ao expor um formato de URL alternativo como URLs limpas, URLs RESTful, URLs simplificadas para usuários ou para URLs amigáveis ao SEO que são URLs puramente estruturais que não contêm uma cadeia de consulta, contendo somente o caminho do recurso (após o esquema e autoridade). Frequentemente, isso é feito para fins estéticos, de usabilidade ou de otimização do mecanismo de pesquisa (SEO).

> [!NOTE]
>  Você pode adicionar somente parâmetros de cadeia de consulta usando a política. Você não pode adicionar parâmetros de caminho do modelo extra à URL reescrita.

### <a name="policy-statement"></a>Declaração de política

```xml
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />
```

### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put?c=d -->
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" copy-unmatched-params="false" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|rewrite-uri|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Atributo|Descrição|Necessário|Padrão|
|---------------|-----------------|--------------|-------------|
|modelo|A URL real do serviço Web com quaisquer parâmetros de cadeia de consulta. Ao usar expressões, o valor inteiro deve ser uma expressão.|Sim|N/D|
|copy-unmatched-params|Especifica se os parâmetros de consulta na solicitação de entrada não presentes no modelo de URL original são adicionados à URL definida pelo modelo reescrito|Não|true|

### <a name="usage"></a>Uso
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.

-   **Seções de política:** de entrada

-   **Escopos da política:** todos os escopos

##  <a name="XSLTransform"></a> Transformar XML usando um XSLT
 A política `Transform XML using an XSLT` aplica uma transformação XSL para XML no corpo da solicitação ou da resposta.

### <a name="policy-statement"></a>Declaração de política

```xml
<xsl-transform>
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:output method="xml" indent="yes" />
        <xsl:param name="User-Agent" />
        <xsl:template match="* | @* | node()">
            <xsl:copy>
                <xsl:if test="self::* and not(parent::*)">
                    <xsl:attribute name="User-Agent">
                        <xsl:value-of select="$User-Agent" />
                    </xsl:attribute>
                </xsl:if>
                <xsl:apply-templates select="* | @* | node()" />
            </xsl:copy>
        </xsl:template>
    </xsl:stylesheet>
  </xsl-transform>
```

### <a name="example"></a>Exemplo

```xml
<policies>
  <inbound>
      <base />
  </inbound>
  <outbound>
      <base />
      <xsl-transform>
        <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />
            <!-- Copy all nodes directly-->
            <xsl:template match="node()| @*|*">
                <xsl:copy>
                    <xsl:apply-templates select="@* | node()|*" />
                </xsl:copy>
            </xsl:template>
        </xsl:stylesheet>
    </xsl-transform>
  </outbound>
</policies>
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|xsl-transform|Elemento raiz.|Sim|
|parâmetro|Usado para definir as variáveis usadas na transformação|Não|
|xsl:stylesheet|Elemento de folha de estilos de raiz. Todos os elementos e atributos definidos dentro dele seguem o padrão [especificação XSLT](https://www.w3.org/TR/xslt)|Sim|

### <a name="usage"></a>Uso
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.

-   **Seções de política:** de entrada, de saída

-   **Escopos da política:** todos os escopos

## <a name="next-steps"></a>Próximas etapas

Para mais informações, consulte os seguintes tópicos:

+ [Políticas no Gerenciamento de API](api-management-howto-policies.md)
+ [Referência de Política](api-management-policy-reference.md) para uma lista completa das instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)
