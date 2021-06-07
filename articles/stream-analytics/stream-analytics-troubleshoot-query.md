---
title: Solucionar problemas de consultas do Azure Stream Analytics
description: Este artigo descreve as técnicas para solucionar problemas das suas consultas nos trabalhos do Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 0dc36b817d5b5cdf731edecd64e1879c153d866a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015123"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Solucionar problemas de consultas do Azure Stream Analytics

Este artigo descreve problemas comuns com o desenvolvimento de consultas do Stream Analytics e como solucioná-los.

Este artigo descreve problemas comuns com o desenvolvimento de consultas do Azure Stream Analytics, como solucionar problemas de consulta e como corrigir os problemas. Muitas etapas de solução de problemas exigem que os logs de recursos sejam habilitados para seu trabalho do Stream Analytics. Se você não tiver os logs de recursos habilitados, confira [Solucionar problemas do Azure Stream Analytics usando os logs de recursos](stream-analytics-job-diagnostic-logs.md).

## <a name="query-is-not-producing-expected-output"></a>A consulta não está produzindo a saída esperada

1.  Examine os erros testando localmente:

    - No portal do Azure, na guia **Consulta**, selecione **Teste**. Use os dados de exemplo baixado para [testar a consulta](stream-analytics-test-query.md). Examine os erros e tente corrigi-los.   
    - Você também pode [testar sua consulta localmente](stream-analytics-live-data-local-testing.md) usando as ferramentas do Azure Stream Analytics para Visual Studio ou [Visual Studio Code](visual-studio-code-local-run-live-input.md). 

2.  [Depurar consultas passo a passo localmente usando o diagrama de trabalho](debug-locally-using-job-diagram-vs-code.md) em ferramentas de Azure Stream Analytics para Visual Studio Code. O diagrama de trabalho mostra como os dados fluem de fontes de entrada (hub de eventos, Hub IoT, etc.) por meio de várias etapas de consulta e finalmente para coletores de saída. Cada etapa da consulta é mapeada para um conjunto de resultados temporário definido no script usando a instrução WITH. Você pode exibir os dados, além das métricas, em cada conjunto de resultados intermediários para localizar a origem do problema.

    ![Resultado da visualização do diagrama de trabalho](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

3.  Se você usar [**Carimbo de Data/Hora Por**](/stream-analytics-query/timestamp-by-azure-stream-analytics), verifique se os eventos têm carimbos de data/hora maiores que a [hora de início do trabalho](./stream-analytics-time-handling.md).

4.  Elimine armadilhas comuns, como:
    - Uma cláusula [**WHERE**](/stream-analytics-query/where-azure-stream-analytics) na consulta filtrou todos os eventos, impedindo que uma saída seja gerada.
    - Uma função [**CAST**](/stream-analytics-query/cast-azure-stream-analytics) falha, causando a falha do trabalho. Nesse caso, para evitar falhas de conversão de tipo, use [**TRY_CAST**](/stream-analytics-query/try-cast-azure-stream-analytics).
    - Ao usar funções de janela, aguarde a duração de toda a janela para ver uma saída da consulta.
    - O carimbo de hora de eventos precede a hora de início do trabalho e os eventos são removidos.
    - As condições [**JOIN**](/stream-analytics-query/join-azure-stream-analytics) não correspondem. Se não houver correspondência, não haverá saída.

5.  Verifique se as políticas de ordenação de eventos estão configuradas conforme o esperado. Acesse **Configurações** e selecione [**Ordenação de eventos**](./stream-analytics-time-handling.md). A política *não* é aplicada quando você usa o botão **Testar** para testar a consulta. Esse resultado é uma das diferenças entre o teste no navegador comparado à execução do trabalho em produção. 

6. Depurar usando logs de atividade e de recursos:
    - Use os [logs de atividade](../azure-resource-manager/management/view-activity-logs.md) e filtre para identificar e depurar erros.
    - Use [logs de recurso de trabalho](stream-analytics-job-diagnostic-logs.md) para identificar e depurar erros.

## <a name="resource-utilization-is-high"></a>A utilização de recursos está alta

Este artigo mostra como tirar proveito da paralelização no Azure Stream Analytics. Você pode aprender a [dimensionar com paralelização de consultas](stream-analytics-parallelization.md) de trabalhos do Stream Analytics configurando partições de entrada e ajustando a definição da consulta de análise.

Se a utilização de recursos estiver consistentemente acima de 80%, o atraso de marca d' água será aumentado e o número de eventos de registro posterior estará aumentando, considere aumentar as unidades de streaming. A alta utilização indica que o trabalho está usando próximo ao máximo de recursos alocados.

## <a name="debug-queries-progressively"></a>Depurar consultas progressivamente

No processamento de dados em tempo real, saber qual será a aparência dos dados no meio da consulta pode ser útil. Isso pode ser visto no diagrama de trabalho no Visual Studio. Se não tiver o Visual Studio, você poderá executar etapas adicionais para gerar dados intermediários.

Como as entradas ou etapas de um trabalho do Stream Analytics do Azure podem ser lidas várias vezes, você pode escrever instruções SELECT INTO extras. Ao fazer isso, dados intermediários são gerados no armazenamento que permitem a você inspecionar a exatidão dos dados, assim como as *variáveis de inspeção* fazem quando você depura um programa.

O exemplo de consulta a seguir em um trabalho do Stream Analytics do Azure tem uma entrada de fluxo, duas entradas de dados de referência e uma saída para o Armazenamento de Tabelas do Azure. A consulta une dados do hub de eventos e dois blobs de referência para obter informações de nome e categoria:

![No Stream Analytics, consulta SELECIONAR EM](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Observe que o trabalho está em execução, mas não há eventos sendo gerados na saída. No bloco **Monitoramento**, mostrado aqui, você pode ver que a entrada está gerando dados, mas não dá para saber qual etapa de **JOIN** fez com que todos os eventos fossem removidos.

![Bloco de Monitoramento do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

Nesse caso, é possível adicionar algumas instruções extras SELECT INTO para "registrar em log" os resultados intermediários de JOIN e os dados que são lidos da entrada.

Neste exemplo, adicionamos duas novas "saídas temporárias." Elas podem ser qualquer coletor desejado. Aqui, usamos o Armazenamento do Azure como um exemplo:

![Adicionar instruções SELECIONAR EM para a consulta do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Assim, você pode reescrever a consulta desta forma:

![Consulta regravada de SELECIONAR EM do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Agora, reinicie o trabalho e deixe ele ser executado por alguns minutos. Em seguida, consulte temp1 e temp2 com o Cloud Explorer do Visual Studio para gerar as seguintes tabelas:

**tabela temp1**
![SELECIONAR EM temp1 tabela consulta do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**tabela temp2**
![SELECIONAR EM temp2 tabela consulta do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Como você pode ver, temp1 e temp2 têm dados e a coluna de nome é preenchida corretamente em temp2. No entanto, como ainda não há dados na saída, algo está errado:

![Tabela output1 SELECIONAR EM sem dados da consulta do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Pela amostragem de dados, você pode ter quase certeza de que o problema é com o segundo JOIN. Você pode baixar os dados de referência do blob e dar uma olhada:

![SELECIONAR EM ref de consulta da tabela do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Como você pode ver, o formato do GUID nos dados de referência é diferente do formato da coluna [from] em temp2. Por esse motivo, os dados não chegaram em output1 como esperado.

É possível corrigir o formato dos dados, carregá-los no blob de referência e tentar novamente:

![SELECIONAR EM tabela temporária da consulta do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Dessa vez, os dados na saída são formatados e preenchidos conforme esperado.

![SELECIONAR EM tabela final de consulta do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, confira nossa [página de Perguntas e respostas do Microsoft do Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](/rest/api/streamanalytics/)
