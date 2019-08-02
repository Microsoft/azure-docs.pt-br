---
title: 'Tutorial de classificação de imagens: Treinar modelos'
titleSuffix: Azure Machine Learning service
description: Saiba como treinar um modelo de classificação de imagem com scikit-learn em um Python Jupyter Notebook com o Serviço do Azure Machine Learning. Este tutorial é parte de uma série de duas partes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: 97e3fcb732e85f8c190a0d6607d85a6ffc8d36a7
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370750"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn-using-azure-machine-learning"></a>Tutorial: Treinar modelos de classificação de imagem usando dados MNIST e scikit-learn com o Azure Machine Learning

Neste tutorial, você treina um modelo de machine learning em recursos remotos de computação. Você usará o fluxo de trabalho de treinamento e implantação do serviço Azure Machine Learning (versão prévia) em um Jupyter Notebook Python.  Você pode usar o notebook como um modelo para treinar seu próprio modelo de aprendizado de máquina com seus próprios dados. Este tutorial é **parte uma de uma série de tutoriais de duas partes**.  

Este tutorial treina uma regressão logística simples, usando o conjunto de dados do [MNIST](http://yann.lecun.com/exdb/mnist/) e o [scikit-learn](https://scikit-learn.org) com o Serviço do Azure Machine Learning. MNIST é um conjunto de dados popular que consiste em 70.000 imagens em escala de cinza. Cada imagem é um dígito manuscrito de 28 x 28 pixels, representando um número de zero a nove. A meta é criar um classificador de várias classes para identificar o dígito que uma determinada imagem representa.

Saiba como executar as seguintes ações:

> [!div class="checklist"]
> * Configurar seu ambiente de desenvolvimento.
> * Acessar e examinar os dados.
> * Treinar um modelo de regressão logística simples em um cluster remoto.
> * Analisar os resultados do treinamento e registrar o melhor modelo.

Você aprenderá como selecionar um modelo e implantá-lo na [parte dois deste tutorial](tutorial-deploy-models-with-aml.md).

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

>[!NOTE]
> O código deste artigo foi testado com a versão 1.0.41 do SDK do Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

Vá para [Configurar seu ambiente de desenvolvimento](#start) para ler as etapas do notebook ou use as instruções abaixo para obter o notebook e executá-lo em Azure Notebooks ou seu próprio servidor de notebook.  Para executar o notebook, você precisará de:

* Um servidor de notebook do Python 3.6 com o seguinte instalado:
    * O SDK do Azure Machine Learning para Python
    * `matplotlib` e `scikit-learn`
* O notebook do tutorial e o arquivo **utils.py**
* Um workspace do machine learning
* O arquivo de configuração para o workspace no mesmo diretório que o notebook

Obtenha todos esses pré-requisitos de qualquer uma das seções a seguir.

* Usar um [servidor de notebook de nuvem em seu workspace](#azure)
* Usar [seu próprio servidor de notebook](#server)

### <a name="azure"></a>Usar um servidor de notebook de nuvem em seu workspace

É fácil começar a usar seu próprio servidor de notebook baseado em nuvem. O [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk) já está instalado e configurado depois que você cria este recurso de nuvem.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Depois de iniciar a página da Web do notebook, abra o notebook **tutorials/img-classification-part1-training.ipynb**.

### <a name="server"></a>Use seu próprio servidor do Jupyter Notebook

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

 Depois de concluir as etapas, execute o notebook **tutorials/img-classification-part1-training.ipynb** no diretório clonado.

## <a name="start"></a>Configurar seu ambiente de desenvolvimento

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
print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
```

### <a name="create-an-experiment"></a>Criar uma experiência

Crie um teste para acompanhar as execuções em seu workspace. Um workspace pode ter vários experimentos:

```python
from azureml.core import Experiment
experiment_name = 'sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-resource"></a>Criar ou anexar a um recurso de computação existente

Usando a Computação do Azure Machine Learning, um serviço gerenciado, os cientistas de dados podem treinar modelos de aprendizado de máquina em clusters de máquinas virtuais do Azure. Exemplos incluem máquinas virtuais com suporte a GPU. Neste tutorial, você cria a Computação do Azure Machine Learning como seu ambiente de treinamento. O código abaixo criará clusters de computação para você se eles ainda não existirem em seu workspace.

 **A criação do cluster de computação leva cerca de cinco minutos.** Se o cluster de computação já estiver no workspace, o código o usará e ignorará o processo de criação.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
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

Antes de treinar um modelo, você precisa entender os dados que você usa para treiná-lo. Você também precisa copiar os dados para a nuvem. Em seguida, eles podem ser acessados pelo seu ambiente de treinamento de nuvem. Nesta seção, você aprende como executar as seguintes ações:

* Baixar o conjunto de dados do MNIST.
* Exibir algumas imagens de exemplo.
* Carregar dados para a nuvem.

### <a name="download-the-mnist-dataset"></a>Baixe o conjunto de dados MNIST

Baixe o conjunto de dados MNIST e salvar os arquivos em um `data` directory localmente. Imagens e rótulos, tanto para treinamento quanto para teste, são baixados:

```python
import urllib.request
import os

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-labels.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-labels.gz'))
```

Você verá uma saída semelhante a esta: ```('./data/test-labels.gz', <http.client.HTTPMessage at 0x7f40864c77b8>)```

### <a name="display-some-sample-images"></a>Exibir algumas imagens de exemplo

Carregue os arquivos compactados em `numpy` matrizes. Em seguida, use `matplotlib` para plotar 30 imagens aleatórias do conjunto de dados com seus rótulos acima delas. Esta etapa exige uma função `load_data`, que está incluída em um arquivo `util.py`. Esse arquivo está incluído na pasta de exemplo. Verifique se ele foi colocado na mesma pasta que este notebook. A função `load_data` simplesmente analisa os arquivos compactados em matrizes numpy:

```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(
    data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(
    data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)

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

### <a name="upload-data-to-the-cloud"></a>Carregar dados para a nuvem

Agora, torne os dados acessíveis remotamente enviando-os do computador local para o Azure. Então, ele pode ser acessado para treinamento remoto. O armazenamento de dados é um constructo prático associado ao seu workspace para você fazer upload/download de dados. Você também pode interagir com ele de seus destinos de computação remotos. Ele tem suporte de uma conta de Armazenamento de Blobs do Azure.

Os arquivos do MNIST são carregados em um diretório chamado `mnist` na raiz do armazenamento de dados:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir=data_folder, target_path='mnist',
          overwrite=True, show_progress=True)
```

Agora você tem tudo de que precisa para começar a treinar um modelo.

## <a name="train-on-a-remote-cluster"></a>Treinar em um cluster remoto

Para esta tarefa, envie o trabalho para o cluster de treinamento remoto que você configurou anteriormente.  Para enviar um trabalho é:
* Criar um diretório
* Criar um script de treinamento
* Criar um objeto avaliador
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

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
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

### <a name="create-an-estimator"></a>Criar um estimador

Um objeto [Estimador do SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) é usado para enviar a execução. Crie seu estimador executando o seguinte código para definir estes itens:

* O nome do objeto estimador, `est`.
* O diretório que contém seus scripts. Todos os arquivos neste diretório são carregados nos nós do cluster para execução.
* O destino de computação. Nesse caso, você usará o cluster de computação do Azure Machine Learning que criou.
* O nome do script de treinamento, **train.py**.
* Parâmetros necessários do script de treinamento.

Neste tutorial, esse destino é o AmlCompute. Todos os arquivos na pasta de scripts são carregados em nós de cluster para execução. O **data_folder** está configurado para usar o armazenamento de dados, `ds.path('mnist').as_mount()`:

```python
from azureml.train.sklearn import SKLearn

script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              entry_script='train.py')
```

### <a name="submit-the-job-to-the-cluster"></a>Enviar o trabalho para o cluster

Execute o experimento enviando o objeto avaliador:

```python
run = exp.submit(config=est)
run
```

Já que a chamada é assíncrona, ela retorna um estado **Preparando** ou **Em execução** assim que o trabalho é iniciado.

## <a name="monitor-a-remote-run"></a>Monitorar uma execução remota

No total, a primeira execução leva **aproximadamente 10 minutos**. Mas para execuções subsequentes, desde que as dependências do script não mudem, a mesma imagem é reutilizada. Assim, o tempo de inicialização do contêiner é muito mais rápido.

O que acontece enquanto você espera:

- **Criação de imagem**: Uma imagem do Docker é criada, correspondendo ao ambiente Python especificado pelo estimador. A imagem é carregada no workspace. A criação e o envio da imagem leva **cerca de cinco minutos**.

  Este estágio ocorre uma vez para cada ambiente Python, pois o contêiner é armazenado em cache para execuções subsequentes. Durante a criação da imagem, os logs são transmitidos para o histórico de execução. Você pode monitorar o progresso da criação da imagem usando esses logs.

- **Dimensionamento**: Se o cluster remoto exigir mais nós para realizar a execução que está disponível, mais nós serão adicionados automaticamente. O dimensionamento normalmente leva **cerca de cinco minutos.**

- **Executando**: Neste estágio, os arquivos e scripts necessários são enviados para o destino de computação. Em seguida, armazenamentos de dados são montados ou copiados. Em seguida, o **entry_script** é executado. Enquanto o trabalho está em execução, o **stdout** e o diretório **./logs** são transmitidos para o histórico de execução. Você pode monitorar o progresso da execução usando esses logs.

- **Pós-processamento**: O diretório **./outputs** da execução é copiado para o histórico de execução em seu workspace para que você possa acessar esses resultados.

Você pode verificar o andamento de um trabalho em execução de várias maneiras. Este tutorial usa um widget do Jupyter, bem como um método `wait_for_completion`.

### <a name="jupyter-widget"></a>Widget de Jupyter

Assista ao progresso da execução com um [widget do Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Como o envio de execução, o widget é assíncrono e fornece atualizações ao vivo a cada 10 a 15 segundos até o trabalho ser concluído:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

O widget terá a seguinte aparência ao final do treinamento:

![Widget de notebook](./media/tutorial-train-models-with-aml/widget.png)

Se você precisar cancelar uma execução, poderá seguir [estas instruções](https://aka.ms/aml-docs-cancel-run).

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

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Você também pode excluir apenas o cluster de Computação do Azure Machine Learning. No entanto, o dimensionamento automático está ativado e o cluster mínimo é zero. Portanto, esse recurso específico não incorrerá em encargos de computação adicionais quando não estiver em uso:

```python
# optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial do Serviço do Azure Machine Learning, você usou o Python para as seguintes tarefas:

> [!div class="checklist"]
> * Configurar seu ambiente de desenvolvimento.
> * Acessar e examinar os dados.
> * Treinar vários modelos em um cluster remoto usando a popular biblioteca de aprendizado de máquina scikit-learn
> * Analise os detalhes do treinamento e registre o melhor modelo.

Você está pronto para implantar este modelo registrado usando as instruções na próxima parte da série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial 2 - implantar modelos](tutorial-deploy-models-with-aml.md)
