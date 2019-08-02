---
title: Implantar um aplicativo do Service Fabric com integração contínua e Azure Pipelines no Azure | Microsoft Docs
description: Neste tutorial, você aprende a configurar a integração e a implantação contínua de um aplicativo do Service Fabric usando o Azure Pipelines.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 184c57c0d9160cedef4be417f16c52c8635a1eb4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68385299"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Tutorial: Implantar um aplicativo com CI/CD em um cluster do Service Fabric

Este tutorial é a quarta parte de uma série e descreve como configurar a integração e a implantação contínua de um aplicativo do Azure Service Fabric usando o Azure Pipelines.  É necessário um aplicativo existente do Service Fabric e o aplicativo criado em [Criar um aplicativo .NET](service-fabric-tutorial-create-dotnet-app.md) é usado como exemplo.

Na terceira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Adicionar controle do código-fonte ao seu projeto
> * Criar um pipeline de build no Azure Pipelines
> * Criar um pipeline de lançamento no Azure Pipelines
> * Implantar e atualizar automaticamente um aplicativo

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Criar um aplicativo .NET do Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Implantar o aplicativo em um cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Adicionar um ponto de extremidade HTTPS a um serviço de front-end do ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Configurar CI/CD usando o Azure Pipelines
> * [Configurar monitoramento e diagnóstico para o aplicativo](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Instale o Visual Studio 2019](https://www.visualstudio.com/) e as cargas de trabalho **Desenvolvimento do Azure** e **Desenvolvimento para a Web e ASP.NET**.
* [Instalar o SDK do Service Fabric](service-fabric-get-started.md)
* Crie um cluster do Service Fabric do Windows no Azure; você pode [seguir este tutorial](service-fabric-tutorial-create-vnet-and-windows-cluster.md) como exemplo
* Criar uma [organização do Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student). Isso permite que você crie um projeto no Azure DevOps e use Azure Pipelines.

## <a name="download-the-voting-sample-application"></a>Baixar o aplicativo de exemplo Votação

Se você não tiver criado o aplicativo de exemplo Votação na [parte um esta série de tutoriais](service-fabric-tutorial-create-dotnet-app.md), poderá baixá-lo. Em uma janela de comando, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Preparar um perfil de publicação

Agora que você [criou um aplicativo](service-fabric-tutorial-create-dotnet-app.md) e [implantou o aplicativo no Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md), você está pronto para configurar a integração contínua.  Primeiro, prepare um perfil de publicação em seu aplicativo para ser usado pelo processo de implantação que é executado no Azure Pipelines.  O perfil de publicação deve ser configurado para direcionar-se ao cluster que você criou anteriormente.  Inicie o Visual Studio e abra um projeto de aplicativo do Service Fabric existente.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no aplicativo e selecione **Publicar...** .

Escolha um perfil de destino em seu projeto de aplicativo a fim de usar para o fluxo de trabalho de integração contínua, por exemplo, Nuvem.  Especifique o ponto de extremidade de conexão do cluster.  Marque a caixa de seleção **Atualizar o Aplicativo** para que seu aplicativo seja atualizado a cada implantação no Azure DevOps.  Clique no hiperlink **Salvar** para salvar as configurações em seu perfil de publicação e, em seguida, clique em **Cancelar** para fechar a caixa de diálogo.

![Perfil de envio por push][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Compartilhar sua solução do Visual Studio em um novo repositório Git do Azure DevOps

Compartilhe os arquivos de origem do aplicativo em um projeto no Azure DevOps para que você possa gerar builds.

Crie um novo repositório Git local para seu projeto selecionando **Adicionar ao controle do código-fonte** -> **Git** na barra de status no canto inferior direito do Visual Studio.

Na exibição **Push** no **Team Explorer**, selecione o botão **Publicar Repositório Git** em **Efetuar Push para o Azure DevOps**.

![Enviar por push o repositório Git][push-git-repo]

Verifique seu email e selecione sua conta na lista suspensa **Domínio do Azure DevOps**. Digite o nome do seu repositório e selecione **Publicar Repositório**.

![Enviar por push o repositório Git][publish-code]

A publicação do repositório cria um novo projeto na conta com o mesmo nome do repositório local. Para criar o repositório em um projeto existente, clique em **Avançado** ao lado do nome do **Repositório** e escolha um projeto. Você pode exibir seu código na Web selecionando **Vê-lo na Web**.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Configurar a entrega contínua com o Azure Pipelines

Um pipeline de build do Azure Pipelines descreve um fluxo de trabalho composto por um conjunto de etapas de build que são executadas sequencialmente. Crie um pipeline de build que produz um pacote de aplicativos do Service Fabric e outros artefatos para implantar em um cluster do Service Fabric. Saiba mais sobre os [pipelines de build do Azure Pipelines](https://www.visualstudio.com/docs/build/define/create). 

Um pipeline de lançamento do Azure Pipelines descreve um fluxo de trabalho que implanta um pacote de aplicativos em um cluster. Quando usados juntos, o pipeline de lançamento e o pipeline de build executam todo o fluxo de trabalho, começando com os arquivos de origem e terminando com um aplicativo em execução em seu cluster. Saiba mais sobre os [pipelines de lançamento do Azure Pipelines](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-build-pipeline"></a>Criar um pipeline de build

Abra um navegador da Web e navegue até seu novo projeto em: [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting).

Selecione a guia **Pipelines**, **Builds** e clique em **Novo Pipeline**.

![Novo Pipeline][new-pipeline]

Selecione **Git do Azure Repos** como fonte, projeto **Voting** Team, Repositório **Voting** e o branch Padrão **mestre** para builds manuais e agendados.  Clique em **Continuar**.

![Selecionar repositório][select-repo]

Em **Selecionar um modelo**, selecione o modelo **Aplicativo do Azure Service Fabric** e clique em **Aplicar**.

![Escolher o modelo de build][select-build-template]

Em **Tarefas**, insira "VS2017 Hospedado" como o**Pool de agentes**.

![Selecionar tarefas][save-and-queue]

Em **Gatilhos**, habilite a integração contínua marcando **Habilitar a integração contínua**. Dentro de **Filtros de branch**, a **Especificação de branch** assume **mestre** como padrão. Selecione **Salvar e enfileirar** para iniciar uma compilação manualmente.

![Selecionar gatilhos][save-and-queue2]

A compilação também é disparada durante o push ou o check-in. Para verificar o progresso da compilação, alterne para a guia **Compilações**.  Depois de verificar que o build foi executado com êxito, configure um pipeline de lançamento para implantar aplicativo em um cluster.

### <a name="create-a-release-pipeline"></a>Criar um pipeline de lançamento

Selecione a guia **Pipelines**, **Versões** e **+ Novo pipeline**.  Em **Selecionar um modelo**, selecione o modelo **Implantação do Azure Service Fabric** na lista e **Aplicar**.

![Escolher modelo de versão][select-release-template]

Selecione **Tarefas**->**Ambiente 1** e **+ Novo** para adicionar uma nova conexão do cluster.

![Adicionar conexão do cluster][add-cluster-connection]

No modo de exibição **Adicionar nova Conexão do Service Fabric**, selecione a autenticação **Baseada em Certificado** ou **Azure Active Directory**.  Especifique o nome de conexão de "mysftestcluster" e um ponto de extremidade do cluster "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" (ou o ponto de extremidade do cluster em que você está implantando).

Para a autenticação baseada em certificado, adicione a **Impressão digital do certificado do servidor** do certificado do servidor usado para criar o cluster.  Em **Certificado do cliente**, adicione a codificação de base 64 do arquivo de certificado do cliente. Veja no pop-up de ajuda do campo informações sobre como obter essa representação de codificação de base 64 do certificado. Essa também é a **Senha** para o arquivo do certificado.  Se você não tiver um certificado do cliente ou do servidor separado, poderá usar o certificado de cluster ou do servidor.

Para as credenciais do Azure Active Directory, adicione a **Impressão digital do certificado de servidor** do certificado do servidor usado para criar o cluster e as credenciais que deseja usar para se conectar ao cluster nos campos **Nome de Usuário** e **Senha**.

Clique em **Adicionar** para salvar a conexão do cluster.

Em seguida, adicione um artefato de compilação ao pipeline para que o pipeline de lançamento possa encontrar a saída da compilação. Selecione **Pipeline** e **Artefatos**-> **+Adicionar**.  Em **Fonte (Definição de compilação)** , selecione o pipeline de build criado anteriormente.  Clique em **Adicionar** para salvar o artefato de compilação.

![Adicionar artefato][add-artifact]

Habilite um gatilho de implantação contínua para que uma versão seja criada automaticamente quando a compilação for concluída. Clique no ícone de raio no artefato, habilite o gatilho e clique em **Salvar** para salvar o pipeline de lançamento.

![Habilitar gatilho][enable-trigger]

Selecione **+ Versão** -> **Criar uma Versão** -> **Criar** para criar manualmente uma versão. Você pode monitorar o andamento da versão na guia **Versões**.

Verifique se a implantação foi bem-sucedida e o aplicativo está em execução no cluster.  Abra um navegador da Web e navegue até [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Observe a versão do aplicativo, neste exemplo é "1.0.0.20170616.3".

## <a name="commit-and-push-changes-trigger-a-release"></a>Confirmar e enviar alterações por push, disparar uma versão

Para verificar se o pipeline de integração contínua está funcionando conferindo algumas alterações de código no Azure DevOps.

Ao escrever seu código, suas alterações são rastreadas automaticamente pelo Visual Studio. Confirme as alterações em seu repositório Git local, selecionando o ícone de alterações pendentes (![Pendente][pending]) na barra de status na parte inferior direita.

Na exibição **Alterações** no Team Explorer, adicione uma mensagem que descreva a atualização e confirme suas alterações.

![Confirmar tudo][changes]

Selecione o ícone de barra de status de alterações não publicadas (![Alterações não publicadas][unpublished-changes]) ou a exibição de sincronização no Team Explorer. Selecione **Push** para atualizar seu código no Azure Pipelines.

![Enviar alterações por push][push]

O push das alterações para o Azure Pipelines dispara um build automaticamente.  Quando o pipeline de build é concluído com êxito, uma versão é criada automaticamente e inicia a atualização do aplicativo no cluster.

Para verificar o progresso do build, alterne para a guia **Builds** no **Team Explorer** no Visual Studio.  Depois de verificar que o build foi executado com êxito, configure um pipeline de lançamento para implantar aplicativo em um cluster.

Verifique se a implantação foi bem-sucedida e o aplicativo está em execução no cluster.  Abra um navegador da Web e navegue até [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Observe a versão do aplicativo, neste exemplo é "1.0.0.20170815.3".

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Atualizar o aplicativo

Fazer alterações de código no aplicativo.  Salve e confirme as alterações, seguindo as etapas anteriores.

Depois que a atualização do aplicativo se inicia, você pode observar o progresso da atualização no Service Fabric Explorer:

![Service Fabric Explorer][sfx2]

A atualização do aplicativo pode levar vários minutos. Quando a atualização estiver concluída, o aplicativo estará executado a próxima versão.  Neste exemplo, "1.0.0.20170815.4".

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Adicionar controle do código-fonte ao seu projeto
> * Criar um pipeline de build
> * Criar um pipeline de lançamento
> * Implantar e atualizar automaticamente um aplicativo

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Configurar monitoramento e diagnóstico para o aplicativo](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewPipeline.png
[select-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectRepo.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-AzureDevOpsServices]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
