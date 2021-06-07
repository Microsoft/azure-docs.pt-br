---
title: Azure Functions associações de saída HTTP
description: Saiba como retornar respostas HTTP no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84697435"
---
# <a name="azure-functions-http-output-bindings"></a>Azure Functions associações de saída HTTP

Use a associação de saída HTTP para responder ao remetente da solicitação HTTP. Essa associação exige um gatilho HTTP e permite que você personalize a resposta associada à solicitação do gatilho.

O valor de retorno padrão para uma função disparada por HTTP é:

- `HTTP 204 No Content` com um corpo vazio no Functions 2.x e superior
- `HTTP 200 OK` com um corpo vazio no Functions 1.x

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json*. Para as bibliotecas de classes C#, não há propriedades de atributo que correspondem a essas propriedades do *function.json*.

|Propriedade  |Descrição  |
|---------|---------|
| **tipo** |Deve ser definido como `http`. |
| **direction** | Deve ser definido como `out`. |
| **name** | O nome da variável usada no código de função para a resposta, ou `$return` para usar o valor de retorno. |

## <a name="usage"></a>Uso

Para enviar uma resposta HTTP, use os padrões de resposta padrão do idioma. Em c# ou script c#, faça o tipo de retorno da função retorno `IActionResult` ou `Task<IActionResult>`. Em c#, um atributo de valor de retorno não é necessário.

Por ver exemplos de respostas, confira o [exemplo de gatilho](./functions-bindings-http-webhook-trigger.md#example).

## <a name="hostjson-settings"></a>configurações de host.json

Esta seção descreve as definições de configuração global disponíveis para essa associação nas versões 2. x e superior. O exemplo host.jsno arquivo abaixo contém apenas as configurações da versão 2. x + para essa associação. Para obter mais informações sobre definições de configuração global nas versões 2. x e posteriores, consulte [host.jsem referência para Azure Functions](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host.json no Functions 1.x, confira [Referência de host.json para o Azure Functions 1.x](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
| customHeaders|nenhum|Permite que você defina cabeçalhos personalizados na resposta HTTP. O exemplo anterior adiciona o `X-Content-Type-Options` cabeçalho à resposta para evitar a detecção de tipo de conteúdo. |
|dynamicThrottlesEnabled|true<sup>\*</sup>|Quando habilitada, essa configuração faz com que o pipeline de processamento de solicitações Verifique periodicamente os contadores de desempenho do sistema como `connections/threads/processes/memory/cpu/etc` e se algum desses contadores está acima de um limite alto interno (80%), as solicitações serão rejeitadas com uma `429 "Too Busy"` resposta até que os contadores retornem aos níveis normais.<br/><sup>\*</sup>O padrão em um plano de consumo é `true` . O padrão em um plano dedicado é `false` .|
|hsts|não habilitado|Quando `isEnabled` é definido como `true` , o [comportamento de HSTS (segurança de transporte estrito) de http do .NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) é imposto, conforme definido na [ `HstsOptions` classe](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). O exemplo acima também define a [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) propriedade como 10 dias. As propriedades com suporte do `hsts` são: <table><tr><th>Propriedade</th><th>Descrição</th></tr><tr><td>excludedHosts</td><td>Uma matriz de cadeia de caracteres de nomes de host para a qual o cabeçalho HSTS não é adicionado.</td></tr><tr><td>includeSubDomains</td><td>Valor booliano que indica se o parâmetro includeSubDomain do cabeçalho Strict-Transport-Security está habilitado.</td></tr><tr><td>Período</td><td>Cadeia de caracteres que define o parâmetro Max-age do cabeçalho Strict-Transport-Security.</td></tr><tr><td>pré-carregar</td><td>Booliano que indica se o parâmetro PreLoad do cabeçalho Strict-Transport-Security está habilitado.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|O número máximo de funções HTTP que são executadas em paralelo. Esse valor permite que você controle a simultaneidade, o que pode ajudar a gerenciar a utilização de recursos. Por exemplo, você pode ter uma função HTTP que usa um grande número de recursos do sistema (memória/CPU/soquetes), de modo que isso cause problemas quando a simultaneidade for muito alta. Ou você pode ter uma função que faça solicitações de saída para um serviço de terceiros, e essas chamadas precisam ser limitadas por taxa. Nesses casos, aplicar uma limitação aqui pode ajudar. <br/><sup>*</sup>O padrão para um plano de consumo é 100. O padrão para um plano dedicado é não associado ( `-1` ).|
|maxOutstandingRequests|200<sup>\*</sup>|O número máximo de solicitações pendentes mantidas em um dado momento. Esse limite inclui solicitações que estão na fila, mas não iniciaram a execução, bem como qualquer execução em andamento. Quaisquer solicitações recebidas acima desse limite são rejeitadas com uma resposta "Muito ocupado" 429. Isso permite que os chamadores empreguem estratégias de repetição com base em tempo e também ajuda você a controlar as latências máximas de solicitação. Isso controla apenas o enfileiramento que ocorre no caminho de execução do host de script. Outras filas, como a fila de solicitação ASP.NET, ainda estarão vigor e não serão afetadas por essa configuração. <br/><sup>\*</sup>O padrão para um plano de consumo é 200. O padrão para um plano dedicado é não associado ( `-1` ).|
|routePrefix|api|O prefixo da rota que se aplica a todas as rotas. Use uma cadeia de caracteres vazia para remover o prefixo padrão. |

## <a name="next-steps"></a>Próximas etapas

- [Executar uma função de uma solicitação HTTP](./functions-bindings-http-webhook-trigger.md)