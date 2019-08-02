---
title: Visão Geral das Funções Duráveis – Azure
description: Introdução à extensão de Funções Duráveis do Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 12/22/2018
ms.author: glenga
ms.reviewer: azfuncdf
ms.openlocfilehash: 6e1acf5f605d7f6fb42d24e6e7ec624a317b0e31
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612874"
---
# <a name="what-are-durable-functions"></a>O que são as Durable Functions?

As *Durable Functions* são uma extensão do [Azure Functions](../functions-overview.md) que permitem escrever funções com estado em um ambiente sem servidor. A extensão gerencia estado, pontos de verificação e reinicializações para você.

## <a name="benefits"></a>Benefícios

A extensão permite que você defina fluxos de trabalho com estado usando uma [*função de orquestrador*](durable-functions-types-features-overview.md#orchestrator-functions), que pode oferecer os seguintes benefícios:

* Você pode definir seus fluxos de trabalho no código. Não são necessários designers ou esquemas JSON.
* Outras funções podem ser chamadas de forma síncrona e assíncrona. A saída das funções chamadas pode ser salva em variáveis locais.
* O progresso é marcado automaticamente com um ponto de verificação quando a função aguarda. O estado local nunca é perdido quando o processo é reciclado ou a VM é reiniciada.

## <a name="application-patterns"></a>Padrões de aplicativo

O principal caso de uso das Durable Functions é simplificar requisitos complexos de coordenação com estado em aplicativos sem servidor. Estes são alguns padrões de aplicativo típicos que podem se beneficiar das Durable Functions:

* [Encadeamento](durable-functions-concepts.md#chaining)
* [Fan-out/fan-in](durable-functions-concepts.md#fan-in-out)
* [APIs HTTP assíncronas](durable-functions-concepts.md#async-http)
* [Monitoramento](durable-functions-concepts.md#monitoring)
* [Interação humana](durable-functions-concepts.md#human)

## <a name="language-support"></a>Linguagens compatíveis

Atualmente, as Durable Functions dão suporte às seguintes linguagens:

* **C#** : [bibliotecas de classes pré-compiladas](../functions-dotnet-class-library.md) e [script C#](../functions-reference-csharp.md).
* **F#** : bibliotecas de classes pré-compiladas e script F#. Só há suporte para o script F# na versão 1.x do Azure Functions Runtime.
* **JavaScript**: compatível apenas com a versão 2.x do Azure Functions Runtime. Exige a versão 1.7.0 da extensão das Durable Functions ou uma versão posterior. 

As Durable Functions têm o objetivo de dar suporte a todas as [linguagens do Azure Functions](../supported-languages.md). Confira a [lista de problemas das Durable Functions](https://github.com/Azure/azure-functions-durable-extension/issues) para obter o último status do trabalho para dar suporte a linguagens adicionais.

Assim como o Azure Functions, há modelos para ajudá-lo a desenvolver as Durable Functions usando o [Visual Studio 2019](durable-functions-create-first-csharp.md), o [Visual Studio Code](quickstart-js-vscode.md) e o [portal do Azure](durable-functions-create-portal.md).

## <a name="billing"></a>Cobrança

As Durable Functions são cobradas da mesma forma que o Azure Functions. Para saber mais, confira [Preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="jump-right-in"></a>Comece a usar agora

Comece a usar as Durable Functions em menos de 10 minutos concluindo um destes tutoriais de início rápido específicos a uma linguagem:

* [C# usando o Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript usando o Visual Studio Code](quickstart-js-vscode.md)

Nos dois inícios rápidos, você criará e testará uma durable function "Olá, Mundo" localmente. Em seguida, você publicará o código de função no Azure. A função que você criará orquestra e encadeia chamadas para outras funções.

## <a name="learn-more"></a>Saiba mais

O seguinte vídeo destaca os benefícios das Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Como as Durable Functions são uma extensão avançada do [Azure Functions](../functions-overview.md), elas não são apropriadas para todos os aplicativos. Para saber mais sobre as Durable Functions, confira [Padrões e conceitos técnicos sobre as Durable Functions](durable-functions-concepts.md). Para obter uma comparação com outras tecnologias de orquestração do Azure, confira [Comparar o Azure Functions e os Aplicativos Lógicos do Azure](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Padrões e conceitos técnicos sobre as Durable Functions](durable-functions-concepts.md)
