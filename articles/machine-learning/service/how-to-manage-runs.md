---
title: Iniciar, monitorar e cancelar as execuções de treinamento em Python
titleSuffix: Azure Machine Learning service
description: Saiba como começar, defina o status da marca e organizar seus experimentos de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: c0c1c1353b12944fa913dfb0789192917b99f234
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819198"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Iniciar, monitorar e cancelar as execuções de treinamento em Python

O [SDK de aprendizado de máquina do Azure para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) fornece vários métodos para monitorar, organizar e gerenciar suas execuções de treinamento e experimentação.

Este artigo mostra exemplos das seguintes tarefas:

* Monitor de desempenho de execução.
* Cancelar ou não é executado.
* Crie filho é executado.
* Marcar e encontrar execuções.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará dos seguintes itens:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Uma [espaço de trabalho de serviço de Azure Machine Learning](setup-create-workspace.md).

* O SDK de aprendizado de máquina do Azure para Python (versão 1.0.21 ou posterior). Para instalar ou atualizar para a versão mais recente do SDK, consulte [instalar ou atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Para verificar sua versão do SDK do Azure Machine Learning, use o seguinte código:

    ```Python
    print(azureml.core.VERSION)
    ```

## <a name="start-a-run-and-its-logging-process"></a>Iniciar uma execução e seu processo de registro em log

Configurar seu experimento, importando o [espaço de trabalho](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [experimentar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [execute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py), e [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) classes a partir de [azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) pacote.

```Python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Iniciar uma execução e seu processo de registro em log com o [ `start_logging()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) método.

```Python
notebook_run = exp.start_logging()

notebook_run.log(name="message", value="Hello from run!")
```

## <a name="monitor-the-status-of-a-run"></a>Monitorar o status de uma execução

Obter o status de uma execução com o [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) método.

```Python
print(notebook_run.get_status())
```

Para obter detalhes adicionais sobre a execução, use o [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) método.

```Python
notebook_run.get_details()
```

Quando sua execução for concluída com êxito, use o [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) método marcá-lo como concluído.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Se você usar do Python `with...as` padrão, a execução marcará automaticamente em si como concluído quando a execução estiver fora do escopo. Você não precisa marcar manualmente a execução como concluído.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

## <a name="cancel-or-fail-runs"></a>Cancelar ou deixar de execuções

 Se você observar um erro ou se o seu tempo de execução está demorando muito para ser concluído, use o [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) método para parar a execução antes que ele termine e marcá-la como cancelada.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Se a conclusão da execução, mas ele contém um erro (por exemplo, o script de treinamento incorreta foi usado), você pode usar o [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) método marcá-lo como falha.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

## <a name="create-child-runs"></a>Criar filho é executado

Crie filho é executado para agrupar execuções relacionadas, como iterações diferentes de ajuste de hiperparâmetro.

Este exemplo de código usa o `hello_with_children.py` script para criar um lote de cinco execuções de filho de dentro de uma execução enviada usando o [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) método:

```Python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Quando eles passam fora do escopo, o filho executa automaticamente é marcadas como concluídos.

Você também pode iniciar execuções filho individualmente, mas como cada criação resulta em uma chamada de rede, é menos eficiente do que enviar um lote de execuções.

Para consultar as execuções de filho de um pai específico, use o [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) método.

```Python
list(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Marcar e encontrar execuções

No serviço de Azure Machine Learning, você pode usar marcas e propriedades para ajudar a organizar e consultar as execuções para obter informações importantes.

### <a name="add-properties-and-tags"></a>Adicionar marcas e propriedades

Para adicionar metadados pesquisáveis para suas execuções, use o [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) método. Por exemplo, o código a seguir adiciona o `"author"` propriedade para a execução:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Propriedades são imutáveis, então, eles criam um registro permanente para fins de auditoria. O código a seguir os resultados de exemplo em um erro, como nós já adicionamos `"azureml-user"` como o `"author"` valor da propriedade no código anterior:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Ao contrário das propriedades, as marcas são mutáveis. Para adicionar informações pesquisáveis e significativas para os consumidores do seu experimento, use o [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) método.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Você também pode adicionar marcas de cadeia de caracteres simples. Quando essas marcas são exibidos no dicionário de marca, eles têm um valor de `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Marcas e propriedades de consulta

Você pode consultar é executado dentro de um experimento para retornar uma lista de execuções que correspondem a marcas e propriedades específicas.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Blocos de anotações de exemplo

Os seguintes blocos de anotações demonstram os conceitos neste artigo:

* Para saber mais sobre as APIs de log, consulte a [notebook da API de registro em log](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Para obter mais informações sobre como gerenciar é executado com o SDK do Azure Machine Learning, consulte o [Gerenciar execuções de notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Próximas etapas

* Para saber como registrar em log as métricas para seus testes, consulte [registrar métricas durante execuções de treinamento](how-to-track-experiments.md).