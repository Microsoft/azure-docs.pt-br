---
title: 'Início Rápido: Criar um pipeline de CI/CD para .NET com o Azure DevOps Starter'
description: Com o Azure DevOps Starter, é mais fácil começar a usar o Azure. Ele ajuda você a iniciar um aplicativo .NET em um serviço do Azure de sua escolha com algumas etapas rápidas.
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 02/23/2021
ms.author: mlearned
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 75531b9a75c2d28ad665748a9be4e37e2056b451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547961"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-starter"></a>Criar um pipeline de CI/CD para .NET com o Azure DevOps Starter

Configure a CI (integração contínua) e a CD (entrega contínua) para seu aplicativo .NET Core ou ASP.NET com o DevOps Starter. O DevOps Starter simplifica a configuração inicial de um pipeline de build e lançamento no Azure Pipelines.

Caso não tenha uma assinatura do Azure, você pode obter uma gratuita via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

O DevOps Starter cria um pipeline de CI/CD no Azure DevOps. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O DevOps Starter também cria recursos do Azure na assinatura do Azure de sua escolha.

1. Entre no [Portal do Microsoft Azure](https://portal.azure.com).

1. Na caixa de pesquisa, digite **DevOps Starter** e selecione. Clique em **Adicionar** para criar um. 

    ![O painel do DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecionar um aplicativo de exemplo e o serviço do Azure

1. Selecione o aplicativo de exemplo do **.NET**. Os exemplos do .NET incluem uma opção de estrutura do ASP.NET de fonte aberta ou estrutura do .NET Core de plataforma cruzada.

   ![.NET Framework](_img/azure-devops-project-aspnet-core/select-dotnet.png)
   
   > [!NOTE]
   > A opção padrão para configurar o iniciador do DevOps é com o **GitHub**, mas essa configuração pode ser alterada por meio do assistente.
2. Este exemplo é um aplicativo ASP.NET Core MVC. Selecione a estrutura de aplicativo do **.NET Core** e selecione **Avançar**.    
    
3. Selecione **Aplicativo Web do Windows** como um destino de implantação, em seguida, selecione **Avançar**. Opcionalmente, você pode escolher outros serviços do Azure para sua implantação. A estrutura do aplicativo, que foi escolhida anteriormente, determina o tipo de destino de implantação do serviço do Azure disponível aqui.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure 

1. Insira um **Nome de projeto**.

2. Crie uma nova **Organização do Azure DevOps** gratuita ou escolha uma existente no menu suspenso.

3. Selecione sua **Assinatura do Azure**, insira um nome para seu **aplicativo Web** ou utilize o padrão e selecione **Concluído**. Após alguns minutos, a Visão Geral da Implantação do DevOps Starter é exibida no portal do Azure. 

4. Selecione **Ir para o recurso** para ver o painel do DevOps Starter. No canto superior direito, fixe o **Projeto** ao seu painel para ter acesso rápido. Um aplicativo de exemplo é configurado em um repositório em sua **Organização do Azure DevOps**. Uma compilação é executada e seu aplicativo é implantado no Azure.

5. O painel oferece visibilidade no seu repositório de código, pipeline de CI/CD e aplicativo no Azure. À direita, em recursos do Azure, selecione **Procurar** para exibir o aplicativo em execução.

   ![Exibição Painel](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Confirmar as alterações de código e executar CI/CD

O DevOps Starter criou um repositório Git no Azure Repos ou no GitHub. Para exibir o repositório e fazer alterações de código no seu aplicativo, faça o seguinte:

1. Na esquerda do painel do DevOps Starter, selecione o link para a ramificação **principal**. Esse link abre uma exibição do repositório de Git recém-criado.

2. Nas próximas etapas, você pode usar o navegador da Web para criar e confirmar as alterações de código diretamente na ramificação **principal**. Também pode clonar seu repositório Git no seu IDE favorito, selecionando **Clonar** na parte superior direita da página de repositório. 

3. À esquerda, navegue pela estrutura de arquivos do aplicativo até **Application/aspnet-core-dotnet-core/Pages/Index.cshtml**.

4. Selecione **Editar**, depois faça uma alteração no cabeçalho h2. Por exemplo, digite **Começar agora mesmo com o Azure DevOps Starter** ou faça outra alteração.

      ![Edições de código](_img/azure-devops-project-aspnet-core/codechange.png)

5. Selecione **Confirmar**, deixe um comentário e selecione **Confirmar** novamente.

6. No navegador, acesse o painel do Azure DevOps Starter.  Agora você deve ver uma compilação que está em andamento. As alterações feitas são automaticamente compiladas e implantadas via pipeline de CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Examinar o pipeline de CI/CD

Na etapa anterior, o Azure DevOps Starter configurou automaticamente um pipeline de CI/CD completo. Explore e personalize o pipeline conforme necessário. Siga as etapas a seguir para se familiarizar com os pipelines de build e de lançamento do Azure DevOps.

1. Na parte superior do painel do DevOps Starter, selecione **Pipelines de Build**. Esse link abre uma guia de navegador e o pipeline de build do Azure DevOps para seu novo projeto.

1. Selecione as reticências (...).  Essa ação abre um menu no qual você pode iniciar várias atividades, como enfileirar um novo build, pausar um build e editar o pipeline de build.

1. Selecione **Editar**.

    ![Pipeline de build](_img/azure-devops-project-aspnet-core/builddef.png)

1. Nesse painel, é possível examinar as diversas tarefas do pipeline de build. A compilação realiza várias tarefas, como efetuar buscas das fontes no repositório Git, restaurar dependências e publicar as saídas usadas para as implantações.

1. Na parte superior do pipeline de build, selecione o nome desse pipeline.

1. Altere o nome do pipeline de build para algo mais descritivo, selecione **Salvar e enfileirar** e selecione **Salvar**.

1. Embaixo do nome do pipeline de build, selecione **Histórico**.   
No painel **Histórico**, é exibida uma trilha de auditoria das alterações recentes do build.  O Azure Pipelines controla quaisquer alterações feitas no pipeline de build e permite que você compare versões.

1. Selecione **Gatilhos**. O DevOps Starter criou automaticamente um gatilho de CI, e cada confirmação no repositório inicia um novo build. Você pode optar por incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

1. Selecione **Build e lançamento**, depois selecione **Lançamentos**.  
O DevOps Starter cria um pipeline de lançamento para gerenciar implantações no Azure.

1.  À esquerda, selecione as reticências (...) ao lado do pipeline de lançamento, depois selecione **Editar**. O pipeline de lançamento contém um pipeline, o qual define o processo de lançamento.  

1. Em **Artefatos**, selecione **Soltar**. O pipeline de build examinado nas etapas anteriores produz a saída usada para o artefato. 

1. Ao lado do ícone **Soltar**, selecione o **Gatilho de implantação contínua**. Esse pipeline de lançamento tem um gatilho de CD habilitado, o qual executa uma implantação sempre que houver um novo artefato de build disponível. Outra opção é desabilitar o gatilho para que suas implantações exijam uma execução manual.  

1. À esquerda, selecione **Tarefas**.  As tarefas são as atividades realizadas pelo seu processo de implantação. Neste exemplo, uma tarefa foi criada para ser implantada no Serviço de Aplicativo do Azure.

1. À direita, selecione **Exibir versões**. Essa exibição mostra um histórico de versões.

1. Selecione as reticências (...) ao lado de uma das versões, depois selecione **Abrir**. Há vários menus para explorar, como um resumo da versão, itens de trabalho associados e testes.

1. Selecione **Confirmações**. Essa exibição mostra as confirmações de código associadas à implantação específica. 

1. Selecione **Logs**. Os logs contêm informações úteis sobre o processo de implantação. Eles podem ser exibidos durante e após as implantações.

## <a name="clean-up-resources"></a>Limpar os recursos

É possível excluir o Serviço de Aplicativo do Azure e outros recursos criados quando eles não forem mais precisos. Use a funcionalidade **Excluir** no painel do DevOps Starter.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como modificar os pipelines de build e de lançamento para atender às necessidades de sua equipe, consulte este tutorial:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](/azure/devops/pipelines/release/define-multistage-release-process)

## <a name="videos"></a>vídeos

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
