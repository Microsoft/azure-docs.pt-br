---
title: 'Tutorial: Pipelines de ML para pontuação do lote'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você criará um pipeline de machine learning para realizar a pontuação de lote. Concentre-se no machine learning em vez da infraestrutura e da automação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: lobrien
ms.author: laobri
ms.reviewer: laobri
ms.date: 10/13/2020
ms.custom: contperf-fy20q4, devx-track-python
ms.openlocfilehash: 570bfed5ae5fc6fafea36b9ed1f2673a0daae22b
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521502"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Tutorial: Criar um pipeline do Azure Machine Learning para pontuação de lote

Neste tutorial avançado, você aprende a criar um [pipeline do Azure Machine Learning](concept-ml-pipelines.md) para executar um trabalho de pontuação em lotes. Os pipelines de aprendizado de máquina otimizam o fluxo de trabalho com velocidade, portabilidade e reutilização, de modo que você possa se concentrar no aprendizado de máquina em vez de na infraestrutura e na automação. Depois de criar e publicar um pipeline, você configurará um ponto de extremidade REST que poderá ser usado para disparar o pipeline por meio de qualquer biblioteca HTTP em qualquer plataforma. 

O exemplo usa um modelo de rede neural convolucional [Inception-V3](https://arxiv.org/abs/1512.00567) pré-treinado implementado no Tensorflow para classificar imagens sem rótulo. 

Neste tutorial, você completa as seguintes tarefas:

> [!div class="checklist"]
> * Configurar o workspace 
> * Baixar e armazenar dados de exemplo
> * Criar objetos de conjunto de dados para efetuar fetch de dados e gerá-los como saída
> * Baixar, preparar e registrar o modelo no workspace
> * Provisionar destinos de computação e criar um script de pontuação
> * Usar a classe `ParallelRunStep` para a pontuação em lote assíncrona
> * Criar, executar e publicar um pipeline
> * Habilitar um ponto de extremidade REST para o pipeline

Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Pré-requisitos

* Caso você ainda não tenha um Workspace do Azure Machine Learning ou máquina virtual de notebook, conclua a [Parte 1 do tutorial de instalação](tutorial-1st-experiment-sdk-setup.md).
* Quando concluir o tutorial de instalação, use o mesmo servidor de notebook para abrir o notebook *tutorials/machine-learning-pipelines-advanced/tutorial-pipeline-batch-scoring-classification.ipynb*.

Se você desejar executar o tutorial de instalação em seu próprio [ambiente local](how-to-configure-environment.md#local), poderá acessar o tutorial no [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Execute `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` para obter os pacotes necessários.

## <a name="configure-workspace-and-create-a-datastore"></a>Configurar um workspace e criar um armazenamento de dados

Crie um objeto de workspace do Workspace do Azure Machine Learning existente.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

> [!IMPORTANT]
> Esse snippet de código espera que a configuração do workspace seja salva no diretório atual ou no pai dele. Para obter mais informações sobre como criar um workspace, confira [Criar e gerenciar workspaces do Azure Machine Learning](how-to-manage-workspace.md). Para obter mais informações sobre como salvar a configuração no arquivo, confira [Criar um arquivo de configuração de workspace](how-to-configure-environment.md#workspace).

## <a name="create-a-datastore-for-sample-images"></a>Criar um armazenamento de dados para imagens de exemplo

Na conta `pipelinedata`, obtenha a amostra de dados públicos de avaliação do ImageNet do contêiner de blobs público `sampledata`. Chamar `register_azure_blob_container()` disponibiliza os dados para o workspace com o nome `images_datastore`. Em seguida, defina o armazenamento de dados padrão do workspace como o armazenamento de dados de saída. Use o armazenamento de dados de saída para pontuar a saída no pipeline.

Para obter mais informações sobre como acessar dados, confira [Como acessar dados](./how-to-access-data.md).

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>Criar objetos de conjunto de dados

Ao criar pipelines, os objetos `Dataset` são usados para ler os dados de armazenamentos de dados do workspace e os objetos `OutputFileDatasetConfig` são usados para transferir dados intermediários entre as etapas do pipeline.

> [!Important]
> O exemplo de pontuação de lote neste tutorial usa apenas uma etapa de pipeline. Em casos de uso que têm várias etapas, o fluxo típico incluirá estas:
>
> 1. Usar objetos `Dataset` como *entradas* para efetuar fetch de dados brutos, executar algumas transformações e *gerar* com um objeto `OutputFileDatasetConfig`.
>
> 2. Use o *objeto de saída* `OutputFileDatasetConfig` na etapa anterior como um *objeto de entrada*. Repita-a para as etapas subsequentes.

Neste cenário, você cria objetos `Dataset` que correspondem aos diretórios de armazenamento de dados para as imagens de entrada e os rótulos de classificação (valores de teste y). Você também cria um objeto `OutputFileDatasetConfig` para os dados de saída da pontuação do lote.

```python
from azureml.core.dataset import Dataset
from azureml.data import OutputFileDatasetConfig

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/"))
output_dir = OutputFileDatasetConfig(name="scores")
```

Registre os conjuntos de dados no workspace se desejar reutilizá-los mais tarde. Esta etapa é opcional.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>Baixar e registrar o modelo

Baixe o modelo pré-treinado do TensorFlow para usá-lo para pontuação do lote em um pipeline. Primeiro, crie um diretório local em que você armazenará o modelo. Em seguida, baixe e extraia o modelo.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Depois, registre o modelo no workspace para você poder recuperar facilmente o modelo no processo de pipeline. Na função estática `register()`, o parâmetro `model_name` é a chave que você usa para localizar o modelo em todo o SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Criar e anexar o destino de computação remota

Os pipelines de aprendizado de máquina não podem ser executados localmente; portanto, execute-os nos recursos de nuvem ou nos *destinos de computação remota*. Um destino de computação remota é um ambiente de computação virtual reutilizável em que você executa experimentos e fluxos de trabalho de aprendizado de máquina. 

Execute o código a seguir para criar um destino [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute) habilitado para GPU e anexe-o ao seu workspace. Para obter mais informações sobre destinos de computação, confira o [artigo conceitual](./concept-compute-target.md).


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Gravar um script de pontuação

Para fazer a pontuação, crie um script de pontuação de lote chamado `batch_scoring.py` e grave-o no diretório atual. O script usa imagens de entrada, aplica o modelo de classificação e transmite as previsões para um arquivo de resultados.

O script `batch_scoring.py` usa os seguintes parâmetros, que são transmitidos da `ParallelRunStep` que será criada mais tarde:

- `--model_name`: o nome do modelo que está sendo usado.
- `--labels_dir`: a localização do arquivo `labels.txt`.

A infraestrutura de pipelines usa a classe `ArgumentParser` para passar parâmetros em etapas de pipeline. Por exemplo, no código a seguir, o primeiro argumento `--model_name` recebe o identificador de propriedade `model_name`. Na função `init()`, `Model.get_model_path(args.model_name)` é usado para acessar esta propriedade.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict(labels_dir):
    label = []
    labels_path = os.path.join(labels_dir, 'labels.txt')
    proto_as_ascii_lines = tf.gfile.GFile(labels_path).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_dir', dest="labels_dir", required=True)
    args, _ = parser.parse_known_args()

    label_dict = get_class_label_dict(args.labels_dir)
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> O pipeline deste tutorial tem apenas uma etapa e grava a saída em um arquivo. Para pipelines de várias etapas, use também `ArgumentParser` para definir um diretório para gravar os dados de saída da entrada nas etapas seguintes. Para obter um exemplo de como passar dados entre várias etapas de pipeline usando o padrão de design `ArgumentParser`, confira o [notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-the-pipeline"></a>Criar o pipeline

Antes de executar o pipeline, crie um objeto que define o ambiente Python e as dependências que seu script `batch_scoring.py` requer. A principal dependência necessária é o TensorFlow, mas também é possível instalar `azureml-core` e `azureml-dataprep[fuse]` que são exigidos por ParallelRunStep. Além disso, especifique o Docker e o suporte Docker-GPU.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.15.2",
                                            "azureml-core", "azureml-dataprep[fuse]"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>Criar a configuração para encapsular o script

Crie a etapa do pipeline usando o script, a configuração do ambiente e os parâmetros. Especifique o destino de computação que você já anexou ao workspace.

```python
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>Criar a etapa do pipeline

Uma etapa de pipeline é um objeto que encapsula tudo o que você precisa para executar um pipeline, incluindo:

* Configurações de dependência e ambiente
* O recurso de computação no qual executar o pipeline
* Dados de entrada e de saída e parâmetros personalizados
* Referência a um script ou lógica de SDK a ser executada durante a etapa

Várias classes herdam da classe pai [`PipelineStep`](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep). É possível escolher classes para usar estruturas específicas ou pilhas para criar uma etapa. Neste exemplo, você usará a classe `ParallelRunStep` para definir a lógica da etapa usando um script Python personalizado. Se um argumento para o script for uma entrada para a etapa ou uma saída da etapa, ele precisará ser definido *igualmente* na matriz `arguments`*e* no parâmetro `input` ou `output`, respectivamente. 

Nos cenários em que há mais de uma etapa, uma referência de objeto na matriz `outputs` torna-se disponível como uma *entrada* para uma etapa de pipeline seguinte.

```python
from azureml.pipeline.steps import ParallelRunStep
from datetime import datetime

parallel_step_name = "batchscoring-" + datetime.now().strftime("%Y%m%d%H%M")

label_config = label_ds.as_named_input("labels_input")

batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--model_name", "inception",
               "--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

Para obter uma lista de todas as classes que podem ser usadas para diferentes tipos de etapa, confira o [pacote de etapas](/python/api/azureml-pipeline-steps/azureml.pipeline.steps).

## <a name="submit-the-pipeline"></a>Enviar o pipeline

Agora execute o pipeline. Primeiro, crie um objeto `Pipeline` usando a referência do workspace e a etapa de pipeline que você criou. O parâmetro `steps` é uma matriz de etapas. Nesse caso, há apenas uma etapa para a pontuação do lote. Para criar pipelines que têm várias etapas, coloque as etapas em ordem nesta matriz.

Em seguida, use a função `Experiment.submit()` para enviar o pipeline para execução. A função `wait_for_completion` gera logs durante o processo de build do pipeline. É possível usar os logs para ver o progresso atual.

> [!IMPORTANT]
> A primeira execução do pipeline leva aproximadamente *15 minutos*. Todas as dependências precisam ser baixadas, uma imagem do Docker é criada e o ambiente do Python é provisionado e criado. Executar o pipeline novamente leva significativamente menos tempo, porque esses recursos são reutilizados, em vez de serem criados. No entanto, o tempo de execução total do pipeline depende da carga de trabalho dos seus scripts e dos processos sendo executados em cada etapa de pipeline.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'Tutorial-Batch-Scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Baixar e examinar a saída

Execute o código a seguir para baixar o arquivo de saída criado com base no script `batch_scoring.py`. Em seguida, explore os resultados da pontuação.

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publicar e executar em um ponto de extremidade REST

Execute o código a seguir para publicar o pipeline no workspace. No workspace do estúdio do Azure Machine Learning é possível ver os metadados do pipeline, incluindo o histórico de execuções e as durações. Você também pode executar o pipeline manualmente no estúdio.

A publicação do pipeline habilita que um ponto de extremidade REST que pode ser usado para executar o pipeline de qualquer biblioteca HTTP em qualquer plataforma.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Para executar o pipeline no ponto de extremidade REST, é necessário um cabeçalho de autenticação do tipo Portador do OAuth2. O exemplo a seguir usa a autenticação interativa (para fins de ilustração), mas para a maioria dos cenários de produção que exigem autenticação automatizada ou sem periféricos, use a autenticação da entidade de serviço [descrita neste artigo](how-to-setup-authentication.md).

A autenticação de entidade de serviço envolve a criação de um *Registro de Aplicativo* no *Azure Active Directory*. Primeiro, gere um segredo do cliente e, em seguida, conceda à sua entidade de serviço *acesso de função* ao Workspace do Machine Learning. Use a classe [`ServicePrincipalAuthentication`](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication) para gerenciar o fluxo de autenticação. 

Ambos [`InteractiveLoginAuthentication`](/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication) e `ServicePrincipalAuthentication` são herdados de `AbstractAuthentication`. Em ambos os casos, você usa a função [`get_authentication_header()`](/python/api/azureml-core/azureml.core.authentication.abstractauthentication#get-authentication-header--) da mesma maneira para buscar o cabeçalho:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Obtenha a URL REST da propriedade `endpoint` do objeto de pipeline publicado. Encontre também a URL REST no workspace do estúdio do Azure Machine Learning. 

Crie uma solicitação HTTP POST para o ponto de extremidade. Especifique o cabeçalho de autenticação na solicitação. Adicione um objeto de conteúdo JSON com o nome do experimento.

Faça a solicitação para disparar a execução. Inclua o código para acessar a chave `Id` do dicionário de respostas para obter o valor da ID de execução.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "Tutorial-Batch-Scoring",
                               "ParameterAssignments": {"process_count_per_node": 6}})
run_id = response.json()["Id"]
```

Use a ID de execução para monitorar o status da nova execução. A nova execução leva mais de 10 a 15 minutos para ser concluída. 

A nova execução será semelhante ao pipeline executado anteriormente no tutorial. É possível optar por não exibir a saída completa.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["Tutorial-Batch-Scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Limpar os recursos

Não conclua esta seção se desejar executar outros tutoriais do Azure Machine Learning.

### <a name="stop-the-compute-instance"></a>Parar a instância de computação

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Excluir tudo

Se você não pretende usar os recursos criados, exclua-os para não gerar encargos:

1. No portal do Azure, no menu esquerdo, selecione **Grupos de recursos**.
1. Na lista de grupos de recursos, selecione o grupo de recursos criado.
1. Selecione **Excluir grupo de recursos**.
1. Insira o nome do grupo de recursos. Em seguida, selecione **Excluir**.

Você também pode manter o grupo de recursos, mas excluir um único workspace. Exiba as propriedades do workspace e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial de pipelines do Machine Learning, você executou as seguintes tarefas:

> [!div class="checklist"]
> * Criou um pipeline com dependências de ambiente para execução em um recurso de computação de GPU remota.
> * Criou um script de pontuação para executar previsões em lote usando um modelo pré-treinado do TensorFlow.
> * Publicou um pipeline e o habilitou a ser executado em um ponto de extremidade REST.

Para obter mais exemplos de como criar pipelines usando o SDK de aprendizado de máquina, confira o [repositório de notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
