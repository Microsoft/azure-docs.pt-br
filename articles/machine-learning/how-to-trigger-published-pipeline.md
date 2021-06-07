---
title: Disparar pipelines de Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Pipelines disparados permitem automatizar tarefas demoradas de rotina, como processamento de dados, treinamento e monitoramento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 01/29/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 3ecf4458b052f4fdc0eb2e6e697b0468c71ce9c2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519649"
---
# <a name="trigger-machine-learning-pipelines"></a>Disparar pipelines do Machine Learning

Neste artigo, você aprenderá a agendar programaticamente um pipeline para ser executado no Azure. Você pode criar uma agenda com base no tempo decorrido ou nas alterações do sistema de arquivos. Agendas baseadas em tempo podem ser usadas para cuidar de tarefas rotineiras, como o monitoramento de descompasso de dados. Os agendamentos baseados em alterações podem ser usados para reagir a alterações irregulares ou imprevisíveis, como novos dados carregados ou dados antigos sendo editados. Depois de aprender a criar agendas, você aprenderá a recuperá-las e desativá-las. Por fim, você aprenderá a usar outros serviços do Azure, o aplicativo lógico do Azure e o Azure Data Factory, para executar pipelines. Um aplicativo lógico do Azure permite uma lógica ou comportamento mais complexo de gatilho. Azure Data Factory pipelines permitem chamar um pipeline do Machine Learning como parte de um pipeline de orquestração de dados maior.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).

* Um ambiente do Python no qual o SDK do Azure Machine Learning para Python está instalado. Para obter mais informações, consulte [criar e gerenciar ambientes reutilizáveis para treinamento e implantação com o Azure Machine Learning.](how-to-use-environments.md)

* Um espaço de trabalho Machine Learning com um pipeline publicado. Você pode usar aquela criada para [criar e executar pipelines de Machine Learning com Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md).

## <a name="trigger-pipelines-with-azure-machine-learning-sdk-for-python"></a>Disparar pipelines com Azure Machine Learning SDK para Python

Para agendar um pipeline, você precisará de uma referência ao seu espaço de trabalho, o identificador do pipeline publicado e o nome do experimento no qual você deseja criar o agendamento. Você pode obter esses valores com o seguinte código:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Criar um agendamento

Para executar um pipeline de forma recorrente, você criará uma agenda. Um `Schedule` associa um pipeline, um experimento e um gatilho. O gatilho pode ser um `ScheduleRecurrence` que descreve a espera entre execuções ou um caminho de repositório de armazenamento que especifica um diretório para observar as alterações. Em ambos os casos, você precisará do identificador do pipeline e do nome do experimento no qual criar o agendamento.

Na parte superior do arquivo Python, importe as `Schedule` classes e `ScheduleRecurrence` :

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Criar uma agenda baseada em tempo

O `ScheduleRecurrence` Construtor tem um `frequency` argumento necessário que deve ser uma das seguintes cadeias de caracteres: "minuto", "hora", "dia", "semana" ou "mês". Ele também requer um `interval` argumento inteiro especificando quantas `frequency` unidades devem decorrer entre a agenda começar. Os argumentos opcionais permitem que você seja mais específico sobre horários de início, conforme detalhado nos [documentos do SDK do ScheduleRecurrence](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence).

Crie um `Schedule` que comece uma execução a cada 15 minutos:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Criar uma agenda baseada em alteração

Os pipelines disparados por alterações de arquivo podem ser mais eficientes do que os agendamentos baseados em tempo. Quando você deseja fazer algo antes de um arquivo ser alterado, ou quando um novo arquivo é adicionado a um diretório de dados, você pode pré-processar esse arquivo. Você pode monitorar qualquer alteração em um repositório de armazenamento ou alterações dentro de um diretório específico dentro do repositório de armazenamento. Se você monitorar um diretório específico, as alterações nos subdiretórios desse diretório _não_ dispararão uma execução.

Para criar um arquivo-reativo `Schedule` , você deve definir o `datastore` parâmetro na chamada para [Schedule. Create](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Para monitorar uma pasta, defina o `path_on_datastore` argumento.

O `polling_interval` argumento permite que você especifique, em minutos, a frequência na qual o repositório de armazenamento é verificado quanto a alterações.

Se o pipeline foi construído com um [DataPath](/python/api/azureml-core/azureml.data.datapath.datapath) [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter), você pode definir essa variável como o nome do arquivo alterado, definindo o `data_path_parameter_name` argumento.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Argumentos opcionais ao criar uma agenda

Além dos argumentos discutidos anteriormente, você pode definir o `status` argumento como `"Disabled"` para criar uma agenda inativa. Por fim, o `continue_on_step_failure` permite que você passe um booliano que substituirá o comportamento de falha padrão do pipeline.

## <a name="view-your-scheduled-pipelines"></a>Exibir seus pipelines agendados

No navegador da Web, navegue até Azure Machine Learning. Na seção **pontos de extremidade** do painel de navegação, escolha **pontos de extremidade de pipeline**. Isso leva você para uma lista dos pipelines publicados no espaço de trabalho.

:::image type="content" source="./media/how-to-trigger-published-pipeline/scheduled-pipelines.png" alt-text="Página pipelines do AML":::

Nesta página, você pode ver informações resumidas sobre todos os pipelines no espaço de trabalho: nomes, descrições, status e assim por diante. Faça uma busca detalhada clicando em seu pipeline. Na página resultante, há mais detalhes sobre o pipeline e você pode fazer uma busca detalhada em execuções individuais.

## <a name="deactivate-the-pipeline"></a>Desativar o pipeline

Se você tiver um `Pipeline` que é publicado, mas não agendado, você pode desabilitá-lo com:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Se o pipeline estiver agendado, você deverá cancelar o agendamento primeiro. Recupere o identificador da agenda do portal ou executando:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Quando você tiver o `schedule_id` que deseja desabilitar, execute:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Se você executar `Schedule.list(ws)` novamente, deverá obter uma lista vazia.

## <a name="use-azure-logic-apps-for-complex-triggers"></a>Usar aplicativos lógicos do Azure para gatilhos complexos 

Regras de gatilho mais complexas ou comportamento podem ser criados usando um [aplicativo lógico do Azure](../logic-apps/logic-apps-overview.md).

Para usar um aplicativo lógico do Azure para disparar um pipeline de Machine Learning, você precisará do ponto de extremidade REST para um pipeline de Machine Learning publicado. [Crie e publique seu pipeline](./how-to-create-machine-learning-pipelines.md). Em seguida, localize o ponto de extremidade REST de seu `PublishedPipeline` usando a ID do pipeline:

```python
# You can find the pipeline ID in Azure Machine Learning studio

published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
published_pipeline.endpoint 
```

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico

Agora, crie uma instância [do aplicativo lógico do Azure](../logic-apps/logic-apps-overview.md) . Se desejar, [use um ISE (ambiente do serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) e [Configure uma chave gerenciada pelo cliente](../logic-apps/customer-managed-keys-integration-service-environment.md) para ser usada pelo seu aplicativo lógico.

Depois que seu aplicativo lógico tiver sido provisionado, use estas etapas para configurar um gatilho para seu pipeline:

1. [Crie uma identidade gerenciada atribuída pelo sistema](../logic-apps/create-managed-service-identity.md) para conceder ao aplicativo acesso ao seu Workspace do Azure Machine Learning.

1. Navegue até o modo de exibição designer de aplicativo lógico e selecione o modelo aplicativo lógico em branco. 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/blank-template.png" alt-text="Modelo em branco":::

1. No designer, pesquise por **blob**. Selecione o gatilho **quando um blob é adicionado ou modificado (somente Propriedades)** e adicione esse gatilho ao seu aplicativo lógico.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/add-trigger.png" alt-text="Adicionar gatilho":::

1. Preencha as informações de conexão para a conta de armazenamento de BLOBs que você deseja monitorar para adições ou modificações de BLOB. Selecione o contêiner a ser monitorado. 
 
    Escolha o **intervalo** e a **frequência** para sondar as atualizações que funcionam para você.  

    > [!NOTE]
    > Esse gatilho monitorará o contêiner selecionado, mas não monitorará as subpastas.

1. Adicione uma ação HTTP que será executada quando um blob novo ou modificado for detectado. Selecione **+ nova etapa**, procure e selecione a ação http.

  > [!div class="mx-imgBorder"]
  > :::image type="content" source="media/how-to-trigger-published-pipeline/search-http.png" alt-text="Pesquisar ação HTTP":::

  Use as seguintes configurações para configurar sua ação:

  | Configuração | Valor | 
  |---|---|
  | Ação HTTP | POST |
  | URI |o ponto de extremidade para o pipeline publicado que você encontrou como um [pré-requisito](#prerequisites) |
  | Modo de autenticação | Identidade Gerenciada |

1. Configure sua agenda para definir o valor de qualquer [pipelineparameters de DataPath](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) que você possa ter:

    ```json
    {
      "DataPathAssignments": {
        "input_datapath": {
          "DataStoreName": "<datastore-name>",
          "RelativePath": "@{triggerBody()?['Name']}" 
        }
      },
      "ExperimentName": "MyRestPipeline",
      "ParameterAssignments": {
        "input_string": "sample_string3"
      },
      "RunSource": "SDK"
    }
    ```

    Use o `DataStoreName` que você adicionou ao seu espaço de trabalho como um [pré-requisito](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/http-settings.png" alt-text="Configurações de HTTP":::

1. Selecione **salvar** e sua agenda agora está pronta.

> [!IMPORTANT]
> Se você estiver usando o controle de acesso baseado em função do Azure (RBAC do Azure) para gerenciar o acesso ao seu pipeline, [defina as permissões para seu cenário de pipeline (treinamento ou pontuação)](how-to-assign-roles.md#common-scenarios).

## <a name="call-machine-learning-pipelines-from-azure-data-factory-pipelines"></a>Chamar pipelines do Machine Learning de pipelines de Azure Data Factory

Em um pipeline Azure Data Factory, a atividade *Machine Learning executar pipeline* executa um pipeline Azure Machine Learning. Você pode encontrar essa atividade na página de criação do Data Factory na categoria *Machine Learning* :

:::image type="content" source="media/how-to-trigger-published-pipeline/azure-data-factory-pipeline-activity.png" alt-text="Captura de tela mostrando a atividade de pipeline ML no ambiente de criação de Azure Data Factory":::

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você usou o SDK do Azure Machine Learning para Python para agendar um pipeline de duas maneiras diferentes. Uma agenda se repete com base no tempo decorrido do relógio. A outra agenda será executada se um arquivo for modificado em um especificado `Datastore` ou dentro de um diretório nesse repositório. Você viu como usar o portal para examinar o pipeline e as execuções individuais. Você aprendeu a desabilitar uma agenda para que o pipeline pare de funcionar. Por fim, você criou um aplicativo lógico do Azure para disparar um pipeline. 

Para obter mais informações, consulte:

> [!div class="nextstepaction"]
> [Usar o Azure Machine Learning Pipelines para pontuação do lote](tutorial-pipeline-batch-scoring-classification.md)

* Saiba mais sobre [pipelines](concept-ml-pipelines.md)
* Saiba mais sobre como [explorar Azure Machine Learning com Jupyter](samples-notebooks.md)