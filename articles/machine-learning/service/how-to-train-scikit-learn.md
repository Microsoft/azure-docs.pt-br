---
title: Treinar e registrar scikit-Saiba mais modelos
titleSuffix: Azure Machine Learning service
description: Este artigo mostra como treinar e registrar um scikit-saiba de modelo usando o serviço de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 06/30/2019
ms.custom: seodec18
ms.openlocfilehash: c9e983f7981c1155964617694d2cce86aba741b7
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840013"
---
# <a name="train-and-register-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>Treinar e registrar modelos Scikit-learn em escala com serviço de Azure Machine Learning

Este artigo mostra como treinar e registrar um modelo Scikit-learn usando o serviço de Azure Machine Learning. Ele usa a popular [conjunto de dados íris](https://archive.ics.uci.edu/ml/datasets/iris) classificar imagens de flor de íris com personalizado [scikit-Saiba](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) classe.

Scikit-learn é uma estrutura de código-fonte aberto computacional comumente usada para o aprendizado de máquina. Com o serviço de Azure Machine Learning, você pode expandir rapidamente os trabalhos de treinamento do código-fonte aberto usando recursos de computação de nuvem elásticos. Você também pode acompanhar suas execuções de treinamento, modelos de versão, implante modelos e muito mais.

Se você estiver desenvolvendo um modelo Scikit-learn desde o início ou está trazendo um modelo existente para a nuvem, o serviço Azure Machine Learning pode ajudá-lo a criar modelos prontos para produção.

## <a name="prerequisites"></a>Pré-requisitos

Execute esse código em qualquer um desses ambientes:
 - Azure Machine Learning Notebook VM - nenhuma downloads ou a necessidade de instalar

    - Conclua o [início rápido do notebook com base em nuvem](quickstart-run-cloud-notebook.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo.
    - Na pasta exemplos no servidor de notebook, encontrar um bloco de anotações concluído e expandido navegando para este diretório: **how-to-use-azureml > Treinamento > train-hyperparameter-tune-deploy-with-sklearn** pasta.

 - Seu próprio servidor Jupyter Notebook

    - [Instalar o Azure Machine Learning do SDK para Python](setup-create-workspace.md#sdk)
    - [Criar um arquivo de configuração do espaço de trabalho](setup-create-workspace.md#write-a-configuration-file)
    - [Baixe o arquivo de script de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn) `train_iris.py`
    - Você também pode encontrar um concluídos [versão do bloco de anotações do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-sklearn.ipynb) deste guia, na página de exemplos do GitHub. O bloco de anotações inclui uma seção expandida abrangendo hiperparâmetro inteligente de ajuste e recuperar o melhor modelo por métricas principais.

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

Crie um experimento e uma pasta para armazenar seus scripts de treinamento. Neste exemplo, crie um teste chamado "sklearn-íris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Carregar o conjunto de dados e scripts

O [datastore](how-to-access-data.md) é um lugar onde os dados podem ser armazenados e acessados pela montagem ou copiar os dados para o destino de computação. Cada espaço de trabalho fornece um repositório de dados padrão. Carregar os dados e scripts de treinamento para o repositório de dados para que eles possam ser facilmente acessados durante o treinamento.

1. Crie o diretório para seus dados.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Carregue o conjunto de dados íris para o repositório de dados padrão.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Script de treinamento de upload do Scikit-learn, `train_iris.py`.

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>Criar ou obter um destino de computação

Crie um destino de computação para seu trabalho Scikit-learn ser executado no. Scikit Saiba só dá suporte a nó único, computação de CPU.

O código a seguir cria uma computação gerenciada do Azure Machine Learning (AmlCompute) para o recurso de computação remota de treinamento. Criação de AmlCompute leva aproximadamente 5 minutos. Se o AmlCompute com esse nome já está em seu espaço de trabalho, esse código vai ignorar o processo de criação.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Para obter mais informações sobre destinos de computação, consulte o [o que é um destino de computação](concept-compute-target.md) artigo.

## <a name="create-a-scikit-learn-estimator"></a>Criar um estimador Scikit-learn

O [Scikit-learn estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornece uma maneira simples de iniciar Scikit-learn trabalho de treinamento em um destino de computação. Ele é implementado por meio de [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) classe, que pode ser usado para dar suporte ao treinamento de CPU de nó único.

Se precisar de seu script de treinamento adicional pip ou conda pacotes para serem executados, você pode ter os pacotes instalados na imagem resultante do docker, passando nomes através de `pip_packages` e `conda_packages` argumentos.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```

## <a name="submit-a-run"></a>Enviar uma execução

O [executar objeto](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução, enquanto o trabalho está em execução e após sua conclusão.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Que a execução seja executada, ele passa pelas fases a seguir:

- **Preparando**: Uma imagem do docker é criada de acordo com o avaliador de TensorFlow. A imagem é carregada no registro de contêiner do espaço de trabalho e armazenados em cache para as execuções posteriores. Os logs também são transmitidos para o histórico de execução e podem ser exibidos para monitorar o progresso.

- **Dimensionamento**: O cluster tenta escalar verticalmente se o cluster de IA do lote requer mais nós para a execução que estão disponíveis no momento.

- **Executando**: Todos os scripts na pasta de script são carregados para o destino de computação, armazenamentos de dados sejam montados ou copiados e o entry_script é executado. Saídas de stdout e o. / pasta logs serão transmitidos para o histórico de execução e pode ser usado para monitorar a execução.

- **Pós-processamento**: A. / saídas de pasta da execução é copiada para o histórico de execução.

## <a name="save-and-register-the-model"></a>Salve e registrar o modelo

Depois que você treinou o modelo, você pode salvar e registrá-lo ao seu espaço de trabalho. Registro de modelo permite que você store e a versão de seus modelos em seu espaço de trabalho para simplificar [gerenciamento e implantação de modelo](concept-model-management-and-deployment.md).

Adicione o seguinte código ao seu script de treinamento, train_iris.py, para salvar o modelo. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registre o modelo para seu espaço de trabalho com o código a seguir.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você treinado e registrou um modelo Scikit-learn no serviço do Azure Machine Learning.

* Para saber como implantar um modelo, continue no nosso [implantação de modelo](how-to-deploy-and-where.md) artigo.

* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)

* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)