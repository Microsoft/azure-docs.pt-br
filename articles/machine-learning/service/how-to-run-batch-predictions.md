---
title: Executar previsões em lotes para dados grandes
titleSuffix: Azure Machine Learning service
description: Saiba como fazer previsões em lote de forma assíncrona em grandes quantidades de dados usando o Serviço do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 07/12/2019
ms.openlocfilehash: 689ee003e0923a65d3ca3f2d13c1a2d05c299dbd
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358716"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-service"></a>Executar previsões em lote em grandes conjuntos de dados com o Serviço do Azure Machine Learning

Neste artigo, você aprenderá a fazer previsões sobre grandes quantidades de dados de forma assíncrona usando o serviço de Azure Machine Learning.

A previsão em lote (ou pontuação do lote) fornece inferência econômica com taxa de transferência incomparável para aplicativos assíncronos. Os pipelines de previsão em lote podem ser dimensionados para realizar inferências em terabytes de dados de produção. A previsão em lote é otimizada para previsões tipo fire-and-forget de alta taxa de transferência para uma grande coleção de dados.

>[!TIP]
> Se o sistema exigir processamento de baixa latência (para processar rapidamente um único documento ou um pequeno conjunto de documentos), use a [pontuação em tempo real](how-to-consume-web-service.md) em vez da previsão em lote.

Nas etapas a seguir, você criará um [pipeline de Machine Learning](concept-ml-pipelines.md) para registrar um modelo de visão de computador pré-treinado ([concepção-v3](https://arxiv.org/abs/1512.00567)). Em seguida, você usa o modelo pré-treinado para fazer a pontuação de lote em imagens disponíveis em sua conta de armazenamento de BLOBs do Azure. Essas imagens usadas na pontuação são imagens não rotuladas do conjunto de dados [ImageNet](http://image-net.org/).

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree).

- Configure seu ambiente de desenvolvimento para instalar o SDK do Azure Machine Learning. Para saber mais, consulte [Configurar um ambiente de desenvolvimento para o Azure Machine Learning](how-to-configure-environment.md).

- Crie um espaço de trabalho do Azure Machine Learning que manterá todos os recursos de seu pipeline. Você pode usar o código a seguir ou, para obter mais opções, consulte [Criar um arquivo de configuração do espaço de trabalho](how-to-configure-environment.md#workspace).

  ```python
  from azureml.core import Workspace
  ws = Workspace.create(name = '<workspace-name>',
                        subscription_id = '<subscription-id>',
                        resource_group = '<resource-group>',
                        location = '<workspace_region>',
                        exist_ok = True
                        )
  ```

## <a name="set-up-machine-learning-resources"></a>Configurar recursos de aprendizado de máquina

As etapas a seguir configurarão os recursos necessários para executar um pipeline:

- Acesse o repositório de dados que já possui o modelo previamente treinado, rótulos de entrada e imagens para pontuação (isso já está configurado para você).
- Configure um repositório de dados para armazenar os resultados.
- Configure objetos `DataReference` para apontar para os dados nos armazenamentos de dados anteriores.
- Configure as máquinas ou os clusters de computação em que as etapas do pipeline serão executadas.

### <a name="access-the-datastores"></a>Acessar os repositórios de dados

Primeiro, acesse o repositório de dados que possui o modelo, os rótulos e as imagens.

Use um contêiner de blob público, chamado *SampleData*, na conta *pipelinedata* que contém imagens do conjunto de avaliação ImageNet. O nome do repositório de dados desse contêiner público é *images_datastore*. Registre esse repositório de dados em seu espaço de trabalho:

```python
from azureml.core import Datastore

account_name = "pipelinedata"
datastore_name = "images_datastore"
container_name = "sampledata"

batchscore_blob = Datastore.register_azure_blob_container(ws,
                                                          datastore_name=datastore_name,
                                                          container_name=container_name,
                                                          account_name=account_name,
                                                          overwrite=True)
```

Em seguida, configure para usar o armazenamento de dados padrão para as saídas.

Ao criar seu espaço de trabalho, [Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) e [armazenamento de Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) são conectados ao espaço de trabalho por padrão. Arquivos do Azure é o armazenamento de dados padrão para um espaço de trabalho, mas também é possível usar o armazenamento de Blob como um armazenamento de dados. Para obter mais informações, consulte [Opções de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>Configurar referências de dados

Agora, faça referência aos dados em seu pipeline como entradas para as etapas do pipeline.

Uma fonte de dados em um pipeline é representada por um objeto [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) . O objeto `DataReference` aponta para dados que residem ou são acessados a partir de um armazenamento de dados. Você precisa de objetos`DataReference` para o diretório usado para imagens de entrada, o diretório no qual o modelo pré-treinado está armazenado, o diretório de rótulos e o diretório de saída.

```python
from azureml.data.data_reference import DataReference

input_images = DataReference(datastore=batchscore_blob,
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download")

model_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_model",
                          path_on_datastore="batchscoring/models",
                          mode="download")

label_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download")

output_dir = PipelineData(name="scores",
                          datastore=def_data_store,
                          output_path_on_compute="batchscoring/results")
```

### <a name="set-up-compute-target"></a>Configurar destino de computação

No Azure Machine Learning, *computação* (ou *destino de computação*) refere-se a computadores ou clusters que executam as etapas computacionais no pipeline de aprendizado de máquina. Por exemplo, você pode criar uma `Azure Machine Learning compute`.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

compute_name = "gpucluster"
compute_min_nodes = 0
compute_max_nodes = 4
vm_size = "STANDARD_NC6"

if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target. just use it. ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(
        vm_size=vm_size,  # NC6 is GPU-enabled
        vm_priority='lowpriority',  # optional
        min_nodes=compute_min_nodes,
        max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws,
                                          compute_name,
                                          provisioning_config)

    compute_target.wait_for_completion(
        show_output=True,
        min_node_count=None,
        timeout_in_minutes=20)
```

## <a name="prepare-the-model"></a>Preparar o modelo

Antes de usar o modelo previamente treinado, você precisa fazer o download do modelo e registrá-lo em seu espaço de trabalho.

### <a name="download-the-pretrained-model"></a>Fazer o download do modelo previamente treinado

Faça o download do modelo de visão computacional previamente treinado (InceptionV3) de <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz>. Em seguida, extraia-o para a subpasta `models`.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url = "http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>Registre o modelo

Veja como registrar o modelo:

```python
import shutil
from azureml.core.model import Model

# register downloaded model
model = Model.register(
    model_path="models/inception_v3.ckpt",
    model_name="inception",  # This is the name of the registered model
    tags={'pretrained': "inception"},
    description="Imagenet trained tensorflow inception",
    workspace=ws)
```

## <a name="write-your-scoring-script"></a>Gravar seu script de pontuação

>[!Warning]
>O código a seguir é apenas um exemplo do que está contido no [batch_score.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py) usado pelo [bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb). Será preciso criar seu próprio script de pontuação para o seu cenário.

O script `batch_score.py` pega imagens de entrada em  *dataset_path*, modelos previamente treinados em *model_dir,* e gera *results-label.txt* em *output_dir*.

```python
# Snippets from a sample scoring script
# Refer to the accompanying batch-scoring Notebook
# https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb
# for the implementation script

# Get labels
def get_class_label_dict(label_file):
  label = []
  proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
  for l in proto_as_ascii_lines:
    label.append(l.rstrip())
  return label

class DataIterator:
  # Definition of the DataIterator here

def main(_):
    # Refer to batch-scoring Notebook for implementation.
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)
    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                        num_classes=classes_num,
                                                        is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")

        # copy the file to artifacts
        shutil.copy(out_filename, "./outputs/")
```

## <a name="build-and-run-the-batch-scoring-pipeline"></a>Compilar e executar o pipeline de pontuação em lote

### <a name="prepare-the-run-environment"></a>Prepare o ambiente de execução

Especifique as dependências conda de seu script. Você precisará deste objeto mais tarde quando criar a etapa do pipeline.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

cd = CondaDependencies.create(
    pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>Especifique o parâmetro para o pipeline

Crie um parâmetro de pipeline usando um objeto [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) com um valor padrão.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(
    name="param_batch_size",
    default_value=20)
```

### <a name="create-the-pipeline-step"></a>Criar a etapa do pipeline

Crie a etapa do pipeline usando o script, a configuração do ambiente e os parâmetros. Especifique o destino de computação que você já anexou ao seu espaço de trabalho como o destino de execução do script. Use [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) para criar a etapa do pipeline.

```python
from azureml.pipeline.steps import PythonScriptStep
inception_model_name = "inception_v3.ckpt"

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_score.py",
    arguments=["--dataset_path", input_images,
               "--model_name", "inception",
               "--label_dir", label_dir,
               "--output_dir", output_dir,
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config,
    source_directory=scripts_folder
```

### <a name="run-the-pipeline"></a>Executar o pipeline

Agora, execute o pipeline e analise a saída produzida. A saída terá uma pontuação correspondente a cada imagem de entrada.

```python
import pandas as pd
from azureml.pipeline.core import Pipeline

# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(
    pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>Publicar o pipeline

Quando estiver satisfeito com o resultado da execução, publique o pipeline para que você possa executá-lo com diferentes valores de entrada mais tarde. Ao publicar um pipeline, você obtém um ponto de extremidade REST. Esse ponto de extremidade aceita invocação do pipeline com o conjunto de parâmetros já incorporado, usando [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py).

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring",
    description="Batch scoring using Inception v3 model",
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>Executar novamente o pipeline usando o ponto de extremidade REST

Para executar o pipeline novamente, será necessário um token de cabeçalho de autenticação do Azure Active Directory, conforme descrito na [classe AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>Próximas etapas

Para ver isso funcionando de ponta a ponta, experimente o bloco de anotações de pontuação do lote no [GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

