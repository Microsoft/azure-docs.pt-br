---
title: Tutorial - Implantar do GitHub para o Serviço de Aplicativo do Azure com o Jenkins
description: Configurar o Jenkins para CI (integração contínua) do GitHub e CD (implantação contínua) para o Serviço de Aplicativo do Azure no caso de aplicativos Web Java
services: jenkins
ms.service: jenkins
author: tomarchermsft
ms.author: tarcher
manager: jeconnoc
ms.topic: tutorial
ms.date: 11/15/2018
ms.openlocfilehash: 019c4a8f77f2664c68dcc6499fb2f27cc0d1447c
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326919"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Tutorial: Implantar do GitHub para o Serviço de Aplicativo do Azure com implantação e integração contínua do Jenkins

Este tutorial implanta um aplicativo Web Java de exemplo do GitHub no [Serviço de Aplicativo do Azure no Linux](/azure/app-service/containers/app-service-linux-intro) configurando a CI (integração contínua) e a CD (implantação contínua) no Jenkins. Ao atualizar o aplicativo efetuando push das atualizações para o GitHub, o Jenkins criará e republicará seu aplicativo automaticamente no Serviço de Aplicativo do Azure. O aplicativo de exemplo neste tutorial foi desenvolvido usando a estrutura [Spring Boot](https://projects.spring.io/spring-boot/). 

![Visão geral](media/tutorial-jenkins-deploy-web-app-azure-app-service/overview.png)

Neste tutorial, você concluirá estas tarefas:

> [!div class="checklist"]
> * Instalar plug-ins do Jenkins para poder compilar no GitHub, implantar no Serviço de Aplicativo do Azure e realizar outras tarefas.
> * Criar fork do repositório GitHub de exemplo para ter uma cópia funcional.
> * Conectar o Jenkins ao GitHub.
> * Criar uma entidade de serviço do Azure para que o Jenkins possa acessar o Azure sem usar suas credenciais.
> * Adicionar a entidade de serviço ao Jenkins.
> * Criar o pipeline do Jenkins que compila e implanta o aplicativo de exemplo sempre que você atualiza o aplicativo no GitHub.
> * Criar arquivos de compilação e implantação para o pipeline do Jenkins.
> * Apontar o pipeline do Jenkins para o script de compilação e implantação.
> * Implantar seu aplicativo de exemplo no Azure executando uma compilação manual.
> * Efetuar push de uma atualização no GitHub, que dispara o Jenkins para compilar e implantar novamente no Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará destes itens:

* Um servidor [Jenkins](https://jenkins.io/) com as ferramentas do JDK (Java Development Kit) e o Maven instalado em uma VM Linux do Azure

  Se você não tiver um servidor Jenkins, conclua estas etapas agora no portal do Azure: [Criar servidor Jenkins em uma VM do Linux do Azure](/azure/jenkins/install-jenkins-solution-template)

* Uma conta do [GitHub](https://github.com), de modo que você possa obter uma cópia funcional (fork) do aplicativo Web Java de exemplo. 

* A [CLI do Azure](/cli/azure/install-azure-cli), que pode ser executada na linha de comando local ou no [Azure Cloud Shell](/azure/cloud-shell/overview)

## <a name="install-jenkins-plug-ins"></a>Instalar os plug-ins do Jenkins

1. Entre no console Web do Jenkins neste local:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. Na página principal do Jenkins, escolha **Gerenciar o Jenkins** > **Gerenciar Plug-ins**.

   ![Gerenciar plug-ins do Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. Na guia **Disponível**, marque estes plug-ins:

   - [Serviço de Aplicativo do Azure](https://plugins.jenkins.io/azure-app-service)
   - [Origem de Branch do GitHub](https://plugins.jenkins.io/github-branch-source)
   - [Plug-in Injetor do Ambiente](https://plugins.jenkins.io/envinject) do Jenkins
   - [Credenciais do Azure](https://plugins.jenkins.io/azure-credentials)

   Se os plug-ins não forem exibidos, verifique se eles já estão instalados verificando a guia **Instalados**.

1. Para instalar os plug-ins escolhidos, escolha **Baixar agora e instalar após a reinicialização**.

1. Depois de terminar, no menu do Jenkins, escolha **Gerenciar o Jenkins** para voltar até a página de gerenciamento do Jenkins e iniciar outras etapas.

## <a name="fork-sample-github-repo"></a>Criar fork do repositório GitHub de exemplo

1. [Entrar no repositório do GitHub para obter o aplicativo de exemplo Spring Boot](https://github.com/spring-guides/gs-spring-boot). 

1. No canto superior direito do GitHub, escolha **Fork**.

   ![Criar fork do repositório GitHub de exemplo](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. Siga os prompts para escolher sua conta do GitHub e concluir o fork.

Em seguida, configure o Jenkins com suas credenciais do GitHub.

## <a name="connect-jenkins-to-github"></a>Conectar o Jenkins ao GitHub

Para que o Jenkins monitore o GitHub e responda quando novas confirmações forem enviadas por push para o aplicativo Web no fork do GitHub, habilite [Webhooks do GitHub](https://developer.github.com/webhooks/) no Jenkins.

> [!NOTE]
> 
> Estas etapas criam credenciais de token de acesso pessoal para o Jenkins trabalhar com o GitHub usando seu nome de usuário e senha do GitHub. 
> No entanto, se sua conta do GitHub usa a autenticação de dois fatores, crie o token no GitHub e configure o Jenkins para usar esse token. 
> Para obter mais informações, confira a documentação sobre o [plug-in GitHub do Jenkins](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin).

1. Na página **Gerenciar o Jenkins**, escolha **Configurar Sistema**. 

   ![Configurar sistema](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. Na seção **GitHub**, forneça detalhes sobre seu servidor do GitHub. Na lista **Adicionar Servidor GitHub**, escolha **Servidor GitHub**. 

   ![Adicionar servidor GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. Se a propriedade **Gerenciar ganchos** não estiver marcada, marque-a. Escolha **Avançado** para poder especificar outras configurações. 

   ![Escolher "Avançado" para obter mais configurações](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. Na lista **Gerenciar ações adicionais do GitHub**, escolha **Converter logon e senha em token**.

   ![Escolher "Gerenciar ações adicionais do GitHub"](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. Escolha **Com base em logon e senha** para poder inserir seu nome de usuário e senha do GitHub. Quando terminar, escolha **Criar credenciais de token**, que criará um [PAT (token de acesso pessoal) do GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![Criar PAT do GitHub com base em logon e senha](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. Na seção **Servidor GitHub**, na lista **Credenciais**, escolha o novo token. Verifique se a autenticação está funcionando marcando **Testar conexão**.

   ![Verificar conexão com o servidor GitHub com o novo PAT](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

Em seguida, crie a entidade de serviço do Azure que o Jenkins usa para autenticar e acessar recursos do Azure.

## <a name="create-service-principal"></a>Criar uma entidade de serviço

Em uma seção posterior, você deverá criar um trabalho de pipeline do Jenkins para compilar seu aplicativo do GitHub e implantá-lo no Serviço de Aplicativo do Azure. Para que o Jenkins acesse o Azure sem inserir suas credenciais, crie uma [entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) no Azure Active Directory para o Jenkins. Uma entidade de serviço é uma identidade separada que o Jenkins pode usar para autenticar o acesso a recursos do Azure. Para criar essa entidade de serviço, execute o comando da CLI do Azure [ **`az ad sp create-for-rbac`** ](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) em sua linha de comando local ou no Azure Cloud Shell, por exemplo: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Use aspas para delimitar o nome da entidade de serviço. Além disso, crie uma senha forte com base nas [restrições e regras de senha do Azure Active Directory](/azure/active-directory/active-directory-passwords-policy). Se você não fornecer uma senha, a CLI do Azure criará uma. 

Veja a saída gerada pelo comando **`create-for-rbac`** : 

```json
{
   "appId": "yourAzureServicePrincipal-ID", // A GUID such as AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
   "displayName": "yourAzureServicePrincipalName", // A user-friendly name for your Azure service principal
   "name": "http://yourAzureServicePrincipalName",
   "password": "yourSecurePassword",
   "tenant": "yourAzureActiveDirectoryTenant-ID" // A GUID such as BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
}
```

> [!TIP]
> 
> Se você já tiver uma entidade de serviço, poderá reutilizá-la.
> Ao fornecer valores de entidade de serviço para autenticação, use os valores das propriedades `appId`, `password` e `tenant`. 
> Ao procurar por uma entidade de serviço existente, use o valor da propriedade `displayName`.

## <a name="add-service-principal-to-jenkins"></a>Adicionar entidade de serviço ao Jenkins

1. No painel principal do Jenkins, escolha **Credenciais** > **Sistema**. 

1. Na página **Sistema**, em **Domínio**, escolha **Credenciais globais (irrestritas)** .

1. No menu à esquerda, escolha **Adicionar Credenciais**.

1. Na lista **Variante**, escolha **Entidade de Serviço do Azure**.

1. Forneça as informações da entidade de serviço e da assinatura do Azure nas propriedades descritas pela tabela nesta etapa:

   ![Adicionar credenciais de entidade de serviço do Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Propriedade | Valor | DESCRIÇÃO | 
   |----------|-------|-------------| 
   | **ID da assinatura** | <*yourAzureSubscription-ID*> | O valor do GUID da assinatura do Azure <p>**Dica**: Se não souber sua ID de assinatura do Azure, execute este comando da CLI do Azure na linha de comando ou no Cloud Shell e, em seguida, use o valor do GUID `id`: <p>`az account list` | 
   | **ID do Cliente** | <*yourAzureServicePrincipal-ID*> | O valor do GUID `appId` gerado anteriormente para a entidade de serviço do Azure | 
   | **Segredo do Cliente** | <*yourSecurePassword*> | O valor `password` ou "segredo" fornecido para a entidade de serviço do Azure | 
   | **ID do locatário** | <*yourAzureActiveDirectoryTenant-ID*> | O valor do GUID `tenant` do locatário do Azure Active Directory | 
   | **ID** | <*yourAzureServicePrincipalName*> | O valor `displayName` da entidade de serviço do Azure | 

1. Para confirmar que a entidade de serviço funciona, escolha **Verificar Entidade de Serviço**. Quando terminar, escolha **OK**.

Em seguida, crie o pipeline do Jenkins que compila e implanta seu aplicativo.

## <a name="create-jenkins-pipeline"></a>Criar pipeline do Jenkins

No Jenkins, crie o trabalho de pipeline para criar e implantar seu aplicativo.

1. Retorne à home page do Jenkins e escolha **Novo Item**. 

   ![Selecione “Novo Item”](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. Forneça um nome para seu trabalho de pipeline, por exemplo, "My-Java-Web-App", e escolha **Pipeline**. Na parte inferior, escolha **OK**.  

   ![Escolher "Pipeline"](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. Configure o Jenkins com sua entidade de serviço para que o Jenkins possa implantar no Azure sem usar suas próprias credenciais.

   1. Na guia **Geral**, escolha **Preparar um ambiente para execução**. 

   1. Na caixa **Conteúdo das Propriedades** exibida, adicione essas variáveis de ambiente e seus valores. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Escolher "Preparar um ambiente para execução" e definir variáveis de ambiente](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-run.png)

1. Quando terminar, escolha **Salvar**.

Em seguida, crie scripts de compilação e implantação para o Jenkins.

## <a name="create-build-and-deployment-files"></a>Criar arquivos de compilação e implantação

Agora, crie os arquivos que usam o Jenkins para criar e implantar seu aplicativo.

1. Na pasta `src/main/resources/` de seu fork no GitHub, crie esse arquivo de configuração de aplicativo denominado `web.config`, que contém o XML, mas substitua `$(JAR_FILE_NAME)` por `gs-spring-boot-0.1.0.jar`:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. Na pasta raiz de seu fork no GitHub, crie esse script de compilação e implantação denominado `Jenkinsfile`, que contém o texto ([fonte no GitHub aqui](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)):

   ```groovy
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. Confirme os arquivos `web.config` e `Jenkinsfile` em seu fork no GitHub e efetue push de suas alterações.

## <a name="point-pipeline-at-script"></a>Apontar o pipeline para o script

Agora, especifique o script de compilação e implantação que você deseja que o Jenkins use.

1. No Jenkins, escolha o trabalho de pipeline criado anteriormente. 

   ![Escolher o trabalho de pipeline para o aplicativo Web](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. No menu esquerdo, escolha **Configurar**.

1. Na guia **Pipeline**, na lista **Definição**, marque **Script de pipeline do SCM**.

   1. Na caixa **SCM** exibida, marque **Git** como o controle de controle do código-fonte. 

   1. Na seção **Repositórios**, em **URL do Repositório**, insira a URL do fork do GitHub, por exemplo: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. Em **Credenciais**, escolha seu token de acesso pessoal do GitHub criado anteriormente.

   1. Na caixa **Caminho do Script**, adicione o caminho para o script "Jenkinsfile".

   Quando terminar, sua definição de pipeline se parecerá com este exemplo: 

   ![Apontar o pipeline para o script](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. Quando terminar, escolha **Salvar**.

Em seguida, compile e implante seu aplicativo no Serviço de Aplicativo do Azure. 

## <a name="build-and-deploy-to-azure"></a>Criar e implantar no Azure

1. Com a CLI do Azure, na linha de comando ou no Azure Cloud Shell, crie um [Aplicativo Web do Serviço de Aplicativo do Azure no Linux](/azure/app-service/containers/app-service-linux-intro) para que o Jenkins implante seu aplicativo Web depois de concluir uma compilação. Verifique se seu aplicativo Web tem um nome exclusivo.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Para obter mais informações sobre esses comandos da CLI do Azure, confira estas páginas:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. No Jenkins, escolha o trabalho de pipeline e marque **Compilar Agora**.

   Após a conclusão da compilação, o Jenkins implantará seu aplicativo, que agora está ativo no Azure na URL de publicação, por exemplo: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Exibir o aplicativo implantado no Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Enviar as alterações por push e implantar novamente

1. No navegador da Web, vá para este local no fork do GitHub de seu aplicativo Web:

   `complete/src/main/java/Hello/Application.java`
   
1. No canto superior direito no GitHub, escolha **Editar este arquivo**.

1. Faça essa alteração no método `commandLineRunner()` e confirme a mudança para o branch `master` do repositório. A confirmação no branch `master` iniciará uma compilação no Jenkins. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Após a conclusão da compilação e a reimplantação no Azure pelo Jenkins, atualize seu aplicativo, que agora exibirá a atualização.

   ![Exibir o aplicativo implantado no Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Solução de problemas do plug-in do Jenkins

Se você encontrar bugs nos plug-ins do Jenkins, registre um problema no [JIRA do Jenkins](https://issues.jenkins-ci.org/) para o componente específico.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Usar VMs do Azure como agentes de build](/azure/jenkins/jenkins-azure-vm-agents)
