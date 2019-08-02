---
title: Cenário sem servidor – criar um painel de informações do cliente com os serviços do Azure | Microsoft Docs
description: Saiba como você pode gerenciar os comentários dos clientes, dados de mídia social e muito mais, criando um painel de controle do cliente com os Aplicativos Lógicos do Azure e Azure Functions
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: 6f0176253d0f1953e9c779a317cfe7bf453acb8f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385440"
---
# <a name="create-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Criar um painel de informações do cliente em streaming com os Aplicativos Lógicos do Azure e o Azure Functions

O Azure oferece ferramentas [sem servidor](https://azure.microsoft.com/solutions/serverless/) do Azure que ajudam você a rapidamente criar e hospedar aplicativos na nuvem, sem precisar pensar sobre a infraestrutura. Nesse tutorial, você pode criar um painel que terá como gatilho os comentários de cliente, analisará os comentários com o aprendizado de máquina e publicará informações em uma fonte como o Power BI ou Azure Data Lake.

Para esta solução, você deve usar esses componentes-chave do Azure para aplicativos sem servidor: [Azure Functions](https://azure.microsoft.com/services/functions/) e [Aplicativos Lógicos do Azure](https://azure.microsoft.com/services/logic-apps/).
Aplicativos Lógicos do Azure fornecem um mecanismo de fluxo de trabalho sem servidor na nuvem para que você possa criar orquestrações entre componentes sem servidor e se conectar a mais de 200 serviços e APIs. O Azure Functions fornece computação sem servidor na nuvem. Essa solução usa o Azure Functions para sinalizar tweets de cliente com base em palavras-chave predefinidas.

Nesse cenário, você cria um aplicativo lógico que terá como gatilho encontrar os comentários dos clientes. Alguns conectores que ajudam a responder aos comentários dos clientes incluem Outlook.com, Office 365, Survey Monkey, Twitter e uma [solicitação HTTP de um formulário web](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). O fluxo de trabalho que você cria monitora um hashtag no Twitter.

Você pode [criar toda a solução no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) e [implantar a solução com o modelo do Azure Resource Manager](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md). Para obter uma explicação de vídeo que mostra como criar essa solução, [Assista a este vídeo do Channel 9](https://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Gatilho em dados do cliente

1. No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco. 

   Se você não estiver familiarizado com os aplicativos lógicos, examine o [Início rápido para o portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md) ou [Início rápido para o Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. No Designer de Aplicativos Lógicos, localizar e adicionar o gatilho do Twitter com esta ação: **Quando um novo tweet é postado**

3. Configure o gatilho para escutar tweets com base em uma palavra-chave ou hashtag.

   Em gatilhos de sondagem, como o gatilho do Twitter, a propriedade de recorrência do gatilho determina com que frequência o aplicativo lógico verifica novos itens.

   ![Exemplo de gatilho do Twitter][1]

Este aplicativo lógico é acionado em todos os novos tweets. Você pode executar e analisar os dados de tweet para que você possa entender melhor as opiniões expressadas. 

## <a name="analyze-tweet-text"></a>Analisar o texto do tweet

Para detectar o sentimento por trás de um texto, você pode usar os [Serviços Cognitivos Azure](https://azure.microsoft.com/services/cognitive-services/).

1. No Designer de Aplicativos Lógicos, no gatilho, escolha **Nova etapa**.

2. Localize o conector de **Análise de Texto**.

3. Selecione a ação **Detectar Sentimento**.

4. Se solicitado, forneça uma chave de Serviços Cognitivos válida para o serviço de análise de texto.

5. Em **Corpo da solicitação**, selecione o campo **Texto do tweet**, que fornece o texto escrito como entrada para análise.

Depois de obter os dados de tweet e informações sobre o tweet, agora você pode usar vários outros conectores relevantes e suas ações:

* **Power BI – adicionar linhas ao conjunto de dados de streaming**: Exiba tweets de entrada em um dashboard do Power BI.
* **Azure Data Lake – acrescentar arquivo**: Adicionar dados do cliente em um conjunto de dados do Azure Data Lake para incluir nos trabalhos de análise.
* **SQL – adicionar linhas**: Armazenar dados em um banco de dados para recuperação posterior.
* **Slack – enviar mensagem**: Notificar um canal do Slack quando um comentário negativo requer ações.

Você também pode criar uma função do Azure para que você possa executar processamento personalizado em seus dados. 

## <a name="process-data-with-azure-functions"></a>Processar dados com o Azure Functions

Antes de criar uma função, crie um aplicativo de funções na sua assinatura do Azure. Além disso, para seu aplicativo lógico chamar diretamente uma função, a função deve ter um associação de gatilho HTTP, por exemplo, use o modelo **HttpTrigger**. Saiba [como criar seu primeiro aplicativo de funções e função no portal do Azure](../azure-functions/functions-create-first-azure-function-azure-portal.md).

Para esse cenário, use o texto do tweet como corpo da solicitação para sua função do Azure. No seu código de função, defina a lógica que determina se o texto do tweet contém uma palavra-chave ou frase. Manter a função como simples ou complexa conforme necessário para o cenário.
No final da função, retornar uma resposta para o aplicativo lógico com alguns dados, por exemplo, um valor booliano simples como `containsKeyword` ou um objeto complexo.

> [!TIP]
> Para acessar uma resposta complexa de uma função em um aplicativo lógico, use a ação **analisar JSON**.

Quando terminar, salve a função e, em seguida, adicione a função como uma ação no aplicativo lógico que você está criando.

## <a name="add-azure-function-to-logic-app"></a>Adicionar a função do Azure ao aplicativo lógico

1. No Designer de Aplicativos Lógicos, na ação **Detectar sentimento**, escolha **Nova etapa**.

2. Localize o conector do **Azure Functions** e, em seguida, selecione a função que você criou.

3. Em **Corpo da solicitação**, selecione **Texto do tweet**.

![Etapa de função do Azure configurada][2]

## <a name="run-and-monitor-your-logic-app"></a>Executar e monitorar seu aplicativo lógico

Para examinar todas as execuções atuais ou anteriores para o aplicativo lógico, você pode usar os recursos de monitoramento e depuração avançada que os Aplicativos Lógicos do Azure fornecem no portal do Azure, o Visual Studio, ou por meio de SDKs e APIs REST do Azure.

Para testar facilmente seu aplicativo lógico, no Designer de Aplicativos Lógicos, escolha **Executar gatilho**. O gatilho sonda tweets com base em seu agendamento especificado, até encontrar um tweet que atenda aos seus critérios. Durante o andamento da execução, o designer mostra uma exibição em tempo real para essa execução.

Para exibir históricos de execução anteriores no Visual Studio ou no portal do Azure: 

* Abrir o Gerenciador de Nuvem do Visual Studio. Localizar seu aplicativo lógico, abra o menu de atalho do aplicativo. Selecione **Abrir histórico de execução**.

  > [!TIP]
  > Caso não tenha esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes do Visual Studio.

* No portal do Azure, localize o aplicativo lógico. No menu do aplicativo lógico, escolha **Visão geral**. 

## <a name="create-automated-deployment-templates"></a>Criar modelos de implantação automatizados

Depois de criar uma solução de aplicativo lógico, você pode capturar e implantar seu aplicativo como um [modelo do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment) em qualquer região do Azure no mundo. Você pode usar essa funcionalidade para modificar os parâmetros para a criação de versões diferentes do seu aplicativo e para integrar a solução no Azure Pipelines. Você também pode incluir o Azure Functions no seu modelo de implantação, para que você possa gerenciar a solução inteira, com todas as dependências, como um modelo único. Saiba como automatizar a [implantação do aplicativo lógico](logic-apps-azure-resource-manager-templates-overview.md).

Para um exemplo de um modelo de implantação com uma função do Azure, verifique o [repositório de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Próximas etapas

* [Localizar outros exemplos e cenários de Aplicativos Lógicos do Azure](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
