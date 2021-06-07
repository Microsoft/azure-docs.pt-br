---
title: Gatilhos e associações do armazenamento de filas do Azure para Azure Functions visão geral
description: Entenda como usar o gatilho do armazenamento de fila do Azure e a associação de saída no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: a1b9d03da29b7c89055303fa97fc38c2ef734b23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381470"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Gatilhos e associações do armazenamento de filas do Azure para Azure Functions visão geral

Azure Functions pode executar como novas mensagens de armazenamento de fila do Azure são criadas e podem gravar mensagens de fila em uma função.

| Ação | Type |
|---------|---------|
| Executar uma função como armazenamento de filas alterações de dados | [Gatilho](./functions-bindings-storage-queue-trigger.md) |
| Gravar mensagens de armazenamento de fila |[Associação de saída](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Adicionar ao seu aplicativo de funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e posteriores

Trabalhar com o gatilho e as associações exige que você referencie o pacote apropriado. O pacote NuGet é usado para bibliotecas de classes do .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicativos.

| Idioma                                        | Adicionar por...                                   | Comentários 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalando o [pacote NuGet], versão 3. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrando o [pacote de extensão]          | A [extensão de ferramentas do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) é recomendada para uso com Visual Studio Code. |
| Script C# (somente online em portal do Azure)         | Adicionando uma associação                            | Para atualizar as extensões de associação existentes sem precisar republicar seu aplicativo de funções, consulte [atualizar suas extensões]. |

#### <a name="storage-extension-5x-and-higher"></a>Extensão de armazenamento 5. x e superior

Uma nova versão da extensão de associações de armazenamento está disponível como um [pacote NuGet de visualização](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2). Essa visualização apresenta a capacidade de [se conectar usando uma identidade em vez de um segredo](./functions-reference.md#configure-an-identity-based-connection). Para aplicativos .NET, ele também altera os tipos aos quais você pode associar, substituindo os tipos de `WindowsAzure.Storage` e `Microsoft.Azure.Storage` por tipos mais recentes de [Azure. Storage. Queues](/dotnet/api/azure.storage.queues).

> [!NOTE]
> O pacote de visualização não está incluído em um pacote de extensão e deve ser instalado manualmente. Para aplicativos .NET, adicione uma referência ao pacote. Para todos os outros tipos de aplicativos, consulte [atualizar suas extensões].

[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Atualizar suas extensões]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

Os aplicativos do Functions 1. x têm automaticamente uma referência ao pacote NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versão 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>configurações de host.json

Esta seção descreve as definições de configuração global disponíveis para essa associação nas versões 2. x e superior. O exemplo *host.jsno* arquivo abaixo contém apenas as configurações da versão 2. x + para essa associação. Para obter mais informações sobre definições de configuração global nas versões 2. x e posteriores, consulte [host.jsem referência para Azure Functions](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host.json no Functions 1.x, confira [Referência de host.json para o Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------|
|maxPollingInterval|00:00:01|O intervalo máximo entre as sondagens de fila. O mínimo é 00:00:00.100 (100 ms) e incrementa até 00:01:00 (1 min).  No functions 2. x e superior, o tipo de dados é a `TimeSpan` , enquanto na versão 1. x, em milissegundos.|
|visibilityTimeout|00:00:00|O intervalo de tempo entre as repetições quando o processamento de uma mensagem falha. |
|batchSize|16|O número de mensagens em fila que o runtime de Funções recupera simultaneamente e processa em paralelo. Quando o número que está sendo processado chega até `newBatchThreshold`, o runtime obtém outro lote e começa a processar as mensagens. Portanto, o número máximo de mensagens simultâneas que estão sendo processadas por função é `batchSize` mais `newBatchThreshold`. Esse limite se aplica separadamente a cada função acionada por fila. <br><br>Se quiser evitar uma execução paralela para mensagens recebidas em uma fila, é possível definir `batchSize` como 1. No entanto, essa configuração elimina a simultaneidade, desde que seu aplicativo de funções seja executado somente em uma única máquina virtual (VM). Se o aplicativo de função se expande para várias VMs, cada VM pode executar uma instância de cada função acionada por fila.<br><br>O máximo `batchSize` é 32. |
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de movê-la para a fila de mensagens suspeitas.|
|newBatchThreshold|batchSize/2|Sempre que o número de mensagens processadas simultaneamente chega a esse número, o runtime recupera outro lote.|
|messageEncoding|base64| Essa configuração só está disponível na [versão de extensão 5.0.0 e superior](#storage-extension-5x-and-higher). Ele representa o formato de codificação para mensagens. Os valores válidos são `base64` e `none`.|

## <a name="next-steps"></a>Próximas etapas

- [Executar uma função como armazenamento de fila alterações de dados (gatilho)](./functions-bindings-storage-queue-trigger.md)
- [Gravar mensagens de armazenamento de fila (Associação de saída)](./functions-bindings-storage-queue-output.md)
