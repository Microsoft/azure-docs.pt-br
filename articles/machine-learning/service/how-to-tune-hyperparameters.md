---
title: Ajustar os hiperparâmetros para o seu modelo
titleSuffix: Azure Machine Learning service
description: Ajuste os hiperparâmetros para seu modelo de aprendizado profundo / aprendizado de máquina usando o serviço do Azure Machine Learning. Você aprenderá a definir o espaço de pesquisa de parâmetro, especifique uma métrica principal para otimizar e encerrar no início de execuções de mau desempenho.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/08/2019
ms.custom: seodec18
ms.openlocfilehash: 730f39bf0b05ef33bbbca150532f96f1e495a9ed
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302358"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning-service"></a>Ajustar os hiperparâmetros para o seu modelo com o Serviço do Azure Machine Learning

Ajuste os hiperparâmetros para seu modelo usando o serviço Azure Machine Learning de forma eficiente.  O ajuste de Hiperparâmetro inclui as seguintes etapas:

* Defina o espaço de pesquisa de parâmetro
* Especificar uma métrica primária para otimizar  
* Especificar critérios de término antecipado para execuções de mau desempenho
* Aloque recursos para ajuste de hiperparâmetro
* Inicializar um teste com a configuração acima
* Visualizar as execuções de treinamento
* Selecione o melhor desempenho de configuração para o seu modelo

## <a name="what-are-hyperparameters"></a>O que são hiperparâmetros?

Hiperparâmetros são parâmetros ajustáveis que você escolhe para treinar um modelo que controlam o processo de treinamento. Por exemplo, para treinar uma rede neural profunda, você decide o número de camadas ocultas na rede e o número de nós em cada camada antes de treinar o modelo. Esses valores geralmente permanecem constantes durante o processo de treinamento.

Em cenários de aprendizado profundo/aprendizado de máquina, o desempenho do modelo depende muito os valores de hiperparâmetro selecionados. A meta da exploração de hiperparâmetro é pesquisar em várias configurações de hiperparâmetro para encontrar uma configuração que resulte no desempenho esperado. Normalmente, o processo de exploração de hiperparâmetro é arduamente manual, considerando que o espaço de pesquisa é vasto e a avaliação de cada configuração pode ser cara.

O Azure Machine Learning permite automatizar essa exploração de hiperparâmetro de maneira eficiente, economizando tempo e recursos significativos. Você especifica o intervalo de valores de hiperparâmetro e um número máximo de treinamento é executado. O sistema automaticamente inicia várias execuções simultâneas com configurações de parâmetros diferentes e localiza a configuração que resulta em melhor desempenho, medido pela métrica que você escolher. Execuções de treinamento com mau desempenho são automaticamente encerradas de modo antecipado, reduzindo o desperdício de recursos de computação. Em vez disso, esses recursos são usados para explorar outras configurações de hiperparâmetro.


## <a name="define-search-space"></a>Definir espaço de pesquisa

Ajuste os hiperparâmetros automaticamente ao explorar o intervalo de valores definidos para cada hiperparâmetro.

### <a name="types-of-hyperparameters"></a>Tipos de hiperparâmetros

Cada hiperparâmetro pode ser discreto ou contínuo.

#### <a name="discrete-hyperparameters"></a>Hiperparâmetros discretos 

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

Nesse caso, `batch_size` assume um dos valores [16, 32, 64, 128] e `number_of_hidden_layers` assume um dos valores de [1, 2, 3, 4].

Hiperparâmetros discretos avançados também podem ser especificados usando uma distribuição. Há suporte para as seguintes distribuições:

* `quniform(low, high, q)` – Retorna um valor como round(uniform(low, high) / q) * q
* `qloguniform(low, high, q)` – Retorna um valor como round(exp(uniform(low, high)) / q) * q
* `qnormal(mu, sigma, q)` – Retorna um valor como round(normal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)` – Retorna um valor como round(exp(normal(mu, sigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>Hiperparâmetros contínuos 

Hiperparâmetros contínuos podem ser especificados como uma distribuição em um intervalo de valores contínuos. Distribuições com suporte incluem:

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

Você também pode especificar o método de amostragem de parâmetro para usar sobre a definição de espaço do hiperparâmetro. O serviço do Azure Machine Learning dá suporte para amostragem Aleatória, amostragem de Grade e amostragem Bayesiana.

#### <a name="random-sampling"></a>Amostragem aleatória

Na amostragem Aleatória, os valores de hiperparâmetro serão selecionadas aleatoriamente do espaço de pesquisa definido. A [amostragem aleatória](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) permite que o espaço de pesquisa inclua hiperparâmetros discretos e contínuos.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Amostragem de grade

A [amostragem de grade](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) executa uma pesquisa de grade simples em todos os valores viáveis no espaço de pesquisa definido. Pode ser usada apenas com hiperparâmetros especificados usando `choice`. Por exemplo, o espaço a seguir tem um total de seis exemplos:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Amostragem Bayesiana

A [amostragem de Bayesiana](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) baseia-se no algoritmo de otimização Bayesiana e faz escolhas inteligentes nos valores de hiperparâmetro para a amostra seguinte. Escolhe essa amostra com base no desempenho de amostras anteriores de modo que a nova amostra aprimore a métrica primária relatada.

Ao usar a amostragem Bayesiana, o número de execuções simultâneas tem um impacto sobre a eficácia do processo de ajuste. Normalmente, um número menor de execuções simultâneas pode levar a convergência de amostragem melhor, desde que o menor grau de paralelismo aumente o número de execuções que se beneficiam de execuções concluídas anteriormente.

Amostragem Bayesiana dá apenas suporte a distribuições `choice` e `uniform` no espaço de pesquisa. 

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> A amostragem Bayesiana não oferece suporte a qualquer política de rescisão antecipada (consulte [especificar uma política de rescisão antecipada](#specify-early-termination-policy)). Ao usar a amostragem de parâmetro Bayesiana, defina `early_termination_policy = None`, ou deixar de fora o `early_termination_policy` parâmetro.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Especificar métrica principal

Especifique a [métrica primária](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) que você deseja que o teste de ajuste de hiperparâmetro Otimize. Cada execução de treinamento é avaliada para a métrica principal. Desempenho ruim (em que a métrica primária não atende aos critérios definidos pela política de rescisão antecipada) serão encerradas. Além do nome da métrica primária, você também especificará a meta da otimização – maximizar ou minimizar a métrica primária.

* `primary_metric_name`: O nome da métrica primária a otimizar. O nome da métrica primária deve corresponder exatamente ao nome da métrica registrada pelo script de treinamento. Veja [Registrar em log métricas de ajuste de hiperparâmetro](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Pode ser tanto `PrimaryMetricGoal.MAXIMIZE` quanto `PrimaryMetricGoal.MINIMIZE` e determina se a métrica primária será maximizada ou minimizada ao avaliar as execuções. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Otimize as execuções para maximizar a "precisão".  Certifique-se de fazer esse valor em seu script de treinamento.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Registrar em log métricas de ajuste de hiperparâmetro

O script de treinamento para o seu modelo deve fazer as métricas relevantes durante o treinamento de modelo. Quando você configurar o ajuste de hiperparâmetro, você pode especificar a métrica principal a ser usada para avaliar o desempenho de execução. (Veja [Especificar uma métrica primária para otimizar](#specify-primary-metric-to-optimize)).  No seu script de treinamento, você deve fazer essa métrica para que esteja disponível para o processo de ajuste de hiperparâmetro.

Essa métrica de log em seu script de treinamento com o seguinte trecho de exemplo:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

O script de treinamento calcula o `val_accuracy` e registra em log como "precisão", que é usado como a métrica principal. Cada vez que a métrica é registrada e recebida, o serviço de ajuste de hiperparâmetro. Cabe ao desenvolvedor modelo determinar a frequência para relatar essa métrica.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Especificar política de rescisão antecipada

Encerrar execuções com mau desempenho automaticamente com uma [política de encerramento antecipado. Encerramento reduz o desperdício de recursos e usa esses recursos para explorar outras configurações de parâmetro.

Ao usar uma política de rescisão antecipada, você pode configurar os seguintes parâmetros que controlam quando uma política é aplicada:

* `evaluation_interval`: a frequência para aplicar a política. Cada vez que o script de treinamento registra em log a métrica primária conta como um intervalo. Assim, um `evaluation_interval` de 1 aplicará a política sempre outro que o script de treinamento relatar a métrica primária. Um `evaluation_interval` de 2 aplicará a política sempre outro que o script de treinamento relatar a métrica primária. Se não for especificado, `evaluation_interval` é definido como 1 por padrão.
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos. Isso é um parâmetro opcional que permite que todas as configurações sejam executadas para um número mínimo inicial de intervalos, evitando o encerramento prematuro de execuções de treinamento. Se for especificado, a política se aplicará a cada múltiplo de evaluation_interval maior ou igual a delay_evaluation.

O Serviço Azure Machine Learning tem suporte para as seguintes políticas de término antecipado.

### <a name="bandit-policy"></a>Política Bandit

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) é uma política de encerramento com base no fator de margem de atraso/valor da margem de atraso e no intervalo de avaliação. A política encerra todas as execuções em que a métrica primária não está dentro do fator de margem de atraso/quantidade de margem de atraso especificado em relação à execução de treinamento de melhor desempenho. Usa os seguintes parâmetros de configuração:

* `slack_factor` ou `slack_amount`: a margem de atraso permitida em relação à execução de treinamento de melhor desempenho. `slack_factor` especifica a margem de atraso permitida como uma proporção. `slack_amount` especifica a margem de atraso permitida como um valor absoluto, em vez de uma proporção.

    Por exemplo, considere uma política Bandit sendo aplicada no intervalo de 10. Suponha que a execução de melhor desempenho no intervalo 10 tenha relatado uma métrica primária de 0,8 com uma meta para maximizar a métrica primária. Se a política tiver sido especificada com um `slack_factor` de 0,2, qualquer execução de treinamento cuja melhor métrica no intervalo de 10 seja menor que 0,66 (0,8/(1+`slack_factor`)) será encerrada. Se, em vez disso, a política tiver sido especificada com um `slack_amount` de 0,2, qualquer execução de treinamento cuja melhor métrica no intervalo de 10 seja menor que 0,6 (0,8 – `slack_amount`) será encerrada.
* `evaluation_interval`: a frequência para aplicar a política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, a política de encerramento inicial é aplicada a cada intervalo quando as métricas são relatadas, começando no intervalo de avaliação 5. Qualquer execução cuja melhor métrica seja menor que (1/(1+0,1) ou 91% da execução de melhor desempenho será encerrada.

### <a name="median-stopping-policy"></a>Política de Encerramento Mediana

A [parada mediana](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) é uma política de término inicial com base nas médias em execução das métricas primárias relatadas pelas execuções. Esta política computa as médias corridas em todas as execuções de treinamento e encerra execuções cujo desempenho seja pior do que a mediana das médias corridas. Esta política usa os seguintes parâmetros de configuração:
* `evaluation_interval`: a frequência para aplicar a política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, a política de encerramento inicial é aplicada a cada intervalo começando em um intervalo de avaliação 5. Uma execução será encerrada no intervalo de 5 se a sua melhor métrica primária for pior do que a mediana de médias corridas em intervalos de 1:5 em todas as execuções de treinamento.

### <a name="truncation-selection-policy"></a>Política de seleção de truncamento

[Seleção](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) de truncamento cancela um determinado percentual de execuções de execução mais baixa em cada intervalo de avaliação. As execuções são comparadas com base em seu desempenho na métrica primária e os X% inferiores são encerrados. Usa os seguintes parâmetros de configuração:

* `truncation_percentage`: o percentual de execuções de menor desempenho a encerrar a cada intervalo de avaliação. Especifique um valor inteiro entre 1 e 99.
* `evaluation_interval`: a frequência para aplicar a política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Neste exemplo, a política de encerramento inicial é aplicada a cada intervalo começando em um intervalo de avaliação 5. Uma execução será encerrada no intervalo de 5 se seu desempenho no intervalo de 5 estiver nos 20% mais baixos de desempenho de todas as execuções em um intervalo de 5.

### <a name="no-termination-policy"></a>Nenhuma política de encerramento

Se você quiser que todas as execuções de treinamento sejam executadas até a conclusão, defina a política como Nenhum. Isso terá o efeito de não aplicar nenhuma política de encerramento antecipado.

```Python
policy=None
```

### <a name="default-policy"></a>Política padrão

Se nenhuma política for especificada, o serviço de ajuste de hiperparâmetro permitirá que todas as execuções de treinamento executadas até a conclusão.

>[!NOTE] 
>Se você estiver procurando por uma política conservador que proporciona economia sem encerrar trabalhos promissoras, você pode usar uma política de parando mediana com `evaluation_interval` 1 e `delay_evaluation` 5. Essas são configurações conservadoras, que podem fornecer aproximadamente 25 a 35% de economia sem perda na métrica primária (com base em nossos dados de avaliação).

## <a name="allocate-resources"></a>Alocar recursos

Controle seu orçamento de recursos para seu experimento de ajuste de hiperparâmetro, especificando o número total máximo de execuções de treinamento.  Opcionalmente, especifique a duração máxima de seu experimento de ajuste de hiperparâmetro.

* `max_total_runs`: Número total máximo de execuções de treinamento que serão criadas. Limite superior - pode haver menos execuções, por exemplo, se o espaço do hiperparâmetro é finito e tiver menos amostras. Deve ser um número entre 1 e 1000.
* `max_duration_minutes`: Duração máxima em minutos do experimento de ajuste de hiperparâmetro. O parâmetro é opcional e, se estiver presente, todas as execuções que seriam executadas após esta duração são canceladas automaticamente.

>[!NOTE] 
>Se tanto `max_total_runs` quanto `max_duration_minutes` forem especificados, o experimento de ajuste de hiperparâmetro será encerrado quando o primeiro dos dois desses limites for atingido.

Além disso, especifique o número máximo de execuções de treinamento a serem executadas simultaneamente durante sua pesquisa de ajuste de hiperparâmetro.

* `max_concurrent_runs`: É o número máximo de execuções a serem executadas simultaneamente em um determinado momento. Se nenhum for especificado, todas as `max_total_runs` serão iniciadas em paralelo. Se especificado, deverá ser um número entre 1 e 100.

>[!NOTE] 
>O número de execuções simultâneas é ligado aos recursos disponíveis no destino de computação especificado. Portanto, você precisará garantir que o destino de computação tenha os recursos disponíveis para a simultaneidade desejada.

Aloque recursos para ajuste de hiperparâmetro:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Esse código configura o experimento de ajuste de hiperparâmetro para usar um máximo de 20 execuções no total, executando 4 configurações por vez.

## <a name="configure-experiment"></a>Configurar o experimento

[Configure seu experimento de ajuste](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) de hiperparâmetro usando o espaço de pesquisa de hiperparâmetro definido, a política de término antecipado, a métrica primária e a alocação de recursos das seções acima. Além disso, você precisará fornecer um `estimator` que será chamado com os hiperparâmetros de amostra. O `estimator` descreve o script de treinamento que você executa, os por trabalho (GPU única ou múltipla) e o destino de computação a ser usado. Uma vez que a simultaneidade para seu experimento de ajuste de hiperparâmetro é ligado a recursos disponíveis, certifique-se de que o destino de computação especificado no `estimator` tem recursos suficientes para a simultaneidade desejada. (Para obter mais informações sobre os avaliadores, consulte [como treinar modelos](how-to-train-ml-models.md).)

Configurar seu experimento de ajuste de hiperparâmetro:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>Enviar experimento

Depois de definir a configuração de ajuste de hiperparâmetro, [envie um experimento](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name` é o nome atribuído ao seu experimento, de ajuste de hiperparâmetro e `workspace` é o espaço de trabalho no qual você deseja criar o experimento (para obter mais informações sobre experimentos, consulte [Como funciona o serviço do Azure Machine Learning?](concept-azure-machine-learning-architecture.md))

## <a name="visualize-experiment"></a>Visualizar o experimento

O SDK do Azure Machine Learning fornece um [Widget de bloco de anotações](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) que visualiza o progresso de suas execuções de treinamento. O trecho a seguir visualiza todas as execuções em um único lugar em um bloco de anotações do Jupyter de ajuste de seu parâmetro:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Esse código exibe uma tabela com detalhes sobre as execuções de treinamento para cada uma das configurações de hiperparâmetro.

![tabela de ajuste de hiperparâmetro](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Você também pode visualizar o desempenho de cada uma das execuções à medida que o treinamento avança. 

![gráfico de ajuste de hiperparâmetro](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Por fim, você pode identificar visualmente a correlação entre desempenho e valores de hiperparâmetros individuais usando um Gráfico de Coordenadas Paralelas. 

![coordenadas paralelas de ajuste de hiperparâmetro](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)

Você pode visualizar todas as suas execuções de ajuste de hiperparâmetro no seu portal da Web do Azure também. Para mais informações sobre como visualizar uma experiência no portal da Web, consulte [como acompanhar experiências](how-to-track-experiments.md#view-the-experiment-in-the-web-portal).

![portal de ajuste de hiperparâmetro](media/how-to-tune-hyperparameters/HyperparameterTuningPortal.png)

## <a name="find-the-best-model"></a>Localizar o melhor modelo

Depois que todas as execuções de ajuste de hiperparâmetro forem concluídas, [identifique a configuração de melhor desempenho](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true-) e os valores de hiperparâmetro correspondentes:

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
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas
* [Rastrear um experimento](how-to-track-experiments.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
