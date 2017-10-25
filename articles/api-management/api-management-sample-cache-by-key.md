---
title: Cache personalizado no Gerenciamento de API do Azure
description: Saiba como armazenar em cache os itens por chave no gerenciamento de API do Azure
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 4a41e4e0be44e855ead253ad76fe5a3af52070ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="custom-caching-in-azure-api-management"></a>Cache personalizado no Gerenciamento de API do Azure
O serviço de gerenciamento de API do Azure tem suporte interno para [Cache de resposta HTTP](api-management-howto-cache.md) usando a URL de recurso como chave. A chave pode ser modificada por cabeçalhos de solicitação usando as propriedades do `vary-by` . Isso é útil para armazenar em cache respostas HTTP inteiras (também conhecido como representações), mas às vezes é útil para armazenar apenas uma parte de uma representação. As novas políticas [cache-lookup-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) e [cache-store-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) fornecem a capacidade de armazenar e recuperar partes arbitrárias de dados de dentro das definições da política. Essa capacidade também adiciona valor à política anteriormente introduzida [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) porque você agora pode armazenar em cache as respostas de serviços externos.

## <a name="architecture"></a>Arquitetura
O serviço de Gerenciamento de API usa um cache de dados compartilhados por locatário para que, à medida que você escalar verticalmente para várias unidades, ainda tenha acesso ao mesmo dados em cache. No entanto, ao trabalhar com uma implantação de várias regiões, existem caches independentes dentro de cada uma das regiões. Por isso, é importante não tratar o cache como um armazenamento de dados, em que ele é a única fonte de alguma informação. Se posteriormente você decidir se beneficiar da implantação de várias regiões, clientes com usuários que viajam podem perder o acesso aos dados armazenados em cache.

## <a name="fragment-caching"></a>Cache de fragmento
Há alguns casos nos quais respostas sendo retornadas contêm uma parte dos dados que é cara de determinar e ainda permanece atualizada por um período razoável. Por exemplo, considere um serviço criado por uma companhia aérea que fornece informações relacionadas a reservas de voo, status de voo, etc. Se o usuário for um membro do programa de pontos da companhia aérea, ele também teria informações relacionadas ao seu status atual e milhas acumuladas. Essas informações relacionadas ao usuário podem ser armazenadas em um sistema diferente, mas pode ser desejável incluí-las em respostas retornadas sobre reservas e o status de voo. Isso pode ser feito usando um processo chamado cache fragmentado. A representação principal pode ser retornada do servidor de origem usando algum tipo de token para indicar onde as informações relacionadas ao usuário devem ser inseridas. 

Considere a seguinte resposta JSON de uma API de back-end.

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

E o recursos secundário em `/userprofile/{userid}` , semelhante.

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Para determinar as informações de usuário apropriadas a incluir, precisamos identificar quem é o usuário final. Esse mecanismo é dependente da implementação. Por exemplo, estou usando a declaração `Subject` de um token `JWT`. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

Armazenamos o valor `enduserid` em uma variável de contexto para uso posterior. A próxima etapa é determinar se uma solicitação anterior já recuperou as informações do usuário e armazenou-a no cache. Para fazer isso, usamos a política `cache-lookup-value` .

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Se não houver nenhuma entrada no cache que corresponda ao valor da chave, então, nenhuma variável de contexto `userprofile` será criada. Podemos verificar o êxito da pesquisa usando a política de fluxo de controle `choose` .

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Se a variável de contexto `userprofile` não existir, teremos de fazer uma solicitação HTTP para recuperá-la.

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

Usamos o `enduserid` para construir a URL para o recurso de perfil do usuário. Assim que tivermos a resposta, podemos extrair o texto do corpo de resposta e armazená-lo em uma variável de contexto.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Para evitar fazer essa solicitação HTTP novamente, quando o mesmo usuário fizer outra solicitação, podemos armazenar o perfil do usuário no cache.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

Armazenamos o valor em cache usando a mesma chave exata com qual tentamos originalmente recuperá-la. A duração que escolhemos para armazenar o valor deve ser baseada na frequência das alterações e no nível de tolerância dos usuários a informações desatualizadas. 

É importante perceber que recuperar do cache ainda é uma solicitação de rede fora do processo e, potencialmente, pode ainda adicionar dezenas de milissegundos à solicitação. Os benefícios são fornecidos ao determinar que as informações de perfil do usuário demoram muito mais, devido à necessidade de fazer consultas em banco de dados de consultas ou agregar informações de vários back-ends.

A etapa final do processo é atualizar a resposta retornada com nossas informações de perfil do usuário.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Optei por incluir aspas como parte do token, para que, mesmo que a substituição não ocorra, a resposta seja um JSON válido. Isso era, principalmente, para facilitar a depuração.

Depois que você combina todas essas etapas, o resultado final é uma política semelhante à mostrada a seguir.

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If we don’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

Essa abordagem de cache é usada principalmente em sites da Web em que o HTML é composto no lado do servidor para que possa ser renderizado como uma única página. No entanto, ela também pode ser útil em APIs em que os clientes não podem realizar cache HTTP de cliente ou é desejável não colocar a responsabilidade no cliente.

Esse tipo de cache de fragmento também pode ser feito nos servidores back-end da Web, usando um servidor de cache Redis, no entanto, o uso do serviço de gerenciamento de API para realizar esse trabalho é útil quando os fragmentos em cache são provenientes de back-ends diferentes das respostas principais.

## <a name="transparent-versioning"></a>Controle de versão transparente
É uma prática comum para várias versões de implementação diferentes de uma API, ter suporte a qualquer momento. Isso, talvez, é para dar suporte a ambientes diferentes, como desenvolvimento, teste, produção, etc., ou pode dar suporte a versões mais antigas da API e dar tempo aos consumidores da API para migrarem para versões mais recentes. 

Uma abordagem para lidar com isso, em vez de exigir que os desenvolvedores de cliente alterem as URLs de `/v1/customers` para `/v2/customers`, é armazenar dados de perfil do consumidor, cuja versão da API eles desejam usar e chamar a URL de back-end apropriada. Para determinar a URL correta de back-end para chamar determinado cliente, é necessário consultar alguns dados de configuração. Ao armazenar em cache esses dados de configuração, podemos minimizar a degradação do desempenho ao fazer essa pesquisa.

A primeira etapa é determinar o identificador usado para configurar a versão desejada. Neste exemplo, optei por associar a versão à chave de assinatura do produto. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

Em seguida, fazemos uma pesquisa no cache para ver se já recuperamos a versão de cliente desejada.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Em seguida, verificamos se não a encontramos no cache.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Se não, nós a recuperamos.

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

Extraia o texto do corpo de resposta da resposta.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Armazene-o no cache para uso futuro.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

E, finalmente, atualize a URL do back-end para selecionar a versão de serviço desejada pelo cliente.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

A política completa é conforme segue.

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If we don’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

Habilitar consumidores da API a controlar, de forma transparente, qual versão de back-end está sendo acessado por clientes sem a necessidade de atualizar e reimplantar clientes é uma solução elegante que resolve muitas preocupações de controle de versão de API.

## <a name="tenant-isolation"></a>Isolamento de locatário
Em implantações maiores, de vários locatários, algumas empresas criar grupos separados de locatários em implantações distintas do hardware de back-end. Isso minimiza o número de clientes afetados por um problema de hardware no back-end. Também permite que novas versões de software sejam implementadas em estágios. O ideal é que essa arquitetura de back-end seja transparente para os consumidores de API. Isso pode ser obtido de forma semelhante ao controle de versão transparente porque baseia-se na mesma técnica de manipular a URL do back-end usando o estado de configuração por chave de API.  

Em vez de retornar uma versão de preferência da API para cada chave de assinatura, você retornará um identificador que relaciona um locatário ao grupo de hardware atribuído. Esse identificador pode ser usado para construir a URL de back-end apropriada.

## <a name="summary"></a>Resumo
A liberdade de usar o cache de gerenciamento de API do Azure para armazenar qualquer tipo de dados permite acesso eficiente aos dados de configuração que podem afetar a maneira pela qual uma solicitação de entrada é processada. Ele também pode ser usado para armazenar fragmentos de dados que podem aumentar as respostas retornadas de uma API de back-end.

## <a name="next-steps"></a>Próximas etapas
Envie-nos seus comentários no thread de discussão para este tópico, se houver outros cenários que essas políticas habilitaram para você, ou se houver cenários que você gostaria de ter, mas não sente que é possível no momento.

