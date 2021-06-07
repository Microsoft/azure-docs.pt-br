---
title: Usar Azure Pipelines para compilar e implantar soluções de HPC
description: Saiba como implantar um pipeline de build/versão para um aplicativo HPC em execução no Lote do Azure.
author: chrisreddington
ms.author: chredd
ms.date: 03/04/2021
ms.topic: how-to
ms.openlocfilehash: 7170044af58a508ff5a43751cc376f8b8d498444
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435538"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Usar Azure Pipelines para compilar e implantar soluções de HPC

As ferramentas fornecidas pelo Azure DevOps podem traduzir para a criação automatizada e o teste de soluções HPC (computação de alto desempenho). O [Azure pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) fornece uma variedade de processos de CI (integração contínua moderna) e de CD (implantação contínua) para compilar, implantar, testar e monitorar software. Esses processos aceleram sua entrega de software, permitindo que você se concentre no código em vez de oferecer suporte à infraestrutura e às operações.

Este artigo explica como configurar os processos de CI/CD usando [Azure pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) para soluções de HPC implantadas no lote do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas neste artigo, você precisa de uma [organização DevOps do Azure](/azure/devops/organizations/accounts/create-organization). Você também precisará [criar um projeto no Azure DevOps](/azure/devops/organizations/projects/create-project).

É útil ter uma compreensão básica do controle do [código-fonte](/azure/devops/user-guide/source-control) e da [sintaxe do modelo de Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) antes de começar.

## <a name="create-an-azure-pipeline"></a>Criar um pipeline do Azure

Neste exemplo, você criará um pipeline de Build e versão para implantar uma infraestrutura do lote do Azure e liberará um pacote de aplicativos. Supondo que o código seja desenvolvido localmente, esse é o fluxo de implantação geral:

![Diagrama mostrando o fluxo de implantação no pipeline,](media/batch-ci-cd/DeploymentFlow.png)

Este exemplo usa vários modelos de Azure Resource Manager e binários existentes. Você pode copiar esses exemplos em seu repositório e enviá-los para o Azure DevOps.

### <a name="understand-the-azure-resource-manager-templates"></a>Entender os modelos de Azure Resource Manager

Este exemplo usa vários modelos de Azure Resource Manager para implantar a solução. Três modelos de funcionalidade (semelhantes a unidades ou módulos) são usados para implementar uma parte específica da funcionalidade. Um modelo de solução de ponta a ponta (deployment.jsem) é usado para implantar esses modelos de funcionalidade subjacentes. Essa [estrutura de modelo vinculada ](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) permite que cada modelo de funcionalidade seja testado e reutilizado individualmente em todas as soluções.

![Diagrama mostrando uma estrutura de modelo vinculada usando modelos de Azure Resource Manager.](media/batch-ci-cd/ARMTemplateHierarchy.png)

Este modelo define uma conta de armazenamento do Azure, que é necessária para implantar o aplicativo na conta do lote. Para obter informações detalhadas, consulte o [Guia de referência do modelo do Resource Manager para tipos de recurso Microsoft. Storage](/azure/templates/microsoft.storage/allversions).

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

O próximo modelo define uma [conta do lote do Azure](accounts.md). A conta do lote atua como uma plataforma para executar vários aplicativos em [pools](nodes-and-pools.md#pools). Para obter informações detalhadas, consulte o [Guia de referência do modelo do Resource Manager para Microsoft.Battipos de recurso ch](/azure/templates/microsoft.batch/allversions).

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

O próximo modelo cria um pool do lote na conta do lote. Para obter informações detalhadas, consulte o [Guia de referência do modelo do Resource Manager para Microsoft.Battipos de recurso ch](/azure/templates/microsoft.batch/allversions).

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

O modelo final atua como um orquestrador, implantando os três modelos de recursos subjacentes.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resource Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resource Manager templates"
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

### <a name="understand-the-hpc-solution"></a>Entender a solução HPC

Conforme observado anteriormente, este exemplo usa vários modelos de Azure Resource Manager e binários existentes. Você pode copiar esses exemplos em seu repositório e enviá-los para o Azure DevOps.

Para essa solução, FFmpeg é usado como o pacote de aplicativos. Você pode [baixar o pacote ffmpeg](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) se ainda não o tiver.

![Captura de tela da estrutura do repositório.](media/batch-ci-cd/git-repository.jpg)

Há quatro seções principais para este repositório:

- Uma pasta **ARM-templates** , contendo os modelos de Azure Resource Manager
- Uma pasta **HPC-Application** , que contém a versão do Windows 64 bits do [ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08).
- Uma pasta **pipelines** , que contém um arquivo YAML que define o processo de pipeline de compilação.
- Opcional: uma pasta **cliente-aplicativo** , que é uma cópia do [processamento de arquivos .net do lote do Azure com](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) o exemplo ffmpeg. Este aplicativo não é necessário para este artigo.


> [!NOTE]
> Esse é apenas um exemplo de uma estrutura para uma base de código. Essa abordagem é usada com o objetivo de demonstrar que o aplicativo, a infraestrutura e o código de pipeline estão armazenados no mesmo repositório.

Agora que o código-fonte está configurado, você pode começar a primeira compilação.

## <a name="continuous-integration"></a>Integração contínua

O [Azure Pipelines](/azure/devops/pipelines/get-started/), no Azure DevOps Services, ajuda a implementar um pipeline de build, teste e implantação para seus aplicativos.

Nesse estágio do pipeline, os testes são normalmente executados para validar o código e compilar as partes apropriadas do software. O número e os tipos de testes e as tarefas adicionais que você executar dependerão da sua estratégia de build e lançamento mais ampla.

## <a name="prepare-the-hpc-application"></a>Preparar o aplicativo HPC

Nesta seção, você trabalhará com a pasta **HPC-Application** . Essa pasta contém o software (ffmpeg) que será executado na conta do lote do Azure.

1. Navegue até a seção Builds do Azure Pipelines no Azure DevOps da sua organização. Crie um **Novo pipeline**.

    ![Captura de tela do novo pipeline.](media/batch-ci-cd/new-build-pipeline.jpg)

1. Você tem duas opções para criar um Pipeline de build:

    a. [Use o designer visual](/azure/devops/pipelines/get-started-designer). Para fazer isso, selecione "usar o designer visual" na página **novo pipeline** .

    b. [Use compilações do YAML](/azure/devops/pipelines/get-started-yaml). Você pode criar um novo pipeline do YAML clicando na opção Azure Repos ou GitHub na página **novo pipeline** . Como alternativa, você pode armazenar o exemplo abaixo no controle do código-fonte e fazer referência a um arquivo YAML existente selecionando o Visual Designer e, em seguida, usando o modelo YAML.

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

1. Depois de configurar o build conforme necessário, selecione **Salvar e Fila**. Se você tiver a integração contínua habilitada (na seção **Gatilhos**), o build será disparado automaticamente quando for realizada uma nova confirmação para o repositório, atendendo às condições definidas no build.

    ![Captura de tela de um pipeline de Build existente.](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Exiba as atualizações dinâmicas sobre o andamento do seu build no Azure DevOps navegando até a seção **Build** do Azure Pipelines. Selecione o build apropriado na definição de build.

    ![Captura de tela de saídas dinâmicas do Build no Azure DevOps.](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Se você usar um aplicativo cliente para executar sua solução HPC, será necessário criar uma definição de compilação separada para esse aplicativo. Você pode encontrar vários guias de instruções na documentação do [Azure Pipelines](/azure/devops/pipelines/get-started/index).

## <a name="continuous-deployment"></a>Implantação contínua

Azure Pipelines também é usado para implantar seu aplicativo e a infraestrutura subjacente. Os [pipelines de versão](/azure/devops/pipelines/release) habilitam a implantação contínua e automatizam seu processo de lançamento.

### <a name="deploy-your-application-and-underlying-infrastructure"></a>Implantar seu aplicativo e a infraestrutura subjacente

Há várias etapas envolvidas na implantação da infraestrutura. Como essa solução usa [modelos vinculados](../azure-resource-manager/templates/linked-templates.md), esses modelos precisarão ser acessíveis de um ponto de extremidade público (http ou HTTPS). Isso pode ser um repositório no GitHub, uma conta de armazenamento de BLOBs do Azure ou outro local de armazenamento. Os artefatos de modelo carregados podem permanecer seguros, pois podem ser mantidos em um modo privado, mas serão acessados usando alguma forma de token SAS (assinatura de acesso compartilhado).

O exemplo a seguir demonstra como implantar uma infraestrutura com modelos a partir do blob de Armazenamento do Azure.

1. Crie uma **nova definição de versão** e, em seguida, selecione uma definição vazia. Renomeie o ambiente recém-criado para algo relevante para seu pipeline.

    ![Captura de tela do pipeline de liberação inicial.](media/batch-ci-cd/Release-0.jpg)

1. Crie uma dependência no pipeline de compilação para obter a saída para o aplicativo HPC.

    > [!NOTE]
    > Anote o alias de **origem**, pois isso será necessário quando as tarefas forem criadas dentro da definição de versão.

    ![Captura de tela mostrando um link de artefato para o HPCApplicationPackage no pipeline de compilação apropriado.](media/batch-ci-cd/Release-1.jpg)

1. Crie um link para outro artefato, desta vez, um repositório do Azure. Isso é necessário para acessar os modelos do Resource Manager armazenados em seu repositório. Como os modelos do Resource Manager não exigem build, você não precisa enviá-los por meio do pipeline de build.

    > [!NOTE]
    > Mais uma vez, observe o **alias de origem**, pois isso será necessário mais tarde.

    ![Captura de tela mostrando um link de artefato para o Azure Repos.](media/batch-ci-cd/Release-2.jpg)

1. Navegue até a seção **Variáveis**. Você desejará criar um número de variáveis em seu pipeline para que não precise inserir novamente as mesmas informações em várias tarefas. Este exemplo usa as seguintes variáveis:

   - **applicationStorageAccountName**: nome da conta de armazenamento que contém os binários do aplicativo HPC
   - **batchAccountApplicationName**: nome do aplicativo na conta do lote
   - **batchAccountName**: nome da conta do lote
   - **batchAccountPoolName**: nome do pool de VMs que executa o processamento
   - **batchApplicationId**: ID exclusiva para o aplicativo do lote
   - **batchApplicationVersion**: versão semântica do seu aplicativo do lote (ou seja, os binários do ffmpeg)
   - **local**: local para os recursos do Azure a serem implantados
   - **resourceGroupName**: o nome do grupo de recursos a ser criado e o local em que os recursos serão implantados
   - **storageAccountName**: nome da conta de armazenamento que contém os modelos vinculados do Resource Manager

   ![Captura de tela mostrando as variáveis definidas para a versão Azure Pipelines.](media/batch-ci-cd/Release-4.jpg)

1. Navegue até as tarefas do ambiente de desenvolvimento. No instantâneo abaixo, é possível ver seis tarefas. Essas tarefas vão: baixar os arquivos ffmpeg compactados, implantar uma conta de armazenamento para hospedar os modelos aninhados do Resource Manager, copiar esses modelos do Resource Manager para a conta de armazenamento, implantar a conta do lote e as dependências necessárias, criar um aplicativo na Conta do Lote do Azure e carregar o pacote de aplicativos na Conta do Lote do Azure.

    ![Captura de tela mostrando as tarefas usadas para liberar o aplicativo HPC para o lote do Azure.](media/batch-ci-cd/Release-3.jpg)

1. Adicione a tarefa **Baixar o artefato do pipeline (versão prévia)** e defina as seguintes propriedades:
    - **Nome de exibição:** baixar o ApplicationPackage para o agente
    - **O nome do artefato a ser baixado:** hpc-application
    - **Caminho para baixar**: $(System.DefaultWorkingDirectory)

1. Crie uma conta de armazenamento para armazenar seus modelos de Azure Resource Manager. Uma conta de armazenamento existente da solução pode ser usada, mas para dar suporte a esse exemplo autônomo e isolamento de conteúdo, você fará uma conta de armazenamento dedicada.

    Adicione a tarefa **Implantação do grupo de recursos do Azure** e defina as seguintes propriedades:
    - **Nome para exibição:** Implantar conta de armazenamento para modelos do Resource Manager
    - **Assinatura do Azure:** Selecione a assinatura do Azure apropriada
    - **Ação**: Criar ou atualizar o grupo de recursos
    - **Grupo de recursos**: $(resourceGroupName)
    - **Localização**: $(location)
    - **Modelo**: $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/storageAccount.json
    - **Substitua parâmetros de modelo**: -accountName $(storageAccountName)

1. Carregue os artefatos do controle do código-fonte na conta de armazenamento usando Azure Pipelines. Como parte dessa Azure Pipelines tarefa, o URI do contêiner da conta de armazenamento e o token SAS podem ser enviados para uma variável no Azure Pipelines, permitindo que eles sejam reutilizados em toda esta fase do agente.

    Adicione a tarefa **Cópia de Arquivos do Azure** e defina as seguintes propriedades:
    - **Fonte**: $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/
    - **Tipo de conexão do Azure**: Azure Resource Manager
    - **Assinatura do Azure:** Selecione a assinatura do Azure apropriada
    - **Tipo de destino**: Blob do Azure
    - **Conta de armazenamento do RM**: $(storageAccountName)
    - **Nome do contêiner**: templates
    - **URI do contêiner de armazenamento**: templateContainerUri
    - **Token SAS do contêiner de armazenamento**: templateContainerSasToken

1. Implante o modelo de orquestrador. Este modelo inclui parâmetros para o URI do contêiner da conta de armazenamento e o token SAS. As variáveis necessárias no modelo do Resource Manager são mantidas na seção de variáveis da definição de versão ou foram definidas de outra tarefa de Azure Pipelines (por exemplo, parte da tarefa de cópia de blob do Azure).

    Adicione a tarefa **Implantação do grupo de recursos do Azure** e defina as seguintes propriedades:
    - **Nome de exibição:** implantar o Lote do Azure
    - **Assinatura do Azure:** Selecione a assinatura do Azure apropriada
    - **Ação**: Criar ou atualizar o grupo de recursos
    - **Grupo de recursos**: $(resourceGroupName)
    - **Localização**: $(location)
    - **Modelo**: $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/deployment.json
    - **Substituir parâmetros de modelo**: `-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)`

   Uma prática comum é usar tarefas do Azure Key Vault. Se a entidade de serviço conectada à sua assinatura do Azure tiver um conjunto de políticas de acesso apropriado, ele poderá baixar segredos de um Azure Key Vault e ser usado como variáveis em seu pipeline. O nome do segredo será definido com o valor associado. Por exemplo, um segredo de sshPassword poderia ser referenciado com $ (sshPassword) na definição da versão.

1. As próximas etapas chamam o CLI do Azure. O primeiro é usado para criar um aplicativo no lote do Azure e carregar pacotes associados.

    Adicione a tarefa **CLI do Azure** e defina as propriedades a seguir:
    - **Nome para exibição:** Criar aplicativo na conta do lote do Azure
    - **Assinatura do Azure:** Selecione a assinatura do Azure apropriada
    - **Local do script**: script embutido
    - **Script embutido**: `az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)`

1. A segunda etapa é usada para carregar pacotes associados ao aplicativo (nesse caso, os arquivos ffmpeg).

    Adicione a tarefa **CLI do Azure** e defina as propriedades a seguir:
    - **Nome para exibição:** Carregar pacote na conta do lote do Azure
    - **Assinatura do Azure:** Selecione a assinatura do Azure apropriada
    - **Local do script**: script embutido
    - **Script embutido**: `az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip`

    > [!NOTE]
    > O número de versão do pacote de aplicativos é definido como uma variável. Isso permite substituir as versões anteriores do pacote e permite controlar manualmente o número de versão do pacote enviado por push para o lote do Azure.

1. Crie uma nova versão selecionando **Versão > Criar uma nova versão**. Depois de acionado, selecione o link da nova versão para exibir o status.

1. Exiba a saída ao vivo do agente selecionando o botão **logs** abaixo do seu ambiente.

    ![Captura de tela mostrando o status da versão.](media/batch-ci-cd/Release-5.jpg)

## <a name="test-the-environment"></a>Testar o ambiente

Assim que o ambiente estiver configurado, confirme se os testes a seguir podem ser concluídos com êxito.

Conecte-se à nova Conta de Lote do Azure, usando o CLI do Azure em um prompt de comando do PowerShell.

- Entre na sua conta do Azure com `az login` e siga as instruções para autenticar.
- Agora autentique a conta do Lote: `az batch account login -g <resourceGroup> -n <batchAccount>`

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

Consulte estes tutoriais para saber como interagir com uma conta do lote por meio de um aplicativo simples.

- [Executar uma carga de trabalho paralela com o Lote do Azure usando a API do Python](tutorial-parallel-python.md)
- [Executar uma carga de trabalho paralela com o Lote do Azure usando a API do .NET](tutorial-parallel-dotnet.md)
