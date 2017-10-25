---
title: "Cenário - Criação de um painel de informações do cliente com o Azure sem servidor | Microsoft Docs"
description: "Um exemplo de como você pode criar um painel para gerenciar os comentários dos clientes, dados sociais e muito mais com aplicativos lógicos do Azure e funções do Azure."
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: jehollan
ms.openlocfilehash: 0b6e118cb13ab8185d8eeb42bec6147155967967
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-real-time-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Criação de um painel de informações do cliente em tempo real com aplicativos lógicos do Azure e funções do Azure

Ferramentas sem servidor do Azure fornecem recursos avançados para criar e hospedar aplicativos na nuvem, sem precisar pensar sobre a infraestrutura.  Nesse cenário, criaremos um painel que terá como gatilho os comentários de cliente, analisará os comentários com o aprendizado de máquina e publicará informações em uma fonte como o Power BI ou Azure Data Lake.

## <a name="overview-of-the-scenario-and-tools-used"></a>Visão geral do cenário e das ferramentas usadas

Para implementar essa solução, estenderemos os dois componentes principais de aplicativos sem servidor no Azure: [Funções do Azure](https://azure.microsoft.com/services/functions/) e [Aplicativos lógicos do Azure](https://azure.microsoft.com/services/logic-apps/).

Aplicativos lógicos são um mecanismo de fluxo de trabalho sem servidor na nuvem.  Eles fornecem orquestração em componentes sem servidor e também se conectam a mais de 100 serviços e APIs.  Para este cenário, criaremos um aplicativo lógico que terá como gatilho os comentários de clientes.  Alguns dos conectores que podem ajudar a reagir aos comentários dos clientes incluem Outlook.com, Office 365, Survey Monkey, Twitter e uma solicitação HTTP [de um formulário web](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/).  No fluxo de trabalho abaixo, monitoraremos uma hashtag no Twitter.

As funções fornecem computação sem servidor na nuvem.  Nesse cenário, usaremos as funções do Azure para sinalizar tweets de clientes com base em uma série de palavras-chave predefinidas.

A solução inteira pode ser [de compilação no Visual Studio](logic-apps-deploy-from-vs.md) e [implantados como parte de um modelo de recurso](logic-apps-create-deploy-template.md).  Também há vídeo passo a passo do cenário [no Channel 9](http://aka.ms/logicappsdemo).

## <a name="build-the-logic-app-to-trigger-on-customer-data"></a>Criar o aplicativo lógico para ser disparado em dados do cliente

Depois de [criar um aplicativo lógico](logic-apps-create-a-logic-app.md) no Visual Studio ou no portal do Azure:

1. Adicione um gatilho para **novos tweets** do Twitter
2. Configure o gatilho para escutar tweets com uma palavra-chave ou hashtag.

   > [!NOTE]
   > A propriedade de recorrência do gatilho determina com que frequência o aplicativo lógico verifica novos itens nos gatilhos de sondagem

   ![Exemplo de gatilho do Twitter][1]

Agora, este aplicativo será acionado em todos os tweets novos.  Podemos então levar dados de tweet e compreender melhor o sentimento expressado.  Para isso, usamos o [serviços cognitivos do Azure](https://azure.microsoft.com/services/cognitive-services/) para detecção de sentimento do texto.

1. Clique em **Nova Etapa**
1. Selecione ou pesquise o conector **análise de texto**
1. Selecione a operação **Detecção de sentimento**
1. Se solicitado, forneça uma chave de serviços cognitivos válida para o serviço de análise de texto
1. Adicionar o **texto do Tweet** como o texto para analisar.

Agora que temos os dados de tweet e análises sobre o tweet, uma série de outros conectores podem ser relevantes:
* Power BI - adicionar linhas ao conjunto de dados de streaming: exibição em tempo real de tweets em um painel do Power BI.
* Azure Data Lake - Acrescentar arquivo: adicionar dados do cliente em um conjunto de dados do Azure Data Lake para incluir nos trabalhos de análise.
* SQL - Adicionar linhas: armazenar dados em um banco de dados para recuperação posterior.
* Slack - Envia a mensagem: um canal do Slack quando um comentário negativo requer ações de alerta.

Uma função do Azure também pode ser usada para fazer mais computação personalizada sobre os dados.

## <a name="enriching-the-data-with-an-azure-function"></a>Como enriquecer os dados com uma função do Azure

Para que possa criar uma função, precisamos ter um aplicativo de função em nossa assinatura do Azure.  Detalhes sobre a criação de uma função do Azure no portal podem [ser encontradas aqui](../azure-functions/functions-create-first-azure-function-azure-portal.md)

Para uma função a ser chamada diretamente de um aplicativo lógico, ela precisa ter um gatilho HTTP associado.  Recomendamos usar o modelo **HttpTrigger**.

Nesse cenário, o corpo da solicitação da função do Azure é o texto do tweet.  No código da função, simplesmente defina a lógica indicando se o texto do tweet contém uma frase ou palavra-chave.  A própria função pode ser mantida simples ou complexa conforme necessário para o cenário.

No final da função, basta retornar uma resposta para o aplicativo lógico com alguns dados.  Isso pode ser um valor booliano simples (por exemplo, `containsKeyword`), ou um objeto complexo.

![Etapa de função do Azure configurada][2]

> [!TIP]
> Ao acessar uma resposta complexa de uma função em um aplicativo lógico, use a ação analisar JSON.

Quando a função é salva, ela pode ser adicionada ao aplicativo lógico criado anteriormente.  No aplicativo lógico:

1. Clique para adicionar uma **Nova Etapa**
1. Selecione o conector**Funções do Azure**
1. Selecione para escolher uma função existente e vá para a função criada
1. Enviar o **texto do tweet** para o **corpo da solicitação**

## <a name="running-and-monitoring-the-solution"></a>Execução e monitoração da solução

Um dos benefícios da criação de orquestrações sem servidor de aplicativos lógicos é de a depuração avançada e os recursos de monitoramento.  Qualquer execução (atual ou histórica) pode ser visualizada do Visual Studio, do portal do Azure ou da API REST e SDKs.

Uma das maneiras mais fáceis de testar um aplicativo lógico é usar o botão**Executar** no designer.  Clicar em **Executar** continuará a sondar o gatilho a cada 5 segundos até que um evento seja detectado e forneça uma exibição em tempo real conforme o andamento da execução.

O histórico de execução anterior pode ser exibido na folha de visão geral no portal do Azure ou usando o Visual Studio Cloud Explorer.

## <a name="creating-a-deployment-template-for-automated-deployments"></a>Criação de um modelo de implantação para implantações automatizadas

Depois de uma solução ser desenvolvida, ela pode ser capturada e implantada por meio de um modelo de implantação do Azure para qualquer região do Azure no mundo.  Isso é útil para ambos os parâmetros de modificação para versões diferentes deste fluxo de trabalho, e também para a integração dessa solução em um pipeline de compilação e versão.  Detalhes sobre como criar um modelo de implantação podem ser encontrados [neste artigo](logic-apps-create-deploy-template.md).

Funções do Azure também podem ser incorporadas no modelo de implantação, para que a solução inteira, com todas as dependências, possa ser gerenciada como um modelo único.  Um exemplo de um modelo de implantação de função pode ser encontrado no [repositório de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Próximas etapas

* [Confira outros exemplos e cenários de aplicativos lógicos do Azure](logic-apps-examples-and-scenarios.md)
* [Assista a um vídeo passo a passo sobre como criar essa solução de ponta a ponta](http://aka.ms/logicappsdemo)
* [Saiba como manipular e capturar exceções dentro de um aplicativo lógico](logic-apps-exception-handling.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png