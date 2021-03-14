---
title: GitHub Actions para CI/CD
titleSuffix: Azure Machine Learning
description: Saiba mais sobre como criar um fluxo de trabalho de ações do GitHub para treinar um modelo no Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: b21f53f8ec76257fc19e0e30cd025ecc46ad2188
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218274"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>Usar GitHub Actions com o Azure Machine Learning

Introdução às [ações do GitHub](https://docs.github.com/en/actions) para treinar um modelo em Azure Machine Learning. 

> [!NOTE]
> As ações do GitHub para Azure Machine Learning são fornecidas no estado em que se encontram e não têm suporte total da Microsoft. Se você encontrar problemas com uma ação específica, abra um problema no repositório para a ação. Por exemplo, se você encontrar um problema com a ação AML-Deploy, relate o problema no [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) repositório.

## <a name="prerequisites"></a>Pré-requisitos 

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma conta do GitHub. Caso ainda não tenha uma, inscreva-se [gratuitamente](https://github.com/join).  

## <a name="workflow-file-overview"></a>Visão geral do arquivo do fluxo de trabalho

Um fluxo de trabalho é definido por um arquivo YAML (.yml) no caminho `/.github/workflows/` no repositório. Essa definição contém as várias etapas e os parâmetros que compõem o fluxo de trabalho.

O arquivo tem quatro seções:

|Seção  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Definir uma entidade de serviço. <br /> 2. Criar um segredo do GitHub. |
|**Connect** | 1. Conecte-se ao espaço de trabalho do Machine Learning. <br /> 2. Conecte-se a um destino de computação. |
|**Executar** | 1. envie uma execução de treinamento. |
|**Implantar** | 1. Registre o modelo no registro Azure Machine Learning. 1. Implantar o modelo. |

## <a name="create-repository"></a>Criar repositório

Crie um novo repositório fora das [operações do ml com ações do GitHub e Azure Machine Learning modelo](https://github.com/machine-learning-apps/ml-template-azure). 

1. Abra o [modelo](https://github.com/machine-learning-apps/ml-template-azure) no github. 
2. Selecione **Usar este modelo**. 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="Selecione usar este modelo":::
3. Crie um novo repositório a partir do modelo. Defina o nome do repositório como `ml-learning` ou como um nome de sua escolha. 


## <a name="generate-deployment-credentials"></a>Gerar as credenciais de implantação

Crie uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) com o comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) na [CLI do Azure](/cli/azure/). Execute esse comando com o [Azure Cloud Shell](https://shell.azure.com/) no portal do Azure ou selecionando o botão **Experimentar**.

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
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

## <a name="configure-the-github-secret"></a>Configurar o segredo do GitHub

1. No [GitHub](https://github.com/), procure seu repositório, selecione **configurações > segredos > adicionar um novo segredo**.

2. Cole toda a saída JSON do comando da CLI do Azure no campo valor do segredo. Dê ao segredo o nome `AZURE_CREDENTIALS`.

## <a name="connect-to-the-workspace"></a>Conectar-se ao workspace

Use a [ação Workspace do Azure Machine Learning](https://github.com/marketplace/actions/azure-machine-learning-workspace) para se conectar ao seu espaço de trabalho do Azure Machine Learning. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

Por padrão, a ação espera um `workspace.json` arquivo. Se o arquivo JSON tiver um nome diferente, você poderá especificá-lo com o `parameters_file` parâmetro de entrada. Se não houver um arquivo, um novo será criado com o nome do repositório.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
A ação grava as propriedades do Azure Resource Manager do espaço de trabalho (ARM) em um arquivo de configuração, que será escolhido por todas as ações futuras do GitHub Azure Machine Learning. O arquivo é salvo em `GITHUB_WORKSPACE/aml_arm_config.json` . 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Conectar-se a um destino de computação no Azure Machine Learning

Use a [ação de computação Azure Machine Learning](https://github.com/Azure/aml-compute) para se conectar a um destino de computação no Azure Machine Learning.  Se o destino de computação existir, a ação será conectada a ele. Caso contrário, a ação criará um novo destino de computação. A [ação de computação AML](https://github.com/Azure/aml-compute) só dá suporte ao cluster de computação do Azure ml e ao AKs (serviço kubernetes do Azure). 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>Enviar execução de treinamento

Use a [ação de treinamento Azure Machine Learning](https://github.com/Azure/aml-run) para enviar um ScriptRun, um estimador ou um Pipeline para Azure Machine Learning. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>Registrar modelo no registro

Use a [ação de modelo de registro Azure Machine Learning](https://github.com/Azure/aml-registermodel) para registrar um modelo para Azure Machine Learning.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Implantar modelo para Azure Machine Learning para ACI

Use a [ação Azure Machine Learning implantar](https://github.com/Azure/aml-deploy) para implantar um modelo e criar um ponto de extremidade para o modelo. Você também pode usar o Azure Machine Learning implantar para implantar no serviço kubernetes do Azure. Consulte [este fluxo de trabalho de exemplo](https://github.com/Azure-Samples/mlops-enterprise-template) para um modelo que é implantado no serviço kubernetes do Azure.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>Exemplo completo

Treine seu modelo e implante no Azure Machine Learning. 

```yaml
# Actions train a model on Azure Machine Learning
name: Azure Machine Learning training and deployment
on:
  push:
    branches:
      - master
    # paths:
    #   - 'code/*'
jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Check Out Repository
      id: checkout_repository
      uses: actions/checkout@v2
        
    # Connect or Create the Azure Machine Learning Workspace
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Connect or Create a Compute Target in Azure Machine Learning
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Submit a training run to the Azure Machine Learning
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}

    # Register model in Azure Machine Learning model registry
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}

    # Deploy model in Azure Machine Learning to ACI
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o grupo de recursos e o repositório não forem mais necessários, limpe os recursos implantados excluindo o grupo de recursos e seu repositório GitHub. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Crie e execute pipelines de machine learning com o SDK do Azure Machine Learning](./how-to-create-machine-learning-pipelines.md)