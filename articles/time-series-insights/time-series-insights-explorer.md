---
title: Explorar dados usando o Azure Time Series Insights explorer | Microsoft Docs
description: Este artigo descreve como usar o Azure Time Series Insights Explorer no navegador da Web para ver rapidamente uma exibição global do Big Data e validar o ambiente de IoT.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: a7905213d67fd1cb97a8e50c938427b7ad775924
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165784"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights Explorer

Este artigo descreve os recursos e opções de disponibilidade em geral para o Azure Time Series Insights [aplicativo web explorer](https://insights.timeseries.azure.com/). O Time Series Insights explorer demonstra os recursos de visualização de dados eficientes fornecidos pelo serviço e pode ser acessado em seu próprio ambiente.

O Azure Time Series Insights é um serviço totalmente gerenciado de análise, armazenamento e visualização que facilita a exploração e análise de bilhões de eventos de IoT simultaneamente. Ele fornece uma exibição global dos dados, o que permite validar rapidamente sua solução de IoT e evitar um tempo de inatividade dispendioso de dispositivos críticos. Descubra tendências ocultas, detecte anomalias e realize análises de causa raiz quase em tempo real. Atualmente, o Time Series Insights Explorer está em visualização pública.

> [!TIP]
> Para um tour guiado por meio do ambiente de demonstração, leia as [início rápido do Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Vídeo

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Saiba mais sobre como consultar dados usando o Time Series Insights explorer. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Consulte o vídeo anterior <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Introdução ao Time Series Insights usando um acelerador de solução de IoT do Azure."</a>

## <a name="prerequisites"></a>Pré-requisitos

Antes de usar o Time Series Insights Explorer, é necessário:

- Crie um ambiente do Time Series Insights. Para obter mais informações, consulte [como começar com o Time Series Insights](./time-series-insights-get-started.md).
- [Fornecer acesso](time-series-insights-data-access.md) à sua conta no ambiente.
- Adicionar um [hub IoT](time-series-insights-how-to-add-an-event-source-iothub.md) ou [hub de eventos](time-series-insights-how-to-add-an-event-source-eventhub.md) origem de evento.

## <a name="explore-and-query-data"></a>Explorar e consultar dados

Alguns minutos após conectar a origem do evento ao ambiente do Time Series Insights, você pode explorar e consultar seus dados de série temporal.

1. Para iniciar, abra o [Time Series Insights explorer](https://insights.timeseries.azure.com/) no navegador da web. No lado esquerdo da janela, selecione um ambiente. Todos os ambientes aos quais você tem acesso são listados em ordem alfabética.

1. Depois de selecionar um ambiente, usar o **partir** e **para** configurações na parte superior, ou clique e arraste sobre o período de tempo que você deseja. Selecione a Lupa no canto superior direito, ou clique duas vezes em que o período de tempo selecionado e selecione **pesquisa**.

1. Você também pode atualizar disponibilidade automaticamente a cada minuto, selecionando o **automático na** botão. O **automático na** botão só se aplica ao gráfico de disponibilidade, não o conteúdo da visualização principal.

1. O ícone de nuvem do Azure leva você ao seu ambiente no portal do Azure.

   [![Ambiente de análise de séries de tempo](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Em seguida, você verá um gráfico que mostra uma contagem de todos os eventos durante o período de tempo selecionado. Aqui, você tem uma série de controles:

    - **Painel do Editor de termos**: O espaço de termo é o local em que você consulta o ambiente. Ele é encontrado no lado esquerdo da tela:
      - **Medida**: Essa lista suspensa mostra todas as colunas numéricas (**duplos**).
      - **Dividir por**: Essa lista suspensa mostra colunas categóricas (**cadeias de caracteres**).
      - Habilite a interpolação escalonada, mostre o mínimo e máximo e ajuste o eixo y no painel de controle lado **medida**. Você também pode ajustar se os dados mostrados são uma contagem, média ou soma dos dados.
      - Você pode adicionar até cinco termos a serem exibidos no mesmo eixo x. Use o **cópia-down** botão para adicionar um termo adicional ou selecione **Add** para adicionar um novo termo.

        [![Painel do Editor de termos](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predicado**: Use o predicado para filtrar rapidamente os eventos usando o conjunto de operandos listados na tabela a seguir. Se você realizar uma pesquisa selecionando ou clicando em, o predicado automaticamente as atualizações com base em que a pesquisa. Os tipos de operando com suporte incluem:

         |Operação  |Tipos com suporte  |Observações  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, TimeSpan       |         |
         |`=`, `!=`, `<>`     | String, Bool, Double, DateTime, TimeSpan, NULL        |         |
         |IN     | String, Bool, Double, DateTime, TimeSpan, NULL        |  Todos os operandos devem ser do mesmo tipo ou ser uma constante NULL.        |
         |HAS     | Cadeia de caracteres        |  Literais de cadeia de caracteres constante só são permitidas no lado direito. Cadeia de caracteres vazia e NULL não são permitidas.       |

      - **Exemplos de consultas**

         [![Consultas de exemplo](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. Você pode usar o **tamanho do intervalo** ferramenta de controle deslizante para ampliar e reduzir intervalos sobre o mesmo período de tempo. O controle deslizante fornece controle mais preciso dos movimentos entre fatias de tempo grandes que mostram tendências suaves até fatias que são tão pequenas quanto milissegundos, que permitem a você ver recortes granulares e de alta resolução dos seus dados. Padrão do controle deslizante ponto de partida é definido como a exibição mais ideal dos dados da sua seleção para equilibrar a resolução, velocidade de consulta e granularidade.

1. O **pincel de tempo** ferramenta torna mais fácil de navegar de um intervalo de tempo para outra.

1. Use o **salvar** comando para salvar a consulta atual e compartilhá-lo com outros usuários do ambiente. Quando você usa **abrir**, você pode ver todas as consultas salvas e as consultas compartilhadas de outros usuários em ambientes que você tem acesso.

   [![Consultas](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Visualizar dados

1. Use o **exibição de perspectiva** ferramenta para uma exibição simultânea de até quatro consultas exclusivas. O **exibição de perspectiva** botão está no canto superior direito do gráfico.

   [![Exibição de perspectiva](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Exibir um gráfico para explorar seus dados visualmente e usar o **gráfico** ferramentas:

    - **Selecione** ou **clique em** um período de tempo específico ou uma única série de dados.
    - Dentro de uma seleção de intervalo de tempo, você pode ampliar ou explorar eventos.
    - Em uma série de dados, você pode dividir a série por outra coluna, adicionar a série como um novo termo, mostrar apenas a série selecionada, excluir a série selecionada, executar ping da série ou explorar eventos da série selecionada.
    - Na área de filtro à esquerda do gráfico, você pode ver todas as séries de dados exibidos e reordenar por nome ou valor. Você também pode exibir todas as séries de dados ou qualquer série fixada ou não fixada. Você pode selecionar uma série de dados único e dividir a série por outra coluna, adicione a série como um novo termo, mostrar apenas a série selecionada, exclua a série selecionada, fixe essa série ou explorar eventos da série selecionada.
    - Quando você exibir vários termos simultaneamente, você pode de pilha, desempilhe, consulte dados adicionais sobre uma série de dados e usar o mesmo eixo y em todos os termos. Use os botões no canto superior direito do gráfico.

    [![Ferramentas do gráfico](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Use o **mapa de calor** para identificar rapidamente séries de dados exclusivas ou anormais em determinada consulta. Apenas um termo de pesquisa pode ser visualizado como um mapa de calor.

    [![Mapa de calor](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. Quando você explorar eventos selecionando ou clicando duas vezes, o **eventos** painel se tornam disponível. Aqui, você pode ver todos os seus eventos brutos e exportá-los como arquivos JSON ou CSV. Time Series Insights armazena todos os dados brutos.

    [![Eventos](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Selecione o **STATS** guia depois de explorar eventos para expor os padrões e estatísticas de coluna.

    - **Padrões**: Esse recurso revela de maneira proativa os padrões estatisticamente significativos em uma região de dados selecionada. Você não precisa examinar milhares de eventos para entender quais padrões exigem mais tempo e energia. Time Series Insights, você pode ir diretamente para esses padrões estatisticamente significativos para continuar realizando uma análise. Esse recurso também é útil para investigações post-mortem em dados históricos.
    - **Estatísticas de Coluna**: Estatísticas de coluna fornecem gráficos e tabelas que dividem os dados de cada coluna da série de dados selecionada sobre o período de tempo selecionado.

      [![STATS](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Agora você viu os vários recursos e opções que estão disponíveis no aplicativo da web do Time Series Insights explorer.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [diagnosticar e resolver problemas](time-series-insights-diagnose-and-solve-problems.md) em seu ambiente do Time Series Insights.
- Levar o guiada [início rápido do Azure Time Series Insights](time-series-quickstart.md) tour.
