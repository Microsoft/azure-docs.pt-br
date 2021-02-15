---
title: DevOps para um pipeline de ingestão de dados
titleSuffix: Azure Machine Learning
description: Saiba como aplicar práticas de DevOps para criar um pipeline de ingestão de dados para preparar dados usando Azure Data Factory e Azure Databricks.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: fe2f35708f6a148f8db9ef6fd0a598e19e746fbd
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358619"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps para um pipeline de ingestão de dados

Na maioria dos cenários, uma solução de ingestão de dados é uma composição de scripts, invocações de serviço e um pipeline que orquestra todas as atividades. Neste artigo, você aprende a aplicar as práticas de DevOps ao ciclo de vida de desenvolvimento de um pipeline de ingestão de dados comum que prepara dados para treinamento de modelo de aprendizado de máquina. O pipeline é criado usando os seguintes serviços do Azure:

* __Azure data Factory__ : lê os dados brutos e orquestra a preparação de dados.
* __Azure Databricks__ : executa um bloco de anotações do Python que transforma os dados.
* __Azure pipelines__ : automatiza um processo contínuo de integração e desenvolvimento.

## <a name="data-ingestion-pipeline-workflow"></a>Fluxo de trabalho do pipeline de ingestão de dados

O pipeline de ingestão de dados implementa o seguinte fluxo de trabalho:

1. Os dados brutos são lidos em um pipeline de Azure Data Factory (ADF).
1. O pipeline do ADF envia os dados para um cluster Azure Databricks, que executa um notebook Python para transformar os dados.
1. Os dados são armazenados em um contêiner de BLOB, onde podem ser usados pelo Azure Machine Learning para treinar um modelo.

![fluxo de trabalho do pipeline de ingestão de dados](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

## <a name="continuous-integration-and-delivery-overview"></a>Visão geral de integração e entrega contínua

Assim como ocorre com muitas soluções de software, há uma equipe (por exemplo, engenheiros de dados) trabalhando nela. Eles colaboram e compartilham os mesmos recursos do Azure, como Azure Data Factory, Azure Databricks e contas de armazenamento do Azure. A coleção desses recursos é um ambiente de desenvolvimento. Os engenheiros de dados contribuem para a mesma base de código-fonte.

Um sistema de integração e entrega contínua automatiza o processo de criação, teste e fornecimento (implantação) da solução. O processo de CI (integração contínua) executa as seguintes tarefas:

* Monta o código
* Verifica com os testes de qualidade de código
* Executa testes de unidade
* Produz artefatos como código testado e modelos de Azure Resource Manager

O processo de entrega contínua (CD) implanta os artefatos nos ambientes downstream.

![diagrama de ingestão de dados do cICD](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Este artigo demonstra como automatizar os processos de CI e CD com o [Azure pipelines](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Gerenciamento de controle do código-fonte

O gerenciamento de controle do código-fonte é necessário para controlar as alterações e habilitar a colaboração entre os membros da equipe.
Por exemplo, o código seria armazenado em um repositório DevOps, GitHub ou GitLab do Azure. O fluxo de trabalho de colaboração é baseado em um modelo de ramificação. Por exemplo, [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html).

### <a name="python-notebook-source-code"></a>Código-fonte do notebook Python

Os engenheiros de dados trabalham com o código-fonte do notebook Python localmente em um IDE (por exemplo, [Visual Studio Code](https://code.visualstudio.com)) ou diretamente no espaço de trabalho do databricks. Depois que as alterações de código forem concluídas, elas serão mescladas ao repositório após uma política de ramificação.

> [!TIP] 
> Recomendamos armazenar o código em `.py` arquivos em vez de no `.ipynb` formato Jupyter notebook. Ele melhora a legibilidade do código e permite verificações automáticas de qualidade de código no processo de CI.

### <a name="azure-data-factory-source-code"></a>Azure Data Factory código-fonte

O código-fonte de pipelines de Azure Data Factory é uma coleção de arquivos JSON gerados por um espaço de trabalho Azure Data Factory. Normalmente, os engenheiros de dados trabalham com um designer visual no espaço de trabalho Azure Data Factory, e não com os arquivos de código-fonte diretamente. 

Para configurar o espaço de trabalho para usar um repositório de controle do código-fonte, confira [autor com Azure Repos integração com o Git](../data-factory/source-control.md#author-with-azure-repos-git-integration).   

## <a name="continuous-integration-ci"></a>CI (Integração contínua)

O objetivo final do processo de integração contínua é reunir o trabalho de equipe conjunta do código-fonte e prepará-lo para a implantação nos ambientes downstream. Assim como no gerenciamento de código-fonte, esse processo é diferente para os notebooks Python e Azure Data Factory pipelines. 

### <a name="python-notebook-ci"></a>CI do notebook Python

O processo de CI para os notebooks do Python Obtém o código da ramificação de colaboração (por exemplo, * **Master** _ ou _*_develop_*_ ) e executa as seguintes atividades: _ fiapoing de código
* Teste de unidade
* Salvando o código como um artefato

O trecho de código a seguir demonstra a implementação dessas etapas em um pipeline do Azure DevOps * **YAML** _:

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/_-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks
```

O pipeline usa [flake8](https://pypi.org/project/flake8/) para fazer o refiapoção do código Python. Ele executa os testes de unidade definidos no código-fonte e publica os resultados de fiapos e de teste para que fiquem disponíveis na tela de execução do pipeline do Azure:

![refiapondo testes de unidade](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Se os testes de unidade e de fiapoção forem bem-sucedidos, o pipeline copiará o código-fonte para o repositório de artefatos a ser usado pelas etapas de implantação subsequentes.

### <a name="azure-data-factory-ci"></a>CI Azure Data Factory

O processo de CI para um pipeline de Azure Data Factory é um afunilamento para um pipeline de ingestão de dados. Não há integração contínua. Um artefato implantável para Azure Data Factory é uma coleção de modelos de Azure Resource Manager. A única maneira de produzir esses modelos é clicar no botão * **publicar** _ no espaço de trabalho Azure data Factory.

1. Os engenheiros de dados mesclam o código-fonte de suas ramificações de recursos no Branch de colaboração, por exemplo, _*_mestre_*_ ou _*_desenvolvimento_*_. 
1. Alguém com as permissões concedidas clica no botão _*_publicar_*_ para gerar modelos de Azure Resource Manager do código-fonte no Branch de colaboração. 
1. O espaço de trabalho valida os pipelines (Imagine-os a partir de refiapoing e teste de unidade), gera modelos de Azure Resource Manager (imagine a partir do prédio) e salva os modelos gerados em uma ramificação técnica _*_adf_publish_*_ no mesmo repositório de código (Imagine-os a partir de artefatos de publicação). Essa ramificação é criada automaticamente pelo espaço de trabalho Azure Data Factory. 

Para obter mais informações sobre esse processo, consulte [integração e entrega contínuas em Azure data Factory](../data-factory/continuous-integration-deployment.md).

É importante garantir que os modelos de Azure Resource Manager gerados sejam independentes de ambiente. Isso significa que todos os valores que podem ser diferentes entre os ambientes são parametrizadas. Azure Data Factory é inteligente o suficiente para expor a maioria desses valores como parâmetros. Por exemplo, no modelo a seguir, as propriedades de conexão para um Azure Machine Learning espaço de trabalho são expostas como parâmetros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

No entanto, talvez você queira expor suas propriedades personalizadas que não são tratadas pelo espaço de trabalho Azure Data Factory por padrão. No cenário deste artigo, um pipeline Azure Data Factory invoca um bloco de anotações Python que processa os dados. O notebook aceita um parâmetro com o nome de um arquivo de dados de entrada.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Esse nome é diferente para os ambientes _*_dev_*_ , _*_QA_*_ , _*_UAT_*_ e _*_prod_*_ . Em um pipeline complexo com várias atividades, pode haver várias propriedades personalizadas. É uma boa prática coletar todos esses valores em um único local e defini-los como _*_variáveis_*_ de pipeline:

![Captura de tela mostra um bloco de anotações chamado PrepareData e M L executar pipeline chamado M L executar pipeline na parte superior, com a guia variáveis selecionada abaixo com a opção de adicionar novas variáveis, cada uma com um nome, tipo e valor padrão.](media/how-to-cicd-data-ingestion/adf-variables.png)

As atividades de pipeline podem se referir às variáveis de pipeline ao mesmo tempo em que as utilizam:

![A captura de tela mostra um bloco de anotações chamado PrepareData e M L executar pipeline chamado M L executar pipeline na parte superior, com a guia Configurações selecionada abaixo.](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

O espaço de trabalho Azure Data Factory _*_não_*_ expõe variáveis de pipeline como parâmetros de modelos de Azure Resource Manager por padrão. O espaço de trabalho usa o [modelo de parametrização padrão](../data-factory/continuous-integration-deployment.md#default-parameterization-template) que determina quais propriedades de pipeline devem ser expostas como Azure Resource Manager parâmetros de modelo. Para adicionar variáveis de pipeline à lista, atualize a `"Microsoft.DataFactory/factories/pipelines"` seção do [modelo de parametrização padrão](../data-factory/continuous-integration-deployment.md#default-parameterization-template) com o trecho a seguir e coloque o arquivo JSON de resultado na raiz da pasta de origem:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "_": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Isso forçará o espaço de trabalho Azure Data Factory a adicionar as variáveis à lista de parâmetros quando o botão * **publicar** _ for clicado:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

Os valores no arquivo JSON são valores padrão configurados na definição do pipeline. Eles devem ser substituídos pelos valores de ambiente de destino quando o modelo de Azure Resource Manager é implantado.

## <a name="continuous-delivery-cd"></a>CD (Entrega contínua)

O processo de entrega contínua usa os artefatos e os implanta no primeiro ambiente de destino. Ele verifica se a solução funciona executando testes. Se for bem-sucedido, ele continuará no próximo ambiente. 

O pipeline do Azure do CD consiste em vários estágios que representam os ambientes. Cada estágio contém [implantações](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) e [trabalhos](/azure/devops/pipelines/process/phases?tabs=yaml&view=azure-devops&preserve-view=true) que executam as seguintes etapas:

_ Implantar um bloco de anotações do Python no espaço de trabalho Azure Databricks
* Implantar um pipeline de Azure Data Factory 
* Executar o pipeline
* Verificar o resultado da ingestão de dados

Os estágios de pipeline podem ser configurados com [aprovações](/azure/devops/pipelines/process/approvals?tabs=check-pass&view=azure-devops&preserve-view=true) e [Gates](/azure/devops/pipelines/release/approvals/gates?view=azure-devops&preserve-view=true) que fornecem controle adicional sobre como o processo de implantação evolui pela cadeia de ambientes.

### <a name="deploy-a-python-notebook"></a>Implantar um bloco de anotações do Python

O trecho de código a seguir define uma [implantação](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) de pipeline do Azure que copia um bloco de anotações do Python para um cluster do databricks:

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

Os artefatos produzidos pelo CI são copiados automaticamente para o agente de implantação e estão disponíveis na `$(Pipeline.Workspace)` pasta. Nesse caso, a tarefa de implantação refere-se ao `di-notebooks` artefato que contém o bloco de notas do Python. Essa [implantação](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) usa a [extensão DevOps do databricks do Azure](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) para copiar os arquivos do bloco de anotações para o espaço de trabalho do databricks.

O `Deploy_to_QA` estágio contém uma referência ao `devops-ds-qa-vg` grupo de variáveis definido no projeto DevOps do Azure. As etapas neste estágio referem-se às variáveis desse grupo de variáveis (por exemplo, `$(DATABRICKS_URL)` e `$(DATABRICKS_TOKEN)` ). A ideia é que o próximo estágio (por exemplo, `Deploy_to_UAT` ) funcionará com os mesmos nomes de variáveis definidos em seu próprio grupo de variáveis no escopo de UAT.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Implantar um pipeline de Azure Data Factory

Um artefato implantável para Azure Data Factory é um modelo de Azure Resource Manager. Ela será implantada com a tarefa * **implantação do grupo de recursos do Azure** , pois ela é demonstrada no seguinte trecho de código:

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
O valor do parâmetro data filename vem da `$(DATA_FILE_NAME)` variável definida em um grupo de variáveis de estágio de QA. Da mesma forma, todos os parâmetros definidos no _*_ARMTemplateForFactory.jsem_*_ podem ser substituídos. Se não forem, os valores padrão serão usados.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Executar o pipeline e verificar o resultado da ingestão de dados

A próxima etapa é verificar se a solução implantada está funcionando. A definição de trabalho a seguir executa um pipeline Azure Data Factory com um [script do PowerShell](https://github.com/microsoft/DataOps/tree/master/adf/utils) e executa um notebook Python em um cluster Azure Databricks. O notebook verifica se os dados foram ingeridos corretamente e valida o arquivo de dados de resultado com o `$(bin_FILE_NAME)` nome.

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

A tarefa final no trabalho verifica o resultado da execução do bloco de anotações. Se ele retornar um erro, ele definirá o status da execução do pipeline como com falha.

## <a name="putting-pieces-together"></a>Juntando partes

O pipeline completo de CI/CD do Azure consiste nos seguintes estágios: _ CI
* Implantar em p e r
    * Implantar no databricks + implantar no ADF
    * Teste de integração

Ele contém um número de * **implantação** _ estágios igual ao número de ambientes de destino que você tem. Cada estágio de _*_implantação_*_ contém duas [implantações](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) executadas em paralelo e um [trabalho](/azure/devops/pipelines/process/phases?tabs=yaml&view=azure-devops&preserve-view=true) executado após as implantações para testar a solução no ambiente.

Uma implementação de exemplo do pipeline é montada no seguinte trecho de _*_YAML_*_ :

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/_-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>Próximas etapas

* [Controle do código-fonte no Azure Data Factory](../data-factory/source-control.md)
* [Integração e entrega contínuas no Azure Data Factory](../data-factory/continuous-integration-deployment.md)
* [DevOps para Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)