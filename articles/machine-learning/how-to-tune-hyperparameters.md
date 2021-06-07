---
title: Ajuste de hiperparâmetro de um modelo
titleSuffix: Azure Machine Learning
description: Automatize o ajuste de hiperparâmetro para aprendizado profundo e modelos de aprendizado de máquina usando Azure Machine Learning.
ms.author: anumamah
author: Aniththa
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 02/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 34adcf2218e29572ec9a86583addc7c021313085
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519632"
---
# <a name="hyperparameter-tuning-a-model-with-azure-machine-learning"></a>Hiperparâmetro ajustando um modelo com Azure Machine Learning

Automatize o ajuste eficiente de hiperparâmetro usando Azure Machine Learning [pacote hyperdrive](/python/api/azureml-train-core/azureml.train.hyperdrive). Saiba como concluir as etapas necessárias para ajustar os hiperparâmetros com o [SDK do Azure Machine Learning](/python/api/overview/azure/ml/):

1. Defina o espaço de pesquisa de parâmetro
1. Especificar uma métrica primária para otimizar  
1. Especificar a política de término antecipado para execuções de baixo desempenho
1. Criar e atribuir recursos
1. Iniciar um experimento com a configuração definida
1. Visualizar as execuções de treinamento
1. Selecione a melhor configuração para seu modelo

## <a name="what-is-hyperparameter-tuning"></a>O que é ajuste de hiperparâmetro?

Os **hiperparâmetros** são parâmetros ajustáveis que permitem controlar o processo de treinamento do modelo. Por exemplo, com redes neurais, você decide o número de camadas ocultas e o número de nós em cada camada. O desempenho do modelo depende muito dos hiperparâmetros.

 O **ajuste de hiperparâmetro**, também chamado de **otimização de hiperparâmetro**, é o processo de localizar a configuração de hiperparâmetros que resulta no melhor desempenho. O processo normalmente é computacionalmente caro e manual.

Azure Machine Learning permite automatizar o ajuste de hiperparâmetro e executar experimentos em paralelo para otimizar os hiperparâmetros com eficiência.


## <a name="define-the-search-space"></a>Definir o espaço de pesquisa

Ajuste os hiperparâmetros explorando o intervalo de valores definido para cada hiperparâmetro.

Os hiperparâmetros podem ser discretos ou contínuos e ter uma distribuição de valores descritos por uma [expressão de parâmetro](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions).

### <a name="discrete-hyperparameters"></a>Hiperparâmetros discretos

Hiperparâmetros discretos podem ser especificados como um `choice` entre valores discretos. `choice` pode ser:

* um ou mais valores separados por vírgula
* um `range` objeto
* qualquer objeto `list` arbitrário


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Nesse caso, `batch_size` um dos valores [16, 32, 64, 128] e `number_of_hidden_layers` usa um dos valores [1, 2, 3, 4].

Os seguintes hiperparâmetros discretos avançados também podem ser especificados usando uma distribuição:

* `quniform(low, high, q)` – Retorna um valor como round(uniform(low, high) / q) * q
* `qloguniform(low, high, q)` – Retorna um valor como round(exp(uniform(low, high)) / q) * q
* `qnormal(mu, sigma, q)` – Retorna um valor como round(normal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)` – Retorna um valor como round(exp(normal(mu, sigma)) / q) * q

### <a name="continuous-hyperparameters"></a>Hiperparâmetros contínuos 

Os hiperparâmetros contínuos são especificados como uma distribuição em um intervalo contínuo de valores:

* `uniform(low, high)` – Retorna um valor distribuído uniformemente entre baixo e alto
* `loguniform(low, high)` – Retorna um valor traçado conforme exp(uniform(low, high)) de modo que o logaritmo do valor retornado seja distribuído uniformemente
* `normal(mu, sigma)` – Retorna um valor real normalmente distribuído com sigma de desvio padrão e mu médio
* `lognormal(mu, sigma)` – Retorna um valor traçado conforme exp(normal(mu, sigma)) de modo que o logaritmo do valor retornado é normalmente distribuído

Um exemplo de uma definição de espaço de parâmetro:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Esse código define um espaço de pesquisa com dois parâmetros – `learning_rate` e `keep_probability`. `learning_rate` tem uma distribuição normal com um desvio padrão de 3 e o valor médio 10. `keep_probability` tem uma distribuição uniforme com um valor mínimo de 0,05 e um valor máximo de 0.1.

### <a name="sampling-the-hyperparameter-space"></a>Amostragem do espaço do hiperparâmetro

Especifique o método de amostragem de parâmetro a ser usado no espaço de hiperparâmetro. O Azure Machine Learning dá suporte aos seguintes métodos:

* Amostragem aleatória
* Amostragem de grade
* Amostragem Bayesiana

#### <a name="random-sampling"></a>Amostragem aleatória

A [amostragem aleatória](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling) dá suporte a hiperparâmetros discretos e contínuos. Ele dá suporte ao encerramento antecipado de execuções de baixo desempenho. Alguns usuários fazem uma pesquisa inicial com amostragem aleatória e, em seguida, refinam o espaço de pesquisa para melhorar os resultados.

Na amostragem Aleatória, os valores de hiperparâmetro serão selecionadas aleatoriamente do espaço de pesquisa definido. 

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Amostragem de grade

A [amostragem de grade](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling) dá suporte a hiperparâmetros discretos. Use a amostragem de grade se você puder fazer o orçamento para pesquisar exaustivamente no espaço de pesquisa. Dá suporte ao encerramento antecipado de execuções de baixo desempenho.

A amostragem de grade faz uma pesquisa de grade simples sobre todos os valores possíveis. A amostragem de grade só pode ser usada com `choice` hiperparâmetros. Por exemplo, o seguinte espaço tem seis amostras:

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Amostragem Bayesiana

A [amostragem de Bayesiana](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling) é baseada no algoritmo de otimização de bayesiana. Ele escolhe exemplos com base em como as amostras anteriores faziam, para que novos exemplos melhorem a métrica primária.

A amostragem de Bayesiana será recomendada se você tiver orçamento suficiente para explorar o espaço de hiperparâmetro. Para obter melhores resultados, recomendamos um número máximo de execuções maiores ou iguais a 20 vezes o número de hiperparâmetros que estão sendo ajustados. 

O número de execuções simultâneas tem um impacto na eficácia do processo de ajuste. Um número menor de execuções simultâneas pode levar a uma melhor convergência de amostragem, uma vez que o menor grau de paralelismo aumenta o número de execuções que se beneficiam de execuções previamente concluídas.

A amostragem de Bayesiana dá suporte apenas `choice` `uniform` `quniform` a distribuições, e no espaço de pesquisa.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Especificar métrica primária

Especifique a [métrica primária](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal) que você deseja que o ajuste de hiperparâmetro Otimize. Cada execução de treinamento é avaliada para a métrica principal. A política de término antecipado usa a métrica primária para identificar execuções de baixo desempenho.

Especifique os seguintes atributos para sua métrica primária:

* `primary_metric_name`: O nome da métrica primária precisa corresponder exatamente ao nome da métrica registrada pelo script de treinamento
* `primary_metric_goal`: pode ser tanto `PrimaryMetricGoal.MAXIMIZE` quanto `PrimaryMetricGoal.MINIMIZE` e determina se a métrica primária será maximizada ou minimizada ao avaliar as execuções. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Esta amostra maximiza a "exatidão".

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Registrar em log métricas de ajuste de hiperparâmetro

O script de treinamento para seu modelo **deve** registrar a métrica primária durante o treinamento do modelo para que o hyperdrive possa acessá-la para ajuste de hiperparâmetro.

Registre a métrica primária em seu script de treinamento com o seguinte trecho de código de exemplo:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

O script de treinamento calcula o `val_accuracy` e o registra como a "precisão" da métrica primária. Cada vez que a métrica é registrada, ela é recebida pelo serviço de ajuste de hiperparâmetro. Cabe a você determinar a frequência de relatórios.

Para obter mais informações sobre valores de log em execuções de treinamento de modelo, consulte [habilitar o registro em log nas execuções de treinamento do Azure ml](how-to-track-experiments.md).

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Especificar política de término antecipado

O encerramento automático de desempenho é executado com uma política de término antecipado. O encerramento antecipado melhora a eficiência computacional.

Você pode configurar os seguintes parâmetros que controlam quando uma política é aplicada:

* `evaluation_interval`: a frequência da aplicação da política. Cada vez que o script de treinamento registra em log a métrica primária conta como um intervalo. Um `evaluation_interval` de 1 aplicará a política toda vez que o script de treinamento relatar a métrica primária. Um `evaluation_interval` de 2 aplicará a política a cada vez. Se não for especificado, `evaluation_interval` é definido como 1 por padrão.
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos. Esse é um parâmetro opcional que evita o encerramento prematuro de execuções de treinamento, permitindo que todas as configurações sejam executadas para um número mínimo de intervalos. Se for especificado, a política se aplicará a cada múltiplo de evaluation_interval maior ou igual a delay_evaluation.

O Azure Machine Learning dá suporte às seguintes políticas de finalização antecipada:
* [Política Bandit](#bandit-policy)
* [Política de Encerramento Mediana](#median-stopping-policy)
* [Política de seleção de truncamento](#truncation-selection-policy)
* [Nenhuma política de encerramento](#no-termination-policy-default)


### <a name="bandit-policy"></a>Política Bandit

A [política Bandit](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy#definition) é baseada no fator de margem de atraso/valor da margem de atraso e no intervalo de avaliação. Bandit termina em execuções quando a métrica primária não está dentro do fator de margem de atraso especificado/valor da margem de atraso da execução mais bem-sucedida.

> [!NOTE]
> A amostragem de Bayesiana não dá suporte ao encerramento antecipado. Ao usar a amostragem de Bayesiana, defina `early_termination_policy = None` .

Especifique os seguintes parâmetros de configuração:

* `slack_factor` ou `slack_amount`: a margem de atraso permitida em relação à execução de treinamento de melhor desempenho. `slack_factor` especifica a margem de atraso permitida como uma proporção. `slack_amount` especifica a margem de atraso permitida como um valor absoluto, em vez de uma proporção.

    Por exemplo, considere uma política de Bandit aplicada no intervalo de 10. Suponha que a execução de melhor desempenho no intervalo 10 relatou que uma métrica primária é 0,8 com uma meta para maximizar a métrica primária. Se a política especificar um `slack_factor` de 0,2, qualquer treinamento será executado cuja melhor métrica no intervalo 10 seja menor que 0,66 (0,8/(1 + `slack_factor` )) será encerrada.
* `evaluation_interval`: (opcional) a frequência para aplicar a política
* `delay_evaluation`: (opcional) atrasa a primeira avaliação de política para um número de intervalos especificado


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, a política de encerramento inicial é aplicada a cada intervalo quando as métricas são relatadas, começando no intervalo de avaliação 5. Qualquer execução cuja melhor métrica seja menor que (1/(1+0,1) ou 91% da execução de melhor desempenho será encerrada.

### <a name="median-stopping-policy"></a>Política de Encerramento Mediana

A [parada mediana](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy) é uma política de término inicial com base nas médias em execução das métricas primárias relatadas pelas execuções. Essa política computa as médias em execução em todas as execuções de treinamento e interrompe as execuções cujo valor de métrica primário é pior do que a mediana das médias.

Esta política usa os seguintes parâmetros de configuração:
* `evaluation_interval`: a frequência para aplicar a política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, a política de encerramento inicial é aplicada a cada intervalo começando em um intervalo de avaliação 5. Uma execução será interrompida no intervalo 5 se sua melhor métrica primária for pior do que a mediana das médias em execução nos intervalos de 1:5 em todas as execuções de treinamento.

### <a name="truncation-selection-policy"></a>Política de seleção de truncamento

[Seleção de truncamento](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy) cancela uma porcentagem de execuções de execução mais baixa em cada intervalo de avaliação. As execuções são comparadas usando a métrica primária. 

Esta política usa os seguintes parâmetros de configuração:

* `truncation_percentage`: o percentual de execuções de menor desempenho a encerrar a cada intervalo de avaliação. Um valor inteiro entre 1 e 99.
* `evaluation_interval`: (opcional) a frequência para aplicar a política
* `delay_evaluation`: (opcional) atrasa a primeira avaliação de política para um número de intervalos especificado


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Neste exemplo, a política de encerramento inicial é aplicada a cada intervalo começando em um intervalo de avaliação 5. Uma execução será encerrada no intervalo 5 se seu desempenho no intervalo 5 estiver nos mais baixos 20% do desempenho de todas as execuções no intervalo de 5.

### <a name="no-termination-policy-default"></a>Nenhuma política de encerramento (padrão)

Se nenhuma política for especificada, o serviço de ajuste de hiperparâmetro permitirá que todas as execuções de treinamento sejam executadas até a conclusão.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>Selecionando uma política de encerramento antecipado

* Para uma política conservadora que fornece economia sem encerrar trabalhos promissores, considere um mediano de interrupção de política com `evaluation_interval` 1 e `delay_evaluation` 5. Essas são configurações conservadoras, que podem fornecer aproximadamente 25 a 35% de economia sem perda na métrica primária (com base em nossos dados de avaliação).
* Para economias mais agressivas, use a política Bandit com uma menor margem de atraso permitida ou política de seleção de truncamento com uma porcentagem maior de truncamento.

## <a name="create-and-assign-resources"></a>Criar e atribuir recursos

Controle seu orçamento de recursos especificando o número máximo de execuções de treinamento.

* `max_total_runs`: Número máximo de execuções de treinamento. Deve ser um inteiro entre 1 e 1000.
* `max_duration_minutes`: (opcional) duração máxima, em minutos, do experimento de ajuste de hiperparâmetro. É executado após essa duração ser cancelada.

>[!NOTE] 
>Se tanto `max_total_runs` quanto `max_duration_minutes` forem especificados, o experimento de ajuste de hiperparâmetro será encerrado quando o primeiro dos dois desses limites for atingido.

Além disso, especifique o número máximo de execuções de treinamento a serem executadas simultaneamente durante sua pesquisa de ajuste de hiperparâmetro.

* `max_concurrent_runs`: (opcional) número máximo de execuções que podem ser executadas simultaneamente. Se não for especificado, todas as execuções são iniciadas em paralelo. Se especificado, deve ser um inteiro entre 1 e 100.

>[!NOTE] 
>O número de execuções simultâneas é ligado aos recursos disponíveis no destino de computação especificado. Verifique se o destino de computação tem os recursos disponíveis para a simultaneidade desejada.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Esse código configura o experimento de ajuste de hiperparâmetro para usar um máximo de 20 execuções de total, executando quatro configurações por vez.

## <a name="configure-hyperparameter-tuning-experiment"></a>Configurar experimento de ajuste de hiperparâmetro

Para [Configurar o experimento de ajuste de hiperparâmetro](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig) , forneça o seguinte:
* O espaço de pesquisa de hiperparâmetro definido
* Sua política de encerramento antecipado
* A métrica primária
* Configurações de alocação de recursos
* ScriptRunConfig `script_run_config`

O ScriptRunConfig é o script de treinamento que será executado com os hiperparâmetros amostrados. Ele define os recursos por trabalho (único ou vários nós) e o destino de computação a ser usado.

> [!NOTE]
>O destino de computação usado no `script_run_config` deve ter recursos suficientes para satisfazer seu nível de simultaneidade. Para obter mais informações sobre o ScriptRunConfig, consulte [configurar execuções de treinamento](how-to-set-up-training-targets.md).

Configurar seu experimento de ajuste de hiperparâmetro:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
from azureml.train.hyperdrive import RandomParameterSampling, BanditPolicy, uniform, PrimaryMetricGoal

param_sampling = RandomParameterSampling( {
        'learning_rate': uniform(0.0005, 0.005),
        'momentum': uniform(0.9, 0.99)
    }
)

early_termination_policy = BanditPolicy(slack_factor=0.15, evaluation_interval=1, delay_evaluation=10)

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

O `HyperDriveConfig` define os parâmetros passados para o `ScriptRunConfig script_run_config` . O `script_run_config` , por sua vez, passa parâmetros para o script de treinamento. O trecho de código acima é extraído do exemplo de treinamento de bloco de anotações [, ajuste de hiperparâmetro e implantação com PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch). Neste exemplo, os `learning_rate` parâmetros e `momentum` serão ajustados. A interrupção antecipada de execuções será determinada por um `BanditPolicy` , o que interrompe uma execução cuja métrica primária fica fora do `slack_factor` (consulte a [referência da classe BanditPolicy](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy)). 

O código a seguir do exemplo mostra como os valores que estão sendo ajustados são recebidos, analisados e passados para a função do script de treinamento `fine_tune_model` :

```python
# from pytorch_train.py
def main():
    print("Torch version:", torch.__version__)

    # get command-line arguments
    parser = argparse.ArgumentParser()
    parser.add_argument('--num_epochs', type=int, default=25,
                        help='number of epochs to train')
    parser.add_argument('--output_dir', type=str, help='output directory')
    parser.add_argument('--learning_rate', type=float,
                        default=0.001, help='learning rate')
    parser.add_argument('--momentum', type=float, default=0.9, help='momentum')
    args = parser.parse_args()

    data_dir = download_data()
    print("data directory is: " + data_dir)
    model = fine_tune_model(args.num_epochs, data_dir,
                            args.learning_rate, args.momentum)
    os.makedirs(args.output_dir, exist_ok=True)
    torch.save(model, os.path.join(args.output_dir, 'model.pt'))
```

> [!Important]
> Cada hiperparâmetro é executado reinicia o treinamento do zero, incluindo a recriação do modelo e _de todos os carregadores de dados_. Você pode minimizar esse custo usando um pipeline Azure Machine Learning ou um processo manual para fazer o máximo de preparação de dados possível antes de suas execuções de treinamento. 

## <a name="submit-hyperparameter-tuning-experiment"></a>Enviar experimento de ajuste de hiperparâmetro

Depois de definir a configuração de ajuste de hiperparâmetro, [envie o experimento](/python/api/azureml-core/azureml.core.experiment%28class%29#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-hyperparameter-tuning-optional"></a>Ajuste de hiperparâmetro de início quente (opcional)

Encontrar os melhores valores de hiperparâmetro para seu modelo pode ser um processo iterativo. Você pode reutilizar o conhecimento das cinco execuções anteriores para acelerar o ajuste de hiperparâmetro.

A inicialização a quente é tratada de forma diferente, dependendo do método de amostragem:
- **Amostragem de Bayesiana**: as avaliações da execução anterior são usadas como conhecimento prévio para escolher novos exemplos e para melhorar a métrica primária.
- Amostragem **aleatória** ou **amostra de grade**: o encerramento antecipado usa o conhecimento de execuções anteriores para determinar execuções com mau desempenho. 

Especifique a lista de execuções pai das quais você deseja iniciar a inicialização.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Se um experimento de ajuste de hiperparâmetro for cancelado, você poderá retomar as execuções de treinamento do último ponto de verificação. No entanto, seu script de treinamento deve manipular a lógica do ponto de verificação.

A execução de treinamento deve usar a mesma configuração de hiperparâmetro e montar as pastas de saídas. O script de treinamento deve aceitar o `resume-from` argumento, que contém os arquivos de ponto de verificação ou modelo dos quais retomar a execução de treinamento. Você pode retomar as execuções de treinamento individuais usando o seguinte trecho:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Você pode configurar o teste de ajuste de hiperparâmetro para iniciar a quente de um experimento anterior ou retomar execuções de treinamento individuais usando os parâmetros opcionais `resume_from` e `resume_child_runs` na configuração:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-hyperparameter-tuning-runs"></a>Visualizar execuções de ajuste de hiperparâmetro

Você pode visualizar as execuções de ajuste de hiperparâmetro no Azure Machine Learning Studio ou pode usar um widget de bloco de anotações.

### <a name="studio"></a>Estúdio

Você pode visualizar todas as execuções de ajuste de hiperparâmetro no [Azure Machine Learning Studio](https://ml.azure.com). Para obter mais informações sobre como exibir um experimento no portal, consulte [exibir registros de execução no estúdio](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal).

- **Gráfico de métricas**: essa visualização acompanha as métricas registradas para cada filho hyperdrive executado durante o ajuste de hiperparâmetro. Cada linha representa uma execução filho, e cada ponto mede o valor principal da métrica nessa iteração de tempo de execução.  

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-metrics.png" alt-text="Gráfico de métricas de ajuste de hiperparâmetro":::

- **Gráfico de coordenadas paralelas**: essa visualização mostra a correlação entre o desempenho da métrica primária e os valores de hiperparâmetro individuais. O gráfico é interativo por meio da movimentação de eixos (clique e arraste pelo rótulo do eixo) e, ao realçar valores em um único eixo (clique e arraste verticalmente ao longo de um único eixo para realçar um intervalo de valores desejados).

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates.png" alt-text="Gráfico de coordenadas paralelas de ajuste de hiperparâmetro":::

- **gráfico de dispersão bidimensional**: essa visualização mostra a correlação entre quaisquer dois hiperparâmetros individuais, juntamente com seu valor de métrica primária associado.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-2-dimensional-scatter.png" alt-text="Gráfico de dispersão bidimensional de ajuste Hyparameter":::

- **gráfico de dispersão tridimensional**: essa visualização é igual a 2D, mas permite três dimensões de hiperparâmetro de correlação com o valor principal de métrica. Você também pode clicar e arrastar para reorientar o gráfico para exibir diferentes correlações no espaço 3D.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-3-dimensional-scatter.png" alt-text="Gráfico de dispersão tridimensional de Hyparameter ajuste":::

### <a name="notebook-widget"></a>Widget de notebook

Use o [widget Notebook](/python/api/azureml-widgets/azureml.widgets.rundetails) para visualizar o progresso de suas execuções de treinamento. O snippet a seguir visualiza todas as execuções em um único lugar em um bloco de anotações do Jupyter de ajuste de seu parâmetro:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Esse código exibe uma tabela com detalhes sobre as execuções de treinamento para cada uma das configurações de hiperparâmetro.

:::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-table.png" alt-text="Tabela de ajuste de hiperparâmetro":::

Você também pode visualizar o desempenho de cada uma das execuções à medida que o treinamento avança.

## <a name="find-the-best-model"></a>Localizar o melhor modelo

Depois que todas as execuções de ajuste de hiperparâmetro forem concluídas, identifique os valores de configuração e hiperparâmetro de melhor desempenho:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Exemplo de bloco de anotações

Consulte os blocos de anotações Train-hiperparameter-* nesta pasta:
* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas
* [Rastrear um experimento](how-to-track-experiments.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
