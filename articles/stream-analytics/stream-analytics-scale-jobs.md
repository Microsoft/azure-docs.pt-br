---
title: Dimensionar trabalhos do Stream Analytics para aumentar a produtividade | Microsoft Docs
description: "Aprenda a dimensionar trabalhos do Stream Analytics configurando partições de entrada, ajustando a definição da consulta e definindo unidades de streaming de trabalho."
keywords: "streaming de dados, processamento de dados de streaming, ajuste de análise"
services: stream-analytics
documentationcenter: 
author: JSeb225
manager: jhubbard
editor: cgronlun
ms.assetid: 7e857ddb-71dd-4537-b7ab-4524335d7b35
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/22/2017
ms.author: jeanb
ms.openlocfilehash: a38394d825c9a9b3007b30f598b37caa08f7325f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scale-azure-stream-analytics-jobs-to-increase--throughput"></a>Dimensionar trabalhos do Azure Stream Analytics para aumentar a produtividade
Este artigo explica como você pode ajustar a consulta do Stream Analytics para aumentar a produtividade para os trabalhos do Streaming Analytics. Você pode usar o guia a seguir para dimensionar seu trabalho a fim de lidar com uma carga maior e aproveitar mais recursos do sistema (como maior largura de banda, mais recursos de CPU, mais memória).
Como pré-requisito, você precisa ler os seguintes artigos:
-   [Compreender e ajustar as Unidades de Streaming](stream-analytics-streaming-unit-consumption.md)
-   [Criar trabalhos paralelizáveis](stream-analytics-parallelization.md)


## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Caso 1: a consulta é inerentemente totalmente paralelizável entre partições de entrada
Se sua consulta é inerentemente totalmente paralelizável entre partições de entrada, você pode executar as seguintes etapas:
1.  Criar sua consulta para ser embaraçosamente paralela usando a palavra-chave **PARTITION BY**. Veja mais detalhes na seção Trabalhos embaraçosamente paralelos [nesta página](stream-analytics-parallelization.md).
2.  Dependendo de tipos de saída usados na consulta, alguns resultados podem não ser paralelizáveis ou precisar de mais configuração para serem embaraçosamente paralelos. Por exemplo, resultados SQL, SQL DW e PowerBI não são paralelizáveis. Os resultados sempre são mesclados antes de enviar para o coletor de saída. Os blobs, as tabelas, o ADLS, o Barramento de Serviço e o Azure Functions são paralelizados automaticamente. O CosmosDB e Hub de Eventos devem ter a configuração de PartitionKey definida para corresponder ao campo **PARTITION BY** (geralmente PartitionId). Para o Hub de eventos, cuidado ao corresponder o número de partições a todas as entradas e saídas para evitar o cruzamento entre partições. 
3.  Execute a consulta com **6 UA** (que é a capacidade total de um único nó de computação) para medir a taxa de transferência máxima possível, e se você estiver usando **GROUP BY**, meça quantos grupos (cardinalidade) o trabalho pode atender. Os sintomas gerais do trabalho atingindo os limites de recursos do sistema são as seguintes.
    - A métrica de utilização da % de SU etá acima de 80%. Isso indica que o uso da memória está alto. Os fatores que contribuem para o aumento dessa métrica estão descritos [aqui](stream-analytics-streaming-unit-consumption.md). 
    -   O carimbo de data/hora de saída está atrasado em relação à hora real. Dependendo de sua lógica de consulta, o carimbo de data/hora de saída pode ter uma defasagem em relação à hora real. No entanto, eles devem progredir mais ou menos no mesmo ritmo. Se o carimbo de data/hora de saída está se atrasando mais, isso é um indicador de que o sistema está trabalhando em excesso. Ele pode ser resultado da limitação do coletor de saída downstream ou da alta utilização da CPU. Não fornecemos métrica de utilização da CPU no momento e, portanto, pode ser difícil diferenciar as duas.
        - Se o problema for devido à limitação do coletor, talvez seja necessário aumentar o número de partições de saída (e também de partições de entrada para manter o trabalho totalmente paralelizáveis) ou aumentar a quantidade de recursos do coletor (por exemplo, número de unidades de solicitação para o CosmosDB).
    - No diagrama de trabalho, há uma métrica de evento de lista de pendências por partição para cada entrada. Se a métrica de evento de lista de pendências continuar crescendo, será também um indicador de que o recurso do sistema está restrito (seja devido à limitação do coletor de saída ou à alta utilização da CPU).
4.  Depois de determinar os limites do que um trabalho com 6 UA pode alcançar, é possível extrapolar linearmente a capacidade de processamento do trabalho conforme vai adicionando mais UA, supondo que você não tenha nenhuma distorção de dados que torne certa partição "ativa".
>[!Note]
> Escolha o número correto de Unidades de Streaming: como o Stream Analytics cria um nó de processamento para cada 6 SU adicionadas, é melhor ter um número de nós que seja divisor do número de partições de entrada, para que as partições possam ser distribuídas uniformemente entre os nós.
> Por exemplo, você mediu que seu trabalho com 6 UA pode alcançar a taxa de processamento de 4 MB/s, e a contagem de partições de entrada é 4. Você pode optar por executar o trabalho com 12 UA para alcançar a taxa de processamento de aproximadamente 8 MB/s ou 24 UA para alcançar 16 MB/s. Em seguida, você pode decidir quando aumentar o número de UA para o trabalho e em qual valor como função da sua taxa de entrada.



## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Caso 2: se sua consulta não é embaraçosamente paralela.
Se sua consulta não é embaraçosamente paralela, você pode executar as etapas a seguir.
1.  Inicie com uma consulta sem nenhum **PARTITION BY** primeiro para evitar a complexidade do particionamento e execute a consulta com 6 UA para medir a carga máxima como no [Caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Se você pode obter sua carga prevista em termos de taxa de transferência, está tudo certo. Como alternativa, você pode optar por medir o mesmo trabalho em execução em 3 UAs e 1 UA para descobrir o número mínimo de UAs que funciona no seu cenário.
3.  Se você não conseguir obter a taxa de transferência desejada, tente dividir a consulta em várias etapas, se possível (se ela não já tiver várias etapas) e alocar até 6 UAs para cada etapa na consulta. Por exemplo, se você tiver 3 etapas, aloque 18 UAs na opção "Escala".
4.  Ao executar o trabalho, o Stream Analytics colocará cada etapa em seu próprio nó com os recursos de 6 UAs dedicadas. 
5.  Se ainda não tiver obtido sua carga-alvo, poderá tentar usar **PARTITION BY** começando por etapas mais próximas à entrada. Para operador **GROUP BY** que pode não ser naturalmente particionável, você pode usar o padrão de agregação global/local para executar um **GROUP BY** particionado seguido de um **GROUP BY**  não particionado. Por exemplo, se você desejar contar quantos carros passam por cada pedágio a cada três minutos e o volume de dados está além do que pode ser tratado por 6 UAs.

Consulta:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Na consulta, você está contando carros por pedágio por partição e, em seguida, adicionando a contagem de todas as partições juntas.

Depois de particionar, para cada partição da etapa, aloque até 6 UAs (o máximo de cada partição é 6 UAs) para que cada partição possa ser colocada em seu próprio nó de processamento.

> [!Note]
> Se sua consulta não pode ser particionada, adicionar mais UAs em uma consulta de várias etapas pode nem sempre melhorar a produtividade. Uma maneira de obter um melhor desempenho é reduzir o volume de etapas iniciais usando padrão de agregação local/global, como descrito anteriormente na etapa 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Caso 3: você está executando muitas consultas independentes em um trabalho.
Para determinados casos de uso de ISV, em que é mais eficiente processar dados de vários locatários em um único trabalho, usando separado de entradas e saídas para cada locatário, você pode acabar executando várias (por exemplo, 20) consultas independentes em um mesmo trabalho. A suposição é a de que a carga de cada subconsulta é relativamente pequena. Nesse caso, você pode seguir as etapas abaixo.
1.  Nesse caso, não use **PARTITION BY** na consulta
2.  Reduza a contagem de partições de entrada para o menor valor possível de 2 se você estiver usando um Hub de Eventos.
3.  Execute a consulta com 6 UAs. Com carga esperada para cada subconsulta, adicione quantas subconsultas puder, até que o trabalho esteja atingindo os limites de recursos do sistema. Consulte o [Caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) para saber quais são os sintomas quando isso acontece.
4.  Depois que você está atingindo o limite de subconsulta medido acima, comece a adicionar a subconsulta a um novo trabalho. O número de trabalhos a serem executados como uma função do número de consultas independentes deve ser bastante linear, supondo que você não tenha nenhuma distorção de carga. Você pode prever quantos trabalhos com 6 UA precisam ser executados em função do número de locatários que devem ser atendidos.
5.  Ao usar a união de dados de referência com tais consultas, você deverá unir as entradas antes de unir os mesmos dados de referência e, em seguida, dividir os eventos, se necessário. Caso contrário, cada união de dados de referência mantém uma cópia dos dados de referência na memória, provavelmente estourando o uso da memória desnecessariamente.

> [!Note] 
> Quantos locatários devem ser colocados em cada trabalho?
> Geralmente, esse padrão de consulta tem um grande número de subconsultas e resulta em topologia muito grande e complexa. O controlador do trabalho pode não conseguir lidar com uma topologia tão grande. Como regra geral, fique com menos de 40 locatários para trabalho com 1 UA e 60 locatários para trabalhos de 3 UAs e 6 UAs. Quando você está excedendo a capacidade do controlador, o trabalho não é iniciado com êxito.


## <a name="an-example-of-stream-analytics-throughput-at-scale"></a>Um exemplo de taxa de transferência do Stream Analytics em escala
Para ajudá-lo a entender como os trabalhos do Stream Analytics escalam, realizamos uma experiência com base na entrada de um dispositivo Raspberry Pi. Esse teste nos permitiu ver o efeito na taxa de transferência de várias partições e unidades de streaming.

Nesse cenário, o dispositivo envia dados de sensor (clientes) para um hub de eventos. Streaming Analytics processa os dados e envia um alerta ou estatísticas como uma saída para outro hub de eventos. 

O cliente envia dados de sensor em formato JSON. A saída de dados também está no formato JSON. Os dados tem esta aparência:

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

A consulta a seguir é usada para enviar um alerta quando uma luz é desligada:

    SELECT AVG(lght), "LightOff" as AlertText
    FROM input TIMESTAMP BY devicetime 
    PARTITION BY PartitionID
    WHERE lght< 0.05 GROUP BY TumblingWindow(second, 1)

### <a name="measure-throughput"></a>Medida de taxa de transferência

Neste contexto, a produtividade é a quantidade de dados de entrada processados pelo Stream Analytics em um período fixo. (É medido por 10 minutos.) Para obter a melhor taxa de transferência de processamento para os dados de entrada, tanto a entrada do fluxo de dados quanto a consulta foram particionadas. Nós incluímos **COUNT()** na consulta para medir a quantidade de eventos de entrada processados. Para garantir que o trabalho não esteja apenas esperando pelos eventos de entrada, cada partição do Hub de Eventos de Entrada foi pré-carregada com dados de entrada de cerca de 300 MB.

A tabela a seguir mostra os resultados que vimos quando aumentamos o número de unidades de streaming e a partição correspondente conta nos Hubs de eventos.  

<table border="1">
<tr><th>Partições de entrada</th><th>Partições de saída</th><th>Unidades de streaming</th><th>Taxa de transferência mantida
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4,06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8,06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38,32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172,67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454,27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609,69 MB/s</td>
</tr>
</table>

E o gráfico a seguir mostra uma visualização da relação entre SUs e taxa de transferência.

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

