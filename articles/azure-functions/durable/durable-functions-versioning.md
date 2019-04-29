---
title: Controle de Versão nas Funções Duráveis – Azure
description: Saiba como implementar o Controle de Versão na extensão de Funções Duráveis do Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: 33ca6c36cd11d53a3c50a8374181c511fd2f8c3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648120"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Controle de Versão nas Funções Duráveis (Azure Functions)

É inevitável que funções sejam adicionadas, removidas e alteradas ao longo do tempo de vida de um aplicativo. As [Funções Duráveis](durable-functions-overview.md) permitem encadear funções de maneiras que não eram possíveis anteriormente e esse encadeamento afeta a forma como você pode tratar o controle de versão.

## <a name="how-to-handle-breaking-changes"></a>Como lidar com as alterações que causam interrupção

Há vários exemplos de alterações que causam interrupção a serem considerados. Este artigo discute os mais comuns. O tema principal por trás delas é que tanto orquestrações de função novas quando as já existentes são afetadas por alterações no código da função.

### <a name="changing-activity-function-signatures"></a>Alteração nas assinaturas da função de atividade

Uma alteração de assinatura é uma alteração no nome, na entrada ou na saída de uma função. Se esse tipo de alteração for feita em uma função de atividade, ele poderá interromper a função de orquestrador que depende dela. Se atualizar a função de orquestrador para acomodar essa alteração, você poderá interromper instâncias existentes em curso.

Por exemplo, suponha que você tenha a seguinte função.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Essa função simples obtém os resultados de **Foo** e os transmite para **Bar**. Digamos que precisemos alterar o valor retornado de **Foo** de `bool` para `int` para dar suporte a uma variedade maior de valores de resultado. O resultado é semelhante a:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Essa alteração funciona bem para todas as novas instâncias da função de orquestrador, mas interrompe todas as instâncias em curso. Por exemplo, considere o caso em que uma instância de orquestração chama **Foo**, obtém de volta um valor booliano e, em seguida, faz uma verificação pontual. Se a alteração da assinatura for implantada nesse ponto, a instância que passou pela verificação falhará imediatamente quando for retomada e reproduzir a chamada para `context.CallActivityAsync<int>("Foo")`. Isso acontece porque o resultado na tabela de histórico é `bool`, mas o novo código tenta desserializá-lo para `int`.

Esta é apenas uma das várias maneiras diferentes que uma alteração de assinatura pode interromper instâncias existentes. De modo geral, se um orquestrador precisar alterar a forma como chama uma função, provavelmente a alteração será um problema.

### <a name="changing-orchestrator-logic"></a>Alterando a lógica do orquestrador

O outro tipo de problema de controle de versão é consequência de alterar o código da função de orquestrador de forma a confundir a lógica de reprodução para instâncias em curso.

Considere a seguinte função de orquestrador:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Agora, vamos supor que você queira fazer uma alteração aparentemente inocente para adicionar outra chamada de função.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

Essa alteração adiciona uma nova chamada de função a **SendNotification**, entre **Foo** e **Bar**. Não há nenhuma alteração de assinatura. O problema surge quando uma instância existente é retomada da chamada para **Bar**. Durante a reprodução, se a chamada original para **Foo** retornar `true`, a reprodução do orquestrador chamará **SendNotification**, que não está em seu histórico de execução. Como resultado, o Framework de Tarefa Durável falhará com um `NonDeterministicOrchestrationException`, porque encontrou uma chamada para **SendNotification** quando esperava encontrar uma chamada para **Bar**.

## <a name="mitigation-strategies"></a>Estratégias de mitigação

Veja algumas das estratégias para lidar com desafios de controle de versão:

* Não fazer nada
* Parar todas as instâncias em curso
* Implantações lado a lado

### <a name="do-nothing"></a>Não fazer nada

A maneira mais fácil de lidar com uma alteração que causa interrupção é deixar que as instâncias de orquestração em curso falhem. Novas instâncias executarão com êxito o código alterado.

O quanto isso é um problema depende da importância de suas instâncias em curso. Se você estiver em um desenvolvimento ativo e não se preocupar com instâncias em curso, isso pode ser suficiente. No entanto, você precisará lidar com exceções e erros em seu pipeline de diagnóstico. Se quiser evitar essas coisas, considere as outras opções de controle de versão.

### <a name="stop-all-in-flight-instances"></a>Parar todas as instâncias em curso

Outra opção é parar todas as instâncias em curso. Isso pode ser feito limpando o conteúdo da **fila de controle** e da **fila de item de trabalho** internas. As instâncias ficarão para sempre presas onde estão, mas não poluirão sua telemetria com mensagens de falha. Isso é ideal no desenvolvimento rápido de protótipos.

> [!WARNING]
> Os detalhes dessas filas podem mudar ao longo do tempo, portanto, não use essa técnica para cargas de trabalho de produção.

### <a name="side-by-side-deployments"></a>Implantações lado a lado

A maneira mais infalível de garantir que alterações que causam interrupção sejam implantadas com segurança é implantá-las lado a lado com as versões mais antigas. Isso pode ser feito usando qualquer uma das seguintes técnicas:

* Implantar todas as atualizações como funções totalmente novas (novos nomes).
* Implantar todas as atualizações como um novo aplicativo de funções com uma conta de armazenamento diferente.
* Implantar uma nova cópia do aplicativo de funções, mas com um nome `TaskHub` atualizado. Essa é a técnica recomendada.

### <a name="how-to-change-task-hub-name"></a>Como alterar o nome do hub de tarefas

O hub de tarefas pode ser configurado no arquivo *host.json* da seguinte forma:

#### <a name="functions-1x"></a>Funções 1.x

```json
{
    "durableTask": {
        "HubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-2x"></a>Funções 2.x

O valor padrão é `DurableFunctionsHub`.

Todas as entidades do Armazenamento do Azure são nomeadas com base no valor de configuração `HubName`. Fornecendo um novo nome ao hub de tarefas, você garante que filas e uma tabela de histórico diferentes sejam criadas para a nova versão de seu aplicativo.

É recomendável implantar a nova versão do aplicativo de funções em um novo [Slot de implantação](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Os slots de implantação permitem que você execute várias cópias de seu aplicativo de funções lado a lado, com apenas uma delas como o slot de *produção* ativo. Quando você estiver pronto para expor a nova lógica de orquestração para sua infraestrutura existente, isso pode ser tão simples quanto colocar a nova versão no slot de produção.

> [!NOTE]
> Essa estratégia funciona melhor quando você usa gatilhos de webhook e HTTP para funções de orquestrador. Para os gatilhos não HTTP, como filas ou Hubs de eventos, a definição do gatilho deve [derivam de uma configuração de aplicativo](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) que seja atualizada como parte da operação de troca.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como lidar com problemas de desempenho e escala](durable-functions-perf-and-scale.md)
