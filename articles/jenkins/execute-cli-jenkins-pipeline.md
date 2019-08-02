---
title: Executar CLI do Azure com o Jenkins
description: Saiba como usar a CLI do Azure para implantar um aplicativo Web do Java no Azure na pipeline do Jenkins
ms.service: jenkins
keywords: jenkins, azure, devops, serviço de aplicativo, cli
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 6/7/2017
ms.openlocfilehash: 5728a9ab70c5b7db10a123d6964b498e70f96588
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66162202"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Implantar o Serviço de Aplicativo do Azure com o Jenkins e a CLI do Azure
Para implantar um aplicativo Web do Java no Azure, você pode usar a CLI do Azure na [pipeline do Jenkins](https://jenkins.io/doc/book/pipeline/). Neste tutorial, você cria um pipeline de CI/CD em uma VM do Azure, incluindo como:

> [!div class="checklist"]
> * Criar uma VM Jenkins
> * Configurar o Jenkins
> * Criar um aplicativo Web no Azure
> * Preparar um repositório GitHub
> * Criar pipeline do Jenkins
> * Executar o pipeline e verificar o aplicativo Web

Este tutorial requer a CLI do Azure, versão 2.0.4 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisar atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Criar e configurar uma instância do Jenkins
Se você ainda não tiver um mestre do Jenkins, comece com o [Modelo de solução](install-jenkins-solution-template.md), que inclui o plug-in [Credenciais do Azure](https://plugins.jenkins.io/azure-credentials) necessário por padrão. 

O plug-in Credencial do Azure permite que você armazene as credenciais de entidade de serviço do Microsoft Azure no Jenkins. Na versão 1.2, adicionamos suporte para que esse pipeline do Jenkins possa obter as credenciais do Azure. 

Verifique se você tem a versão 1.2 ou posterior:
* No painel do Jenkins, clique em **Gerenciar Jenkins -> Gerenciador de plug-ins ->** e pesquise pela **Credencial do Azure**. 
* Atualize o plug-in se a versão for anterior à 1.2.

O Java JDK e o Maven também são necessários no mestre do Jenkins. Para instalar, entre no Jenkins principal usando o SSH e execute os seguintes comandos:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Adicione uma entidade de serviço do Azure na credencial do Jenkins

Uma credencial do Azure é necessária para executar a CLI do Azure.

* No painel do Jenkins, clique em **Credenciais -> Sistema ->** . Clique em **Credenciais globais (irrestrito)** .
* Clique em **Adicionar credenciais** para adicionar uma [entidade de serviço do Microsoft Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json), preenchendo a ID da assinatura, a ID do cliente, o segredo do cliente e o Ponto de Extremidade do Token OAuth 2.0. Forneça uma ID para uso na próxima etapa.

![Adicionar Credenciais](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Criar um Serviço de Aplicativo do Azure para implantar o aplicativo Web Java

Crie um plano do Serviço de Aplicativo do Azure com o tipo de preço **GRÁTIS** usando a CLI de comando [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create). O plano do serviço de aplicativo define os recursos físicos usados para hospedar seus aplicativos. Todos os aplicativos atribuídos a um plano do serviço de aplicativo compartilham esses recursos, permitindo que você economize hospedando vários aplicativos. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Quando o plano estiver pronto, a CLI do Azure mostra uma saída semelhante ao exemplo a seguir:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Criar um aplicativo Web do Azure

 Use a CLI de comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) para criar uma definição de aplicativo Web no `myAppServicePlan` Plano do Serviço de Aplicativo. A definição de aplicativo Web fornece uma URL para acessar seu aplicativo e define várias opções para implantar seu código no Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Substitua o espaço reservado `<app_name>` por seu próprio nome exclusivo do aplicativo. Esse nome exclusivo é parte do nome de domínio padrão para o aplicativo Web, portanto, o nome precisa ser exclusivo entre todos os aplicativos no Azure. Você poderá mapear uma entrada de nome de domínio personalizada para o aplicativo Web antes de expor para seus usuários.

Quando a definição do aplicativo Web tiver pronta, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Configurar o Java 

Definir a configuração de tempo de execução Java que seu aplicativo precisa com o comando [az appservice web config update](/cli/azure/webapp/config).

O comando a seguir configura o aplicativo Web para ser executado em um JDK 8 Java recente e [Apache Tomcat](https://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Preparar um Repositório GitHub
Abra o repositório [Aplicativo Web Java simples para o Azure](https://github.com/azure-devops/javawebappsample). Para bifurcar o repositório para sua própria conta do GitHub, clique no botão **Bifurcação** no canto superior direito.

* Na interface do usuário da Web do GitHub, abra o arquivo **Jenkinsfile**. Clique no ícone de lápis para editar esse arquivo para atualizar o grupo de recursos e o nome do seu aplicativo Web nas linhas 20 e 21, respectivamente.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Altere a linha 23 para atualizar a ID de credencial na sua instância do Jenkins

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Criar pipeline do Jenkins
Abra o Jenkins em um navegador da Web, clique em **Novo Item**. 

* Dê um nome para o trabalho e selecione **Pipeline**. Clique em **OK**.
* Clique na próxima guia **Pipeline**. 
* Para **Definição**, selecione **Script de pipeline do SCM**.
* Para **SCM**, selecione **Git**.
* Insira a URL do GitHub em seu repositório bifurcado: https:\<seu repositório bifurcado\>.git
* Clique em **Salvar**

## <a name="test-your-pipeline"></a>Testar o pipeline
* Acesse o pipeline que você criou, clique em **Compilar agora**
* Um build deve ter êxito em poucos segundos e você pode acessar o build e clicar em **Saída do Console** para ver os detalhes

## <a name="verify-your-web-app"></a>Verifique seu aplicativo Web
Para verificar se o arquivo WAR foi implantado com êxito em seu aplicativo Web, abra um navegador da Web:

* Acesse http://&lt;app_name>.azurewebsites.net/api/calculator/ping  
Você verá:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Acesse http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (substitua &lt;x > e &lt;y > por qualquer número) para obter a soma de x e y

![Calculadora: adicionar](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Implantar no Aplicativo Web do Azure no Linux
Agora que você sabe como usar a CLI do Azure em seu pipeline Jenkins, você pode modificar o script para implantar um aplicativo Web do Azure no Linux.

O aplicativo Web no Linux dá suporte a uma forma diferente de fazer a implantação, que é usar o Docker. Para implantar, você precisa fornecer um Dockerfile que empacote seu aplicativo Web com tempo de execução do serviço em uma imagem do Docker. O plug-in, então, compilará a imagem, a enviará por push a um registro de Docker e a implantará em seu aplicativo Web.

* Siga as etapas [aqui](../app-service/containers/quickstart-nodejs.md) para criar um aplicativo Web do Azure em execução no Linux.
* Instale o Docker em sua instância do Jenkins seguindo as instruções deste [artigo](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Crie um registro de contêiner no Portal do Azure seguindo as etapas [aqui](/azure/container-registry/container-registry-get-started-azure-cli).
* No mesmo repositório bifurcado do [Aplicativo Web Java Simples para o Azure](https://github.com/azure-devops/javawebappsample), edite o arquivo **Jenkinsfile2**:
    * Nas linhas 18-21, atualize com os nomes do grupo de recursos, do aplicativo Web e do ACR, respectivamente. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Na linha 24, atualize \<azsrvprincipal\> com sua ID de credencial
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Crie um novo pipeline do Jenkins como fez ao implantar o aplicativo Web do Azure no Windows. Mas, somente dessa vez, use o **Jenkinsfile2**.
* Execute seu novo trabalho.
* Para verificar, na CLI do Azure, execute:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Você obterá o seguinte resultado:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Acesse http://&lt;app_name>.azurewebsites.net/api/calculator/ping. Você verá a mensagem: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Acesse http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (substitua &lt;x > e &lt;y > por qualquer número) para obter a soma de x e y
    
## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você configurou um pipeline do Jenkins que verifica o código-fonte no repositório do GitHub, executa o Maven para compilar um arquivo war e, então, usa a CLI do Azure para implantar o Serviço de Aplicativo do Azure. Você aprendeu como:

> [!div class="checklist"]
> * Criar uma VM Jenkins
> * Configurar o Jenkins
> * Criar um aplicativo Web no Azure
> * Preparar um repositório GitHub
> * Criar pipeline do Jenkins
> * Executar o pipeline e verificar o aplicativo Web
