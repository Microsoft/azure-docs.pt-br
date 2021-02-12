---
title: Usar Azure Pipelines para compilar e implantar soluções de HPC
description: Saiba como implantar um pipeline de build/versão para um aplicativo HPC em execução no Lote do Azure.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: how-to
ms.openlocfilehash: e87be0db65cf12a265566e0c05815722ce3cc609
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578868"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Usar Azure Pipelines para compilar e implantar soluções de HPC

O Azure DevOps Services fornecer uma variedade de ferramentas usadas pelas equipes de desenvolvimento ao compilar um aplicativo personalizado. As ferramentas fornecidas pelo Azure DevOps podem se traduzir em build e teste automatizados de soluções de computação de alto desempenho. Este artigo demonstra como configurar uma CI (integração contínua) e um CD (implantação contínua) usando o Azure Pipelines para uma solução de computação de alto desempenho implantada no Lote do Azure.

O Azure Pipelines fornece uma variedade de processos de CI/CD modernos para compilar, implantar, testar e monitorar software. Esses processos aceleram sua entrega de software, permitindo que você se concentre no código em vez de oferecer suporte à infraestrutura e às operações.

## <a name="create-an-azure-pipeline"></a>Criar um pipeline do Azure

Neste exemplo, criaremos um pipeline de build e lançamento para implantar uma infraestrutura do Lote do Azure e liberar um pacote de aplicativos. Supondo que o código seja desenvolvido localmente, esse é o fluxo de implantação geral:

![Diagrama mostrando o fluxo de implantação em nosso pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Instalação

Para seguir as etapas neste artigo, você precisa de um projeto de equipe e uma organização do Azure DevOps.

* [Criar uma organização do Azure DevOps](/azure/devops/organizations/accounts/create-organization)
* [Criar um projeto no Azure DevOps](/azure/devops/organizations/projects/create-project)

### <a name="source-control-for-your-environment"></a>Controle do código-fonte do seu ambiente

O controle do código-fonte permite que as equipes acompanhem as alterações feitas na base de código e inspecionem as versões anteriores dele.

Normalmente, o controle do código-fonte é considerado lado a lado com o código do software. E quanto à infraestrutura subjacente? Isso nos leva à infraestrutura como código, na qual usaremos os modelos do Azure Resource Manager ou outras alternativas de open-source para definir declarativamente nossa infraestrutura subjacente.

Esse exemplo conta muito com um número de Documentos JSON (Modelos do Resource Manager) e binários existentes. Você pode copiar esses exemplos em seu repositório e enviá-los para o Azure DevOps.

A estrutura de base de código usada neste exemplo é semelhante à seguinte:

* Uma pasta **arm-templates** , que contém vários modelos do Azure Resource Manager. Os modelos são explicados neste artigo.
* Uma pasta **client-application** , que é uma cópia do exemplo de [Processamento de arquivo .NET do Lote do Azure com ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial). Isso não é necessário para este artigo.
* Uma pasta **HPC-Application** , que é a versão do Windows 64 bits do [ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08).
* Uma pasta de **pipelines**. Ela contém um arquivo YAML que descreve nosso processo de build. Isso é abordado no artigo.

Esta seção pressupõe que você esteja familiarizado com o controle de versão e a criação de modelos do Resource Manager. Se você não estiver familiarizado com esses conceitos, confira as páginas a seguir para saber mais.

* [O que é controle do código-fonte?](/azure/devops/user-guide/source-control)
* [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Este exemplo utiliza vários modelos do Resource Manager para implantar nossa solução. Para fazer isso, usamos vários modelos de recursos (semelhantes a unidades ou módulos) que implementam uma parte específica da funcionalidade. Também usamos um modelo de solução de ponta a ponta que é responsável por reunir esses recursos subjacentes. Há alguns benefícios nessa abordagem:

* Os modelos de funcionalidade subjacentes podem ser testados individualmente por unidade.
* Os modelos de funcionalidade subjacentes podem ser definidos como um padrão dentro de uma organização e reutilizados em várias soluções.

Para este exemplo, há um modelo de solução de ponta a ponta (deployment.json) que implanta três modelos. Os modelos subjacentes são modelos de funcionalidade, responsáveis pela implantação de um aspecto específico da solução.

![Exemplo de estrutura de modelo vinculado usando modelos do Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

O primeiro modelo que examinaremos é para uma Conta de Armazenamento do Azure. Nossa solução requer uma conta de armazenamento para implantar o aplicativo em nossa Conta do lote. Esteja ciente do [guia de referência do modelo do Resource Manager para os tipos de recurso Microsoft.Storage](/azure/templates/microsoft.storage/allversions) ao compilar modelos do Resource Manager para contas de armazenamento.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Em seguida, veremos o modelo de Conta do Lote do Azure. A Conta do Lote do Azure atua como uma plataforma para executar vários aplicativos em pools (agrupamentos de computadores). Esteja ciente do [guia de referência do modelo do Resource Manager para os tipos de recurso Microsoft.Batch](/azure/templates/microsoft.batch/allversions) ao compilar modelos do Resource Manager para Contas de Lote.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

O próximo modelo mostra um exemplo de criação de um pool do Lote do Azure (os computadores de back-end para processar nossos aplicativos). Esteja ciente do [guia de referência do modelo do Resource Manager para os tipos de recurso Microsoft.Batch](/azure/templates/microsoft.batch/allversions) ao compilar modelos do Resource Manager para Pools de Contas de Lote.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Por fim, temos um modelo que age de forma semelhante a um orquestrador. Este modelo é responsável por implantar os modelos de funcionalidade.

Você também pode saber mais sobre [como criar modelos vinculados do Azure Resource Manager](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) em um artigo separado.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>A solução HPC

A infraestrutura e o software podem ser definidos como código e colocalizados no mesmo repositório.

Para essa solução, o ffmpeg é usado como o pacote de aplicativos. O pacote ffmpeg pode ser baixado [aqui](https://www.videohelp.com/software?d=ffmpeg-3.3.4-win64-static.zip).

![Exemplo de estrutura de repositório Git](media/batch-ci-cd/git-repository.jpg)

Há quatro seções principais para este repositório:

* A pasta **arm-templates** que armazena nossa infraestrutura como código
* A pasta **hpc-application** que contém os binários para ffmpeg
* A pasta **pipelines** que contém a definição para o pipeline de build.
* **Opcional** : A pasta **client-application** que armazenaria o código para o aplicativo .NET. Não usamos isso no exemplo, mas em seu próprio projeto, talvez você queira realizar execuções do Aplicativo do Lote HPC por meio de um aplicativo cliente.

> [!NOTE]
> Esse é apenas um exemplo de uma estrutura para uma base de código. Essa abordagem é usada com o objetivo de demonstrar que o aplicativo, a infraestrutura e o código de pipeline estão armazenados no mesmo repositório.

Agora que o código-fonte está configurado, podemos iniciar o primeiro build.

## <a name="continuous-integration"></a>Integração contínua

O [Azure Pipelines](/azure/devops/pipelines/get-started/), no Azure DevOps Services, ajuda a implementar um pipeline de build, teste e implantação para seus aplicativos.

Nesse estágio do pipeline, os testes são normalmente executados para validar o código e compilar as partes apropriadas do software. O número e os tipos de testes e as tarefas adicionais que você executar dependerão da sua estratégia de build e lançamento mais ampla.

## <a name="preparing-the-hpc-application"></a>Preparar o aplicativo HPC

Neste exemplo, iremos nos concentrar na pasta **hpc-application**. A pasta **hpc-application** é o software ffmpeg que será executado a partir da conta do Lote do Azure.

1. Navegue até a seção Builds do Azure Pipelines no Azure DevOps da sua organização. Crie um **Novo pipeline**.

    ![Criar um novo pipeline de build](media/batch-ci-cd/new-build-pipeline.jpg)

1. Você tem duas opções para criar um Pipeline de build:

    a. [Como usar o Designer Visual](/azure/devops/pipelines/get-started-designer). Para fazer isso, clique em "Usar o designer visual" na página **Novo pipeline**.

    b. [Como usar builds do YAML](/azure/devops/pipelines/get-started-yaml). Você pode criar um novo pipeline do YAML clicando na opção Azure Repos ou GitHub na página Novo pipeline. Como alternativa, você pode armazenar o exemplo abaixo no controle do código-fonte e fazer referência a um arquivo YAML existente clicando no Designer Visual e, em seguida, usando o modelo YAML.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Depois de configurar o build conforme necessário, selecione **Salvar e Fila**. Se você tiver a integração contínua habilitada (na seção **Gatilhos** ), o build será disparado automaticamente quando for realizada uma nova confirmação para o repositório, atendendo às condições definidas no build.

    ![Exemplo de um Pipeline de Build existente](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Exiba as atualizações dinâmicas sobre o andamento do seu build no Azure DevOps navegando até a seção **Build** do Azure Pipelines. Selecione o build apropriado na definição de build.

    ![Exibir as saídas dinâmicas do build](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Se você usar um aplicativo cliente para executar o Aplicativo do Lote HPC, será necessário criar uma definição de build separada para esse aplicativo. Você pode encontrar vários guias de instruções na documentação do [Azure Pipelines](/azure/devops/pipelines/get-started/index).

## <a name="continuous-deployment"></a>Implantação contínua

O Azure Pipelines também é usado para implantar seu aplicativo e a infraestrutura subjacente. O [pipeline de lançamento](/azure/devops/pipelines/release) é o componente que permite a implantação contínua e automatizada do processo de lançamento.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Implantar o aplicativo e a infraestrutura subjacente

Há várias etapas envolvidas na implantação da infraestrutura. Como nós usamos [modelos vinculados](../azure-resource-manager/templates/linked-templates.md), esses modelos precisarão ser acessíveis de um ponto de extremidade público (HTTP ou HTTPS). Isso pode ser um repositório no GitHub, uma conta de armazenamento de BLOBs do Azure ou outro local de armazenamento. Os artefatos de modelo carregados podem permanecer seguros, pois podem ser mantidos em um modo privado, mas serão acessados usando alguma forma de token SAS (assinatura de acesso compartilhado). O exemplo a seguir demonstra como implantar uma infraestrutura com modelos a partir do blob de Armazenamento do Azure.

1. Crie uma **Nova definição da versão** e selecione uma definição vazia. Em seguida, precisamos renomear o ambiente recém-criado para algo relevante ao nosso pipeline.

    ![Pipeline de lançamento inicial](media/batch-ci-cd/Release-0.jpg)

1. Crie uma dependência no pipeline de build para obter a saída para nosso aplicativo HPC.

    > [!NOTE]
    > Mais uma vez, observe o **Alias de origem** , pois isso será necessário quando as tarefas forem criadas dentro da Definição da versão.

    ![Criar um link de artefato para o HPCApplicationPackage no pipeline de build apropriado](media/batch-ci-cd/Release-1.jpg)

1. Crie um link para outro artefato, desta vez, um repositório do Azure. Isso é necessário para acessar os modelos do Resource Manager armazenados em seu repositório. Como os modelos do Resource Manager não exigem build, você não precisa enviá-los por meio do pipeline de build.

    > [!NOTE]
    > Mais uma vez, observe o **Alias de origem** , pois isso será necessário quando as tarefas forem criadas dentro da Definição da versão.

    ![Criar um link de artefato para o Azure Repos](media/batch-ci-cd/Release-2.jpg)

1. Navegue até a seção **Variáveis**. É recomendável criar algumas variáveis em seu pipeline, para que você não insira as mesmas informações em várias tarefas. Essas são as variáveis usadas neste exemplo e como elas afetam a implantação.

    * **applicationStorageAccountName** : nome da conta de armazenamento para armazenar os binários do aplicativo HPC
    * **batchAccountApplicationName** : nome do aplicativo na conta do Lote do Azure
    * **batchAccountName** : nome da conta do Lote do Azure
    * **batchAccountPoolName** : nome do pool de VMs que executa o processamento
    * **batchApplicationId** : ID exclusiva para o aplicativo do Lote do Azure
    * **batchApplicationVersion** : versão semântica do seu aplicativo do lote (ou seja, os binários ffmpeg)
    * **location** : local para os recursos do Azure a serem implantados
    * **resourceGroupName** : nome do Grupo de recursos a ser criado e onde seus recursos serão implantados
    * **storageAccountName** : nome da Conta de armazenamento para manter os modelos vinculados do Resource Manager

    ![Exemplo de variáveis definidas para o lançamento do Azure Pipelines](media/batch-ci-cd/Release-4.jpg)

1. Navegue até as tarefas do ambiente de desenvolvimento. No instantâneo abaixo, é possível ver seis tarefas. Essas tarefas vão: baixar os arquivos ffmpeg compactados, implantar uma conta de armazenamento para hospedar os modelos aninhados do Resource Manager, copiar esses modelos do Resource Manager para a conta de armazenamento, implantar a conta do lote e as dependências necessárias, criar um aplicativo na Conta do Lote do Azure e carregar o pacote de aplicativos na Conta do Lote do Azure.

    ![Exemplo das tarefas usadas para lançar o aplicativo HPC no Lote do Azure](media/batch-ci-cd/Release-3.jpg)

1. Adicione a tarefa **Baixar o artefato do pipeline (versão prévia)** e defina as seguintes propriedades:
    * **Nome de exibição:** baixar o ApplicationPackage para o agente
    * **O nome do artefato a ser baixado:** hpc-application
    * **Caminho para baixar** : $(System.DefaultWorkingDirectory)

1. Crie uma Conta de Armazenamento para armazenar os artefatos. Uma conta de armazenamento existente da solução pode ser usada, mas para o exemplo autossuficiente e isolamento de conteúdo, estamos criando uma conta de armazenamento dedicada para nossos artefatos (especificamente os modelos do Resource Manager).

    Adicione a tarefa **Implantação do grupo de recursos do Azure** e defina as seguintes propriedades:
    * **Nome de exibição:** conta de armazenamento de implantação para modelos do Resource Manager
    * **Assinatura do Azure:** selecione a Assinatura do Azure apropriada
    * **Ação** : Criar ou atualizar o grupo de recursos
    * **Grupo de recursos** : $(resourceGroupName)
    * **Localização** : $(location)
    * **Modelo** : $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/storageAccount.json
    * **Substitua parâmetros de modelo** : -accountName $(storageAccountName)

1. Carregue os artefatos do Controle do Código-Fonte na Conta de armazenamento. Há uma tarefa do Azure Pipeline para executar isso. Como parte dessa tarefa, a URL do Contêiner da conta de armazenamento e o token SAS podem ser enviados para uma variável em Azure Pipelines. Isso significa que ele pode ser reutilizado durante toda a fase do agente.

    Adicione a tarefa **Cópia de Arquivos do Azure** e defina as seguintes propriedades:
    * **Fonte** : $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/
    * **Tipo de conexão do Azure** : Azure Resource Manager
    * **Assinatura do Azure:** selecione a Assinatura do Azure apropriada
    * **Tipo de destino** : Blob do Azure
    * **Conta de armazenamento do RM** : $(storageAccountName)
    * **Nome do contêiner** : templates
    * **URI do contêiner de armazenamento** : templateContainerUri
    * **Token SAS do contêiner de armazenamento** : templateContainerSasToken

1. Implante o modelo de orquestrador. Faça recall do modelo de orquestrador anterior, você notará que havia parâmetros para a URL do contêiner da conta de armazenamento, além do token SAS. Você deve observar que as variáveis necessárias no modelo do Resource Manager são mantidas na seção de variáveis da definição da versão ou foram configuradas a partir de outra tarefa do Azure Pipelines (por exemplo, parte da tarefa Cópia de blob do Azure).

    Adicione a tarefa **Implantação do grupo de recursos do Azure** e defina as seguintes propriedades:
    * **Nome de exibição:** implantar o Lote do Azure
    * **Assinatura do Azure:** selecione a Assinatura do Azure apropriada
    * **Ação** : Criar ou atualizar o grupo de recursos
    * **Grupo de recursos** : $(resourceGroupName)
    * **Localização** : $(location)
    * **Modelo** : $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/deployment.json
    * **Substituir parâmetros de modelo** : ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Uma prática comum é usar tarefas do Azure Key Vault. Se a Entidade de serviço (conexão com sua Assinatura do Azure) tiver um conjunto de políticas de acesso apropriado, ele poderá baixar segredos de um Azure Key Vault e ser usado como variáveis em seu pipeline. O nome do segredo será definido com o valor associado. Por exemplo, um segredo de sshPassword poderia ser referenciado com $ (sshPassword) na definição da versão.

1. As próximas etapas chamam o CLI do Azure. O primeiro é usado para criar um aplicativo no Lote do Azure. E carregar pacotes associados.

    Adicione a tarefa **CLI do Azure** e defina as propriedades a seguir:
    * **Nome de exibição:** criar o aplicativo na conta do Lote do Azure
    * **Assinatura do Azure:** selecione a Assinatura do Azure apropriada
    * **Local do script** : script embutido
    * **Script embutido** : ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. a segunda etapa é usada para carregar pacotes associados ao aplicativo. Em nosso caso, os arquivos ffmpeg.

    Adicione a tarefa **CLI do Azure** e defina as propriedades a seguir:
    * **Nome de exibição:** carregar pacote na conta do Lote do Azure
    * **Assinatura do Azure:** selecione a Assinatura do Azure apropriada
    * **Local do script** : script embutido
    * **Script embutido** : ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > O número de versão do pacote de aplicativos é definido como uma variável. Isso é conveniente se a substituição de versões anteriores do pacote funcionar para você e se você quiser controlar manualmente o número de versão do pacote enviado para o Lote do Azure.

1. Crie uma nova versão selecionando **Versão > Criar uma nova versão**. Depois de acionado, selecione o link da nova versão para exibir o status.

1. Você pode exibir a saída ao vivo do agente selecionando o botão **Logs** abaixo do ambiente.

    ![Exibir o status da sua versão](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Teste o ambiente

Assim que o ambiente estiver configurado, confirme se os testes a seguir podem ser concluídos com êxito.

Conecte-se à nova Conta de Lote do Azure, usando o CLI do Azure em um prompt de comando do PowerShell.

* Entre na sua conta do Azure com `az login` e siga as instruções para autenticar.
* Agora autentique a conta do Lote: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Listar os aplicativos disponíveis

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Verificar se o pool é válido

```azurecli
az batch pool list
```

Observe o valor de `currentDedicatedNodes` na saída desse comando. Esse valor é ajustado no próximo teste.

#### <a name="resize-the-pool"></a>Redimensionar o pool

Redimensione o pool para que haja nós de computação disponíveis para teste de trabalho e tarefa, verifique com o comando de lista de pools para ver o status atual até que o redimensionamento seja concluído e que haja nós disponíveis

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Próximas etapas

Além deste artigo, há dois tutoriais que utilizam o ffmpeg, usando o .NET e o Python. Confira estes tutoriais para obter mais informações sobre como interagir com uma conta do Lote por meio de um aplicativo simples.

* [Executar uma carga de trabalho paralela com o Lote do Azure usando a API do Python](tutorial-parallel-python.md)
* [Executar uma carga de trabalho paralela com o Lote do Azure usando a API do .NET](tutorial-parallel-dotnet.md)
