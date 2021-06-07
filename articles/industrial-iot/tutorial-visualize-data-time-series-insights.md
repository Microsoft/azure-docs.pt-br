---
title: Visualizar dados do OPC UA no Azure Time Series Insights
description: Neste tutorial, você aprenderá a visualizar dados com o Time Series Insights.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 5bd218c0d94922b6137a964e3993f516216ca4b7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787181"
---
# <a name="tutorial-visualize-data-with-time-series-insights-tsi"></a>Tutorial: Visualizar dados com o TSI (Time Series Insights)

O módulo do OPC Publisher se conecta a servidores do OPC UA e publica dados desses servidores no Hub IoT. O processador de Telemetria na plataforma de IoT Industrial processa esses eventos e encaminha amostras contextuais para o TSI e para outros consumidores.  

Este guia de instruções mostra como visualizar e analisar a Telemetria do OPC UA usando esse ambiente do Time Series Insights.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Todos os tutoriais incluem uma lista que resume as etapas para conclusão
> * Cada um desses tópicos está alinhado a uma chave H2
> * Use essas caixas de seleção verdes em um tutorial

## <a name="prerequisite"></a>Pré-requisito

* Implantar a Plataforma de IIoT para que um ambiente do Time Series Insights seja criado automaticamente
* Os dados estão sendo publicados no Hub IoT

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

O explorador do Time Series Insights é um aplicativo da web que você pode usar para visualizar sua telemetria. Para recuperar a URL do aplicativo, abra o arquivo `.env` salvo como resultado da implantação.  Abra um navegador para a URL na variável `PCS_TSI_URL`.  

Antes de usar o Gerenciador do Time Series Insights, você precisa permitir acesso aos dados do TSI aos usuários com direito de visualizá-los. Observe que, em uma nova implantação, nenhuma política de acesso a dados é definida por padrão e, portanto, ninguém pode ver os dados. As políticas de acesso a dados precisam ser definidas no portal do Azure, no ambiente do Time Series Insights implantado no grupo de recursos implantado da plataforma de IIoT, da seguinte maneira:

   ![Gerenciador do Time Series Insights 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-1.png)

Selecione as Políticas de Acesso a Dados:

   ![Gerenciador do Time Series Insights 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-2.png)

Atribua os usuários necessários:

   ![Gerenciador do Time Series Insights 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-3.png)


No Gerenciador do TSI, observe as Instâncias de Série Temporal Não Atribuídas. Uma Instância de TSI corresponde à série de tempo/valor de um ponto de dados específico originado de um nó publicado em um servidor OPC. A instância de TSI, com relação ao ponto de dados OPC UA, é identificada exclusivamente por EndpointId, SubscriptionId e NodeId. Os modelos de instâncias do TSI são detectados automaticamente e exibidos no gerenciador com base nos dados de telemetria ingeridos do hub de eventos do processador de telemetria da plataforma de IIoT.

   ![Gerenciador do Time Series Insights 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-0.png)

Os dados telemétricos podem ser visualizados no gráfico clicando com o botão direito do mouse na instância do TSI e selecionando o valor. O período a ser usado no gráfico pode ser ajustado no canto superior direito. O valor de várias instâncias pode ser visualizado na mesma seleção baseada no tempo.

Para obter mais informações, confira [Início Rápido: Explorar a versão prévia do Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)

## <a name="define-and-apply-a-new-model"></a>Definir e aplicar um novo modelo

Como as instâncias de telemetria estão apenas no formato bruto, elas precisam ser contextualizadas com as 

Para obter informações detalhadas sobre os modelos de TSI, confira [Modelo de série temporal na versão prévia do Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)

1. Etapa 1 – Na guia de modelo do Gerenciador, defina uma nova hierarquia para os dados telemétricos ingeridos. Uma hierarquia é a estrutura de árvore lógica destinada a permitir que o usuário insira as meta-informações necessárias para uma navegação mais intuitiva por meio das instâncias do TSI. Um usuário pode criar/excluir/modificar modelos de hierarquia que podem, posteriormente, ser instanciados para as várias instâncias do TSI.

   ![Etapa 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-1.png)

2. Etapa 2 – Definir um novo tipo para os valores. Em nosso exemplo, tratamos apenas de tipos de dados numéricos

   ![Etapa 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-2.png)

3. Etapa 3 – Selecionar a nova instância do TSI que precisa ser categorizada na hierarquia definida anteriormente

   ![Etapa 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-3.png)

4. Etapa 4 – Preencher as propriedades das instâncias – nome, descrição, valor de dados, bem como os campos de hierarquia, para corresponder à estrutura lógica 

   ![Etapa 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-4.png)

5. Etapa 5 – Repetir a etapa 5 para todas as instâncias do TSI não categorizadas

   ![Etapa 5](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-5.png)

6. Etapa 6 – De volta à página principal do Gerenciador do TSI, percorrer a hierarquia de instâncias categorizadas e selecionar os valores dos pontos de dados a serem analisados

   ![Etapa 6](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-6.png)

## <a name="connect-time-series-insights-to-power-bi"></a>Conectar o Time Series Insights ao Power BI

Você também pode conectar o ambiente do Time Series Insights ao Power BI.  Para obter mais informações, confira [Como conectar o TSI ao Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) e [Visualizar dados do TSI no Power BI](https://docs.microsoft.com/azure/time-series-insights/concepts-power-bi).


## <a name="next-steps"></a>Próximas etapas
Agora que aprendeu a visualizar os dados no TSI, você pode conferir o repositório GitHub da IoT Industrial:

> [!div class="nextstepaction"]
> [Repositório GitHub da Plataforma de IIoT](https://github.com/Azure/iot-edge-opc-publisher)