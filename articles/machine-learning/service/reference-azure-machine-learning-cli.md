---
title: Extensão de aprendizado de máquina
titleSuffix: Azure Machine Learning service
description: Aprenda sobre a extensão CLI do Azure Machine Learning para a CLI do Azure. A CLI do Azure é um utilitário de linha de comando de plataforma cruzada que permite trabalhar com recursos na nuvem do Azure. A extensão Machine Learning permite que você trabalhe com o Serviço de Aprendizado de Máquina do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 2504ca9cb785529a9eab321c2521db46390632b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60752982"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Use a extensão CLI do Serviço do Azure Machine Learning

A CLI do Azure Machine Learning é uma extensão do [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), uma interface de linha de comando de plataforma cruzada para a plataforma Azure. Esta extensão fornece comandos para trabalhar com o serviço do Machine Learning na linha de comando. Ele permite que você crie scripts que automatizam seus fluxos de trabalho de aprendizado de máquina. Por exemplo, você pode criar scripts que executam as seguintes ações:

+ Executar experimentos para criar modelos de aprendizado de máquina

+ Registrar modelos de aprendizado de máquina para uso do cliente

+ Empacote, implante e rastreie o ciclo de vida de seus modelos de aprendizado de máquina

O CLI não é um substituto para o SDK do Azure Machine Learning. É uma ferramenta complementar que é otimizada para lidar com tarefas altamente parametrizadas, como:

* Criando recursos de computação

* Submissão de experimentos parametrizados

* Registro do modelo

* Criação de imagem

* Implantação de serviço

## <a name="prerequisites"></a>Pré-requisitos


* Para usar a CLI, você deve ter uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* O [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalar a extensão

Para instalar a extensão CLI do Machine Learning, use o seguinte comando:

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-1.0.10-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

Quando solicitado, selecione `y` para instalar a extensão.

Para verificar se a extensão foi instalada, use o seguinte comando para exibir uma lista de subcomandos específicos do ML:

```azurecli-interactive
az ml -h
```

> [!TIP]
> Para atualizar a extensão, você deve __remover__ e, em seguida, __instalá-la__. Isso instala a versão mais recente.

## <a name="remove-the-extension"></a>Remover a extensão

Para remover a extensão CLI, use o seguinte comando:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Gerenciamento de recursos

Os comandos a seguir demonstram como usar a CLI para gerenciar recursos usados pelo Azure Machine Learning.


+ Crie uma área de trabalho do Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -n myworkspace -g myresourcegroup
    ```

+ Defina um espaço de trabalho padrão:

    ```azurecli-interactive
    az configure --defaults aml_workspace=myworkspace group=myresourcegroup
    ```
    
+ Anexar um cluster AKS

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a name="experiments"></a>Testes

Os comandos a seguir demonstram como usar a CLI para trabalhar com experimentos:

* Anexe um projeto (execute a configuração) antes de enviar uma experiência:

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* Comece uma corrida de sua experiência. Ao usar esse comando, especifique o nome do arquivo runconfig que contém a configuração de execução. O destino de computação usa a configuração de execução para criar o ambiente de treinamento para o modelo. Neste exemplo, a configuração de execução é carregada do arquivo `./aml_config/myrunconfig.runconfig`.

    ```azurecli-interactive
    az ml run submit -c myrunconfig train.py
    ```

    Para obter mais informações sobre o arquivo runconfig, confira a seção [RunConfig](#runconfig).

* Exiba uma lista dos experimentos enviados:

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>Registro de modelo, criação e implantação de imagem

Os comandos a seguir demonstram como registrar um modelo treinado e implantá-lo como um serviço de produção:

+ Registre um modelo com o Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Crie uma imagem que contenha seu modelo e dependências de aprendizado de máquina: 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Implante uma imagem em um destino de computação:

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```

## <a id="runconfig"></a> Arquivo runconfig

Uma configuração de execução é usada para configurar o ambiente de treinamento usado para treinar seu modelo. Essa configuração pode ser criada na memória usando o SDK ou carregada de um arquivo runconfig.

O arquivo runconfig é um documento de texto que descreve a configuração para o ambiente de treinamento. Por exemplo, ele lista o nome do script de treinamento e o arquivo que contém as dependências de conda necessárias para treinar o modelo.

A CLI do Azure Machine Learning cria dois arquivos `.runconfig` padrão denominados `docker.runconfig` e `local.runconfig` quando você anexa a um projeto usando o comando `az ml project attach`. 

Se você tiver um código que cria uma configuração de execução usando a classe [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py), poderá usar o método `save()` para mantê-lo em um arquivo `.runconfig`.

O seguinte é um exemplo do conteúdo de um arquivo `.runconfig`:

```text
# The script to run.
script: train.py
# The arguments to the script file.
arguments: []
# The name of the compute target to use for this run.
target: local
# Framework to execute inside. Allowed values are "Python" ,  "PySpark", "CNTK",  "TensorFlow", and "PyTorch".
framework: PySpark
# Communicator for the given framework. Allowed values are "None" ,  "ParameterServer", "OpenMpi", and "IntelMpi".
communicator: None
# Automatically prepare the run environment as part of the run itself.
autoPrepareEnvironment: true
# Maximum allowed duration for the run.
maxRunDurationSeconds:
# Number of nodes to use for running job.
nodeCount: 1
# Environment details.
environment:
# Environment variables set for the run.
  environmentVariables:
    EXAMPLE_ENV_VAR: EXAMPLE_VALUE
# Python details
  python:
# user_managed_dependencies=True indicates that the environmentwill be user managed. False indicates that AzureML willmanage the user environment.
    userManagedDependencies: false
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
  docker:
# Set True to perform this run inside a Docker container.
    enabled: true
# Base image used for Docker-based runs.
    baseImage: mcr.microsoft.com/azureml/base:0.2.4
# Set False if necessary to work around shared volume bugs.
    sharedVolumes: true
# Run with NVidia Docker extension to support GPUs.
    gpuSupport: false
# Extra arguments to the Docker run command.
    arguments: []
# Image registry that contains the base image.
    baseImageRegistry:
# DNS name or IP address of azure container registry(ACR)
      address:
# The username for ACR
      username:
# The password for ACR
      password:
# Spark details
  spark:
# List of spark repositories.
    repositories:
    - https://mmlspark.azureedge.net/maven
    packages:
    - group: com.microsoft.ml.spark
      artifact: mmlspark_2.11
      version: '0.12'
    precachePackages: true
# Databricks details
  databricks:
# List of maven libraries.
    mavenLibraries: []
# List of PyPi libraries
    pypiLibraries: []
# List of RCran libraries
    rcranLibraries: []
# List of JAR libraries
    jarLibraries: []
# List of Egg libraries
    eggLibraries: []
# History details.
history:
# Enable history tracking -- this allows status, logs, metrics, and outputs
# to be collected for a run.
  outputCollection: true
# whether to take snapshots for history.
  snapshotProject: true
# Spark configuration details.
spark:
  configuration:
    spark.app.name: Azure ML Experiment
    spark.yarn.maxAppAttempts: 1
# HDI details.
hdi:
# Yarn deploy mode. Options are cluster and client.
  yarnDeployMode: cluster
# Tensorflow details.
tensorflow:
# The number of worker tasks.
  workerCount: 1
# The number of parameter server tasks.
  parameterServerCount: 1
# Mpi details.
mpi:
# When using MPI, number of processes per node.
  processCountPerNode: 1
# data reference configuration details
dataReferences: {}
# Project share datastore reference.
sourceDirectoryDataStore:
# AmlCompute details.
amlcompute:
# VM size of the Cluster to be created.Allowed values are Azure vm sizes. The list of vm sizes is available in 'https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs
  vmSize:
# VM priority of the Cluster to be created.Allowed values are "dedicated" , "lowpriority".
  vmPriority:
# A bool that indicates if the cluster has to be retained after job completion.
  retainCluster: false
# Name of the cluster to be created. If not specified, runId will be used as cluster name.
  name:
# Maximum number of nodes in the AmlCompute cluster to be created. Minimum number of nodes will always be set to 0.
  clusterMaxNodeCount: 1
```
