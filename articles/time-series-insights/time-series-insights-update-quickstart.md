---
title: 'Início Rápido: Explore o ambiente de demonstração do Azure Time Series Insights Preview | Microsoft Docs'
description: Compreender o ambiente de demonstração do Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: dpalled
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: 547edb194ffdf52a805f573d338f61edff80623a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164874"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Início Rápido: Explorar o ambiente de demonstração do Azure Time Series Insights Preview

Este início rápido ajuda você a começar a usar o ambiente de Versão Prévia do Azure Time Series Insights. Na demonstração gratuita, você conhecerá os principais recursos adicionados à Versão Prévia do Time Series Insights.

O ambiente de demonstração da Versão Prévia do Time Series Insights contém uma empresa de exemplo, a Contoso, que opera dois parques eólicos. Cada parque tem 10 turbinas. Cada turbina tem 20 sensores que relatam dados a cada minuto para o Hub IoT do Azure. Os sensores coletam informações sobre as condições climáticas, o passo das pás e a posição da rotação sobre o eixo y. Também registram informações do desempenho do gerador, do comportamento das engrenagens e dos monitores de segurança.

Neste início rápido, você aprenderá a usar o Time Series Insights para descobrir insights acionáveis nos dados da Contoso. Você também realizará uma análise de causa raiz breve para prever melhor falhas críticas e realizar a manutenção.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Explore o Time Series Insights Explorer em um ambiente de demonstração

O Gerenciador da Versão Prévia do Time Series Insights demonstra a análise de causas raiz e dados históricos. Introdução:

1. Crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) se você não tiver uma.

1. Acesse o ambiente de  [demonstração da Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples).  

1. Se receber uma solicitação, entre no Gerenciador do Time Series Insights usando suas credenciais da conta do Azure.

## <a name="work-with-historical-data"></a>Trabalhar com os dados históricos

1. Na **Usina 1 da Contoso**, observe a turbina eólica **W7**.  

   1. Altere o intervalo de exibição para **1/1/17 20h a 10/3/17 20h (UTC)** .
   1. Para selecionar um sensor, selecione **Usina 1 da Contoso** > **W7** > **Sistema de Gerador** > **GeneratorSpeed**. Depois, examine os valores mostrados.

      [![W7 na Usina 1 da Contoso](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. Recentemente, a Contoso descobriu um incêndio em turbina eólica **W7**. As opiniões sobre o que causou o incêndio variam. No Time Series Insights, podemos ver que o sensor de alerta de incêndio foi ativado durante o incêndio.

   1. Altere o intervalo de exibição para **9/3/17 20h a 10/3/17 20h (UTC)** .
   1. Selecione **Sistema de Segurança** > **FireAlert**.

      [![A Contoso detectou um incêndio na turbina eólica W7](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. Examine outros eventos próximos à hora do incêndio para entender o que ocorreu. A pressão do óleo e os avisos ativos aumentaram repentinamente logo antes do incêndio.

   1. Selecione **Sistema de Regulagem do Passo** > **HydraulicOilPressure**.
   1. Selecione **Sistema de Regulagem do Passo** > **ActiveWarning**.

      [![Examinar outros eventos ocorridos próximos a uma hora específica](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. Os sensores de pressão do óleo e de avisos ativos aumentaram repentinamente logo antes do incêndio. Expanda a série temporal exibida para ver outros sinais evidentes que levaram ao incêndio. Os dois sensores flutuaram de forma consistente ao longo do tempo. As flutuações indicam um padrão persistente e preocupante.

    * Altere o intervalo de exibição para **24/2/17 20h a 10/3/17 20h (UTC)** .

      [![Os sensores de pressão do óleo e de avisos ativos também aumentaram repentinamente](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. Uma análise de dois anos de dados históricos revela outro evento de incêndio com as mesmas flutuações de sensor.

    * Altere o intervalo de exibição para **1/1/16 a 31/12/17** (todos os dados).

      [![Procurar padrões históricos](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

Usando o Time Series Insights e a telemetria de sensor, descobrimos uma tendência de longo prazo problemática ocultada nos dados históricos. Com esses novos insights, podemos:

> [!div class="checklist"]
> * Explicar o que realmente ocorreu.
> * Corrigir o problema.
> * Implementar sistemas superiores de notificação de alerta.

## <a name="root-cause-analysis"></a>Análise da causa raiz

1. Alguns cenários exigem uma análise sofisticada para revelar indicações sutis nos dados. Selecione o moinho de vento **W6** na data **25/6**.

    1. Altere o intervalo de exibição para **1/6/17 20h a 1/7/17 20h (UTC)** .
    1. Selecione o **Usina 1 da Contoso** > **W6** > **Sistema de Segurança** > **VoltageActuatorSwitchWarning**.

       [![Alterar o intervalo de exibição e selecionar W6](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. O aviso indica um problema com a tensão de saída do gerador. A saída de energia geral do gerador está operando dentro dos parâmetros normais no intervalo atual. Aumentando nosso intervalo, outro padrão surge. Há uma queda definida evidente.

    1. Remova o sensor **VoltageActuatorSwitchWarning**.
    1. Selecione **Sistema de Gerador** > **ActivePower**.
    1. Altere o intervalo para **3d**.

       [![Altere o intervalo para 3d](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. Expandindo o intervalo de tempo, podemos determinar se o problema foi resolvido ou se ele continua.

    * Estenda o período de tempo para 60 dias.

      [![Estender o período de tempo para 60 dias](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. Outros pontos de dados de sensor podem ser adicionados para fornecer um contexto superior. Quanto mais sensores vemos, maior é nossa compreensão do problema. Vamos criar um marcador para ver os valores reais. 

    1. Selecione **Sistema Gerador** e selecione três sensores: **GridVoltagePhase1**, **GridVoltagePhase2** e **GridVoltagePhase3**.
    1. Crie um marcador no último ponto de dados na área visível.

       [![Remover um marcador](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    Dois dos sensores de tensão estão funcionando comparativamente e dentro dos parâmetros normais. Parece que o sensor **GridVoltagePhase3** é o culpado.

1. Com a adição de dados altamente contextuais, parece ainda mais que a queda da fase 3 é o problema. Agora, temos uma boa pista sobre a causa do aviso. Estamos prontos para indicar o problema à nossa equipe de manutenção.  

    * Altere a exibição para sobrepor todos os sensores do **Sistema Gerador** na mesma escala de gráfico.

      [![Alterar a exibição para incluir tudo](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Você está pronto para criar seu próprio ambiente do Time Series Insights Preview. Para iniciar:

> [!div class="nextstepaction"]
> [Planejar o ambiente do Time Series Insights Preview](time-series-insights-update-plan.md)

Saiba como navegar pela demonstração e por seus recursos:

> [!div class="nextstepaction"]
> [O Gerenciador da Versão Prévia do Time Series Insights](time-series-insights-update-explorer.md)
