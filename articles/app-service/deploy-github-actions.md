---
title: Configurar a CI/CD com o GitHub Actions
description: Saiba como implantar seu código no Serviço de Aplicativo do Azure por meio de um pipeline de CI/CD com o GitHub Actions. Personalize as tarefas de build e execute implantações complexas.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 59eb56dd188edf258c3631cde957c0864454ad76
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762666"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Implantação no Serviço de Aplicativo usando o GitHub Actions

Introdução às [ações do GitHub](https://docs.github.com/en/actions/learn-github-actions) para automatizar o fluxo de trabalho e implantá-lo no [serviço Azure app](overview.md) do github. 

## <a name="prerequisites"></a>Pré-requisitos 

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma conta do GitHub. Caso ainda não tenha uma, inscreva-se [gratuitamente](https://github.com/join).  
- Um aplicativo de serviço de Azure App de trabalho. 
    - .NET: [criar um aplicativo web ASP.NET Core no Azure](quickstart-dotnetcore.md)
    - ASP.NET: [criar um aplicativo Web do ASP.NET Framework no Azure](quickstart-dotnet-framework.md)
    - JavaScript: [criar um aplicativo web Node.js no serviço Azure app](quickstart-nodejs.md)  
    - Java: [criar um aplicativo Java no serviço Azure app](quickstart-java.md)
    - Python: [criar um aplicativo Python no serviço Azure app](quickstart-python.md)

## <a name="workflow-file-overview"></a>Visão geral do arquivo do fluxo de trabalho

Um fluxo de trabalho é definido por um arquivo YAML (.yml) no caminho `/.github/workflows/` no repositório. Essa definição contém as várias etapas e os parâmetros que compõem o fluxo de trabalho.

O arquivo tem três seções:

|Seção  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. definir uma entidade de serviço ou um perfil de publicação. <br /> 2. Criar um segredo do GitHub. |
|**Build** | 1. configurar o ambiente. <br /> 2. Compile o aplicativo Web. |
|**Implantar** | 1. implante o aplicativo Web. |

## <a name="use-the-deployment-center"></a>Usar a central de implantação

Você pode começar rapidamente com as ações do GitHub usando o centro de implantação do serviço de aplicativo. Isso gerará automaticamente um arquivo de fluxo de trabalho com base em sua pilha de aplicativos e o confirmará no repositório do GitHub no diretório correto.

1. Navegue até seu webapp no portal do Azure
1. No lado esquerdo, clique em **central de implantação**
1. Em **implantação contínua (CI/CD)**, selecione **GitHub**
1. Em seguida, selecione **ações do GitHub**
1. Use os menus suspensos para selecionar o repositório, a ramificação e a pilha de aplicativos do GitHub
    - Se a ramificação selecionada estiver protegida, você ainda poderá continuar a adicionar o arquivo de fluxo de trabalho. Certifique-se de examinar suas proteções de Branch antes de continuar.
1. Na tela final, você pode revisar suas seleções e visualizar o arquivo de fluxo de trabalho que será confirmado no repositório. Se as seleções estiverem corretas, clique em **concluir**

Isso confirmará o arquivo de fluxo de trabalho para o repositório. O fluxo de trabalho para compilar e implantar seu aplicativo será iniciado imediatamente.

## <a name="set-up-a-workflow-manually"></a>Configurar um fluxo de trabalho manualmente

Você também pode implantar um fluxo de trabalho sem usar a central de implantação. Para fazer isso, você precisará primeiro gerar as credenciais de implantação. 

## <a name="generate-deployment-credentials"></a>Gerar as credenciais de implantação

A maneira recomendada de autenticar com os serviços Azure App para ações do GitHub é com um perfil de publicação. Você também pode autenticar com uma entidade de serviço, mas o processo requer mais etapas. 

Salve a credencial de perfil de publicação ou a entidade de serviço como um [segredo do GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets) para autenticar com o Azure. Você acessará o segredo em seu fluxo de trabalho. 

# <a name="publish-profile"></a>[Perfil de publicação](#tab/applevel)

Um perfil de publicação é uma credencial no nível do aplicativo. Configure seu perfil de publicação como um segredo do GitHub. 

1. Vá para o serviço de aplicativo no portal do Azure. 

1. Na página **visão geral** , selecione **obter perfil de publicação**.

1. Salve o arquivo baixado. Você usará o conteúdo do arquivo para criar um segredo do GitHub.

> [!NOTE]
> A partir de outubro de 2020, os aplicativos Web do Linux precisarão da configuração do aplicativo `WEBSITE_WEBDEPLOY_USE_SCM` definida como `true` **antes de baixar o perfil de publicação**. Esse requisito será removido no futuro.

# <a name="service-principal"></a>[Entidade de serviço](#tab/userlevel)

Crie uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) com o comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) na [CLI do Azure](/cli/azure/). Execute esse comando com o [Azure Cloud Shell](https://shell.azure.com/) no portal do Azure ou selecionando o botão **Experimentar**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

No exemplo acima, substitua os espaços reservados pela sua ID de assinatura, pelo nome do grupo de recursos e pelo nome do aplicativo. A saída é um objeto JSON com as credenciais de atribuição de função que fornecem acesso ao aplicativo do Serviço de Aplicativo semelhante ao mostrado abaixo. Copie esse objeto JSON para uso posterior.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> É sempre uma boa prática permitir acesso mínimo. O escopo no exemplo anterior é limitado ao aplicativo do serviço de aplicativo específico e não ao grupo de recursos inteiro.

---

## <a name="configure-the-github-secret"></a>Configurar o segredo do GitHub


# <a name="publish-profile"></a>[Perfil de publicação](#tab/applevel)

No [GitHub](https://github.com/), procure seu repositório, selecione **configurações > segredos > adicionar um novo segredo**.

Para usar [credenciais de nível de aplicativo](#generate-deployment-credentials), Cole o conteúdo do arquivo de perfil de publicação baixado no campo valor do segredo. Nomeie o segredo `AZURE_WEBAPP_PUBLISH_PROFILE` .

Ao configurar o fluxo de trabalho do GitHub, você usa o `AZURE_WEBAPP_PUBLISH_PROFILE` na ação implantar aplicativo Web do Azure. Por exemplo:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Entidade de serviço](#tab/userlevel)

No [GitHub](https://github.com/), procure seu repositório, selecione **configurações > segredos > adicionar um novo segredo**.

Para usar [credenciais de nível de usuário](#generate-deployment-credentials), Cole toda a saída JSON do comando CLI do Azure no campo valor do segredo. Dê ao segredo o nome `AZURE_CREDENTIALS`.

Ao configurar o arquivo de fluxo de trabalho posteriormente, você usa o segredo para o `creds` de entrada da ação de Logon do Azure. Por exemplo:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>Configurar o ambiente

A configuração do ambiente pode ser feita por meio de uma das ações de instalação.

|**Idioma**  |**Ação de instalação**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Os exemplos a seguir mostram como configurar o ambiente para os diferentes idiomas com suporte:

**.NET**

```yaml
    - name: Setup Dotnet 3.3.x
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.3.x'
```

**ASP.NET**

```yaml
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x,
        # change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

**JavaScript**

```yaml
env:
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
```
**Python**

```yaml
    - name: Setup Python 3.x 
      uses: actions/setup-python@v1
      with:
        python-version: 3.x
```

## <a name="build-the-web-app"></a>Criar o aplicativo Web

O processo de criação de um aplicativo Web e implantação em Azure App serviço é alterado de acordo com o idioma. 

Os exemplos a seguir mostram a parte do fluxo de trabalho que cria o aplicativo Web, em diferentes idiomas com suporte.

Para todos os idiomas, você pode definir o diretório raiz do aplicativo Web com `working-directory` . 

**.NET**

A variável de ambiente `AZURE_WEBAPP_PACKAGE_PATH` define o caminho para seu projeto de aplicativo Web. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

Você pode restaurar as dependências do NuGet e executar o MSBuild com `run` . 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.0

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

Por Node.js, você pode definir `working-directory` ou alterar para o diretório NPM no `pushd` . 

```yaml
- name: npm install, build, and test
  run: |
    npm install
    npm run build --if-present
    npm run test --if-present
  working-directory: my-app-folder # set to the folder with your app if it is not the root directory
```

**Python**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```


## <a name="deploy-to-app-service"></a>Implantar no Serviço de Aplicativo

Para implantar seu código em um aplicativo do Serviço de Aplicativo, use a ação `azure/webapps-deploy@v2`. Essa ação tem quatro parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **app-name** | (Obrigatório) Nome do aplicativo do Serviço de Aplicativo | 
| **publish-profile** | (Opcional) Publique o conteúdo do arquivo de perfil com os segredos da Implantação da Web |
| **package** | (Opcional) Caminho para o pacote ou a pasta. O caminho pode incluir *. zip, *. War, *. jar ou uma pasta a ser implantada |
| **slot-name** | Adicional Insira um slot existente que não seja o [slot](deploy-staging-slots.md) de produção |


# <a name="publish-profile"></a>[Perfil de publicação](#tab/applevel)

### <a name="net-core"></a>.NET Core

Crie e implante um aplicativo .NET Core no Azure usando um perfil de publicação do Azure. A `publish-profile` entrada faz referência ao `AZURE_WEBAPP_PUBLISH_PROFILE` segredo que você criou anteriormente.

```yaml
name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

### <a name="aspnet"></a>ASP.NET

Crie e implante um aplicativo MVC ASP.NET que usa o NuGet e `publish-profile` para autenticação. 


```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    - uses: actions/checkout@main  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
```

### <a name="java"></a>Java

Crie e implante um aplicativo Spring Java no Azure usando um perfil de publicação do Azure. A `publish-profile` entrada faz referência ao `AZURE_WEBAPP_PUBLISH_PROFILE` segredo que você criou anteriormente.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: my-app-path
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.jar
```

Para implantar um `war` em vez de um `jar` , altere o `package` valor. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

Crie e implante um aplicativo Node.js no Azure usando o perfil de publicação do aplicativo. A `publish-profile` entrada faz referência ao `AZURE_WEBAPP_PUBLISH_PROFILE` segredo que você criou anteriormente.

```yaml
# File: .github/workflows/workflow.yml
name: JavaScript CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name   # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: 'my-app-path'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory: my-app-path
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

### <a name="python"></a>Python 

Crie e implante um aplicativo Python no Azure usando o perfil de publicação do aplicativo. Observe como a `publish-profile` entrada faz referência ao `AZURE_WEBAPP_PUBLISH_PROFILE` segredo que você criou anteriormente.

```yaml
name: Python CI

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-web-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Building web app
      uses: azure/appservice-build@v2
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

# <a name="service-principal"></a>[Entidade de serviço](#tab/userlevel)

### <a name="net-core"></a>.NET Core 

Crie e implante um aplicativo .NET Core no Azure usando uma entidade de serviço do Azure. Observe como a `creds` entrada faz referência ao `AZURE_CREDENTIALS` segredo que você criou anteriormente.


```yaml
name: .NET Core

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
      
      - name: logout
        run: |
          az logout
```

### <a name="aspnet"></a>ASP.NET

Crie e implante um aplicativo MVC ASP.NET no Azure usando uma entidade de serviço do Azure. Observe como a `creds` entrada faz referência ao `AZURE_CREDENTIALS` segredo que você criou anteriormente.

```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    # checkout the repo
    - uses: actions/checkout@main
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
  
    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="java"></a>Java 

Crie e implante um aplicativo Spring Java no Azure usando uma entidade de serviço do Azure. Observe como a `creds` entrada faz referência ao `AZURE_CREDENTIALS` segredo que você criou anteriormente.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: complete
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        package: my/target/*.jar

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="javascript"></a>JavaScript 

Crie e implante um aplicativo Node.js no Azure usando uma entidade de serviço do Azure. Observe como a `creds` entrada faz referência ao `AZURE_CREDENTIALS` segredo que você criou anteriormente.

```yaml
name: JavaScript CI

on: [push]

name: Node.js

env:
  AZURE_WEBAPP_NAME: my-app   # set this to your application's name
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@main
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory:  my-app-path
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="python"></a>Python 

Crie e implante um aplicativo Python no Azure usando uma entidade de serviço do Azure. Observe como a `creds` entrada faz referência ao `AZURE_CREDENTIALS` segredo que você criou anteriormente.

```yaml
name: Python application

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
    - name: logout
      run: |
        az logout
```


---

## <a name="next-steps"></a>Próximas etapas

Encontre nosso conjunto de ações agrupadas em diferentes repositórios no GitHub, cada um contendo documentação e exemplos para ajudar você a usar o GitHub para CI/CD e implantar seus aplicativos no Azure.

- [Fluxos de trabalho de ações a serem implantados no Azure](https://github.com/Azure/actions-workflow-samples)

- [Logon do Azure](https://github.com/Azure/login)

- [Aplicativo Web do Azure](https://github.com/Azure/webapps-deploy)

- [Aplicativo Web do Azure para contêineres](https://github.com/Azure/webapps-container-deploy)

- [Logon/logoff do Docker](https://github.com/Azure/docker-login)

- [Eventos que disparam fluxos de trabalho](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [Implantação do K8s](https://github.com/Azure/k8s-deploy)

- [Fluxos de trabalho iniciais](https://github.com/actions/starter-workflows)
