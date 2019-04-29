---
title: Trabalhar com proxies no Azure Functions | Microsoft Docs
description: Visão geral de como usar Proxies do Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 2fbf29385b9a14cf5d4a9df621f0767a32079587
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020973"
---
# <a name="work-with-azure-functions-proxies"></a>Trabalhe com Proxies do Azure Functions

Este artigo explica como configurar e trabalhar com proxies do Azure Functions. Com esse recurso, você pode especificar os pontos de extremidade em seu aplicativo de funções que são implementados por outro recurso. Você pode usar esses proxies para dividir uma API grande em vários aplicativos de função (como uma arquitetura de microsserviços), enquanto ainda apresenta uma única superfície de API para clientes.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> A cobrança do Standard Functions se aplica para execuções de proxy. Para saber mais, confira [Preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Criar um proxy

Esta seção mostra como criar um proxy no portal do Functions.

1. Abra o [Portal do Azure] e navegue até seu aplicativo de funções.
2. No painel esquerdo, selecione **Novo proxy**.
3. Forneça um nome para seu proxy.
4. Configurar o ponto de extremidade exposto no aplicativo de função especificando o **modelo de rota** e **Métodos HTTP**. Esses parâmetros se comportam de acordo com as regras de [gatilhos HTTP].
5. Defina a **URL de back-end** para outro ponto de extremidade. Esse ponto de extremidade pode ser uma função em outro aplicativo de funções, ou pode ser qualquer outra API. O valor não precisa ser estático e pode fazer referência as [configurações do aplicativo] e os [parâmetros da solicitação original do cliente].
6. Clique em **Criar**.

Seu proxy agora existe como um novo ponto de extremidade em seu aplicativo de funções. Da perspectiva do cliente, é equivalente a um HttpTrigger no Azure Functions. Você pode testar seu novo proxy copiando a URL do Proxy e testá-lo com seu cliente HTTP favorito.

## <a name="modify-requests-responses"></a>Modificar solicitações e respostas

Com Proxies do Azure Functions, você pode modificar solicitações e respostas do back-end. Essas transformações podem usar variáveis, conforme definido em [Usar variáveis].

### <a name="modify-backend-request"></a>Modificar a solicitação de back-end

Por padrão, a solicitação de back-end é inicializada como uma cópia da solicitação original. Além de definir a URL de back-end, é possível fazer alterações no método HTTP, cabeçalhos e parâmetros de cadeia de consulta. Os valores modificados podem referenciar as [configurações do aplicativo] e os [parâmetros da solicitação original do cliente].

As solicitações de back-end podem ser modificadas no portal expandindo a seção *substituição da solicitação* na página de detalhes do proxy. 

### <a name="modify-response"></a>Modificar a resposta

Por padrão, a resposta do cliente é inicializada como uma cópia da resposta de back-end. Você pode fazer alterações no código de status, na frase de motivo, nos cabeçalhos e no corpo da resposta. Os valores modificados podem referenciar as [configurações do aplicativo], os [parâmetros da solicitação original do cliente] e os [parâmetros da resposta de back-end].

As solicitações de back-end podem ser modificadas no portal expandindo a seção *substituição da resposta* na página de detalhes do proxy. 

## <a name="using-variables"></a>Usar variáveis

A configuração de um proxy não precisa ser estática. Você pode condicioná-la para usar variáveis da solicitação do cliente original, da resposta de back-end ou das configurações do aplicativo.

### <a name="reference-localhost"></a>Funções de local de referência
Você pode usar `localhost` para fazer referência a uma função diretamente dentro do mesmo aplicativo de função, sem uma solicitação de proxy de ida e volta.

`"backendurl": "https://localhost/api/httptriggerC#1"` fará referência a uma função disparada do HTTP local na rota`/api/httptriggerC#1`

 
>[!Note]  
>Se sua função usar os níveis de autorização *função, administrador ou sys*, você precisará fornecer o código e o clientId, de acordo com a URL da função original. Nesse caso, a referência seria semelhante a: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"`

### <a name="request-parameters"></a>Parâmetros de solicitação de referência

Use os parâmetros de solicitação como entradas para a propriedade de URL de back-end ou como parte da modificação de solicitações e respostas. Alguns parâmetros podem ser limitados ao modelo de rota especificado na configuração do proxy base, enquanto outros são obtidos de propriedades da solicitação de entrada.

#### <a name="route-template-parameters"></a>Parâmetros de modelo de rota
Os parâmetros usados no modelo de rota estão disponíveis para serem referenciados pelo nome. Os nomes de parâmetro são colocados entre chaves ({}).

Por exemplo, se um proxy tem um modelo de rota como `/pets/{petId}`, a URL do back-end pode incluir o valor de `{petId}`, como em `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Se o modelo de rota termina em um caractere curinga, como `/api/{*restOfPath}`, o valor `{restOfPath}` será uma representação de cadeia de caracteres dos segmentos de caminho restantes da solicitação de entrada.

#### <a name="additional-request-parameters"></a>Parâmetros de solicitação adicionais
Além dos parâmetros do modelo de rota, os seguintes valores podem ser usados em valores de configuração:

* **{request.method}**: O método HTTP usado na solicitação original.
* **{request.headers.\<HeaderName\>}**: Um cabeçalho que pode ser lido por meio da solicitação original. Substitua *\<HeaderName\>* pelo nome do cabeçalho que você deseja ler. Se o cabeçalho não estiver incluído na solicitação, o valor será a cadeia de caracteres vazia.
* **{request.querystring.\<ParameterName\>}**: Um parâmetro de cadeia de caracteres de consulta que pode ser lido na solicitação original. Substitua *\<ParameterName\>* pelo nome do parâmetro que você deseja ler. Se o parâmetro não estiver incluído na solicitação, o valor será a cadeia de caracteres vazia.

### <a name="response-parameters"></a>Parâmetros de resposta de back-end de referência

Parâmetros de resposta podem ser usados como parte da modificação da resposta ao cliente. Os seguintes valores podem ser usados em valores de configuração:

* **{backend.response.statusCode}**: O código de status HTTP retornado na resposta de back-end.
* **{backend.response.statusReason}**: A frase de motivo HTTP retornada na resposta de back-end.
* **{backend.response.headers.\<HeaderName\>}**: Um cabeçalho que pode ser lido por meio da resposta de back-end. Substitua *\<HeaderName\>* pelo nome do cabeçalho que você deseja ler. Se o cabeçalho não estiver incluído na solicitação, o valor será a cadeia de caracteres vazia.

### <a name="use-appsettings"></a>Configurações do aplicativo de referência

Você também referenciar as [configurações do aplicativo definidas para o aplicativo de funções](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) envolvendo o nome da configuração entre sinais de percentual (%).

Por exemplo, uma URL de back-end de *https://%ORDER_PROCESSING_HOST%/api/orders* teria "%ORDER_PROCESSING_HOST%" substituído pelo valor da configuração ORDER_PROCESSING_HOST.

> [!TIP] 
> Usar configurações do aplicativo para hosts de back-end quando você tem várias implantações ou ambientes de teste. Dessa forma, você pode garantir que está sempre se comunicando com o back-end correto para aquele ambiente.

## <a name="debugProxies"></a>Solucionar problemas de Proxies

Ao adicionar o sinalizador `"debug":true` em qualquer proxy em seu `proxies.json`, você habilitará o log de depuração. Os logs são armazenados em `D:\home\LogFiles\Application\Proxies\DetailedTrace` e ficam acessíveis pelas ferramentas avançadas (kudu). As respostas HTTP também conterão um cabeçalho `Proxy-Trace-Location` com uma URL para acessar o arquivo de log.

Você pode depurar um proxy do lado do cliente adicionando um cabeçalho `Proxy-Trace-Enabled` definido como `true`. Isso também registrará um rastreamento no sistema de arquivos e retornará a URL de rastreamento como um cabeçalho na resposta.

### <a name="block-proxy-traces"></a>Bloquear rastreamentos de proxy

Por motivos de segurança, você pode não desejar permitir que qualquer pessoa chame o seu serviço para gerar um rastreamento. Essas pessoas não poderão acessar o conteúdo de rastreamento sem suas credenciais de logon, mas gerar o rastreamento consome recursos e expõe o seu uso dos Proxies de Função.

Desabilite totalmente os rastreamentos adicionando `"debug":false` a qualquer proxy específico em `proxies.json`.

## <a name="advanced-configuration"></a>Configuração avançada

Os proxies que você configura são armazenados em um arquivo *proxies.json*, que está localizado na raiz de um diretório de aplicativo de função. Você pode editar esse arquivo manualmente e implantá-lo como parte do seu aplicativo ao usar qualquer um dos [métodos de implantação](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) que ofereça suporte a funções. 

> [!TIP] 
> Se você não configurou um dos métodos de implantação, também poderá trabalhar com o arquivo *proxies.json* no portal. Vá até o aplicativo de funções e selecione **Recursos da plataforma** e,depois, selecione **Editor do Serviço de Aplicativo**. Isso permitirá que você veja toda a estrutura de arquivo do aplicativo de funções e faça alterações.

*Proxies.json* é definido por um objeto de proxies, composto de proxies nomeados e suas definições. Opcionalmente, você pode referenciar um [esquema JSON](http://json.schemastore.org/proxies) para o preenchimento do código, caso seu editor dê suporte a isso. Um arquivo de exemplo pode parecer com o seguinte:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Cada proxy tem um nome amigável, como *proxy1*, no exemplo acima. O objeto de definição de proxy correspondente é definido pelas seguintes propriedades:

* **matchCondition**: Obrigatório – um objeto que define as solicitações que disparam a execução desse proxy. Ele contém duas propriedades compartilhadas com [Gatilhos HTTP]:
    * _methods_: Uma matriz dos métodos HTTP aos quais o proxy responde. Se não for especificado, o proxy responderá a todos os métodos HTTP na rota.
    * _route_: Obrigatório – define o modelo da rota, controlando para quais URLs de solicitação seu proxy responde. Ao contrário de disparadores HTTP, não há nenhum valor padrão.
* **backendUri**: A URL do recurso de back-end ao qual a solicitação deve ser transmitida por proxy. Esse valor pode referenciar as configurações do aplicativo e os parâmetros da solicitação original do cliente. Se esta propriedade não for incluída, o Azure Functions responderá com um HTTP 200 OK.
* **requestOverrides**: Um objeto que define as transformações para a solicitação de back-end. Confira [Definir um objeto requestOverrides].
* **responseOverrides**: Um objeto que define as transformações para a resposta do cliente. Confira [Definir um objeto responseOverrides].

> [!NOTE] 
> A propriedade de *rota* dos proxies de funções do Azure não honra a propriedade *routePrefix* da configuração de host do Aplicativo de funções. Se você quiser incluir um prefixo, como `/api`, ele deve ser incluído na propriedade de *rota*.

### <a name="disableProxies"></a> Desabilitar proxies individuais

Você pode desabilitar proxies individuais adicionando `"disabled": true` ao proxy no arquivo `proxies.json`. Isso fará com que as solicitações atendam a matchCondition para retornar 404.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="applicationSettings"></a> Configurações de aplicativo

O comportamento do proxy pode ser controlado por várias configurações de aplicativo. Todas elas são descritas na [referência de Configurações do Aplicativo de Funções](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reservedChars"></a> Caracteres reservados (formatação de cadeia de caracteres)

Os proxies leem todas as cadeias de caracteres de um arquivo JSON, usando \ como símbolo de escape. Os proxies também interpretam chaves. Veja a seguir um conjunto completo de exemplos.

|Character|Caractere de escape|Exemplo|
|-|-|-|
|{ ou }|{{ ou }}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="requestOverrides"></a>Definir um objeto requestOverrides

O objeto requestOverrides define as alterações feitas à solicitação quando o recurso de back-end é chamado. O objeto é definido pelas seguintes propriedades:

* **backend.request.method**: O método HTTP usado para chamar o back-end.
* **backend.request.querystring.\<ParameterName\>**: Um parâmetro de cadeia de caracteres de consulta que pode ser definido para a chamada ao back-end. Substitua *\<ParameterName\>* pelo nome do parâmetro que você deseja definir. Se a cadeia de caracteres vazia for fornecida, o parâmetro não será incluído na solicitação de back-end.
* **backend.request.headers.\<HeaderName\>**: Um cabeçalho que pode ser definido para a chamada ao back-end. Substitua *\<HeaderName\>* pelo nome do cabeçalho que você deseja definir. Se você fornecer a cadeia de caracteres vazia, o cabeçalho não será incluído na solicitação de back-end.

Os valores podem referenciar as configurações do aplicativo e os parâmetros da solicitação original do cliente.

Uma configuração de exemplo pode ser parecida com a seguinte:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="responseOverrides"></a>Definir um objeto responseOverrides

O objeto requestOverrides define as alterações feitas à resposta passada novamente ao cliente. O objeto é definido pelas seguintes propriedades:

* **response.statusCode**: O código de status HTTP a ser retornado ao cliente.
* **response.statusReason**: A frase de motivo do HTTP a ser retornada ao cliente.
* **response.body**: A representação de cadeia de caracteres do corpo a ser retornada ao cliente.
* **response.headers.\<HeaderName\>**: Um cabeçalho que pode ser definido para a resposta ao cliente. Substitua *\<HeaderName\>* pelo nome do cabeçalho que você deseja definir. Se você fornecer a cadeia de caracteres vazia, o cabeçalho não será incluído na resposta.

Os valores podem referenciar as configurações do aplicativo, os parâmetros da solicitação original do cliente e os parâmetros da resposta de back-end.

Uma configuração de exemplo pode ser parecida com a seguinte:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> Neste exemplo, o corpo da resposta é definido diretamente e, portanto, nenhuma propriedade `backendUri` é necessária. O exemplo mostra como você pode usar os Proxies do Azure Functions para simular APIs.

[Portal do Azure]: https://portal.azure.com
[Gatilhos HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definir um objeto requestOverrides]: #requestOverrides
[Definir um objeto responseOverrides]: #responseOverrides
[configurações do aplicativo]: #use-appsettings
[Usar variáveis]: #using-variables
[parâmetros da solicitação original do cliente]: #request-parameters
[parâmetros da resposta de back-end]: #response-parameters
