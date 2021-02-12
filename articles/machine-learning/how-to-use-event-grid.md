---
title: Eventos de gatilho em fluxos de trabalho am (visualização)
titleSuffix: Azure Machine Learning
description: Configure aplicativos, processos ou fluxos de trabalho de machine learning de CI/CD controlados por eventos no Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 1fd177273c9dafb04add64d8a8bfef1d81cc65d0
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319318"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Disparar aplicativos, processos ou fluxos de trabalho de CI/CD com base em eventos de Azure Machine Learning (versão prévia)

Neste artigo, você aprenderá a configurar aplicativos, processos ou fluxos de trabalho de CI/CD controlados por eventos com base em eventos do Azure Machine Learning, como emails de notificação de falha ou execuções de pipeline de ML, quando determinadas condições são detectadas pela [Grade de Eventos do Azure](../event-grid/index.yml).

O Azure Machine Learning gerencia todo o ciclo de vida do processo de machine learning, incluindo treinamento de modelo, implantação de modelo e monitoramento. Você pode usar a Grade de Eventos para reagir a eventos do Azure Machine Learning, como a conclusão de execuções de treinamento, o registro e a implantação de modelos e a detecção de descompasso de dados, usando arquiteturas modernas sem servidor. Em seguida, é possível assinar e consumir eventos como o status de execução alterado, conclusão da execução, registro de modelo, implantação de modelo e detecção de descompasso de dados em um workspace.

Quando usar a Grade de Eventos para ações controladas por evento:
* Enviar emails sobre falhas e conclusão de execução
* Usar uma função do Azure depois que um modelo é registrado
* Transmitir eventos do Azure Machine Learning para vários pontos de extremidade
* Disparar um pipeline de ML quando o descompasso for detectado

> [!NOTE] 
> Atualmente, os eventos runStatusChanged são disparados apenas quando o status de execução é **falha**

## <a name="prerequisites"></a>Pré-requisitos
Para usar a Grade de Eventos, você precisa de acesso de colaborador ou proprietário ao Workspace do Azure Machine Learning para o qual você criará eventos.

## <a name="the-event-model--types"></a>Modelo e tipos de evento

A Grade de Eventos do Azure lê eventos de origens, como o Azure Machine Learning e outros serviços do Azure. Esses eventos são enviados para manipuladores de eventos, como os Hubs de Eventos do Azure, o Azure Functions, os Aplicativos Lógicos e outros. O diagrama a seguir mostra como a Grade de Eventos conecta origens e manipuladores, mas não é uma lista abrangente de integrações compatíveis.

![Modelo funcional da Grade de Eventos do Azure](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Para saber mais sobre as origens e os manipuladores de eventos, confira [O que é a Grade de Eventos?](../event-grid/overview.md).

### <a name="event-types-for-azure-machine-learning"></a>Tipos de eventos para o Azure Machine Learning

O Azure Machine Learning fornece eventos em vários pontos do ciclo de vida de machine learning: 

| Tipo de evento | Descrição |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Gerado quando uma execução de experimento de machine learning é concluída |
| `Microsoft.MachineLearningServices.ModelRegistered` | Gerado quando um modelo de machine learning é registrado no workspace |
| `Microsoft.MachineLearningServices.ModelDeployed` | Gerado quando uma implantação do serviço de inferência com um ou mais modelos é concluída |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Gerado quando um trabalho de detecção de descompasso de dados para dois conjuntos de dados é concluído |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Gerado quando um status de execução é alterado, atualmente só é gerado quando um status de execução é de "Falha" |

### <a name="filter--subscribe-to-events"></a>Filtrar e assinar eventos

Esses eventos são publicados por meio da Grade de Eventos do Azure. Por meio do portal do Azure, do PowerShell ou da CLI do Azure, os clientes podem assinar facilmente os eventos [especificando um ou mais tipos de evento e filtrando as condições](../event-grid/event-filtering.md). 

Ao configurar seus eventos, você pode aplicar filtros para disparar apenas dados específicos dos eventos. No exemplo a seguir, para eventos de status de execução alterado, é possível filtrar por tipos de execução. O evento só dispara quando os critérios são atendidos. Confira [Esquema de grade de eventos do Azure Machine Learning](../event-grid/event-schema-machine-learning.md) para saber mais sobre os dados de evento que podem ser filtrados. 

As assinaturas para eventos de Azure Machine Learning são protegidas pelo Azure RBAC (controle de acesso baseado em função). Somente um [colaborador ou proprietário](how-to-assign-roles.md#default-roles) do workspace pode criar, atualizar e excluir assinaturas de evento.  Filtros podem ser aplicados a assinaturas de evento durante a [criação](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest&preserve-view=true) da assinatura do evento ou em um momento posterior. 


1. Acesse o portal do Azure, selecione uma nova assinatura ou uma existente. 

1. Selecione a guia de filtros e role para baixo até "Filtros avançados". Em **Chave** e **Valor** , defina os tipos de propriedade que você quer filtrar. Aqui você pode ver que o evento só será disparado quando o tipo de execução for uma execução de pipeline ou etapa de pipeline.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="Filtrar eventos":::


+ **Filtrar por tipo de evento:** Uma assinatura de evento pode especificar um ou mais tipos de evento do Azure Machine Learning.

+ **Filtrar por assunto do evento:** A Grade de Eventos do Azure é compatível com filtros de assuntos baseados em correspondências de __começa com__ e __termina com__ , para que os eventos com o assunto correspondente sejam entregues ao assinante. Diferentes eventos de machine learning têm um formato de assunto distinto.

  | Tipo de evento | Formato do assunto | Exemplo de assunto |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Filtragem avançada** : A Grade de Eventos do Azure também é compatível com a filtragem avançada com base no esquema de evento publicado. Os detalhes do esquema de eventos do Azure Machine Learning podem ser encontrados em [Esquema de eventos da Grade de Eventos do Azure para o Azure Machine Learning](../event-grid/event-schema-machine-learning.md).  Alguns exemplos de filtros avançados que você pode executar:

  Para o evento `Microsoft.MachineLearningServices.ModelRegistered`, filtrar o valor da tag do modelo:

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  Para saber mais sobre como aplicar filtros, confira [Filtrar eventos para a Grade de Eventos](../event-grid/how-to-filter-events.md).

## <a name="consume-machine-learning-events"></a>Consumir eventos do Machine Learning

Aplicativos que manipulam eventos do Machine Learning devem seguir algumas práticas recomendadas:

> [!div class="checklist"]
> * Como várias assinaturas podem ser configuradas para direcionar eventos para o mesmo manipulador, é importante não supor que os eventos sejam de uma origem específica, mas para verificar o tópico de mensagem a fim de garantir que ela venha do Workspace do Machine Learning que você está esperando.
> * Da mesma forma, verifique se o eventType é do tipo que você está preparado para processar, e não suponha que todos os eventos recebidos serão os tipos esperados.
> * Como as mensagens podem chegar fora de ordem e após algum atraso, use os campos de etag para entender se suas informações sobre objetos ainda estão atualizadas.  Além disso, use os campos do sequenciador para entender a ordem de eventos em qualquer objeto específico.
> * Ignore os campos que você não entende. Essa prática ajudará você a manter-se resiliente a novos recursos que possam ser adicionados no futuro.
> * As operações do Azure Machine Learning com falha ou canceladas não dispararão um evento. Por exemplo, se uma implantação de modelo falhar, Microsoft.MachineLearningServices.ModelDeployed não será disparado. Considere esse modo de falha ao projetar seus aplicativos. Você sempre pode usar o SDK, a CLI ou o portal do Azure Machine Learning para verificar o status de uma operação e entender os motivos de falha com detalhes.

A Grade de Eventos do Azure permite que os clientes criem manipuladores de mensagens desacoplados, que podem ser disparados por eventos do Azure Machine Learning. Alguns exemplos notáveis de manipuladores de mensagens são:
* Funções do Azure
* Aplicativos Lógicos do Azure
* Hubs de eventos do Azure
* Pipeline do Azure Data Factory
* Webhooks genéricos, que podem ser hospedados na plataforma do Azure ou em outro lugar

## <a name="set-up-in-azure-portal"></a>Configurar no portal do Azure

1. Abra o [portal do Azure](https://portal.azure.com) e acesse o Workspace do Azure Machine Learning.

1. Na barra à esquerda, selecione __Eventos__ e **Assinaturas de Evento**. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Selecione o tipo de evento a ser consumido. Por exemplo, na seguinte captura de tela, __Modelo registrado__ , __Modelo implantado__ , __Execução concluída__ e __Descompasso de conjunto de dados detectado__ estão selecionados:

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Selecione o ponto de extremidade para publicar o evento. Na seguinte captura de tela, o __Hub de eventos__ é o ponto de extremidade selecionado:

    ![Captura de tela mostra o painel criar assinatura de evento com selecionar Hub de eventos abrir.](./media/how-to-use-event-grid/select-event-handler.png)

Depois de confirmar sua seleção, clique em __Criar__. Após a configuração, esses eventos serão enviados por push para o ponto de extremidade.


### <a name="set-up-with-the-cli"></a>Configurar com a CLI

É possível instalar a [CLI do Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) mais recente ou usar o Azure Cloud Shell fornecido como parte da assinatura do Azure.

Para instalar a extensão da Grade de Eventos, use o seguinte comando da CLI:

```azurecli-interactive
az add extension --name eventgrid
```

O seguinte exemplo demonstra como selecionar uma assinatura do Azure e cria e uma assinatura de evento para o Azure Machine Learning:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace. This example uses EventHub as a destination. 
az eventgrid event-subscription create --name {eventGridFilterName} \
  --source-resource-id /subscriptions/{subId}/resourceGroups/{RG}/providers/Microsoft.MachineLearningServices/workspaces/{wsName} \
  --endpoint-type eventhub \
  --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.EventHub/namespaces/n1/eventhubs/EH1 \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>Exemplos

### <a name="example-send-email-alerts"></a>Exemplo: Enviar alertas de email

Use os [Aplicativos Lógicos do Azure](../logic-apps/index.yml) para configurar emails para todos os seus eventos. Personalize com condições e especifique os destinatários para permitir a colaboração e o reconhecimento entre equipes que trabalham juntas.

1. Na portal do Azure, acesse o Workspace do Azure Machine Learning e selecione a guia de eventos na barra à esquerda. Nela, selecione __Aplicativos Lógicos__. 

    ![Captura de tela mostra uma página Machine Learning eventos de espaço de trabalho com aplicativos lógicos.](./media/how-to-use-event-grid/select-logic-ap.png)

1. Entre na interface do usuário do Aplicativo Lógico e selecione "serviço do Machine Learning" como o tipo de tópico. 

    ![Captura de tela mostra a caixa de diálogo quando um evento de recurso ocorre com o Machine Learning selecionado como um tipo de recurso.](./media/how-to-use-event-grid/select-topic-type.png)

1. Selecione quais eventos serão notificados. Por exemplo, a captura de tela a seguir, __RunCompleted__.

    ![Captura de tela mostra a caixa de diálogo quando um evento de recurso ocorre com um tipo de evento selecionado.](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Use o método de filtragem na seção acima ou adicione filtros para disparar apenas o aplicativo lógico em um subconjunto de tipos de evento. Na captura de tela a seguir, um __filtro de prefixo__ de __/datadriftID/runs/__ é usado.

    ![filter-events](./media/how-to-use-event-grid/filtering-events.png)

1. Em seguida, adicione uma etapa para consumir esse evento e pesquise o email. Há várias contas de email diferentes que você pode usar para receber eventos. Você também pode configurar condições sobre quando enviar um alerta por email.

    ![Captura de tela mostra a caixa de diálogo escolher uma ação com email inserido na linha de pesquisa.](./media/how-to-use-event-grid/select-email-action.png)

1. Selecione __Enviar um email__ e preencha os parâmetros. No assunto, você pode incluir o __Tipo de Evento__ e o __Tópico__ para ajudar a filtrar os eventos. Você também pode incluir um link para a página do workspace para as execuções no corpo da mensagem. 

    ![Captura de tela mostra a caixa de diálogo enviar um email com o tópico e o tipo de evento adicionados à linha de assunto da lista à direita.](./media/how-to-use-event-grid/configure-email-body.png)

1. Para salvar essa ação, selecione **Salvar Como** no canto esquerdo da página. Na barra à direita que aparece, confirme a criação desta ação.

    ![Captura de tela mostra os botões salvar como e criar no designer de aplicativos lógicos.](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Exemplo: Novo treinamento dos gatilhos de descompasso de dados

Os modelos ficam obsoletos com o passar do tempo e não permanecem úteis no contexto em que estão sendo executados. Um modo de saber se é hora de treinar novamente o modelo é detectar o descompasso de dados. 

Este exemplo mostra como usar a grade de eventos com um Aplicativo Lógico do Azure para disparar o novo treinamento. O exemplo dispara um pipeline do Azure Data Factory quando o descompasso de dados ocorre entre o treinamento de um modelo e o fornecimento de conjuntos de dados.

Antes de começar, realize as seguintes ações:

* Configurar um monitor de conjunto de dados para [detectar o descompasso de dados](how-to-monitor-datasets.md) em um workspace
* Criar um [pipeline publicado do Azure Data Factory](../data-factory/index.yml).

Neste exemplo, um pipeline simples do Data Factory é usado a fim de copiar arquivos para um repositório de BLOB e executar um pipeline publicado do Machine Learning. Para saber mais sobre este cenário, veja como configurar uma [etapa do Machine Learning no Azure Data Factory](../data-factory/transform-data-machine-learning-service.md).

![Captura de tela mostra o pipeline de treinamento em recursos de fábrica com cópia de data1 de dados1 M L executar Pipeline1.](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Comece com a criação do aplicativo lógico. Abra o [portal do Azure](https://portal.azure.com), pesquise os Aplicativos Lógicos e clique em "Criar".

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Preencha as informações solicitadas. Para simplificar a experiência, use a mesma assinatura e grupo de recursos que o pipeline do Azure Data Factory e o Workspace do Azure Machine Learning.

    ![Captura de tela mostra o painel criar aplicativo lógico.](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Depois de criar o aplicativo lógico, selecione __Quando ocorrer um evento de recurso da Grade de Eventos__. 

    ![Captura de tela mostra o designer de aplicativos lógicos com início com opções de gatilho comuns, incluindo quando ocorre um evento de recurso de grade de eventos.](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Faça logon e preencha os detalhes do evento. Defina o __Nome do Recurso__ no nome do workspace. Defina o __Tipo de Evento__ como __DatasetDriftDetected__.

    ![Captura de tela mostra o quando um evento de recurso ocorre com um item de tipo de evento selecionado.](./media/how-to-use-event-grid/login-and-add-event.png)

1. Adicione uma nova etapa e pesquise __Azure Data Factory__. Selecione __Criar uma execução de pipeline__. 

    ![Captura de tela mostra o painel escolher uma ação com criar uma execução de pipeline selecionada.](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Faça logon e especifique o pipeline publicado do Azure Data Factory a ser executado.

    ![Captura de tela mostra o painel criar um pipeline de execução com vários valores.](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Salve e crie o aplicativo lógico usando o botão **Salvar** na parte superior esquerda da página. Para ver o aplicativo, acesse o workspace no [portal do Azure](https://portal.azure.com) e clique em **Eventos**.

    ![Captura de tela mostra eventos com o aplicativo lógico realçado.](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Agora, o pipeline de data factory é disparado quando ocorre o descompasso. Veja os detalhes da execução de descompasso de dados e o pipeline de machine learning no [novo portal do workspace](https://ml.azure.com). 

![Captura de tela mostra pontos de extremidade de pipeline.](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Exemplo: Implantar um modelo com base em tags

Um objeto de modelo do Azure Machine Learning contém parâmetros em que você pode dinamizar implantações, como nome do modelo, versão, tag e propriedade. O evento de registro de modelo pode disparar um ponto de extremidade e você pode usar uma Função do Azure para implantar um modelo com base no valor desses parâmetros.

Para ver um exemplo, confira o repositório [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) e siga as etapas no arquivo **leiame**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a Grade de Eventos e experimente os eventos do Azure Machine Learning:

- [Sobre a Grade de Eventos](../event-grid/overview.md)

- [Esquema de eventos do Azure Machine Learning](../event-grid/event-schema-machine-learning.md)