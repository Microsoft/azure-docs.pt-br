---
title: Configurar destinos de computação de inferência de & de treinamento
titleSuffix: Azure Machine Learning
description: Adicionar recursos de computação (destinos de computação) ao seu espaço de trabalho para usar o treinamento e a inferência de Machine Learning
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/02/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 850b590ae8aeab822367714fb9a56661306c2387
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518684"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>Configurar destinos de computação para treinamento e implantação do modelo

Saiba como anexar recursos de computação do Azure ao seu espaço de trabalho Azure Machine Learning.  Em seguida, você pode usar esses recursos como [metas de computação](concept-compute-target.md) de treinamento e inferência em suas tarefas de aprendizado de máquina.

Neste artigo, saiba como configurar seu espaço de trabalho para usar estes recursos de computação:

* Seu computador local
* Máquinas virtuais remotas
* Azure HDInsight
* Lote do Azure
* Azure Databricks
* Análise Azure Data Lake
* Azure Container Instance

Para usar destinos de computação gerenciados pelo Azure Machine Learning, consulte:


* [Instância de computação do Azure Machine Learning](how-to-create-manage-compute-instance.md)
* [Cluster de computação do Azure Machine Learning](how-to-create-attach-compute-cluster.md)
* [Cluster do serviço kubernetes do Azure](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).

* A [extensão CLI do Azure para o serviço Machine Learning](reference-azure-machine-learning-cli.md), o [SDK do Azure Machine Learning Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)ou a [extensão Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

## <a name="limitations"></a>Limitações

* Não **crie vários anexos simultâneos para a mesma computação** do seu espaço de trabalho. Por exemplo, anexar um cluster do serviço kubernetes do Azure a um espaço de trabalho usando dois nomes diferentes. Cada novo anexo interromperá os anexos existentes anteriores.

    Se você quiser reanexar um destino de computação, por exemplo, para alterar o TLS ou outra configuração de cluster, primeiro você deve remover o anexo existente.

## <a name="whats-a-compute-target"></a>O que é um destino de computação?

Com o Azure Machine Learning, você pode treinar seu modelo em uma variedade de recursos ou ambientes, coletivamente denominados [__destinos de computação__](concept-azure-machine-learning-architecture.md#compute-targets). Um destino de computação pode ser um computador local ou um recurso de nuvem, como Computação do Azure Machine Learning, Azure HDInsight ou uma máquina virtual remota.  Você também usa destinos de computação para implantação de modelo, conforme descrito em ["onde e como implantar seus modelos"](how-to-deploy-and-where.md).


## <a name="local-computer"></a><a id="local"></a>Computador local

Quando você usa seu computador local para **treinamento**, não é necessário criar um destino de computação.  Basta [enviar a execução de treinamento](how-to-set-up-training-targets.md) do computador local.

Ao usar seu computador local para **inferência**, você deve ter o Docker instalado. Para executar a implantação, use [LocalWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.local.localwebservice?preserve-view=true&view=azure-ml-py#deploy-configuration-port-none-) para definir a porta que será usada pelo serviço Web. Em seguida, use o processo de implantação normal, conforme descrito em [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="remote-virtual-machines"></a><a id="vm"></a>Máquinas virtuais remotas

O Azure Machine Learning também dá suporte para que você traga seu próprio recurso de computação e anexe-o ao workspace. Um desses tipos de recursos é uma VM remota arbitrária, desde que seja acessível pelo Azure Machine Learning. O recurso pode ser uma VM do Azure ou um servidor remoto na organização ou no local. Especificamente, fornecido o endereço IP e as credenciais (nome de usuário e senha ou chave SSH), é possível usar qualquer VM acessível para execuções remotas.

Você pode usar um [ambiente Conda criado pelo sistema](how-to-use-environments.md), um ambiente de [Python](how-to-configure-environment.md#local)já existente ou um [contêiner do Docker](https://docs.docker.com/engine/install/ubuntu/). Para executar em um contêiner do Docker, você deve ter um mecanismo do Docker em execução na VM. Essa funcionalidade é especialmente útil quando você quer um ambiente de experimentação/desenvolvimento baseado em nuvem mais flexível que seu computador local.

Use o Máquina Virtual de Ciência de Dados do Azure (DSVM) como a VM do Azure escolhida para esse cenário. Essa VM é uma ciência de dados pré-configuradas e o ambiente de desenvolvimento de inteligência artificial do Azure. A VM oferece uma opção selecionada de ferramentas e estruturas para desenvolvimento do aprendizado de máquina de todo o ciclo de vida. Para saber mais sobre como usar a DSVM com o Azure Machine Learning, consulte [Configurar um ambiente de desenvolvimentopara o Azure Machine Learning](./how-to-configure-environment.md#dsvm).

1. **Criar**: Crie uma DSVM antes de usá-la para treinar seu modelo. Para criar esse recurso, veja [Provisionar a Máquina Virtual de Ciência de Dados para Linux (Ubuntu)](./data-science-virtual-machine/dsvm-ubuntu-intro.md).

    > [!WARNING]
    > Azure Machine Learning só dá suporte a máquinas virtuais que executam o **Ubuntu**. Para criar uma VM ou escolher uma VM existente, selecione uma VM que usa o Ubuntu.
    > 
    > Azure Machine Learning também exige que a máquina virtual tenha um __endereço IP público__.

1. **Anexar**: Para anexar uma máquina virtual existente como um destino de computação, você precisa fornecer a ID do recurso, o nome de usuário e a senha da máquina virtual. A ID de recurso da VM pode ser criada usando a ID da assinatura, o nome do grupo de recursos e o nome da VM com o seguinte formato de cadeia de caracteres: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Outra opção é anexar a DSVM ao seu workspace [usando o estúdio do Azure Machine Learning](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Não crie vários anexos simultâneos para o mesmo DSVM do seu espaço de trabalho. Cada novo anexo interromperá os anexos existentes anteriores.

1. **Configurar**: Crie uma configuração de execução para o destino de computação da DSVM. O Docker e o conda são usados para criar e configurar o ambiente de treinamento na DSVM.

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

O Azure HDInsight é uma plataforma popular para análise de dados. A plataforma fornece o Apache Spark, que pode ser usado para o treinamento do seu modelo.

1. **Criar**:  Crie o cluster HDInsight antes de usá-lo para o treinamento do seu modelo. Para criar um Spark no cluster HDInsight, consulte [Criar um Cluster Spark no HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

    > [!WARNING]
    > Azure Machine Learning requer que o cluster HDInsight tenha um __endereço IP público__.

    Ao criar o cluster, especifique um nome de usuário SSH e senha. Observe esses valores, conforme necessário, ao usar o HDInsight como um destino de computação.
    
    Depois que o cluster for criado, conecte-o com o hostname \<clustername> -SSH.azurehdinsight.net, em que \<clustername> é o nome que você forneceu para o cluster. 

1. **Anexar**: Para anexar a um cluster do HDInsight como um destino de computação, forneça a ID do recurso, o nome de usuário e a senha para o cluster HDInsight. A ID de recurso do cluster HDInsight pode ser criada usando a ID da assinatura, o nome do grupo de recursos e o nome do cluster HDInsight com o seguinte formato de cadeia de caracteres: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Outra opção é anexar o cluster HDInsight ao seu workspace [usando o estúdio do Azure Machine Learning](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Não crie vários anexos simultâneos para o mesmo HDInsight no seu espaço de trabalho. Cada novo anexo interromperá os anexos existentes anteriores.

1. **Configurar**: Crie uma configuração de execução para o destino de computação da HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](how-to-set-up-training-targets.md).

## <a name="azure-batch"></a><a id="azbatch"></a>Lote do Azure 

O Lote do Azure é usado para executar com eficiência aplicativos HPC (computação de alto desempenho) e paralelos em grande escala na nuvem. O AzureBatchStep pode ser usado em um Pipeline do Azure Machine Learning para enviar trabalhos a um pool de computadores do Lote do Azure.

Para anexar o Lote do Azure como um destino de computação, você deve usar o SDK do Azure Machine Learning e fornecer as seguintes informações:

-    **Nome de computação do Lote do Azure**: Um nome amigável a ser usado para a computação no espaço de trabalho
-    **Nome da conta do Lote do Azure**: O nome da conta do Lote do Azure
-    **Grupo de Recursos**: O grupo de recursos que contém o a conta do Lote do Azure.

O código a seguir demonstra como anexar o Lote do Azure como um destino de computação:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> Não crie vários anexos simultâneos para o mesmo lote do Azure do seu espaço de trabalho. Cada novo anexo interromperá os anexos existentes anteriores.

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

As Azure Databricks são um ambiente baseado em Apache Spark na nuvem do Azure. Ele pode ser usado como um destino de computação com um pipeline do Azure Machine Learning.

Crie um workspace do Azure Databricks antes de usá-lo. Para criar um recurso de espaço de trabalho, consulte o documento [executar um trabalho do Spark no Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) .

Para anexar o Azure Databricks como um destino de computação, forneça as seguintes informações:

* __Nome de computação do databricks__: o nome que você deseja atribuir a esse recurso de computação.
* __Nome do espaço de trabalho do databricks__: o nome do espaço de trabalho Azure Databricks.
* __Token de acesso do databricks__: o token de acesso usado para autenticar para Azure Databricks. Para gerar um token de acesso, consulte o documento [Autenticação](/azure/databricks/dev-tools/api/latest/authentication).

O código a seguir demonstra como anexar Azure Databricks como um destino de computação com o SDK do Azure Machine Learning (__o espaço de trabalho do databricks precisa estar presente na mesma assinatura que o seu espaço de trabalho AML__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Para obter um exemplo mais detalhado, consulte um [exemplo de bloco de anotações](https://aka.ms/pl-databricks) no github.

> [!WARNING]
> Não crie vários anexos simultâneos para o mesmo Azure Databricks do seu espaço de trabalho. Cada novo anexo interromperá os anexos existentes anteriores.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Análise Azure Data Lake

O Azure Data Lake Analytics é uma plataforma de análise de big data na nuvem do Azure. Ele pode ser usado como um destino de computação com um pipeline do Azure Machine Learning.

Crie uma conta do Azure Data Lake Analytics antes de usá-lo. Para criar este recurso, consulte o documento [Introdução ao Google Analytics do Azure Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Para anexar o Data Lake Analytics como um destino de computação, você deve usar o SDK do Aprendizado de Máquina do Azure e fornecer as seguintes informações:

* __Nome da computa__: o nome que você deseja atribuir a este recurso de computação.
* __Grupo de recursos__: o grupo de recursos que contém a conta de data Lake Analytics.
* __Nome da conta__: o nome da conta de data Lake Analytics.

O código a seguir demonstra como anexar o Data Lake Analytics como um destino de computação:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Para obter um exemplo mais detalhado, consulte um [exemplo de bloco de anotações](https://aka.ms/pl-adla) no github.

> [!WARNING]
> Não crie vários anexos simultâneos para o mesmo ADLA do seu espaço de trabalho. Cada novo anexo interromperá os anexos existentes anteriores.

> [!TIP]
> Os pipelines do Azure Machine Learning só podem funcionar com dados armazenados no armazenamento de dados padrão da conta do Data Lake Analytics. Se os dados com os quais você precisa trabalhar estiverem em um repositório não padrão, você poderá usar um [`DataTransferStep`](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?preserve-view=true&view=azure-ml-py) para copiar os dados antes do treinamento.

## <a name="azure-container-instance"></a><a id="aci"></a>Instância de contêiner do Azure

As ACI (instâncias de contêiner do Azure) são criadas dinamicamente quando você implanta um modelo. Não é possível criar ou anexar ACI ao seu espaço de trabalho de nenhuma outra maneira. Para obter mais informações, consulte [implantar um modelo para instâncias de contêiner do Azure](how-to-deploy-azure-container-instance.md).

## <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure

O AKS (serviço kubernetes do Azure) permite uma variedade de opções de configuração quando usadas com Azure Machine Learning. Para obter mais informações, consulte [como criar e anexar o serviço kubernetes do Azure](how-to-create-attach-kubernetes.md).


## <a name="notebook-examples"></a>Exemplos de notebook

Veja estes notebooks para obter exemplos de treinamento com vários destinos de computação:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

* Use o recurso de computação para [configurar e enviar uma execução de treinamento](how-to-set-up-training-targets.md).
* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md) usa um destino de computação gerenciado para treinar um modelo.
* Saiba como [ajustar hiperparâmetros de modo eficiente](how-to-tune-hyperparameters.md) para criar modelos melhores.
* Quando você tiver um modelo treinado, aprenda [como e em que local implantar modelos](how-to-deploy-and-where.md).
* [Usar o Azure Machine Learning com Redes Virtuais do Azure](./how-to-network-security-overview.md)