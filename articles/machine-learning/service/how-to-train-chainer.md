---
title: Treinar e registrar os modelos de Encadeador
titleSuffix: Azure Machine Learning service
description: Este artigo mostra como treinar e registrar um modelo de Encadeador usando o serviço de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 06/15/2019
ms.openlocfilehash: 8ecefccbdf5f02652e935858b6ae8fb4cdfde640
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840032"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning-service"></a>Treinar e registrar os modelos de Encadeador em escala com serviço de Azure Machine Learning

Este artigo mostra como treinar e registrar um modelo de Encadeador usando o serviço de Azure Machine Learning. Ele usa a popular [conjunto de dados MNIST](http://yann.lecun.com/exdb/mnist/) para classificar os dígitos manuscritos usando uma rede neural profunda (DNN) criada usando o [biblioteca Python de Encadeador](https://Chainer.org) em execução na parte superior da [numpy](https://www.numpy.org/).

Encadeador é uma rede neural de alto nível API capaz de executar na parte superior de outras estruturas DNN populares para simplificar o desenvolvimento. Com o serviço de Azure Machine Learning, você pode expandir rapidamente os trabalhos de treinamento usando recursos de computação de nuvem elásticos. Você também pode acompanhar suas execuções de treinamento, modelos de versão, implante modelos e muito mais.

Se você estiver desenvolvendo um modelo de Encadeador desde o início ou está trazendo um modelo existente para a nuvem, o serviço Azure Machine Learning pode ajudá-lo a criar modelos prontos para produção.

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Teste hoje mesmo a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Pré-requisitos

Execute esse código em qualquer um desses ambientes:

- Azure Machine Learning Notebook VM - nenhuma downloads ou a necessidade de instalar

    - Conclua o [início rápido do notebook com base em nuvem](quickstart-run-cloud-notebook.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo.
    - Na pasta exemplos no servidor de notebook, encontrar um bloco de anotações concluído e arquivos na **how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer** pasta.  O bloco de anotações inclui expandidas seções que abrangem o ajuste de hiperparâmetro inteligente, implantação de modelo e os widgets bloco de anotações.

- Seu próprio servidor Jupyter Notebook

    - [Instalar o Azure Machine Learning do SDK para Python](setup-create-workspace.md#sdk)
    - [Criar um arquivo de configuração do espaço de trabalho](setup-create-workspace.md#write-a-configuration-file)
    - Baixe o arquivo de script de exemplo [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py)
     - Você também pode encontrar um concluídos [versão do bloco de anotações do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) deste guia, na página de amostras do GitHub. O bloco de anotações inclui expandidas seções que abrangem o ajuste de hiperparâmetro inteligente, implantação de modelo e os widgets bloco de anotações.

## <a name="set-up-the-experiment"></a>Configure o experimento

Esta seção define o teste de treinamento por carregar os pacotes python necessários, inicializando um espaço de trabalho, criação de um experimento e carregar os dados de treinamento e scripts de treinamento.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe o azureml.core exibição de anúncio de biblioteca do Python o número de versão.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Inicializar um espaço de trabalho

O [espaço de trabalho de serviço de Azure Machine Learning](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefatos que você cria. No SDK do Python, você pode acessar os artefatos de espaço de trabalho com a criação de um [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objeto.

Criar um objeto de espaço de trabalho do `config.json` arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Crie um diretório do projeto
Crie um diretório que contém todo o código necessário em seu computador local que você precisará acessar no recurso remoto. Isso inclui o script de treinamento e quaisquer arquivos adicionais que depende de seu script de treinamento.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Preparar o script de treinamento

Neste tutorial, o script de treinamento **chainer_mnist.py** já é fornecido para você. Na prática, você deve ser capaz de tomar qualquer script de treinamento personalizado que seja e executá-lo com o Azure ML sem a necessidade de modificar seu código.

Para usar o Azure ML recursos de rastreamento e métricas, você precisará adicionar uma pequena quantidade de código de AM do Azure dentro de seu script de treinamento.  O script de treinamento **chainer_mnist.py** mostra como registrar em log algumas métricas para sua execução do Azure ML. Para fazer isso, você acessar o Azure ML `Run` objeto dentro do script.

Copie o script de treinamento **chainer_mnist.py** no seu diretório do projeto.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-an-experiment"></a>Criar uma experiência

Crie um experimento e uma pasta para armazenar seus scripts de treinamento. Neste exemplo, crie um teste chamado "mnist chainer".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Criar ou obter um destino de computação

Você precisará de uma [destino de computação](concept-compute-target.md) para treinar seu modelo. Neste tutorial, você usará a computação de AM do Azure gerenciada (AmlCompute) para o recurso de computação remota de treinamento.

**Criação de AmlCompute leva aproximadamente 5 minutos**. Se o AmlCompute com esse nome já está em seu espaço de trabalho, esse código vai ignorar o processo de criação.  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

Para obter mais informações sobre destinos de computação, consulte o [o que é um destino de computação](concept-compute-target.md) artigo.

## <a name="create-a-chainer-estimator"></a>Criar um estimador de Encadeador

O [estimador de Encadeador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) fornece uma maneira simples de iniciar trabalhos de treinamento do Encadeador no seu destino de computação.

O avaliador de Encadeador é implementado por meio de genéricos [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe, que pode ser usado para dar suporte a qualquer estrutura. Para obter mais informações sobre o treinamento de modelos usando o avaliador de genérico, consulte [treinar modelos com o Azure Machine Learning usando o Vistoriador](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>Enviar uma execução

O [executar objeto](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução, enquanto o trabalho está em execução e após sua conclusão.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Que a execução seja executada, ele passa pelas fases a seguir:

- **Preparando**: Uma imagem do docker é criada de acordo com o avaliador de Encadeador. A imagem é carregada no registro de contêiner do espaço de trabalho e armazenados em cache para as execuções posteriores. Os logs também são transmitidos para o histórico de execução e podem ser exibidos para monitorar o progresso.

- **Dimensionamento**: O cluster tenta escalar verticalmente se o cluster de IA do lote requer mais nós para a execução que estão disponíveis no momento.

- **Executando**: Todos os scripts na pasta de script são carregados para o destino de computação, armazenamentos de dados sejam montados ou copiados e o entry_script é executado. Saídas de stdout e o. / pasta logs serão transmitidos para o histórico de execução e pode ser usado para monitorar a execução.

- **Pós-processamento**: A. / saídas de pasta da execução é copiada para o histórico de execução.

## <a name="save-and-register-the-model"></a>Salve e registrar o modelo

Depois que você treinou o modelo, você pode salvar e registrá-lo ao seu espaço de trabalho. Registro de modelo permite que você store e a versão de seus modelos em seu espaço de trabalho para simplificar [gerenciamento e implantação de modelo](concept-model-management-and-deployment.md).

Adicione o seguinte código para seu script de treinamento **chainer_mnist.py**, para salvar o modelo. 

``` Python
    serializers.save_npz(os.path.join(args.output_dir, 'model.npz'), model)
```

Registre o modelo para seu espaço de trabalho com o código a seguir.

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você treinou um modelo de Encadeador no serviço do Azure Machine Learning. 

* Para saber como implantar um modelo, continue no nosso [implantação de modelo](how-to-deploy-and-where.md) artigo.

* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)

* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
