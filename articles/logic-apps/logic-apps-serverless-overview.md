---
title: Visão geral do Azure Serverless | Microsoft Docs
description: Saiba mais sobre a criação de poderosas soluções na nuvem sem precisar pensar em infraestrutura
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 9cd1946d8fa670764bcc95b82298d07cc70417bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684101"
---
# <a name="overview-azure-serverless-with-azure-logic-apps-and-azure-functions"></a>Visão geral: Azure Serverless com aplicativos lógicos do Azure e o Azure Functions

[Aplicativos sem servidor](https://azure.microsoft.com/solutions/serverless/) oferecem benefícios de um aumento na velocidade do desenvolvimento, redução no código necessário e simplicidade com escala.  Este artigo aborda os diferentes atributos de soluções sem servidor e ofertas sem servidor do Azure.

## <a name="what-is-serverless"></a>O que é sem servidor?

Sem servidor não significa que não existem servidores – simplesmente significa que o desenvolvedor não precisa se preocupar com os servidores.  Uma grande parte do desenvolvimento de aplicativos tradicionais é responder às perguntas sobre soluções de dimensionamento, hospedagem e monitoramento para atender às demandas do aplicativo.  Com o Serverless, essas perguntas são tratadas como parte da solução.  Além disso, aplicativos sem servidor são cobrados em um plano baseado em consumo.  Se o aplicativo nunca é usado, nunca se incorre em encargo nenhum.  Esses recursos permitem aos desenvolvedores se concentrarem exclusivamente na lógica de negócios da solução.

Os principais serviços no Azure ao redor do Serverless são o [Azure Functions](https://azure.microsoft.com/services/functions/) e os [Aplicativos Lógicos do Azure](https://azure.microsoft.com/services/logic-apps/).  Ambas essas soluções seguem os princípios acima e permitem que os desenvolvedores criem aplicativos robustos na nuvem com o mínimo de código.

## <a name="what-are-azure-functions"></a>O que são Azure Functions?

O Azure Functions é uma solução para executar facilmente pequenos trechos de código, ou "funções", na nuvem. Você pode simplesmente escrever o código de que necessita para o problema em questão, sem se preocupar com todo o aplicativo ou a infraestrutura para executá-lo. Funções podem tornar o desenvolvimento ainda mais produtivo e você pode usar a linguagem de desenvolvimento de sua escolha, por exemplo, C#, F#, Node.js, Python ou PHP. Pague somente pelo tempo de execução do seu código e o Azure o dimensiona conforme necessário.

Se você quiser começar a usar imediatamente o Azure Functions, comece com [Criar sua primeira função](../azure-functions/functions-create-first-azure-function.md). Se você estiver procurando informações mais técnicas sobre o Functions, confira a [referência do desenvolvedor](../azure-functions/functions-reference.md).

## <a name="what-are-azure-logic-apps"></a>O que são os Aplicativos Lógicos do Azure?

Os Aplicativos Lógicos do Azure fornecem uma maneira de simplificar e implementar integrações escalonáveis e fluxos de trabalho na nuvem. Ele fornece um designer visual para modelar e automatizar o processo como uma série de etapas conhecidas como fluxo de trabalho.  Há [muitos conectores](../connectors/apis-list.md) em serviços locais e de nuvem para conectar um aplicativo sem servidor rapidamente a outras APIs.  Um aplicativo lógico começa com um gatilho (como 'Quando uma conta é adicionada ao Dynamics CRM') e, após ser disparado, pode iniciar muitas ações de combinações, conversões e lógica de condição.  Os Aplicativos Lógicos são uma ótima opção ao orquestrar diferentes Azure Functions em um processo – especialmente quando o processo requer interação com uma API ou sistema externo.

Para uma introdução aos Aplicativos Lógicos, comece com [criar seu primeiro aplicativo lógico](quickstart-create-first-logic-app-workflow.md).  Se você estiver procurando informações mais técnicas sobre Aplicativos Lógicos, confira a [referência do desenvolvedor](logic-apps-workflow-actions-triggers.md).

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Como criar e implantar aplicativos Serverless no Azure?

O Azure fornece um rico conjunto de ferramentas em desenvolvimento, implantação e gerenciamento de aplicativos Serverless.  Aplicativos podem ser criados diretamente no Portal do Azure ou com [ferramentas do Visual Studio](logic-apps-serverless-get-started-vs.md).  Depois que um aplicativo tiver sido desenvolvido, ele poderá ser [implantado instantaneamente](logic-apps-create-deploy-template.md).  O Azure também fornece monitoramento para aplicativos sem servidor.  Esse monitoramento pode ser acessado do portal do Azure, por meio da API ou SDKs, ou com ferramentas integradas para logs do Azure Monitor e Application Insights.

## <a name="next-steps"></a>Próximas etapas

* [Introdução à criação de um aplicativo Serverless no Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Crie um painel do Customer Insights com o Serverless](logic-apps-scenario-social-serverless.md)
* [Criar um modelo de implantação para um aplicativo lógico](logic-apps-create-deploy-template.md)
