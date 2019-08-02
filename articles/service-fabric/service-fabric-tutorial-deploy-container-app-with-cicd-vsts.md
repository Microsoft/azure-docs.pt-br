---
title: Implantar um aplicativo de contêiner com CI/CD a um cluster do Azure Service Fabric
description: Neste tutorial, você aprenderá a configurar a integração e a implantação contínuas para um aplicativo de contêiner do Azure Service Fabric usando o Azure DevOps do Visual Studio.
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
ms.date: 08/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: b686ceace3679d1541e8f1a74bca7e99b81ba932
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598901"
---
# <a name="tutorial-deploy-a-container-application-with-cicd-to-a-service-fabric-cluster"></a>Tutorial: Implantar um aplicativo de contêiner com CI/CD em um cluster do Service Fabric

Este tutorial é a segunda parte de uma série e descreve como configurar a integração e a implantação contínua de um aplicativo de contêiner do Azure Service Fabric usando o Visual Studio e o Azure DevOps.  É necessário ter um aplicativo do Service Fabric e o aplicativo criado em [Implantar um aplicativo .NET em um contêiner do Windows no Azure Service Fabric](service-fabric-host-app-in-a-container.md) é usado como exemplo.

Na segunda parte da série, você aprenderá como:

> [!div class="checklist"]
> * Adicionar controle do código-fonte ao seu projeto
> * Criar uma definição de build no Team Explorer do Visual Studio
> * Criar uma definição da versão no Team Explorer do Visual Studio
> * Implantar e atualizar automaticamente um aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Ter um cluster no Azure ou [criar um com este tutorial](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Implantar um aplicativo em contêineres nele](service-fabric-host-app-in-a-container.md)

## <a name="prepare-a-publish-profile"></a>Preparar um perfil de publicação

Agora que você já [implantou um aplicativo de contêiner](service-fabric-host-app-in-a-container.md), está pronto para configurar a integração contínua.  Primeiro, prepare um perfil de publicação em seu aplicativo para ser usado pelo processo de implantação que é executado no Azure DevOps.  O perfil de publicação deve ser configurado para direcionar-se ao cluster que você criou anteriormente.  Inicie o Visual Studio e abra um projeto de aplicativo do Service Fabric existente.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no aplicativo e selecione **Publicar...** .

Escolha um perfil de destino em seu projeto de aplicativo a fim de usar para o fluxo de trabalho de integração contínua, por exemplo, Nuvem.  Especifique o ponto de extremidade de conexão do cluster.  Marque a caixa de seleção **Atualizar o Aplicativo** para que seu aplicativo seja atualizado a cada implantação no Azure DevOps.  Clique no hiperlink **Salvar** para salvar as configurações em seu perfil de publicação e, em seguida, clique em **Cancelar** para fechar a caixa de diálogo.

![Perfil de envio por push][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Compartilhar sua solução do Visual Studio em um novo repositório Git do Azure DevOps

Compartilhe os arquivos de origem do aplicativo em um projeto de equipe no Azure DevOps para que você possa gerar builds.

Crie um novo repositório Git local para seu projeto selecionando **Adicionar ao controle do código-fonte** -> **Git** na barra de status no canto inferior direito do Visual Studio.

Na exibição **Push** no **Team Explorer**, selecione o botão **Publicar Repositório Git** em **Efetuar Push para o Azure DevOps**.

![Enviar por push o repositório Git][push-git-repo]

Verifique seu email e selecione sua conta na lista suspensa **Conta**. Talvez seja preciso configurar uma organização, caso ainda não tenha uma. Digite o nome do seu repositório e selecione **Publicar Repositório**.

![Enviar por push o repositório Git][publish-code]

A publicação do repositório cria um novo projeto de equipe em sua conta com o mesmo nome do repositório local. Para criar o repositório em um projeto de equipe existente, clique em **Avançado** ao lado do nome do **Repositório** e escolha um projeto de equipe. Você pode exibir seu código na Web selecionando **Vê-lo na Web**.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Configurar a entrega contínua com o Azure Pipelines

Uma definição de build do Azure DevOps descreve um fluxo de trabalho composto por um conjunto de etapas de compilação que são executadas sequencialmente. Crie uma definição de build que produza um pacote de aplicativos do Service Fabric e outros artefatos, para implantar em um cluster do Service Fabric. Saiba mais sobre [definição de build](https://www.visualstudio.com/docs/build/define/create) do Azure DevOps. 

Uma definição de versão do Azure DevOps descreve um fluxo de trabalho que implanta um pacote de aplicativos em um cluster. Quando usadas juntas, a definição de build e a definição de versão executam todo o fluxo de trabalho, começando com os arquivos de origem e terminando com um aplicativo em execução em seu cluster. Saiba mais sobre a [definição da versão](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition) do Azure DevOps.

### <a name="create-a-build-definition"></a>Criar a definição de build

Abra seu novo projeto de equipe navegando até https://dev.azure.com em um navegador da Web e selecionando sua organização, seguida pelo novo projeto. 

Selecione a opção **Pipelines** no painel à esquerda, depois clique em **Novo pipeline**.

>[!NOTE]
>Se você não vir o modelo de definição de compilação, verifique se o recurso **Nova experiência de criação de pipeline do YAML** está desativado. Este recurso é configurado dentro da seção **Versão prévia dos recursos** da sua conta do DevOps.

![Novo Pipeline][new-pipeline]

Selecione **Git do Azure Repos** como fonte, o nome do projeto da sua equipe, seu repositório de projeto e o branch Padrão **master** ou builds manuais e agendados.  Clique em **Continuar**.

Em **Selecionar um modelo**, selecione o modelo **Aplicativo do Azure Service Fabric com suporte para Docker** e clique em **Aplicar**.

![Escolher o modelo de build][select-build-template]

Em **Tarefas**, selecione **VS2017 Hospedado** como o **Pool do agente**.

![Selecionar tarefas][task-agent-pool]

Clique em **Marcar imagens**.

No **Tipo de Registro de Contêiner**, selecione **Registro de Contêiner do Azure**. Selecione uma **Assinatura do Azure**, em seguida, clique em **Autorizar**. Selecione um **Registro de Contêiner do Azure**.

![Selecione as imagens do Docker Tag][select-tag-images]

Clique em **Enviar imagens por push**.

No **Tipo de Registro de Contêiner**, selecione **Registro de Contêiner do Azure**. Selecione uma **Assinatura do Azure**, em seguida, clique em **Autorizar**. Selecione um **Registro de Contêiner do Azure**.

![Selecione Enviar imagens por push do Docker][select-push-images]

Na guia **Gatilhos**, habilite a integração contínua marcando **Habilitar a integração contínua**. Dentro de **Filtros de ramificação**, clique em **+ Adicionar** e **Especificação de branch** assumirá como padrão **mestre**.

Na **caixa de diálogo Salvar pipeline de build e enfileirar**, clique em **Salvar e enfileirar** para iniciar manualmente um build.

![Selecionar gatilhos][save-and-queue]

A compilação também é disparada durante o push ou o check-in. Para verificar o progresso da compilação, alterne para a guia **Compilações**.  Assim que verificar que o build é executado com êxito, configure uma definição de versão que implante seu aplicativo em um cluster.

### <a name="create-a-release-definition"></a>Criar uma definição de versão

Selecione a opção **Pipelines** no painel à esquerda, depois **Versões** e **+Novo pipeline**.  Em **Selecionar um modelo**, selecione o modelo **Implantação do Azure Service Fabric** na lista e **Aplicar**.

![Escolher modelo de versão][select-release-template]

Selecione **Tarefas**, depois **Ambiente 1** e **+ Novo** para adicionar uma nova conexão do cluster.

![Adicionar conexão do cluster][add-cluster-connection]

No modo de exibição **Adicionar nova Conexão do Service Fabric**, selecione a autenticação **Baseada em Certificado** ou **Azure Active Directory**.  Especifique o nome de conexão de "mysftestcluster" e um ponto de extremidade do cluster "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" (ou o ponto de extremidade do cluster em que você está implantando).

Para autenticação baseada em certificado, adicione a **Impressão digital do certificado de servidor** do certificado do servidor usado para criar o cluster.  Em **Certificado do cliente**, adicione a codificação de base 64 do arquivo de certificado do cliente. Veja no pop-up de ajuda do campo informações sobre como obter essa representação de codificação de base 64 do certificado. Essa também é a **Senha** para o arquivo do certificado.  Se você não tiver um certificado do cliente ou do servidor separado, poderá usar o certificado de cluster ou do servidor.

Para as credenciais do Azure Active Directory, adicione a **Impressão digital do certificado de servidor** do certificado do servidor usado para criar o cluster e as credenciais que deseja usar para se conectar ao cluster nos campos **Nome de Usuário** e **Senha**.

Clique em **Adicionar** para salvar a conexão do cluster.



Em Fase de Agente, clique em **Implantar o Aplicativo do Service Fabric**.
Clique em **Configurações do Docker** e, em seguida, clique em **Definir configurações do Docker**. Em **Fonte de Credenciais de Registro**, selecione **Conexão de Serviço do Azure Resource Manager**. Selecione sua **assinatura do Azure**.

![Agente do pipeline de lançamento][release-pipeline-agent]

Em seguida, adicione um artefato de compilação ao pipeline para que a definição da versão possa encontrar a saída da compilação. Selecione **Pipeline** e **Artefatos**-> **+Adicionar**.  Em **Fonte (Definição de compilação)** , selecione a definição de compilação criada anteriormente.  Clique em **Adicionar** para salvar o artefato de compilação.

![Adicionar artefato][add-artifact]

Habilite um gatilho de implantação contínua para que uma versão seja criada automaticamente quando a compilação for concluída. Clique no ícone de raio no artefato, habilite o gatilho e clique em **Salvar** para salvar a definição de versão.

![Habilitar gatilho][enable-trigger]

Selecione **+ Versão** -> **Criar uma Versão** -> **Criar** para criar manualmente uma versão. Você pode monitorar o andamento da versão na guia **Versões**.

Verifique se a implantação foi bem-sucedida e o aplicativo está em execução no cluster.  Abra um navegador da Web e navegue até [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Observe a versão do aplicativo, neste exemplo é "1.0.0.20170616.3".

## <a name="commit-and-push-changes-trigger-a-release"></a>Confirmar e enviar alterações por push, disparar uma versão

Para verificar se o pipeline de integração contínua está funcionando conferindo algumas alterações de código no Azure DevOps.

Ao escrever seu código, suas alterações são rastreadas automaticamente pelo Visual Studio. Confirme as alterações em seu repositório Git local, selecionando o ícone de alterações pendentes (![Pendente][pending]) na barra de status na parte inferior direita.

Na exibição **Alterações** no Team Explorer, adicione uma mensagem que descreva a atualização e confirme suas alterações.

![Confirmar tudo][changes]

Selecione o ícone de barra de status de alterações não publicadas (![Alterações não publicadas][unpublished-changes]) ou a exibição de sincronização no Team Explorer. Selecione **Push** para atualizar seu código no Azure DevOps.

![Enviar alterações por push][push]

O push das alterações para o Azure DevOps dispara um build automaticamente.  Quando a definição de build é concluída com êxito, uma versão é criada automaticamente e inicia a atualização do aplicativo no cluster.

Para verificar o progresso do build, alterne para a guia **Builds** no **Team Explorer** no Visual Studio.  Assim que verificar que o build é executado com êxito, configure uma definição de versão que implante seu aplicativo em um cluster.

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
> * Criar a definição de build
> * Criar uma definição de versão
> * Implantar e atualizar automaticamente um aplicativo

Na próxima parte do tutorial, saiba como configurar o [monitoramento do seu contêiner](service-fabric-tutorial-monitoring-wincontainers.md).

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewPipeline.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectBuildTemplate.png
[task-agent-pool]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/TaskAgentPool.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SaveAndQueue.png
[select-tag-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerTagImages.png
[select-push-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerPushImages.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddClusterConnection.png
[release-pipeline-agent]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/ReleasePipelineAgent.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpointDialog.png
