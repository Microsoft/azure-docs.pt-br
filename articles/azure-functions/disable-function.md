---
title: Como desabilitar funções no Azure Functions
description: Saiba como desabilitar e habilitar funções no Azure Functions.
ms.topic: conceptual
ms.date: 02/03/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: cbb84308507ea15f1c44c00122a9a59472f12a88
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551036"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Como desabilitar funções no Azure Functions

Este artigo explica como desabilitar uma função no Azure Functions. *Desabilitar* uma função significa fazer com que o runtime ignore o gatilho automático que está definido para a função. Isso permite impedir que uma função específica seja executada sem interromper todo o aplicativo de funções.

A maneira recomendada para desabilitar uma função é com uma configuração de aplicativo no formato `AzureWebJobs.<FUNCTION_NAME>.Disabled` definido como `true` . Você pode criar e modificar essa configuração de aplicativo de várias maneiras, incluindo usando o [CLI do Azure](/cli/azure/) e da guia **visão geral** da função na [portal do Azure](https://portal.azure.com). 

> [!NOTE]  
> Quando você desabilita uma função disparada por HTTP usando os métodos descritos neste artigo, o ponto de extremidade ainda pode ser acessado se estiver em execução no computador local.  

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

Na CLI do Azure, use o comando [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) para criar e modificar a configuração do aplicativo. O comando a seguir desabilita uma função chamada `QueueTrigger` criando uma configuração de aplicativo chamada `AzureWebJobs.QueueTrigger.Disabled` e definindo-a como `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Para habilitar novamente a função, execute o mesmo comando outra vez com um valor de `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Uso do Portal

Use os botões **Habilitar** e **Desabilitar** na página **Visão geral** da função. Esses botões funcionam alterando o valor da `AzureWebJobs.<FUNCTION_NAME>.Disabled` configuração do aplicativo. Essa configuração específica de função é criada na primeira vez que ela é desabilitada. 

![Alternar o estado da Função](media/disable-function/function-state-switch.png)

Mesmo quando você publica em seu aplicativo de funções de um projeto local, ainda é possível usar o portal para desabilitar funções no aplicativo de funções. 

> [!NOTE]  
> A funcionalidade de teste integrada ao portal ignora a configuração `Disabled`. Isso significa que uma função desabilitada continua sendo executada quando é iniciada na janela **Testar** do portal. 

## <a name="localsettingsjson"></a>local.settings.json

As funções podem ser desabilitadas da mesma maneira quando são executadas localmente. Para desabilitar uma função chamada `HttpExample` , adicione uma entrada à coleção valores no local.settings.jsno arquivo, da seguinte maneira:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

## <a name="other-methods"></a>Outros métodos

Embora o método de configuração de aplicativo seja recomendado para todas as linguagens e todas as versões de runtime, existem várias outras maneiras de desabilitar funções. Esses métodos, que variam por linguagem e versão de runtime, são mantidos para fins de compatibilidade com versões anteriores. 

### <a name="c-class-libraries"></a>Biblioteca de Classes C#

Também é possível usar o atributo `Disable` para impedir que a função seja disparada em uma função da biblioteca de classes. Esse atributo permite que você personalize o nome da configuração usada para desabilitar a função. Use a versão do atributo que permite definir um parâmetro de construtor que se refere a uma configuração de aplicativo booliana, conforme mostrado no exemplo a seguir:

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

Também há um construtor para o parâmetro que não aceita uma cadeia de caracteres para o nome da configuração. Esta versão do atributo não é recomendada. Se você usar essa versão, deverá recompilar e reimplantar o projeto para alterar o estado de desabilitado da função.

### <a name="functions-1x---scripting-languages"></a>Functions 1.x - linguagens de script

Na versão 1.x, você também pode usar a propriedade `disabled` do arquivo *function.json* para dizer ao runtime para não disparar uma função. Esse método funciona apenas para linguagens de script, como script de C# e JavaScript. A propriedade `disabled` pode ser definida como `true` ou com o nome de uma configuração de aplicativo:

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
ou 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

No segundo exemplo, a função está desabilitada quando há uma configuração de aplicativo chamada IS_DISABLED e é definida como `true` ou 1.

>[!IMPORTANT]  
>O portal usa as configurações do aplicativo para desabilitar as funções v1. x. Pode ocorrer um erro quando uma configuração de aplicativo apresenta um conflito com o arquivo function.json. Remova a propriedade `disabled` do arquivo function.json para evitar erros. 


## <a name="next-steps"></a>Próximas etapas

Este artigo trata-se como desabilitar disparadores automáticos. Para obter mais informações, consulte [Gatilhos e associações de armazenamento de Blobs](functions-triggers-bindings.md).
