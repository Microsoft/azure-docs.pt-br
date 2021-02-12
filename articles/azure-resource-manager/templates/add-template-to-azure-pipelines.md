---
title: CI/CD com Azure Pipelines e modelos
description: Descreve como configurar a integração contínua no Azure Pipelines usando modelos de Azure Resource Manager. Ele mostra como usar um script do PowerShell ou copiar arquivos para um local de preparo e implantá-los a partir daí.
ms.topic: conceptual
ms.date: 02/05/2021
ms.openlocfilehash: ea1ccac00f121bd81fd8b9b1f182b565fc53d214
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594190"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integrar modelos do Resource Manager com Azure Pipelines

Você pode integrar modelos de Azure Resource Manager (modelos ARM) com Azure Pipelines para integração contínua e implantação contínua (CI/CD). Neste artigo, você aprende duas maneiras mais avançadas de implantar modelos com Azure Pipelines.

## <a name="select-your-option"></a>Selecione sua opção

Antes de prosseguir com este artigo, vamos considerar as diferentes opções para implantar um modelo ARM de um pipeline.

* **Use a tarefa de implantação de modelo ARM**. Essa opção é a opção mais fácil. Essa abordagem funciona quando você deseja implantar um modelo diretamente de um repositório. Essa opção não é abordada neste artigo, mas, em vez disso, é abordada no tutorial [integração contínua de modelos ARM com Azure pipelines](deployment-tutorial-pipeline.md). Ele mostra como usar a [tarefa de implantação de modelo ARM](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) para implantar um modelo do seu repositório github.

* **Adicionar tarefa que executa um script de Azure PowerShell**. Essa opção tem a vantagem de fornecer consistência em todo o ciclo de vida de desenvolvimento, pois você pode usar o mesmo script que usou ao executar testes locais. O script implanta o modelo, mas também pode executar outras operações, como obter valores para usar como parâmetros. Essa opção é mostrada neste artigo. Consulte [Azure PowerShell tarefa](#azure-powershell-task).

   O Visual Studio fornece o [projeto do grupo de recursos do Azure](create-visual-studio-deployment-project.md) que inclui um script do PowerShell. O script prepara os artefatos do seu projeto para uma conta de armazenamento que o Gerenciador de recursos pode acessar. Os artefatos são itens em seu projeto, como modelos vinculados, scripts e binários de aplicativos. Se você quiser continuar usando o script do projeto, use a tarefa Script do PowerShell mostrada neste artigo.

* **Adicionar tarefas para copiar e implantar tarefas**. Essa opção oferece uma alternativa conveniente ao script do projeto. Você configura duas tarefas no pipeline. Uma tarefa prepara os artefatos para um local acessível. A outra tarefa implanta o modelo desse local. Essa opção é mostrada neste artigo. Consulte [copiar e implantar tarefas](#copy-and-deploy-tasks).

## <a name="prepare-your-project"></a>Preparar seu projeto

Este artigo pressupõe que o modelo do ARM e a organização de DevOps do Azure estão prontos para criar o pipeline. As etapas a seguir mostram como se certificar de que você está pronto:

* Você tem uma organização de DevOps do Azure. Caso não tenha uma, [crie uma gratuitamente](/azure/devops/pipelines/get-started/pipelines-sign-up). Se sua equipe já tiver uma organização de DevOps do Azure, verifique se você é um administrador do projeto DevOps do Azure que deseja usar.

* Você configurou uma [conexão de serviço](/azure/devops/pipelines/library/connect-to-azure) para sua assinatura do Azure. As tarefas no pipeline são executadas sob a identidade da entidade de serviço. Para obter as etapas para criar a conexão, consulte [criar um projeto DevOps](deployment-tutorial-pipeline.md#create-a-devops-project).

* Você tem um [modelo ARM](quickstart-create-templates-use-visual-studio-code.md) que define a infraestrutura para seu projeto.

## <a name="create-pipeline"></a>Criar um pipeline

1. Se você ainda não adicionou um pipeline anteriormente, precisará criar um novo pipeline. Na organização do Azure DevOps, selecione **pipelines** e **novo pipeline**.

   ![Adicionar novo pipeline](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Especifique onde o código está armazenado. A imagem a seguir mostra a seleção de **Azure Repos git**.

   ![Selecionar fonte de código](./media/add-template-to-azure-pipelines/select-source.png)

1. Nessa fonte, selecione o repositório que tem o código para seu projeto.

   ![Selecionar repositório](./media/add-template-to-azure-pipelines/select-repo.png)

1. Selecione o tipo de pipeline a ser criado. Você pode selecionar **pipeline de início**.

   ![Selecionar pipeline](./media/add-template-to-azure-pipelines/select-pipeline.png)

Você está pronto para adicionar uma tarefa Azure PowerShell ou copiar arquivo e implantar tarefas.

## <a name="azure-powershell-task"></a>Tarefa do Azure PowerShell

Esta seção mostra como configurar a implantação contínua usando uma única tarefa que executa o script do PowerShell em seu projeto. Se você precisar de um script do PowerShell que implanta um modelo, consulte [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) ou [Deploy-AzureResourceGroup.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzureResourceGroup.ps1).

O seguinte arquivo YAML cria uma [tarefa Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell):

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzurePowerShell@5
  inputs:
    azureSubscription: 'script-connection'
    ScriptType: 'FilePath'
    ScriptPath: './Deploy-AzTemplate.ps1'
    ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
    azurePowerShellVersion: 'LatestVersion'
```

Quando você define a tarefa como `AzurePowerShell@5` , o pipeline usa o [módulo AZ](/powershell/azure/new-azureps-module-az). Se você estiver usando o módulo AzureRM em seu script, defina a tarefa como `AzurePowerShell@3` .

```yaml
steps:
- task: AzurePowerShell@3
```

Para `azureSubscription` , forneça o nome da conexão de serviço que você criou.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Para `scriptPath` , forneça o caminho relativo do arquivo de pipeline para o script. Você pode procurar no repositório para ver o caminho.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

No `ScriptArguments` , forneça todos os parâmetros necessários para o script. O exemplo a seguir mostra alguns parâmetros para um script, mas você precisará personalizar os parâmetros para o script.

```yaml
ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
```

Quando você seleciona **salvar**, o pipeline de compilação é executado automaticamente. Volte para o resumo do pipeline de compilação e veja o status.

![Exibir os resultados](./media/add-template-to-azure-pipelines/view-results.png)

Você pode selecionar o pipeline em execução no momento para ver detalhes sobre as tarefas. Quando ele for concluído, você verá os resultados de cada etapa.

## <a name="copy-and-deploy-tasks"></a>Copiar e implantar tarefas

Esta seção mostra como configurar a implantação contínua usando duas tarefas. A primeira tarefa prepara os artefatos para uma conta de armazenamento e a segunda tarefa implanta o modelo.

Para copiar arquivos para uma conta de armazenamento, a entidade de serviço para a conexão de serviço deve ser atribuída à função de proprietário de dados de blob de armazenamento ou de data blob de armazenamento. Para obter mais informações, consulte Introdução [ao AzCopy](../../storage/common/storage-use-azcopy-v10.md).

O YAML a seguir mostra a [tarefa cópia de arquivo do Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy).

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
```

Há várias partes dessa tarefa a serem revisadas para o seu ambiente. O `SourcePath` indica o local dos artefatos em relação ao arquivo de pipeline.

```yaml
SourcePath: '<path-to-artifacts>'
```

Para `azureSubscription` , forneça o nome da conexão de serviço que você criou.

```yaml
azureSubscription: '<your-connection-name>'
```

Para o armazenamento e o nome do contêiner, forneça os nomes da conta de armazenamento e do contêiner que você deseja usar para armazenar os artefatos. A conta de armazenamento deve existir.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Depois de criar a tarefa Copiar arquivo, você estará pronto para adicionar a tarefa para implantar o modelo preparado.

O YAML a seguir mostra a [tarefa de implantação de modelo de Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md):

```yaml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Há várias partes dessa tarefa a serem examinadas mais detalhadamente.

- `deploymentScope`: Selecione o escopo de implantação nas opções: `Management Group` , `Subscription` e `Resource Group` . Para saber mais sobre os escopos, confira [Escopos de implantação](deploy-rest.md#deployment-scope).

- `azureResourceManagerConnection`: Forneça o nome da conexão de serviço que você criou.

- `subscriptionId`: Forneça a ID da assinatura de destino. Essa propriedade só se aplica ao escopo de implantação do grupo de recursos e ao escopo de implantação da assinatura.

- `resourceGroupName` e `location` : forneça o nome e o local do grupo de recursos no qual você deseja implantar. A tarefa cria o grupo de recursos, caso ele não exista.

   ```yml
   resourceGroupName: '<resource-group-name>'
   location: '<location>'
   ```

- `csmFileLink`: Forneça o link para o modelo de preparo. Ao definir o valor, use variáveis retornadas da tarefa de cópia de arquivo. O exemplo a seguir contém links para um modelo chamado mainTemplate.jsem. A pasta denominada **modelos** está incluída porque a tarefa de cópia de arquivo copiou o arquivo. Em seu pipeline, forneça o caminho para o modelo e o nome do seu modelo.

   ```yml
   csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
   ```

Seu pipeline se parece com:

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Quando você seleciona **salvar**, o pipeline de compilação é executado automaticamente. Volte para o resumo do pipeline de compilação e veja o status.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar modelos ARM com ações do GitHub, consulte [implantar modelos de Azure Resource Manager usando ações do GitHub](deploy-github-actions.md).
