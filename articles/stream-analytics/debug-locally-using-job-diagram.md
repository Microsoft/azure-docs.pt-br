---
title: Depurar Azure Stream Analytics consultas localmente usando o diagrama de trabalho no Visual Studio
description: Este artigo descreve como depurar consultas localmente usando o diagrama de trabalho no Azure Stream Analytics Tools para Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: d0e94fda1fb21be1a01516f4cecf657426ae867e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019441"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Depurar Azure Stream Analytics consultas localmente usando o diagrama de trabalho no Visual Studio

Trabalhos que não geram nenhum resultado ou resultados inesperados são cenários comuns de solução de problemas para consultas de streaming. Você pode usar o diagrama de trabalho ao testar sua consulta localmente no Visual Studio para examinar o conjunto de resultados intermediário e as métricas para cada etapa. Os diagramas de trabalho podem ajudá-lo a isolar rapidamente a origem de um problema ao solucionar problemas.

## <a name="debug-a-query-using-job-diagram"></a>Depurar uma consulta usando o diagrama de trabalho

Um script Azure Stream Analytics é usado para transformar dados de entrada para dados de saída. O diagrama de trabalho mostra como os dados fluem de fontes de entrada (Hub de eventos, Hub IoT etc.) por meio de várias etapas de consulta e, finalmente, para os coletores de saída. Cada etapa de consulta é mapeada para um conjunto de resultados temporário definido no script usando uma `WITH` instrução. Você pode exibir os dados, bem como as métricas de cada etapa de consulta em cada conjunto de resultados intermediários para localizar a origem de um problema.

> [!NOTE]
> Esse diagrama de trabalho mostra apenas os dados e as métricas para teste local em um único nó. Ele não deve ser usado para ajuste de desempenho e solução de problemas.

### <a name="start-local-testing"></a>Iniciar teste local

Use este guia de [início rápido](stream-analytics-quick-create-vs.md) para aprender a criar um trabalho de Stream Analytics usando o Visual Studio ou [exportar um trabalho existente para um projeto local](stream-analytics-vs-tools.md#export-jobs-to-a-project). Se você quiser testar a consulta com dados de entrada locais, siga estas [instruções](stream-analytics-live-data-local-testing.md). Se você quiser testar com a entrada ao vivo, vá para a próxima etapa.

> [!NOTE]
> Se você exportar um trabalho para um projeto local e desejar testá-lo em um fluxo de entrada ao vivo, precisará especificar as credenciais para todas as entradas novamente.  

Escolha a fonte de entrada e saída no editor de scripts e selecione **executar localmente**. O diagrama de trabalho aparece no lado direito.

### <a name="view-the-intermediate-result-set"></a>Exibir o conjunto de resultados intermediário  

1. Selecione a etapa de consulta para navegar até o script. Você será direcionado automaticamente para o script correspondente no editor à esquerda.

   ![Script de navegação de diagrama de trabalho](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Selecione a etapa de consulta e selecione **Visualizar** na caixa de diálogo exibida. O conjunto de resultados é mostrado em uma guia na janela de resultado inferior.

   ![Resultado da visualização do diagrama de trabalho](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Exibir métricas da etapa

Nesta seção, você explora as métricas disponíveis para cada parte do diagrama.

#### <a name="input-sources-live-stream"></a>Fontes de entrada (Live Stream)

![Fontes de entrada dinâmicas de diagrama de trabalho](./media/debug-locally-using-job-diagram/live-input.png)

|Métrica|Descrição|
|-|-|
|**TaxiRide**| Nome da entrada.|
|**Hub de Evento** | Tipo de fonte de entrada.|
|**Eventos**|O número de eventos lidos.|
|**Fontes de evento de registro posterior**|Quantas mensagens mais precisam ser lidas para os hubs de eventos e entradas do Hub IoT.|
|**Eventos em bytes**|O número de bytes lidos.|
| **Eventos degradados**|A contagem de eventos que tiveram um problema diferente de desserialização.|
|**Eventos antecipados**| O número de eventos que têm um carimbo de data/hora do aplicativo antes da marca d' água alta.|
|**Eventos atrasados**| O número de eventos que têm um carimbo de data/hora do aplicativo após a marca d' água alta.|
|**Origens do Evento**| O número de unidades de dados lidas. Por exemplo, o número de blobs.|

#### <a name="input-sources-local-input"></a>Fontes de entrada (entrada local)

![Fontes de entrada locais do diagrama de trabalho](./media/debug-locally-using-job-diagram/local-input.png)

|Métrica|Descrição|
|-|-|
|**TaxiRide**| Nome da entrada.|
|**Contagem de Linhas**| O número de linhas geradas a partir da etapa.|
|**Tamanho dos dados**| O tamanho dos dados gerados nesta etapa.|
|**Entrada local**| Use dados locais como entrada.|

#### <a name="query-steps"></a>Etapas de consulta

![Etapa de consulta do diagrama de trabalho](./media/debug-locally-using-job-diagram/query-step.png)

|Métrica|Descrição|
|-|-|
|**TripData**|O nome do conjunto de resultados temporário.|
|**Contagem de Linhas**| O número de linhas geradas a partir da etapa.|
|**Tamanho dos dados**| O tamanho dos dados gerados nesta etapa.|
  
#### <a name="output-sinks-live-output"></a>Coletores de saída (saída ao vivo)

![Diagrama de trabalho que mostra os coletores de saída local.](./media/debug-locally-using-job-diagram/live-output.png)

|Métrica|Descrição|
|-|-|
|**regionaggEH**|Nome da saída.|
|**Eventos**|O número de eventos de saída para coletores.|

#### <a name="output-sinks-local-output"></a>Coletores de saída (saída local)

![Coletor de saída local do diagrama de trabalho](./media/debug-locally-using-job-diagram/local-output.png)

|Métrica|Descrição|
|-|-|
|**regionaggEH**|Nome da saída.|
|**Saída local**| Saída de resultado para um arquivo local.|
|**Contagem de Linhas**| O número de linhas de saída para o arquivo local.|
|**Tamanho dos dados**| O tamanho da saída de dados para o arquivo local.|

### <a name="close-job-diagram"></a>Fechar diagrama de trabalho

Se você não precisar mais do diagrama de trabalho, selecione **fechar** no canto superior direito. Depois de fechar a janela diagrama, você precisa iniciar o teste local novamente para vê-la.

### <a name="view-job-level-metrics-and-stop-running"></a>Exibir métricas de nível de trabalho e parar de executar

Outras métricas de nível de trabalho aparecem no console pop-up. Pressione **Ctrl + C** no console se desejar interromper o trabalho.

![Trabalho de parada do diagrama de trabalho](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Limitações

* Não há suporte para Power BI e Azure Data Lake Storage Gen1 coletores de saída devido a limitações do modelo de autenticação.

* Somente opções de entrada de nuvem têm suporte para [políticas de tempo](./stream-analytics-time-handling.md), as opções de entrada locais, não.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: criar um trabalho do Stream Analytics usando o Microsoft Visual Studio](stream-analytics-quick-create-vs.md)
* [Use o Microsoft Visual Studio para visualizar os trabalhos do Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Testar dados dinâmicos localmente usando as ferramentas do Azure Stream Analytics para Visual Studio (versão prévia)](stream-analytics-live-data-local-testing.md)