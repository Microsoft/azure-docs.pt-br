---
title: Hospedagem do plano de consumo Azure Functions
description: Saiba como a hospedagem do plano de consumo de funções do Azure permite que você execute seu código em um ambiente que é dimensionado dinamicamente, mas você paga apenas pelos recursos usados durante a execução.
ms.date: 8/31/2020
ms.topic: conceptual
ms.openlocfilehash: d292a70a8dfaa4cebdb99f2bcb5420c8b8ab9cd8
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98760542"
---
# <a name="azure-functions-consumption-plan-hosting"></a>Hospedagem do plano de consumo Azure Functions

Quando você estiver usando o plano de consumo, as instâncias do host Azure Functions serão adicionadas e removidas dinamicamente com base no número de eventos de entrada. O plano de consumo é a opção de hospedagem totalmente sem <em>servidor</em> para Azure functions.

## <a name="benefits"></a>Benefícios

O plano de consumo é dimensionado automaticamente, mesmo durante períodos de alta carga. Ao executar funções em um plano de consumo, você será cobrado pelos recursos de computação somente quando suas funções estiverem em execução. Em um plano de consumo, a execução de uma função expire após um período configurável.

Para obter uma comparação do plano de consumo com relação aos outros tipos de plano e hospedagem, consulte [Opções de escala e Hospedagem de função](functions-scale.md).

## <a name="billing"></a>Cobrança

A cobrança baseia-se no número de execuções, no tempo de execução e na memória usada. O uso é agregado em todas as funções em um aplicativo de funções. Para saber mais, confira a [página de preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Para saber mais sobre como estimar os custos durante a execução em um plano de consumo, consulte [noções básicas sobre custos do plano de consumo](functions-consumption-costs.md).

## <a name="create-a-consumption-plan-function-app"></a>Criar um aplicativo de função de plano de consumo

Quando você cria um aplicativo de funções no portal do Azure, o plano de consumo é o padrão. Ao usar APIs para criar seu aplicativo de funções, você não precisa primeiro criar um plano do serviço de aplicativo como você faz com planos Premium e dedicado.

Use os links a seguir para saber como criar um aplicativo de funções sem servidor em um plano de consumo, seja de forma programática ou na portal do Azure:

+ [CLI do Azure](./scripts/functions-cli-create-serverless.md)
+ [Azure portal](./functions-get-started.md)
+ [Modelo do Azure Resource Manager](functions-create-first-function-resource-manager.md)

Você também pode criar aplicativos de funções em um plano de consumo ao publicar um projeto do Functions do [Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) ou do [Visual Studio](functions-create-your-first-function-visual-studio.md#publish-the-project-to-azure).

## <a name="multiple-apps-in-the-same-plan"></a>Vários aplicativos no mesmo plano

Os aplicativos de funções na mesma região podem ser atribuídos ao mesmo plano de consumo. Não há nenhuma desvantagem ou impacto para ter vários aplicativos em execução no mesmo plano de consumo. A atribuição de vários aplicativos ao mesmo plano de consumo não afeta a resiliência, a escalabilidade ou a confiabilidade de cada aplicativo.

## <a name="next-steps"></a>Próximas etapas

+ [Opções de Hospedagem de Azure Functions](functions-scale.md)
+ [Dimensionamento controlado por eventos no Azure Functions](event-driven-scaling.md)
