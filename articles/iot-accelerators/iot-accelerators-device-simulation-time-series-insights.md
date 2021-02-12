---
title: Visualizar telemetria simulada com o Time Series Insights-Azure | Microsoft Docs
description: Aprenda a configurar seu ambiente do Time Series Insights para explorar e analisar a telemetria gerada pelo acelerador de solução do Device Simulation.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: e1409b43f0ce1fc0d8c622dda79e857ac6abdd33
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854556"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Use as informações do Time Series para visualizar a telemetria enviada a partir do acelerador de solução do Device Simulation

O acelerador de solução Device Simulation permite gerar telemetria de dispositivos simulados para testar suas soluções de IoT. Este guia prático mostra como visualizar e analisar a telemetria simulada usando um ambiente de Séries Temporais.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas deste guia prático, você precisa de uma assinatura ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

As etapas neste guia prático pressupõem que você tenha implantado o acelerador da solução Simulação de dispositivos em sua assinatura do Azure. Se você ainda não implantou a simulação de dispositivo, consulte [implantação de simulação de dispositivo](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) no github.

Este artigo presume que o nome do seu acelerador de solução é **contoso-simulation**. Substitua **contoso-simulation** pelo nome do seu acelerador de solução ao concluir as etapas a seguir.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Você precisa criar um grupo de consumidores dedicado no hub IoT para telemetria de fluxo para o Time Series Insights. Uma fonte de evento no Time Series Insights deve ter o uso exclusivo de um grupo de consumidores do Hub IoT.

As etapas a seguir usam a CLI do Azure no Azure Cloud Shell para criar o grupo de consumidores:

1. O hub IoT é um dos vários recursos criados quando você implantou o acelerador de solução de Simulação de Dispositivo. Execute o seguinte comando para localizar o nome do hub IoT - lembre-se de usar o nome do seu acelerador de solução:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    O hub IoT é o recurso do tipo **Microsoft.Devices/IotHubs**.

1. Adicione um grupo de consumidores chamado **devicesimulationtsi** ao hub. No comando a seguir, use o nome do acelerador de solução e de hub:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Agora, você pode fechar o Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Crie um novo ambiente do Time Series Insights

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) é um serviço totalmente gerenciado de análise, armazenamento e visualização para o gerenciamento de dados de série temporal em escala IoT na nuvem. Para criar um novo ambiente do Time Series Insights:

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **criar um recurso**  >  **Internet das coisas**  >  **Time Series insights**:

    ![Novo Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Para criar seu ambiente do Time Series Insights no mesmo grupo de recursos do acelerador de solução, use os valores na tabela a seguir:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome do ambiente | A captura de tela a seguir usa o nome **Contoso-TSI**. Ao concluir esta etapa, escolha seu próprio nome exclusivo. |
    | Subscription | Na lista suspensa, selecione sua assinatura do Azure. |
    | Resource group | **contoso-simulation**. Use o nome do seu acelerador de solução. |
    | Localização | Este exemplo usa **Leste dos EUA**. Crie seu ambiente na mesma região que seu acelerador de simulação de dispositivo. |
    | Sku |**S1** |
    | Capacity | **1** |

    ![Criar Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Adicionar o ambiente do Time Series Insights ao mesmo grupo de recursos do acelerador de solução significa que ele é excluído quando você exclui o acelerador de solução.

1. Clique em **Criar**. Pode levar alguns minutos para o ambiente ser criado.

## <a name="create-event-source"></a>Criar uma origem de eventos

Crie uma nova fonte de evento para se conectar ao hub IoT. Use o grupo de consumidores criado nas etapas anteriores. A fonte de evento do Time Series Insights requer um grupo de consumidores dedicado que não esteja em uso por outro serviço.

1. No portal do Azure, navegue até o novo Ambiente do Time Series.

1. À esquerda, clique em **Origens de Eventos**:

    ![Exibir Origens do Evento](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Clique em **Adicionar**:

    ![Adicionar Origem do Evento](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Para configurar seu hub IoT como uma nova origem do evento, use os valores da tabela a seguir:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome da Origem do Evento | A captura de tela a seguir usa o nome **contoso-iot-hub**. Ao concluir esta etapa, use seu próprio nome exclusivo. |
    | Fonte | **Hub IoT** |
    | Importar opção | **Usar o Hub IoT nas assinaturas disponíveis** |
    | ID da assinatura | Na lista suspensa, selecione sua assinatura do Azure. |
    | Nome do Hub IoT | **contoso-simulation7d894**. Use o nome do hub IoT do seu acelerador de solução de simulação de dispositivo. |
    | Nome da política do hub IoT | **iothubowner** |
    | Chave de política do hub IoT | Este campo é preenchido automaticamente. |
    | Grupo de consumidores do hub IoT | **devicesimulationtsi** |
    | Formato de serialização do evento | **JSON** |
    | Nome da propriedade timestamp | Deixar em branco |

    ![Criar Origem do Evento](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Clique em **Criar**.

> [!NOTE]
> Você pode [conceder acesso a usuários adicionais](../time-series-insights/concepts-access-policies.md#grant-data-access) para o Time Series Insights explorer.

## <a name="start-a-simulation"></a>Iniciar uma simulação

Antes de usar o explorador do Time Series Insights, configure o acelerador de solução do Device Simulation para gerar alguma telemetria. A captura de tela a seguir mostra uma simulação em execução com 10 dispositivos do chiller:

![Simulação de dispositivos em execução](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

O explorador do Time Series Insights é um aplicativo da web que você pode usar para visualizar sua telemetria.

1. No portal do Azure, selecione a guia Visão Geral de Séries Temporais **Visão geral**.

1. Para abrir o aplicativo da web do explorador do Time Series Insights, clique em **Ir para o ambiente**:

    ![Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. No painel de seleção de tempo, selecione **Últimos 30 minutos** no menu de horários rápidos e clique em **Pesquisar**:

    ![Pesquisa do Gerenciador de análise de séries tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. No painel de termos à esquerda, selecione **temperature** como **Measure** e **iothub-connection-device-id** como o valor **Split By**:

    ![Captura de tela que mostra o Time Series Insights painel "termos", com os valores de "medida" e "divisão por" realçados.](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Clique com o botão direito do mouse no gráfico e selecione **explorar eventos**:

    ![Eventos do explorador do Insights do Time Series](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Os dados do evento são exibidos em uma grade:

    ![Tabela do explorador de Insights do Time Series](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Clique no botão de visualização em perspectiva:

    ![Perspectiva do explorador do Insights do Time Series](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Clique **+** para adicionar uma nova consulta à perspectiva:

    ![Explorador de Insights do Time Series Adicionar Consulta](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Selecione **Últimos 30 minutos** como o intervalo de tempo, **Umidade** como **Medida** e **iothub-connection-device-id** como o **Dividir por** valor:

    ![Consulta do Gerenciador de análise de séries de tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Clique no botão de vista em perspectiva para ver o painel de telemetria do dispositivo:

    ![Painel do Explorador de análise de séries de tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você planejar explorar ainda mais, deixe o acelerador de solução implantado.

Se você não precisar mais do Solution Accelerator, exclua-o da página [soluções provisionadas](https://www.azureiotsolutions.com/Accelerators#dashboard) , selecionando-o e, em seguida, clicando em **excluir solução**.

Se você tiver adicionado o ambiente do Time Series Insights ao grupo de recursos do acelerador de solução, ele será excluído automaticamente quando você excluir o acelerador de solução. Caso contrário, você deverá remover manualmente o ambiente do Time Series Insights do portal do Azure.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como explorar e consultar dados no explorador Estatísticas do Time Series, consulte [Explorador do Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).