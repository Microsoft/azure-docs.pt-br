---
title: Visualizar experimentos com o TensorBoard
titleSuffix: Azure Machine Learning
description: Inicie o TensorBoard para visualizar os históricos de execuções de experimento e identificar possíveis áreas para ajuste e readaptação de hiperparâmetro.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: minxia
ms.author: minxia
ms.date: 02/27/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 56b411a490fe1a0c45ea4dd183d88defd61a2fde
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640844"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualizar execuções e métricas de experimento com TensorBoard e Azure Machine Learning


Neste artigo, você aprenderá a exibir suas execuções e métricas de experimento no TensorBoard usando [o pacote `tensorboard`](/python/api/azureml-tensorboard/) no SDK principal do Azure Machine Learning. Depois de inspecionar as execuções de experimento, você poderá ajustar e treinar novamente seus modelos de machine learning.

[O TensorBoard](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) é um pacote de aplicativos Web para inspecionar e compreender a estrutura e o desempenho dos experimentos.

O TensorBoard é iniciado de maneira diferente de acordo com o tipo do experimento do Azure Machine Learning:
+ Se seu experimento gera arquivos de log de forma nativa que podem ser consumidos pelo TensorBoard, como experimentos PyTorch, Chainer e TensorFlow, você pode [iniciar o TensorBoard diretamente](#launch-tensorboard) no histórico de execuções do experimento. 

+ Para experimentos que não geram arquivos consumíveis do TensorBoard de forma nativa, como experimentos do Scikit-learn ou Azure Machine Learning, use [o método `export_to_tensorboard()`](#export) para exportar históricos de execuções como logs do TensorBoard e iniciar o TensorBoard a partir daí. 

> [!TIP]
> Este documento oferece informações principalmente para cientistas de dados e desenvolvedores que querem monitorar o processo de treinamento de modelo. Se você for um administrador interessado em monitorar o uso de recursos e os eventos do Azure Machine Learning, como cotas, execuções de treinamento concluídas ou implantações de modelo concluídas, consulte [Monitoramento do Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos

* Para iniciar o TensorBoard e exibir seus históricos de execução de experimento, seus experimentos precisam ter o registro em log habilitado anteriormente para controlar suas métricas e desempenho.  
* O código neste documento pode ser executado nos seguintes ambientes: 
    * Instância de computação do Azure Machine Learning - nenhum download ou instalação é necessária
        * Conclua o [Tutorial: Configure o ambiente e o espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o exemplo de repositório.
        * Na pasta de amostras no servidor do notebook, há dois notebooks concluídos e expandidos que você pode encontrar nestes diretórios:
            * **instruções-para-uso – azureml > os experimentos de monitoramento e monitor > tensorboard > exportar-executar-History-to-tensorboard > exportar-Run-History-to-tensorboard. ipynb**
            * **instruções-para-uso-azureml > a cursos de monitoramento e monitor > tensorboard > tensorboard > tensorboard. ipynb**
    * Seu próprio servidor de notebook Juptyer
       * [Instale o SDK do Azure Machine Learning](/python/api/overview/azure/ml/install) com `tensorboard` extra
        * [Criar um workspace do Azure Machine Learning](how-to-manage-workspace.md).  
        * [Criar um arquivo de configuração de workspace](how-to-configure-environment.md#workspace).

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Opção 1: Exibir diretamente o histórico de execuções no TensorBoard

Essa opção funciona para experimentos que geram nativamente os arquivos de log consumíveis pelo TensorBoard, como experimentos do PyTorch, Chainer e TensorFlow. Se esse não for o caso do seu experimento, use [o método `export_to_tensorboard()`](#export).

O código de exemplo a seguir usa o [teste de demonstração do MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) do repositório do TensorFlow em um destino de computação remoto, Computação do Machine Learning. Em seguida, configuraremos e iniciaremos uma execução para treinar o modelo TensorFlow e, em seguida, iniciar o TensorBoard nesse experimento de TensorFlow.

### <a name="set-experiment-name-and-create-project-folder"></a>Definir o nome do experimento e criar a pasta do projeto

Aqui, nomeamos o experimento e criamos a pasta dele. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Baixar o código do experimento de demonstração do TensorFlow

O repositório de TensorFlow tem uma demonstração do MNIST com uma ampla instrumentação do TensorBoard. Não alteramos nem precisamos alterar nenhum código dessa demonstração para que funcione no Azure Machine Learning. No código a seguir, baixamos o código MNIST e o salvamos em nossa pasta de experimento recém-criada.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
Em todo o arquivo do código MNIST, mnist_with_summaries.py, observe que há linhas que com chamada para `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` etc. Esses métodos agrupam, registram e marcam as métricas principais dos experimentos no histórico de execuções. `tf.summary.FileWriter()` é especialmente importante porque serializa os dados das suas métricas de teste registradas, o que permite que o TensorBoard gere visualizações para eles.

 ### <a name="configure-experiment"></a>Configurar o experimento

A seguir, configuramos nosso experimento e definimos diretórios para logs e dados. Esses logs serão carregados no histórico de execução, que TensorBoard acessará mais tarde.

> [!Note]
> Para este exemplo do TensorFlow, será necessário instalar o TensorFlow no computador local. Além disso, o módulo TensorBoard (ou seja, aquele incluído com o TensorFlow) deve estar acessível ao kernel desse notebook porque o TensorBoard é executado pelo computador local.

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

# Writing logs to ./logs results in their being uploaded to the run history,
# and thus, made accessible to our TensorBoard instance.
args = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Criar um cluster para o experimento
Criamos um cluster AmlCompute para este experimento, no entanto, os experimentos podem ser criados em qualquer ambiente e você ainda poderá iniciar o TensorBoard em relação ao histórico de execuções do experimento. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpu-cluster"

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="configure-and-submit-training-run"></a>Configurar e enviar a execução de treinamento

Configure um trabalho de treinamento criando um objeto ScriptRunConfig.

```Python
from azureml.core import ScriptRunConfig
from azureml.core import Environment

# Here we will use the TensorFlow 2.2 curated environment
tf_env = Environment.get(ws, 'AzureML-TensorFlow-2.2-GPU')

src = ScriptRunConfig(source_directory=exp_dir,
                      script='mnist_with_summaries.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
run = exp.submit(src)
```

### <a name="launch-tensorboard"></a>Iniciar o TensorBoard

Você pode iniciar o TensorBoard durante a execução ou após a conclusão. A seguir, criamos uma instância do objeto TensorBoard, `tb`, que usa o histórico de execuções do experimento carregado em `run`e, em seguida, inicia o TensorBoard com o método `start()`. 
  
O [construtor TensorBoard](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard) usa uma matriz de execuções, portanto, não esqueça de passá-lo como uma matriz de elemento único.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

> [!Note]
> Embora este exemplo tenha usado TensorFlow, TensorBoard pode ser usado tão facilmente com PyTorch ou encadeamento. O TensorFlow precisa estar disponível no computador que executa o TensorBoard, mas não é necessário no computador que faz os cálculos do PyTorch ou do Chainer. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Opção 2: Exportar histórico como log para exibir no TensorBoard

O código a seguir configura um exemplo de experimento, inicia o processo de registrar em log usando as APIs de histórico de execuções do Azure Machine Learning e exporta o histórico de execuções em logs consumíveis pelo TensorBoard para visualização. 

### <a name="set-up-experiment"></a>Configurar experimento

O código a seguir configura um novo experimento e nomeia o diretório de execução `root_run`. 

```python
from azureml.core import Workspace, Experiment
import azureml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Aqui, carregamos o conjunto de dados de diabetes, um pequeno conjunto de dados interno que vem com o scikit-learn, e o dividimos em conjuntos de teste e treinamento.

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

### <a name="run-experiment-and-log-metrics"></a>Executar métricas de experimento e log

Para esse código, treinamos um modelo de regressão linear e métricas de chave de log, o coeficiente alfa, `alpha` e o erro de quadrado médio, `mse`, no histórico de execuções.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run:
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Exportar execuções para o TensorBoard

Com o método [export_to_tensorboard ()](/python/api/azureml-tensorboard/azureml.tensorboard.export) do SDK, podemos exportar o histórico de execuções do nosso experimento do Azure Machine Learning em logs do TensorBoard, para que possamos exibi-los por meio do TensorBoard.  

No código a seguir, criamos a pasta `logdir` no nosso diretório de trabalho atual. Essa pasta é aonde o histórico e os logs de execuções de experimento de `root_run` serão exportados. Em seguida, a execução será marcada como concluída. 

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

> [!Note]
> Você também pode exportar uma execução específica para o TensorBoard especificando o nome da execução `export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Iniciar e parar o TensorBoard
Quando o histórico de execuções desse experimento é exportado, podemos iniciar o TensorBoard com o método [start ()](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard#start-start-browser-false-). 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Quando terminar, não se esqueça de chamar o método [stop()](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard#stop--) do objeto do TensorBoard. Caso contrário, o TensorBoard continuará a ser executado até que você desligue o kernel do notebook. 

```python
tb.stop()
```

## <a name="next-steps"></a>Próximas etapas

Nestas instruções, você criou dois experimentos e aprendeu a iniciar o TensorBoard em relação a seus históricos de execuções para identificar possíveis áreas de ajuste e retreinamento. 

* Se estiver satisfeito com seu modelo, vá para nosso artigo [Como implantar um modelo](how-to-deploy-and-where.md). 
* Saiba mais sobre o [ajuste de hiperparâmetro](how-to-tune-hyperparameters.md).
