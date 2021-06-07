---
title: 'Tutorial de classificação de imagens: Treinar modelos'
titleSuffix: Azure Machine Learning
description: Use o Azure Machine Learning para treinar um modelo de classificação de imagens com o Scikit-learn em um Jupyter Notebook em Python. Este tutorial é a primeira parte de duas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 09/28/2020
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 85dea807ee09338e7f0e9e388f6b196fd3beef33
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588657"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn"></a>Tutorial: Treinar modelos de classificação de imagem usando dados MNIST e scikit-learn 


Neste tutorial, você treina um modelo de machine learning em recursos remotos de computação. Você usará o fluxo de trabalho de treinamento e implantação para o Azure Machine Learning em um Jupyter Notebook em Python.  Você pode usar o notebook como um modelo para treinar seu próprio modelo de aprendizado de máquina com seus próprios dados. Este tutorial é **parte uma de uma série de tutoriais de duas partes**.  

Este tutorial treina uma regressão logística simples usando o conjunto de dados do [MNIST](http://yann.lecun.com/exdb/mnist/) e o [Scikit-learn](https://scikit-learn.org) com o Azure Machine Learning. MNIST é um conjunto de dados popular que consiste em 70.000 imagens em escala de cinza. Cada imagem é um dígito manuscrito de 28 x 28 pixels, representando um número de zero a nove. O objetivo é criar um classificador de várias classes para identificar o dígito que uma determinada imagem representa.

Saiba como executar as seguintes ações:

> [!div class="checklist"]
> * Configurar seu ambiente de desenvolvimento.
> * Acessar e examinar os dados.
> * Treinar um modelo de regressão logística simples em um cluster remoto.
> * Analisar os resultados do treinamento e registrar o melhor modelo.

Você aprenderá como selecionar um modelo e implantá-lo na [parte dois deste tutorial](tutorial-deploy-models-with-aml.md).

Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

>[!NOTE]
> O código deste artigo foi testado com o [SDK do Azure Machine Learning](/python/api/overview/azure/ml/intro) versão 1.13.0.

## <a name="prerequisites"></a>Pré-requisitos

* Conclua o [Tutorial: Introdução à criação do seu primeiro experimento do Azure Machine Learning](tutorial-1st-experiment-sdk-setup.md) para:
    * Criar um workspace
    * Clonar o notebook de tutoriais em sua pasta no workspace.
    * Crie uma instância de computação baseada em nuvem.

* Na pasta *tutorials/image-classification-mnist-data* clonada, abra o notebook *img-classification-part1-training.ipynb*. 


O tutorial (e o arquivo complementar **utils.py**) também estará disponível no [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) se você quiser usá-lo em seu próprio [ambiente local](how-to-configure-environment.md#local). Execute `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` para instalar as dependências para este tutorial.

> [!Important]
> O restante deste artigo contém o mesmo conteúdo que você vê no notebook.  
>
> Alterne para o Jupyter Notebook agora se quiser ler enquanto executa o código. 
> Para executar uma única célula de código em um notebook, clique na célula de código e pressione **Shift + Enter**. Ou execute o notebook inteiro escolhendo a **Executar tudo** na barra de ferramentas superior.

## <a name="set-up-your-development-environment"></a><a name="start"></a>Configurar seu ambiente de desenvolvimento

Toda a configuração para o seu trabalho de desenvolvimento pode ser realizada em um bloco de anotações do Python. A configuração inclui as seguintes ações:

* Importar pacotes do Python.
* Conectar-se ao workspace, para que seu computador local possa se comunicar com recursos remotos.
* Criar um experimento para acompanhar todas as suas execuções.
* Criar um destino de computação remota para usar no treinamento.

### <a name="import-packages"></a>Importar pacotes

Importe pacotes Python que você precisa nesta sessão. Além disso, exiba a versão do SDK do Azure Machine Learning:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Conectar-se a um workspace

Crie um objeto de workspace a partir do workspace existente. `Workspace.from_config()` lê o arquivo **config. JSON** e carrega os detalhes em um objeto chamado `ws`:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

>[!NOTE]
> Talvez seja solicitado que você autentique o workspace na primeira vez em que executar o código a seguir. Siga as instruções na tela.

### <a name="create-an-experiment"></a>Criar uma experiência

Crie um teste para acompanhar as execuções em seu workspace. Um workspace pode ter vários experimentos:

```python
from azureml.core import Experiment
experiment_name = 'Tutorial-sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>Criar ou anexar um destino de computação existente

Usando a Computação do Azure Machine Learning, um serviço gerenciado, os cientistas de dados podem treinar modelos de aprendizado de máquina em clusters de máquinas virtuais do Azure. Exemplos incluem máquinas virtuais com suporte a GPU. Neste tutorial, você cria a Computação do Azure Machine Learning como seu ambiente de treinamento. Você enviará o código do Python a ser executado nessa VM mais adiante no tutorial. 

O código abaixo criará clusters de computação para você se eles ainda não existirem em seu workspace. Ele configura um cluster que será reduzido verticalmente para 0 quando não estiver em uso e que poderá ser escalado verticalmente para, no máximo, 4 nós. 

 **A criação do destino de computação leva cerca de cinco minutos.** Se o recurso de computação já estiver no workspace, o código o usará e ignorará o processo de criação.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Agora você tem os pacotes necessários e recursos de computação para treinar um modelo na nuvem. 

## <a name="explore-data"></a>Explorar dados

Antes de treinar um modelo, você precisa entender os dados que você usa para treiná-lo. Nesta seção, você aprenderá como:

* Baixar o conjunto de dados do MNIST.
* Exibir algumas imagens de exemplo.

### <a name="download-the-mnist-dataset"></a>Baixe o conjunto de dados MNIST

Use o Azure Open DataSets para obter os arquivos de dados MNIST brutos. Os [Conjuntos de dados abertos do Azure](../open-datasets/overview-what-are-open-datasets.md) são conjuntos de dados públicos coletados que você pode usar para adicionar recursos específicos do cenário para soluções de aprendizado de máquina para obter modelos mais precisos. Cada conjunto de dados tem uma classe correspondente, `MNIST` nesse caso, para recuperar os dados de maneiras diferentes.

Esse código recupera os dados como um objeto `FileDataset`, que é uma subclasse de `Dataset`. Uma `FileDataset` faz referência a um ou vários arquivos de qualquer formato em seus armazenamento de dados ou URLs públicas. A classe fornece a capacidade de baixar ou montar os arquivos em sua computação criando uma referência ao local da fonte de dados. Além disso, você registra o conjunto de dados em seu workspace para facilitar a recuperação durante o treinamento.

Siga as [instruções](how-to-create-register-datasets.md) para saber mais sobre conjuntos de dados e seu uso no SDK.

```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)

mnist_file_dataset = mnist_file_dataset.register(workspace=ws,
                                                 name='mnist_opendataset',
                                                 description='training and test dataset',
                                                 create_new_version=True)
```

### <a name="display-some-sample-images"></a>Exibir algumas imagens de exemplo

Carregue os arquivos compactados em `numpy` matrizes. Em seguida, use `matplotlib` para plotar 30 imagens aleatórias do conjunto de dados com seus rótulos acima delas. Esta etapa exige uma função `load_data`, que está incluída em um arquivo `utils.py`. Esse arquivo está incluído na pasta de exemplo. Verifique se ele foi colocado na mesma pasta que este notebook. A função `load_data` simplesmente analisa os arquivos compactados em matrizes numpy.

```python
# make sure utils.py is in the same directory as this code
from utils import load_data
import glob


# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder,"**/train-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder,"**/train-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)


# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Uma amostra aleatória de imagens é exibida:

![Amostra aleatória das imagens](./media/tutorial-train-models-with-aml/digits.png)

Agora você tem uma ideia de como essas imagens se parecem e o resultado esperado da previsão.

## <a name="train-on-a-remote-cluster"></a>Treinar em um cluster remoto

Para esta tarefa, envie o trabalho a ser executado no cluster de treinamento remoto configurado anteriormente.  Para enviar um trabalho é:
* Criar um diretório
* Criar um script de treinamento
* Criar uma configuração de execução de script
* Enviar o trabalho

### <a name="create-a-directory"></a>Criar um diretório

Crie um diretório para entregar o código necessário do seu computador para o recurso remoto.

```python
import os
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Criar um script de treinamento

Para enviar o trabalho para o cluster, primeiro crie um script de treinamento. Execute o seguinte código para criar o script de treinamento chamado `train.py` no diretório que você acabou de criar.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np
import glob

from sklearn.linear_model import LogisticRegression
import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the dataset to mount or download, and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)

print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Observe como o script obtém dados e salva modelos:

+ O script de treinamento lê um argumento para localizar o diretório que contém os dados. Quando você envia o trabalho mais tarde, você aponta para o armazenamento de dados para este argumento: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ O script de treinamento salva o modelo em um diretório chamado **outputs**. Qualquer coisa escrita neste diretório é automaticamente enviada para o seu workspace. Você acessará seu modelo desse diretório posteriormente no tutorial. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ O script de treinamento exige o arquivo `utils.py` para carregar o conjunto de dados corretamente. O código a seguir copia `utils.py` em `script_folder`, de modo que o arquivo possa ser acessado juntamente com o script de treinamento no recurso remoto.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="configure-the-training-job"></a>Configurar um trabalho de treinamento

Crie um objeto [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) para especificar os detalhes da configuração de seu trabalho de treinamento, incluindo o script de treinamento, o ambiente a ser usado e o destino de computação para a execução. Configure o ScriptRunConfig especificando:

* O diretório que contém seus scripts. Todos os arquivos neste diretório são carregados nos nós do cluster para execução.
* O destino de computação. Nesse caso, você usará o cluster de computação do Azure Machine Learning que criou.
* O nome do script de treinamento, **train.py**.
* Um ambiente que contém as bibliotecas necessárias para executar o script.
* Argumentos necessários do script de treinamento.

Neste tutorial, esse destino é o AmlCompute. Todos os arquivos na pasta de scripts são carregados em nós de cluster para execução. O **--data_folder** está definido para usar o conjunto de dados.

Primeiro, crie um ambiente que contém: a biblioteca Scikit-learn, o azureml-dataset-runtime necessário para acessar o conjunto de dados e o azureml-defaults, que contém as dependências para as métricas de registro em log. Os azureml-defaults também contém as dependências necessárias para implantar o modelo como um serviço Web posteriormente na parte 2 do tutorial.

Depois que o ambiente for definido, registre-o no workspace para reutilizá-lo na parte 2 do tutorial.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataset-runtime[pandas,fuse]', 'azureml-defaults'], conda_packages=['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace=ws)
```

Em seguida, crie o ScriptRunConfig especificando o script de treinamento, o destino de computação e o ambiente.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', mnist_file_dataset.as_mount(), '--regularization', 0.5]

src = ScriptRunConfig(source_directory=script_folder,
                      script='train.py', 
                      arguments=args,
                      compute_target=compute_target,
                      environment=env)
```

### <a name="submit-the-job-to-the-cluster"></a>Enviar o trabalho para o cluster

Execute o experimento enviando o objeto ScriptRunConfig:

```python
run = exp.submit(config=src)
run
```

Já que a chamada é assíncrona, ela retorna um estado **Preparando** ou **Em execução** assim que o trabalho é iniciado.

## <a name="monitor-a-remote-run"></a>Monitorar uma execução remota

No total, a primeira execução leva **aproximadamente 10 minutos**. Mas para execuções subsequentes, desde que as dependências do script não mudem, a mesma imagem é reutilizada. Assim, o tempo de inicialização do contêiner é muito mais rápido.

O que acontece enquanto você espera:

- **Criação de imagem**: será criada uma imagem do Docker correspondente ao ambiente Python especificado pelo ambiente do Azure ML. A imagem é carregada no workspace. A criação e o envio da imagem leva **cerca de cinco minutos**.

  Este estágio ocorre uma vez para cada ambiente Python, pois o contêiner é armazenado em cache para execuções subsequentes. Durante a criação da imagem, os logs são transmitidos para o histórico de execução. Você pode monitorar o progresso da criação da imagem usando esses logs.

- **Dimensionamento**: Se o cluster remoto exigir mais nós para realizar a execução que está disponível, mais nós serão adicionados automaticamente. O dimensionamento normalmente leva **cerca de cinco minutos.**

- **Em execução**: Neste estágio, os arquivos e scripts necessários são enviados para o destino de computação. Em seguida, armazenamentos de dados são montados ou copiados. Em seguida, o **entry_script** é executado. Enquanto o trabalho está em execução, o **stdout** e o diretório **./logs** são transmitidos para o histórico de execução. Você pode monitorar o progresso da execução usando esses logs.

- **Pós-processamento**: O diretório **./outputs** da execução é copiado para o histórico de execução em seu workspace para que você possa acessar esses resultados.

Você pode verificar o andamento de um trabalho em execução de várias maneiras. Este tutorial usa um widget do Jupyter, bem como um método `wait_for_completion`.

### <a name="jupyter-widget"></a>Widget de Jupyter

Assista ao progresso da execução com um [widget do Jupyter](/python/api/azureml-widgets/azureml.widgets). Como o envio de execução, o widget é assíncrono e fornece atualizações ao vivo a cada 10 a 15 segundos até o trabalho ser concluído:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

O widget terá a seguinte aparência ao final do treinamento:

![Widget de notebook](./media/tutorial-train-models-with-aml/widget.png)

Se você precisar cancelar uma execução, poderá seguir [estas instruções](./how-to-manage-runs.md).

### <a name="get-log-results-upon-completion"></a>Obter resultados de log após a conclusão

O treinamento e monitoramento do modelo acontecem em segundo plano. Aguarde até que o modelo tenha concluído o treinamento antes de você executar mais código. Use `wait_for_completion` para mostrar quando o treinamento do modelo estiver concluído:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Exibir resultados de execução

Agora você tem um modelo treinado em um cluster remoto. Recupere a precisão do modelo:

```python
print(run.get_metrics())
```

A saída mostra que o modelo remoto tem precisão de 0,9204:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

No próximo tutorial, você explorará esse modelo com mais detalhes.

## <a name="register-model"></a>Registrar modelo

A última etapa do script de treinamento escreveu o arquivo `outputs/sklearn_mnist_model.pkl` em um diretório chamado `outputs` na VM do cluster em que a tarefa é executada. `outputs` é um diretório especial em que todo o conteúdo deste diretório é automaticamente carregado para o seu workspace. Esse conteúdo aparece no registro de execução no experimento em seu workspace. Portanto, o arquivo de modelo agora também está disponível em seu workspace.

Você pode ver os arquivos associados que são executados:

```python
print(run.get_file_names())
```

Registre o modelo no workspace para que você (ou outros colaboradores) possa consultar, examinar e implantar posteriormente esse modelo:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Você também pode excluir apenas o cluster de Computação do Azure Machine Learning. No entanto, o dimensionamento automático está ativado e o cluster mínimo é zero. Portanto, esse recurso específico não incorrerá em encargos de computação adicionais quando não estiver em uso:

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial do Azure Machine Learning, você usou o Python para as seguintes tarefas:

> [!div class="checklist"]
> * Configurar seu ambiente de desenvolvimento.
> * Acessar e examinar os dados.
> * Treinar vários modelos em um cluster remoto usando a popular biblioteca de aprendizado de máquina scikit-learn
> * Analise os detalhes do treinamento e registre o melhor modelo.

Você está pronto para implantar este modelo registrado usando as instruções na próxima parte da série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial 2 - implantar modelos](tutorial-deploy-models-with-aml.md)
