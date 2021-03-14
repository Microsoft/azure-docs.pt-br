---
title: Estender IoT Central do Azure com análise personalizada | Microsoft Docs
description: Como desenvolvedor de soluções, configure um aplicativo IoT Central para realizar análises e visualizações personalizadas. Essa solução usa Azure Databricks.
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 02/18/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 11e5ba3c0700cc9b29b8a11c0f9aa20cb5adb132
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551310"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Estenda o IoT Central do Azure com análises personalizadas usando Azure Databricks

Este guia de instruções mostra, como desenvolvedor de soluções, como estender seu aplicativo IoT Central com análises e visualizações personalizadas. O exemplo usa um espaço de trabalho [Azure Databricks](/azure/azure-databricks/) para analisar o fluxo de telemetria IOT central e gerar visualizações como [plotagens de caixa](https://wikipedia.org/wiki/Box_plot).  

Este guia de instruções mostra como estender IoT Central além do que ele já pode fazer com as ferramentas de [análise internas](./howto-create-custom-analytics.md).

Neste guia de instruções, você aprende a:

* Transmita a telemetria de um aplicativo IoT Central usando a *exportação de dados contínuas*.
* Crie um ambiente de Azure Databricks para analisar e plotar a telemetria do dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de instruções, é necessário ter uma assinatura ativa do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

### <a name="iot-central-application"></a>Aplicativo IoT Central

Crie um aplicativo IoT Central no site do [Azure IOT central Application Manager](https://aka.ms/iotcentral) com as seguintes configurações:

| Configuração | Valor |
| ------- | ----- |
| Plano de preços | Standard |
| Modelo de aplicativo | Análise na loja – monitoramento de condição |
| Nome do aplicativo | Aceite o padrão ou escolha seu próprio nome |
| URL | Aceite o padrão ou escolha seu próprio prefixo de URL exclusivo |
| Diretório | Seu locatário Azure Active Directory |
| Assinatura do Azure | Sua assinatura do Azure |
| Região | Sua região mais próxima |

Os exemplos e capturas de tela neste artigo usam a região **Estados Unidos** . Escolha um local perto de você e certifique-se de criar todos os seus recursos na mesma região.

Este modelo de aplicativo inclui dois dispositivos termostato simulados que enviam telemetria.

### <a name="resource-group"></a>Resource group

Use o [portal do Azure para criar um grupo de recursos](https://portal.azure.com/#create/Microsoft.ResourceGroup) chamado **IoTCentralAnalysis** para conter os outros recursos que você criar. Crie seus recursos do Azure no mesmo local que o aplicativo IoT Central.

### <a name="event-hubs-namespace"></a>Namespace do Hubs de Eventos

Use o [portal do Azure para criar um namespace de hubs de eventos](https://portal.azure.com/#create/Microsoft.EventHub) com as seguintes configurações:

| Configuração | Valor |
| ------- | ----- |
| Nome    | Escolha o nome do namespace |
| Tipo de preço | Basic |
| Subscription | Sua assinatura |
| Resource group | IoTCentralAnalysis |
| Localização | Leste dos EUA |
| Unidades de produtividade | 1 |

### <a name="azure-databricks-workspace"></a>Espaço de trabalho Azure Databricks

Use o [portal do Azure para criar um serviço de Azure Databricks](https://portal.azure.com/#create/Microsoft.Databricks) com as seguintes configurações:

| Configuração | Valor |
| ------- | ----- |
| Nome do workspace    | Escolha o nome do seu espaço de trabalho |
| Subscription | Sua assinatura |
| Resource group | IoTCentralAnalysis |
| Localização | Leste dos EUA |
| Camada de preços | Standard |

Quando você criou os recursos necessários, o grupo de recursos **IoTCentralAnalysis** é semelhante à captura de tela a seguir:

![Grupo de recursos de análise de IoT Central](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Criar um Hub de Evento

Você pode configurar um aplicativo de IoT Central para exportar continuamente a telemetria para um hub de eventos. Nesta seção, você cria um hub de eventos para receber telemetria do seu aplicativo IoT Central. O Hub de eventos fornece a telemetria para seu trabalho de Stream Analytics para processamento.

1. Na portal do Azure, navegue até o namespace de seus hubs de eventos e selecione **+ Hub de eventos**.
1. Nomeie o **centralexport** do hub de eventos e selecione **criar**.
1. Na lista de hubs de eventos em seu namespace, selecione **centralexport**. Em seguida, escolha **políticas de acesso compartilhado**.
1. Selecione **+ Adicionar**. Crie uma política chamada **Listen** com a Declaração **Listen** .
1. Quando a política estiver pronta, selecione-a na lista e copie o valor da **chave primária da cadeia de conexão** .
1. Anote essa cadeia de conexão, você a usará mais tarde quando configurar o bloco de anotações do databricks para ler do hub de eventos.

O namespace dos hubs de eventos é semelhante à captura de tela a seguir:

![Namespace do Hubs de Eventos](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Configurar a exportação no IoT Central

No site do [Azure IOT central Application Manager](https://aka.ms/iotcentral) , navegue até o aplicativo IOT central que você criou por meio do modelo da contoso. Nesta seção, você configura o aplicativo para transmitir a telemetria de seus dispositivos simulados para o Hub de eventos. Para configurar a exportação:

1. Navegue até a página **exportação de dados** , selecione **+ novo** e os **hubs de eventos do Azure**.
1. Use as configurações a seguir para configurar a exportação e, em seguida, selecione **salvar**:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome de Exibição | Exportar para hubs de eventos |
    | habilitado | Ativado |
    | Namespace do Hubs de Eventos | Nome do namespace de seus hubs de eventos |
    | Hub de Eventos | centralexport |
    | Medidas | Ativado |
    | Dispositivos | Desativado |
    | Modelos de Dispositivo | Desativado |

![Configuração de exportação de dados](media/howto-create-custom-analytics/cde-configuration.png)

Aguarde até que o status de exportação seja **executado** antes de continuar.

## <a name="configure-databricks-workspace"></a>Configurar o espaço de trabalho do databricks

Na portal do Azure, navegue até o serviço de Azure Databricks e selecione **Iniciar espaço de trabalho**. Uma nova guia é aberta no navegador e você entra no espaço de trabalho.

### <a name="create-a-cluster"></a>Criar um cluster

Na página **Azure Databricks** , na lista de tarefas comuns, selecione **novo cluster**.

Use as informações na tabela a seguir para criar o cluster:

| Configuração | Valor |
| ------- | ----- |
| Nome do cluster | centralanalysis |
| Modo de cluster | Standard |
| Versão do Databricks Runtime | 5,5 LTS (escala 2,11, Spark 2.4.5) |
| Versão do Python | 3 |
| Habilitar o dimensionamento automático | Não |
| Terminar após minutos de inatividade | 30 |
| Tipo de trabalho | Standard_DS3_v2 |
| Trabalhos | 1 |
| Tipo de driver | Igual ao trabalho |

A criação de um cluster pode levar vários minutos, aguarde a conclusão da criação do cluster antes de continuar.

### <a name="install-libraries"></a>Instalar bibliotecas

Na página **clusters** , aguarde até que o estado do cluster esteja **em execução**.

As etapas a seguir mostram como importar a biblioteca que seu exemplo precisa para o cluster:

1. Na página **clusters** , aguarde até que o estado do cluster interativo **Centralanalysis** esteja **em execução**.

1. Selecione o cluster e, em seguida, escolha a guia **bibliotecas** .

1. Na guia **bibliotecas** , escolha **instalar novo**.

1. Na página **instalar biblioteca** , escolha **Maven** como a origem da biblioteca.

1. Na caixa de texto **coordenadas** , insira o seguinte valor: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Escolha **instalar** para instalar a biblioteca no cluster.

1. O status da biblioteca agora está **instalado**:

    ![Biblioteca instalada](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Importar um bloco de anotações do databricks

Use as etapas a seguir para importar um bloco de anotações do databricks que contém o código Python para analisar e visualizar sua telemetria de IoT Central:

1. Navegue até a página do **espaço de trabalho** em seu ambiente do databricks. Selecione o menu suspenso ao lado do nome da sua conta e escolha **importar**.

1. Escolha Importar de uma URL e insira o seguinte endereço: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Para importar o bloco de anotações, escolha **importar**.

1. Selecione o **espaço de trabalho** para exibir o bloco de anotações importado:

    ![Bloco de anotações importado](media/howto-create-custom-analytics/import-notebook.png)

1. Edite o código na primeira célula do Python para adicionar a cadeia de conexão dos hubs de eventos que você salvou anteriormente:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Executar análise

Para executar a análise, você deve anexar o bloco de anotações ao cluster:

1. Selecione **desanexado** e, em seguida, selecione o cluster **centralanalysis** .
1. Se o cluster não estiver em execução, inicie-o.
1. Para iniciar o bloco de anotações, selecione o botão Executar.

Você pode ver um erro na última célula. Nesse caso, verifique se as células anteriores estão em execução, aguarde um minuto para que alguns dados sejam gravados no armazenamento e, em seguida, execute a última célula novamente.

### <a name="view-smoothed-data"></a>Exibir dados suavizados

No bloco de anotações, role para baixo até a célula 14 para ver uma plotagem da umidade média móvel por tipo de dispositivo. Essa plotagem é atualizada continuamente à medida que a telemetria de streaming chega:

![Gráfico de telemetria suavizada](media/howto-create-custom-analytics/telemetry-plot.png)

Você pode redimensionar o gráfico no bloco de anotações.

### <a name="view-box-plots"></a>Exibir gráficos de caixa

No bloco de anotações, role para baixo até a célula 20 para ver as [plotagens da caixa](https://en.wikipedia.org/wiki/Box_plot). As plotagens de caixa são baseadas em dados estáticos para que atualizá-los, você deve executar novamente a célula:

![Plotagens de caixa](media/howto-create-custom-analytics/box-plots.png)

Você pode redimensionar as plotagens no bloco de anotações.

## <a name="tidy-up"></a>Limpar

Para fazer a limpeza após este procedimento e evitar custos desnecessários, exclua o grupo de recursos **IoTCentralAnalysis** no portal do Azure.

Você pode excluir o aplicativo IoT Central da página de **Gerenciamento** dentro do aplicativo.

## <a name="next-steps"></a>Próximas etapas

Neste guia de instruções, você aprendeu como:

* Transmita a telemetria de um aplicativo IoT Central usando a *exportação de dados contínuas*.
* Crie um ambiente de Azure Databricks para analisar e plotar dados de telemetria.

Agora que você sabe como criar análises personalizadas, a próxima etapa sugerida é aprender a [Visualizar e analisar seus dados de IOT central do Azure em um painel de Power bi](howto-connect-powerbi.md).