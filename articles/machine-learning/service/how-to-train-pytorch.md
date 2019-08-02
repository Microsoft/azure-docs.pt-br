---
title: Treinar e registrar os modelos de PyTorch
titleSuffix: Azure Machine Learning service
description: Este artigo mostra como treinar e registrar um modelo de PyTorch usando o serviço de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: d9c953eeecedf14a8f3fae43c5d4713252d58b4c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840092"
---
# <a name="train-and-register-pytorch-models-at-scale-with-azure-machine-learning-service"></a>Treinar e registrar o PyTorch modelos em escala com serviço de Azure Machine Learning

Este artigo mostra como treinar e registrar um modelo de PyTorch usando o serviço de Azure Machine Learning. Ele se baseia [tutorial de aprendizado de transferência de PyTorch](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) que cria um classificador de rede neural profunda (DNN) para imagens de chickens e turkeys.

[PyTorch](https://pytorch.org/) é uma estrutura de código-fonte aberto computacional comumente usada para criar redes neurais profundas (DNN). Com o serviço de Azure Machine Learning, você pode expandir rapidamente os trabalhos de treinamento do código-fonte aberto usando recursos de computação de nuvem elásticos. Você também pode acompanhar suas execuções de treinamento, modelos de versão, implante modelos e muito mais.

Se você estiver desenvolvendo um modelo de PyTorch desde o início ou está trazendo um modelo existente para a nuvem, o serviço Azure Machine Learning pode ajudá-lo a criar modelos prontos para produção.

## <a name="prerequisites"></a>Pré-requisitos

Execute esse código em qualquer um desses ambientes:

 - Azure Machine Learning Notebook VM - nenhuma downloads ou a necessidade de instalar

    - Conclua o [início rápido do notebook com base em nuvem](quickstart-run-cloud-notebook.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo.
    - Na pasta exemplos no servidor de notebook, encontrar um bloco de anotações concluído e expandido navegando para este diretório: **how-to-use-azureml > Treinamento com aprendizado profundo > train-hyperparameter-tune-deploy-with-pytorch** pasta. 
 
 - Seu próprio servidor Jupyter Notebook

    - [Instalar o Azure Machine Learning do SDK para Python](setup-create-workspace.md#sdk)
    - [Criar um arquivo de configuração do espaço de trabalho](setup-create-workspace.md#write-a-configuration-file)
    - [Baixar os arquivos de script de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    Você também pode encontrar um concluídos [versão do bloco de anotações do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) deste guia, na página de exemplos do GitHub. O bloco de anotações inclui expandidas seções que abrangem o ajuste de hiperparâmetro inteligente, implantação de modelo e os widgets bloco de anotações.

## <a name="set-up-the-experiment"></a>Configure o experimento

Esta seção define o teste de treinamento por carregar os pacotes python necessários, inicializando um espaço de trabalho, criação de um experimento e carregar os dados de treinamento e scripts de treinamento.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas necessárias do Python.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Inicializar um espaço de trabalho

O [espaço de trabalho de serviço de Azure Machine Learning](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefatos que você cria. No SDK do Python, você pode acessar os artefatos de espaço de trabalho com a criação de um [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objeto.

Criar um objeto de espaço de trabalho do `config.json` arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Criar uma experiência

Crie um experimento e uma pasta para armazenar seus scripts de treinamento. Neste exemplo, crie um teste chamado "pássaros pytorch".

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Obter os dados

O conjunto de dados consiste em cerca de 120 imagens de treinamento cada para turkeys e chickens, com 100 imagens de validação para cada classe. Podemos irá baixar e extrair o conjunto de dados como parte do nosso script de treinamento `pytorch_train.py`. As imagens são um subconjunto do [abrir imagens v5 Dataset](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Preparar scripts de treinamento

Neste tutorial, o script de treinamento, `pytorch_train.py`, já é fornecido. Na prática, você pode executar qualquer script de treinamento personalizado, como está e executá-lo com o serviço Azure Machine Learning.

Carregar o script de treinamento Pytorch, `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

No entanto, se você quiser usar os recursos de métricas e rastreamento de serviço do Azure Machine Learning, você precisará adicionar um código pequeno valor dentro do script de treinamento. Exemplos de métricas de controle podem ser encontrados em `pytorch_train.py`.

## <a name="create-a-compute-target"></a>Criar um destino de computação

Crie um destino de computação para seu trabalho PyTorch ser executado no. Neste exemplo, crie um cluster de computação do Azure Machine Learning habilitada para GPU.

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

## <a name="create-a-pytorch-estimator"></a>Criar um estimador PyTorch

O [PyTorch estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) fornece uma maneira simples de iniciar um trabalho de treinamento PyTorch em um destino de computação.

O avaliador de PyTorch é implementado por meio de genéricos [ `estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe, que pode ser usado para dar suporte a qualquer estrutura. Para obter mais informações sobre o treinamento de modelos usando o avaliador de genérico, consulte [treinar modelos com o Azure Machine Learning usando o Vistoriador](how-to-train-ml-models.md)

Se precisar de seu script de treinamento adicional pip ou conda pacotes para serem executados, você pode ter os pacotes instalados na imagem resultante do docker, passando nomes através de `pip_packages` e `conda_packages` argumentos.

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

## <a name="submit-a-run"></a>Enviar uma execução

O [executar objeto](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução, enquanto o trabalho está em execução e após sua conclusão.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Que a execução seja executada, ele passa pelas fases a seguir:

- **Preparando**: Uma imagem do docker é criada de acordo com o avaliador de PyTorch. A imagem é carregada no registro de contêiner do espaço de trabalho e armazenados em cache para as execuções posteriores. Os logs também são transmitidos para o histórico de execução e podem ser exibidos para monitorar o progresso.

- **Dimensionamento**: O cluster tenta escalar verticalmente se o cluster de IA do lote requer mais nós para a execução que estão disponíveis no momento.

- **Executando**: Todos os scripts na pasta de script são carregados para o destino de computação, armazenamentos de dados sejam montados ou copiados e o entry_script é executado. Saídas de stdout e o. / pasta logs serão transmitidos para o histórico de execução e pode ser usado para monitorar a execução.

- **Pós-processamento**: A. / saídas de pasta da execução é copiada para o histórico de execução.

## <a name="register-or-download-a-model"></a>Registrar ou fazer o download de um modelo

Depois que você treinou o modelo, você pode registrá-lo ao seu espaço de trabalho. Registro de modelo permite que você store e a versão de seus modelos em seu espaço de trabalho para simplificar [gerenciamento e implantação de modelo](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

Você também pode baixar uma cópia local do modelo usando o objeto de execução. No script de treinamento `pytorch_train.py`, um PyTorch Salvar objeto persiste o modelo em uma pasta local (local para o destino de computação). Você pode usar o objeto de execução para baixar uma cópia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Treinamento distribuído

O [ `PyTorch` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) estimador também dá suporte a treinamento distribuído entre os clusters de CPU e GPU. Você pode facilmente executar trabalhos de PyTorch distribuídos e serviço Azure Machine Learning gerenciará a orquestração para você.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) é uma código-fonte aberto, reduzir a estrutura para treinamento distribuído desenvolvidos por Uber. Ele oferece um caminho fácil para os trabalhos de GPU PyTorch distribuídos.

Para usar Horovod, especifique um [ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) do objeto para o `distributed_training` parâmetro no construtor PyTorch. Esse parâmetro garante que a biblioteca Horovod está instalada para que você possa usar em seu script de treinamento.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod e suas dependências serão instaladas para você, portanto, você poderá importá-lo em seu script de treinamento `train.py` da seguinte maneira:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exportar para ONNX

Para otimizar a inferência de tipos com o [tempo de execução ONNX](concept-onnx.md), converta seu modelo PyTorch no formato ONNX. Inferência de tipos ou modelo de pontuação, é a fase em que o modelo implantado é usado para previsão, mais comumente em dados de produção. Consulte a [tutorial](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) para obter um exemplo.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você treinado e registrou um modelo de PyTorch no serviço do Azure Machine Learning. Para saber como implantar um modelo, continue com o nosso artigo de implantação de modelo.

> [!div class="nextstepaction"]
> [Como e onde implantar modelos](how-to-deploy-and-where.md)
* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
