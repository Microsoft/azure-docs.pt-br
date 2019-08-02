---
title: O que são o Microsoft Flow, os Aplicativos Lógicos, o Functions e o WebJobs? - Azure
description: 'Compare os serviços em nuvem da Microsoft otimizados para tarefas de integração: Microsoft Flow, Aplicativos Lógicos, Functions e WebJobs.'
services: functions, logic-apps
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: microsoft flow, flow, aplicativos lógicos, azure functions, functions, azure webjobs, webjobs, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 04/09/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: d6abf266d4af5c79333819f5077009124fa28247
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348233"
---
# <a name="what-are-microsoft-flow-logic-apps-functions-and-webjobs"></a>O que são o Microsoft Flow, os Aplicativos Lógicos, o Functions e o WebJobs?

Este artigo compara os seguintes serviços em nuvem da Microsoft:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Aplicativos Lógicos do Azure](https://azure.microsoft.com/services/logic-apps/)
* [Funções do Azure](https://azure.microsoft.com/services/functions/)
* [WebJobs no Serviço de Aplicativo do Azure](../app-service/webjobs-create.md)

Todos esses serviços podem resolver problemas de integração e automatizar processos empresariais. Todos eles definem entrada e saída, condições e ações. Você pode executar cada um em um cronograma ou gatilho. Cada serviço traz vantagens exclusivas e este artigo explica as diferenças.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Comparar o Microsoft Flow e os Aplicativos Lógicos do Azure

O Microsoft Flow e os Aplicativos Lógicos são serviços de integração *centrados no designer* que podem criar fluxos de trabalho. Ambos os serviços se integram a vários aplicativos SaaS e empresariais. 

O Microsoft Flow se baseia nos Aplicativos Lógicos. Eles compartilham o mesmo designer de fluxo de trabalho e os mesmos [conectores](../connectors/apis-list.md). 

O Microsoft Flow capacita qualquer trabalhador a realizar integrações simples (por exemplo, um processo de aprovação em uma biblioteca de documentos do SharePoint) sem passar pelos desenvolvedores nem pelo departamento de TI. Os Aplicativos Lógicos também podem permitir integrações avançadas (por exemplo, processos B2B), em que o Azure DevOps de nível empresarial e práticas de segurança são necessárias. É comum que um fluxo de trabalho de negócios aumente de complexidade ao longo do tempo. Da mesma forma, você pode começar com um fluxo e, em seguida, convertê-lo em um aplicativo lógico, conforme necessário.

A seguinte tabela ajuda você a determinar a melhor opção para uma integração específica, o Microsoft Flow ou os Aplicativos Lógicos:

|  | Microsoft Flow | Aplicativos Lógicos |
| --- | --- | --- |
| Usuários |Funcionários do escritório, usuários de negócios, administradores do SharePoint |Integradores profissionais e desenvolvedores, profissionais de TI |
| Cenários |Autoatendimento |Integrações avançadas |
| Ferramenta de design |Aplicativo do navegador e móvel, somente interface do usuário |No navegador e no [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Exibição de código](../logic-apps/logic-apps-author-definitions.md) disponível |
| ALM (Gerenciamento do Ciclo de Vida do Aplicativo) |Design e teste em ambientes de não produção; promoção para produção quando pronto |Azure DevOps: controle do código-fonte, teste, suporte, automação e capacidade de gerenciamento no [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Experiência de admin |Gerenciar ambientes do Microsoft Flow e políticas DLP (prevenção contra perda de dados), acompanhamento do licenciamento: [Centro de Administração do Microsoft Flow](https://admin.flow.microsoft.com) |Gerenciar grupos de recursos, conexões, gerenciamento de acesso e log: [Portal do Azure](https://portal.azure.com) |
| Segurança |Logs de auditoria de Segurança e Conformidade do Office 365, DLP e [criptografia em repouso](https://wikipedia.org/wiki/Data_at_rest#Encryption) para dados confidenciais |Garantia de segurança do Azure: [Segurança do Azure](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) e [logs de auditoria](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Comparar o Azure Functions e os Aplicativos Lógicos do Azure

O Functions e os Aplicativos Lógicos são os serviços do Azure que permitem as cargas de trabalho sem servidor. O Azure Functions é um serviço de computação sem servidor, enquanto os Aplicativos Lógicos do Azure fornecem fluxos de trabalho sem servidor. Ambos podem criar *orquestrações* complexas. Uma orquestração é uma coleção de funções ou etapas, chamadas *ações* nos Aplicativos Lógicos, que são executadas para realizar uma tarefa complexa. Por exemplo, para processar um lote de pedidos, você pode executar várias instâncias de uma função em paralelo, aguardar a conclusão de todas as instâncias e, em seguida, executar uma função que calcula um resultado na agregação.

Para o Azure Functions, você desenvolve orquestrações escrevendo o código e usando a [extensão Durable Functions](durable/durable-functions-concepts.md). Para os Aplicativos Lógicos, você pode criar orquestrações usando uma GUI ou editando arquivos de configuração.

Você pode misturar e fazer a correspondência de serviços quando você compila uma orquestração, chamando funções de aplicativos lógicos e chamando aplicativos lógicos de funções. Escolha como criar cada orquestração com base em recursos dos serviços ou de sua preferência pessoal. A tabela a seguir lista algumas das principais diferenças entre esses serviços:
 
|  | Funções duráveis | Aplicativos Lógicos |
| --- | --- | --- |
| Desenvolvimento | Primeiro o código (obrigatória) | Primeiro o designer (declarativa) |
| Conectividade | [Vários tipos de associação integradas](functions-triggers-bindings.md#supported-bindings), escrever código para associações personalizadas | [Grande coleção de conectores](../connectors/apis-list.md), [Enterprise Integration Pack para cenários de B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [criar conectores personalizados](../logic-apps/custom-connector-overview.md) |
| Ações | Cada atividade é uma função do Azure. Escrever código para funções de atividade |[Grande coleção de ações predefinidas](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Monitoramento | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Logs do Azure Monitor](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| Gerenciamento | [API REST](durable/durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [API REST](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Contexto de execução | Pode ser executado [localmente](functions-runtime-overview.md) ou na nuvem | É executado somente na nuvem|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Comparar Functions e WebJobs

Como no Azure Functions, o WebJobs do Serviço de Aplicativo do Azure com o SDK do WebJobs é um serviço de integração *com prioridade de código* que foi projetado para desenvolvedores. Ambos são criados no [Serviço de Aplicativo do Azure](../app-service/overview.md) e dão suporte a recursos como [integração de controle de origem](../app-service/deploy-continuous-deployment.md), [autenticação](../app-service/overview-authentication-authorization.md) e [monitoramento com integração do Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs e SDK do WebJobs

Use o recurso *WebJobs* do Serviço de Aplicativo para executar um script ou um código no contexto de um aplicativo Web do Serviço de Aplicativo. O *SDK do WebJobs* é uma estrutura projetada para WebJobs que simplifica o código que você cria para responder a eventos nos serviços do Azure. Por exemplo, você pode responder à criação de um blob de imagem no Armazenamento do Azure criando uma imagem em miniatura. O SDK do WebJobs é executado como um aplicativo de console do .NET, que você pode implantar em um WebJob. 

O WebJobs e o SDK do WebJobs funcionam melhor em conjunto, mas você pode usar o WebJobs sem o SDK do WebJobs e vice-versa. Um WebJob pode executar qualquer programa ou script que possa ser executado na área restrita do Serviço de Aplicativo. Um aplicativo de console do SDK do WebJobs pode ser executado em qualquer lugar que execute aplicativos de console, como servidores locais.

### <a name="comparison-table"></a>Tabela de comparação

O Azure Functions se baseia no SDK do WebJobs e, portanto, compartilha muitos dos mesmos gatilhos de evento e conexões com outros serviços do Azure. Estes são alguns fatores a serem considerados quando você escolher entre o Azure Functions e o WebJobs com o SDK do WebJobs:

|  | Funções | WebJobs com o SDK do WebJobs |
| --- | --- | --- |
|[Modelo de aplicativo sem servidor](https://azure.microsoft.com/solutions/serverless/) com [dimensionamento automático](functions-scale.md#how-the-consumption-and-premium-plans-work)|✔||
|[Desenvolver e testar no navegador](functions-create-first-azure-function.md) |✔||
|[Preço de pagamento por uso](functions-scale.md#consumption-plan)|✔||
|[Integração com os Aplicativos Lógicos](functions-twitter-email.md)|✔||
| Eventos de gatilho |[Timer](functions-bindings-timer.md)<br>[Blobs e filas do Armazenamento do Azure](functions-bindings-storage-blob.md)<br>[Filas e tópicos do Barramento de Serviço do Azure](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Hubs de eventos do Azure](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Grade de Eventos do Azure](functions-bindings-event-grid.md)|[Timer](functions-bindings-timer.md)<br>[Blobs e filas do Armazenamento do Azure](functions-bindings-storage-blob.md)<br>[Filas e tópicos do Barramento de Serviço do Azure](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Hubs de eventos do Azure](functions-bindings-event-hubs.md)<br>[Sistema de Arquivos](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Idiomas com suporte  |C#<br>F#<br>JavaScript<br>Java (versão prévia)<br>Python (versão prévia) |C#<sup>1</sup>|
|Gerenciador de pacotes|NPM e NuGet|NuGet<sup>2</sup>|

<sup>1</sup> O WebJobs (sem o SDK do WebJobs) é compatível com C#, Java, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python, entre outros. Essa não é uma lista completa. Um WebJob pode executar qualquer programa ou script que possa ser executado na área restrita do Serviço de Aplicativo.

<sup>2</sup> O WebJobs (sem o SDK do WebJobs) dá suporte ao NPM e ao NuGet.

### <a name="summary"></a>Resumo

O Azure Functions oferece mais produtividade para o desenvolvedor do que o WebJobs do Serviço de Aplicativo do Azure. Também oferece mais opções de linguagens de programação, ambientes de desenvolvimento, integração de serviços do Azure e preços. Na maioria dos cenários, é a melhor opção.

Aqui estão dois cenários para os quais o WebJobs pode ser a melhor opção:

* Você precisa de mais controle sobre o código que escuta eventos, o objeto `JobHost`. O Functions oferece um número limitado de maneiras de personalizar o comportamento do `JobHost` no arquivo [host.json](functions-host-json.md). Às vezes, você precisa fazer coisas que não podem ser especificadas por uma cadeia de caracteres em um arquivo JSON. Por exemplo, somente o SDK do WebJobs permite configurar uma política de repetição personalizada para o Armazenamento do Azure.
* Você tem um aplicativo do Serviço de Aplicativo para o qual deseja executar snippets de código e deseja gerenciá-los juntos no mesmo ambiente do Azure DevOps.

Para outros cenários em que você deseja executar snippets de código para integrar o Azure ou serviços de terceiros, escolha o Azure Functions em vez do WebJobs com o SDK do WebJobs.

<a name="together"></a>

## <a name="microsoft-flow-logic-apps-functions-and-webjobs-together"></a>Microsoft Flow, Aplicativos Lógicos, Functions e WebJobs juntos

Você não precisa escolher apenas um desses serviços. Eles se integram entre si e a serviços externos.

Um fluxo pode chamar um aplicativo lógico. Um lógica de aplicativo pode chamar uma função e uma função pode chamar um aplicativo lógico. Consulte, por exemplo, [Criar uma função que se integra aos Aplicativos Lógicos do Azure](functions-twitter-email.md).

A integração entre o Microsoft Flow, os Aplicativos Lógicos e o Functions continua melhorando com o tempo. Você pode criar algo em um serviço e usá-lo em outros serviços.

Obtenha mais informações sobre os serviços de integração usando os seguintes links:

* [Aproveitando o Azure Functions e o Serviço de Aplicativo do Azure em cenários de integração, por Christopher Anderson](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integração simplificada, por Charles Lamanna](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Webcast ao vivo sobre os Aplicativos Lógicos](https://aka.ms/logicappslive)
* [Perguntas frequentes sobre o Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)

## <a name="next-steps"></a>Próximas etapas

Comece criando seu primeiro fluxo, aplicativo lógico ou aplicativo de funções. Selecione um dos seguintes links:

* [Introdução ao Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Como criar a sua primeira função do Azure](functions-create-first-azure-function.md)
