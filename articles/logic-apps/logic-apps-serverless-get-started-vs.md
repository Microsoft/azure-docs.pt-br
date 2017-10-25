---
title: Criar um aplicativo sem servidor no Visual Studio | Microsoft Docs
description: "Obtenha uma introdução ao seu primeiro aplicativo sem servidor com este guia em para criar, implantar e gerenciar o aplicativo no Visual Studio."
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
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 3672beda8a502e5fe2c8182076a8edef7ee9ebf6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-serverless-app-in-visual-studio-with-logic-apps-and-functions"></a>Criar um aplicativo sem servidor no Visual Studio com os Aplicativos Lógicos e o Functions

Ferramentas e funcionalidades sem servidor do Azure permitem rápido desenvolvimento e implantação de aplicativos de nuvem.  Este documento concentra-se em uma introdução à criação de um aplicativo sem servidor no Visual Studio.  Uma visão geral do uso sem servidor no Azure [pode ser encontrada neste artigo](logic-apps-serverless-overview.md).

## <a name="getting-everything-ready"></a>Deixando tudo pronto

Aqui estão os pré-requisitos necessários para criar um aplicativo sem servidor no Visual Studio:

* [Visual Studio 2017](https://www.visualstudio.com/vs/) ou Visual Studio 2015 – Community, Professional ou Enterprise
* [Ferramentas de Aplicativos Lógicos para Visual Studio](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551)
* [SDK mais recente do Azure](https://azure.microsoft.com/downloads/) (2.9.1 ou superior)
* [PowerShell do Azure](https://github.com/Azure/azure-powershell#installation)
* [Ferramentas Básicas do Azure Functions](https://www.npmjs.com/package/azure-functions-core-tools) para depurar Funções localmente
* Acesso à Web ao usar o designer de Aplicativo Lógico incorporado

## <a name="getting-started-with-a-deployment-template"></a>Introdução a um modelo de implantação

O gerenciamento de recursos no Azure é realizado dentro de um grupo de recursos.  Um grupo de recursos é um agrupamento lógico de recursos.  Grupos de recursos permitem a implantação e o gerenciamento de uma coleção de recursos.  Para um aplicativo sem servidor no Azure, nosso grupo de recursos contém tanto Aplicativo Lógico do Azure quanto Azure Functions.  Usando o projeto do grupo de recursos no Visual Studio, somos capazes de desenvolver, gerenciar e implantar o aplicativo inteiro como um único ativo.

### <a name="create-a-resource-group-project-in-visual-studio"></a>Criar um projeto de grupo de recursos no Visual Studio

1. No Visual Studio, clique para adicionar um **Novo Projeto**
1. Na categoria **Nuvem**, selecione para criar um projeto de **Grupo de Recursos** do Azure  
 * Se você não vir a categoria ou o projeto listado, verifique se você tem o SDK do Azure instalado para o Visual Studio
1. Dê ao projeto um nome e uma localização e selecione **Ok** para criar prompts do Visual Studio para seleção de um modelo.  Você pode selecionar para iniciar de espaço em branco, começar com um aplicativo lógico ou outro recurso.  No entanto, nesse caso, use um Modelo de Início Rápido do Azure para começar com um aplicativo sem servidor.
1. Selecione para mostrar os modelos de **Início rápido do Azure** ![Seleção de modelos do Início rápido do Azure][1]
1. Selecione o modelo de início rápido sem servidor: **101-logic-app-and-function-app** e clique em **Ok**

O modelo de início rápido cria um modelo de implantação em seu projeto de grupo de recursos.  O modelo contém um aplicativo lógico simples que chama o Azure Functions e retorna o resultado.  Se você abrir o arquivo `azuredeploy.json` no Gerenciador de Soluções, você poderá ver os recursos para o aplicativo sem servidor.

## <a name="deploying-the-serverless-application"></a>Implantar o aplicativo sem servidor

Antes de abrir o designer visual do Aplicativo Lógico no Visual Studio, precisa haver um grupo de recursos do Azure pré-implantado.  Isso permite que o designer crie e use conexões com serviços e recursos no aplicativo lógico.  Para começar, precisamos apenas implantar a solução criada.

1. Clique com o botão direito do mouse no projeto no Visual Studio, selecione **Implantar**e crie uma **Nova** implantação ![Selecionando nova implantação de recursos][2]
1. Selecione um Grupo de Recursos e uma assinatura válida do Azure
1. Selecione esta opção para **Implantar** a solução
1. Insira o nome do aplicativo lógico e do Aplicativo de Funções do Azure.  O nome da Função do Azure precisa ser globalmente exclusivo

A solução sem servidor é implantada no grupo de recursos especificado.  Se você examinar a **Saída** no Visual Studio, você poderá ver o status da implantação.

## <a name="editing-the-logic-app-in-visual-studio"></a>Editar o aplicativo lógico no Visual Studio

Depois que a solução foi implantada em qualquer grupo de recursos, o Designer Visual pode ser usado para editar e fazer alterações ao aplicativo lógico.

1. Clique com o botão direito do mouse no arquivo `azuredeploy.json` no Gerenciador de Soluções e selecione **Abrir com o Designer de Aplicativos Lógicos**
1. Selecione o **Grupo de Recursos** e a **Localização** para a qual a solução foi implantada e selecione **OK**

O designer visual do Aplicativo Lógico agora deve estar visível com o Visual Studio.  Você pode continuar a adicionar etapas, modificar o fluxo de trabalho e salvar as alterações.  Você também pode criar aplicativo lógico do Visual Studio.  Se você clicar com o botão direito do mouse em **Recursos** no navegador de modelos, você poderá optar por adicionar um **Aplicativo Lógico** ao projeto.  Aplicativos lógicos vazios são carregados no designer visual sem uma pré-implantação em um grupo de recursos.

### <a name="managing-and-viewing-run-history-for-a-deployed-logic-app"></a>Gerenciar e exibir o histórico de execução para um aplicativo lógico implantado

Você também pode gerenciar e exibir o histórico de execução de aplicativos lógicos implantados no Azure.  Se você abrir a ferramenta **Gerenciador de Nuvem** no Visual Studio, clique com o botão direito do mouse em qualquer aplicativo lógico e escolha editar, desabilitar, exibir propriedades ou exibir histórico de execução.  Clicar em editar também permite que você baixe um aplicativo lógico publicado em um projeto do grupo de recursos do Visual Studio.  Isso significa que mesmo que você desenvolva seu aplicativo lógico no Portal do Azure, você pode ainda importá-lo e gerenciá-lo do Visual Studio.

## <a name="developing-an-azure-function-in-visual-studio"></a>Desenvolvendo uma Função do Azure no Visual Studio

O modelo de implantação implanta todos os Azure Functions que estão contidos na solução para o repositório de git especificado nas variáveis `azuredeploy.json`.  Se você criar um projeto de função dentro da solução, inclua-o no controle de código-fonte (GitHub, Visual Studio Team Services, etc.) e atualize a variável `repo`, o modelo implantará a Função do Azure.

### <a name="creating-an-azure-function-project"></a>Criar um projeto de Função do Azure

Se usar JavaScript, Python, F #, Bash, Lote ou PowerShell, siga as [etapas na CLI do Functions](../azure-functions/functions-run-local.md) para criar um projeto.  Se estiver desenvolvendo uma função em C#, você poderá usar uma [biblioteca de classes do C#](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/) na solução atual para a Função do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Saiba como criar um painel social sem servidor](logic-apps-scenario-social-serverless.md)
* [Gerenciar um aplicativo lógico do Visual Studio Cloud Explorer](logic-apps-manage-from-vs.md)
* [Linguagem de definição de fluxo de trabalho de aplicativo lógico](logic-apps-workflow-definition-language.md)

<!-- Image references -->
[1]: ./media/logic-apps-serverless-get-started-vs/select-template.png
[2]: ./media/logic-apps-serverless-get-started-vs/deploy.png
