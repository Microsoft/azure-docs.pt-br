---
title: configurar uma implantação contínua
description: Saiba como habilitar o CI/CD para Azure App serviço do GitHub, BitBucket, Azure Repos ou outros repositórios. Selecione o pipeline de compilação que atenda às suas necessidades.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 799699662b738804790e3fe18ce9bd579027808d
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616308"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Implantação contínua no Serviço de Aplicativo do Azure

[Azure app serviço](overview.md) permite a implantação contínua de repositórios GitHub, BitBucket e [Azure Repos](https://azure.microsoft.com/services/devops/repos/) ao extrair as atualizações mais recentes. Este artigo mostra como usar o portal do Azure para implantar continuamente seu aplicativo por meio do serviço de compilação kudu ou [Azure pipelines](https://azure.microsoft.com/services/devops/pipelines/). 

Para obter mais informações sobre os serviços de controle do código-fonte, consulte [criar um repositório (github)], [criar um repositório (BitBucket)]ou [criar um novo repositório git (Azure Repos)].

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autorizar serviço Azure App 

Para usar Azure Repos, verifique se sua organização do Azure DevOps está vinculada à sua assinatura do Azure. Para obter mais informações, consulte [Configurar uma conta de Azure DevOps Services para que ela possa ser implantada em um aplicativo Web](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true).

Para bitbucket ou GitHub, autorize o serviço de Azure App a se conectar ao repositório. Você só precisa autorizar com um serviço de controle do código-fonte uma vez. 

1. Na [portal do Azure](https://portal.azure.com), procure serviços de  **aplicativos** e selecione.

   ![Pesquise serviços de aplicativos.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Selecione o serviço de aplicativo que você deseja implantar.

   ![Selecione seu aplicativo.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Na página do aplicativo, selecione **central de implantação** no menu à esquerda.
   
1. Na página **centro de implantação** , selecione **GitHub** ou **bitbucket** e, em seguida, selecione **autorizar**. 
   
   ![Selecione serviço de controle do código-fonte, em seguida, selecione autorizar.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Entre no serviço, se necessário, e siga os prompts de autorização. 

## <a name="enable-continuous-deployment"></a>Habilitar a implantação contínua 

Depois de autorizar um serviço de controle do código-fonte, configure seu aplicativo para implantação contínua por meio do servidor de Build do [serviço de aplicativo kudu](#option-1-kudu-app-service) interno ou por meio de [Azure pipelines](#option-2-azure-pipelines). 

### <a name="option-1-kudu-app-service"></a>Opção 1: serviço de aplicativo do kudu

Você pode usar o servidor de Build do serviço de aplicativo kudu interno para implantar continuamente do GitHub, do bitbucket ou do Azure Repos. 

1. Na [portal do Azure](https://portal.azure.com), procure serviços de **aplicativos** e, em seguida, selecione o serviço de aplicativo que você deseja implantar. 
   
1. Na página do aplicativo, selecione **central de implantação** no menu à esquerda.
   
1. Selecione o provedor de controle do código-fonte autorizado na página **centro de implantação** e selecione **continuar**. Para o GitHub ou o bitbucket, você também pode selecionar **alterar conta** para alterar a conta autorizada. 
   
   > [!NOTE]
   > Para usar Azure Repos, verifique se sua organização de Azure DevOps Services está vinculada à sua assinatura do Azure. Para obter mais informações, consulte [Configurar uma conta de Azure DevOps Services para que ela possa ser implantada em um aplicativo Web](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true).
   
1. Para o GitHub ou o Azure Repos, na página do **provedor de compilação** , selecione serviço de compilação do **serviço de aplicativo** e, em seguida, selecione **continuar**. O bitbucket sempre usa o serviço de compilação do serviço de aplicativo.
   
   ![Selecione serviço de compilação do serviço de aplicativo e selecione continuar.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Na página **Configurar** :
   
   - Para o GitHub, clique na lista suspensa e selecione a **organização**, o **repositório** e a **ramificação** que você deseja implantar continuamente.
     
     > [!NOTE]
     > Se você não vir nenhum repositório, talvez seja necessário autorizar o serviço Azure App no GitHub. Navegue até o repositório do GitHub e vá para **configurações**  >  **aplicativo aplicativos**  >  **OAuth autorizados**. Selecione **Azure app serviço** e, em seguida, selecione **conceder**. Em repositórios corporativos, você precisa ser um proprietário da organização para conceder as permissões.
     
   - Para bitbucket, selecione a **equipe** do bitbucket, o **repositório** e a **ramificação** que você deseja implantar continuamente.
     
   - Para Azure Repos, selecione a **organização do Azure DevOps**, o **projeto**, o **repositório** e a **ramificação** que você deseja implantar continuamente.
     
     > [!NOTE]
     > Se sua organização do Azure DevOps não estiver listada, verifique se ele está vinculada à sua assinatura do Azure. Para obter mais informações, consulte [Configurar uma conta de Azure DevOps Services para que ela possa ser implantada em um aplicativo Web](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true).
     
1. Selecione **Continuar**.
   
   ![Preencha as informações do repositório e selecione continuar.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Depois de configurar o provedor de compilação, examine as configurações na página **Resumo** e selecione **concluir**.
   
1. Novas confirmações no repositório e no branch selecionados agora são implantadas continuamente em seu aplicativo do Serviço de Aplicativo. Você pode acompanhar as confirmações e implantações na página do **Centro de Implantação**.
   
   ![Acompanhar confirmações e implantações na central de implantação](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>Opção 2: Azure Pipelines 

Se sua conta tiver as permissões necessárias, você poderá configurar Azure Pipelines para implantar continuamente do GitHub ou Azure Repos. Para obter mais informações sobre a implantação por meio do Azure Pipelines, consulte [implantar um aplicativo Web em serviços Azure apps](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

#### <a name="prerequisites"></a>Pré-requisitos

Para Azure App serviço para criar a entrega contínua usando Azure Pipelines, sua organização do Azure DevOps deve ter as seguintes permissões: 

- Sua conta do Azure deve ter permissões para gravar em Azure Active Directory e criar um registro de aplicativo. 
  
- Sua conta do Azure deve ter a função de **proprietário** em sua assinatura do Azure.

- Você deve ser um administrador no projeto DevOps do Azure que deseja usar.

#### <a name="github--azure-pipelines"></a>GitHub + Azure Pipelines

1. Na [portal do Azure](https://portal.azure.com), procure serviços de **aplicativos** e, em seguida, selecione o serviço de aplicativo que você deseja implantar. 
   
1. Na página do aplicativo, selecione **central de implantação** no menu à esquerda.

1. Selecione **GitHub** como o provedor de controle do código-fonte na página **centro de implantação** e selecione **continuar**. Para o **GitHub**, você pode selecionar **alterar conta** para alterar a conta autorizada.

    :::image type="content" source="media/app-service-continuous-deployment/deployment-center-src-control.png" alt-text="Captura de tela da página do centro de implantação do serviço de aplicativo.":::
   
1. Na página **provedor de compilação** , selecione **Azure pipelines (versão prévia)** e, em seguida, selecione **continuar**.

    :::image type="content" source="media/app-service-continuous-deployment/select-build-provider.png" alt-text="Captura de tela mostrando a página da central de implantação com Azure Pipelines (versão prévia) selecionada.":::
   
1. Na página **Configurar** , na seção **código** , selecione a **organização**, o **repositório** e a **ramificação** que você deseja implantar continuamente e selecione **continuar**.
     
     > [!NOTE]
     > Se você não vir nenhum repositório, talvez seja necessário autorizar o serviço Azure App no GitHub. Navegue até o repositório do GitHub e vá para **configurações**  >  **aplicativo aplicativos**  >  **OAuth autorizados**. Selecione **Azure app serviço** e, em seguida, selecione **conceder**. Em repositórios corporativos, você precisa ser um proprietário da organização para conceder as permissões.
       
    Na seção **Build** , especifique a organização de DevOps do Azure, o projeto, a estrutura de linguagem que Azure pipelines deve usar para executar tarefas de compilação e, em seguida, selecione **continuar**.

   :::image type="content" source="media/app-service-continuous-deployment/build-configure.png" alt-text="Captura de tela da seção de compilação com texto de exemplo nos campos.":::

1. Depois de configurar o provedor de compilação, examine as configurações na página **Resumo** e selecione **concluir**.

   :::image type="content" source="media/app-service-continuous-deployment/summary.png" alt-text="Captura de tela da página da central de implantação mostrando as confirmações e implantações com o botão atualizar realçado.":::
   
1. Novas confirmações no repositório e Branch selecionados agora são implantadas continuamente em seu serviço de aplicativo. Você pode acompanhar as confirmações e implantações na página do **Centro de Implantação**.
   
   ![Acompanhar confirmações e implantações na central de implantação](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure Repos + Azure Pipelines

1. Na [portal do Azure](https://portal.azure.com), procure serviços de **aplicativos** e, em seguida, selecione o serviço de aplicativo que você deseja implantar. 
   
1. Na página do aplicativo, selecione **central de implantação** no menu à esquerda.

1. Selecione **Azure Repos** como o provedor de controle do código-fonte na página **centro de implantação** e selecione **continuar**.

    :::image type="content" source="media/app-service-continuous-deployment/deployment-center-src-control.png" alt-text="Captura de tela da página da central de implantação mostrando as seleções de CI/CD (implantação contínua).":::

1. Na página **provedor de compilação** , selecione **Azure pipelines (versão prévia)** e, em seguida, selecione **continuar**.

    :::image type="content" source="media/app-service-continuous-deployment/azure-pipelines.png" alt-text="Captura de tela da central de implantação mostrando Azure Pipelines (versão prévia).":::

1. Na página **Configurar** , na seção **código** , selecione a **organização**, o **repositório** e a **ramificação** que você deseja implantar continuamente e selecione **continuar**.

   > [!NOTE]
   > Se sua organização do Azure DevOps existente não estiver listada, talvez seja necessário vinculá-la à sua assinatura do Azure. Para obter mais informações, consulte [definir o pipeline de liberação de CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).

   Na seção **Build** , especifique a organização de DevOps do Azure, o projeto, a estrutura de linguagem que Azure pipelines deve usar para executar tarefas de compilação e, em seguida, selecione **continuar**.

   :::image type="content" source="media/app-service-continuous-deployment/build-configure.png" alt-text="Captura de tela da seção Build mostrando a organização DevOps do Azure e os campos de projeto preenchidos com exemplos.":::

1. Depois de configurar o provedor de compilação, examine as configurações na página **Resumo** e selecione **concluir**.  
     
   :::image type="content" source="media/app-service-continuous-deployment/summary-azure-pipelines.png" alt-text="Captura de tela mostrando as configurações selecionadas na página Resumo.":::

1. Novas confirmações no repositório e Branch selecionados agora são implantadas continuamente em seu serviço de aplicativo. Você pode acompanhar as confirmações e implantações na página do **Centro de Implantação**.

## <a name="disable-continuous-deployment"></a>Desabilitar a implantação contínua

Para desabilitar a implantação contínua, selecione **Desconectar** na parte superior da página do **centro de implantação** do seu aplicativo.

![Desabilitar a implantação contínua](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Usar repositórios sem suporte

Para aplicativos do Windows, você pode configurar manualmente a implantação contínua de um repositório git ou do Mercurial de nuvem ao qual o portal não dá suporte diretamente, como [GitLab](https://gitlab.com/). Você faz isso escolhendo a caixa externa na página **centro de implantação** . Para obter mais informações, consulte [Configurar a implantação contínua usando etapas manuais](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Recursos adicionais

* [Investigue os problemas comuns com a implantação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Usar PowerShell do Azure](/powershell/azure/)
* [Documentação do Git](https://git-scm.com/documentation)
* [Kudu do Projeto](https://github.com/projectkudu/kudu/wiki)

[Criar um repositório (GitHub)]: https://help.github.com/articles/create-a-repo
[Criar um repositório (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Criar um novo repositório git (Azure Repos)]: /azure/devops/repos/git/creatingrepo