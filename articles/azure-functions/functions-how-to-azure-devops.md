---
title: Fornecer continuamente função atualizações de código por meio do Azure DevOps - Azure Functions
description: Saiba como configurar um pipeline de DevOps do Azure que tem como alvo o Azure Functions.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 0fdad0caa2deef0d7d55b30a85632f72f4ff0ecc
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594468"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Entrega contínua com o uso de DevOps do Azure

Você pode implantar automaticamente sua função a um aplicativo do Azure Functions usando [Pipelines do Azure](/azure/devops/pipelines/).

Você tem duas opções para definir seu pipeline:

- **Arquivo YAML**: Um arquivo YAML descreve o pipeline. O arquivo pode ter uma seção de etapas de compilação e uma seção de versão. O arquivo YAML deve estar no mesmo repositório que o aplicativo.
- **Modelo**: Os modelos são tarefas prontas que compilar ou implantar seu aplicativo.

## <a name="yaml-based-pipeline"></a>Pipeline com base em YAML

Para criar um pipeline com base em YAML, primeiro crie o seu aplicativo e, em seguida, implante o aplicativo.

### <a name="build-your-app"></a>Compilar seu aplicativo

Como você pode compilar seu aplicativo em Pipelines do Azure depende da linguagem de programação do seu aplicativo. Cada linguagem tem etapas de compilação específica que criar um artefato de implantação. Um artefato de implantação é usado para implantar seu aplicativo de funções no Azure.

#### <a name="net"></a>.NET

Você pode usar o exemplo a seguir para criar um arquivo YAML para criar um aplicativo .NET:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: true
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

Você pode usar o exemplo a seguir para criar um arquivo YAML para criar um aplicativo JavaScript:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="python"></a>Python

Você pode usar o exemplo a seguir para criar um arquivo YAML para criar um aplicativo do Python. Python tem suporte somente para Linux do Azure Functions.

```yaml
pool:
      vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```
#### <a name="powershell"></a>PowerShell

Você pode usar o exemplo a seguir para criar um arquivo YAML para empacotar um aplicativo PowerShell. PowerShell tem suporte apenas para o Windows Azure Functions.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

### <a name="deploy-your-app"></a>Implantar seu aplicativo

Você deve incluir um dos seguintes exemplos YAML no arquivo YAML, dependendo do sistema operacional host.

#### <a name="windows-function-app"></a>Aplicativo de funções do Windows

Você pode usar o trecho a seguir para implantar um aplicativo de funções do Windows:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Aplicativo de funções do Linux

Você pode usar o trecho a seguir para implantar um aplicativo de funções do Linux:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Com base no modelo de pipeline

Os modelos no DevOps do Azure são grupos predefinidos de tarefas que compilar ou implantar um aplicativo.

### <a name="build-your-app"></a>Compilar seu aplicativo

Como você pode compilar seu aplicativo em Pipelines do Azure depende da linguagem de programação do seu aplicativo. Cada linguagem tem etapas de compilação específica que criar um artefato de implantação. Um artefato de implantação é usado para atualizar seu aplicativo de funções no Azure.

Para usar os modelos de build internas, quando você cria um novo pipeline de compilação, selecione **usar o editor clássico** para criar um pipeline usando modelos do designer.

![Selecione o editor de Pipelines do Azure clássico](media/functions-how-to-azure-devops/classic-editor.png)

Depois de configurar a origem do seu código, pesquisa do Azure Functions modelos de compilação. Selecione o modelo que corresponda ao idioma do seu aplicativo.

![Selecione um modelo de compilação do Azure Functions](media/functions-how-to-azure-devops/build-templates.png)

Em alguns casos, os artefatos de compilação têm uma estrutura de pasta específica. Talvez você precise selecionar o **Prepend nome da pasta raiz para arquivar caminhos** caixa de seleção.

![A opção para preceder o nome da pasta raiz](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Aplicativos de JavaScript

Se seu aplicativo JavaScript tem uma dependência em módulos nativos do Windows, você deverá atualizar a versão do pool de agente para **VS2017 hospedado**.

![Atualizar a versão do pool de agente](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Implantar seu aplicativo

Quando você cria um novo pipeline de lançamento, procure o modelo de versão do Azure Functions.

![Procure o modelo de versão do Azure Functions](media/functions-how-to-azure-devops/release-template.png)

Não há suporte para a implantação em um slot de implantação no modelo de versão.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Criar um pipeline de build usando a CLI do Azure

Para criar um pipeline de compilação no Azure, use o `az functionapp devops-pipeline create` [comando](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). O pipeline de compilação é criado para compilar e liberar quaisquer alterações de código que são feitas em seu repositório. O comando gera um novo arquivo YAML que define o pipeline de build e versão e, em seguida, confirma-lo ao seu repositório. Os pré-requisitos para esse comando dependem do local do seu código.

- Se seu código está no GitHub:

    - Você deve ter **gravar** permissões para sua assinatura.

    - Você deve ser o administrador de projeto de DevOps do Azure.

    - Você deve ter permissões para criar um token de acesso pessoal do GitHub (PAT) que tenha permissões suficientes. Para obter mais informações, consulte [requisitos de permissão PAT do GitHub.](https://aka.ms/azure-devops-source-repos)

    - Você deve ter permissões para confirmar para o branch mestre no seu repositório do GitHub, portanto, você poderá confirmar o arquivo YAML gerado automaticamente.

- Se seu código é em repositórios do Azure:

    - Você deve ter **gravar** permissões para sua assinatura.

    - Você deve ser o administrador de projeto de DevOps do Azure.

## <a name="next-steps"></a>Próximas etapas

- Examine os [visão geral do Azure Functions](functions-overview.md).
- Examine os [visão geral de DevOps do Azure](/azure/devops/pipelines/).
