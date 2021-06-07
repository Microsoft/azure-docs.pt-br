---
title: Treinar e implantar modelos da CLI
titleSuffix: Azure Machine Learning
description: Saiba como usar a extensão de machine learning para a CLI do Azure para treinar, registrar e implantar um modelo na linha de comando.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/02/2021
ms.openlocfilehash: e518ea83608472753d3463a980aae169b291d4de
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221289"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Tutorial: Treinar e implantar um modelo da CLI


Neste tutorial, você usa a extensão de machine learning para a CLI do Azure para treinar, registrar e implantar um modelo.

Os scripts de treinamento do Python neste tutorial usam [scikit-learn](https://scikit-learn.org/) para treinar um modelo simples. O foco deste tutorial não está nos scripts nem no modelo, mas no processo de usar a CLI para trabalhar com o Azure Machine Learning.

Saiba como executar as seguintes ações:

> [!div class="checklist"]
> * Instalar a extensão de machine learning
> * Criar um workspace do Azure Machine Learning
> * Criar o recurso de computação usado para treinar o modelo
> * Definir e registrar o conjunto de dados usado para treinar o modelo
> * Iniciar uma execução de treinamento
> * Registrar e baixar um modelo
> * Implantar o modelo como um serviço Web
> * Pontuar os dados usando o serviço Web

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para usar os comandos da CLI deste documento em seu **ambiente local**, você precisará da [CLI do Azure](/cli/azure/install-azure-cli).

    Se você usar o [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), a CLI será acessada por meio do navegador e residirá na nuvem.

## <a name="download-the-example-project"></a>Baixar o projeto de exemplo

Para este tutorial, baixe o projeto [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps). Os arquivos no diretório `examples/cli-train-deploy` são usados pelas etapas neste tutorial.

Para obter uma cópia local dos arquivos, [baixe um arquivo .zip](https://github.com/microsoft/MLOps/archive/master.zip) ou use o comando Git a seguir para clonar o repositório:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Arquivos de treinamento

O diretório `examples/cli-train-deploy` do projeto contém os seguintes arquivos, que são usados ao treinar um modelo:

* `.azureml\mnist.runconfig`: Um arquivo de __configuração de execução__. Esse arquivo define o ambiente de runtime necessário para treinar o modelo. Neste exemplo, ele também monta os dados usados para treinar o modelo no ambiente de treinamento.
* `scripts\train.py`: O script de treinamento. Esse arquivo treina o modelo.
* `scripts\utils.py`: Um arquivo auxiliar usado pelo script de treinamento.
* `.azureml\conda_dependencies.yml`: Define as dependências de software necessárias para executar o script de treinamento.
* `dataset.json`: A definição de conjunto de dados. Usado para registrar o conjunto de dados do MNIST no workspace do Azure Machine Learning.

### <a name="deployment-files"></a>Arquivos de implantação

O repositório contém os seguintes arquivos, que são usados para implantar o modelo treinado como um serviço Web:

* `aciDeploymentConfig.yml`: Um arquivo de __configuração da implantação__. Esse arquivo define o ambiente de hospedagem necessário para o modelo.
* `inferenceConfig.json`: Um arquivo de __configuração de inferência__. Esse arquivo define o ambiente de software usado pelo serviço para pontuar dados com o modelo.
* `score.py`: Um script Python que aceita dados de entrada, pontua-os usando o modelo e, em seguida, retorna uma resposta.
* `scoring-env.yml`: As dependências do conda necessárias para executar o modelo e o script `score.py`.
* `testdata.json`: Um arquivo de dados que pode ser usado para testar o serviço Web implantado.

## <a name="connect-to-your-azure-subscription"></a>Conecte-se à sua assinatura do Azure

Há várias maneiras de se autenticar em sua assinatura do Azure por meio da CLI. O mais simples é autenticar-se interativamente usando um navegador. Para se autenticar interativamente, abra uma linha de comando ou terminal e use o seguinte comando:

```azurecli-interactive
az login
```

Se a CLI pode abrir seu navegador padrão, ela irá fazê-lo e carregar uma página de entrada. Caso contrário, você precisará abrir um navegador e seguir as instruções na linha de comando. As instruções envolvem a navegação para [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e a inserção de um código de autorização.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner de recursos na plataforma do Azure. Ao trabalhar com o Azure Machine Learning, o grupo de recursos conterá seu workspace do Azure Machine Learning. Ele também conterá outros serviços do Azure usados pelo workspace. Por exemplo, se você treinar seu modelo usando um recurso de computação baseado em nuvem, esse recurso será criado no grupo de recursos.

Para __criar um grupo de recursos__, use o comando a seguir. Substitua `<resource-group-name>` pelo nome a ser usado para esse grupo de recursos. Substitua `<location>` pela região do Azure a ser usada para este grupo de recursos:

> [!TIP]
> Você deve selecionar uma região na qual o Azure Machine Learning está disponível. Para obter informações, veja [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

A resposta desse comando é semelhante ao JSON a seguir:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Para obter mais informações sobre como trabalhar com grupos de recursos, confira [az group](/cli/azure/group).

## <a name="create-a-workspace"></a>Criar um workspace

Para criar um workspace, use o comando a seguir. Substitua `<workspace-name>` pelo nome que você deseja usar para esse workspace. Substitua `<resource-group-name>` pelo nome do grupo de recursos:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Conectar o projeto local ao workspace

Em um terminal ou prompt de comando, use os seguintes comandos para alterar os diretórios para o diretório `cli-train-deploy` e, em seguida, conecte-se ao seu workspace:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Esse comando cria um arquivo de `.azureml/config.json`, que contém as informações necessárias para conectar-se ao workspace. O restante dos comandos `az ml` usados neste tutorial usará esse arquivo, de modo que você não precisa adicionar o workspace e o grupo de recursos a todos os comandos.

## <a name="create-the-compute-target-for-training"></a>Criar o destino de computação para treinamento

Este exemplo usa um cluster de Computação do Azure Machine Learning para treinar o modelo. Para criar um cluster de computação, use o seguinte comando:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Este comando cria um novo destino de computação chamado `cpu-cluster`, com um máximo de quatro nós. O tamanho da VM selecionado fornece uma VM com um recurso de GPU. Para obter informações sobre o tamanho da VM, confira [Tipos e tamanhos de VM].

> [!IMPORTANT]
> O nome do destino de computação (`cpu-cluster` nesse caso), é importante. Ele é referenciado pelo arquivo `.azureml/mnist.runconfig` usado na próxima seção.

## <a name="define-the-dataset"></a>Definir o conjunto de dados

Para treinar um modelo, você pode fornecer os dados de treinamento usando um conjunto de dados. Para criar um conjunto de dados com base na CLI, forneça um arquivo de definição de conjunto de dados. O arquivo `dataset.json` fornecido no repositório cria um novo conjunto de dados usando os dados de MNIST. O conjunto de dados que ele cria é denominado `mnist-dataset`.

Para registrar o conjunto de dados usando o arquivo `dataset.json`, use o seguinte comando:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```

> [!IMPORTANT]
> Copie o valor da entrada `id`, pois ele é usado na próxima seção.

Para ver um modelo mais abrangente para um conjunto de dados, use o seguinte comando:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Referenciar o conjunto de dados

Para disponibilizar o conjunto de dados no ambiente de treinamento, você deve referenciá-lo do arquivo runconfig. O arquivo `.azureml/mnist.runconfig` contém as seguintes entradas YAML:

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

Altere o valor da entrada `id` para corresponder ao valor retornado quando você registrou o conjunto de dados. Esse valor é usado para carregar os dados no destino de computação durante o treinamento.

Esse YAML resulta nas seguintes ações durante o treinamento:

* Monta o conjunto de dados (com base na ID do conjunto de dados) no ambiente de treinamento e armazena o caminho para o ponto de montagem na variável de ambiente `mnist`.
* Passa o local dos dados (ponto de montagem) dentro do ambiente de treinamento para o script usando o argumento `--data-folder`.

O arquivo runconfig também contém informações usadas para configurar o ambiente usado pela execução de treinamento. Se você inspecionar esse arquivo, verá que ele faz referência ao destino de computação `cpu-compute` que você criou anteriormente. Ele também lista o número de nós a serem usados ao treinar (`"nodeCount": "4"`) e contém uma seção `"condaDependencies"` que lista os pacotes do Python necessários para executar o script de treinamento.

> [!TIP]
> Embora seja possível criar manualmente um arquivo runconfig, aquele neste exemplo foi criado usando o arquivo `generate-runconfig.py` incluído no repositório. Esse arquivo obtém uma referência ao conjunto de dados registrado, cria uma configuração de execução programaticamente e, em seguida, persiste no arquivo.

Para obter mais informações sobre arquivos de configuração de execução, confira [Usar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md#whats-a-run-configuration). Para obter uma referência completa do JSON, confira [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json).

## <a name="submit-the-training-run"></a>Enviar a execução de treinamento

Para iniciar uma execução de treinamento no destino de computação `cpu-cluster`, use o seguinte comando:

```azurecli-interactive
az ml run submit-script -c mnist -e tutorial-cli --source-directory scripts -t runoutput.json
```

Este comando especifica um nome para o experimento (`tutorial-cli`). O experimento armazena informações sobre essa execução no workspace.

O parâmetro `-c mnist` especifica o arquivo `.azureml/mnist.runconfig`.

O parâmetro `-t` armazena uma referência a essa execução em um arquivo JSON e será usado nas próximas etapas para registrar e baixar o modelo.

Como os processos de execução de treinamento, ele transmite informações da sessão de treinamento no recurso de computação remota. A parte da informação é semelhantes ao texto a seguir:

```output
Predict the test set
Accuracy is 0.9185
```

Esse texto é registrado no script de treinamento e exibe a precisão do modelo. Outros modelos terão métricas de desempenho diferentes.

Se você inspecionar o script de treinamento, verá que ele também usa o valor alfa ao armazenar o modelo treinado para `outputs/sklearn_mnist_model.pkl`.

O modelo foi salvo no diretório `./outputs` no destino de computação em que foi treinado. Nesse caso, a instância de Computação do Azure Machine Learning na nuvem do Azure. O processo de treinamento carrega automaticamente o conteúdo do diretório `./outputs` do destino de computação em que o treinamento ocorre em seu workspace do Azure Machine Learning. Ele é armazenado como parte do experimento (`tutorial-cli` neste exemplo).

## <a name="register-the-model"></a>Registre o modelo

Para registrar o modelo diretamente da versão armazenada em seu experimento, use o seguinte comando:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Esse comando registra o arquivo de `outputs/sklearn_mnist_model.pkl` criado pela execução de treinamento como um novo registro de modelo chamado `mymodel`. O `--assets-path` faz referência a um caminho em um experimento. Nesse caso, as informações de teste e execução são carregadas do arquivo `runoutput.json` criado pelo comando de treinamento. O `-t registeredmodel.json` cria um arquivo JSON que faz referência ao novo modelo registrado criado por esse comando e é usado por outros comandos da CLI que funcionam com modelos registrados.

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "tutorial-cli",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "tutorial-cli_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Controle de versão de modelo

Observe o número de versão retornado para o modelo. A versão é incrementada sempre que você registra um novo modelo com esse nome. Por exemplo, você pode baixar o modelo e registrá-lo de um arquivo local usando os seguintes comandos:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

O primeiro comando baixa o modelo registrado no diretório atual. O nome do arquivo é `sklearn_mnist_model.pkl`, que é o arquivo referenciado quando você registrou o modelo. O segundo comando registra o modelo local (`-p "sklearn_mnist_model.pkl"`) com o mesmo nome do registro anterior (`mymodel`). Desta vez, os dados JSON retornados listam a versão como 2.

## <a name="deploy-the-model"></a>Implantar o modelo

Para implantar um modelo, use o seguinte comando:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Você pode receber um aviso sobre "falha ao verificar a existência de LocalWebservice" ou "falha ao criar o cliente Docker". Você pode ignorá-lo com segurança, pois não está implantando um serviço Web local.

Este comando implanta um novo serviço chamado `myservice`, usando a versão 1 do modelo que você registrou anteriormente.

O arquivo `inferenceConfig.yml` fornece informações sobre como usar o modelo para inferência. Por exemplo, ele faz referência ao script de entrada (`score.py`) e às dependências de software.

Para obter mais informações sobre a estrutura desse arquivo, confira o [Esquema de configuração de inferência](reference-azure-machine-learning-cli.md#inference-configuration-schema). Para obter mais informações sobre scripts de entrada, confira [Implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

O `aciDeploymentConfig.yml` descreve o ambiente de implantação usado para hospedar o serviço. A configuração de implantação é específica para o tipo de computação que você usa para a implantação. Nesse caso, uma instância de contêiner do Azure é usada. Para obter mais informações, confira o [Esquema de configuração de implantação](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Concluir o processo de implantação levará vários minutos.

> [!TIP]
> Neste exemplo, as Instâncias de Contêiner do Azure são usadas. As implantações no ACI criam automaticamente o recurso de ACI necessário. Se, em vez disso, você fosse implantar no Serviço de Kubernetes do Azure, deveria criar um cluster do AKS antecipadamente e especificá-lo como parte do comando `az ml model deploy`. Para obter um exemplo de implantação no AKS, confira [Implantar um modelo em um cluster do serviço de Kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md).

Após alguns minutos, informações semelhantes ao JSON a seguir são retornadas:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>O URI de pontuação

O `scoringUri` retornado da implantação é o ponto de extremidade REST para um modelo implantado como um serviço Web. Você também pode obter esse URI usando o seguinte comando:

```azurecli-interactive
az ml service show -n myservice
```

Esse comando retorna o mesmo documento JSON, incluindo o `scoringUri`.

O ponto de extremidade REST pode ser usado para enviar dados para o serviço. Para obter informações sobre como criar um aplicativo cliente que envia dados para o serviço, confira [Consumir um modelo do Azure Machine Learning implantado como um serviço Web](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>Enviar dados para o serviço

Embora você possa criar um aplicativo cliente para chamar o ponto de extremidade, a CLI do Machine Learning fornece um utilitário que pode atuar como um cliente de teste. Use o comando a seguir para enviar dados no arquivo `testdata.json` para o serviço:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Se você usa o PowerShell, use o seguinte comando:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

A resposta do comando é semelhante a `[ 3 ]`.

## <a name="clean-up-resources"></a>Limpar os recursos

> [!IMPORTANT]
> Os recursos que você criou podem ser usados como pré-requisitos em outros tutoriais e artigos de instruções do Azure Machine Learning.

### <a name="delete-deployed-service"></a>Excluir serviços implantados

Se você planeja continuar a usar o workspace do Azure Machine Learning, mas deseja livrar-se do serviço implantado para reduzir os custos, use o seguinte comando:

```azurecli-interactive
az ml service delete -n myservice
```

Esse comando retorna um documento JSON que contém o nome do serviço excluído. Pode levar vários minutos para que o serviço seja excluído.

### <a name="delete-the-training-compute"></a>Excluir a computação de treinamento

Se você planeja continuar a usar o workspace do Azure Machine Learning, mas deseja livrar-se do destino de computação `cpu-cluster` criado para treinamento, use o seguinte comando:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

Esse comando retorna um documento JSON que contém a ID do destino de computação excluído. Excluir o destino de computação pode levar vários minutos.

### <a name="delete-everything"></a>Excluir tudo

Se você não planeja usar os recursos criados, exclua-os para não gerar encargos adicionais.

Para excluir o grupo de recursos e todos os recursos do Azure criados neste documento, use o comando a seguir. Substitua `<resource-group-name>` pelo nome do grupo de recursos que você criou antes:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial do Azure Machine Learning, você usou a CLI de machine learning para as seguintes tarefas:

> [!div class="checklist"]
> * Instalar a extensão de machine learning
> * Criar um workspace do Azure Machine Learning
> * Criar o recurso de computação usado para treinar o modelo
> * Definir e registrar o conjunto de dados usado para treinar o modelo
> * Iniciar uma execução de treinamento
> * Registrar e baixar um modelo
> * Implantar o modelo como um serviço Web
> * Pontuar os dados usando o serviço Web

Para obter mais informações sobre como usar a CLI, confira [Usar a extensão da CLI para o Azure Machine Learning](reference-azure-machine-learning-cli.md).