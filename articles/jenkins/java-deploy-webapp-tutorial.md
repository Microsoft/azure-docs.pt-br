---
title: Usar o Jenkins para implantar seus aplicativos Web no Azure | Microsoft Docs
description: "Configure a integração contínua do GitHub ao Serviço de Aplicativo do Azure em seus aplicativos Web Java usando o Jenkins e o Docker."
author: rloutlaw
manager: douge
ms.service: jenkins
ms.search.scope: 
ms.devlang: java
ms.topic: article
ms.workload: web
ms.date: 08/02/2017
ms.author: routlaw
ms.custom: Jenkins, devcenter
ms.openlocfilehash: 3a2635ac968d843226f05dc51cf4a5f078235c11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Configurar a integração e implantação contínuas ao Serviço de Aplicativo do Azure com o Jenkins

Este tutorial configura a CI/CD (integração e implantação contínuas) de um aplicativo Web Java de exemplo desenvolvido com a estrutura [Spring Boot](http://projects.spring.io/spring-boot/) ao [Aplicativo Web do Serviço de Aplicativo do Azure no Linux](/azure/app-service/containers/app-service-linux-intro) usando o Jenkins.

Você realizará as seguintes tarefas neste tutorial:

> [!div class="checklist"]
> * Instalar os plug-ins do Jenkins necessários para implantar no Serviço de Aplicativo do Azure.
> * Definir um trabalho do Jenkins para criar imagens do Docker com base em um repositório GitHub quando uma nova confirmação é enviada por push.
> * Definir um novo Aplicativo Web do Azure para o Linux e configurá-lo para implantar as imagens do Docker enviadas por push para o registro de Contêiner do Azure.
> * Configurar o plug-in do Jenkins do Serviço de Aplicativo do Azure.
> * Implantar o aplicativo de exemplo no Serviço de Aplicativo do Azure com um build manual.
> * Disparar um build do Jenkins e atualizar o aplicativo Web enviando alterações por push ao GitHub.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este tutorial, você precisará:

* [Jenkins](https://jenkins.io/) com as ferramentas JDK e Maven configuradas. Se você não tiver um sistema Jenkins, crie um agora no Azure com base no [modelo de solução Jenkins](/azure/jenkins/install-jenkins-solution-template).
* Uma conta do [GitHub](https://github.com).
* A [CLI do Azure 2.0](/cli/azure/overview), na linha de comando local ou no [Azure Cloud Shell](/azure/cloud-shell/overview)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>Instalar os plug-ins do Jenkins

1. Abra um navegador da Web no console Web do Jenkins, selecione **Gerenciar Jenkins** no menu à esquerda e, em seguida, selecione **Gerenciar Plug-ins**.
2. Selecione a guia **Disponível**.
3. Pesquise e selecione a caixa de seleção ao lado dos seguintes plug-ins:   

    - [Plug-in do Serviço de Aplicativo do Azure](https://plugins.jenkins.io/azure-app-service)
    - [Plug-in da Origem de Branch do GitHub](https://plugins.jenkins.io/github-branch-source)

    Se os plug-ins não forem exibidos, verifique se eles já não estão instalados verificando a guia **Instalados**.

1. Selecione **Baixar agora e instalar após a reinicialização** para habilitar os plug-ins na configuração do Jenkins.

## <a name="configure-github-and-jenkins"></a>Configurar o GitHub e o Jenkins

Configure o Jenkins para receber [webhooks do GitHub](https://developer.github.com/webhooks/) quando novas confirmações forem enviadas por push para um repositório em sua conta.

1. Selecione **Gerenciar Jenkins** e, em seguida, **Configurar Sistema**. Na seção **GitHub**, verifique se a opção **Gerenciar ganchos** está marcada e, em seguida, selecione **Gerenciar ações adicionais do GitHub** e escolha **Converter logon e senha em token**.
2. Selecione o botão de opção **Com base em logon e senha** e insira seu nome de usuário e a senha do GitHub. Selecione **Criar credenciais de token** para criar um novo [Token de Acesso Pessoal do GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   
   ![Criar PAT do GitHub com base em logon e senha](media/jenkins-java-quickstart/create_github_credentials.png)
3.  Selecione o token recém-criado na lista suspensa **Credenciais** na configuração de Servidores do GitHub. Selecione **Testar conectividade** para verificar se a autenticação está funcionando.   
   ![Verificar a conexão com o GitHub após a configuração do PAT](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> Caso sua conta do GitHub tenha a autenticação de dois fatores habilitada, crie o token no GitHub e configure o Jenkins para usá-lo. Examine a documentação [Plug-in do Jenkins no GitHub](https://wiki.jenkins.io/display/JENKINS/Github+Plugin) para obter detalhes completos.

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>Criar fork do repositório de exemplo e criar um trabalho do Jenkins 

1. Abra o [repositório de aplicativos de exemplo do Spring Boot](https://github.com/spring-guides/gs-spring-boot-docker) e crie um fork dele em sua própria conta do GitHub selecionando **Criar Fork** no canto superior direito.   
    ![Fork do GitHub](media/jenkins-java-quickstart/fork_github_repo.png)
1. No console Web do Jenkins, selecione **Novo Item**, dê a ele o nome **MyJavaApp**, selecione **Projeto Freestyle** e, em seguida, selecione **OK**.   
    ![Novo projeto Freestyle do Jenkins](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. Na seção **Geral**, selecione o projeto do **GitHub** e insira a URL do repositório bifurcado, como https://github.com/raisa/gs-spring-boot-docker
3. Na seção **Gerenciamento de código-fonte**, selecione **Git**, insira a URL `.git` do repositório bifurcado, como https://github.com/raisa/gs-spring-boot-docker.git
4. Na seção **Gatilhos de Build**, selecione **Gatilho de gancho do GitHub para sondagem de GITscm**.
5. Na seção **Compilar**, selecione **Adicionar etapa de build** e escolha **Invocar destinos de nível superior do Maven**. Insira `package` no campo **Metas**.
6. Selecione **Salvar**. Teste o trabalho selecionando **Compilar Agora** na página do projeto.

## <a name="configure-azure-app-service"></a>Configurar o Serviço de Aplicativo do Azure 

1. Usando a CLI do Azure ou o [Cloud Shell](/azure/cloud-shell/overview), crie um novo [Aplicativo Web no Linux](/azure/app-service/containers/app-service-linux-intro). O nome do aplicativo Web neste tutorial é `myJavaApp`, mas você precisa usar um nome exclusivo para seu próprio aplicativo.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan
    ```

2. Crie um [Registro de Contêiner do Azure](/azure/container-registry/container-registry-intro) para armazenar as imagens do Docker criadas pelo Jenkins. O nome do registro de contêiner usado neste tutorial é `jenkinsregistry`, mas você precisa usar um nome exclusivo para seu próprio registro de contêiner. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. Configure o aplicativo Web para executar as imagens do Docker enviadas por push para o registro de contêiner e especifique que o aplicativo em execução no contêiner escuta solicitações na porta 8080.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Configurar o plug-in do Jenkins do Serviço de Aplicativo do Azure

1. No console Web do Jenkins, selecione o trabalho **MyJavaApp** criado e, em seguida, selecione **Configurar** no lado esquerdo da página.
2. Role para baixo até **Ações Pós-build** e, em seguida, selecione **Adicionar ação pós-build** e escolha **Publicar um Aplicativo Web do Azure**.
3. Em **Configuração de Perfil do Azure**, selecione **Adicionar** ao lado de **Credenciais do Azure** e escolha **Jenkins**.
4. Na caixa de diálogo **Adicionar Credenciais**, selecione **Entidade de Serviço do Microsoft Azure** na lista suspensa **Tipo**.
5. Crie uma Entidade de serviço do Active Directory por meio da CLI do Azure ou do [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
6. Insira as credenciais da entidade de serviço na caixa de diálogo **Adicionar credenciais**. Se você não souber sua ID de assinatura do Azure, consulte-a na CLI:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    ![Configurar uma entidade de serviço do Azure](media/jenkins-java-quickstart/azure_service_principal.png)
6. Verifique se a entidade de serviço é autenticada com o Azure selecionando **Verificar Entidade de Serviço**. 
7. Selecione **Adicionar** para salvar as credenciais.
8. Selecione a credencial da entidade de serviço recém-adicionada na lista suspensa **Credenciais do Azure** quando retornar à configuração **Publicar um Aplicativo Web do Azure**.
9. Em **Configuração do Aplicativo**, escolha seu grupo de recursos e o nome do aplicativo Web na lista suspensa.
10. Selecione o botão de opção **Publicar por meio do Docker**.
11. Insira `complete/Dockerfile` em **caminho do Dockerfile**.
12. Insira `https://jenkinsregistry.azurecr.io` no campo **URL de registro do Docker**.
13. Selecione **Adicionar** ao lado de **Credenciais de Registro**. 
14. Insira o nome de usuário administrador no Registro de Contêiner do Azure criado para o **Nome de usuário**.
15. Insira a senha do registro de Contêiner do Azure no campo **Senha**. Obtenha o nome de usuário e a senha no portal do Azure ou por meio do seguinte comando da CLI:

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![Adicionar suas credenciais de registro de contêiner](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. Selecione **Adicionar** para salvar a credencial.
16. Selecione a credencial recém-criada na lista suspensa **Credenciais de registro** no painel **Configuração do Aplicativo** da opção **Publicar um Aplicativo Web do Azure**. A ação pós-build concluída deverá ser parecida com a seguinte imagem:   
    ![Configuração da ação pós-build para Implantação do Serviço de Aplicativo do Azure](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. Selecione **Salvar** para salvar a configuração de trabalho.

## <a name="deploy-the-app-from-github"></a>Implantar o aplicativo por meio do GitHub

1. No projeto do Jenkins, selecione **Compilar Agora** para implantar o aplicativo de exemplo no Azure.
2. Depois que o build for concluído, o aplicativo estará ativo no Azure em sua URL de publicação, por exemplo http://myjavaapp.azurewebsites.net.   
   ![Exibir o aplicativo implantado no Azure](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>Enviar as alterações por push e implantar novamente

1. No fork do GitHub, navegue na Web para `complete/src/main/java/Hello/Application.java`. Selecione o link **Editar este arquivo** no lado direito da interface do GitHub.
2. Faça a alteração a seguir no método `home()` e confirme a alteração no branch mestre do repositório.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. Um novo build será iniciado no Jenkins, disparado pela nova confirmação no branch `master` do repositório. Depois que ele for concluído, recarregue o aplicativo no Azure.     
      ![Exibir o aplicativo implantado no Azure](media/jenkins-java-quickstart/hello_docker_world.png)
  
## <a name="next-steps"></a>Próximas etapas

- [Usar VMs do Azure como agentes de build](/azure/jenkins/jenkins-azure-vm-agents)
- [Gerenciar recursos em trabalhos e pipelines com a CLI do Azure](/azure/jenkins/execute-cli-jenkins-pipeline)
 
