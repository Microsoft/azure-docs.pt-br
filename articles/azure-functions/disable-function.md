---
title: Como desabilitar funções no Azure Functions
description: Saiba como desabilitar e habilitar funções no Azure Functions 1.x e 2.x.
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
origin.date: 07/24/2018
ms.date: 08/31/2018
ms.author: v-junlch
ms.openlocfilehash: a32b4815a2716428ceeec034ddc5589e3aa062e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710551"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Como desabilitar funções no Azure Functions

Este artigo explica como desabilitar uma função no Azure Functions. *Desabilitar* significa fazer com que o tempo de execução igore o gatilho automático que está definido para a função. A maneira de fazer isso depende da versão de tempo de execução e a linguagem de programação:

- Funções 1.x
  - Linguagens de script
  - Biblioteca de Classes C#
- Funções 2.x
  - Uma maneira para todos os idiomas
  - Modo opcional para bibliotecas de classes do C#

## <a name="functions-1x---scripting-languages"></a>Functions 1.x - linguagens de script

Para linguagens de script, como o script do C# e JavaScript, você deve usar a `disabled` propriedade do arquivo *Function. JSON* para informar para o tempo de execução não disparar uma função. Essa propriedade pode ser definida como `true` ou o nome de uma configuração de aplicativo:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
ou o 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

No segundo exemplo, a função está desabilitada quando há uma configuração de aplicativo chamada IS_DISABLED e é definida como `true` ou 1.

Você pode editar o arquivo no portal do Azure ou usar o **Estado da Função** ative a função da guia **Gerenciar**. A opção portal funciona, alterando o arquivo *function.json*.

![Alternar o estado da Função](./media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Functions 1.x - bibliotecas de classes do C#

Em uma biblioteca de classes das Functions 1.x, você deve usar um `Disable` atributo para impedir que uma função que está sendo disparado. Você pode usar o atributo sem um parâmetro de construtor, conforme mostrado no exemplo a seguir:

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

O atributo sem parâmetro de construtor exige que você recompilar e reimplanta o projeto para alterar o estado de desabilitado da função. Uma maneira mais flexível para usar o atributo deve incluir um parâmetro de construtor que se refere a uma configuração de aplicativo booliano, conforme mostrado no exemplo a seguir:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Esse método permite habilitar e desabilitar a função, alterando a configuração de aplicativo, sem recompilar ou reinstalar. Alterar uma configuração de aplicativo faz com que o aplicativo de funções seja reiniciado, portanto, a alteração de estado desabilitado é reconhecida imediatamente.

> [!IMPORTANT]
> O `Disabled` atributo é a única maneira de desabilitar uma função de biblioteca de classe. Gerado o arquivo *function. JSON* para uma função de biblioteca de classe não se destina a ser editado diretamente. Se você editar esse arquivo, tudo o que faria para a `disabled` propriedade não terá efeito.
>
> O mesmo vale para o **estado de função** ative a guia **Gerenciar**, assim que funcionar, alterando o arquivo *Function. JSON*.
>
> Além disso, observe que o portal pode indicar que a função está desabilitada quando não estiver.



## <a name="functions-2x---all-languages"></a>Funtions 2.x - todos os idiomas

No Functions 2.x você desativa uma função usando uma configuração de aplicativo. Por exemplo, para desabilitar uma função chamada `QueueTrigger`, você cria uma configuração de aplicativo denominada `AzureWebJobs.QueueTrigger.Disabled`e defina-o como `true`. Para habilitar a função, defina a configuração do aplicativo como `false`. Você também pode usar o **Estado da Função** ativar a guia **Gerenciar** da função. A opção funciona criando e excluindo o `AzureWebJobs.<functionname>.Disabled` configuração de aplicativo.

![Alternar o estado da Função](./media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Functions 2.x - bibliotecas de classes do C#

Em uma biblioteca de classes do Functions 2.x, é recomendável que você use o método que funciona para todos os idiomas. Mas se você preferir, você pode [usar o atributo desabilitar como no Functions 1.x](#functions-1x---c-class-libraries).

## <a name="next-steps"></a>Próximas etapas

Este artigo trata-se como desabilitar disparadores automáticos. Para obter mais informações, consulte [Gatilhos e associações de armazenamento de Blobs](functions-triggers-bindings.md).

