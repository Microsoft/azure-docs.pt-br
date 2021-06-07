---
title: Cenários comuns e exemplos
description: Encontre cenários comuns, exemplos, tutoriais e instruções passo a passo para os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 5f7afe044ab4f782f2028598c2c56e75edf95860
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92317388"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Cenários comuns, exemplos, tutoriais e instruções passo a passo para os Aplicativos Lógicos do Azure

Os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) ajudam você a orquestrar e integrar diferentes serviços, fornecendo [centenas de conectores prontos para uso](../connectors/apis-list.md), desde o SQL Server ou SAP até Serviços Cognitivos do Azure. O serviço de Aplicativos Lógicos é "sem servidor", portanto, você não precisa se preocupar sobre escala ou instâncias. Tudo o que você precisa fazer é definir o fluxo de trabalho com um gatilho e as ações que o fluxo de trabalho executa. A plataforma subjacente lida com a escala, disponibilidade e desempenho. Os Aplicativos Lógicos são especialmente úteis para casos de uso e cenários em que você precise coordenar várias ações em sistemas.

Para ajudá-lo a saber mais sobre os recursos e padrões aos quais os Aplicativos Lógicos do Azure dá suporte, este artigo descreve pontos de partida, exemplos e cenários comuns.

## <a name="common-starting-points-for-logic-app-workflows"></a>Pontos de partida comuns para fluxos de trabalho de aplicativo lógico

Cada aplicativo lógico começa com um [*gatilho*](../logic-apps/logic-apps-overview.md#logic-app-concepts) e apenas um gatilho, que inicia o fluxo de trabalho do aplicativo lógico e passa todos os dados como parte do que o gatilho. Alguns conectores fornecem gatilhos, que são fornecidos nestes tipos:

* *Gatilhos de sondagem*: verificam regularmente um ponto de extremidade de serviço para novos dados. Quando novos dados existirem, o gatilho cria e executa uma nova instância de fluxo de trabalho com os dados como entrada.

* *Gatilhos de envio por push*: escutam dados em um ponto de extremidade de serviço e aguardam até que ocorra um evento específico. Quando o evento ocorrer, o gatilho é acionado imediatamente, criando e executando uma nova instância de fluxo de trabalho que usa todos os dados disponíveis como entrada.

Este são exemplos que descrevem os gatilhos comumente usados:

* Gatilhos de *sondagem*:

  * O [**gatilho de**  recorrência](../connectors/connectors-native-recurrence.md) permite que você defina a data e hora de início, além da recorrência para acionar seu aplicativo lógico. Por exemplo, você pode selecionar os dias da semana e horas do dia para disparar seu aplicativo lógico. Para saber mais, consulte esses tópicos:<p>

    * [Agendar e executar tarefas automatizadas, processos e fluxos de trabalho recorrentes com Aplicativos Lógicos do Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Tutorial: criar fluxos de trabalho automatizados, recorrentes e com base em agendamento usando os Aplicativos Lógicos do Azure](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * O gatilho **Quando um email é recebido** permite que seu aplicativo lógico verifique se há novos emails de qualquer provedor de email com suporte dos Aplicativos Lógicos, por exemplo, [Outlook do Office 365](../connectors/connectors-create-api-office365-outlook.md), [Gmail](/connectors/gmail/), [Outlook.com](/connectors/outlook/) e assim por diante.

    > [!IMPORTANT]
    > Se você quiser usar o conector do Gmail, somente as contas comerciais do G Suite poderão usar esse conector sem restrição nos aplicativos lógicos. Se você tiver uma conta de consumidor do Gmail, poderá usar esse conector somente com serviços específicos do Google aprovados ou poderá [criar um aplicativo cliente do Google para usar para autenticação com o conector do Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Para obter mais informações, confira [Políticas de privacidade e segurança de dados para os conectores do Google nos Aplicativos Lógicos do Azure](../connectors/connectors-google-data-security-privacy-policy.md).

    Para saber mais, consulte esses tópicos:<p>

    * [Tutorial: Criar fluxos de trabalho automatizados baseados em aprovação usando os Aplicativos Lógicos do Azure](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Tutorial: Automatizar tarefas para processar emails usando os Aplicativos Lógicos do Azure, o Azure Functions e o Armazenamento do Azure](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * O [gatilho **HTTP**](../connectors/connectors-native-http.md) pode chamar um ponto de extremidade de serviço por HTTP ou HTTPS. Para saber mais, confira [Chamar, disparar ou aninhar fluxos de trabalho usando pontos de extremidade HTTP](../logic-apps/logic-apps-http-endpoint.md).

* Gatilhos de *envio*:

  * O [gatilho **de** solicitação](../connectors/connectors-native-reqres.md) pode receber solicitações HTTPS de entrada.

  * O [**gatilho** Webhook HTTP](../connectors/connectors-native-webhook.md) inscreve em um ponto de extremidade de serviço, registrando um *URL de retorno de chamada* com esse serviço. Dessa forma, o serviço pode apenas notificar o gatilho quando o evento especificado ocorrer, para que o gatilho não precise sondar o serviço.

Depois que o evento especificado acontecer, o gatilho é acionado, cria uma nova instância de fluxo de trabalho do aplicativo lógico e executa as ações no fluxo de trabalho. Você pode acessar todos os dados do gatilho durante todo o fluxo de trabalho. Por exemplo, o gatilho do Twitter **em um novo tweet** passa o conteúdo do tweet para a execução do aplicativo lógico. Para começar a usar os Aplicativos Lógicos do Azure, experimente usar estes tópicos de início rápido:

* [Início Rápido: criar o primeiro fluxo de trabalho automatizado com os Aplicativos Lógicos do Azure – Portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Início Rápido: criar tarefas, processos e fluxos de trabalho automatizadas usando os Aplicativos Lógicos do Azure – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Início Rápido: criar e gerenciar fluxos de trabalho de aplicativos lógicos automatizados usando o Visual Studio Code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Recursos de tratamento de erro e fluxo de controle

Os aplicativos lógicos incluem recursos avançados para o fluxo de controle avançado, como os comutadores, condições, loops e escopos. Para garantir soluções resilientes, implemente também a manipulação de erros e exceções em seus fluxos de trabalho.

* Executar ações diferentes com base em [instruções condicionais](../logic-apps/logic-apps-control-flow-conditional-statement.md) e [alternar instruções](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Repetir as etapas ou processar itens em matrizes e coleções com loops](../logic-apps/logic-apps-control-flow-loops.md)
* [Agrupar ações com os escopos](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Adicionar manipulação de erro e exceções do autor no fluxo de trabalho](../logic-apps/logic-apps-exception-handling.md)
* [Caso de uso: Como uma empresa de assistência médica usa a manipulação de exceção do aplicativo lógico para fluxos de trabalho HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Criar conectores e APIs personalizados

Para sistemas e serviços que não tem conectores publicados, também é possível estender os aplicativos lógicos.

* [Criar APIs personalizadas para fazer chamadas dos Aplicativos Lógicos do Azure](../logic-apps/logic-apps-create-api-app.md)
* [Verificar se há novos dados ou eventos regularmente usando o padrão de gatilho de sondagem](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Aguardar e escutar novos dados ou eventos usando o padrão de gatilho de webhook](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Executar tarefas de execução prolongada usando o padrão de ação de sondagem](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Executar tarefas de longa execução usando o padrão de ação do webhook](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Conectores personalizados nos Aplicativos Lógicos do Azure](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Criar soluções B2B (entre empresas)

Para soluções de integração empresarial e comunicação direta entre organizações, você pode criar fluxos de trabalho escalonáveis automatizados para esses cenários usando o EIP (Enterprise Integration Pack) com os Aplicativos Lógicos do Azure. Embora as organizações usem diferentes protocolos e formatos, elas podem trocar mensagens eletronicamente. O EIP transforma formatos diferentes em um formato que os sistemas de sua organização podem processar e dá suporte a protocolos padrão do setor, incluindo o AS2, X12, EDIFACT e o RosettaNet. Para criar essas soluções, você cria uma conta de integração, que é um recurso separado do Microsoft Azure que fornece um contêiner seguro, escalonável e gerenciável para os artefatos que você define e usa com seus fluxos de trabalho de aplicativo lógico. Por exemplo, os artefatos incluem parceiros comerciais, contratos, mapas, esquemas, certificados e configurações de lote.

* [Visão geral: soluções de integração empresarial B2B com os Aplicativos Lógicos do Azure e o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Criar e gerenciar contas de integração para integrações empresariais B2B nos Aplicativos Lógicos do Azure](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Acessar recursos de rede virtual do Azure

Às vezes, os aplicativos lógicos e as contas de integração precisam acessar recursos protegidos, como VMs (máquinas virtuais) e outros sistemas ou serviços que estão na rede virtual do Azure. Para configurar esse acesso, você pode criar um ISE (ambiente do serviço de integração) onde você pode compilar e executar seus aplicativos lógicos. Um ISE é uma instância isolada e privada do serviço de Aplicativos Lógicos que usa recursos dedicados como armazenamento e é executado separadamente do serviço público dos Aplicativos Lógicos multilocatários "globais". Separar sua instância privada isolada e a instância global pública também ajuda a reduzir o impacto que outros locatários do Azure podem ter no desempenho de seus aplicativos, o que também é conhecido como efeito "vizinhos ruidosos".

* [Visão geral: acessar aos recursos da rede virtual do Azure a partir dos Aplicativos Lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Conectar-se a redes virtuais do Azure a partir do serviço Aplicativos Lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Implantar, gerenciar e monitorar aplicativos lógicos

Você pode desenvolver e implantar totalmente aplicativos lógicos com o Visual Studio, o Azure DevOps ou qualquer outra ferramenta de controle do código-fonte e de build automatizada. Para dar suporte à implantação de fluxos de trabalho e conexões dependentes em um modelo de recurso, os aplicativos lógicos usam modelos de implantação de recursos do Azure. As ferramentas do Visual Studio geram automaticamente esses modelos, que você pode conferir para controlar o código-fonte a fim de controlar a versão. Para receber notificação e logs de diagnóstico para status de execução de fluxo de trabalho, os Aplicativos Lógicos do Azure também fornecem monitoramento e alertas.

### <a name="deploy"></a>Implantar

* [Início Rápido: criar tarefas, processos e fluxos de trabalho automatizadas usando os Aplicativos Lógicos do Azure – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Visão geral: automatizar a implantação de aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Criar modelos do Azure Resource Manager para automatizar a implantação para Aplicativos Lógicos do Azure](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Implantar modelos do Azure Resource Manager para Aplicativos Lógicos do Azure](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Amostra: conectar-se a filas do barramento de serviço do Azure a partir dos Aplicativos Lógicos do Azure e implantar com o Azure Pipelines no Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: conectar-se a contas do Armazenamento do Microsoft Azure a partir dos Aplicativos Lógicos do Azure e implantar com o Azure Pipelines no Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: configurar uma ação de aplicativo de funções para Aplicativos Lógicos do Azure e implantar com o Azure Pipelines no Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: conectar-se a contas de integração a partir dos Aplicativos Lógicos do Azure e implantar com o Azure Pipelines no Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Amostra: organizar o Azure Pipelines usando os Aplicativos Lógicos do Azure](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Gerenciar

* [Gerenciar aplicativos lógicos usando o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Criar e gerenciar contas de integração para integrações empresariais B2B](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Gerenciar ISE (ambiente do serviço de integração) nos Aplicativos Lógicos do Azure](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Monitoramento

* [Monitorar o status de execução, examinar o histórico de gatilho e configurar alertas para aplicativos lógicos do Azure](../logic-apps/monitor-logic-apps.md)
* [Como configurar os logs do Azure Monitor e coletar dados de diagnóstico para os Aplicativos Lógicos do Azure](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Configurar logs de Azure Monitor e coletar dados de diagnóstico para mensagens B2B em aplicativos lógicos do Azure](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Exibir e criar consultas para monitoramento e acompanhamento em logs do Azure Monitor para Aplicativos Lógicos do Azure](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Lidando com tipos de conteúdo, conversões e transformações

Você pode acessar, converter e transformar vários tipos de conteúdo usando diversas funções na [linguagem de definição de fluxo de trabalho](./logic-apps-workflow-definition-language.md) dos Aplicativos Lógicos do Azure. Por exemplo, você pode converter entre uma cadeia de caracteres, JSON e XML com as expressões de fluxo de trabalho `@json()` e `@xml()`. O mecanismo do Aplicativo Lógico preserva os tipos de conteúdo a fim de dar suporte à transferência de conteúdo entre serviços sem perdas.

* [Lidar com tipos de conteúdo em Aplicativos Lógicos do Azure](../logic-apps/logic-apps-content-type.md), como `application/`, `application/octet-stream`, e `multipart/formdata`
* [Guia de referência do uso de funções em expressões para os Aplicativos Lógicos do Azure e o Power Automate](../logic-apps/workflow-definition-language-functions-reference.md)
* [Esquema de linguagem de definição de fluxo de trabalho para os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Outros recursos e integrações

Os aplicativos lógicos integram muitos serviços, como o Azure Functions, o Gerenciamento de API do Azure, Serviço de Aplicativo do Azure e pontos de extremidade HTTP personalizados, por exemplo, o REST e o SOAP.

* [Chamar o Azure Functions a partir dos Aplicativos Lógicos do Azure](../logic-apps/logic-apps-azure-functions.md)
* [Tutorial: chamar ou acionar os aplicativos lógicos usando o Azure Functions e o Barramento de Serviço do Azure](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Tutorial: criar de um painel de informações do cliente em streaming com Aplicativos Lógicos do Azure e Azure Functions](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Tutorial: criar uma função que se integra ao Aplicativos Lógicos do Azure e aos Serviços Cognitivos do Azure para analisar o sentimento de postagens do Twitter](../azure-functions/functions-twitter-email.md)
* [Tutorial: criar um painel social alimentado por IA usando o Power BI e Aplicativos Lógicos do Azure](https://aka.ms/logicappsdemo)
* [Tutorial: Monitorar as alterações de máquinas virtuais usando a Grade de Eventos do Azure e os Aplicativos Lógicos](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Tutorial: Monitoramento remoto IoT e notificações com Aplicativos Lógicos do Azure conectando o Hub IoT e a caixa de correio](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: chamar serviços SOAP usando Aplicativos Lógicos do Azure](/archive/blogs/logicapps/using-soap-services-with-logic-apps)

## <a name="end-to-end-scenarios"></a>Cenários de ponta a ponta

* [White paper: Integração de gerenciamento de casos completos com serviços do Azure, como Aplicativos Lógicos](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Relatos de clientes

Saiba como os Aplicativos Lógicos do Azure, juntamente com outros serviços do Azure e produtos da Microsoft, ajudaram [estas empresas](https://aka.ms/logic-apps-customer-stories) a melhorar a agilidade e a concentração em seus negócios essenciais simplificando, organizando, automatizando e coordenando processos complexos.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
* Saiba mais sobre [cenários de integração empresarial B2B com Aplicativos Lógicos do Azure](../logic-apps/logic-apps-enterprise-integration-overview.md)