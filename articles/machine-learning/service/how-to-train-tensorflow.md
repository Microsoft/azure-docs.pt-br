---
title: Treinar modelos com o TensorFlow & Keras
titleSuffix: Azure Machine Learning service
description: Saiba como executar o nó único e distribuída de treinamento de modelos de TensorFlow, Keras com os TensorFlow e Keras estimadores
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 78db7d21774750892c831ac220244c54594b78f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817730"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Treinar modelos de TensorFlow e o Keras com o serviço Azure Machine Learning

Para treinamento de DNN (rede neural profunda) com o TensorFlow, o Azure Machine Learning oferece uma aula `TensorFlow` personalizada do `Estimator`. O SDK do Azure [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimador (para não ser confundida com o [ `tf.estimator.Estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator) classe) permite que você facilmente enviar trabalhos de treinamento de TensorFlow para execuções de nó único e distribuídas no Azure computação.

## <a name="single-node-training"></a>Treinamento de nó único
O treinamento com o Estimador do `TensorFlow` é semelhante a usar a [base `Estimator`](how-to-train-ml-models.md), por isso, leia primeiro o artigo de instruções e verifique se você entendeu os conceitos apresentados nele.
  
Para executar um trabalho do TensorFlow, instancie um objeto `TensorFlow`. Você já deve ter criado seu objeto de [destino de computação](how-to-set-up-training-targets.md#amlcompute)`compute_target`.

```Python
from azureml.train.dnn import TensorFlow

script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py',
                    conda_packages=['scikit-learn'], # in case you need scikit-learn in train.py
                    use_gpu=True)
```

Agora, especificamos os seguintes parâmetros para o construtor do TensorFlow:

Parâmetro | DESCRIÇÃO
--|--
`source_directory` | O diretório local que contém todo o código necessário para o trabalho de treinamento. Essa pasta é copiada em seu computador local para a computação remota
`script_params` | Especificando os argumentos de linha de comando para o seu script de treinamento de dicionário `entry_script`, na forma de < argumento de linha de comando, valor > pares.  Para especificar um sinalizador detalhado no `script_params`, use `<command-line argument, "">`.
`compute_target` | Destino de computação remota no qual seu script de treinamento será executado, neste caso, um cluster ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) da Computação do Machine Learning
`entry_script` | FilePath (relativo ao `source_directory`) do script de treinamento para ser executado na computação remota. Esse arquivo e quaisquer arquivos adicionais que ele depende devem ser localizados nesta pasta
`conda_packages` | A lista de pacotes do Python a serem instalados via conda, necessária ao seu script de treinamento. Nesse caso, o script de treinamento usa `sklearn` para carregar os dados, então, especifique esse pacote para ser instalado.  O construtor tem outro parâmetro chamado `pip_packages` que você pode usar para qualquer pacote pip necessário
`use_gpu` | Defina esse sinalizador como `True` para aproveitar a GPU para treinamento. Usa `False` como padrão.

Como você está usando o estimador do TensorFlow, o contêiner usado no treinamento incluirá por padrão o pacote do TensorFlow e as dependências relacionadas necessárias para o treinamento nas CPUs e nas GPUs.

Em seguida, envie o trabalho do TensorFlow:
```Python
run = exp.submit(tf_est)
```

## <a name="keras-support"></a>Suporte ao Keras
[Keras](https://keras.io/) é uma API de Python de DNN de alto nível populares que dá suporte a TensorFlow, CNTK ou Theano como back-ends. Se você usar o TensorFlow como back-end, você pode usar facilmente o avaliador de TensFlow para treinar um modelo do Keras. Aqui está um exemplo de um estimador de TensorFlow com o Keras adicionadas a ele:

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py',
                       pip_packages=['keras'], # just add keras through pip
                       use_gpu=True)
```
O construtor de estimador de TensorFlow acima instrui o serviço de Azure Machine Learning para instalar a Keras por meio de pip para o ambiente de execução. E seu `keras_train.py` , em seguida, pode importar a API Keras para treinar um modelo do Keras. Para obter um exemplo completo, explore [este bloco de anotações do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb).

## <a name="distributed-training"></a>Treinamento distribuído
O Estimador do TensorFlow também permite treinar modelos em escala em todos os clusters de CPU e GPU das VMs do Azure. O treinamento do TensorFlow pode ser executado facilmente com algumas chamadas à API. Já o Azure Machine Learning gerenciará nos bastidores toda a infraestrutura e a orquestração necessárias para realizar essas cargas de trabalho.

O Azure Machine Learning é compatível com dois métodos de treinamento distribuído no TensorFlow:
* Treinamento distribuído baseado em MPI usando a estrutura [Horovod](https://github.com/uber/horovod)
* [TensorFlow distribuído](https://www.tensorflow.org/deploy/distributed) nativo por meio do método de servidor de parâmetro

### <a name="horovod"></a>Horovod
O [Horovod](https://github.com/uber/horovod) é uma estrutura de software livre ring-allreduce para treinamento distribuído desenvolvida pela Uber.

Para executar o TensorFlow distribuído usando a estrutura Horovod, crie o objeto do TensorFlow da seguinte maneira:

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi',
                    use_gpu=True)
```

O código acima expõe estes novos parâmetros para o construtor do TensorFlow:

Parâmetro | DESCRIÇÃO | Padrão
--|--|--
`node_count` | O número de nós a serem usados no seu trabalho de treinamento. | `1`
`process_count_per_node` | Número de processos (ou "trabalhos") a serem executado em cada nó.|`1`
`distributed_backend` | O back-end para iniciar distribuído treinamento, o que o avaliador de oferece por meio de MPI. Se você quer realizar treinamento paralelo ou distribuído (por exemplo, `node_count`> 1 ou `process_count_per_node`> 1 ou os dois) com MPI (e Horovod), defina `distributed_backend='mpi'`. A implementação de MPI usada pelo Azure Machine Learning é [MPI Aberta](https://www.open-mpi.org/). | `None`

O exemplo acima executará treinamento distribuído com dois trabalhos, um por nó.

Horovod e suas dependências serão instaladas para você, portanto, você poderá importá-lo em seu script de treinamento `train.py` da seguinte maneira:

```Python
import tensorflow as tf
import horovod
```

Por fim, envie o trabalho do TensorFlow:
```Python
run = exp.submit(tf_est)
```

### <a name="parameter-server"></a>Servidor de parâmetros
Também é possível executar o [TensorFlow distribuído nativo](https://www.tensorflow.org/deploy/distributed), que usa o modelo de servidor de parâmetros. Nesse método, é preciso treinar em um cluster de servidores de parâmetro e trabalhos. Os trabalhos calculam os gradientes durante o treinamento, enquanto os servidores de parâmetros agregam os gradientes.

Construa o objeto do TensorFlow:

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps',
                    use_gpu=True)
```

Preste atenção aos seguintes parâmetros para o construtor do TensorFlow no código acima:

Parâmetro | DESCRIÇÃO | Padrão
--|--|--
`worker_count` | Número de funções de trabalho. | `1`
`parameter_server_count` | Número de servidores de parâmetro. | `1`
`distributed_backend` | Back-end a ser usado para treinamento distribuído. Para realizar o treinamento distribuído por meio do servidor de parâmetros, defina `distributed_backend='ps'` | `None`

#### <a name="note-on-tfconfig"></a>Observe o `TF_CONFIG`
Você também precisa dos endereços de rede e portas do cluster do [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), para que o Azure Machine Learning defina a variável de ambiente `TF_CONFIG` para você.

A variável de ambiente `TF_CONFIG` é uma cadeia de caracteres JSON. Confira um exemplo da variável para um servidor de parâmetros:
```
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Se você estiver usando um nível alto do TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, o TensorFlow irá analisar isso `TF_CONFIG` especificação da variável e o cluster de compilação para você. 

Se você estiver usando as APIs principais de treinamento de baixo nível do TensorFlow, será necessário analisar a variável `TF_CONFIG` e criar o `tf.train.ClusterSpec` por conta própria no código do treinamento. [Neste exemplo](https://aka.ms/aml-notebook-tf-ps), você faria isso no **script de treinamento** da seguinte maneira:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

Após terminar de escrever o script de treinamento e criar o objeto do TensorFlow, será possível enviar o trabalho de treinamento:
```Python
run = exp.submit(tf_est)
```

## <a name="examples"></a>Exemplos

Explorar várias [notebooks no aprendizado aprofundado distribuído no Github](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas
* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
