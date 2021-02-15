---
title: Associações do Serviço SignalR do Azure Functions
description: Entenda como usar associações do Serviço SignalR com o Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 9e60fd9a20720d75f96a0b78ee783bd5509a8f90
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763482"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Associações do Serviço SignalR para o Azure Functions

Este conjunto de artigos explica como autenticar e enviar mensagens em tempo real para clientes conectados ao [serviço de signaler do Azure](https://azure.microsoft.com/services/signalr-service/) usando associações de serviço de sinalização no Azure functions. O Azure Functions dá suporte a associações de entrada e saída para o Serviço SignalR.

| Ação | Tipo |
|---------|---------|
| Tratar mensagens do serviço Signalr | [Disparar Associação](./functions-bindings-signalr-service-trigger.md) |
| Retornar a URL do ponto de extremidade de serviço e o token de acesso | [Associação de entrada](./functions-bindings-signalr-service-input.md) |
| Enviar mensagens do serviço Signalr |[Associação de saída](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Adicionar ao seu aplicativo de funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e posteriores

Trabalhar com o gatilho e as associações exige que você referencie o pacote apropriado. O pacote NuGet é usado para bibliotecas de classes do .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicativos.

| Linguagem                                        | Adicionar por...                                   | Comentários 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalando o [pacote NuGet], versão 3. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrando o [pacote de extensão]          | A [extensão de ferramentas do Azure] é recomendada para uso com Visual Studio Code. |
| Script C# (somente online em portal do Azure)         | Adicionando uma associação                            | Para atualizar as extensões de associação existentes sem precisar republicar seu aplicativo de funções, consulte [atualizar suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Atualizar suas extensões]: ./functions-bindings-register.md
[Extensão de ferramentas do Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Para obter detalhes sobre como configurar e usar o serviço de sinalização e Azure Functions juntos, consulte [Azure Functions desenvolvimento e configuração com o serviço de signaler do Azure](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Biblioteca de anotações (somente Java)

Para usar as anotações do serviço Signalr em funções Java, você precisa adicionar uma dependência ao artefato *do Azure-Functions-Java-library-signalr* (versão 1,0 ou superior) ao arquivo *pom.xml* .

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Próximas etapas

- [Manipular mensagens do serviço de sinalização (Associação de gatilho)](./functions-bindings-signalr-service-trigger.md)
- [Retornar a URL do ponto de extremidade de serviço e o token de acesso (Associação de entrada)](./functions-bindings-signalr-service-input.md)
- [Enviar mensagens do serviço Signalr (Associação de saída)](./functions-bindings-signalr-service-output.md)