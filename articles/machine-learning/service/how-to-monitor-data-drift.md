---
title: Detectar descompasso de dados (versão prévia) em implantações de AKS
titleSuffix: Azure Machine Learning service
description: Detectar descompasso de dados nos modelos implantados do serviço kubernetes do Azure no serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 07/08/2019
ms.openlocfilehash: 9852ec450b6da3814a3bd2bfc6aae7d19acaf584
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370393"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Detectar descompasso de dados (versão prévia) em modelos implantados no serviço kubernetes do Azure (AKS)

Neste artigo, você aprenderá a monitorar a descompasso de dados entre o conjunto de dados de treinamento e de inferência de um modelo implantado. No contexto do aprendizado de máquina, os modelos de aprendizado de máquina treinados podem enfrentar desempenho de previsão degradado devido à descompasso. Com o serviço de Azure Machine Learning, você pode monitorar a descompasso de dados e o serviço pode enviar um alerta por email para você quando for detectado descompasso.

## <a name="what-is-data-drift"></a>O que é descompasso de dados?

A descompasso de dados ocorre quando os dados servidos para um modelo na produção são diferentes dos dados usados para treinar o modelo. É um dos principais motivos em que a precisão do modelo degrada ao longo do tempo, o que monitora a descompasso de dados ajuda a detectar problemas de desempenho do modelo. 

## <a name="what-can-i-monitor"></a>O que posso monitorar?

Com o serviço de Azure Machine Learning, você pode monitorar as entradas em um modelo implantado em AKS e comparar esses dados com o DataSet de treinamento para o modelo. Em intervalos regulares, os dados de inferência são [instantâneos e](how-to-explore-prepare-data.md)analisados, em seguida, computados em relação ao conjunto de dados de linha de base para produzir uma análise de descompasso que: 

+ Mede a magnitude da descompasso de dados, chamada de coeficiente de descompasso.
+ Mede a contribuição de descompasso de dados por recurso, informando quais recursos causaram a descompasso de dados.
+ Mede métricas de distância. Atualmente, a Wasserstein e a distância de energia são computadas.
+ Mede as distribuições de recursos. Estimativa e histogramas de densidade do kernel no momento.
+ Enviar alertas para descompasso de dados por email.

> [!Note]
> Esse serviço está em (versão prévia) e limitado nas opções de configuração. Consulte nossa [documentação de API](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) e [notas de versão](azure-machine-learning-release-notes.md) para obter detalhes e atualizações. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Como a descompasso de dados é monitorada no serviço Azure Machine Learning

Usando o serviço Azure Machine Learning, a descompasso de dados é monitorada por meio de conjuntos ou de implantações. Para monitorar a descompasso de dados, é um DataSet de linha de base – geralmente o conjunto de dado de treinamento para um modelo-é especificado. Um segundo conjunto de dados-geralmente modelar os dados de entrada coletados de uma implantação-é testado no conjunto de dado de linha de base. Os dois conjuntos de dados são de [perfil](how-to-explore-prepare-data.md#explore-with-summary-statistics) e são inseridos para o serviço de monitoramento de descompasso. Um modelo de aprendizado de máquina é treinado para detectar diferenças entre os dois conjuntos de valores. O desempenho do modelo é convertido para o coeficiente de descompasso, que mede a magnitude do descompasso entre os dois conjuntos de valores. Usando a [interpretação de modelo](machine-learning-interpretability-explainability.md), os recursos que contribuem para o coeficiente de descompasso são calculados. No perfil do conjunto de dados, as informações estatísticas sobre cada recurso são rastreadas. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

- Um espaço de trabalho do serviço do Machine Learning do Azure e o SDK do Azure Machine Learning para Python instalados. Use as instruções descritas em [Criar um workspace do Serviço do Azure Machine Learning](setup-create-workspace.md#sdk) para fazer o seguinte:

    - Criar um ambiente Miniconda
    - Instalar o SDK do Azure Machine Learning para Python
    - Criar um espaço de trabalho
    - Gravar um arquivo de configuração de espaço de trabalho (aml_config/config. JSON).

- Instale o SDK de descompasso de dados usando o seguinte comando:

    ```shell
    pip install azureml-contrib-datadrift
    ```

- Crie um [conjunto](how-to-create-register-datasets.md) de dados a partir de seus dados de treinamento do modelo.

- Especifique o conjunto de registros de treinamento ao [registrar](concept-model-management-and-deployment.md) o modelo. O exemplo a seguir demonstra o `datasets` uso do parâmetro para especificar o conjunto de os de treinamento:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Habilite a coleta de dados de modelo](how-to-enable-data-collection.md) para coletar dados da implantação AKs do modelo e confirmar que os dados estão `modeldata` sendo coletados no contêiner de BLOB.

## <a name="configure-data-drift"></a>Configurar descompasso de dados
Para configurar a descompasso de dados para seu experimento, importe as dependências conforme mostrado no exemplo de Python a seguir. 

Este exemplo demonstra como configurar [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) o objeto:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Enviar uma execução do DataDriftDetector

Com o `DataDriftDetector` objeto configurado, você pode enviar uma [execução](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) de descompasso de dados em uma determinada data para o modelo. Como parte da execução, habilite alertas do DataDriftDetector definindo o `drift_threshold` parâmetro. Se o [datadrift_coefficient](#metrics) estiver acima do fornecido `drift_threshold`, um email será enviado.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Visualizar métricas de descompasso

<a name="metrics"></a>

Depois de enviar sua execução do DataDriftDetector, você poderá ver as métricas de descompasso salvas em cada iteração de execução para uma tarefa de descompasso de dados:


|Métrica|Descrição|
--|--|
wasserstein_distance|Distância estatística definida para distribuição numérica unidimensional.|
energy_distance|Distância estatística definida para distribuição numérica unidimensional.|
datadrift_coefficient|Calculado da mesma forma que o coeficiente de correlação de Matthew, mas essa saída é um número real que varia de 0 a 1. No contexto de descompasso, 0 indica que não há descompasso e 1 indica o descompasso máximo.|
datadrift_contribution|Importância dos recursos que contribuem para descompasso.|

Há várias maneiras de exibir as métricas de descompasso:

* Use o `RunDetails` [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Use a [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) função em qualquer `datadrift` objeto Run.
* Exiba as métricas no portal do Azure em seu modelo.

O exemplo de Python a seguir demonstra como plotar métricas de descompasso de dados relevantes. Você pode usar as métricas retornadas para criar visualizações personalizadas:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Consulte descompasso de dados detectado por Azure Machine Learning](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Agendar verificações de descompasso de dados 

Quando você habilita a detecção de descompasso de dados, um DataDriftDetector é executado na frequência especificada, agendada. Se o datadrift_coefficient atingir o dado `drift_threshold`, um email será enviado com cada execução agendada. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

A configuração do detector de descompasso de dados pode ser vista na página de detalhes do modelo na portal do Azure.

![Configuração de descompasso de dados portal do Azure](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>Exibir resultados na interface do usuário do Workspace do ML do Azure

Para exibir os resultados na interface do usuário do Workspace do ML do Azure, navegue até a página modelo. Na guia detalhes do modelo, a configuração de descompasso de dados é mostrada. Uma guia ' descompasso de dados (visualização) ' agora está disponível visualizando as métricas de descompasso de dados. 

![Descompasso portal do Azure dados](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="receiving-drift-alerts"></a>Recebendo alertas de descompasso

Ao definir o limite de alerta de coeficiente de descompasso e fornecer um endereço de email, um alerta de email [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) é enviado automaticamente sempre que o coeficiente de descompasso está acima do limite. 

Para configurar ações e alertas personalizados, todas as métricas de descompasso de dados são armazenadas no recurso de [Application insights](how-to-enable-app-insights.md) que foi criado junto com o espaço de trabalho de serviço do Azure Machine Learning. Você pode seguir o link no alerta de email para a consulta de Application Insights.

![Alerta de email de descompasso de dados](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Treinar novamente o modelo após a descompasso

Quando a descompasso de dados afeta negativamente o desempenho do modelo implantado, é hora de treinar novamente o modelo. O método [ a seguir `diff()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#diff-rhs-dataset--compute-target-none--columns-none-
) fornece uma noção inicial do que mudou entre os conjuntos de dados de treinamento antigo e novo. 

```python
from azureml.core import Dataset

old_training_dataset.diff(new_training_dataset)
```

Com base na saída do código anterior, talvez você queira treinar novamente seu modelo. Para fazer isso, continue com as etapas a seguir.

* Investigue os dados coletados e prepare os dados para treinar o novo modelo.
* Divida-o em dados de treinamento/teste.
* Treine o modelo novamente usando os novos dados.
* Avalie o desempenho do modelo recém-gerado.
* Implante o novo modelo se o desempenho for melhor do que o modelo de produção.

## <a name="next-steps"></a>Próximas etapas

* Para obter um exemplo completo de como usar a descompasso de dados, consulte o notebook de descompasso de [dados do Azure ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb). Este Jupyter Notebook demonstra como usar um conjunto de dados [aberto do Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) para treinar um modelo para prever o clima, implantá-lo no AKs e monitorar a descompasso de dados. 

* Agradecemos suas dúvidas, seus comentários ou suas sugestões, pois a descompasso de dados se move para a disponibilidade geral. Use o botão comentários do produto abaixo! 
