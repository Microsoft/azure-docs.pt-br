---
title: "Políticas avançadas de Gerenciamento de API do Azure| Microsoft Docs"
description: "Saiba mais sobre as políticas avançadas disponíveis para uso no Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 8a13348b-7856-428f-8e35-9e4273d94323
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: e5a658e0d20d42911870f2522f6c1bab7529ea11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="api-management-advanced-policies"></a>Políticas avançadas de Gerenciamento de API
Este tópico fornece uma referência para as políticas de Gerenciamento de API a seguir. Para obter mais informações sobre como adicionar e configurar políticas, consulte [Políticas de Gerenciamento de API](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="AdvancedPolicies"></a> Políticas avançadas  
  
-   [Controlar fluxo](api-management-advanced-policies.md#choose) - Aplica-se condicionalmente a instruções de políticas com base nos resultados da avaliação do booliano [expressions](api-management-policy-expressions.md).  
  
-   [Encaminhar solicitação](#ForwardRequest) -Encaminha a solicitação ao serviço de back-end.

-   [Simultaneidade de limite](#LimitConcurrency) - impede que as políticas embutidas sejam executadas mais do que o número especificado de solicitações por vez.
  
-   [Registrar no Hub de Eventos](#log-to-eventhub) – envia mensagens no formato especificado para um Hub de Eventos definido por uma entidade Logger. 

-   [Resposta fictícia](#mock-response) – anula a execução de pipeline e retorna uma resposta fictícia diretamente para o chamador.
  
-   [Repetir](#Retry) - repete a execução das instruções de política, se e até que a condição seja atendida. A execução será repetida em intervalos de tempo especificados até e a contagem de repetições especificada.  
  
-   [Retornar resposta](#ReturnResponse) - Anula a execução de pipeline e retorna a resposta especificada diretamente para o autor da chamada. 
  
-   [Enviar solicitação unidirecional](#SendOneWayRequest) - Envia uma solicitação para a URL especificada sem aguardar uma resposta.  
  
-   [Enviar solicitação](#SendRequest) - Envia uma solicitação para a URL especificada.  

-   [Definir proxy HTTP](#SetHttpProxy) – permite a você, por meio de um proxy HTTP, reencaminhar solicitações encaminhadas.  

-   [Definir método de solicitação](#SetRequestMethod) - Permite alterar o método HTTP de uma solicitação.  
  
-   [Definir código de status](#SetStatus) – altera o código de status de HTTP para o valor especificado.  
  
-   [Definir variável](api-management-advanced-policies.md#set-variable) – persiste um valor em uma variável de [contexto](api-management-policy-expressions.md#ContextVariables) nomeada para acesso posterior.  

-   [Rastreamento](#Trace) - adiciona uma cadeia de caracteres para a saída do [Inspetor de API](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/).  
  
-   [Aguardar](#Wait) – aguarda a conclusão das políticas [Enviar solicitação](api-management-advanced-policies.md#SendRequest), [Obter valor do cache](api-management-caching-policies.md#GetFromCacheByKey) ou [Controlar fluxo](api-management-advanced-policies.md#choose) antes de continuar.  
  
##  <a name="choose"></a> Controlar fluxo  
 A política `choose` aplica declarações de política embutidas com base no resultado da avaliação de expressões boolianas, semelhantes a um if-then-else ou a um constructo de opção em uma linguagem de programação.  
  
###  <a name="ChoosePolicyStatement"></a> Declaração de política  
  
```xml  
<choose>   
    <when condition="Boolean expression | Boolean constant">   
        <!— one or more policy statements to be applied if the above condition is true  -->  
    </when>   
    <when condition="Boolean expression | Boolean constant">   
        <!— one or more policy statements to be applied if the above condition is true  -->  
    </when>   
    <otherwise>   
        <!— one or more policy statements to be applied if none of the above conditions are true  -->  
</otherwise>   
</choose>  
```  
  
 A política de fluxo de controle deve conter pelo menos um elemento `<when/>`. O elemento `<otherwise/>` é opcional. As condições nos elementos `<when/>` são avaliadas na ordem de aparecimento na política. As declarações de política incluídas dentro do primeiro elemento `<when/>` com atributo de condição igual a `true` serão aplicadas. As políticas incluídas dentro do elemento `<otherwise/>`, se estiverem presentes, serão aplicadas se todos os atributos de condição do elemento `<when/>` forem `false`.  
  
### <a name="examples"></a>Exemplos  
  
####  <a name="ChooseExample"></a> Exemplo  
 O exemplo a seguir demonstra uma política [set-variable](api-management-advanced-policies.md#set-variable) e duas políticas de fluxo de controle.  
  
 A política de definir variável está na seção de entrada e cria uma variável de [contexto](api-management-policy-expressions.md#ContextVariables) booliana `isMobile` que será definida como true se o cabeçalho da solicitação `User-Agent` contiver o texto `iPad` ou `iPhone`.  
  
 A primeira política de fluxo de controle também está na seção de entrada e aplica condicionalmente uma de duas políticas [Definir parâmetro de cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) dependendo do valor da variável de contexto `isMobile`.  
  
 A segunda política de fluxo de controle está na seção de saída e aplica condicionalmente a política [Converter XML para JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) quando `isMobile` é definida como `true`.  
  
```xml  
<policies>  
    <inbound>  
        <set-variable name="isMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />  
        <base />  
        <choose>  
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">  
                <set-query-parameter name="mobile" exists-action="override">  
                    <value>true</value>  
                </set-query-parameter>  
            </when>  
            <otherwise>  
                <set-query-parameter name="mobile" exists-action="override">  
                    <value>false</value>  
                </set-query-parameter>  
            </otherwise>  
        </choose>  
    </inbound>  
    <outbound>  
        <base />  
        <choose>  
            <when condition="@(context.GetValueOrDefault<bool>("isMobile"))">  
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>  
            </when>  
        </choose>  
    </outbound>  
</policies>  
```  
  
#### <a name="example"></a>Exemplo  
 Este exemplo mostra como executar a filtragem de conteúdo removendo elementos de dados da resposta recebida do serviço de back-end ao usar o produto `Starter`. Para ver uma demonstração da configuração e do uso dessa política, consulte [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) (Abordagem da Nuvem Episódio 177: Mais Recursos de Gerenciamento de API com Vlad Vinogradsky) e avance para 34:30. Inicie em 31:50 para uma visão geral da [API da Previsão de Céu Escuro](https://developer.forecast.io/) usada para esta demonstração.  
  
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
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Obrigatório|  
|-------------|-----------------|--------------|  
|choose|Elemento raiz.|Sim|  
|when|A condição a ser usada para as partes `if` ou `ifelse` da política `choose`. Se política `choose` tiver várias seções `when`, elas serão avaliadas sequencialmente. Uma vez que o `condition` de um elemento when é avaliado como `true`, nenhuma outra condição `when` é avaliada.|Sim|  
|otherwise|Contém o trecho de código da política a ser usado se nenhuma das condições `when` for avaliada como `true`.|Não|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Obrigatório|  
|---------------|-----------------|--------------|  
|condition="Boolean expression &#124; Boolean constant"|A constante ou expressão booliana a ser avaliada quando a declaração de política contendo `when` é avaliada.|Sim|  
  
###  <a name="ChooseUsage"></a> Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções da política:** entrada, saída, back-end, em caso de erro  
  
-   **Escopos da política:** todos os escopos  
  
##  <a name="ForwardRequest"></a> Encaminhar solicitação  
 A política `forward-request` encaminha a solicitação de entrada para o serviço de back-end especificado na variável de [contexto](api-management-policy-expressions.md#ContextVariables) de solicitação. A URL do serviço de back-end é especificada nas [configurações](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) de API e pode ser alterada usando a política [definir o serviço de back-end](api-management-transformation-policies.md).  
  
> [!NOTE]
>  A remoção dessa política resulta na solicitação não ser encaminhada para o serviço de back-end e as políticas na seção de saída serem avaliadas imediatamente após a conclusão bem-sucedida das políticas na seção de entrada.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<forward-request timeout="time in seconds" follow-redirects="true | false"/>  
```  
  
### <a name="examples"></a>Exemplos  
  
#### <a name="example"></a>Exemplo  
 A política de nível de API a seguir encaminha todas as solicitações para o serviço de back-end com um intervalo de tempo limite de 60 segundos.  
  
```xml  
<!-- api level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <forward-request timeout="60"/>  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>Exemplo  
 Esta política de nível de operação usa o elemento `base` para herdar a política de back-end do escopo de nível de API pai.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <base/>  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>Exemplo  
 Essa política de nível de operação explicitamente encaminha todas as solicitações para o serviço de back-end com um tempo limite de 120 e não herda a política de back-end do nível da API pai.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <forward-request timeout="120"/>   
        <!-- effective policy. note the absence of <base/> -->  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>Exemplo  
 Essa política de nível de operação não encaminha solicitações para o serviço de back-end.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <!-- no forwarding to backend -->  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Obrigatório|  
|-------------|-----------------|--------------|  
|forward-request|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Obrigatório|Padrão|  
|---------------|-----------------|--------------|-------------|  
|timeout="integer"|O intervalo de tempo limite em segundos antes de a chamada para o serviço de back-end falhar.|Não|Sem tempo limite|  
|follow-redirects="true &#124; false"|Especifica se os redirecionamentos do serviço de back-end são seguidos pelo gateway ou retornados ao chamador.|Não|false|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** back-end  
  
-   **Escopos da política:** todos os escopos  
  
##  <a name="LimitConcurrency"></a> Simultaneidade de limite  
 A política `limit-concurrency` impede que as políticas embutidas sejam executadas mais do que o número especificado de solicitações em um determinado momento. Ao exceder o limite, novas solicitações são adicionadas a uma fila, até que o comprimento máximo da fila seja atingido. Quando a fila atinge seu máximo, novas solicitações falham imediatamente.
  
###  <a name="LimitConcurrencyStatement"></a> Declaração de política  
  
```xml  
<limit-concurrency key="expression" max-count="number" timeout="in seconds" max-queue-length="number">
        <!— nested policy statements -->  
</limit-concurrency>
``` 

### <a name="examples"></a>Exemplos  
  
####  <a name="ChooseExample"></a> Exemplo  
 O exemplo a seguir demonstra como limitar o número de solicitações encaminhadas a um back-end com base no valor de uma variável de contexto.
 
```xml  
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3" timeout="60">
      <forward-request timeout="120"/>
    <limit-concurrency/>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Obrigatório|  
|-------------|-----------------|--------------|    
|limit-concurrency|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Obrigatório|Padrão|  
|---------------|-----------------|--------------|--------------|  
|chave|Uma cadeia de caracteres. Expressão permitida. Especifica o escopo de simultaneidade. Pode ser compartilhado por várias políticas.|Sim|N/D|  
|max-count|Um inteiro. Especifica um número máximo de solicitações que são permitidas para inserir a política.|Sim|N/D|  
|Tempo limite|Um inteiro. Expressão permitida. Especifica o número de segundos que uma solicitação deve esperar para inserir um escopo antes de falhar com "429 Muitas solicitações"|Não|Infinito|  
|max-queue-length|Um inteiro. Expressão permitida. Especifica o comprimento máximo da fila. Solicitações de entrada que tentarem inserir essa política serão encerradas com "429 Muitas solicitações" assim que a fila atingir seu comprimento máximo.|Não|Infinito|  
  
###  <a name="ChooseUsage"></a> Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções da política:** entrada, saída, back-end, em caso de erro  
  
-   **Escopos da política:** todos os escopos  

##  <a name="log-to-eventhub"></a> Registrar no Hub de Eventos  
 A política `log-to-eventhub` envia mensagens no formato especificado para um Hub de Eventos definido por uma entidade Logger. Como o nome sugere, a política é usada para salvar informações de contexto de solicitação ou de resposta solicitadas para a análise online ou offline.  
  
> [!NOTE]
>  Para obter um guia passo a passo sobre como configurar um hub de eventos e registrar eventos, consulte [Como registrar eventos em log para Hubs de Eventos do Azure no Gerenciamento de API](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">  
  Expression returning a string to be logged  
</log-to-eventhub>  
  
```  
  
### <a name="example"></a>Exemplo  
 Qualquer cadeia de caracteres pode ser usada como o valor a ser registrado em Hubs de Eventos. Neste exemplo, a data e hora, nome do serviço de implantação, a ID de solicitação, endereço IP e o nome da operação para todas as chamadas de entrada são registrados no agente do hub de eventos registrado com a ID `contoso-logger`.  
  
```xml  
<policies>  
  <inbound>  
    <log-to-eventhub logger-id ='contoso-logger'>  
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )   
    </log-to-eventhub>  
  </inbound>  
  <outbound>          
  </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Obrigatório|  
|-------------|-----------------|--------------|  
|log-to-eventhub|Elemento raiz. O valor desse elemento é a cadeia de caracteres a ser registrada no seu hub de eventos.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Obrigatório|  
|---------------|-----------------|--------------|  
|logger-id|A ID do agente registrada com o serviço de Gerenciamento de API.|Sim|  
|partition-id|Especifica o índice da partição em que as mensagens são enviadas.|Opcional. Esse atributo não poderá ser usado se `partition-key` for usado.|  
|partition-key|Especifica o valor usado para a atribuição de partição quando as mensagens são enviadas.|Opcional. Esse atributo não poderá ser usado se `partition-id` for usado.|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções da política:** entrada, saída, back-end, em caso de erro  
  
-   **Escopos da política:** todos os escopos  

##  <a name="mock-response"></a> Resposta fictícia  
O `mock-response`, como o nome indica, é usado para simular APIs e operações. Ele anula a execução normal de pipeline e retorna uma resposta fictícia ao chamador. A política sempre tenta retornar respostas da mais alta fidelidade. Ela prefere exemplos de conteúdo de resposta, sempre que disponíveis. Ela gera respostas de exemplo com base em esquemas, quando esquemas são fornecidos e exemplos não são fornecidos. Se não forem encontrados exemplos nem esquemas, serão retornadas respostas sem conteúdo.
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<mock-response status-code="code" content-type="media type"/>  
  
```  
  
### <a name="examples"></a>Exemplos  
  
```xml  
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this 
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this 
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Obrigatório|  
|-------------|-----------------|--------------|  
|mock-response|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Obrigatório|Padrão|  
|---------------|-----------------|--------------|--------------|  
|status-code|Especifica o código de status da resposta e é usado para selecionar o exemplo ou o esquema correspondente.|Não|200|  
|content-type|Especifica o valor de cabeçalho da resposta `Content-Type` e é usado para selecionar o exemplo ou o esquema correspondente.|Não|Nenhum|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** de entrada, de saída, em caso de erro  
  
-   **Escopos da política:** todos os escopos

##  <a name="Retry"></a> Repetir  
 A política `retry` executa suas políticas filho uma vez e tenta realizar sua execução novamente até `condition` da nova tentativa se tornar `false` ou `count` da nova tentativa ser esgotada.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
  
<retry  
    condition="boolean expression or literal"  
    count="number of retry attempts"  
    interval="retry interval in seconds"  
    max-interval="maximum retry interval in seconds"  
    delta="retry interval delta in seconds"  
    first-fast-retry="boolean expression or literal">  
        <!-- One or more child policies. No restrictions -->  
</retry>  
  
```  
  
### <a name="example"></a>Exemplo  
 No exemplo a seguir o encaminhamento de solicitação será repetido até dez vezes usando o algoritmo de nova tentativa exponencial. Uma vez que `first-fast-retry` é definido como false, todas novas tentativas estão sujeitas ao algoritmo de nova tentativa exponencial.  
  
```xml  
  
<retry  
    condition="@(context.Response.StatusCode == 500)"  
    count="10"  
    interval="10"  
    max-interval="100"  
    delta="10"  
    first-fast-retry="false">  
        <forward-request />  
</retry>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Obrigatório|  
|-------------|-----------------|--------------|  
|tentar novamente|Elemento raiz. Pode conter quaisquer outras políticas como seus elementos filho.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Obrigatório|Padrão|  
|---------------|-----------------|--------------|-------------|  
|condition|Uma [expressão](api-management-policy-expressions.md) ou literal booliano especificando se as novas tentativas devem ser paradas (`false`) ou continuadas (`true`).|Sim|N/D|  
|count|Um número positivo que especifica o número máximo de novas tentativas a serem realizadas.|Sim|N/D|  
|intervalo|Um número positivo, em segundos, que especifica o intervalo de espera entre as novas tentativas.|Sim|N/D|  
|max-interval|Um número positivo, em segundos, que especifica o intervalo de espera máximo entre as novas tentativas. Ele é usado para implementar um algoritmo de nova tentativa exponencial.|Não|N/D|  
|delta|Um número positivo, em segundos, que especifica o incremento do intervalo de espera. Ele é usado para implementar algoritmos de nova tentativa exponenciais e lineares.|Não|N/D|  
|first-fast-retry|Se definido como `true`, a primeira nova tentativa será realizada imediatamente.|Não|`false`|  
  
> [!NOTE]
>  Quando apenas `interval` for especificado, novas tentativas de intervalo **fixo** serão realizadas.  
>  Quando apenas `interval` e `delta` forem especificados, um algoritmo de nova tentativa de intervalo **linear** será usado, em que o tempo de espera entre as novas tentativas é calculado de acordo com a seguinte fórmula – `interval + (count - 1)*delta`.  
>  Quando `interval`, `max-interval` e `delta` forem especificados, o algoritmo de nova tentativa de intervalo **exponencial** será aplicado, em que o tempo de espera entre as novas tentativas aumenta exponencialmente do valor de `interval` até o valor de `max-interval` de acordo com a seguinte fórmula – `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir. Observe que as restrições de uso de política filho serão herdadas por essa política.  
  
-   **Seções da política:** entrada, saída, back-end, em caso de erro  
  
-   **Escopos da política:** todos os escopos  
  
##  <a name="ReturnResponse"></a> Retornar resposta  
 A política `return-response` anula a execução do pipeline e retorna uma resposta padrão ou personalizada para o chamador. A resposta padrão é `200 OK` sem corpo. A resposta personalizada pode ser especificada por meio de declarações de política ou variável de contexto. Quando ambas são fornecidas, a resposta contida na variável de contexto é modificada pelas instruções de política antes de ser retornada para o chamador.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<return-response response-variable-name="existing context variable">  
  <set-header/>  
  <set-body/>  
  <set-status/>  
</return-response>  
  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<return-response>  
   <set-status code="401" reason="Unauthorized"/>  
   <set-header name="WWW-Authenticate" exists-action="override">  
      <value>Bearer error="invalid_token"</value>  
   </set-header>  
</return-response>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Obrigatório|  
|-------------|-----------------|--------------|  
|return-response|Elemento raiz.|Sim|  
|set-header|Uma declaração de política [set-header](api-management-transformation-policies.md#SetHTTPheader).|Não|  
|set-body|Uma declaração de política [set-body](api-management-transformation-policies.md#SetBody).|Não|  
|set-status|Uma declaração de política [set-status](api-management-advanced-policies.md#SetStatus).|Não|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Obrigatório|  
|---------------|-----------------|--------------|  
|response-variable-name|O nome da variável de contexto referenciada de, por exemplo, uma política [send-request](api-management-advanced-policies.md#SendRequest) upstream e que contém um objeto `Response`|Opcional.|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções da política:** entrada, saída, back-end, em caso de erro  
  
-   **Escopos da política:** todos os escopos  
  
##  <a name="SendOneWayRequest"></a> Enviar solicitação unidirecional  
 A política `send-one-way-request` envia a solicitação fornecida para a URL especificada sem aguardar uma resposta.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<send-one-way-request mode="new | copy">  
  <url>...</url>  
  <method>...</method>  
  <header name="" exists-action="override | skip | append | delete">...</header>  
  <body>...</body>  
</send-one-way-request>  
  
```  
  
### <a name="example"></a>Exemplo  
 Essa política de exemplo mostra um exemplo de uso da política `send-one-way-request` para enviar uma mensagem para uma sala de chat do Slack se o código de resposta HTTP for maior ou igual a 500. Para obter mais informações sobre esse exemplo, consulte [Uso dos serviços externos do serviço de Gerenciamento de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
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
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Obrigatório|  
|-------------|-----------------|--------------|  
|send-one-way-request|Elemento raiz.|Sim|  
|url|A URL da solicitação.|Não se mode=copy, caso contrário, sim.|  
|estático|O método HTTP para a solicitação.|Não se mode=copy, caso contrário, sim.|  
|cabeçalho|Cabeçalho da solicitação. Use vários elementos de cabeçalho para vários cabeçalhos de solicitação.|Não|  
|corpo|O corpo da solicitação.|Não|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Obrigatório|Padrão|  
|---------------|-----------------|--------------|-------------|  
|mode="string"|Determina se esta é uma nova solicitação ou uma cópia da solicitação atual. No modo de saída, mode=copy não inicializa o corpo da solicitação.|Não|Novo|  
|name|Especifica o nome do cabeçalho a ser definido.|Sim|N/D|  
|exists-action|Especifica a ação a ser adotada quando o cabeçalho já foi especificado. Este atributo deve ter um dos valores a seguir.<br /><br /> – override – substitui o valor do cabeçalho existente.<br />– skip – não substitui o valor do cabeçalho existente.<br />– append – acrescenta o valor ao valor do cabeçalho existente.<br />– delete – remove o cabeçalho da solicitação.<br /><br /> Quando definido como `override`, listar diversas entradas com o mesmo nome faz com que o cabeçalho seja definido de acordo com todas as entradas (que serão listadas várias vezes); somente valores listados serão definidos no resultado.|Não|override|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções da política:** entrada, saída, back-end, em caso de erro  
  
-   **Escopos da política:** todos os escopos  
  
##  <a name="SendRequest"></a> Enviar solicitação  
 A política `send-request` envia a solicitação fornecida para a URL especificada, aguardando não mais do que o valor de tempo limite definido.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error  
="false|true">  
  <set-url>...</set-url>  
  <set-method>...</set-method>  
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>  
  <set-body>...</set-body>  
</send-request>  
  
```  
  
### <a name="example"></a>Exemplo  
 Este exemplo mostra uma maneira de verificar um token de referência com um servidor de autorização. Para obter mais informações sobre esse exemplo, consulte [Uso dos serviços externos do serviço de Gerenciamento de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
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
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Obrigatório|  
|-------------|-----------------|--------------|  
|send-request|Elemento raiz.|Sim|  
|url|A URL da solicitação.|Não se mode=copy, caso contrário, sim.|  
|estático|O método HTTP para a solicitação.|Não se mode=copy, caso contrário, sim.|  
|cabeçalho|Cabeçalho da solicitação. Use vários elementos de cabeçalho para vários cabeçalhos de solicitação.|Não|  
|corpo|O corpo da solicitação.|Não|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Obrigatório|Padrão|  
|---------------|-----------------|--------------|-------------|  
|mode="string"|Determina se esta é uma nova solicitação ou uma cópia da solicitação atual. No modo de saída, mode=copy não inicializa o corpo da solicitação.|Não|Novo|  
|response-variable-name="string"|Se não estiver presente, `context.Response` será usado.|Não|N/D|  
|timeout="integer"|O intervalo de tempo limite em segundos antes de a chamada para a URL falhar.|Não|60|  
|ignore-error|Se for true e a solicitação resultar em um erro:<br /><br /> – Se response-variable-name tiver sido especificado, ele conterá um valor nulo.<br />– Se response-variable-name não tiver sido especificado, context.Request não será atualizado.|Não|false|  
|name|Especifica o nome do cabeçalho a ser definido.|Sim|N/D|  
|exists-action|Especifica a ação a ser adotada quando o cabeçalho já foi especificado. Este atributo deve ter um dos valores a seguir.<br /><br /> – override – substitui o valor do cabeçalho existente.<br />– skip – não substitui o valor do cabeçalho existente.<br />– append – acrescenta o valor ao valor do cabeçalho existente.<br />– delete – remove o cabeçalho da solicitação.<br /><br /> Quando definido como `override`, listar diversas entradas com o mesmo nome faz com que o cabeçalho seja definido de acordo com todas as entradas (que serão listadas várias vezes); somente valores listados serão definidos no resultado.|Não|override|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções da política:** entrada, saída, back-end, em caso de erro  
  
-   **Escopos da política:** todos os escopos  
  
##  <a name="SetHttpProxy"></a> Definir proxy HTTP  
 A política `proxy` permite reencaminhar, por meio de um proxy HTTP, as solicitações encaminhadas aos back-ends. Entre o gateway e o proxy, há suporte apenas para HTTP (e não para HTTPS). Somente autenticação básica e NTLM.
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<proxy url="http://hostname-or-ip:port" username="username" password="password" />  
  
```  
  
### <a name="example"></a>Exemplo  
Observe o uso de [propriedades](api-management-howto-properties.md) como valores de nome de usuário e senha para evitar armazenar informações confidenciais no documento de política.  
  
```xml  
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Obrigatório|  
|-------------|-----------------|--------------|  
|proxy|Elemento raiz|Sim|  

### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Obrigatório|Padrão|  
|---------------|-----------------|--------------|-------------|  
|url="string"|URL do proxy no formato http://host:porta.|Sim|N/D |  
|username="string"|Nome de usuário a ser usado para autenticação com o proxy.|Não|N/D |  
|password="string"|Senha a ser usada para autenticação com o proxy.|Não|N/D |  

### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** de entrada  
  
-   **Escopos da política:** todos os escopos  

##  <a name="SetRequestMethod"></a> Definir método de solicitação  
 A política `set-method` permite alterar o método de solicitação HTTP de uma solicitação.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<set-method>METHOD</set-method>  
  
```  
  
### <a name="example"></a>Exemplo  
 Essa política de exemplo que usa a política `set-method` mostra um exemplo de envio de uma mensagem para uma sala de chat do Slack se o código de resposta HTTP for maior ou igual a 500. Para obter mais informações sobre esse exemplo, consulte [Uso dos serviços externos do serviço de Gerenciamento de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
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
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Obrigatório|  
|-------------|-----------------|--------------|  
|set-method|Elemento raiz. O valor do elemento especifica o método HTTP.|Sim|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** entrada, em caso de erro  
  
-   **Escopos da política:** todos os escopos  
  
##  <a name="SetStatus"></a> Definir código de status  
 A política `set-status` define o código de status HTTP para o valor especificado.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<set-status code="" reason=""/>  
  
```  
  
### <a name="example"></a>Exemplo  
 Este exemplo mostra como retornar uma resposta 401, se o token de autorização for inválido. Para obter mais informações, consulte [Uso dos serviços externos do serviço de Gerenciamento de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)  
  
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
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Obrigatório|  
|-------------|-----------------|--------------|  
|set-status|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Obrigatório|Padrão|  
|---------------|-----------------|--------------|-------------|  
|code="integer"|O código de status HTTP a ser retornado.|Sim|N/D|  
|reason="string"|Uma descrição do motivo para retornar o código de status.|Sim|N/D|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** saída, back-end, em caso de erro  
  
-   **Escopos da política:** todos os escopos  

##  <a name="set-variable"></a> Definir variável  
 A política `set-variable` declara uma variável de [contexto](api-management-policy-expressions.md#ContextVariables) e atribui a ela um valor especificado por meio de uma [expressão](api-management-policy-expressions.md) ou literal de cadeia de caracteres. Se a expressão contiver um literal ele será convertido em uma cadeia de caracteres e o tipo do valor será `System.String`.  
  
###  <a name="set-variablePolicyStatement"></a> Declaração de política  
  
```xml  
<set-variable name="variable name" value="Expression | String literal" />  
```  
  
###  <a name="set-variableExample"></a> Exemplo  
 O exemplo a seguir demonstra uma política de definir a variável na seção de entrada. Essa política de definir variável cria uma variável de [contexto](api-management-policy-expressions.md#ContextVariables) booliana `isMobile` que será definida como true se o cabeçalho da solicitação `User-Agent` contiver o texto `iPad` ou `iPhone`.  
  
```xml  
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Obrigatório|  
|-------------|-----------------|--------------|  
|set-variable|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Obrigatório|  
|---------------|-----------------|--------------|  
|name|O nome da variável.|Sim|  
|valor|O valor da variável. Isso pode ser uma expressão ou um valor literal.|Sim|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções da política:** entrada, saída, back-end, em caso de erro  
  
-   **Escopos da política:** todos os escopos  
  
###  <a name="set-variableAllowedTypes"></a> Tipos permitidos  
 As expressões usadas na política `set-variable` devem retornar um dos seguintes tipos básicos.  
  
-   System.Boolean  
  
-   System.SByte  
  
-   System.Byte  
  
-   System.UInt16  
  
-   System.UInt32  
  
-   System.UInt64  
  
-   System.Int16  
  
-   System.Int32  
  
-   System.Int64  
  
-   System.Decimal  
  
-   System.Single  
  
-   System.Double  
  
-   System.Guid  
  
-   System.String  
  
-   System.Char  
  
-   System.DateTime  
  
-   System.TimeSpan  
  
-   System.Byte?  
  
-   System.UInt16?  
  
-   System.UInt32?  
  
-   System.UInt64?  
  
-   System.Int16?  
  
-   System.Int32?  
  
-   System.Int64?  
  
-   System.Decimal?  
  
-   System.Single?  
  
-   System.Double?  
  
-   System.Guid?  
  
-   System.String?  
  
-   System.Char?  
  
-   System.DateTime?  

##  <a name="Trace"></a> Rastreamento  
 A política `trace` adiciona uma cadeia de caracteres à saída do [Inspetor de API](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/). A política será executada somente quando o rastreamento for disparado, ou seja, o cabeçalho de solicitação `Ocp-Apim-Trace` está presente e definido como `true` e o cabeçalho de solicitação `Ocp-Apim-Subscription-Key` está presente e contém uma chave válida associada à conta do administrador.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
  
<trace source="arbitrary string literal"/>  
    <!-- string expression or literal -->  
</trace>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Obrigatório|  
|-------------|-----------------|--------------|  
|trace|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Obrigatório|Padrão|  
|---------------|-----------------|--------------|-------------|  
|fonte|Literal de cadeia de caracteres significativo para o visualizador de rastreamento e especificando a fonte da mensagem.|Sim|N/D|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções da política:** entrada, saída, back-end, em caso de erro  
  
-   **Escopos da política:** todos os escopos  
  
##  <a name="Wait"></a> Aguardar  
 A política `wait` executa suas políticas filho imediatas em paralelo e aguarda que todas ou uma das políticas filho imediatas sejam concluídas antes de ser concluída. A política de espera pode ter como suas políticas de filho imediatas as políticas de [Enviar solicitação](api-management-advanced-policies.md#SendRequest), [Obter valor do cache](api-management-caching-policies.md#GetFromCacheByKey) e [Controlar fluxo](api-management-advanced-policies.md#choose).  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<wait for="all|any">  
  <!--Wait policy can contain send-request, cache-lookup-value,   
        and choose policies as child elements -->  
</wait>  
  
```  
  
### <a name="example"></a>Exemplo  
 No exemplo a seguir há duas políticas `choose` como políticas filho imediatas da política `wait`. Cada uma dessas políticas `choose` executadas em paralelo. Cada política `choose` tenta recuperar um valor em cache. Se houver uma perda no cache, um serviço de back-end será chamado para fornecer o valor. Neste exemplo a política `wait` não é concluída até todas as políticas filho imediatas serem concluídas, porque o atributo `for` está definido como `all`.   Neste exemplo as variáveis de contexto (`execute-branch-one`, `value-one`, `execute-branch-two` e `value-two`) são declaradas fora do escopo desta política de exemplo.  
  
```xml  
<wait for="all">  
  <choose>  
    <when condition="@((bool)context.Variables["execute-branch-one="])">  
      <cache-lookup-value key="key-one" variable-name="value-one" />  
      <choose>  
        <when condition="@(!context.Variables.ContainsKey("value-one="))">  
          <send-request mode="new" response-variable-name="value-one">  
            <set-url>https://backend-one</set-url>  
            <set-method>GET</set-method>  
          </send-request>  
        </when>  
      </choose>  
    </when>  
  </choose>  
  <choose>  
    <when condition="@((bool)context.Variables["execute-branch-two="])">  
      <cache-lookup-value key="key-two" variable-name="value-two" />  
      <choose>  
        <when condition="@(!context.Variables.ContainsKey("value-two="))">  
          <send-request mode="new" response-variable-name="value-two">  
            <set-url>https://backend-two</set-url>  
            <set-method>GET</set-method>  
          </send-request>  
        </when>  
      </choose>  
    </when>  
  </choose>  
</wait>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Obrigatório|  
|-------------|-----------------|--------------|  
|wait|Elemento raiz. Pode conter como elementos filho somente as políticas `send-request`, `cache-lookup-value` e `choose`.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Obrigatório|Padrão|  
|---------------|-----------------|--------------|-------------|  
|for|Determina se a política `wait` aguarda todas as políticas filho imediatas a serem concluídas ou apenas uma. Valores permitidos são:<br /><br /> -   `all` – aguarda todas as políticas filho imediatas serem concluídas<br />– any – aguarda qualquer política filho imediata ser concluída. Concluída a primeira política filho imediata, a política `wait` é concluída e a execução de qualquer outra política filho imediata é encerrada.|Não|tudo|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** entrada, saída, back-end  
  
-   **Escopos da política:**todos os escopos  
  
## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como trabalhar com políticas, consulte:
-   [Políticas no Gerenciamento de API](api-management-howto-policies.md) 
-   [Expressões de política](api-management-policy-expressions.md)
