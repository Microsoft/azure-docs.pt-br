---
title: Usar Apache Spark em um pipeline do Machine Learning (visualização)
titleSuffix: Azure Machine Learning
description: Vincule seu espaço de trabalho do Azure Synapse Analytics ao pipeline do Azure Machine Learning para usar Apache Spark para a manipulação de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, synapse-azureml
ms.openlocfilehash: b03915608c6143a9e205ba1a1e08e411b8aa9093
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868639"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>Como usar Apache Spark (da plataforma Azure Synapse Analytics) em seu pipeline do Machine Learning (versão prévia)

Neste artigo, você aprenderá a usar os pools de Apache Spark fornecidos pelo Azure Synapse Analytics como o destino de computação para uma etapa de preparação de dados em um pipeline de Azure Machine Learning. Você aprenderá como um único pipeline pode usar recursos de computação adequados para a etapa específica, como preparação de dados ou treinamento. Você verá como os dados são preparados para a etapa do Spark e como ele é passado para a próxima etapa. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Crie um [Workspace do Azure Machine Learning](how-to-manage-workspace.md) para manter todos os seus recursos de pipeline.

* [Configure seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o sdk do Azure Machine Learning ou use uma [instância de computação Azure Machine Learning](concept-compute-instance.md) com o SDK já instalado.

* Crie um espaço de trabalho do Azure Synapse Analytics e o pool de Apache Spark (consulte [início rápido: criar um pool sem servidor Apache Spark usando o Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)). 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Vincular seu espaço de trabalho do Azure Machine Learning e o espaço de trabalho do Azure Synapse Analytics 

Você cria e administra seus pools de Apache Spark em um espaço de trabalho do Azure Synapse Analytics. Para integrar um pool de Apache Spark com um espaço de trabalho do Azure Machine Learning, você deve [vincular ao espaço de trabalho do Azure Synapse Analytics](how-to-link-synapse-ml-workspaces.md). 

Você pode anexar um pool de Apache Spark por meio da interface do usuário do Azure Machine Learning Studio usando a página **Serviços vinculados** . Você também pode fazer isso por meio da página de **computação** com a opção **anexar computação** .

Você também pode anexar um pool de Apache Spark via SDK (conforme elaborado abaixo) ou por meio de um modelo do ARM (consulte este [modelo ARM de exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)). 

Você pode usar a linha de comando para seguir o modelo ARM, adicionar o serviço vinculado e anexar o pool de Apache Spark com o seguinte código:

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Para vincular com êxito o espaço de trabalho do Azure Synapse Analytics, você deve ter a função de proprietário no recurso de espaço de trabalho do Azure Synapse Analytics. Verifique seu acesso no portal do Azure.
> O serviço vinculado receberá uma SAI (identidade atribuída pelo sistema) ao criá-la. Você deve atribuir esse serviço de link SAI a função "administrador de Apache Spark do Synapse" do Synapse Studio para que ele possa enviar o trabalho do Spark (consulte [como gerenciar atribuições de função Synapse RBAC no Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)). Você também deve conceder ao usuário do Azure Machine Learning espaço de trabalho a função "colaborador" de portal do Azure de gerenciamento de recursos.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>Criar ou recuperar o link entre o espaço de trabalho do Azure Synapse Analytics e seu espaço de trabalho Azure Machine Learning

Você pode recuperar serviços vinculados em seu espaço de trabalho com um código como:

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

Primeiro, `Workspace.from_config()` o acessa seu espaço de trabalho do Azure Machine Learning usando a configuração no `config.json` (consulte o [tutorial: introdução ao Azure Machine Learning em seu ambiente de desenvolvimento](tutorial-1st-experiment-sdk-setup-local.md)). Em seguida, o código imprime todos os serviços vinculados disponíveis no espaço de trabalho. Por fim, `LinkedService.get()` o recupera um serviço vinculado denominado `'synapselink1'` . 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Anexe o pool do Apache Spark como um destino de computação para Azure Machine Learning

Para usar o pool do Apache Spark para ativar uma etapa no pipeline do Machine Learning, você deve anexá-lo como uma `ComputeTarget` para a etapa de pipeline, conforme mostrado no código a seguir.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(
        linked_service = linked_service,
        type="SynapseSpark",
        pool_name="spark01") # This name comes from your Synapse workspace

synapse_compute=ComputeTarget.attach(
        workspace=ws,
        name='link1-spark01',
        attach_configuration=attach_config)

synapse_compute.wait_for_completion()
```

A primeira etapa é configurar o `SynapseCompute` . O `linked_service` argumento é o `LinkedService` objeto que você criou ou recuperou na etapa anterior. O `type` argumento deve ser `SynapseSpark` . O `pool_name` argumento em `SynapseCompute.attach_configuration()` deve corresponder ao de um pool existente no seu espaço de trabalho do Azure Synapse Analytics. Para obter mais informações sobre como criar um pool do Apache Spark no espaço de trabalho do Azure Synapse Analytics, consulte [início rápido: criar um pool de Apache Spark sem servidor usando o Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md). O tipo de `attach_config` é `ComputeTargetAttachConfiguration`.

Depois que a configuração é criada, você cria um aprendizado de máquina `ComputeTarget` passando o `Workspace` , o `ComputeTargetAttachConfiguration` e o nome por meio do qual você gostaria de consultar a computação no espaço de trabalho do Machine Learning. A chamada para `ComputeTarget.attach()` é assíncrona, portanto, o exemplo é bloqueado até que a chamada seja concluída.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>Criar um `SynapseSparkStep` que usa o pool de Apache Spark vinculado

O trabalho de exemplo do notebook [Spark no pool do Apache Spark](https://github.com/azure/machinelearningnotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) define um pipeline de aprendizado de máquina simples. Primeiro, o notebook define uma etapa de preparação de dados fornecida pelo `synapse_compute` definido na etapa anterior. Em seguida, o notebook define uma etapa de treinamento fornecida por um destino de computação mais adequado para treinamento. O bloco de anotações de exemplo usa o banco de dados de sobrevivência Titanic para demonstrar entrada e saída de dados; na verdade, ele não limpa os dados nem faz um modelo de previsão. Como não há treinamento real neste exemplo, a etapa de treinamento usa um recurso de computação barato baseado em CPU.

Os dados fluem em um pipeline de Machine Learning por meio de `DatasetConsumptionConfig` objetos, que podem conter dados tabulares ou conjuntos de arquivos. Os dados geralmente vêm de arquivos no armazenamento de BLOBs no armazenamento de dados de um espaço de trabalho. O código a seguir mostra um código típico para a criação de entrada para um pipeline de Machine Learning:

```python
from azureml.core import Dataset

datastore = ws.get_default_datastore()
file_name = 'Titanic.csv'

titanic_tabular_dataset = Dataset.Tabular.from_delimited_files(path=[(datastore, file_name)])
step1_input1 = titanic_tabular_dataset.as_named_input("tabular_input")

# Example only: it wouldn't make sense to duplicate input data, especially one as tabular and the other as files
titanic_file_dataset = Dataset.File.from_files(path=[(datastore, file_name)])
step1_input2 = titanic_file_dataset.as_named_input("file_input").as_hdfs()
```

O código acima pressupõe que o arquivo `Titanic.csv` está no armazenamento de BLOBs. O código mostra como ler o arquivo como um `TabularDataset` e como um `FileDataset` . Esse código é apenas para fins de demonstração, pois seria confuso para duplicar entradas ou para interpretar uma única fonte de dados como um recurso contendo uma tabela e apenas como um arquivo.

> [!Important]
> Para usar um `FileDataset` como entrada, sua `azureml-core` versão deve ser pelo menos `1.20.0` . Como especificar isso usando a `Environment` classe é discutida abaixo.

Quando uma etapa é concluída, você pode optar por armazenar dados de saída usando um código semelhante a:

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

Nesse caso, os dados seriam armazenados no `datastore` em um arquivo chamado `test` e estarão disponíveis no espaço de trabalho do Machine Learning como um `Dataset` com o nome `registered_dataset` .

Além dos dados, uma etapa de pipeline pode ter dependências de Python por etapa. `SynapseSparkStep`Os objetos individuais também podem especificar a configuração precisa do Azure Synapse Apache Spark. Isso é mostrado no código a seguir, que especifica que a `azureml-core` versão do pacote deve ser pelo menos `1.20.0` . (Conforme mencionado anteriormente, esse requisito para `azureml-core` é necessário para usar uma `FileDataset` como entrada.)

```python
from azureml.core.environment import Environment
from azureml.pipeline.steps import SynapseSparkStep

env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core>=1.20.0")

step_1 = SynapseSparkStep(name = 'synapse-spark',
                          file = 'dataprep.py',
                          source_directory="./code", 
                          inputs=[step1_input1, step1_input2],
                          outputs=[step1_output],
                          arguments = ["--tabular_input", step1_input1, 
                                       "--file_input", step1_input2,
                                       "--output_dir", step1_output],
                          compute_target = 'link1-spark01',
                          driver_memory = "7g",
                          driver_cores = 4,
                          executor_memory = "7g",
                          executor_cores = 2,
                          num_executors = 1,
                          environment = env)
```

O código acima Especifica uma única etapa no pipeline do Azure Machine Learning. Esta etapa `environment` especifica uma versão específica `azureml-core` e pode adicionar outras dependências de Conda ou Pip, conforme necessário.

O `SynapseSparkStep` será zip e carregado do computador local no subdiretório `./code` . Esse diretório será recriado no servidor de computação e a etapa executará o arquivo `dataprep.py` desse diretório. O `inputs` e `outputs` dessa etapa são os `step1_input1` objetos, `step1_input2` e `step1_output` anteriormente discutidos. A maneira mais fácil de acessar esses valores no `dataprep.py` script é associá-los com o nomeado `arguments` .

O próximo conjunto de argumentos para o `SynapseSparkStep` controle de construtor Apache Spark. O `compute_target` é o `'link1-spark01'` que anexamos como um destino de computação anteriormente. Os outros parâmetros especificam a memória e os núcleos que gostaríamos de usar.

O bloco de anotações de exemplo usa o seguinte código para `dataprep.py` :

```python
import os
import sys
import azureml.core
from pyspark.sql import SparkSession
from azureml.core import Run, Dataset

print(azureml.core.VERSION)
print(os.environ)

import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--tabular_input")
parser.add_argument("--file_input")
parser.add_argument("--output_dir")
args = parser.parse_args()

# use dataset sdk to read tabular dataset
run_context = Run.get_context()
dataset = Dataset.get_by_id(run_context.experiment.workspace,id=args.tabular_input)
sdf = dataset.to_spark_dataframe()
sdf.show()

# use hdfs path to read file dataset
spark= SparkSession.builder.getOrCreate()
sdf = spark.read.option("header", "true").csv(args.file_input)
sdf.show()

sdf.coalesce(1).write\
.option("header", "true")\
.mode("append")\
.csv(args.output_dir)
```

Esse script de "preparação de dados" não faz nenhuma transformação de dados real, mas ilustra como recuperar dados, convertê-los em um dataframe Spark e como fazer alguma manipulação básica de Apache Spark. Você pode encontrar a saída em Azure Machine Learning Studio abrindo a execução filha, escolhendo a guia **saídas + logs** e abrindo o `logs/azureml/driver/stdout` arquivo, conforme mostrado na figura a seguir.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="Captura de tela do estúdio mostrando a guia stdout da execução filho":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>Usar o `SynapseSparkStep` em um pipeline

Outras etapas no pipeline podem ter seus próprios ambientes exclusivos e ser executadas em recursos de computação diferentes apropriados para a tarefa em questão. O notebook de exemplo executa a "etapa de treinamento" em um pequeno cluster de CPU:

```python
from azureml.core.compute import AmlCompute

cpu_cluster_name = "cpucluster"

if cpu_cluster_name in ws.compute_targets:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
else:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', max_nodes=1)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    print('Allocating new CPU compute cluster')

cpu_cluster.wait_for_completion(show_output=True)

step2_input = step1_output.as_input("step2_input").as_download()

step_2 = PythonScriptStep(script_name="train.py",
                          arguments=[step2_input],
                          inputs=[step2_input],
                          compute_target=cpu_cluster_name,
                          source_directory="./code",
                          allow_reuse=False)
```

O código acima cria o novo recurso de computação, se necessário. Em seguida, o `step1_output` resultado é convertido em entrada para a etapa de treinamento. A `as_download()` opção significa que os dados serão movidos para o recurso de computação, resultando em acesso mais rápido. Se os dados fossem tão grandes que não couberem no disco rígido de computação local, você usaria a `as_mount()` opção para transmitir os dados por meio do sistema de arquivos de fusível. A `compute_target` desta segunda etapa é `'cpucluster'` , não o `'link1-spark01'` recurso que você usou na etapa de preparação de dados. Esta etapa usa um programa simples `train.py` em vez do `dataprep.py` que você usou na etapa anterior. Você pode ver os detalhes de `train.py` no bloco de anotações de exemplo.

Depois de definir todas as suas etapas, você pode criar e executar o pipeline. 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

O código acima cria um pipeline que consiste na etapa de preparação de dados em pools de Apache Spark da plataforma Azure Synapse Analytics ( `step_1` ) e na etapa de treinamento ( `step_2` ). O Azure calcula o grafo de execução examinando as dependências de dados entre as etapas. Nesse caso, há apenas uma dependência simples que `step2_input` necessariamente requer `step1_output` .

A chamada para `pipeline.submit` cria, se necessário, um experimento chamado `synapse-pipeline` e inicia de forma assíncrona uma execução dentro dele. As etapas individuais no pipeline são executadas como execuções filhas dessa execução principal e podem ser monitoradas e revisadas na página experimentos do estúdio.

## <a name="next-steps"></a>Próximas etapas

* [Publicar e acompanhar pipelines do Machine Learning](how-to-deploy-pipelines.md) 
* [Monitorar o Azure Machine Learning](monitor-azure-machine-learning.md)
* [Usar o ML automatizado em um pipeline de Azure Machine Learning no Python](how-to-use-automlstep-in-pipelines.md)
