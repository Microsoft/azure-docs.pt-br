---
title: configurar uma implantação contínua
description: Saiba como habilitar o CI/CD para Azure App serviço do GitHub, BitBucket, Azure Repos ou outros repositórios. Selecione o pipeline de compilação que atenda às suas necessidades.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/03/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 5af8294518759181326e7736ef755f0a83581014
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564945"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Implantação contínua no Serviço de Aplicativo do Azure

[Azure app serviço](overview.md) permite a implantação contínua de repositórios [GitHub](https://help.github.com/articles/create-a-repo), [BitBucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)e [Azure Repos](/azure/devops/repos/git/creatingrepo) ao extrair as atualizações mais recentes.

> [!NOTE]
> A página do **centro de desenvolvimento (clássico)** na portal do Azure, que é a antiga experiência de implantação, será preterida em março de 2021. Essa alteração não afetará nenhuma configuração de implantação existente em seu aplicativo e você poderá continuar a gerenciar a implantação do aplicativo na página do **centro de implantação** .

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>Configurar a origem da implantação

1. Na [portal do Azure](https://portal.azure.com), navegue até a página de gerenciamento do aplicativo do serviço de aplicativo.

1. No menu à esquerda, clique em configurações da **central de implantação**  >  . 

1. Em **origem**, selecione uma das opções de CI/CD.

    ![Mostra como escolher a origem da implantação na central de implantação para o serviço Azure App](media/app-service-continuous-deployment/choose-source.png)

Escolha a guia que corresponde à sua seleção para as etapas.

# <a name="github"></a>[GitHub](#tab/github)

4. As [ações do GitHub](#how-the-github-actions-build-provider-works) são o provedor de compilação padrão. Para alterá-lo, clique em **alterar**  >  **serviço de compilação do serviço de aplicativo** do provedor (kudu) > **OK**.

    > [!NOTE]
    > Para usar Azure Pipelines como o provedor de compilação para seu aplicativo do serviço de aplicativo, não o configure no serviço de aplicativo. Em vez disso, configure o CI/CD diretamente do Azure Pipelines. A opção **Azure pipelines** apenas aponta para a direção certa.

1. Se você estiver implantando do GitHub pela primeira vez, clique em **autorizar** e siga os prompts de autorização. Se você quiser implantar do repositório de um usuário diferente, clique em **alterar conta**.

1. Depois de autorizar sua conta do Azure com o GitHub, selecione a **organização**, o **repositório** e a **ramificação** para configurar o CI/CD para o.

1. Quando as ações do GitHub são o provedor de compilação escolhido, você pode selecionar o arquivo de fluxo de trabalho que deseja com a **pilha de tempo de execução** e os menus suspensos de **versão** . O Azure confirma esse arquivo de fluxo de trabalho em seu repositório GitHub selecionado para lidar com tarefas de compilação e implantação. Para ver o arquivo antes de salvar as alterações, clique em **Visualizar arquivo**.

    > [!NOTE]
    > O serviço de aplicativo detecta a [configuração de pilha de idiomas](configure-common.md#configure-language-stack-settings) do seu aplicativo e seleciona o modelo de fluxo de trabalho mais apropriado. Se você escolher um modelo diferente, ele poderá implantar um aplicativo que não seja executado corretamente. Para obter mais informações, consulte [como funciona o provedor de compilação de ações do GitHub](#how-the-github-actions-build-provider-works).

1. Clique em **Save** (Salvar).
   
    Novas confirmações no repositório e no branch selecionados agora são implantadas continuamente em seu aplicativo do Serviço de Aplicativo. Você pode acompanhar as confirmações e implantações na guia **logs** .

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

A integração do BitBucket usa os serviços de compilação do serviço de aplicativo (kudu) para automação de compilação.

4. Se você estiver implantando do BitBucket pela primeira vez, clique em **autorizar** e siga os prompts de autorização. Se você quiser implantar do repositório de um usuário diferente, clique em **alterar conta**.

1. Para bitbucket, selecione a **equipe** do bitbucket, o **repositório** e a **ramificação** que você deseja implantar continuamente.

1. Clique em **Save** (Salvar).
   
    Novas confirmações no repositório e no branch selecionados agora são implantadas continuamente em seu aplicativo do Serviço de Aplicativo. Você pode acompanhar as confirmações e implantações na guia **logs** .
   
# <a name="local-git"></a>[Git Local](#tab/local)

Consulte [implantação do git local para Azure app serviço](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)

> [!NOTE]
> Azure Repos como uma fonte de implantação é o suporte para aplicativos do Windows.
>

4. O serviço de compilação do serviço de aplicativo (kudu) é o provedor de compilação padrão.

    > [!NOTE]
    > Para usar Azure Pipelines como o provedor de compilação para seu aplicativo do serviço de aplicativo, não o configure no serviço de aplicativo. Em vez disso, configure o CI/CD diretamente do Azure Pipelines. A opção **Azure pipelines** apenas aponta para a direção certa.

1. Selecione a organização, o **projeto**, o **repositório** e a **ramificação** do **Azure DevOps** que você deseja implantar continuamente. 

    Se sua organização DevOps não estiver listada, ela ainda não está vinculada à sua assinatura do Azure. Para obter mais informações, consulte [criar uma conexão de serviço do Azure](/azure/devops/pipelines/library/connect-to-azure).

-----

## <a name="disable-continuous-deployment"></a>Desabilitar a implantação contínua

1. Na [portal do Azure](https://portal.azure.com), navegue até a página de gerenciamento do aplicativo do serviço de aplicativo.

1. No menu à esquerda, clique em configurações da **central de implantação**  >    >  **Desconectar**. 

    ![Mostra como desconectar a sincronização da pasta de nuvem com seu aplicativo do serviço de aplicativo no portal do Azure.](media/app-service-continuous-deployment/disable.png)

1. Por padrão, o arquivo de fluxo de trabalho de ações do GitHub é preservado em seu repositório, mas continuará a disparar a implantação em seu aplicativo. Para excluí-lo do repositório, selecione **excluir arquivo de fluxo de trabalho**.

1. Clique em **OK**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Como funciona o provedor de compilação de ações do GitHub

O provedor de compilação de ações do GitHub é uma opção para [CI/CD do GitHub](#configure-deployment-source)e faz o seguinte para configurar CI/CD:

- Deposita um arquivo de fluxo de trabalho de ações do GitHub em seu repositório GitHub para lidar com tarefas de compilação e implantação no serviço de aplicativo.
- Adiciona o perfil de publicação para seu aplicativo como um segredo do GitHub. O arquivo de fluxo de trabalho usa esse segredo para autenticar com o serviço de aplicativo.
- Captura informações do fluxo de [trabalho executar logs](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) e exibe-as na guia **logs** no **centro de implantação** do seu aplicativo.

Você pode personalizar o provedor de compilação de ações do GitHub das seguintes maneiras:

- Personalize o arquivo de fluxo de trabalho depois que ele for gerado em seu repositório GitHub. Para obter mais informações, consulte [sintaxe de fluxo de trabalho para ações do GitHub](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Apenas certifique-se de que o fluxo de trabalho seja implantado no serviço de aplicativo com a ação de [implantação do Azure/webapps](https://github.com/Azure/webapps-deploy) .
- Se a ramificação selecionada estiver protegida, você ainda poderá visualizar o arquivo de fluxo de trabalho sem salvar a configuração e adicioná-la manualmente ao repositório. Esse método não lhe dá a integração de log com o portal do Azure.
- Em vez de um perfil de publicação, implante usando uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) no Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Autenticar com uma entidade de serviço

1. Gere uma entidade de serviço com o comando [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) na [CLI do Azure](/cli/azure/). No exemplo a seguir, substitua *\<subscription-id>* , *\<group-name>* e *\<app-name>* pelos seus próprios valores:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > Para segurança, conceda o acesso mínimo necessário à entidade de serviço. O escopo no exemplo anterior é limitado ao aplicativo do serviço de aplicativo específico e não ao grupo de recursos inteiro.
    
1. Salve toda a saída JSON para a próxima etapa, incluindo o nível superior `{}` .

1. No [GitHub](https://github.com/), procure seu repositório, selecione **configurações > segredos > adicionar um novo segredo**.

1. Cole toda a saída JSON do comando da CLI do Azure no campo valor do segredo. Nomeie o segredo como `AZURE_CREDENTIALS` .

1. No arquivo de fluxo de trabalho gerado pela **central de implantação**, revise a `azure/webapps-deploy` etapa com um código como o exemplo a seguir (que é modificado de um arquivo de fluxo de trabalho Node.js):

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>Implantar de outros repositórios

Para aplicativos do Windows, você pode configurar manualmente a implantação contínua de um repositório git ou do Mercurial de nuvem ao qual o portal não dá suporte diretamente, como [GitLab](https://gitlab.com/). Você faz isso escolhendo git externo na lista suspensa **origem** . Para obter mais informações, consulte [Configurar a implantação contínua usando etapas manuais](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="more-resources"></a>Mais recursos

* [Implantar de Azure Pipelines para serviços Azure App](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Investigue os problemas comuns com a implantação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Usar PowerShell do Azure](/powershell/azure/)
* [Kudu do Projeto](https://github.com/projectkudu/kudu/wiki)
