---
title: "Escolha entre o Flow, os Aplicativos Lógicos, o Functions e o WebJobs | Microsoft Docs"
description: "Compare e contraste os serviços de integração na nuvem da Microsoft e decida quais deles você deve usar."
services: functions,app-service\logic
documentationcenter: na
author: ggailey777
manager: wpickett
tags: 
keywords: "microsoft flow, flow, aplicativos lógicos, azure functions, functions, azure webjobs, webjobs, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/11/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: bc801ed3dac213dacac5ee14e1b4e73b87ecc7bd
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Escolha entre o Flow, os Aplicativos Lógicos, o Functions e o WebJobs
Este artigo compara e contrasta os seguintes serviços na nuvem da Microsoft, que podem todos solucionar problemas de integração e automatizar processos de negócios:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Aplicativos Lógicos do Azure](https://azure.microsoft.com/services/logic-apps/)
* [Funções do Azure](https://azure.microsoft.com/services/functions/)
* [WebJobs no Serviço de Aplicativo do Azure](../app-service/web-sites-create-web-jobs.md)

Todos esses serviços são úteis para "unir" sistemas diferentes. Todos eles definem entrada e saída, condições e ações. Você pode executar cada um em um cronograma ou gatilho. No entanto, cada serviço tem vantagens exclusivas e compará-los não é uma questão de "Qual serviço é o melhor?" mas de "qual serviço é o mais adequado para essa situação?" Geralmente, uma combinação desses serviços é a melhor maneira de criar rapidamente uma solução escalonável de integração completa.

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Flow vs. Aplicativos Lógicos
Podemos discutir Microsoft Flow e o Aplicativo Lógico do Azure juntos porque ambos são serviços de integração que *exigem configuração antes do uso*. Eles facilitam a criação de processos e fluxos de trabalho e se integram com vários aplicativos SaaS e empresariais. 

* O Flow é criado em cima de Aplicativos Lógicos
* Eles têm o mesmo designer de fluxo de trabalho
* [Conectores](../connectors/apis-list.md) que funcionam em um também podem funcionar no outro

O Flow permite que qualquer trabalhador possa fazer integrações simples (por exemplo, obter SMS para emails importantes) sem passar por desenvolvedores ou pelo departamento de TI. Por outro lado, o Aplicativo Lógico pode habilitar integrações avançadas ou essenciais (por exemplo, processos de B2B) em que há a necessidade de práticas de segurança e DevOps de nível empresarial. É comum que um fluxo de trabalho de negócios aumente de complexidade ao longo do tempo. Da mesma forma, você pode começar com um fluxo e convertê-lo em um aplicativo lógico conforme a necessidade.

A tabela a seguir ajuda a determinar o que é melhor para determinada integração, o Flow ou os Aplicativos Lógicos.

|  | Flow | Aplicativos Lógicos |
| --- | --- | --- |
| Público-alvo |Funcionários do escritório, usuários de negócios |profissionais de TI, desenvolvedores |
| Cenários |Autoatendimento |Essenciais |
| Ferramenta de design |Aplicativo do navegador e móvel, somente interface do usuário |No navegador e no [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Exibição de código](../logic-apps/logic-apps-author-definitions.md) disponível |
| DevOps |Ad-hoc, desenvolver em produção |controle de origem, teste, suporte, automação e capacidade de gerenciamento no [Azure Resource Manager](../logic-apps/logic-apps-arm-provision.md) |
| Experiência de administrador |[https://flow.microsoft.com](https://flow.microsoft.com) |[https://portal.azure.com](https://portal.azure.com) |
| Segurança |Práticas padrão: [soberania de dados](https://wikipedia.org/wiki/Technological_Sovereignty), [criptografia em repouso](https://wikipedia.org/wiki/Data_at_rest#Encryption) para dados confidenciais etc. |Garantia de segurança do Azure: [segurança do Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Central de Segurança](https://azure.microsoft.com/services/security-center/), [logs de auditoria](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) e muito mais. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions vs. Trabalhos Web
Podemos discutir o Azure Functions e os WebJobs do Serviço de Aplicativo do Azure juntos porque eles são serviços de integração de *code-first* e projetados para desenvolvedores. Eles permitem que você execute um script ou um trecho de código em resposta a vários eventos, como [novos blobs de armazenamento](functions-bindings-storage.md) ou [uma solicitação WebHook](functions-bindings-http-webhook.md). Eis as semelhanças: 

* Ambos são criados no [Serviço de Aplicativo do Azure](../app-service/app-service-web-overview.md) e têm recursos como [controle do código-fonte](../app-service/app-service-continuous-deployment.md), [autenticação](../app-service/app-service-authentication-overview.md) e [monitoramento](../app-service/web-sites-monitor.md).
* Ambos são serviços voltados para desenvolvedores.
* Ambos dão suporte a scripts e linguagens de programação padrão.
* Ambos têm suporte NuGet e NPM.

O Functions é a evolução natural do WebJobs no sentido de ter o melhor do WebJob, só que aprimorado. As melhorias incluem: 

* Desenvolvimento simplificado, teste e execução de código diretamente no navegador.
* Integração interna com outros serviços do Azure e serviços de terceiros como o [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
* Pagamento por uso. Não é preciso pagar um [Plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
* Automático, [dimensionamento dinâmico](functions-scale.md).
* Para os clientes existentes do Serviço de Aplicativo, a execução no Plano do Serviço de Aplicativo ainda possível (para tirar proveito dos recursos subutilizados).
* Integração com os Aplicativos Lógicos.

A tabela a seguir resume as diferenças entre o Functions e o WebJobs:

|  | Funções | Trabalhos Web |
| --- | --- | --- |
| Dimensionamento |Dimensionamento sem configuração |Dimensionar com o Plano do Serviço de Aplicativo |
| Preços |Pagamento por uso ou parte de Plano do Serviço de Aplicativo |Parte do Plano do Serviço de Aplicativo |
| Executar-tipo |Disparado, agendado (pelo gatilho de temporizador) |Disparado, contínuo, agendado |
| Eventos de gatilho |[Temporizador](functions-bindings-timer.md), [Azure Cosmos DB](functions-bindings-documentdb.md), [Hubs de Eventos do Azure](functions-bindings-event-hubs.md), [HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md), [Aplicativos Móveis do Serviço de Aplicativo do Azure](functions-bindings-mobile-apps.md), [Hubs de Notificação do Azure](functions-bindings-notification-hubs.md), [Barramento de Serviço do Azure](functions-bindings-service-bus.md), [Armazenamento do Azure](functions-bindings-storage-blob.md) |[Armazenamento do Azure](functions-bindings-storage-blob.md), [Barramento de Serviço do Azure](functions-bindings-service-bus.md) |
| Desenvolvimento no navegador |Suportado |Sem suporte |
| C# |Suportado |Suportado |
| F# |Suportado |Sem suporte |
| JavaScript |Suportado |Suportado |
| Java |Suportado | Sem suporte |
| Bash |Experimental |Suportado |
| Scripts do Windows (.cmd, .bat) |Experimental |Suportado |
| PowerShell |Experimental |Suportado |
| PHP |Experimental |Suportado |
| Python |Experimental |Suportado |
| TypeScript |Experimental |Sem suporte |

O uso do Functions ou do WebJobs depende do que você já está fazendo com o Serviço de Aplicativo. Se você tiver um aplicativo do Serviço de Aplicativo para o qual deseja executar trechos de código e quiser gerenciá-los juntos no mesmo ambiente DevOps, use o WebJobs. Nos cenários a seguir, use o Functions.

* Você deseja executar trechos de código para outros serviços do Azure ou aplicativos de terceiros.
* Você deseja gerenciar seu código de integração separadamente de seus aplicativos do Serviço de Aplicativo.
* Você deseja chamar trechos de código de um aplicativo lógico. 

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Flow, Aplicativos Lógicos e Functions juntos
Conforme mencionado anteriormente, o serviço mais adequado para você depende da situação. 

* Para a otimização de negócios simples, use o Flow.
* Se seu cenário de integração é muito avançado para o Flow, ou se você precisa de recursos de DevOps e conformidades de segurança, use os Aplicativos Lógicos.
* Se uma etapa no seu cenário de integração requer transformação altamente personalizada ou código especializado, escreva uma função e dispare-a como uma ação em seu aplicativo lógico.

Você pode chamar um aplicativo lógico em um fluxo. Você também pode chamar uma função em um aplicativo lógico e um aplicativo lógico em uma função. A integração entre o Flow, o Aplicativo Lógico e o Functions segue melhorando a cada dia. Você pode criar algo em um serviço e usá-lo em outros serviços. Portanto, todo o investimento feito nessas três tecnologias vale a pena.

## <a name="next-steps"></a>Próximas etapas
Comece a usar cada um dos serviços criando seu primeiro fluxo, aplicativo lógico, aplicativo de funções ou trabalho Web. Clique em um dos links abaixo:

* [Introdução ao Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md)
* [Criar sua primeira Função do Azure](functions-create-first-azure-function.md)
* [Implantar Trabalhos Web usando o Visual Studio](../app-service/websites-dotnet-deploy-webjobs.md)

Ou saiba mais sobre esses serviços de integração com os links abaixo:

* [Aproveitando o Azure Functions e o Serviço de Aplicativo do Azure em cenários de integração, por Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integração simplificada, por Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Webcast ao vivo de Aplicativos Lógicos](http://aka.ms/logicappslive)
* [Perguntas frequentes sobre o Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)

