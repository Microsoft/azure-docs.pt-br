---
title: Visualizar experimentos com o TensorBoard
titleSuffix: Azure Machine Learning service
description: Inicie o TensorBoard para visualizar os históricos de execução do experimento e identificar áreas potenciais de readaptação e ajuste de hiperparâmetro.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: maxluk
ms.author: maxluk
ms.date: 06/28/2019
ms.openlocfilehash: fde2b6d1d298e89227951c376d584452fbff2679
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707059"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualizar métricas com TensorBoard e Azure Machine Learning e execuções de experimento

Neste artigo, você aprenderá a exibir as execuções de teste e métricas no usando o TensorBoard [as `tensorboard` pacote](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) SDK de serviço principal do Azure Machine Learning. Depois que inspecionada suas execuções de experimento melhor ajuste e readaptar os modelos de aprendizado de máquina.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) é um pacote de aplicativos web para inspecionar e Noções básicas sobre a estrutura de teste e o desempenho.

Como inicializar o TensorBoard com experiências de aprendizado de máquina do Azure depende do tipo de teste:
+ Se seu experimento nativamente gera arquivos de log que podem ser consumidos pelos TensorBoard, como o PyTorch, Chainer e TensorFlow experimentos, você pode [iniciar o TensorBoard diretamente](#direct) de teste histórico de execução. 

+ Para testes que não nativamente arquivos de saída TensorBoard consumíveis, tais como como Scikit-learn ou do Azure Machine Learning experimentos, use [as `export_to_tensorboard()` método](#export) para exportar o histórico de execução como logs do TensorBoard e iniciar TensorBoard a partir daí. 

## <a name="prerequisites"></a>Pré-requisitos

* Para iniciar o TensorBoard e exibir seu experimento de históricos de execução, é necessário já tiver habilitado o registro em log acompanhar as métricas e o desempenho seus experimentos.  

* O código neste passo a passo pode ser executado em qualquer um dos seguintes ambientes: 

    * Azure Machine Learning Notebook VM - nenhuma downloads ou a necessidade de instalar

        * Conclua o [início rápido do notebook com base em nuvem](quickstart-run-cloud-notebook.md#create-notebook) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo.

        * Na pasta exemplos no servidor de notebook, encontrar duas concluída e expandido blocos de anotações, navegando até este diretório: **how-to-use-azureml > Treinamento com aprendizado profundo**.
        * export-run-history-to-run-history.ipynb
        * tensorboard.ipynb

    * Seu próprio servidor de notebook Juptyer
      * Use o [criar um espaço de trabalho de artigo](setup-create-workspace.md) para
          * [Instalar o SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) com o `tensorboard` extra
          * Criar um espaço de trabalho e seu arquivo de configuração (config)
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Opção 1: Diretamente Exibir histórico de execuções no TensorBoard

Esta opção funciona para experimentos que nativamente saídas log arquivos podem ser consumidos por TensorBoard, como o PyTorch, Chainer e TensorFlow experimentos. Se não for o caso do seu experimento, use [as `export_to_tensorboard()` método](#export) em vez disso.

O código de exemplo a seguir usa o [experimento de demonstração do MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) do repositório do TensorFlow em um destino de computação remota, computação do Azure Machine Learning. Em seguida, treinar nosso modelo com personalizado do SDK [estimador de TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), e, em seguida, inicie o TensorBoard em relação a esse teste TensorFlow, ou seja, um experimento que nativamente gera arquivos de eventos do TensorBoard.

### <a name="set-experiment-name-and-create-project-folder"></a>Defina o nome do teste e crie a pasta do projeto

Aqui podemos nomear o teste e crie sua pasta. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Baixe o código de teste de demonstração do TensorFlow

Repositório do TensorFlow tem uma demonstração do MNIST com a instrumentação do TensorBoard extensiva. Não, nem necessário, alterar nenhum código desta demonstração para que ele funcione com o serviço de Azure Machine Learning. No código a seguir, vamos baixar o código MNIST e salvá-lo em nossa pasta de teste recém-criado.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
Em todo o arquivo de código MNIST, mnist_with_summaries.py, observe que há linhas que chamam `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` etc. Esses métodos de grupo, log e as principais métricas de marca de seus experimentos em Histórico de execução. O `tf.summary.FileWriter()` é especialmente importante, pois ele serializa os dados de suas métricas de teste registrados, que permite o TensorBoard gerar visualizações deles.

 ### <a name="configure-experiment"></a>Configurar o experimento

A seguir, podemos configurar nosso experimento e os diretórios para logs e dados. Esses logs serão carregados para o serviço de artefato, TensorBoard acessa posteriormente.

>[!Note]
> Para este exemplo de TensorFlow, você precisará instalar o TensorFlow em seu computador local. Além disso, o módulo TensorBoard (ou seja, aquele incluído com o TensorFlow) deve ser acessível ao kernel deste bloco de anotações, como o computador local é o que é executado o TensorBoard.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Criar um cluster para o seu experimento
Podemos criar um cluster de AmlCompute neste teste, no entanto, seus testes podem ser criados em qualquer ambiente e você ainda poderá iniciar TensorBoard contra o histórico de execução de teste. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>Enviar a execução com o avaliador de TensorFlow

O avaliador de TensorFlow fornece uma maneira simples de iniciar um trabalho de treinamento de TensorFlow em um destino de computação. Ele é implementado por meio de genéricos [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe, que pode ser usado para dar suporte a qualquer estrutura. Para obter mais informações sobre o treinamento de modelos usando o avaliador de genérico, consulte [treinar modelos com o Azure Machine Learning usando o Vistoriador](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Inicie o TensorBoard

Você pode iniciar o TensorBoard durante sua execução ou após a conclusão. A seguir, criamos uma instância de objeto do TensorBoard `tb`, que usa carregado do histórico de execuções de teste a `run`e, em seguida, inicia o TensorBoard com o `start()` método. 
  
O [TensorBoard construtor](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) pega uma matriz de execuções, portanto, certifique-se e passá-la como uma matriz de elemento único.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Opção 2: Exportar histórico de log para exibir no TensorBoard

O código a seguir configura um experimento de amostra, inicia o processo de registro em log usando as APIs do histórico de execução do Azure Machine Learning e exporta o teste de histórico de execução nos logs podem ser consumidos por TensorBoard para visualização. 

### <a name="set-up-experiment"></a>Configurar o experimento

O código a seguir configura um novo experimento e o nomeia o diretório de execução `root_run`. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Aqui, podemos carregar o conjunto de dados de diabetes – um conjunto de dados pequeno interno que acompanha o scikit-Saiba mais e dividi-la em teste e conjuntos de treinamento.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Executar teste e métricas de log

Para que esse código, treinar um modelo de regressão linear e log de métricas-chave, o coeficiente de alfa `alpha` e significam um erro ao quadrado, `mse`, no histórico de execução.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Execuções de exportação para o TensorBoard

Com o SDK [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) método, podemos exportar o histórico de execução de nosso teste de aprendizado de máquina do Azure nos logs do TensorBoard, portanto, é possível exibi-los por meio do TensorBoard.  

O código a seguir, criamos a pasta `logdir` em nosso diretório de trabalho atual. Essa pasta é onde podemos exportar a nossa experiência de histórico de execução e logs de `root_run` e, em seguida, marcar que são executados como concluído. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 Você também pode exportar uma execução específica para TensorBoard, especificando o nome da execução  `export_to_tensorboard(run_name, logdir)`

Iniciar e parar uma vez o TensorBoard nosso histórico de execução para esse teste é exportado, podemos iniciar TensorBoard com o [Start ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) método. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Quando você terminar, certifique-se de chamar o [Stop ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) método do objeto TensorBoard. Caso contrário, o TensorBoard continuará em execução até que você desligue o kernel do notebook. 

```python
tb.stop()
```

## <a name="next-steps"></a>Próximas etapas

Neste instruções sobre você, criou duas experiências e aprendeu iniciar o TensorBoard em relação a seus históricos de execução para identificar áreas para ajuste potencial e treinamento. 

* Se você estiver satisfeito com seu modelo, vá até nossa [como implantar um modelo](how-to-deploy-and-where.md) artigo. 
* Saiba mais sobre [ajuste de hiperparâmetro](how-to-tune-hyperparameters.md). 
