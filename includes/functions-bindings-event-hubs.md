---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 05d136093bd509e8c23ce8622423216326b0f1f2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102623523"
---
## <a name="add-to-your-functions-app"></a>Adicionar ao seu aplicativo de funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e posteriores

Trabalhar com o gatilho e as associações exige que você referencie o pacote apropriado. O pacote NuGet é usado para bibliotecas de classes do .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicativos.

| Idioma                                        | Adicionar por...                                   | Comentários 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalando o [pacote NuGet], versão 3. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrando o [pacote de extensão]          | A [extensão de ferramentas do Azure] é recomendada para uso com Visual Studio Code. |
| Script C# (somente online em portal do Azure)         | Adicionando uma associação                            | Para atualizar as extensões de associação existentes sem precisar republicar seu aplicativo de funções, consulte [atualizar suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[pacote de extensão]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[Atualizar suas extensões]: ../articles/azure-functions/functions-bindings-register.md
[Extensão de ferramentas do Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="event-hubs-extension-5x-and-higher"></a>Extensão de hubs de eventos 5. x e superior

Uma nova versão da extensão de associações de hubs de eventos está disponível como um [pacote NuGet de visualização](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/5.0.0-beta.1). Essa visualização apresenta a capacidade de [se conectar usando uma identidade em vez de um segredo](../articles/azure-functions/functions-reference.md#configure-an-identity-based-connection). Para aplicativos .NET, ele também altera os tipos aos quais você pode associar, substituindo os tipos de `Microsoft.Azure.EventHubs` por tipos mais novos do [Azure. Messaging. EventHubs](/dotnet/api/azure.messaging.eventhubs).

> [!NOTE]
> O pacote de visualização não está incluído em um pacote de extensão e deve ser instalado manualmente. Para aplicativos .NET, adicione uma referência ao pacote. Para todos os outros tipos de aplicativos, consulte [atualizar suas extensões].

[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/
[Atualizar suas extensões]: ./functions-bindings-register.md
[Extensão de ferramentas do Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

Os aplicativos do Functions 1. x têm automaticamente uma referência ao pacote NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versão 2. x.

## <a name="hostjson-settings"></a>configurações de host.json
<a name="host-json"></a>

O arquivo [host.json](../articles/azure-functions/functions-host-json.md#eventhub) contém configurações que controlam o comportamento de gatilho dos Hubs de Eventos. A configuração é diferente dependendo da versão do Azure Functions.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]