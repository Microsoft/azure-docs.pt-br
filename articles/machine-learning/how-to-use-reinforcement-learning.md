---
title: Treine e implante um modelo de aprendizado reforço (versão prévia).
titleSuffix: Azure Machine Learning
description: Saiba como usar o Azure Machine Learning reforço Learning (versão prévia) para treinar um agente RL para reproduzir o pongue.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.date: 05/05/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 4c03016d003978b3c56361595bec7c559205574b
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520873"
---
# <a name="reinforcement-learning-preview-with-azure-machine-learning"></a>Reinforcement Learning (versão prévia) com Azure Machine Learning



> [!NOTE]
> O Reinforcement Learning do Azure Machine Learning é, no momento, uma versão prévia do recurso. Somente as estruturas Ray e RLlib têm suporte no momento.

Neste artigo, você aprenderá a treinar um agente de RL para jogar o jogo de videogame Pong. Você usa a Biblioteca Python de software livre [Ray RLlib](https://ray.readthedocs.io/en/master/rllib.html) com Azure Machine Learning para gerenciar a complexidade de RL distribuído.

Neste artigo, você aprenderá a:
> [!div class="checklist"]
> * Configurar um experimento
> * Definir nós de cabeçalho e de trabalho
> * Criar um avaliador de RL
> * Enviar um experimento para iniciar uma execução
> * Exibir os resultados

Este artigo se baseia no [exemplo do Pong de RLlib](https://aka.ms/azureml-rl-pong) que pode ser encontrado no [repositório GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md) do notebook do Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um desses ambientes. Recomendamos que você tente Azure Machine Learning instância de computação para obter a experiência de inicialização mais rápida. Você pode clonar e executar rapidamente os notebooks de exemplo reforço em uma instância de computação Azure Machine Learning.

 - Instância de computação do Azure Machine Learning

     - Saiba como clonar notebooks de exemplo no [Tutorial: configurar o ambiente e o workspace](tutorial-1st-experiment-sdk-setup.md).
         - Clone a pasta **how-to-use-azureml** em vez de **tutoriais**
     - Execute o notebook de configuração de rede virtual localizado em `/how-to-use-azureml/reinforcement-learning/setup/devenv_setup.ipynb` para abrir as portas de rede usadas para aprendizado por reforço distribuído.
     - Execute o notebook de exemplo `/how-to-use-azureml/reinforcement-learning/atari-on-distributed-compute/pong_rllib.ipynb`
 
 - Seu próprio servidor Jupyter Notebook

    - Instale o [SDK do Azure Machine Learning](/python/api/overview/azure/ml/install).
    - Instale o [SDK do RL do Azure Machine Learning](/python/api/azureml-contrib-reinforcementlearning/): `pip install --upgrade azureml-contrib-reinforcementlearning`
    - Crie um [arquivo de configuração de workspace](how-to-configure-environment.md#workspace).
    - Execute a rede virtual para abrir portas de rede usadas para aprendizado de reforço distribuído.


## <a name="how-to-train-a-pong-playing-agent"></a>Como treinar um agente para jogar Pong

O RL (Reinforcement Learning) é uma abordagem do machine learning na qual o computador aprende com a prática. Enquanto outras técnicas de machine learning aprendem com a coleta passiva de dados de entrada e com a localização de padrões, o RL usa **agentes em treinamento** para tomar as decisões e aprender ativamente com os resultados.

Os agentes em treinamento aprendem a jogar Pong em um ambiente **simulado**. Os agentes em treinamento fazem uma decisão a cada quadro do jogo para mover a raquete para cima, para baixo ou para ficar no lugar. Ele examina o estado do jogo (uma imagem RGB da tela) para tomar uma decisão.

O RL usa **recompensas** para informar ao agente se suas decisões tiveram êxito. Neste exemplo, o agente Obtém uma recompensa positiva quando pontua um ponto e uma recompensa negativa quando um ponto é pontuado em relação a ele. Em muitas iterações, o agente em treinamento aprende a escolher a ação, com base em seu estado atual, que otimiza a soma das recompensas futuras esperadas. É comum usar **redes neurais profundas** (DNN) para executar essa otimização em RL. 

O treinamento termina quando o agente atinge uma pontuação média de recompensa de 18 em um período de treinamento. Isso significa que o agente superou seu adversário por uma média de pelo menos 18 pontos em partidas de até 21 pontos.

O processo de iteração por meio de simulação e readaptação de um DNN é computacionalmente caro e requer muitos dados. Um modo de melhorar o desempenho dos trabalhos de RL é ao **paralelizar o trabalho** para que vários agentes em treinamento possam agir e aprender simultaneamente. No entanto, gerenciar um ambiente de RL distribuído pode ser uma tarefa complexa.

O Azure Machine Learning fornece a estrutura para gerenciar essas complexidades para escalar horizontalmente suas cargas de trabalho de RL.

## <a name="set-up-the-environment"></a>Configurar o ambiente

Configure o ambiente local RL da:
1. Carregando os pacotes python necessários
1. Inicializando seu espaço de trabalho
1. Criando um experimento
1. Especificando uma rede virtual configurada.

### <a name="import-libraries"></a>Importar bibliotecas

Importe os pacotes Python necessários para executar o restante deste exemplo.

```python
# Azure ML Core imports
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
from azureml.core.runconfig import EnvironmentDefinition
from azureml.widgets import RunDetails
from azureml.tensorboard import Tensorboard

# Azure ML Reinforcement Learning imports
from azureml.contrib.train.rl import ReinforcementLearningEstimator, Ray
from azureml.contrib.train.rl import WorkerConfiguration
```

### <a name="initialize-a-workspace"></a>Inicializar um workspace

Inicialize um objeto de [espaço de trabalho](concept-workspace.md) do `config.json` arquivo criado [na seção pré-requisitos](#prerequisites). Se você está executando esse código em uma Instância de Computação do Azure Machine Learning, o arquivo de configuração já foi criado para você.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-reinforcement-learning-experiment"></a>Criar um experimento de aprendizado por reforço

Crie um [experimento](/python/api/azureml-core/azureml.core.experiment.experiment) para acompanhar a execução do aprendizado por reforço. No Azure Machine Learning, os experimentos são coleções lógicas de avaliações relacionadas para organizar logs de execução, histórico, saídas e muito mais.

```python
experiment_name='rllib-pong-multi-node'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="specify-a-virtual-network"></a>Especificar uma rede virtual

Para trabalhos de RL que usam vários destinos de computação, você deve especificar uma rede virtual com portas abertas que permitam que nós de trabalho e nós de cabeçalho se comuniquem entre si.

A rede virtual pode estar em qualquer grupo de recursos, mas deve estar na mesma região que seu workspace. Para obter mais informações sobre como configurar sua rede virtual, consulte o bloco de anotações de instalação do espaço de trabalho na seção pré-requisitos. Aqui, você especifica o nome da rede virtual em seu grupo de recursos.

```python
vnet = 'your_vnet'
```

## <a name="define-head-and-worker-compute-targets"></a>Definir destinos de computação de cabeçalho e de trabalho

Este exemplo usa destinos de computação separados para os nós de cabeçalho e de trabalho do Ray. Essas configurações permitem que você dimensione seus recursos de computação para cima e para baixo, dependendo de sua carga de trabalho. Defina o número de nós e o tamanho de cada nó, com base em suas necessidades.

### <a name="head-computing-target"></a>Destino de computação de cabeçalho

Você pode usar um cluster de cabeçalho equipado com GPU para melhorar o desempenho de aprendizado profundo. O nó de cabeçalho treina a rede neural que o agente usa para tomar decisões. O nó de cabeçalho também coleta pontos de dados dos nós de trabalho para treinar a rede neural.

A computação de cabeçalho usa uma única VM (máquina virtual) [`STANDARD_NC6`](../virtual-machines/nc-series.md). Ele tem 6 CPUs virtuais nas quais distribuir o trabalho.


```python
from azureml.core.compute import AmlCompute, ComputeTarget

# choose a name for the Ray head cluster
head_compute_name = 'head-gpu'
head_compute_min_nodes = 0
head_compute_max_nodes = 2

# This example uses GPU VM. For using CPU VM, set SKU to STANDARD_D2_V2
head_vm_size = 'STANDARD_NC6'

if head_compute_name in ws.compute_targets:
    head_compute_target = ws.compute_targets[head_compute_name]
    if head_compute_target and type(head_compute_target) is AmlCompute:
        print(f'found head compute target. just use it {head_compute_name}')
else:
    print('creating a new head compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = head_vm_size,
                                                                min_nodes = head_compute_min_nodes, 
                                                                max_nodes = head_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    head_compute_target = ComputeTarget.create(ws, head_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    head_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(head_compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="worker-computing-cluster"></a>Cluster de computação de trabalho

Este exemplo usa quatro VMs [`STANDARD_D2_V2`](../virtual-machines/nc-series.md) para o destino de computação de trabalho. Cada nó de trabalho tem duas CPUs disponíveis para um total de 8 CPUs disponíveis.

As GPUs não são necessárias para os nós de trabalho, pois não estão executando aprendizado profundo. Os trabalhos executam as simulações de jogos e coletam dados.

```python
# choose a name for your Ray worker cluster
worker_compute_name = 'worker-cpu'
worker_compute_min_nodes = 0 
worker_compute_max_nodes = 4

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
worker_vm_size = 'STANDARD_D2_V2'

# Create the compute target if it hasn't been created already
if worker_compute_name in ws.compute_targets:
    worker_compute_target = ws.compute_targets[worker_compute_name]
    if worker_compute_target and type(worker_compute_target) is AmlCompute:
        print(f'found worker compute target. just use it {worker_compute_name}')
else:
    print('creating a new worker compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = worker_vm_size,
                                                                min_nodes = worker_compute_min_nodes, 
                                                                max_nodes = worker_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    worker_compute_target = ComputeTarget.create(ws, worker_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    worker_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(worker_compute_target.get_status().serialize())
```

## <a name="create-a-reinforcement-learning-estimator"></a>Criar um avaliador de aprendizado por reforço
Use o [ReinforcementLearningEstimator](/python/api/azureml-contrib-reinforcementlearning/azureml.contrib.train.rl.reinforcementlearningestimator) para enviar um trabalho de treinamento para Azure Machine Learning.

O Azure Machine Learning usa classes do avaliador para encapsular informações de configuração de execução. Isso permite que você especifique como configurar uma execução de script. 

### <a name="define-a-worker-configuration"></a>Definir uma configuração de trabalho

O objeto WorkerConfiguration informa Azure Machine Learning como inicializar o cluster de trabalho que executa o script de entrada.

```python
# Pip packages we will use for both head and worker
pip_packages=["ray[rllib]==0.8.3"] # Latest version of Ray has fixes for isses related to object transfers

# Specify the Ray worker configuration
worker_conf = WorkerConfiguration(
    
    # Azure ML compute cluster to run Ray workers
    compute_target=worker_compute_target, 
    
    # Number of worker nodes
    node_count=4,
    
    # GPU
    use_gpu=False, 
    
    # PIP packages to use
    pip_packages=pip_packages
)
```

### <a name="define-script-parameters"></a>Definir parâmetros de script

O script de entrada `pong_rllib.py` aceita uma lista de parâmetros que define como executar o trabalho de treinamento. Passar esses parâmetros por meio do avaliador como uma camada de encapsulamento facilita a alteração dos parâmetros de script e a execução das configurações de modo independente.

Especificar o correto `num_workers` faz o máximo proveito de seus esforços de paralelização. Defina o número de trabalhos como o mesmo que o número de CPUs disponíveis. Para este exemplo, você pode usar o seguinte cálculo:

O nó de cabeçalho é um [Standard_NC6](../virtual-machines/nc-series.md) com seis vCPUs. O cluster de trabalho é composto por quatro [VMs Standard_D2_V2](../cloud-services/cloud-services-sizes-specs.md#dv2-series) com duas CPUs cada, para um total de oito CPUs. No entanto, você deve subtrair uma CPU da contagem de trabalho, pois uma deve ser dedicada à função do nó de cabeçalho.

6 CPUs + 8 CPUs - 1 CPU principal = 13 trabalhos simultâneos. O Azure Machine Learning usa clusters de cabeçalho e de trabalho para distinguir recursos de computação. No entanto, Ray não distingue entre Head e Workers, e todas as CPUs estão disponíveis como threads de trabalho.


```python
training_algorithm = "IMPALA"
rl_environment = "PongNoFrameskip-v4"

# Training script parameters
script_params = {
    
    # Training algorithm, IMPALA in this case
    "--run": training_algorithm,
    
    # Environment, Pong in this case
    "--env": rl_environment,
    
    # Add additional single quotes at the both ends of string values as we have spaces in the 
    # string parameters, outermost quotes are not passed to scripts as they are not actually part of string
    # Number of GPUs
    # Number of ray workers
    "--config": '\'{"num_gpus": 1, "num_workers": 13}\'',
    
    # Target episode reward mean to stop the training
    # Total training time in seconds
    "--stop": '\'{"episode_reward_mean": 18, "time_total_s": 3600}\'',
}
```

### <a name="define-the-reinforcement-learning-estimator"></a>Definir um avaliador de aprendizado por reforço

Use a lista de parâmetros e o objeto de configuração de trabalho para construir o avaliador.

```python
# RL estimator
rl_estimator = ReinforcementLearningEstimator(
    
    # Location of source files
    source_directory='files',
    
    # Python script file
    entry_script="pong_rllib.py",
    
    # Parameters to pass to the script file
    # Defined above.
    script_params=script_params,
    
    # The Azure ML compute target set up for Ray head nodes
    compute_target=head_compute_target,
    
    # Pip packages
    pip_packages=pip_packages,
    
    # GPU usage
    use_gpu=True,
    
    # RL framework. Currently must be Ray.
    rl_framework=Ray(),
    
    # Ray worker configuration defined above.
    worker_configuration=worker_conf,
    
    # How long to wait for whole cluster to start
    cluster_coordination_timeout_seconds=3600,
    
    # Maximum time for the whole Ray job to run
    # This will cut off the run after an hour
    max_run_duration_seconds=3600,
    
    # Allow the docker container Ray runs in to make full use
    # of the shared memory available from the host OS.
    shm_size=24*1024*1024*1024
)
```

### <a name="entry-script"></a>Script de entrada

O [script de entrada](https://aka.ms/azure-rl-pong-script) `pong_rllib.py` treina uma rede neural usando o [ambiente Gym do OpenAI](https://github.com/openai/gym/) `PongNoFrameSkip-v4`. Os Gyms do OpenAI são interfaces padronizadas para testar algoritmos de aprendizado por reforço em jogos clássicos do Atari.

Este exemplo usa um algoritmo de treinamento conhecido como [IMPALA](https://arxiv.org/abs/1802.01561) (Importance Weighted Actor-Learner Architecture). O IMPALA paraleliza cada ator de aprendizado individual para dimensionar entre vários nós de computação sem afetar a velocidade ou a estabilidade.

O [Ray Tune](https://ray.readthedocs.io/en/latest/tune.html) orquestra as tarefas de trabalho do IMPALA.

```python
import ray
import ray.tune as tune
from ray.rllib import train

import os
import sys

from azureml.core import Run
from utils import callbacks

DEFAULT_RAY_ADDRESS = 'localhost:6379'

if __name__ == "__main__":

    # Parse arguments
    train_parser = train.create_parser()

    args = train_parser.parse_args()
    print("Algorithm config:", args.config)

    if args.ray_address is None:
        args.ray_address = DEFAULT_RAY_ADDRESS

    ray.init(address=args.ray_address)

    tune.run(run_or_experiment=args.run,
             config={
                 "env": args.env,
                 "num_gpus": args.config["num_gpus"],
                 "num_workers": args.config["num_workers"],
                 "callbacks": {"on_train_result": callbacks.on_train_result},
                 "sample_batch_size": 50,
                 "train_batch_size": 1000,
                 "num_sgd_iter": 2,
                 "num_data_loader_buffers": 2,
                 "model": {
                    "dim": 42
                 },
             },
             stop=args.stop,
             local_dir='./logs')
```

### <a name="logging-callback-function"></a>Função de retorno de chamada de registro em log


O script de entrada usa uma função de utilitário para definir uma [função de retorno de chamada RLlib personalizada](https://docs.ray.io/en/latest/rllib-training.html#callbacks-and-custom-metrics) para registrar métricas em seu workspace do Azure Machine Learning. Saiba como exibir essas métricas na seção [Monitorar e exibir resultados](#monitor-and-view-results).

```python
'''RLlib callbacks module:
    Common callback methods to be passed to RLlib trainer.
'''
from azureml.core import Run

def on_train_result(info):
    '''Callback on train result to record metrics returned by trainer.
    '''
    run = Run.get_context()
    run.log(
        name='episode_reward_mean',
        value=info["result"]["episode_reward_mean"])
    run.log(
        name='episodes_total',
        value=info["result"]["episodes_total"])
```

## <a name="submit-a-run"></a>Enviar uma execução

A [execução](/python/api/azureml-core/azureml.core.run%28class%29) trata do histórico de execução de trabalhos em andamento ou completos. 

```python
run = exp.submit(config=rl_estimator)
```
> [!NOTE]
> Ela pode levar de 30 a 45 minutos para ser concluída.

## <a name="monitor-and-view-results"></a>Monitorar e exibir resultados

Use o widget do Jupyter do Azure Machine Learning para ver o status de suas execuções em tempo real. O widget mostra duas execuções filhas: uma para o Head e outra para os trabalhadores. 

```python
from azureml.widgets import RunDetails

RunDetails(run).show()
run.wait_for_completion()
```

1. Aguarde até que o widget seja carregado.
1. Selecione a execução de cabeçalho na lista de execuções.

Selecione **Clique aqui para ver a execução no estúdio do Azure Machine Learning** para obter informações adicionais de execução no estúdio. Você pode acessar essas informações enquanto a execução estiver em andamento ou após sua conclusão.

![Gráfico de linhas mostrando como a execução detalha o widget](./media/how-to-use-reinforcement-learning/pong-run-details-widget.png)

O gráfico **episode_reward_mean** mostra o número médio de pontos feitos por período de treinamento. Você pode ver que o agente em treinamento teve uma execução inicial insatisfatória, perdendo as partidas ao não fazer pontos (mostrado por um reward_mean de -21). Dentro de 100 iterações, o agente em treinamento aprendeu a superar o computador adversário por uma média de 18 pontos.

Se você procurar os logs da execução filho, poderá ver os resultados da avaliação registrados no arquivo driver_log.txt. Talvez seja necessário aguardar alguns minutos para que essas métricas fiquem disponíveis na página Executar.

Em suma trabalho, você aprendeu a configurar vários recursos de computação para treinar um agente de aprendizado de reforço para executar o pongue muito bem em um computador oppponent.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a treinar um agente de aprendizado por reforço usando um agente de aprendizado do IMPALA. Para ver exemplos adicionais, vá para o [Repositório GitHub do Reinforcement Learning do Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md).