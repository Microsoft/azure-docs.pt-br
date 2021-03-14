---
title: Instalar e usar a CLI do Azure Machine Learning
description: Saiba como usar a extensão de CLI do Azure para o ML para criar & gerenciar recursos, como seu espaço de trabalho, repositórios de armazenamento, conjuntos de informações, pipelines, modelos e implantações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jordane
author: jpe316
ms.date: 06/22/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3e073310d62bfb772ea1120bd379cdc277137da0
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519105"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Instalar e usar a extensão CLI do Azure Machine Learning


A CLI do Azure Machine Learning é uma extensão do [Azure CLI](/cli/azure/), uma interface de linha de comando de plataforma cruzada para a plataforma Azure. Essa extensão fornece comandos para trabalhar com o Azure Machine Learning. Ela permite automatizar suas atividades de machine learning. A seguinte lista fornece algumas ações de exemplo que você pode fazer com a extensão CLI:

+ Executar experimentos para criar modelos de aprendizado de máquina

+ Registrar modelos de aprendizado de máquina para uso do cliente

+ Empacote, implante e rastreie o ciclo de vida de seus modelos de aprendizado de máquina

O CLI não é um substituto para o SDK do Azure Machine Learning. É uma ferramenta complementar que é otimizada para lidar com tarefas altamente parametrizadas, que se adaptam bem à automação.

## <a name="prerequisites"></a>Pré-requisitos

* Para usar a CLI, você deve ter uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para usar os comandos da CLI deste documento em seu **ambiente local**, você precisará da [CLI do Azure](/cli/azure/install-azure-cli).

    Se você usar o [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), a CLI será acessada por meio do navegador e residirá na nuvem.

## <a name="full-reference-docs"></a>Documentos de referência completos

Localize os [documentos de referência completos para a extensão azure-cli-ml da CLI do Azure](/cli/azure/ext/azure-cli-ml/).

## <a name="connect-the-cli-to-your-azure-subscription"></a>Conectar a CLI à assinatura do Azure

> [!IMPORTANT]
> Se você estiver usando o Azure Cloud Shell, ignore esta seção. O Cloud Shell autentica automaticamente você usando a conta que você faz logon em sua assinatura do Azure.

Há várias maneiras de se autenticar em sua assinatura do Azure por meio da CLI. O mais básico é autenticar-se interativamente usando um navegador. Para se autenticar interativamente, abra uma linha de comando ou terminal e use o seguinte comando:

```azurecli-interactive
az login
```

Se a CLI pode abrir seu navegador padrão, ela irá fazê-lo e carregar uma página de entrada. Caso contrário, você precisará abrir um navegador e seguir as instruções na linha de comando. As instruções envolvem a navegação para [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e a inserção de um código de autorização.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Para outros métodos de autenticação, confira [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli).

## <a name="install-the-extension"></a>Instalar a extensão

Para instalar a extensão CLI do Machine Learning, use o seguinte comando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Os arquivos de exemplo que você pode usar com os comandos abaixo podem ser encontrados [aqui](https://aka.ms/azml-deploy-cloud).

Quando solicitado, selecione `y` para instalar a extensão.

Para verificar se a extensão foi instalada, use o seguinte comando para exibir uma lista de subcomandos específicos do ML:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Atualizar a extensão

Para atualizar a extensão CLI do Machine Learning, use o seguinte comando:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Remover a extensão

Para remover a extensão CLI, use o seguinte comando:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Gerenciamento de recursos

Os comandos a seguir demonstram como usar a CLI para gerenciar recursos usados pelo Azure Machine Learning.

+ Se você ainda não tiver um, crie um grupo de recursos:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Criar um workspace do Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Para obter mais informações, consulte [az ml workspace create](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-create).

+ Anexe uma configuração de workspace a uma pasta para habilitar a conscientização contextual da CLI.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Este comando cria um subdiretório `.azureml` que contém os arquivos de ambiente runconfig e conda de exemplo. Ele também contém um arquivo `config.json` que é usado para se comunicar com seu workspace do Azure Machine Learning.

    Para obter mais informações, consulte [az ml folder attach](/cli/azure/ext/azure-cli-ml/ml/folder#ext-azure-cli-ml-az-ml-folder-attach).

+ Anexe um contêiner de Blob do Azure como um armazenamento de dados.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Para obter mais informações, consulte [az ml datastore attach-blob](/cli/azure/ext/azure-cli-ml/ml/datastore#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Carregue arquivos em um armazenamento de dados.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Para obter mais informações, consulte [az ml datastore upload](/cli/azure/ext/azure-cli-ml/ml/datastore#ext-azure-cli-ml-az-ml-datastore-upload).

+ Anexe um cluster do AKS como um destino de computação.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Para obter mais informações, consulte [az ml computetarget attach aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

### <a name="compute-clusters"></a>Clusters de cálculo

+ Crie um novo cluster de computação gerenciado.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ Criar um novo cluster de computação gerenciado com identidade gerenciada

  + Identidade gerenciada atribuída pelo usuário

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + Identidade gerenciada atribuída pelo sistema

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ Adicione uma identidade gerenciada a um cluster existente:

    + Identidade gerenciada atribuída pelo usuário
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + Identidade gerenciada atribuída pelo sistema

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

Para obter mais informações, consulte [az ml computetarget create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>Instância de computação
Gerenciar instâncias de computação.  Em todos os exemplos abaixo, o nome da instância de computação é **CPU**

+ Crie um novo computeinstance.

    ```azurecli-interactive
    az ml computetarget create computeinstance -n cpu -s "STANDARD_D3_V2" -v
    ```

    Para obter mais informações, consulte [AZ ml computetarget Create computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-computeinstance).

+ Parar um computeinstance.

    ```azurecli-interactive
    az ml computetarget computeinstance stop -n cpu -v
    ```

    Para obter mais informações, consulte [AZ ml computetarget computeinstance Stop](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop).

+ Inicie um computeinstance.

    ```azurecli-interactive
    az ml computetarget computeinstance start -n cpu -v
    ```

    Para obter mais informações, consulte [AZ ml computetarget computeinstance Start](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start).

+ Reinicie um computeinstance.

    ```azurecli-interactive
    az ml computetarget computeinstance restart -n cpu -v
    ```

    Para obter mais informações, consulte [AZ ml computetarget computeinstance Restart](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart).

+ Excluir um computeinstance.

    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```

    Para obter mais informações, consulte [AZ ml computetarget Delete computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget#ext-azure-cli-ml-az-ml-computetarget-delete).


## <a name="run-experiments"></a><a id="experiments"></a>Executar experimentos

* Comece uma corrida de sua experiência. Ao usar esse comando, especifique o nome do arquivo runconfig (o texto antes de \*.runconfig se você estiver olhando seu sistema de arquivos) em relação ao parâmetro -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > O comando `az ml folder attach` cria um subdiretório `.azureml`, que contém dois arquivos de exemplo de runconfig. 
    >
    > Se você tiver um script Python que cria um objeto de configuração de execução programaticamente, poderá usar [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) para salvá-lo como um arquivo runconfig.
    >
    > O esquema runconfig completo pode ser encontrado neste [arquivo JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). O esquema está autodocumentando por meio da chave `description` de cada objeto. Além disso, há enumerações para valores possíveis e um snippet de modelo no final.

    Para obter mais informações, consulte [az ml run submit-script](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-submit-script).

* Exibir uma lista de experimentos:

    ```azurecli-interactive
    az ml experiment list
    ```

    Para obter mais informações, consulte [az ml experiment list](/cli/azure/ext/azure-cli-ml/ml/experiment#ext-azure-cli-ml-az-ml-experiment-list).

### <a name="hyperdrive-run"></a>Execução de HyperDrive

Você pode usar o HyperDrive com a CLI do Azure para realizar as execuções de ajuste de parâmetro. Primeiro, crie um arquivo de configuração HyperDrive no formato a seguir. Consulte o artigo [Ajustar hiperparâmetros para seu modelo](how-to-tune-hyperparameters.md) para obter detalhes sobre parâmetros de ajuste de hiperparâmetro.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Adicione esse arquivo junto com os arquivos de configuração de execução. Em seguida, envie uma execução de HyperDrive usando:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Observe a seção *argumentos*  no runconfig e o *espaço de parâmetro* na configuração do HyperDrive. Eles contêm os argumentos de linha de comando a serem passados ao script de treinamento. O valor em runconfig permanece o mesmo em cada iteração, enquanto o intervalo na configuração de HyperDrive é iterado. Não especifique o mesmo argumento em ambos os arquivos.

## <a name="dataset-management"></a>Gerenciamento de conjunto de dados

Os seguintes comandos demonstram como trabalhar com conjuntos de dados no Azure Machine Learning:

+ Registrar um conjunto de dados:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Para obter informações sobre o formato do arquivo JSON usado para definir o conjunto de dados, use `az ml dataset register --show-template`.

    Para obter mais informações, consulte [az ml dataset register](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-register).

+ Listar todos os conjuntos de dados em um workspace:

    ```azurecli-interactive
    az ml dataset list
    ```

    Para obter mais informações, consulte [az ml dataset list](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-list).

+ Obter detalhes de um conjunto de dados:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Para obter mais informações, consulte [az ml dataset show](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-show).

+ Cancelar o registro de um conjunto de dados:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Para obter mais informações, consulte [az ml dataset unregister](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Gerenciamento de ambiente

Os seguintes comandos demonstram como criar, registrar e listar [ambientes](how-to-configure-environment.md) do Azure Machine Learning para seu workspace:

+ Criar arquivos scaffolding para um ambiente:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Para obter mais informações, consulte [az ml environment scaffold](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Registrar um ambiente:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Para obter mais informações, consulte [az ml environment register](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-register).

+ Listar ambientes registrados:

    ```azurecli-interactive
    az ml environment list
    ```

    Para obter mais informações, consulte [az ml environment list](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-list).

+ Baixar um ambiente registrado:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Para obter mais informações, consulte [az ml environment download](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Esquema de configuração de ambiente

Se você usou o comando `az ml environment scaffold`, ele gera um arquivo `azureml_environment.json` de modelo que pode ser modificado e usado para criar configurações de ambiente personalizadas com a CLI. O objeto de nível superior é mapeado de modo flexível para a classe [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) no SDK do Python. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

A tabela a seguir detalha cada campo de nível superior no arquivo JSON, bem como o tipo e uma descrição. Se um tipo de objeto estiver vinculado a uma classe do SDK do Python, haverá uma correspondência de 1:1 flexível entre cada campo JSON e o nome da variável pública na classe Python. Em alguns casos, o campo pode ser mapeado para um argumento de construtor em vez de uma variável de classe. Por exemplo, o campo `environmentVariables` é mapeado para a variável `environment_variables` na classe [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29).

| Campo JSON | Type | Descrição |
|---|---|---|
| `name` | `string` | Nome do ambiente. Não inicie o nome com **Microsoft** ou **AzureML**. |
| `version` | `string` | Versão do ambiente. |
| `environmentVariables` | `{string: string}` | Um mapa de hash de valores e nomes de variável de ambiente. |
| `python` | [`PythonSection`](/python/api/azureml-core/azureml.core.environment.pythonsection)o Hat define o ambiente e o intérprete do Python para usar no recurso de computação de destino. |
| `docker` | [`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection) | Define as configurações para personalizar a imagem do Docker criada para as especificações do ambiente. |
| `spark` | [`SparkSection`](/python/api/azureml-core/azureml.core.environment.sparksection) | A seção define as configurações do Spark. Ela só é usada quando a estrutura é definida como PySpark. |
| `databricks` | [`DatabricksSection`](/python/api/azureml-core/azureml.core.databricks.databrickssection) | Configura as dependências da biblioteca do Databricks. |
| `inferencingStackVersion` | `string` | Especifica a versão da pilha de inferência adicionada à imagem. Para evitar a adição de uma pilha de inferência, deixe esse campo `null`. Valor válido: "latest". |

## <a name="ml-pipeline-management"></a>Gerenciamento de pipeline do ML

Os seguintes comandos demonstram como trabalhar com pipelines de machine learning:

+ Criar um pipeline de machine learning:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Para obter mais informações, consulte [az ml pipeline create](/cli/azure/ext/azure-cli-ml/ml/pipeline#ext-azure-cli-ml-az-ml-pipeline-create).

    Para obter mais informações sobre o arquivo YAML do pipeline, consulte [Definir pipelines de machine learning no YAML](reference-pipeline-yaml.md).

+ Executar um pipeline:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Para obter mais informações, consulte [az ml run submit-pipeline](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Para obter mais informações sobre o arquivo YAML do pipeline, consulte [Definir pipelines de machine learning no YAML](reference-pipeline-yaml.md).

+ Agendar um pipeline:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Para obter mais informações, consulte [az ml pipeline create-schedule](/cli/azure/ext/azure-cli-ml/ml/pipeline#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Para obter mais informações sobre o arquivo YAML de agendamento do pipeline, consulte [Definir pipelines de machine learning no YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Registro de modelo, criação de perfil, implantação

Os comandos a seguir demonstram como registrar um modelo treinado e implantá-lo como um serviço de produção:

+ Registre um modelo com o Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Para obter mais informações, consulte [az ml model register](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-register).

+ **OPCIONAL** Analise seu modelo para obter valores de CPU e memória ideais para implantação.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Para obter mais informações, consulte [az ml model profile](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-profile).

+ Implantar seu modelo no AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Para obter mais informações sobre o esquema do arquivo de configuração de inferência, consulte [Esquema de configuração de inferência](#inferenceconfig).
    
    Para obter mais informações sobre o esquema do arquivo de configuração de implantação, consulte [Esquema de configuração de implantação](#deploymentconfig).

    Para obter mais informações, consulte [az ml model deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Esquema de configuração de inferência

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Esquema de configuração de implantação

### <a name="local-deployment-configuration-schema"></a>Esquema de configuração de implantação local

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Esquema de configuração de implantação da Instância de Contêiner do Azure 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Esquema de configuração de implantação do Serviço de Kubernetes do Azure

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Próximas etapas

* [Referência de comando para a extensão CLI do Machine Learning](/cli/azure/ext/azure-cli-ml/ml).

* [Treinar e implantar modelos de machine learning usando o Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
