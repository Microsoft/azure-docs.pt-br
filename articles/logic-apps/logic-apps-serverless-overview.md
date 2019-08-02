---
title: Visão geral – servidor do Azure
description: Crie soluções poderosas na nuvem sem se preocupar com a infraestrutura
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 363002712bdd06e74360de9af186f5a458a4999d
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385367"
---
# <a name="overview-azure-serverless-with-azure-logic-apps-and-azure-functions"></a>Visão geral: Azure sem servidor com aplicativos lógicos do Azure e Azure Functions

Aplicativos sem [servidor](https://azure.microsoft.com/solutions/serverless/) oferecem benefícios como maior velocidade de desenvolvimento, código reduzido, simplicidade e escala. Este artigo aborda os diferentes atributos de soluções sem servidor e ofertas sem servidor do Azure.

## <a name="what-is-serverless"></a>O que é sem servidor?

Sem servidor não significa que não há servidores, mas, em vez disso, os desenvolvedores não precisam se preocupar com os servidores. Uma grande parte do desenvolvimento de aplicativos tradicionais é responder às perguntas sobre soluções de dimensionamento, hospedagem e monitoramento para atender às demandas do aplicativo. Com o sem servidor, essas perguntas são cuidadas como parte da solução. Além disso, os aplicativos sem servidor são cobrados em um plano baseado em consumo. Se o aplicativo nunca for usado, nenhum encargo será incorrido. Esses recursos ajudam os desenvolvedores a se concentrarem exclusivamente na lógica de negócios de uma solução.

Os principais serviços do Azure para servidores são [aplicativos lógicos do Azure](https://azure.microsoft.com/services/logic-apps/) e [Azure Functions](https://azure.microsoft.com/services/functions/). Ambas as soluções seguem os princípios descritos anteriormente e ajudam os desenvolvedores a criar aplicativos de nuvem robustos com o mínimo de código.

## <a name="what-is-azure-logic-apps"></a>O que são Aplicativos Lógicos do Azure?

Os [aplicativos lógicos do Azure](logic-apps-overview.md) fornecem uma maneira de simplificar e implementar integrações e fluxos de trabalho escalonáveis na nuvem. Esse serviço fornece um designer visual para modelar e automatizar seu processo como uma série de etapas chamadas de fluxo de trabalho. Há muitos [conectores](../connectors/apis-list.md) entre os serviços de nuvem e os sistemas locais que conectam rapidamente um aplicativo sem servidor a outras APIs. Cada aplicativo lógico começa com um gatilho, como "quando uma conta é adicionada ao Dynamics CRM". Depois que o gatilho é acionado, o fluxo de trabalho pode executar combinações de ações, conversões e lógica condicional. Os aplicativos lógicos são uma ótima opção ao orquestrar diferentes Azure Functions em um processo, especialmente quando o processo requer interação com um sistema externo ou uma API.

Para uma introdução aos Aplicativos Lógicos, comece com [criar seu primeiro aplicativo lógico](quickstart-create-first-logic-app-workflow.md). Para obter mais informações técnicas sobre aplicativos lógicos, consulte a [referência do desenvolvedor](logic-apps-workflow-definition-language.md).

## <a name="what-is-azure-functions"></a>O que é Azure Functions?

Azure Functions é um serviço para executar facilmente partes de código ou "funções" na nuvem. Você pode escrever apenas o código necessário para o problema atual, sem se preocupar com um aplicativo inteiro ou a infraestrutura necessária. Funções podem tornar o desenvolvimento ainda mais produtivo e você pode usar a linguagem de desenvolvimento de sua escolha, por exemplo, C#, F#, Node.js, Python ou PHP. Você paga apenas pelo tempo em que seu código é executado e o Azure é dimensionado conforme necessário.

Para começar a usar o Azure Functions, comece com [criar sua primeira função do Azure](../azure-functions/functions-create-first-azure-function.md). Para obter mais informações técnicas sobre o functions, consulte a [referência do desenvolvedor](../azure-functions/functions-reference.md).

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Como posso criar e implantar aplicativos sem servidor no Azure?

O Azure fornece ferramentas avançadas para o desenvolvimento, implantação e gerenciamento de aplicativos sem servidor. Você pode criar aplicativos diretamente no portal do Azure, com [ferramentas no Visual Studio](logic-apps-serverless-get-started-vs.md)ou [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md). Depois de criar seu aplicativo, você pode [implantar esse aplicativo rapidamente com modelos de Azure Resource Manager](logic-apps-deploy-azure-resource-manager-templates.md). O Azure também fornece monitoramento, que pode ser acessado por meio da portal do Azure, por meio da API ou SDKs, ou com ferramentas integradas para Azure Monitor logs e Application Insights.

## <a name="next-steps"></a>Próximas etapas

* [Criar um aplicativo sem servidor no Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Criar um painel do Customer insights sem servidor](logic-apps-scenario-social-serverless.md)
* [Automatizar a implantação do aplicativo lógico](logic-apps-azure-resource-manager-templates-overview.md)
