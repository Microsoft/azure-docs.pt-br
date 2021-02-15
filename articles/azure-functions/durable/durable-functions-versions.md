---
title: Visão geral das versões do Durable Functions-Azure Functions
description: Saiba mais sobre as versões do Durable Functions.
author: cgillum
ms.topic: conceptual
ms.date: 12/23/2020
ms.author: azfuncdf
ms.openlocfilehash: c4d10bab06428295bbc8c5319bd47787d7b1fb34
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763363"
---
# <a name="durable-functions-versions-overview"></a>Visão geral das versões do Durable Functions

As *Funções Duráveis* são uma extensão do [Azure Functions](../functions-overview.md) e do [Azure WebJobs](../../app-service/webjobs-create.md) que permitem que você escreva funções com estado em um ambiente sem servidor. A extensão gerencia estado, pontos de verificação e reinicializações para você. Se você ainda não estiver familiarizado com Durable Functions, consulte a [documentação de visão geral](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Novos recursos no 2. x

Esta seção descreve os recursos de Durable Functions que são adicionados na versão 2. x.

### <a name="durable-entities"></a>Entidades duráveis

No Durable Functions 2. x, introduzimos um novo conceito de [funções de entidade](durable-functions-entities.md) .

As funções de entidade definem operações para leitura e atualização de pequenas partes de estado, conhecidas como *entidades duráveis*. Como as funções de orquestrador, as funções de entidade são funções com um tipo de gatilho especial, o *gatilho de entidade*. Ao contrário das funções de orquestrador, as funções de entidade não têm nenhuma restrição de código específica. As funções de entidade também gerenciam o estado explicitamente, em vez de representar implicitamente o estado por meio do fluxo de controle.

Para saber mais, confira o artigo sobre [entidades duráveis](durable-functions-entities.md) .

### <a name="durable-http"></a>HTTP durável

No Durable Functions 2. x, apresentamos um novo recurso de [http durável](durable-functions-http-features.md#consuming-http-apis) que permite:

* Chame APIs HTTP diretamente de funções de orquestração (com algumas limitações documentadas).
* Implementar sondagem de status HTTP 202 do lado do cliente automático.
* Suporte interno para [identidades gerenciadas do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Para saber mais, consulte o artigo [recursos http](durable-functions-http-features.md#consuming-http-apis) .

## <a name="migrate-from-1x-to-2x"></a>Migrar de 1. x para 2. x

Esta seção descreve como migrar suas Durable Functions existentes da versão 1. x para a versão 2. x para aproveitar os novos recursos.

### <a name="upgrade-the-extension"></a>Atualizar a extensão

Instale a versão 2. x mais recente da extensão de associações de Durable Functions em seu projeto.

#### <a name="javascript-python-and-powershell"></a>JavaScript, Python e PowerShell

O Durable Functions 2. x está disponível na versão 2. x do [pacote de extensão de Azure Functions](../functions-bindings-register.md#extension-bundles).

O suporte do Python no Durable Functions requer Durable Functions 2. x.

Para atualizar a versão do pacote de extensão em seu projeto, abra host.jsem e atualize a `extensionBundle` seção para usar a versão 2. x ( `[2.*, 3.0.0)` ).

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[2.*, 3.0.0)"
    }
}
```

> [!NOTE]
> Se Visual Studio Code não estiver exibindo os modelos corretos depois de alterar a versão do pacote de extensão, recarregue a janela executando o comando *Developer: recarregar janela* (<kbd>Ctrl + R</kbd> no Windows e Linux, <kbd>Command + r</kbd> no MacOS).

#### <a name="net"></a>.NET

Atualize seu projeto .NET para usar a versão mais recente da [extensão de associações de durable Functions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask).

Consulte [registrar Azure Functions extensões de associação](../functions-bindings-register.md#local-csharp) para obter mais informações.

### <a name="update-your-code"></a>Atualizar seu código

O Durable Functions 2. x apresenta várias alterações significativas. Os aplicativos Durable Functions 1. x não são compatíveis com o Durable Functions 2. x sem alterações de código. Esta seção lista algumas das alterações que você deve fazer ao atualizar suas funções da versão 1. x para 2. x.

#### <a name="hostjson-schema"></a>Host.jsno esquema

O Durable Functions 2. x usa um novo host.jsno esquema. As principais alterações de 1. x incluem:

* `"storageProvider"` (e a `"azureStorage"` subseção) para configuração específica de armazenamento.
* `"tracing"` para rastreamento e configuração de log.
* `"notifications"` (e a `"eventGrid"` subseção) para configuração de notificação da grade de eventos.

Consulte a [Durable Functions host.jsna documentação de referência](durable-functions-bindings.md#durable-functions-2-0-host-json) para obter detalhes.

#### <a name="default-taskhub-name-changes"></a>Alterações de nome de taskhub padrão

Na versão 1. x, se um nome de Hub de tarefas não foi especificado no host.jsem, ele foi padronizado como "DurableFunctionsHub". Na versão 2. x, o nome do hub de tarefas padrão agora é derivado do nome do aplicativo de funções. Por isso, se você não tiver especificado um nome de Hub de tarefas ao atualizar para 2. x, seu código estará operando com o novo hub de tarefas e todas as orquestrações em andamento não terão mais um aplicativo processando-os. Para contornar isso, você pode definir explicitamente o nome do hub de tarefas para o padrão v1. x de "DurableFunctionsHub", ou pode seguir nossas [diretrizes de implantação sem tempo de inatividade](durable-functions-zero-downtime-deployment.md) para obter detalhes sobre como lidar com alterações significativas para orquestrações em andamento.

#### <a name="public-interface-changes-net-only"></a>Alterações de interface pública (somente .NET)

Na versão 1. x, os vários objetos de _contexto_ com suporte pelo Durable Functions têm classes base abstratas destinadas a uso em testes de unidade. Como parte do Durable Functions 2. x, essas classes base abstratas são substituídas por interfaces.

A tabela a seguir representa as principais alterações:

| 1.x | 2. x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` ou `IDurableClient` |
| `DurableOrchestrationContext` ou `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` ou `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

No caso em que uma classe base abstrata continha métodos virtuais, esses métodos virtuais foram substituídos por métodos de extensão definidos em `DurableContextExtensions` .

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.jssobre alterações (script JavaScript e C#)

No Durable Functions 1. x, a associação de cliente de orquestração usa um `type` de `orchestrationClient` . A versão 2. x usa `durableClient` em vez disso.

#### <a name="raise-event-changes"></a>Gerar alterações de evento

No Durable Functions 1. x, chamar a API de [evento raise](durable-functions-external-events.md#send-events) e especificar uma instância que não existia resultou em uma falha silenciosa. A partir do 2. x, a geração de um evento para uma orquestração inexistente resulta em uma exceção.
