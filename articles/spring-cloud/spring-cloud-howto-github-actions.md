---
title: CI/CD do Azure Spring Cloud com ações do GitHub
description: Como criar um fluxo de trabalho de CI/CD para o Azure Spring Cloud com ações do GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 8400fcacbfa4c76aceb079b788255e3d3b83ce33
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210403"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>CI/CD do Azure Spring Cloud com ações do GitHub

As ações do GitHub dão suporte a um fluxo de trabalho de desenvolvimento de software automatizado. Com as ações do GitHub para o Azure Spring Cloud, você pode criar fluxos de trabalho em seu repositório para compilar, testar, empacotar, liberar e implantar no Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Este exemplo requer o [CLI do Azure](/cli/azure/install-azure-cli).

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>Configurar repositório do GitHub e autenticar
É necessária uma credencial de entidade de serviço do Azure para autorizar a ação de logon do Azure. Para obter uma credencial do Azure, execute os seguintes comandos em seu computador local:

```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```

Para acessar um grupo de recursos específico, você pode reduzir o escopo:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

O comando deve gerar um objeto JSON:

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Este exemplo usa o [exemplo steeltoe no GitHub](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample).  Bifurcar o repositório, abrir a página do repositório GitHub para a bifurcação e selecionar a guia **configurações** . Abra o menu **segredos** e selecione **novo segredo**:

 ![Adicionar novo segredo](./media/github-actions/actions1.png)

Defina o nome do segredo como `AZURE_CREDENTIALS` e seu valor para a cadeia de caracteres JSON que você encontrou no título *configurar seu repositório do GitHub e autenticar*.

 ![Definir dados secretos](./media/github-actions/actions2.png)

Você também pode obter as credenciais de logon do Azure de Key Vault em ações do GitHub, conforme explicado em [autenticar o Azure Spring com Key Vault em ações do GitHub](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Provisionar instância de serviço
Para provisionar sua instância do serviço de nuvem do Azure Spring, execute os comandos a seguir usando o CLI do Azure.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>Criar o fluxo de trabalho
O fluxo de trabalho é definido usando as opções a seguir.

### <a name="prepare-for-deployment-with-azure-cli"></a>Preparar para a implantação com o CLI do Azure

O comando `az spring-cloud app create` não é idempotente no momento. Depois de executá-lo uma vez, você receberá um erro se executar o mesmo comando novamente. Recomendamos esse fluxo de trabalho em instâncias e aplicativos existentes do Azure Spring Cloud.

Use os seguintes comandos de CLI do Azure para preparação:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Implantar com CLI do Azure diretamente

Crie o `.github/workflows/main.yml` arquivo no repositório com o conteúdo a seguir. Substitua `<your resource group name>` e `<your service name>` pelos valores corretos.

```yaml
name: Steeltoe-CD

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [ main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    env:
      working-directory: ./steeltoe-sample
      resource-group-name: <your resource group name>
      service-name: <your service name>
    
    # Supported .NET Core version matrix.
    strategy:
      matrix:
        dotnet: [ '3.1.x' ]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # Set up .NET Core 3.1 SDK
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ matrix.dotnet }}
          
      # Set credential for az login
      - uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: install Azure CLI extension
        run: |
          az extension add --name spring-cloud --yes
      
      - name: Build and package planet-weather-provider app
        working-directory: ${{env.working-directory}}/src/planet-weather-provider
        run: |
          dotnet publish
          az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
      - name: Build solar-system-weather app
        working-directory: ${{env.working-directory}}/src/solar-system-weather
        run: |
          dotnet publish
          az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
```
::: zone-end

::: zone pivot="programming-language-java"
## <a name="set-up-github-repository-and-authenticate"></a>Configurar repositório do GitHub e autenticar
É necessária uma credencial de entidade de serviço do Azure para autorizar a ação de logon do Azure. Para obter uma credencial do Azure, execute os seguintes comandos em seu computador local:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Para acessar um grupo de recursos específico, você pode reduzir o escopo:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
O comando deve gerar um objeto JSON:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Este exemplo usa o exemplo [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics) no github.  Bifurcar o exemplo, abrir a página do repositório do GitHub e clicar na guia **configurações** . Abra o menu **segredos** e clique em **Adicionar um novo segredo**:

 ![Adicionar novo segredo](./media/github-actions/actions1.png)

Defina o nome do segredo como `AZURE_CREDENTIALS` e seu valor para a cadeia de caracteres JSON que você encontrou no título *configurar seu repositório do GitHub e autenticar*.

 ![Definir dados secretos](./media/github-actions/actions2.png)

Você também pode obter as credenciais de logon do Azure de Key Vault em ações do GitHub, conforme explicado em [autenticar o Azure Spring com Key Vault em ações do GitHub](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Provisionar instância de serviço
Para provisionar sua instância do serviço de nuvem do Azure Spring, execute os comandos a seguir usando o CLI do Azure.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Criar o fluxo de trabalho
O fluxo de trabalho é definido usando as opções a seguir.

### <a name="prepare-for-deployment-with-azure-cli"></a>Preparar para a implantação com o CLI do Azure
O comando `az spring-cloud app create` não é idempotente no momento.  Recomendamos esse fluxo de trabalho em instâncias e aplicativos existentes do Azure Spring Cloud.

Use os seguintes comandos de CLI do Azure para preparação:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Implantar com CLI do Azure diretamente
Crie o `.github/workflow/main.yml` arquivo no repositório:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Implantar com ação de CLI do Azure
O `run` comando AZ usará a versão mais recente do CLI do Azure. Se houver alterações significativas, você também poderá usar uma versão específica do CLI do Azure com o Azure/CLI `action` . 

> [!Note] 
> Esse comando será executado em um novo contêiner, portanto, `env` não funcionará, e o acesso ao arquivo de ação cruzada poderá ter restrições extras.

Crie o arquivo. github/Workflow/Main. yml no repositório:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Implantar com o plug-in Maven
Outra opção é usar o [plug-in do Maven](./spring-cloud-quickstart.md) para implantar o JAR e atualizar as configurações do aplicativo. O comando `mvn azure-spring-cloud:deploy` é idempotente e criará aplicativos automaticamente, se necessário. Você não precisa criar aplicativos correspondentes com antecedência.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

::: zone-end

## <a name="run-the-workflow"></a>Executar o fluxo de trabalho

As **ações** do GitHub devem ser habilitadas automaticamente depois que você enviar por push `.github/workflow/main.yml` para o github. A ação será disparada quando você enviar por push uma nova confirmação. Se você criar esse arquivo no navegador, sua ação já deverá ter sido executada.

Para verificar se a ação foi habilitada, clique na guia **ações** na página repositório do github:

![Verificar ação habilitada](./media/github-actions/actions3.png)

Se a ação for executada com erro, por exemplo, se você não tiver definido a credencial do Azure, poderá executar novamente as verificações depois de corrigir o erro. Na página repositório do GitHub, clique em **ações**, selecione a tarefa de fluxo de trabalho específica e clique no botão **executar verificações** novamente para executar novamente as verificações:

![Executar verificações novamente](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Próximas etapas

* [Key Vault para ações do GitHub do Spring Cloud](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory entidades de serviço](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)
* [GitHub Actions para Azure](https://github.com/Azure/actions/)
