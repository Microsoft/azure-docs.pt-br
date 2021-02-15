---
title: Gatilhos e associações do armazenamento de BLOBs do Azure para Azure Functions
description: Aprenda a usar o gatilho e as associações do armazenamento de BLOBs do Azure no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 4ec21086ee94610be1d9cf5da7b64c837b5311a9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381521"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Associações de armazenamento de BLOBs do Azure para Azure Functions visão geral

O Azure Functions integra-se com o [armazenamento do Azure](../storage/index.yml) por meio [de gatilhos e associações](./functions-triggers-bindings.md). A integração com o armazenamento de BLOBs permite que você crie funções que reajam a alterações em dados de BLOB, bem como valores de leitura e gravação.

| Ação | Digite |
|---------|---------|
| Executar uma função como alterações de dados do armazenamento de BLOBs | [Gatilho](./functions-bindings-storage-blob-trigger.md) |
| Ler dados do armazenamento de BLOBs em uma função | [Associação de entrada](./functions-bindings-storage-blob-input.md) |
| Permitir que uma função grave dados de armazenamento de BLOBs |[Associação de saída](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Adicionar ao seu aplicativo de funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e posteriores

Trabalhar com o gatilho e as associações exige que você referencie o pacote apropriado. O pacote NuGet é usado para bibliotecas de classes do .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicativos.

| Idioma                                        | Adicionar por...                                   | Comentários 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalando o [pacote NuGet], versão 3. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrando o [pacote de extensão]          | A [extensão de ferramentas do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) é recomendada para uso com Visual Studio Code. |
| Script C# (somente online em portal do Azure)         | Adicionando uma associação                            | Para atualizar as extensões de associação existentes sem precisar republicar seu aplicativo de funções, consulte [atualizar suas extensões]. |

#### <a name="storage-extension-5x-and-higher"></a>Extensão de armazenamento 5. x e superior

Uma nova versão da extensão de associações de armazenamento está disponível como um [pacote NuGet de visualização](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2). Essa visualização apresenta a capacidade de [se conectar usando uma identidade em vez de um segredo](./functions-reference.md#configure-an-identity-based-connection). Para aplicativos .NET, ele também altera os tipos aos quais você pode associar, substituindo os tipos de `WindowsAzure.Storage` e `Microsoft.Azure.Storage` por tipos mais recentes de [Azure. Storage. BLOBs](/dotnet/api/azure.storage.blobs).

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

## <a name="hostjson-settings"></a>configurações de host.json

> [!NOTE]
> Esta seção não se aplica ao usar versões de extensão anteriores ao 5.0.0. Para essas versões, não há nenhuma definição de configuração global para BLOBs.

Esta seção descreve as definições de configuração global disponíveis para essa associação ao usar a [versão de extensão 5.0.0 e superior](#storage-extension-5x-and-higher). O exemplo *host.jsno* arquivo abaixo contém apenas as configurações da versão 2. x + para essa associação. Para obter mais informações sobre definições de configuração global no functions versões 2. x e posteriores, consulte [host.jsem referência para Azure Functions](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------|
|maxDegreeOfParallelism|8 * (o número de núcleos disponíveis)|O número inteiro de invocações simultâneas permitidas para cada função disparada por blob. O valor mínimo permitido é 1.|

## <a name="next-steps"></a>Próximas etapas

- [Executar uma função quando os dados do armazenamento de BLOBs forem alterados](./functions-bindings-storage-blob-trigger.md)
- [Ler dados do armazenamento de BLOBs quando uma função é executada](./functions-bindings-storage-blob-input.md)
- [Gravar dados de armazenamento de blobs de uma função](./functions-bindings-storage-blob-output.md)
