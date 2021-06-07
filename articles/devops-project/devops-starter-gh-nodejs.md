---
title: 'Início Rápido: Criar um fluxo de trabalho de CI/CD para Node.js – DevOps Starter para o GitHub para implantação no Azure'
description: O DevOps Starter facilita a introdução ao Azure usando o GitHub Actions.
author: ninallam
editor: ninallam
ms.assetid: ''
ms.devlang: na
ms.service: az-devops-project
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 08/24/2020
ms.author: ninallam
ms.openlocfilehash: e3c72ce6a15f90f0dbe08bbff10db0ca5f6b5c8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588804"
---
# <a name="set-up-cicd-for-a-nodejs-app-with-devops-starter-using-github-actions"></a>Configurar CI/CD para um aplicativo Node.js com o DevOps Starter usando o GitHub Actions

Neste início rápido, você usa a experiência simplificada do DevOps Starter para configurar um fluxo de trabalho de CI (integração contínua) e CD (entrega contínua) para seu aplicativo Node.js usando o GitHub Actions. Use o DevOps Starter para configurar tudo o que você precisa para desenvolver, implantar e monitorar seu aplicativo. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Uma conta do [GitHub](https://github.com/).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

O DevOps Starter cria um fluxo de trabalho de CI/CD usando o GitHub Actions. O DevOps Starter também cria recursos do Azure na assinatura do Azure de sua escolha.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, digite **DevOps Starter** e selecione. Clique em **Adicionar** para criar um.

    ![O painel do DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecionar um aplicativo de exemplo e o serviço do Azure

1. Clique em **Configurar DevOps Starter com o GitHub** na faixa do lado direito.

    ![landing_page](_img/azure-devops-project-nodejs/landing-page.png)

1. Verifique se o provedor de CI/CD está selecionado como o **GitHub Actions**.

    ![CICD_provider](_img/azure-devops-project-nodejs/provider-selection.png)

1. Selecione o aplicativo de exemplo do **Node.js**. Os exemplos do Node.js incluem uma opção de várias estruturas de aplicativo.

1. A estrutura de exemplo padrão é **Express.js**. Mantenha a configuração padrão e selecione **Avançar**.   

2. O Aplicativo Web do Windows é o destino de implantação padrão. A estrutura do aplicativo escolhida anteriormente determina o tipo de destino de implantação do serviço do Azure disponível aqui. Mantenha o serviço padrão e selecione **Avançar**.
 
## <a name="configure-github-account-and-an-azure-subscription"></a>Configurar a conta do GitHub e uma assinatura do Azure 

1. Autentique-se no GitHub.

   1. Clique no botão **Autorizar**. 
   
   1. Entre no GitHub. Se não tiver uma conta do GitHub, você poderá se inscrever aqui também.

2. Escolha uma **organização do GitHub** existente. 
   
   1. Escolha um nome para seu repositório do GitHub. 
   
   1. Selecione a assinatura e o local do Azure, escolha um nome para o aplicativo e selecione **Concluído**.
    
       ![Enter_GH_details](_img/azure-devops-project-nodejs/gh-details.png)


    Após alguns minutos, o painel do DevOps Starter será exibido no portal do Azure. Um aplicativo de exemplo é configurado em um repositório em sua organização do Azure DevOps, um fluxo de trabalho do GitHub é disparado e seu aplicativo é implantado no Azure. Esse painel oferece visibilidade de seu repositório de código, do fluxo de trabalho do GitHub e de seu aplicativo no Azure.
   
3. Selecione **Procurar** para exibir o aplicativo em execução.
    
    O painel contém detalhes do fluxo de trabalho do GitHub e dos recursos do Azure. Para ver os detalhes do fluxo de trabalho do GitHub, como a execução mais recente, as confirmações e o status dos trabalhos, será necessário **Autorizar no GitHub**.
   
   ![Authorize_dashboard](_img/azure-devops-project-nodejs/authenticate-dashboard.png)

O DevOps Starter configurou automaticamente um fluxo de trabalho do GitHub com trabalhos de build e implantação usando GitHub Actions. Agora você está pronto para colaborar com uma equipe em um aplicativo Node.js com um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site da Web.

   ![dashooard_view](_img/azure-devops-project-nodejs/full-dashboard.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Confirmar as alterações de código e executar CI/CD

O DevOps Starter cria um repositório no GitHub. Para exibir o repositório e fazer alterações de código no seu aplicativo, faça o seguinte:

1. Na esquerda do painel do DevOps Starter, selecione o link para a ramificação principal. Esse link abre uma exibição do repositório do GitHub recém-criado.

1. Para exibir a URL de clone do repositório, selecione **Clone** na parte superior direita do navegador. Você pode clonar seu repositório Git no seu IDE favorito. Nas próximas etapas, você pode usar o navegador da Web para criar e confirmar as alterações de código diretamente na ramificação principal.

1. À esquerda do navegador, acesse o arquivo **/Application/views/index.pug**.

1. Selecione **Editar**, depois faça uma alteração de alguma parte do texto.
    Por exemplo, altere partes do texto de uma das marcas.

1. Selecione **Confirmar**, depois salve as alterações.

1. No navegador, acesse o painel do DevOps Starter.   
Agora você deve ver um trabalho de build do fluxo de trabalho do GitHub em andamento. As alterações que você fez são criadas e implantadas automaticamente por meio de um fluxo de trabalho do GitHub.

## <a name="view-the-github-workflow"></a>Ver o fluxo de trabalho do GitHub

Na etapa anterior, o DevOps Starter configurou automaticamente um fluxo de trabalho do GitHub completo. Explore e personalize o fluxo de trabalho conforme necessário. Siga as etapas a seguir para se familiarizar com o fluxo de trabalho.

1. Na esquerda do painel do DevOps Starter, selecione o **fluxo de trabalho do GitHub**. Esse link abre uma guia de navegador e o fluxo de trabalho do GitHub do seu novo projeto.
    > [!NOTE]
    > Não renomeie o arquivo de fluxo de trabalho. O nome do arquivo de fluxo de trabalho deve ser **devops-starter-workflow.yml** para que o painel reflita as alterações

1. O arquivo YAML do fluxo de trabalho contém todas as GitHub Actions necessárias para criar e implantar o aplicativo. Clique na opção **editar arquivo** para personalizar o arquivo de fluxo de trabalho.

1. Na guia **Código** do repositório, clique em **confirmações**. Essa exibição mostra as confirmações de código associadas à implantação específica.

1. Na guia **Ações** do repositório, você pode ver o histórico de todas as execuções de fluxo de trabalho do seu repositório.

1. Selecione a **execução mais recente** para ver todos os trabalhos que foram executados no fluxo de trabalho.

1. Clique nos **trabalhos** para ver os logs detalhados da execução do fluxo de trabalho. Os logs contêm informações úteis sobre o processo de implantação. Eles podem ser exibidos durante e após as implantações.

1. Clique na guia **Solicitação de pull** para ver todas as solicitações de pull em seu repositório

## <a name="clean-up-resources"></a>Limpar os recursos

É possível excluir o Serviço de Aplicativo do Azure e outros recursos relacionados quando você não precisa mais deles. Use a funcionalidade **Excluir** no painel do DevOps Starter.

## <a name="next-steps"></a>Próximas etapas

Quando você configurou o processo de CI/CD, o fluxo de trabalho do GitHub foi criado automaticamente. Você pode modificar esse fluxo de trabalho para atender às necessidades de sua equipe. Para saber mais sobre o GitHub Actions e o fluxo de trabalho, confira:

> [!div class="nextstepaction"]
> [Personalizar fluxo de trabalho do GitHub](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-and-managing-workflow-files-and-runs)
