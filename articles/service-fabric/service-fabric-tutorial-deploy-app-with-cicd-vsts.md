---
title: "Implantar um aplicativo do Azure Service Fabric com integração contínua (Team Services) | Microsoft Docs"
description: "Saiba como configurar a integração e a implantação contínua para um aplicativo do Service Fabric usando o Visual Studio Team Services.  Implantar um aplicativo em um cluster do Service Fabric no Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: d0f67b1a63c36e878ed5f7c9aa1c45267e5b156c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Implantar um aplicativo com CI/CD em um cluster do Service Fabric
Este tutorial é a terceira parte de uma série e descreve como configurar a integração e a implantação contínua para um aplicativo do Service Fabric do Azure usando o Visual Studio Team Services.  É necessário um aplicativo existente do Service Fabric e o aplicativo criado em [Criar um aplicativo .NET](service-fabric-tutorial-create-dotnet-app.md) é usado como exemplo.

Na terceira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Adicionar controle do código-fonte ao seu projeto
> * Criar uma definição de build no Team Services
> * Criar uma definição de versão no Team Services
> * Implantar e atualizar automaticamente um aplicativo

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Criar um aplicativo .NET do Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Implantar o aplicativo em um cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * Configurar CI/CD usando o Visual Studio Team Services
> * [Configurar monitoramento e diagnóstico para o aplicativo](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Instale o Visual Studio 2017](https://www.visualstudio.com/) e instale as cargas de trabalho de **desenvolvimento do Azure** e de **desenvolvimento para a Web e ASP.NET**.
- [Instalar o SDK do Service Fabric](service-fabric-get-started.md)
- Crie um aplicativo do Service Fabric; você pode [seguir este tutorial](service-fabric-tutorial-create-dotnet-app.md) como exemplo. 
- Crie um cluster do Service Fabric do Windows no Azure; você pode [seguir este tutorial](service-fabric-tutorial-create-cluster-azure-ps.md) como exemplo
- Crie uma [conta do Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).

## <a name="download-the-voting-sample-application"></a>Baixar o aplicativo de exemplo Votação
Se você não tiver criado o aplicativo de exemplo Votação na [parte um esta série de tutoriais](service-fabric-tutorial-create-dotnet-app.md), poderá baixá-lo. Em uma janela de comando, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Preparar um perfil de publicação
Agora que você [criou um aplicativo](service-fabric-tutorial-create-dotnet-app.md) e [implantou o aplicativo no Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md), você está pronto para configurar a integração contínua.  Primeiro, prepare um perfil de publicação em seu aplicativo para ser usado pelo processo de implantação que é executado no Team Services.  O perfil de publicação deve ser configurado para direcionar-se ao cluster que você criou anteriormente.  Inicie o Visual Studio e abra um projeto de aplicativo do Service Fabric existente.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no aplicativo e selecione **Publicar...**.

Escolha um perfil de destino em seu projeto de aplicativo a fim de usar para o fluxo de trabalho de integração contínua, por exemplo, Nuvem.  Especifique o ponto de extremidade de conexão do cluster.  Marque a caixa de seleção **Atualizar o Aplicativo** para que seu aplicativo seja atualizado a cada implantação no Team Services.  Clique no hiperlink **Salvar** para salvar as configurações em seu perfil de publicação e, em seguida, clique em **Cancelar** para fechar a caixa de diálogo.  

![Perfil de envio por push][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Compartilhar sua solução do Visual Studio em um novo repositório Git do Team Services
Compartilhe os arquivos de origem do seu aplicativo em um projeto de equipe no Team Services, para que você possa gerar builds.  

Crie um novo repositório Git local para seu projeto selecionando **Adicionar ao controle do código-fonte** -> **Git** na barra de status no canto inferior direito do Visual Studio. 

Na exibição **Push** no **Team Explorer**, selecione o botão **Publicar Repositório Git** em **Enviar por Push para o Visual Studio Team Services**.

![Enviar por push o repositório Git][push-git-repo]

Verifique seu email e selecione sua conta na lista suspensa **Domínio do Team Services**. Digite o nome do seu repositório e selecione **Publicar Repositório**.

![Enviar por push o repositório Git][publish-code]

A publicação do repositório cria um novo projeto de equipe em sua conta com o mesmo nome do repositório local. Para criar o repositório em um projeto de equipe existente, clique em **Avançado** ao lado do nome do **Repositório** e selecione um projeto de equipe. Você pode exibir seu código na Web selecionando **Vê-lo na Web**.

## <a name="configure-continuous-delivery-with-vsts"></a>Configurar a entrega contínua com VSTS
Uma definição de build do Team Services descreve um fluxo de trabalho composto por um conjunto de etapas de compilação que são executadas sequencialmente. Crie uma definição de build que produza um pacote de aplicativos do Service Fabric e outros artefatos, para implantar em um cluster do Service Fabric. Saiba mais sobre as [definições de build do Team Services](https://www.visualstudio.com/docs/build/define/create). 

Uma definição de versão do Team Services descreve um fluxo de trabalho que implanta um pacote de aplicativos em um cluster. Quando usadas juntas, a definição de build e a definição de versão executam todo o fluxo de trabalho, começando com os arquivos de origem e terminando com um aplicativo em execução em seu cluster. Saiba mais sobre as [definições de versão](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)do Team Services.

### <a name="create-a-build-definition"></a>Criar a definição de build
Abra um navegador da web e navegue até seu novo projeto de equipe em: https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting. 

Selecione a guia **Build e versão** e então **Compilações** e **+ Nova definição**.  Em **selecionar um modelo de**, selecione o **aplicativo do Azure Service Fabric** modelo e clique em **aplicar**. 

![Escolher o modelo de build][select-build-template] 

O aplicativo de votação contém um projeto .NET Core, para adicionar uma tarefa que restaura as dependências. Na exibição **Tarefas**, selecione **+ Adicionar Tarefa** na parte inferior esquerda. Pesquise na "Linha de comando" para localizar a tarefa de linha de comando e clique em **Adicionar**. 

![Adicionar tarefa][add-task] 

Na nova tarefa, digite "Executar dotnet.exe" do **nome de exibição**, "dotnet.exe" em **ferramenta**e "restauração" no **argumentos**. 

![Nova tarefa][new-task] 

No **gatilhos** exibir, clique no **habilitar esse gatilho** alternar em **integração contínua**. 

Selecione **Salvar & fila** e digite "VS2017 Hospedado" como a **Fila de agentes**. Selecione **Fila** para iniciar manualmente uma compilação.  A compilação também é disparada durante o push ou check-in.

Para verificar o progresso da compilação, alterne para a guia **Compilações**.  Assim que verificar que o build é executado com êxito, configure uma definição de versão que implante seu aplicativo em um cluster. 

### <a name="create-a-release-definition"></a>Criar uma definição de versão  

Selecione o **Build e versão** guia, em seguida, **versões**, em seguida, **+ nova definição**.  Em **Criar definição de versão**, selecione o **Implantação do Azure Service Fabric** modelo na lista e clique em **próximo**.  Selecione o **criar** fonte, verifique o **implantação contínua** caixa e clique em **criar**. 

Na exibição **Ambientes**, clique em **Adicionar** à direita de **Conexão de Cluster**.  Especifique um nome de conexão de "mysftestcluster", um ponto de extremidade do cluster de "tcp://mysftestcluster.westus.cloudapp.azure.com:19000" e o Azure Active Directory ou as credenciais de certificado para o cluster. Para as credenciais do Azure Active Directory, defina as credenciais que você deseja usar para se conectar ao cluster nos campos **Nome de Usuário** e **Senha**. Para a autenticação baseada em certificado, defina a codificação de Base64 do arquivo de certificado do cliente no campo **Certificado de Cliente**.  Consulte o pop-up de ajuda nesse campo para obter informações sobre como obter esse valor.  Se seu certificado for protegido por senha, defina a senha no campo **Senha** .  Clique em **Salvar** para salvar a definição de versão.

![Adicionar conexão do cluster][add-cluster-connection] 

Clique em **Executar em agente** e selecione **VS2017 Hospedado** para **Fila de implantação**. Clique em **Salvar** para salvar a definição de versão.

![Executar no agente][run-on-agent]

Selecione **+ Versão** -> **Criar Versão** -> **Criar** para criar manualmente uma versão.  Verifique se a implantação foi bem-sucedida e o aplicativo está em execução no cluster.  Abra um navegador da Web e navegue até [http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/).  Observe a versão do aplicativo, neste exemplo é "1.0.0.20170616.3". 

## <a name="commit-and-push-changes-trigger-a-release"></a>Confirmar e enviar alterações por push, disparar uma versão
Para verificar se o pipeline de integração contínua está funcionando ao fazer check-in de algumas alterações de código no Team Services.    

Ao escrever seu código, suas alterações são rastreadas automaticamente pelo Visual Studio. Confirme as alterações em seu repositório Git local, selecionando o ícone de alterações pendentes (![Pendente][pending]) na barra de status na parte inferior direita.

Na exibição **Alterações** no Team Explorer, adicione uma mensagem que descreva a atualização e confirme suas alterações.

![Confirmar tudo][changes]

Selecione o ícone de barra de status de alterações não publicadas (![Alterações não publicadas][unpublished-changes]) ou a exibição de sincronização no Team Explorer. Selecione **Push** para atualizar seu código no Team Services/TFS.

![Enviar alterações por push][push]

Enviar por push as alterações ao Team Services dispara um build automaticamente.  Quando a definição de build é concluída com êxito, uma versão é criada automaticamente e inicia a atualização do aplicativo no cluster.

Para verificar o progresso do build, alterne para a guia **Builds** no **Team Explorer** no Visual Studio.  Assim que verificar que o build é executado com êxito, configure uma definição de versão que implante seu aplicativo em um cluster.

Verifique se a implantação foi bem-sucedida e o aplicativo está em execução no cluster.  Abra um navegador da Web e navegue até [http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/).  Observe a versão do aplicativo, neste exemplo é "1.0.0.20170815.3".

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
> * Criar a definição de build
> * Criar uma definição de versão
> * Implantar e atualizar automaticamente um aplicativo

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Configurar monitoramento e diagnóstico para o aplicativo](service-fabric-tutorial-monitoring-aspnet.md) 


<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[add-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddTask.png
[new-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewTask.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
[run-on-agent]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/RunOnAgent.png