---
title: Explorar dados usando o Gerenciador-Azure Time Series Insights | Microsoft Docs
description: Saiba como usar o Azure Time Series Insights Explorer para exibir seus dados de IoT.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 639f2ef12d190a56e04a9b48d96ea0a6537d243c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020105"
---
# <a name="azure-time-series-insights-gen1-explorer"></a>Azure Time Series Insights Explorer do Gen1

> [!CAUTION]
> Esse é um artigo do Gen1.

Este artigo descreve os recursos e as opções para o Azure Time Series Insights [aplicativo Web](https://insights.timeseries.azure.com/)do Gen1 Explorer. O Azure Time Series Insights Explorer demonstra os poderosos recursos de visualização de dados fornecidos pelo serviço e podem ser acessados em seu próprio ambiente.

O Azure Time Series Insights é um serviço totalmente gerenciado de análise, armazenamento e visualização que facilita a exploração e análise de bilhões de eventos de IoT simultaneamente. Ele fornece uma exibição global dos dados, o que permite validar rapidamente sua solução de IoT e evitar um tempo de inatividade dispendioso de dispositivos críticos. Descubra tendências ocultas, detecte anomalias e realize análises de causa raiz quase em tempo real.

> [!TIP]
> Para obter um tour guiado por meio do ambiente de demonstração, leia o guia de [início rápido do Azure Time Series insights](time-series-quickstart.md).

## <a name="video"></a>Vídeo

### <a name="learn-about-querying-data-by-using-the-azure-time-series-insights-explorer-br"></a>Saiba mais sobre como consultar dados usando o Azure Time Series Insights Explorer. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Reproduza o vídeo anterior <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"introdução ao Azure Time Series insights usando um acelerador de solução do Azure IOT".</a>

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder usar o Azure Time Series Insights Explorer, você deve:

- Criar um ambiente do Azure Time Series Insights. Para obter mais informações, leia como começar a [usar o Azure Time Series insights](./time-series-insights-get-started.md).
- [Forneça acesso](./concepts-access-policies.md) à sua conta no ambiente.
- Adicione um [Hub IOT](./how-to-ingest-data-iot-hub.md) ou origem do evento do [Hub de eventos](./how-to-ingest-data-event-hub.md) a ele.

## <a name="explore-and-query-data"></a>Explorar e consultar dados

Em minutos de conexão da origem do evento ao seu ambiente de Azure Time Series Insights, você pode explorar e consultar seus dados de série temporal.

1. Para iniciar, abra o [Azure Time Series insights Explorer](https://insights.timeseries.azure.com/) em seu navegador da Web. No lado esquerdo da janela, selecione um ambiente. Todos os ambientes aos quais você tem acesso são listados em ordem alfabética.

1. Depois de selecionar um ambiente, use as configurações **de** e **para** na parte superior ou selecione e arraste sobre o período desejado. Selecione a lupa no canto superior direito ou clique com o botão direito do mouse no período de tempo selecionado e selecione **Pesquisar**.

1. Você também pode atualizar a disponibilidade automaticamente a cada minuto selecionando o botão **automático** . O botão **auto on** só se aplica ao gráfico de disponibilidade, não ao conteúdo da visualização principal.

1. O ícone de nuvem do Azure leva você ao seu ambiente no portal do Azure.

   [![Seleção de ambiente de Azure Time Series Insights](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Em seguida, um gráfico que mostra uma contagem de todos os eventos durante o período de tempo selecionado é exibido. Aqui, você tem uma série de controles:

    - **Painel de termos do editor**: o termo espaço é onde você consulta seu ambiente. Ele é encontrado no lado esquerdo da tela:
      - **Medida**: essa lista suspensa mostra todas as colunas numéricas (**duplas**).
      - **Dividir por**: essa lista suspensa mostra colunas categóricas (**cadeias de caracteres**).
      - Você pode habilitar a interpolação de etapa, mostrar mínimo e máximo e ajustar o eixo y do painel de controle ao lado de **medida**. Você também pode ajustar se os dados mostrados são uma contagem, média ou soma dos dados.
      - Você pode adicionar até cinco termos para exibir no mesmo eixo x. Selecione **Adicionar** para adicionar um termo atualizado ou use o botão **clonar este termo** para adicionar uma cópia de um termo existente.

        [![Painel de seleção de termos, filtragem e consulta](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predicado**: Use o predicado para filtrar rapidamente seus eventos usando o conjunto de operandos listados na tabela a seguir. Se você realizar uma pesquisa selecionando ou clicando em, o predicado é atualizado automaticamente com base nessa pesquisa. Os tipos de operando com suporte incluem:

         |Operação  |Tipos com suporte  |Observações  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Double**, **DateTime**, **TimeSpan**       |         |
         |**=**, **!=**, **<>**     | **Cadeia de caracteres**, **bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |         |
         |**IN**     | **Cadeia de caracteres**, **bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |  Todos os operandos devem ser do mesmo tipo ou ser uma constante **nula** .        |
         |**HAS**     | **Cadeia de caracteres**        |  Somente literais de cadeia de caracteres constantes são permitidos no lado direito. Cadeia de caracteres vazia e **NULL** não são permitidos.       |

      - **Consultas de exemplo**

         [![Exemplos de consultas Gen1](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. Você pode usar a ferramenta controle deslizante de **tamanho do intervalo** para ampliar e reduzir intervalos no mesmo período de tempo. O controle deslizante fornece um controle mais preciso da movimentação entre grandes fatias de tempo que mostram tendências suaves para fatias tão pequenas quanto o milissegundo, que permitem exibir e analisar os cortes granulares de alta resolução dos seus dados. O ponto de partida padrão do controle deslizante é definido como a exibição ideal dos dados de sua seleção para balancear a resolução, a velocidade da consulta e a granularidade.

1. A ferramenta de **pincel de tempo** facilita a navegação de um TimeSpan para outro.

1. Selecione o ícone **salvar** para salvar a consulta atual e compartilhá-la com outros usuários do ambiente. Ao selecionar o ícone **abrir** , você pode examinar todas as suas consultas salvas e todas as consultas compartilhadas de outros usuários em ambientes aos quais você tem acesso.

   [![Consultas](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Visualizar dados

1. Use a ferramenta de **exibição de perspectiva** para uma exibição simultânea de até quatro consultas exclusivas. O botão **exibição de perspectiva** está no canto superior direito do gráfico.

   [![Selecionar consultas para adicionar ao painel de perspectiva](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Exiba um gráfico para explorar visualmente seus dados e use as ferramentas de **gráfico** :

    - **Selecione** ou **clique em** um período específico ou em uma única série de dados.
    - Em uma seleção de TimeSpan, você pode aplicar zoom ou explorar eventos.
    - Em uma série de dados, você pode dividir a série por outra coluna, adicionar a série como um novo termo, mostrar apenas a série selecionada, excluir a série selecionada, executar ping da série ou explorar eventos da série selecionada.
    - Na área de filtro à esquerda do gráfico, você pode examinar todas as séries de dados exibidas e reordená-las por valor ou nome. Você também pode exibir todas as séries de dados ou qualquer série fixa ou desafixada. Você pode selecionar uma única série de dados e dividir a série por outra coluna, adicionar a série como um novo termo, mostrar apenas a série selecionada, excluir a série selecionada, fixar a série ou explorar eventos da série selecionada.
    - Ao exibir vários termos simultaneamente, você pode empilhar, desempilhar, examinar dados adicionais sobre uma série de dados e usar o mesmo eixo y em todos os termos. Use os botões no canto superior direito do gráfico.

    [![Configurações da opção de canto superior direito da ferramenta de gráfico](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Use o **calor** para identificar rapidamente séries de dados exclusivas ou anormais em uma determinada consulta. Apenas um termo de pesquisa pode ser visualizado como um mapa de calor.

    [Time Series insights calor Explorer](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png) (Media/Time-Series-percepções-Explorer/tsi-ga-example-heatmap-charting.png # Lightbox)

1. Quando você explora eventos selecionando ou clicando com o botão direito do mouse, o painel **eventos** é disponibilizado. Aqui, você pode examinar todos os seus eventos brutos e exportar seus eventos como arquivos JSON ou CSV. Azure Time Series Insights armazena todos os dados brutos.

    [![Eventos](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Selecione a guia **estatísticas** depois de explorar eventos para expor padrões e estatísticas de coluna.

    - **Padrões**: esse recurso mostra proativamente os padrões mais significativos estatisticamente em uma região de dados selecionada. Você não precisa examinar milhares de eventos para entender quais padrões exigem mais tempo e energia. Com Azure Time Series Insights, você pode ir diretamente para esses padrões estatisticamente significativos para continuar realizando uma análise. Esse recurso também é útil para investigações post-mortem em dados históricos.
    - **Estatísticas de coluna**: estatísticas de coluna fornecem gráficos e tabelas que dividem dados de cada coluna da série de dados selecionada durante o período selecionado.

      [![Opções e gráfico de coluna de estatísticas](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Agora você aprendeu sobre os principais recursos, definições de configuração e opções de exibição que estão disponíveis no aplicativo Web Azure Time Series Insights Explorer.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [diagnosticar e resolver problemas](time-series-insights-diagnose-and-solve-problems.md) em seu ambiente de Azure Time Series insights.

- Faça o Tour de [início rápido Azure Time Series insights](time-series-quickstart.md) guiado.