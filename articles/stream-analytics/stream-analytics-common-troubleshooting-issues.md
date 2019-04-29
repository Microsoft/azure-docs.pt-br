---
title: Problemas comuns no Stream Analytics
description: Este artigo descreve vários problemas comuns no Azure Stream Analytics e as etapas para solucionar esses problemas.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 0191c56e1140870b1710b48c4fa1189fd92a337b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61362109"
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Problemas comuns no Stream Analytics e etapas para solucionar problemas

## <a name="troubleshoot-malformed-input-events"></a>Solucionar problemas de eventos de entrada malformados

 Os problemas de serialização são causados quando o fluxo de entrada do seu trabalho Stream Analytics contém mensagens malformadas. Por exemplo, uma mensagem malformada pode ser causado por um parênteses ausente em um objeto JSON ou de uma chave ausente ou formato de carimbo de hora incorreto em um campo de hora. 
 
 Quando um trabalho do Stream Analytics recebe uma mensagem malformada de uma entrada, ele descarta a mensagem e notifica o usuário com um aviso. Um símbolo de aviso é mostrado no bloco **Entradas** do trabalho do Stream Analytics (esse sinal de aviso existe enquanto o trabalho estiver no estado em execução):

![Bloco de Entradas no painel do Azure Stream Analytics](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Para ver mais informações, habilite os logs de diagnóstico para visualizar os detalhes do aviso. Para eventos de entrada malformados, os logs de execução contêm uma entrada com a mensagem que se parece com: "Mensagem: Não foi possível desserializar os eventos de entrada do recurso \<URI do blob > como json ". 

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

1. Navegue até o bloco de entrada e clique para exibir avisos.

2. O bloco de detalhes de entrada exibe um conjunto de avisos com detalhes sobre o problema. A seguir está uma mensagem de aviso de exemplo, a mensagem de aviso mostra a Partição, o Deslocamento e números de sequência em que há dados JSON malformados. 

   ![Mensagem de aviso de entrada com deslocamento](media/stream-analytics-malformed-events/warning-message-with-offset.png)

3. Para obter os dados JSON com formato incorreto, execute o seguinte o código CheckMalformedEvents.cs. Este exemplo está disponível no [repositório de exemplos GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Este código lê a partição ID, deslocamento, e imprime os dados que estão localizados nesse deslocamento. 

4. Depois de ler os dados, você pode analisar e corrigir o formato de serialização.

5. Você também pode [ler eventos de um Hub IoT com o Explorer do Barramento de Serviço](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="delayed-output"></a>Saída atrasada

### <a name="first-output-is-delayed"></a>A primeira saída está atrasada
Quando um trabalho do Stream Analytics é iniciado, os eventos de entrada são lidos, mas pode haver um atraso na saída produzida em determinadas circunstâncias.

Valores de hora grande em elementos de consulta temporal podem contribuir para o atraso de saída. Para produzir saída correta sobre as janelas de tempo grande, o trabalho de streaming é inicializado lendo dados do último tempo possível (até sete dias atrás) para preencher a janela de tempo. Durante esse período, nenhuma saída é produzida até que a leitura de recuperação do atraso dos eventos de entrada pendentes seja concluída. Esse problema pode surgir quando o sistema atualiza os trabalhos de streaming, assim, reiniciando o trabalho. Essas atualizações geralmente ocorrem uma vez a cada dois meses. 

Portanto, use critério ao criar a consulta do Stream Analytics. Se você usar uma janela de tempo grande (mais do que várias horas, até sete dias) para elementos temporal na sintaxe de consulta do trabalho, isso pode causar um atraso na primeira saída quando o trabalho é iniciado ou reiniciado.  

Uma mitigação para esse tipo de primeiro atraso de saída é usar técnicas de paralelização de consulta (particionamento de dados), ou adicionar mais unidades de Streaming para melhorar a taxa de transferência até o trabalho de captura.  Para obter mais informações, consulte [Considerações ao criar trabalhos do Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)

Esses fatores afetam a pontualidade da primeira saída que é gerada:

1. Uso de agregações em janela (GROUP BY Em cascata, Salto e Janelas deslizantes)
   - Para agregações de janela em cascata ou de salto, os resultados são gerados no final do período de tempo da janela. 
   - Para uma janela deslizante, os resultados são gerados quando um evento entra ou sai da janela deslizante. 
   - Se você estiver planejando usar a janela de tamanho grande (> 1 hora), é melhor escolher janela de salto ou deslizante para que você possa ver a saída com mais frequência.

2. Junções temporais (JOIN com DATEDIFF)
   - As correspondências serão geradas como quando chegarem a ambos os lados dos eventos correspondentes.
   - Dados que não tem uma correspondência (JUNÇÃO EXTERNA ESQUERDA) são gerados no final da janela DATEDIFF em relação a cada evento no lado esquerdo.

3. Uso de funções analíticas temporais (ISFIRST, LAST e a LAG com LIMIT DURATION)
   - Para funções analíticas, a saída será gerada para cada evento, não há nenhum atraso.

### <a name="output-falls-behind"></a>A saída fica atrás
Durante a operação normal do trabalho, se você verificar que a saída do trabalho está atrasada (latência cada vez maior), você pode identificar as causas examinando esses fatores:
- Se o coletor de downstream está limitado
- Se o coletor de upstream está limitado
- Se a lógica de processamento da consulta está com uso intensivo de computação

Para ver os detalhes, no portal do Azure, selecione o trabalho de streaming e selecione o **Diagrama de trabalho**. Para cada entrada, ha uma métrica de evento de lista de pendências por partição. Se a métrica de evento de lista de pendências continuar crescendo, é um indicador de que os recursos do sistema estão restritos. Potencialmente é devido a limitação de coletor de saída ou alta utilização da CPU. Para obter mais informações sobre como usar o diagrama de trabalho, consulte [Depuração orientada a dados usando o diagrama de trabalho](stream-analytics-job-diagram-with-metrics.md).

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>Lidar com registros duplicados na saída do Banco de Dados SQL do Azure

Ao configurar o Banco de Dados SQL do Azure como saída para um trabalho do Stream Analytics, o volume insere registros na tabela de destino. Em geral, o Azure Stream Analytics garante [pelo menos uma vez a entrega]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) para o coletor de saída, ainda pode-se obter [EOD (exactly-once-delivery)]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) para saída do SQL quando a tabela do SQL tiver uma restrição exclusiva definida. 

Quando as restrições de chave exclusivas são definidas na tabela SQL e existem registros duplicados sendo inseridos na tabela do SQL, o Azure Stream Analytics remove o registro duplicado. Ele divide os dados em lotes e recursivamente insere os lotes até que um único registro duplicado seja encontrado. Se o trabalho de streaming tem um número considerável de linhas duplicadas, ele divide e insere o processo para ignorar as duplicatas uma por uma, que é menos eficiente e demorado. Se visualizar várias mensagens de advertência de violação da chave no log de atividades na última hora, é provável que a saída do SQL esteja retardando o trabalho inteiro. 

Para resolver esse problema, é necessário [configurar o índice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) que está causando a violação da chave, habilitando a opção IGNORE_DUP_KEY. Habilitar essa opção permite que os valores duplicados sejam ignorados pelo SQL durante as inserções em massa e o SQL do Azure simplesmente produz uma mensagem de aviso em vez de um erro. O Azure Stream Analytics não produz mais erros de violação de chave primária.

Observe as observações a seguir ao configurar IGNORE_DUP_KEY para vários tipos de índices:

* Não é possível definir IGNORE_DUP_KEY em uma chave primária ou uma restrição exclusiva que utiliza ALTER INDEX, sendo necessário remover e recriar o índice.  
* É possível definir a opção IGNORE_DUP_KEY utilizando ALTER INDEX para um índice exclusivo, que é diferente da restrição PRIMARY KEY/UNIQUE e criada utilizando a definição CREATE INDEX ou INDEX.  
* IGNORE_DUP_KEY não é aplicável para índices de repositório de coluna porque não é possível impor a exclusividade desses índices.  

## <a name="next-steps"></a>Próximas etapas
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
