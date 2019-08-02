---
title: 'Tutorial: Analisar eventos de configuração de Gêmeos Digitais do Azure | Microsoft Docs'
description: Saiba como visualizar e analisar eventos dos espaços dos Gêmeos Digitais do Azure, com o Azure Time Series Insights, usando as etapas neste tutorial.
services: digital-twins
author: alinamstanciu
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 12/18/2018
ms.author: alinast
ms.openlocfilehash: 3f6111457d3438b80ace8cd557747ab8c799efd3
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484746"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-by-using-time-series-insights"></a>Tutorial: Visualizar e analisar eventos de espaços dos Gêmeos Digitais do Azure usando o Time Series Insights

Depois de implantar a instância de Gêmeos Digitais do Azure, provisionar seus espaços e implementar a função personalizada para monitorar condições específicas, você pode visualizar os eventos e os dados provenientes de seus espaços em busca de tendências e anomalias.

No [primeiro tutorial](tutorial-facilities-setup.md), você configurou o grafo espacial de um prédio imaginário, com uma sala que contém sensores de movimento, dióxido de carbono e temperatura. No [segundo tutorial](tutorial-facilities-udf.md), você provisionou o grafo e uma função definida pelo usuário. A função monitora esses valores de sensor e dispara notificações sob as condições corretas. Ou seja, a sala está vazia e os níveis de temperatura e dióxido de carbono estão normais.

Este tutorial mostra como você pode integrar as notificações e os dados provenientes da configuração dos Gêmeos Digitais do Azure com o Azure Time Series Insights. Em seguida, você pode visualizar os valores de sensor ao longo do tempo. Você pode procurar tendências, como qual sala é a mais usada e quais são os horários mais cheios do dia. Você também pode detectar anomalias, como quais salas ficam mais abafadas ou quentes ou se alguma área do seu prédio está enviando valores de temperatura consistentemente altos, indicando que o ar-condicionado está com defeito.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Transmitir dados por streaming usando os Hubs de Eventos do Azure.
> * Analisar com o Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você [configurou](tutorial-facilities-setup.md) e [provisionou](tutorial-facilities-udf.md) sua configuração dos Gêmeos Digitais do Azure. Antes de prosseguir, verifique se você tem:

- Uma [conta do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma instância de Gêmeos Digitais em execução.
- Os [exemplos de C# dos Gêmeos Digitais](https://github.com/Azure-Samples/digital-twins-samples-csharp) baixados e extraídos do seu computador de trabalho.
- [SDK do .NET Core versão 2.1.403 ou posterior](https://www.microsoft.com/net/download) no computador de desenvolvimento para executar o exemplo. Execute `dotnet --version` para verificar se a versão instalada é a correta.

## <a name="stream-data-by-using-event-hubs"></a>Transmitir dados por streaming usando os Hubs de Eventos

Você pode usar o serviço [Hubs de Eventos](../event-hubs/event-hubs-about.md) para criar um pipeline para transmitir os dados. Esta seção mostra como criar seu hub de eventos como o conector entre os Gêmeos Digitais do Azure e as instâncias do Time Series Insights.

### <a name="create-an-event-hub"></a>Criar um Hub de Evento

1. Entre no [Portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **Criar um recurso**.

1. Pesquise e selecione **Hubs de Eventos**. Selecione **Criar**.

1. Insira um **Nome** para o namespace dos Hubs de Eventos. Escolha **Standard** como **Tipo de preço**, sua **Assinatura**, o **Grupo de recursos** que você usou para sua instância dos Gêmeos Digitais e o **Local**. Selecione **Criar**.

1. Na implantação de namespace dos Hubs de eventos, selecione o painel **Visão geral** e, em seguida, selecione **Ir para o recurso**.

    ![Namespace dos Hubs de Eventos após a implantação](./media/tutorial-facilities-analyze/open-event-hub-ns.png)

1. No painel **Visão geral** do namespace dos Hubs de Eventos, selecione o botão **Hub de Eventos** na parte superior.
    ![Botão Adicionar Hub de Eventos](./media/tutorial-facilities-analyze/create-event-hub.png)

1. Insira um **Nome** para o seu hub de eventos e selecione **Criar**.

   Uma vez implantado, ele aparecerá no painel **Hubs de Eventos** do namespace dos Hubs de Eventos com um status **Ativo**. Selecione o hub de eventos para abrir seu painel **Visão geral**.

1. Selecione o botão **Grupo de consumidores** na parte superior e digite um nome, como **tsievents**, para o grupo de consumidores. Selecione **Criar**.

    ![Grupo de consumidores do Hub de Eventos](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   Depois de criado, o grupo de consumidores aparecerá na lista, na parte inferior do painel **Visão geral** do hub de eventos.

1. Abra o painel **Políticas de acesso compartilhadas** do hub de eventos e selecione o botão **Adicionar**. Insira **ManageSend** como o nome da política, verifique se todas as caixas de seleção estão selecionadas e selecione **Criar**.

    ![Cadeia de conexão do Hub de Eventos](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. Abra a política ManageSend que você criou e copie os valores da **Cadeia de conexão – chave primária** e **Cadeia de conexão – chave secundária** para um arquivo temporário. Você precisará desses valores para criar um ponto de extremidade para o hub de eventos na próxima seção.

### <a name="create-an-endpoint-for-the-event-hub"></a>Criar um ponto de extremidade para o hub de eventos

1. Na janela de comando, verifique se você está na pasta **occupancy-quickstart\src** do exemplo dos Gêmeos Digitais do Azure.

1. Abra o arquivo **actions\createEndpoints.yaml** no editor. Substitua o conteúdo pelo seguinte:

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    ```

1. Substitua os espaços reservados `Primary_connection_string_for_your_event_hub` pelo valor da **Cadeia de conexão – chave primária** para o hub de eventos. Verifique se o formato dessa cadeia de conexão é o seguinte:

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Substitua os espaços reservados `Secondary_connection_string_for_your_event_hub` pelo valor da **Cadeia de conexão – chave secundária** para o hub de eventos. Verifique se o formato dessa cadeia de conexão é o seguinte: 

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Substitua os espaços reservados `Name_of_your_Event_Hub` pelo nome do seu Hub de Eventos.

    > [!IMPORTANT]
    > Insira todos os valores sem aspas. Verifique se há pelo menos um caractere de espaço após os dois-pontos no arquivo YAML. Você também pode validar o conteúdo do arquivo YAML usando qualquer validador YAML online, como [esta ferramenta](https://onlineyamltools.com/validate-yaml).

1. Salve e feche o arquivo. Execute o seguinte comando na janela de comando e entre com sua conta do Azure quando solicitado.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Ele cria dois pontos de extremidade para seu hub de eventos.

   ![Pontos de extremidade para os Hubs de Eventos](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>Analisar com o Time Series Insights

1. No painel esquerdo do [portal do Azure](https://portal.azure.com), selecione **Criar um recurso**. 

1. Pesquise e selecione um novo recurso do **Time Series Insights**. Selecione **Criar**.

1. Insira um **Nome** para a instância do Time Series Insights e selecione a **Assinatura**. Selecione o **Grupo de recursos** que você usou para a instância dos Gêmeos Digitais e seu **Local**. Selecione **Avançar: Botão Origem do evento** ou a guia **Origem do evento**.

    ![Seleções para criar uma instância do Time Series Insights](./media/tutorial-facilities-analyze/create-tsi.png)

1. Na guia **Origem do evento**, insira um **nome**, selecione **Hub de eventos** como o **Tipo de fonte** e verifique se os outros valores estão selecionados corretamente. Selecione **ManageSend** como o **Nome de política de acesso do hub de eventos** e selecione o grupo de consumidores criado na seção anterior como o **Grupo de consumidores do hub de eventos**. Selecione **Examinar + criar**.

    ![Seleções para criar uma fonte de evento](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. No painel **Revisar + criar**, examine as informações inseridas e selecione **Criar**.

1. No painel de implantação, selecione o recurso de Time Series Insights que você acabou de criar. Ele abre o painel **Visão geral** do ambiente do Time Series Insights.

1. Selecione o botão **Ir para o ambiente** na parte superior. Se você receber um aviso de acesso a dados, abra o painel **Políticas de acesso de dados** da instância do Time Series Insights, selecione **Adicionar**, selecione **Colaborador** como a função e selecione o usuário apropriado.

1. O botão **Ir para o ambiente** abre o [explorador do Time Series Insights](../time-series-insights/time-series-insights-explorer.md). Se ele não mostrar eventos, simule eventos de dispositivo navegando até o projeto **device-connectivity** do exemplo dos Gêmeos Digitais e execute `dotnet run`.

1. Depois de alguns eventos simulados serem gerados, volte para o explorador do Time Series Insights e selecione o botão Atualizar na parte superior. Você deve ver gráficos analíticos sendo criados para seus dados de sensor simulado. 

    ![Gráfico no explorador do Time Series Insights](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. No explorer do Time Series Insights, é possível gerar gráficos e mapas de calor para diferentes eventos e dados de suas salas, sensores e outros recursos. No lado esquerdo, use as listas suspensas **MEDIDA** e **DIVIDIR POR** para criar suas próprias visualizações. 

   Por exemplo, selecione **Eventos** como **MEDIDA** e **DigitalTwins-SensorHardwareId** como **DIVIDIR POR** para gerar um mapa de calor para cada um dos sensores. O mapa de calor será semelhante à seguinte imagem:

   ![Mapa de calor no explorador do Time Series Insights](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser parar de explorar os Gêmeos Digitais do Azure além desse ponto, você poderá excluir os recursos criados neste tutorial:

1. No menu à esquerda no [portal do Azure](https://portal.azure.com), selecione **Todos os recursos**, selecione o grupo de recursos dos Gêmeos Digitais e selecione **Excluir**.

    > [!TIP]
    > Se você teve problemas para excluir sua instância de Gêmeos Digitais, lançamos uma atualização de serviço com a correção. Tente novamente excluir a instância.

2. Se necessário, exclua os aplicativos de exemplo em seu computador de trabalho.

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo para saber mais sobre grafos de inteligência espacial e modelos de objeto nos Gêmeos Digitais do Azure.

> [!div class="nextstepaction"]
> [Noções básicas sobre modelos de objeto e grafos de inteligência espacial dos Gêmeos Digitais](concepts-objectmodel-spatialgraph.md)
