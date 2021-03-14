---
title: Monitorar e exibir logs de execução do ML & métricas
titleSuffix: Azure Machine Learning
description: Monitore seus experimentos de ML e exiba métricas de execução com widgets Jupyter e o Azure Machine Learning Studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 47531da9c1e508281a57074df7aa10ffffe78810
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518731"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>Monitorar e exibir os logs e as métricas de execução de ML

Saiba como monitorar as execuções de Azure Machine Learning e exibir seus logs. 

Quando você executa um experimento, os logs e as métricas são transmitidos para você.  Além disso, você pode adicionar o seu próprio.  Para saber como, consulte [habilitar o registro em log nas execuções de treinamento do Azure ml](how-to-track-experiments.md).

Os logs podem ajudá-lo a diagnosticar erros e avisos para sua execução. Métricas de desempenho como parâmetros e precisão de modelo ajudam a acompanhar e monitorar suas execuções.

Neste artigo, você aprenderá a exibir os logs usando os seguintes métodos:

> [!div class="checklist"]
> * Monitorar execuções no estúdio
> * Monitorar execuções usando o widget Jupyter Notebook
> * Monitorar execuções automatizadas do Machine Learning
> * Exibir logs de saída após a conclusão
> * Exibir logs de saída no estúdio

Para obter informações gerais sobre como gerenciar seus experimentos, consulte [Iniciar, monitorar e cancelar execuções de treinamento](how-to-manage-runs.md).

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Monitorar execuções usando o widget Jupyter Notebook

Ao usar o método **ScriptRunConfig** para enviar execuções, você pode observar o progresso da execução usando o [widget Jupyter](/python/api/azureml-widgets/azureml.widgets). Como o envio de execução, o widget é assíncrono e fornece atualizações ao vivo a cada 10 a 15 segundos até que o trabalho seja concluído.

Exiba o widget do Jupyter enquanto aguarda a execução ser concluída.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Captura de tela do widget do Jupyter Notebook](./media/how-to-track-experiments/run-details-widget.png)

Você também pode obter um link para a mesma exibição no seu workspace.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>Monitorar execuções automatizadas do Machine Learning

Para execuções automatizadas do Machine Learning, para acessar os gráficos de uma execução anterior, substitua `<<experiment_name>>` pelo nome de teste apropriado:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Widget do notebook Jupyter para Machine Learning automatizado](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>Mostrar saída após a conclusão

Quando você usa **ScriptRunConfig**, pode usar ```run.wait_for_completion(show_output = True)``` para mostrar quando o treinamento do modelo está concluído. O sinalizador ```show_output``` fornece saída detalhada. Para obter mais informações, consulte a seção ScriptRunConfig de [como habilitar o registro em log](how-to-track-experiments.md#scriptrun-logs).

<a id="queryrunmetrics"></a>

## <a name="view-run-metrics"></a>Exibir métricas de execução

## <a name="via-the-sdk"></a>Por meio do SDK
Você pode exibir as métricas de um modelo treinado usando ```run.get_metrics()```. Veja o exemplo abaixo. 

```python
from azureml.core import Run
run = Run.get_context()
run.log('metric-name', metric_value)

metrics = run.get_metrics()
# metrics is of type Dict[str, List[float]] mapping mertic names
# to a list of the values for that metric in the given run.

metrics.get('metric-name')
# list of metrics in the order they were recorded
```

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-records-in-the-studio"></a>Exibir registros de execução no estúdio

Você pode procurar os registros de execução concluídos, incluindo as métricas registradas, no [Azure Machine Learning Studio](https://ml.azure.com).

Navegue até a guia **experimentos** . Para exibir todas as suas execuções em seu espaço de trabalho entre experimentos, selecione a guia **todas as execuções** . Você pode fazer uma busca detalhada em execuções para experimentos específicos aplicando o filtro experimento na barra de menus superior.

Para a exibição de experimento individual, selecione a guia **todos os experimentos** . No painel Executar teste, você pode ver as métricas e os logs acompanhados para cada execução. 

Você também pode editar a tabela de lista de execução para selecionar várias execuções e exibir o valor registrado por último, mínimo ou máximo para suas execuções. Personalize seus gráficos para comparar os valores de métricas registrados e as agregações entre várias execuções. 

![Detalhes da execução no estúdio do Azure Machine Learning](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="view-log-files-for-a-run"></a>Exibir arquivos de log para uma execução 

Os arquivos de log são um recurso essencial para depurar as cargas de trabalho do ML do Azure. Faça uma busca detalhada até uma execução específica para exibir seus logs e saídas:  

1. Navegue até a guia **experimentos** .
1. Selecione o runID para uma execução específica.
1. Selecione **saídas e logs** na parte superior da página.

:::image type="content" source="media/how-to-monitor-view-training-logs/view-logs.png" alt-text="Captura de tela da seção saída e logs de uma execução":::

As tabelas a seguir mostram o conteúdo dos arquivos de log nas pastas que você verá nesta seção.

> [!NOTE]
> Você não verá, necessariamente, todos os arquivos para cada execução. Por exemplo, o 20_image_build_log *. txt só aparece quando uma nova imagem é criada (por exemplo, quando você altera seu ambiente).

#### <a name="azureml-logs-folder"></a>`azureml-logs` pasta

|Arquivo  |Descrição  |
|---------|---------|
|20_image_build_log.txt     | Log de construção de imagem do Docker para o ambiente de treinamento, opcional, uma por execução. Aplicável somente ao atualizar seu ambiente. Caso contrário, o AML reutilizará a imagem armazenada em cache. Se for bem-sucedido, contém detalhes do registro de imagem para a imagem correspondente.         |
|55_azureml-Execution-<node_id # C1.txt     | log stdout/stderr da ferramenta host, um por nó. Efetua pull da imagem para o destino de computação. Observe que esse log aparece apenas quando você tem recursos de computação protegidos.         |
|65_job_prep-<node_id # C1.txt     |   log stdout/stderr do script de preparação do trabalho, um por nó. Baixe seu código para computar o destino e os repositórios de armazenamento (se solicitado).       |
|70_driver_log (_x). txt      |  log stdout/stderr do script de controle AML e do script de treinamento do cliente, um por processo. **Essa é a saída padrão do seu script. É aí que os logs de seu código (por exemplo, as instruções Print) aparecem.** Na maioria dos casos, você monitorará os logs aqui.       |
|70_mpi_log.txt     |   Log da estrutura MPI, opcional, um por execução. Somente para a execução de MPI.   |
|75_job_post-<node_id # C1.txt     |  log stdout/stderr do script de liberação de trabalho, um por nó. Enviar logs, liberar os recursos de computação de volta para o Azure.        |
|process_info.jsem      |   mostrar qual processo está sendo executado em qual nó.  |
|process_status.jsem      | Mostrar status do processo, ou seja, se um processo não for iniciado, em execução ou concluído.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` pasta

|Arquivo  |Descrição  |
|---------|---------|
|110_azureml. log      |         |
|job_prep_azureml. log     |   log do sistema para preparação do trabalho        |
|job_release_azureml. log     | log do sistema para a liberação do trabalho        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` pasta

Quando sidecar estiver habilitado, os scripts de preparação de trabalho e de liberação de trabalho serão executados no contêiner sidecar.  Há uma pasta para cada nó. 

|Arquivo  |Descrição  |
|---------|---------|
|start_cms.txt     |  Log de processo que inicia quando o contêiner sidecar é iniciado       |
|prep_cmd.txt      |   Log para ContextManagers inserido quando `job_prep.py` é executado (alguns deles serão transmitidos para `azureml-logs/65-job_prep` )       |
|release_cmd.txt     |  Log para ComtextManagers saiu quando `job_release.py` é executado        |

#### <a name="other-folders"></a>Outras pastas

Para o treinamento de trabalhos em clusters de várias computações, os logs estão presentes para cada IP de nó. A estrutura de cada nó é igual a trabalhos de nó único. Há uma pasta de logs adicional para logs de execução geral, stderr e stdout.

Azure Machine Learning registra as informações de uma variedade de fontes durante o treinamento, como AutoML ou o contêiner do Docker que executa o trabalho de treinamento. Muitos desses logs não estão documentados. Se você encontrar problemas e entrar em contato com o suporte da Microsoft, eles poderão usar esses logs na solução.

## <a name="monitor-a-compute-cluster"></a>Monitorar um cluster de computação

Para monitorar execuções para um destino de computação específico do seu navegador, use as seguintes etapas:

1. No [Azure Machine Learning Studio](https://ml.azure.com/), selecione seu espaço de trabalho e, em seguida, selecione __computação__ no lado esquerdo da página.

1. Selecione __Clusters de Treinamento__ para exibir uma lista de destinos de computação usados para treinamento. Em seguida, selecione o cluster.

    ![Selecionar o cluster de treinamento](./media/how-to-track-experiments/select-training-compute.png)

1. Selecione __Execuções__. A lista de execuções que usam esse cluster é exibida. Para exibir detalhes de uma execução específica, use o link na coluna __Executar__. Para exibir detalhes do experimento, use o link na coluna __Experimento__.

    ![Selecionar execuções do cluster de treinamento](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Como os destinos de computação de treinamento são um recurso compartilhado, eles podem ter várias execuções enfileiradas ou ativas em um determinado momento.
    > 
    > Uma execução pode conter execuções filhas. Portanto, um trabalho de treinamento pode resultar em várias entradas.

Quando uma execução é concluída, ela deixa de ser exibida nessa página. Para exibir informações sobre execuções concluídas, visite a seção __Experimentos__ do estúdio e selecione o experimento e a execução. Para obter mais informações, consulte a seção [Exibir métricas para execuções concluídas](#view-the-experiment-in-the-web-portal).


## <a name="next-steps"></a>Próximas etapas

Experimente estas próximas etapas para aprender a usar o Azure Machine Learning:

* Saiba como [acompanhar experimentos e habilitar logs no designer de Azure Machine Learning](how-to-track-designer-experiments.md).

* Veja um exemplo de como registrar o melhor modelo e implantá-lo no tutorial [Treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).
