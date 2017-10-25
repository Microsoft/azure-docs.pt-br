---
title: "Exemplos e cenários comuns – Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Saiba mais sobre os aplicativos lógicos com tutoriais, exemplos, cenários e instruções passo a passo"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 09/13/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 5b2b82d90dee41e80233e5f52c960be23d89ee3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Cenários comuns, exemplos, tutoriais e instruções passo a passo para os Aplicativos Lógicos do Azure

[Aplicativos Lógicos do Azure](../logic-apps/logic-apps-what-are-logic-apps.md) ajudam você a orquestrar e integrar diferentes serviços, fornecendo [mais de 100 conectores prontos para uso](../connectors/apis-list.md), variando do SQL Server local ou SAP a Serviços Cognitivos da Microsoft. O serviço de Aplicativos Lógicos é "sem servidor", portanto, você não precisa se preocupar sobre escala ou instâncias. Tudo o que você precisa fazer é definir o fluxo de trabalho com um gatilho e as ações que o fluxo de trabalho executa. A plataforma subjacente lida com a escala, disponibilidade e desempenho. Os Aplicativos Lógicos são especialmente úteis para casos de uso e cenários em que você precise coordenar várias ações em diversos sistemas.

Para ajudar você a conhecer mais sobre os vários padrões e recursos aos quais o [Aplicativo Lógico do Azure](../logic-apps/logic-apps-what-are-logic-apps.md) dá suporte, confira aqui exemplos e cenários comuns.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Pontos de partida populares para fluxos de trabalho de aplicativo lógico

Cada aplicativo lógico começa com um [*gatilho*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) e apenas um gatilho, que inicia o fluxo de trabalho do aplicativo lógico e passa todos os dados como parte do que o gatilho. Alguns conectores fornecem gatilhos, que são fornecidos nestes tipos:

* *Gatilhos de sondagem*: verificam regularmente um ponto de extremidade de serviço para novos dados. Quando novos dados existirem, o gatilho cria e executa uma nova instância de fluxo de trabalho com os dados como entrada.

* *Gatilhos de envio por push*: escutam dados em um ponto de extremidade de serviço e aguardam até que ocorra um evento específico. Quando o evento ocorrer, o gatilho é acionado imediatamente, criando e executando uma nova instância de fluxo de trabalho que usa todos os dados disponíveis como entrada.

Aqui estão alguns exemplos de gatilho populares:

* Sondagem: 

  * [**Agenda – gatilho de** Recorrência](../connectors/connectors-native-recurrence.md) permite que você defina a data e hora de início além da recorrência para acionar seu aplicativo lógico. 
  Por exemplo, você pode selecionar os dias da semana e horas do dia para disparar seu aplicativo lógico.

  * O gatilho "Quando um email é recebido" permite que seu aplicativo lógico verifique se há novos emails de qualquer provedor de email com suporte pelos Aplicativos Lógicos, por exemplo, [Outlook do Office 365](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/) e assim por diante.

  * O [**gatilho**  HTTP](../connectors/connectors-native-http.md) permite que seu aplicativo lógico verifique um ponto de extremidade de serviço especificado por meio da comunicação por HTTP.
  
* Push:

  * O [**gatilho Solicitação / Resposta – Solicitação**](../connectors/connectors-native-reqres.md) permite que seu aplicativo lógico receba solicitações HTTP e responda em tempo real a eventos de alguma maneira.

  * O [**gatilho** Webhook HTTP](../connectors/connectors-native-webhook.md) inscreve em um ponto de extremidade de serviço, registrando um *URL de retorno de chamada* com esse serviço. 
  Dessa forma, o serviço pode apenas notificar o gatilho quando o evento especificado ocorrer, para que o gatilho não precise sondar o serviço.

Depois de receber uma notificação sobre novos dados ou um evento, o gatilho é acionado, cria uma nova instância de fluxo de trabalho do aplicativo lógico e executa as ações no fluxo de trabalho. Você pode acessar todos os dados do gatilho durante todo o fluxo de trabalho. Por exemplo, o gatilho "em um novo tweet" passa o conteúdo do tweet para a execução do aplicativo lógico. 

## <a name="respond-to-triggers-and-extend-actions"></a>Responder a gatilhos e estender ações

Para sistemas e serviços que talvez não tenham conectores publicados, também é possível estender os aplicativos lógicos.

* [Criar gatilhos ou ações personalizadas](../logic-apps/logic-apps-create-api-app.md)
* [Configurar ações de longa execução para execução de fluxo de trabalho](../logic-apps/logic-apps-create-api-app.md)
* [Responder a eventos e ações externas com webhooks](../logic-apps/logic-apps-create-api-app.md)
* [Chamar, disparar ou aninhar fluxos de trabalho com respostas síncronas a solicitações HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Tutorial: Compilar um painel social capacitado por IA em minutos com o Aplicativo Lógicos e o Power BI](http://aka.ms/logicappsdemo)
* [Tutorial: Responder a webhooks de SMS do Twilio e enviar uma resposta de texto](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Recursos de tratamento de erro, registro em log e fluxo de controle

Os aplicativos lógicos incluem recursos avançados para o fluxo de controle avançado, como comutadores, condições, loops e escopos. Para garantir soluções resilientes, implemente também a manipulação de erros e exceções em seus fluxos de trabalho. Para receber notificação e logs de diagnóstico para status de execução de fluxo de trabalho, o Aplicativo Lógico do Azure também fornece monitoramento e alertas.

* [Processar itens em matrizes e coleções com loops e lotes em aplicativos lógicos](../logic-apps/logic-apps-loops-and-scopes.md)
* [Executar ações diferentes com instruções switch](../logic-apps/logic-apps-switch-case.md)
* [Manipulação de erro e exceções do autor em um fluxo de trabalho](../logic-apps/logic-apps-exception-handling.md)
* [Caso de uso: Como uma empresa de assistência médica usa a manipulação de exceção do aplicativo lógico para fluxos de trabalho HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Ativar o monitoramento, o registro em log e alertas para aplicativos lógicos existentes](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Ativar o monitoramento e o registro em log de diagnóstico durante a criação de aplicativos lógicos](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Implantar e gerenciar aplicativos lógicos

Você pode desenvolver e implantar totalmente aplicativos lógicos com o Visual Studio, o Visual Studio Team Services ou qualquer outra ferramenta de controle do código-fonte e compilação automatizada. Para dar suporte à implantação de fluxos de trabalho e conexões dependentes em um modelo de recurso, os aplicativos lógicos usam modelos de implantação de recursos do Azure. As ferramentas do Visual Studio geram automaticamente esses modelos, que você pode conferir para controlar o código-fonte a fim de controlar a versão.

* [Compilar e implantar aplicativos lógicos do Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md)
* [Ativar o monitoramento, o registro em log e alertas para aplicativos lógicos existentes](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Criar modelos um modelo de implantação automatizado](../logic-apps/logic-apps-create-deploy-template.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Tipos de conteúdo, conversões e transformações em uma execução

Você pode acessar, converter e transformar vários tipos de conteúdo usando diversas funções na [linguagem de definição de fluxo de trabalho](http://aka.ms/logicappsdocs) do Aplicativo Lógico do Azure . Por exemplo, você pode converter entre uma cadeia de caracteres, JSON e XML com as expressões de fluxo de trabalho `@json()` e `@xml()`. O mecanismo do Aplicativo Lógico preserva os tipos de conteúdo a fim de dar suporte à transferência de conteúdo entre serviços sem perdas.

* [Como funcionam as expressões de fluxo de trabalho em aplicativos lógicos](../logic-apps/logic-apps-author-definitions.md)
* [Lidar com tipos de conteúdo não JSON](../logic-apps/logic-apps-content-type.md), como `application/xml`, `application/octet-stream` e `multipart/formdata`
* [Referência: Linguagem de definição de fluxo de trabalho do Aplicativo Lógico do Azure](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Outros recursos e integrações

Os aplicativos lógicos também oferecem integração com muitos serviços, como o Azure Functions, O Gerenciamento de API do Azure, Serviços de Aplicativo do Azure e pontos de extremidade HTTP personalizados, por exemplo, REST e SOAP.

* [Criar um painel social em tempo real com o Azure sem servidor](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Chamar o Azure Functions de aplicativos lógicos](../logic-apps/logic-apps-azure-functions.md)
* [Cenário: Disparar aplicativos lógicos com o Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Blog: Chamar pontos de extremidade SOAP de aplicativos lógicos](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Cenários de ponta a ponta

* [White paper: Gerenciamento de casos completos de integração empresarial com serviços do Azure, como Aplicativos Lógicos](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="next-steps"></a>Próximas etapas

* [Criar definições de fluxo de trabalho com a linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-author-definitions.md)
* [Tratar erros e exceções em aplicativos lógicos](../logic-apps/logic-apps-exception-handling.md)
* [Envie seus comentários, dúvidas ou sugestões para podermos aprimorar o Aplicativo Lógico do Azure](https://feedback.azure.com/forums/287593-logic-apps)