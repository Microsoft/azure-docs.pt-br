---
title: Azure Functions gatilho aquecimento
description: Entenda como usar o gatilho aquecimento no Azure Functions.
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, functions, processamento de eventos, aquecimento, Cold Start, Premium, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/08/2019
ms.author: cshoe
ms.openlocfilehash: ea418576ab8fe06964a61e48f16393e1a0566ce8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182240"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure Functions gatilho de aquecimento

Este artigo explica como trabalhar com o gatilho aquecimento no Azure Functions. Um gatilho aquecimento é invocado quando uma instância é adicionada para dimensionar um aplicativo de funções em execução. Você pode usar um gatilho aquecimento para pré-carregar dependências personalizadas durante o [processo de pré-teste](./functions-premium-plan.md#pre-warmed-instances) para que suas funções estejam prontas para iniciar solicitações de processamento imediatamente. 

> [!NOTE]
> O gatilho aquecimento não tem suporte para aplicativos de funções em execução em um plano de consumo.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes-funções 2. x e superior

O pacote NuGet [Microsoft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) , versão **3.0.5 ou superior** , é necessário. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/main/src/WebJobs.Extensions/Extensions/Warmup). 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Gatilho

O gatilho aquecimento permite que você defina uma função que será executada em uma nova instância quando ela for adicionada ao seu aplicativo em execução. Você pode usar uma função aquecimento para abrir conexões, carregar dependências ou executar qualquer outra lógica personalizada antes que seu aplicativo comece a receber tráfego. 

O gatilho aquecimento destina-se a criar dependências compartilhadas que serão usadas por outras funções em seu aplicativo. [Veja exemplos de dependências compartilhadas aqui](./manage-connections.md#client-code-examples).

Observe que o gatilho aquecimento é chamado apenas durante operações de expansão, não durante reinicializações ou outras inicializações sem escala. Você deve garantir que sua lógica possa carregar todas as dependências necessárias sem usar o gatilho aquecimento. O carregamento lento é um bom padrão para conseguir isso.

## <a name="trigger---example"></a>Gatilho - exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que será executada em cada nova instância quando for adicionada ao seu aplicativo. Um atributo de valor de retorno não é necessário.


* Sua função deve ser nomeada ```warmup``` (não diferencia maiúsculas de minúsculas) e pode haver apenas uma função aquecimento por aplicativo.
* Para usar o aquecimento como uma função de biblioteca de classes .NET, verifique se você tem uma referência de pacote para **Microsoft. Azure. webjobs. Extensions >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Os comentários de espaço reservado mostram onde o aplicativo deve declarar e inicializar dependências compartilhadas. 
[Saiba mais sobre dependências compartilhadas aqui](./manage-connections.md#client-code-examples).

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-script"></a>[Script do C#](#tab/csharp-script)


O exemplo a seguir mostra um gatilho aquecimento em um *function.jsno* arquivo e uma [função de script C#](functions-reference-csharp.md) que será executada em cada nova instância quando for adicionada ao seu aplicativo.

Sua função deve ser nomeada ```warmup``` (não diferencia maiúsculas de minúsculas) e pode haver apenas uma função aquecimento por aplicativo.

Aqui está o arquivo *function.json*:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

```cs
public static void Run(WarmupContext warmupContext, ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra um gatilho aquecimento em um *function.jsno* arquivo e uma [função JavaScript](functions-reference-node.md)  que será executada em cada nova instância quando ela for adicionada ao seu aplicativo.

Sua função deve ser nomeada ```warmup``` (não diferencia maiúsculas de minúsculas) e pode haver apenas uma função aquecimento por aplicativo.

Aqui está o arquivo *function.json*:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra um gatilho aquecimento em um *function.jsno* arquivo e uma [função do Python](functions-reference-python.md) que será executada em cada nova instância quando ela for adicionada ao seu aplicativo.

Sua função deve ser nomeada ```warmup``` (não diferencia maiúsculas de minúsculas) e pode haver apenas uma função aquecimento por aplicativo.

Aqui está o arquivo *function.json*:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra um gatilho aquecimento que é executado quando cada nova instância é adicionada ao seu aplicativo.

Sua função deve ser nomeada `warmup` (não diferencia maiúsculas de minúsculas) e pode haver apenas uma função aquecimento por aplicativo.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Gatilho – atributos

Em [bibliotecas de classes C#](functions-dotnet-class-library.md), o `WarmupTrigger` atributo está disponível para configurar a função.

# <a name="c"></a>[C#](#tab/csharp)

Este exemplo demonstra como usar o atributo [aquecimento](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) .

Observe que sua função deve ser chamada ```Warmup``` e pode haver apenas uma função aquecimento por aplicativo.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Para ver um exemplo completo, confira o [exemplo de gatilho](#trigger---example).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

O gatilho aquecimento não tem suporte em Java como um atributo.

---

## <a name="trigger---configuration"></a>Gatilho – configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `WarmupTrigger`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo** | n/d| Obrigatório – deve ser definido como `warmupTrigger`. |
| **direction** | n/d| Obrigatório – deve ser definido como `in`. |
| **name** | n/d| Required – o nome da variável usada no código de função.|

## <a name="trigger---usage"></a>Gatilho - uso

Nenhuma informação adicional é fornecida a uma função disparada por aquecimento quando ela é chamada.

## <a name="trigger---limits"></a>Gatilho - limites

* O gatilho aquecimento só está disponível para aplicativos em execução no [plano Premium](./functions-premium-plan.md).
* O gatilho aquecimento é chamado somente durante operações de expansão, não durante reinicializações ou outras inicializações sem escala. Você deve garantir que sua lógica possa carregar todas as dependências necessárias sem usar o gatilho aquecimento. O carregamento lento é um bom padrão para conseguir isso.
* O gatilho aquecimento não pode ser invocado depois que uma instância já está em execução.
* Só pode haver uma função de gatilho aquecimento por aplicativo de função.

## <a name="next-steps"></a>Próximas etapas

[Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
