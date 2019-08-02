---
title: CI/CD com Pipelines do Azure e Gerenciador de recursos de modelos
description: Descreve como configurar a integração contínua em Pipelines do Azure usando projetos de implantação do grupo de recursos do Azure no Visual Studio para implantar modelos do Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: article
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: b70b38904c0373c53c3731dd0442511116d9c4de
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191454"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Integrar modelos do Resource Manager com Pipelines do Azure

O Visual Studio fornece o projeto do grupo de recursos do Azure para criar modelos e implantá-los para sua assinatura do Azure. Você pode integrar esse projeto com os Pipelines do Azure para integração contínua e implantação contínua (CI/CD).

Há duas maneiras de implantar modelos com os Pipelines do Azure:

* **Adicionar tarefa que executa um script do PowerShell do Azure**. Essa opção tem a vantagem de fornecer consistência em todo o ciclo de vida de desenvolvimento, porque você usa o mesmo script que está incluído no projeto do Visual Studio (Deploy-AzureResourceGroup.ps1). Os artefatos de estágios de script do seu projeto para uma conta de armazenamento do Resource Manager podem acessar. Artefatos são itens em seu projeto, como modelos vinculados, scripts e binários do aplicativo. Em seguida, o script implanta o modelo.

* **Adicionar tarefas para copiar e implantar tarefas**. Essa opção oferece uma alternativa conveniente para o script do projeto. Você pode configurar duas tarefas no pipeline. Uma tarefa prepara os artefatos e a outra tarefa implanta o modelo.

Este artigo mostra as duas abordagens.

## <a name="prepare-your-project"></a>Preparar seu projeto

Este artigo pressupõe que seu projeto do Visual Studio e a organização de DevOps do Azure estão prontos para criar o pipeline. As etapas a seguir mostram como se certificar de que você está pronto:

* Você tem uma organização de DevOps do Azure. Se você não tiver uma, [criar uma gratuitamente](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Se sua equipe já tem uma organização de DevOps do Azure, verifique se que você for um administrador do projeto de DevOps do Azure que você deseja usar.

* Você configurou uma [conexão de serviço](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) sua assinatura do Azure. As tarefas no pipeline executam sob a identidade da entidade de serviço. Para obter as etapas criar a conexão, consulte [criar um projeto de DevOps](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Você tem um projeto do Visual Studio que foi criado a partir de **grupo de recursos do Azure** modelo inicial. Para obter informações sobre como criar esse tipo de projeto, consulte [criando e implantando grupos de recursos do Azure por meio do Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

* Seu projeto do Visual Studio está [conectado a um projeto de DevOps do Azure](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Criar um pipeline

1. Se você ainda não adicionou um pipeline anteriormente, você precisa criar um novo pipeline. Na sua organização de DevOps do Azure, selecione **Pipelines** e **novo pipeline**.

   ![Adicionar novo pipeline](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. Especifique onde seu código estiver armazenado. A imagem a seguir mostra como selecionar **Azure repositórios Git**.

   ![Selecione o código-fonte](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. Dessa fonte, selecione o repositório que tem o código para seu projeto.

   ![Selecionar repositório](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. Selecione o tipo de pipeline para criar. Você pode selecionar **pipeline Starter**.

   ![Selecione o pipeline](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

Você está pronto para adicionar uma tarefa do Azure PowerShell ou o arquivo de cópia e implantar tarefas.

## <a name="azure-powershell-task"></a>Tarefa do PowerShell do Azure

Esta seção mostra como configurar a implantação contínua por meio de uma única tarefa que executa o script do PowerShell em seu projeto. O arquivo YAML a seguir cria uma [tarefa do Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' 
    azurePowerShellVersion: LatestVersion
```

Quando você define a tarefa como `AzurePowerShell@3`, o pipeline usa comandos do módulo AzureRM para autenticar a conexão. Por padrão, o script do PowerShell no projeto do Visual Studio usa o módulo AzureRM. Se você tiver atualizado seu script para usar o [módulo Az](/powershell/azure/new-azureps-module-az), defina a tarefa como `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

Para `azureSubscription`, forneça o nome da conexão de serviço que você criou.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Para `scriptPath`, forneça o caminho relativo do arquivo de pipeline para o seu script. Você pode procurar em seu repositório para ver o caminho.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Se você não precisar para artefatos de preparação, basta passe o nome e local de um grupo de recursos a serem usadas na implantação. O script do Visual Studio cria o grupo de recursos se ele ainda não existir.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Se você precisar de artefatos de preparação para uma conta de armazenamento existente, use:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Agora, que saiba como criar a tarefa, vamos percorrer as etapas para editar o pipeline.

1. Abra seu pipeline e substitua o conteúdo pelo seu YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. Clique em **Salvar**.

   ![Salve o pipeline](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. Forneça uma mensagem para a confirmação e realizar confirmação diretamente **mestre**.

1. Quando você seleciona **salvar**, o pipeline de compilação é executado automaticamente. Volte para o resumo do seu pipeline de build e observar o status.

   ![Exibir resultados](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Você pode selecionar o pipeline em execução no momento para ver detalhes sobre as tarefas. Ao terminar, você pode ver os resultados para cada etapa.

## <a name="copy-and-deploy-tasks"></a>Copie e implantar tarefas

Esta seção mostra como configurar a implantação contínua, usando um duas tarefas para preparar os artefatos e implantar o modelo. 

O YAML a seguir mostra a [tarefa de cópia de arquivos do Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

Há várias partes dessa tarefa para revisar seu ambiente. O `SourcePath` indica o local dos artefatos relativo ao arquivo de pipeline. Neste exemplo, os arquivos existem em uma pasta chamada `AzureResourceGroup1` qual era o nome do projeto.

```yaml
SourcePath: '<path-to-artifacts>'
```

Para `azureSubscription`, forneça o nome da conexão de serviço que você criou.

```yaml
azureSubscription: '<your-connection-name>'
```

Para o nome do contêiner e o armazenamento, forneça os nomes de conta de armazenamento e contêiner que você deseja usar para armazenar os artefatos. A conta de armazenamento deve existir.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

O YAML a seguir mostra a [tarefa de implantação do grupo de recursos do Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    azureSubscription: 'demo-deploy-sp'
    resourceGroupName: 'demogroup'
    location: 'centralus'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
```

Há várias partes dessa tarefa para revisar seu ambiente. Para `azureSubscription`, forneça o nome da conexão de serviço que você criou.

```yaml
azureSubscription: '<your-connection-name>'
```

Para `resourceGroupName` e `location`, forneça o nome e o local do grupo de recursos que você deseja implantar. A tarefa cria o grupo de recursos se ele não existir.

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

Os links de tarefa de implantação para um modelo denominado `WebSite.json` e um arquivo de parâmetros chamado WebSite.parameters.json. Use os nomes dos seus arquivos de modelo e parâmetro.

Agora, que saiba como criar as tarefas, vamos percorrer as etapas para editar o pipeline.

1. Abra seu pipeline e substitua o conteúdo pelo seu YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
   - task: AzureResourceGroupDeployment@2
     displayName: 'Deploy template'
     inputs:
       azureSubscription: 'demo-deploy-sp'
       resourceGroupName: demogroup
       location: 'centralus'
       templateLocation: 'URL of the file'
       csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
       csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
       overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
   ```

1. Clique em **Salvar**.

1. Forneça uma mensagem para a confirmação e realizar confirmação diretamente **mestre**.

1. Quando você seleciona **salvar**, o pipeline de compilação é executado automaticamente. Volte para o resumo do seu pipeline de build e observar o status.

   ![Exibir resultados](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Você pode selecionar o pipeline em execução no momento para ver detalhes sobre as tarefas. Ao terminar, você pode ver os resultados para cada etapa.

## <a name="next-steps"></a>Próximas etapas

Para o processo passo a passo sobre como usar Pipelines do Azure com modelos do Resource Manager, consulte [Tutorial: A integração contínua de modelos do Azure Resource Manager com Pipelines do Azure](resource-manager-tutorial-use-azure-pipelines.md).
