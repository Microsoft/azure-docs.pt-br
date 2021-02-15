---
title: Depurar Azure Stream Analytics consultas localmente usando o diagrama de trabalho no Visual Studio Code
description: Este artigo descreve como depurar consultas localmente usando o diagrama de trabalho na extensão Azure Stream Analytics para Visual Studio Code.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 25ad696ad345fbf672f6bf26eb3f35a13fb03ea5
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019492"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio-code"></a>Depurar Azure Stream Analytics consultas localmente usando o diagrama de trabalho no Visual Studio Code

Os trabalhos de streaming que geram nenhum resultado ou resultados inesperados muitas vezes precisam de solução de problemas. A extensão de Visual Studio Code para Azure Stream Analytics integra diagramas de trabalho, métricas, logs de diagnóstico e resultados intermediários para ajudá-lo a isolar rapidamente a origem de um problema. Você pode usar o diagrama de trabalho ao testar sua consulta localmente para examinar o conjunto de resultados intermediário e as métricas para cada etapa.

## <a name="debug-a-query-using-job-diagram"></a>Depurar uma consulta usando o diagrama de trabalho

Um script Azure Stream Analytics é usado para transformar dados de entrada para dados de saída. O diagrama de trabalho mostra como os dados fluem de fontes de entrada, como hub de eventos ou Hub IoT, por meio de várias etapas de consulta para gerar coletores de saída. Cada etapa de consulta é mapeada para um conjunto de resultados temporário definido no script usando uma `WITH` instrução. Você pode exibir os dados, bem como as métricas de cada etapa de consulta em cada conjunto de resultados intermediários para localizar a origem de um problema.

> [!NOTE]
> Esse diagrama de trabalho mostra apenas os dados e as métricas para teste local em um único nó. Ele não deve ser usado para ajuste de desempenho e solução de problemas.

### <a name="start-local-testing"></a>Iniciar teste local

Use este guia de [início rápido](quick-create-visual-studio-code.md) para aprender a criar um trabalho de Stream Analytics usando Visual Studio Code ou [exportar um trabalho existente para um projeto local](visual-studio-code-explore-jobs.md). As credenciais para entradas e saídas são preenchidas automaticamente para trabalhos exportados.

Se você quiser testar a consulta com dados de entrada locais, siga estas [instruções](visual-studio-code-local-run.md). Se você quiser testar com a entrada ao vivo, configure a mudança de [entrada](stream-analytics-add-inputs.md) para a próxima etapa. 

Abra o arquivo de script *\. asaql* e selecione **executar localmente**. Em seguida, selecione **usar entrada local** ou **usar entrada ao vivo**. O diagrama de trabalho aparece no lado direito da janela.

### <a name="view-the-output-and-intermediate-result-set"></a>Exibir a saída e o conjunto de resultados intermediário  

1. Todas as saídas de trabalho são exibidas na janela resultado no canto inferior direito da janela Visual Studio Code.

   > [!div class="mx-imgBorder"]
   > ![Resultados da saída do trabalho](./media/debug-locally-using-job-diagram-vs-code/job-output-results.png)

2. Selecione a etapa de consulta para navegar até o script. Você é direcionado automaticamente para o script correspondente no editor à esquerda. O resultado intermediário é exibido na janela resultado no canto inferior direito da janela Visual Studio Code.

   > [!div class="mx-imgBorder"]
   > ![Resultado da visualização do diagrama de trabalho](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

### <a name="view-metrics"></a>Métricas de exibição

Nesta seção, você explora as métricas disponíveis para cada parte do diagrama.

1. Selecione a guia **métricas** ao lado da guia **resultado** no lado inferior direito da janela de Visual Studio Code.

2. Selecione **trabalho** na lista suspensa. Você pode selecionar qualquer espaço vazio em um nó do grafo para navegar até as métricas de nível de trabalho. Essa exibição contém todas as métricas, que são atualizadas a cada 10 segundos quando o trabalho está em execução. Você pode marcar ou desmarcar as métricas no lado direito para exibi-las nos gráficos.

   > [!div class="mx-imgBorder"]
   > ![Métricas do diagrama de trabalho](./media/debug-locally-using-job-diagram-vs-code/job-metrics.png)

3. Selecione o nome da fonte de dados de entrada no menu suspenso para ver as métricas de entrada. A fonte de entrada na captura de tela abaixo é chamada de *aspas*. Para obter mais informações sobre as métricas de entrada, consulte [entender Stream Analytics monitoramento de trabalho e como monitorar consultas](stream-analytics-monitoring.md).

   > [!div class="mx-imgBorder"]
   > ![Métricas de entrada do diagrama de trabalho](./media/debug-locally-using-job-diagram-vs-code/input-metrics.png)

4. Selecione uma etapa de consulta no diagrama de trabalho ou selecione o nome da etapa na lista suspensa para ver as métricas de nível de etapa. O atraso da marca d' água é a única métrica de etapa disponível.

   > [!div class="mx-imgBorder"]
   > ![Métricas da etapa](./media/debug-locally-using-job-diagram-vs-code/step-metrics.png)

5. Selecione uma saída no diagrama ou na lista suspensa para ver as métricas relacionadas à saída. Para obter mais informações sobre as métricas de saída, consulte [entender Stream Analytics monitoramento de trabalho e como monitorar consultas](stream-analytics-monitoring.md). Não há suporte para coletores de saída ao vivo.

   > [!div class="mx-imgBorder"]
   > ![Métricas de saída](./media/debug-locally-using-job-diagram-vs-code/output-metrics.png)

### <a name="view-diagnostic-logs"></a>Exibir logs de diagnóstico

Os logs de diagnóstico de nível de trabalho contêm informações de diagnóstico para fontes de dados de entrada e coletores de saída. Quando você seleciona um nó de entrada ou um nó de saída, somente os logs correspondentes são mostrados. Não há logs mostrados se você selecionar uma etapa de consulta. Você pode encontrar todos os logs no nível do trabalho e pode filtrar os logs por severidade e hora.

   > [!div class="mx-imgBorder"]
   > ![Logs de diagnóstico](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs.png)

   Selecione uma entrada de log para ver a mensagem inteira.

   > [!div class="mx-imgBorder"]
   > ![Mensagem dos logs de diagnóstico](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs-message.png)


## <a name="other-job-diagram-features"></a>Outros recursos de diagrama de trabalho

Você pode selecionar **parar** ou **Pausar** na barra de ferramentas, conforme necessário. Depois que o trabalho for pausado, você poderá retomá-lo da última saída.

> [!div class="mx-imgBorder"]
> ![Parar ou pausar trabalho](./media/debug-locally-using-job-diagram-vs-code/stop-pause-job.png)

Selecione **Resumo do trabalho** na parte superior direita do diagrama de trabalho para ver as propriedades e configurações do seu trabalho local.

> [!div class="mx-imgBorder"]
> ![Resumo do trabalho local](./media/debug-locally-using-job-diagram-vs-code/job-summary.png)

## <a name="limitations"></a>Limitações

* Não há suporte para coletores de saída ao vivo na execução local.

* Executar o trabalho localmente com a função JavaScript só tem suporte no sistema operacional Windows.

* Não há suporte para o código personalizado C# e funções de Azure Machine Learning. 

* Somente as opções de entrada de nuvem têm suporte às [políticas de tempo](./stream-analytics-time-handling.md) , enquanto as opções de entrada locais não.

## <a name="next-steps"></a>Próximas etapas

* [Início rápido: criar um trabalho de Stream Analytics usando Visual Studio Code](quick-create-visual-studio-code.md)
* [Explorar Azure Stream Analytics com Visual Studio Code](visual-studio-code-explore-jobs.md)
* [Testar consultas do Stream Analytics localmente com os dados de exemplo usando o Visual Studio Code](visual-studio-code-local-run.md)
* [Testar Azure Stream Analytics trabalhos localmente com a entrada ao vivo usando Visual Studio Code](visual-studio-code-local-run-live-input.md)