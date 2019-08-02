---
title: Treinar e registrar os modelos do Keras em execução no TensorFlow
titleSuffix: Azure Machine Learning service
description: Este artigo mostra como treinar e registrar um modelo do Keras em execução no TensorFlow usando o serviço de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 06/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9d405b454d755e0c848e9422c8d4cf6e7c505b68
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840058"
---
# <a name="train-and-register-keras-models-at-scale-with-azure-machine-learning-service"></a>Treinar e registrar o Keras modelos em escala com serviço de Azure Machine Learning

Este artigo mostra como treinar e registrar um modelo do Keras baseado TensorFlow usando o serviço de Azure Machine Learning. Ele usa a popular [conjunto de dados MNIST](http://yann.lecun.com/exdb/mnist/) para classificar os dígitos manuscritos usando uma rede neural profunda (DNN) criada usando o [biblioteca Keras Python](https://keras.io) em execução na parte superior da [TensorFlow](https://www.tensorflow.org/overview) .

Keras é uma rede neural de alto nível API capaz de executar a parte superior de outras estruturas DNN populares para simplificar o desenvolvimento. Com o serviço de Azure Machine Learning, você pode expandir rapidamente os trabalhos de treinamento usando recursos de computação de nuvem elásticos. Você também pode acompanhar suas execuções de treinamento, modelos de versão, implante modelos e muito mais.

Se você estiver desenvolvendo um modelo do Keras desde o início ou está trazendo um modelo existente para a nuvem, o serviço Azure Machine Learning pode ajudá-lo a criar modelos prontos para produção.

## <a name="prerequisites"></a>Pré-requisitos

Execute esse código em qualquer um desses ambientes:

 - Azure Machine Learning Notebook VM - nenhuma downloads ou a necessidade de instalar

     - Conclua o [início rápido do notebook com base em nuvem](quickstart-run-cloud-notebook.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo.
    - Na pasta exemplos no servidor de notebook, encontrar um bloco de anotações concluído e expandido navegando para este diretório: **how-to-use-azureml > Treinamento com aprendizado profundo > train-hyperparameter-tune-deploy-with-keras** pasta. 
 
 - Seu próprio servidor Jupyter Notebook

     - [Instalar o Azure Machine Learning do SDK para Python](setup-create-workspace.md#sdk)
    - [Criar um arquivo de configuração do espaço de trabalho](setup-create-workspace.md#write-a-configuration-file)
    - [Baixar os arquivos de script de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` e `utils.py`
     
    Você também pode encontrar um concluídos [versão do bloco de anotações do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) deste guia, na página de exemplos do GitHub. O bloco de anotações inclui expandidas seções que abrangem o ajuste de hiperparâmetro inteligente, implantação de modelo e os widgets bloco de anotações.

## <a name="set-up-the-experiment"></a>Configure o experimento

Esta seção define o teste de treinamento por carregar os pacotes python necessários, inicializando um espaço de trabalho, criação de um experimento e carregar os dados de treinamento e scripts de treinamento.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas necessárias do Python.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicializar um espaço de trabalho

O [espaço de trabalho de serviço de Azure Machine Learning](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefatos que você cria. No SDK do Python, você pode acessar os artefatos de espaço de trabalho com a criação de um [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objeto.

Criar um objeto de espaço de trabalho do `config.json` arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Criar uma experiência

Crie um experimento e uma pasta para armazenar seus scripts de treinamento. Neste exemplo, crie um teste chamado "mnist keras".

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Carregar o conjunto de dados e scripts

O [datastore](how-to-access-data.md) é um lugar onde os dados podem ser armazenados e acessados pela montagem ou copiar os dados para o destino de computação. Cada espaço de trabalho fornece um repositório de dados padrão. Carregar os dados e scripts de treinamento para o repositório de dados para que eles possam ser facilmente acessados durante o treinamento.

1. Baixe o conjunto de dados MNIST localmente.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Carregue o conjunto de dados MNIST no repositório de dados padrão.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Carregar o script de treinamento do Keras `keras_mnist.py`e o arquivo auxiliar, `utils.py`.

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Criar um destino de computação

Crie um destino de computação para seu trabalho de TensorFlow ser executado no. Neste exemplo, crie um cluster de computação do Azure Machine Learning habilitada para GPU.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Para obter mais informações sobre destinos de computação, consulte o [o que é um destino de computação](concept-compute-target.md) artigo.

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Criar um estimador de TensorFlow e importar o Keras

O [TensorFlow estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornece uma maneira simples de iniciar trabalhos de treinamento de TensorFlow no destino de computação. Uma vez que o Keras é executada sobre TensorFlow, você pode usar o avaliador de TensorFlow e importe a biblioteca do Keras usando o `pip_packages` argumento.

O avaliador de TensorFlow é implementado por meio de genéricos [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe, que pode ser usado para dar suporte a qualquer estrutura. Para obter mais informações sobre o treinamento de modelos usando o avaliador de genérico, consulte [treinar modelos com o Azure Machine Learning usando o Vistoriador](how-to-train-ml-models.md)

```Python
script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Enviar uma execução

O [executar objeto](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução, enquanto o trabalho está em execução e após sua conclusão.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Que a execução seja executada, ele passa pelas fases a seguir:

- **Preparando**: Uma imagem do docker é criada de acordo com o avaliador de TensorFlow. A imagem é carregada no registro de contêiner do espaço de trabalho e armazenados em cache para as execuções posteriores. Os logs também são transmitidos para o histórico de execução e podem ser exibidos para monitorar o progresso.

- **Dimensionamento**: O cluster tenta escalar verticalmente se o cluster de IA do lote requer mais nós para a execução que estão disponíveis no momento.

- **Executando**: Todos os scripts na pasta de script são carregados para o destino de computação, armazenamentos de dados sejam montados ou copiados e o entry_script é executado. Saídas de stdout e o. / pasta logs serão transmitidos para o histórico de execução e pode ser usado para monitorar a execução.

- **Pós-processamento**: A. / saídas de pasta da execução é copiada para o histórico de execução.

## <a name="register-the-model"></a>Registre o modelo

Depois que você treinou o modelo, você pode registrá-lo ao seu espaço de trabalho. Registro de modelo permite que você store e a versão de seus modelos em seu espaço de trabalho para simplificar [gerenciamento e implantação de modelo](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

Você também pode baixar uma cópia local do modelo. Isso pode ser útil para fazer o trabalho de validação de modelo adicional localmente. No script de treinamento, `mnist-keras.py`, um objeto de proteção de TensorFlow persiste o modelo em uma pasta local (local para o destino de computação). Você pode usar o objeto de execução para baixar uma cópia do repositório de dados.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você treinado e registrou um modelo do Keras no serviço do Azure Machine Learning. Para saber como implantar um modelo, continue com o nosso artigo de implantação de modelo.

> [!div class="nextstepaction"]
> [Como e onde implantar modelos](how-to-deploy-and-where.md)
