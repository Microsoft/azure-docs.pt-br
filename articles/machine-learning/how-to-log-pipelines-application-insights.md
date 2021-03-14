---
title: Monitorar & coletar arquivos de log de pipeline
titleSuffix: Azure Machine Learning
description: Adicione o log aos pipelines de Pontuação de treinamento e de lote e exiba os resultados registrados em Application Insights.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 7ed27eba66b3d18bed8017934fce85928b961392
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520040"
---
# <a name="collect-machine-learning-pipeline-log-files-in-application-insights-for-alerts-and-debugging"></a>Coletar arquivos de log do pipeline do Machine Learning no Application Insights para alertas e depuração


A Biblioteca Python [OpenCensus](https://opencensus.io/quickstart/python/) pode ser usada para rotear logs para Application insights de seus scripts. A agregação de logs de execuções de pipeline em um único local permite que você crie consultas e diagnostique problemas. O uso de Application Insights permitirá que você acompanhe os logs ao longo do tempo e compare os logs de pipeline entre as execuções.

Ter seus logs em vigor fornecerá um histórico de exceções e mensagens de erro. Como o Application Insights se integra aos alertas do Azure, você também pode criar alertas com base em consultas Application Insights.

## <a name="prerequisites"></a>Pré-requisitos

* Siga as etapas para criar um espaço de trabalho [Azure Machine Learning](./how-to-manage-workspace.md) e [criar seu primeiro pipeline](./how-to-create-machine-learning-pipelines.md)
* [Configure seu ambiente de desenvolvimento](./how-to-configure-environment.md) para instalar o SDK do Azure Machine Learning.
* Instale o pacote de [exportador do OpenCensus Azure monitor](https://pypi.org/project/opencensus-ext-azure/) localmente:
  ```python
  pip install opencensus-ext-azure
  ```
* Criar uma [instância de Application insights](../azure-monitor/app/opencensus-python.md) (este documento também contém informações sobre como obter a cadeia de conexão para o recurso)

## <a name="getting-started"></a>Introdução

Esta seção é uma introdução específica para usar o OpenCensus de um pipeline Azure Machine Learning. Para obter um tutorial detalhado, consulte [OpenCensus Azure monitor exportadores](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

Adicione um PythonScriptStep ao seu pipeline do Azure ML. Configure seu [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) com a dependência em opencensus-ext-Azure. Configure a `APPLICATIONINSIGHTS_CONNECTION_STRING` variável de ambiente.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

Crie um arquivo chamado `sample_step.py`. Importe a classe AzureLogHandler para rotear logs para Application Insights. Você também precisará importar a biblioteca de registro em log do Python.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Em seguida, adicione o AzureLogHandler ao agente de log do Python.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Registro em log com dimensões personalizadas
 
Por padrão, os logs encaminhados para Application Insights não terão contexto suficiente para rastrear de volta para a execução ou experimento. Para tornar os logs acionáveis para diagnosticar problemas, são necessários campos adicionais. 

Para adicionar esses campos, é possível adicionar dimensões personalizadas para fornecer contexto a uma mensagem de log. Um exemplo é quando alguém deseja exibir logs em várias etapas na mesma execução de pipeline.

As dimensões personalizadas compõem um dicionário de pares chave-valor (armazenados como cadeia de caracteres, Cadeia de caracteres). Em seguida, o dicionário é enviado para Application Insights e exibido como uma coluna nos resultados da consulta. Suas dimensões individuais podem ser usadas como [parâmetros de consulta](#additional-helpful-queries).

### <a name="helpful-context-to-include"></a>Contexto útil para incluir

| Campo                          | Raciocínio/exemplo                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Pode consultar logs para aqueles com o mesmo parent_run_id para ver os logs ao longo do tempo para todas as etapas, em vez de ter que se aprofundar em cada etapa individual                                        |
| step_id                        | Pode consultar logs para aqueles com o mesmo step_id para ver onde ocorreu um problema com um escopo estreito apenas para a etapa individual                                                        |
| step_name                      | Pode consultar logs para ver o desempenho da etapa ao longo do tempo. Também ajuda a encontrar uma step_id para execuções recentes Sem mergulhar na interface do usuário do portal                                          |
| experiment_name                | Pode consultar entre logs para ver o desempenho do teste ao longo do tempo. Também ajuda a encontrar uma parent_run_id ou step_id para execuções recentes Sem mergulhar na interface do usuário do portal                   |
| run_url                 | Pode fornecer um link diretamente de volta para a execução para investigação. |

**Outros campos úteis**

Esses campos podem exigir Instrumentação de código adicional e não são fornecidos pelo contexto de execução.

| Campo                   | Raciocínio/exemplo                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Se estiver usando CI/CD para implantar, esse campo poderá correlacionar os logs à versão do código que forneceu a lógica de etapa e pipeline. Esse link pode ajudar a diagnosticar problemas ou identificar modelos com características específicas (valores de log/métrica) |
| run_type                       | Pode diferenciar entre diferentes tipos de modelo ou treinamento versus execuções de Pontuação |

### <a name="creating-a-custom-dimensions-dictionary"></a>Criando um dicionário de dimensões personalizadas

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", extra= {"custom_dimensions":custom_dimensions})
```

## <a name="opencensus-python-logging-considerations"></a>Considerações de log do Python OpenCensus

O OpenCensus AzureLogHandler é usado para rotear os logs do Python para Application Insights. Como resultado, as nuances de log do Python devem ser consideradas. Quando um agente é criado, ele tem um nível de log padrão e mostrará os logs maiores ou iguais a esse nível. Uma boa referência para usar os recursos de log do Python é o [manual de registro em log](https://docs.python.org/3/howto/logging-cookbook.html).

A `APPLICATIONINSIGHTS_CONNECTION_STRING` variável de ambiente é necessária para a biblioteca OpenCensus. É recomendável definir essa variável de ambiente em vez de passá-la como um parâmetro de pipeline para evitar passar pelas cadeias de conexão de texto não criptografado.

## <a name="querying-logs-in-application-insights"></a>Consultando logs no Application Insights

Os logs roteados para o Application Insights aparecerão em ' rastreamentos ' ou ' exceções '. Certifique-se de ajustar sua janela de tempo para incluir a execução do pipeline.

![Application Insights resultado da consulta](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

O resultado em Application Insights mostrará a mensagem de log e o nível, o caminho do arquivo e o número da linha de código. Ele também mostrará todas as dimensões personalizadas incluídas. Nesta imagem, o dicionário customDimensions mostra os pares de chave/valor do exemplo de [código](#creating-a-custom-dimensions-dictionary)anterior.

### <a name="additional-helpful-queries"></a>Consultas úteis adicionais

Algumas das consultas abaixo usam ' customDimensions. Level '. Esses níveis de severidade correspondem ao nível no qual o log do Python foi originalmente enviado. Para obter informações adicionais de consulta, consulte [Azure monitor log queries](/azure/data-explorer/kusto/query/).

| Caso de uso                                                               | Consulta                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Registrar resultados para uma dimensão personalizada específica, por exemplo, ' parent_run_id ' | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Registrar resultados para todas as execuções de treinamento nos últimos 7 dias                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Registrar resultados com o erro nível dos últimos 7 dias              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Contagem de resultados de log com erro nível nos últimos 7 dias     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Próximas etapas

Depois que você tiver logs em sua instância de Application Insights, eles poderão ser usados para definir [Azure monitor alertas](../azure-monitor/alerts/alerts-overview.md#what-you-can-alert-on) com base nos resultados da consulta.

Você também pode adicionar resultados de consultas a um [painel do Azure](../azure-monitor/app/tutorial-app-dashboards.md#add-logs-query) para obter informações adicionais.