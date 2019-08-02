---
title: Usando o plug-in do Azure Dev Spaces para Jenkins com o Serviço de Kubernetes do Azure
description: Saiba como usar o plug-in do Azure Dev Spaces em um pipeline de integração contínua.
author: tomarchermsft
ms.author: tarcher
ms.service: jenkins
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/18/2019
ms.openlocfilehash: f5f74ebeb803a5c493f1dbedb6501adf3a88c215
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785663"
---
<!-- GMinchAQ, 06/18/19 -->

# <a name="tutorial-using-the-azure-dev-spaces-plugin-for-jenkins-with-azure-kubenetes-service"></a>Tutorial: Usando o plug-in do Azure Dev Spaces para Jenkins com o Serviço de Kubernetes do Azure 

O Azure Dev Spaces permite que você teste e desenvolva de modo iterativo seu aplicativos de microsserviço em execução no AKS (Serviço de Kubernetes do Azure) sem precisar replicar nem simular dependências. O plug-in Azure Dev Spaces para Jenkins ajuda a usar o Dev Spaces em seu pipeline de entrega (CI/CD) e integração contínua.

Este tutorial utiliza o ACR (Registro de Contêiner do Azure). O ACR armazena imagens e uma Tarefa do ACR compila os artefatos do Docker e do Helm. Usar o ACR e a Tarefas do ACR para geração de artefato elimina a necessidade de instalar software adicional, como o Docker, em seu servidor Jenkins. 

Neste tutorial, você concluirá estas tarefas:

> [!div class="checklist"]
> * Criar um cluster AKS habilitado para o Azure Dev Spaces
> * Implantar um aplicativo de vários serviços no AKS
> * Preparar seu servidor Jenkins
> * Use o plug-in do Azure Dev Spaces em um pipeline do Jenkins para visualizar as alterações de código antes de mesclá-las no projeto

Este tutorial pressupõe conhecimento intermediário dos principais serviços do Azure, AKS, ACR, Azure Dev Spaces, [pipelines](https://jenkins.io/doc/book/pipeline/) e plug-ins do Jenkins e GitHub. É útil ter uma familiaridade básica com ferramentas de suporte, como kubectl e Helm.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Uma conta do GitHub. Se você ainda não tem uma conta do GitHub, crie uma [conta gratuita](https://github.com/) antes de começar.

* [Visual Studio Code](https://code.visualstudio.com/download) coma extensão [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) instalada.

* [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest), versão 2.0.43 ou superior.

* Um servidor mestre do Jenkins. Se você ainda não tiver um mestre Jenkins, implante [Jenkins](https://aka.ms/jenkins-on-azure) no Azure seguindo as etapas deste [início rápido](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* O servidor Jenkins deve ter tanto o Helm quanto o kubectl instalados e disponíveis para a conta do Jenkins, conforme explicado mais adiante neste tutorial.

* VS Code, o Terminal do VS Code ou WSL e Bash. 


## <a name="create-azure-resources"></a>Criar recursos do Azure

Nesta seção, você cria recursos do Azure:

* Um grupo de recursos para conter todos os recursos do Azure para este tutorial.
* Um cluster do AKS ([Serviço de Kubernetes do Azure](https://docs.microsoft.com/azure/aks/)).
* Um ACR ([Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/)) para compilar (usando as Tarefas do ACR) e armazenar imagens do Docker.

1. Crie um grupos de recursos.

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. Criar um cluster do AKS. Crie o cluster do AKS em uma [região compatível com o Dev Spaces](../dev-spaces/about.md#supported-regions-and-configurations).

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Configure o AKS para usar o Dev Spaces.

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Esta etapa instala a extensão da CLI `azds`.

4. Crie um registro de contêiner.

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Implantar aplicativos de exemplo no cluster do AKS

Nesta seção, você pode configurar um espaço de desenvolvimento e implantar um aplicativo de exemplo no cluster do AKS criado na seção anterior. O aplicativo consiste em duas partes, *webfrontend* e *mywebapi*. Ambos os componentes são implantados em um espaço de desenvolvimento. Mais adiante neste tutorial, você enviará uma solicitação de pull para mywebapi para disparar o pipeline de CI no Jenkins.

Para obter mais informações sobre como usar o Azure Dev Spaces e o desenvolvimento de vários serviços com o Azure Dev Spaces, confira [Introdução sobre o Azure Dev Spaces com Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java) e [Desenvolvimento de vários serviços com o Azure Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Esses tutoriais apresentam informações de contexto adicionais não incluídas aqui.

1. Baixe o repositório https://github.com/Azure/dev-spaces do GitHub.

2. Abra a pasta `samples/java/getting-started/webfrontend` no VS Code. (Você pode ignorar todos os prompts padrão para adicionar ativos de depuração ou restaurar o projeto.)

3. Atualize o `/src/main/java/com/ms/sample/webfrontend/Application.java` para que se pareça com o seguinte:

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. Clique em **Exibir** e, em seguida, **Terminal** para abrir o Terminal Integrado no VS Code.

5. Execute o comando `azds prep` para preparar seu aplicativo para execução em um espaço de desenvolvimento. Esse comando deve ser executado no `dev-spaces/samples/java/getting-started/webfrontend` para preparar seu aplicativo corretamente:

    ```bash
    azds prep --public
    ```

    O comando `azds prep` da CLI do Dev Spaces gera ativos do Docker e do Kubernetes com as configurações padrão. Esses arquivos persistirem pelo tempo de vida do projeto e podem ser personalizados:

    * `./Dockerfile` e `./Dockerfile.develop` descrevem a imagem de contêiner do aplicativo e como o código-fonte é compilado e executado no contêiner.
    * Um [Gráfico Helm](https://helm.sh/docs/developing_charts/) em `./charts/webfrontend` descreve como implantar o contêiner no Kubernetes.
    * `./azds.yaml` é o arquivo de configuração do Azure Dev Spaces.

    Para obter mais informações, confira [Como o Azure Dev Spaces funciona e é configurado](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Compile e execute o aplicativo no AKS usando o comando `azds up`:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Examine a saída do console para obter informações sobre a URL pública criada com o comando `up`. Ela estará neste formato:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Abra essa URL em uma janela do navegador e você deverá ver o aplicativo Web. Conforme o contêiner é executado, a saída de `stdout` e `stderr` é transmitida para a janela do terminal.

8. Em seguida, configure e implante *mywebapi*:

    1. Altere o diretório para `dev-spaces/samples/java/getting-started/mywebapi`

    2. Executar

        ```bash
        azds prep
        ```

    3. Executar

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Preparar o servidor Jenkins

Nesta seção, você prepara o servidor Jenkins para executar o pipeline de CI de exemplo.

* Instalar plug-ins
* Instalar a CLI do Helm e do Kubernetes
* Adicionar credenciais

### <a name="install-plugins"></a>Instalar plug-ins

1. Entre no seu servidor Jenkins. Escolha **Gerenciar Jenkins > Gerenciar Plug-ins**.
2. Na guia **Disponível**, selecione os seguintes plug-ins:
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Tarefas do Registro de Contêiner do Azure](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Injetor do Ambiente](https://plugins.jenkins.io/envinject)
    * [Integração com o GitHub](https://plugins.jenkins.io/github-pullrequest)

    Se esses plug-ins não aparecem na lista, verifique a guia **Instalado** para ver se eles já estão instalados.

3. Para instalar os plug-ins, escolha **Baixar agora e instalar após a reinicialização**.

4. Reinicie o servidor do Jenkins para concluir a instalação.

### <a name="install-helm-and-kubectl"></a>Instalar o Helm e o kubectl

O pipeline de exemplo usa o Helm e o kubectl para implantar no espaço de desenvolvimento. Quando o Jenkins está instalado, ele cria uma conta do administrador chamada *jenkins*. Tanto o Helm quanto o kubectl precisam estar acessíveis ao usuário do Jenkins.

1. Faça uma conexão SSH com o mestre do Jenkins. 

2. Alterne para o usuário `jenkins`:
    ```bash
    sudo su jenkins
    ```

3. Instale a CLI do Helm. Para obter mais informações, confira [Como instalar o Helm](https://helm.sh/docs/using_helm/#installing-helm).

4. Instale o kubectl. Para obter mais informações, confira [**az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Adicionar credenciais ao Jenkins

1. O Jenkins precisa de uma entidade de serviço do Azure para autenticar e acessar recursos do Azure. Para criar a entidade de serviço, confira a seção [Criar entidade de serviço](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) no tutorial Implantar no Serviço de Aplicativo do Azure. Salve uma cópia da saída do `create-for-rbac` porque você precisa dessas informações para concluir a próxima etapa. A saída será parecida com esta:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Adicionar um tipo de credencial *Entidade de Serviço do Microsoft Azure* no Jenkins usando as informações de entidade de serviço da etapa anterior. Os nomes na captura de tela abaixo correspondem à saída de `create-for-rbac`.

    O campo **ID** é o nome da credencial do Jenkins para a sua entidade de serviço. O exemplo usa o valor de `displayName` (neste caso, `xxxxxxxjenkinssp`), mas você pode usar qualquer texto que deseje. Esse nome de credencial é o valor da variável de ambiente AZURE_CRED_ID na próxima seção.

    ![Adicionar credenciais de entidade de serviço ao Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    A **Descrição** é opcional. Para obter instruções mais detalhadas, confira a seção [Adicionar entidade de serviço ao Jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) no tutorial Implantar no Serviço de Aplicativo do Azure. 



3. Para mostrar suas credenciais do ACR, execute este comando:

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    Faça uma cópia da saída JSON, que deve ser semelhante a esta:

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. Adicione um tipo de credencial de *Nome de usuário com senha* no Jenkins. O **nome de usuário** é o nome de usuário da última etapa, neste exemplo, `acr01`. A **senha** é o valor para a primeira senha, neste exemplo `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. A **ID** dessa credencial é o valor de ACR_CRED_ID.

5. Configure uma credencial do AKS. Adicione um tipo de credencial *Configuração do Kubernetes (kubeconfig)* no Jenkins (use a opção "Inserir diretamente"). Para obter as credenciais de acesso para seu cluster do AKS, execute o seguinte comando:

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   A **ID** dessa credencial é o valor de KUBE_CONFIG_ID da próxima seção.

## <a name="create-a-pipeline"></a>Criar um pipeline

O cenário selecionado para o pipeline de exemplo baseia-se em um padrão do mundo real: Uma solicitação de pull dispara um pipeline de CI que compila e então implanta as alterações propostas a um espaço de desenvolvimento do Azure para teste e análise. Dependendo do resultado da análise, as alterações são mescladas e implantadas no AKS ou descartadas. Por fim, o espaço de desenvolvimento é removido.

A configuração do pipeline do Jenkins e o arquivo Jenkins definem os estágios no pipeline de CI. Este fluxograma mostra os estágios do pipeline e os pontos de decisão definidos pelo Jenkinsfile:

![Fluxo de pipeline do Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Baixe uma versão modificada do projeto *mywebapi* de https://github.com/azure-devops/mywebapi. Esse projeto contém vários arquivos necessários para criar um pipeline, incluindo *Jenkinsfile*, *Dockerfiles* e o gráfico do Helm.

2. Faça logon no Jenkins. No menu à esquerda, selecione **Adicionar Item**.

3. Selecione **Pipeline** e insira um nome na caixa **Inerir um nome de item**. Selecione **OK** e a tela de configuração do pipeline será aberta automaticamente.

4. Na guia **Geral**, marque **Preparar um ambiente para execução**. 

5. Marque **Manter as Variáveis de Ambiente do Jenkins** e **Manter as Variáveis de Build do Jenkins**.

6. Na caixa **Conteúdo de Propriedades**, digite as seguintes variáveis de ambiente:

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    Usando os valores de exemplo fornecidos nas seções anteriores, a lista de variáveis de ambiente deve ser algo parecido com isto:

    ![Variáveis de ambiente de pipeline do Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Escolha **Script de pipeline do SCM** em **Pipeline > Definição**.
8. Em **SCM**, escolha **Git** e insira a URL do repositório.
9. Em **Especificador de Branch**, insira `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Preencha a URL do repositório do SCM e o caminho do script "Jenkinsfile".
11. **Check-out leve** deve ser marcado.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Crie uma solicitação de pull para disparar o pipeline

Para concluir a etapa 3 nesta seção, você precisará comentar parte do Jenkinsfile, caso contrário, obterá um erro 404 ao tentar exibir as versões nova e antiga lado a lado. Por padrão, quando você escolhe mesclar a PR, a versão compartilhada anteriormente do mywebapi será removida e substituída pela nova versão. Faça a seguinte alteração ao Jenkinsfile antes de concluir a etapa 1:

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. Faça uma alteração a `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java` e, em seguida, crie uma solicitação de pull. Por exemplo:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Faça logon no Jenkins, selecione o nome do pipeline e então escolha **Compilar Agora**. 

    Você também pode configurar um *webhook* para disparar automaticamente o pipeline do Jenkins. Quando uma solicitação de pull é inserida, o GitHub emite um POST para o Jenkins, disparando o pipeline. Para obter mais informações sobre como configurar um webhook, confira [Conectar o Jenkins ao GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Compare as alterações com a versão compartilhada atual:

    1. Abra seu navegador e navegue até a versão compartilhada `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT contém a URL.

    2. Abra outra guia e insira a URL do espaço de desenvolvimento da PR. Ele será semelhante a `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. Você encontrará o link em **Histórico de Build ><build#> > Saída do Console** para o trabalho do Jenkins. Pesquise `aksapp` na página ou, para ver apenas o prefixo, pesquise `azdsprefix`.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Como criar a URL para o espaço de desenvolvimento filho

Quando você registra uma solicitação de pull, o Jenkins cria um espaço de desenvolvimento filho com base no espaço de desenvolvimento compartilhado da equipe e executa o código de sua solicitação de pull nesse espaço de desenvolvimento filho. A URL para o espaço de desenvolvimento filho assume a forma `http://$env.azdsprefix.<test_endpoint>`. 

**$env.azdsprefix** é definido durante a execução do pipeline pelo plug-in do Azure Dev Spaces por **devSpacesCreate**:

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

O `test_endpoint` é a URL para o aplicativo webfrontend implantado anteriormente usando `azds up`em [Implantar aplicativos de exemplo para o cluster do AKS, Etapa 7](#test_endpoint). O valor de `$env.TEST_ENDPOINT` é definido na configuração do pipeline. 

O snippet de código a seguir mostra como a URL do espaço de desenvolvimento filho é usada no estágio `smoketest`. O código verifica se o espaço de desenvolvimento filho TEST_ENDPOINT está disponível e, caso esteja, baixa o texto de saudação para stdout:

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar de usar o aplicativo de exemplo, limpe os recursos do Azure excluindo o grupo de recursos:

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o plug-in do Azure Dev Spaces para Jenkins e o plug-in de Registro de Contêiner do Azure para compilar o código e implantar um espaço de desenvolvimento.

A lista de recursos a seguir apresenta mais informações sobre o Azure Dev Spaces, as Tarefas do ACR e CI/CD com Jenkins.

Azure Dev Spaces:
* [Como o Azure Dev Spaces funciona e é configurado](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

Tarefas do ACR:
* [Automatizar o sistema operacional e a aplicação de patches de estrutura com as Tarefas de ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)
* [Build automático na confirmação de código](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

CI/CD com Jenkins no Azure:
* [Implantação contínua do Jenkins](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)
