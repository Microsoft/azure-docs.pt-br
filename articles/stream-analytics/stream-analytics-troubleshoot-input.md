---
title: Entradas de solução de problemas do Azure Stream Analytics
description: Este artigo descreve técnicas para solucionar problemas de suas conexões de entrada em tarefas do Stream Analytics do Azure.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: b5ed614fdd378b36d8f95fc90ce7ff98d63ef31a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761802"
---
# <a name="troubleshoot-input-connections"></a>Solucionar problemas de conexões de entrada

Esta página descreve problemas comuns com conexões de entrada e como solucioná-los.

## <a name="input-events-not-received-by-job"></a>Eventos de entrada não são recebidos pelo trabalho 
1.  Teste a conectividade. Verifique a conectividade com as entradas e saídas usando o botão **Testar Conexão** em cada entrada e saída.

2.  Examine os dados de entrada.

    Para verificar se os dados de entrada estão fluindo para o Hub de Eventos, use o [Gerenciador do Barramento de Serviço](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) para se conectar ao Hub de Eventos do Azure (caso a entrada do Hub de Eventos seja usada).
        
    Use o botão [**Dados de Exemplo**](stream-analytics-sample-data-input.md) para cada entrada e baixe os dados de exemplo de entrada.
        
    Inspecione os dados de exemplo para entender a forma deles: o esquema e os [tipos de dados](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="malformed-input-events-causes-deserialization-errors"></a>Eventos de entrada malformados causam erros de desserialização 
Os problemas de desserialização são causados quando o fluxo de entrada do seu trabalho do Stream Analytics contém mensagens malformadas. Por exemplo, uma mensagem malformada pode ser causada por um parêntese ausente ou uma chave em um objeto JSON ou um formato de registro de data e hora incorreto no campo de hora. 
 
Quando um trabalho do Stream Analytics recebe uma mensagem malformada de uma entrada, ela solta a mensagem e notifica você com um aviso. Um símbolo de aviso é mostrado no bloco **Entradas** do seu trabalho do Stream Analytics. Este sinal de aviso existe enquanto o trabalho estiver em execução:

![Bloco entradas do Stream Analytics do Azure](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Ative os logs de diagnósticos para visualizar os detalhes do aviso. Para eventos de entrada malformados, os logs de execução contêm uma entrada com a mensagem que se parece com: 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>O que causou o erro de desserialização
Você pode executar as etapas a seguir para analisar os eventos de entrada em detalhes para obter uma compreensão clara do que causou o erro de desserialização. Você pode, então, corrigir a origem do evento para gerar eventos no formato correto para evitar que você atinja novamente esse problema.

1. Navegue até o bloco de entrada e clique nos símbolos de aviso para ver a lista de problemas.

2. O bloco de detalhes de entrada exibe uma lista de avisos com detalhes sobre cada problema. A mensagem de aviso de exemplo abaixo inclui os números de partição, deslocamento e sequência em que há dados JSON malformados. 

   ![Mensagem de aviso do Stream Analytics com deslocamento](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. Para localizar os dados JSON com o formato incorreto, execute o código CheckMalformedEvents.cs disponível no repositório de amostras [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Este código lê a partição ID, deslocamento, e imprime os dados que estão localizados nesse deslocamento. 

4. Depois de ler os dados, você pode analisar e corrigir o formato de serialização.

5. Você também pode [ler eventos de um Hub IoT com o Explorer do Barramento de Serviço](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="job-exceeds-maximum-event-hub-receivers"></a>O trabalho excede o máximo de receptores de hub de eventos
Uma prática recomendada para usar os Hubs de Eventos é usar vários grupos de consumidores para garantir a escalabilidade de tarefas. O número de leitores no trabalho do Stream Analytics para uma entrada específica afeta o número de leitores em um único grupo de consumidores. O número preciso de receptores é baseado em detalhes de implementação interna para a lógica de topologia de scale-out e não é exposto externamente. O número de leitores pode mudar quando um trabalho é iniciado ou durante os upgrades de trabalho.

O erro mostrado quando o número de destinatários excede o máximo é: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Quando o número de leitores muda durante o upgrade de um trabalho, avisos temporários são gravados nos logs de auditoria. Os trabalhos do Stream Analytics são recuperados automaticamente desses problemas transitórios.

### <a name="add-a-consumer-group-in-event-hubs"></a>Adicionar um grupo de consumidores nos Hubs de Eventos
Para adicionar um novo grupo de consumidores à instância dos Hubs de Eventos, execute estas etapas:

1. Entre no Portal do Azure.

2. Localize seus Hubs de Eventos.

3. Selecione **Hubs de Eventos** sob o **Entidades**.

4. Selecione o Hub de Eventos por nome.

5. Na página **Instância de Hubs de Eventos** no cabeçalho **Entidades**, selecione **Grupos de consumidores**. Um grupo de consumidores com nome **$Default** está listado.

6. Selecione **+ Grupo de Consumidores** para adicionar um novo grupo de consumidores. 

   ![Adicionar um grupo de consumidores nos Hubs de Eventos](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Quando criou a entrada no trabalho do Stream Analytics para apontar para o Hub de Eventos, você especificou o grupo de consumidores nele. $Default é usado quando nenhum for especificado. Depois de criar um novo grupo de consumidores, edite a entrada do Hub de Eventos no trabalho do Stream Analytics e especifique o nome do novo grupo de consumidores.


## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Os leitores por partição excede o limite de Hubs de eventos

Se a sintaxe de consulta de streaming fizer referência ao mesmo recurso do Hub de Eventos de entrada várias vezes, o mecanismo de trabalho poderá usar vários leitores por consulta desse mesmo grupo de consumidores. Quando há um número excessivo de referências para o mesmo grupo de consumidores, o trabalho pode exceder o limite de cinco e gerar um erro. Nessas circunstâncias, você pode dividir ainda mais usando várias entradas em vários grupos de consumidores utilizando a solução descrita na seção a seguir. 

Os cenários em que o número de leitores por partição excede o limite de cinco dos Hubs de Eventos incluem os seguintes:

* Várias instruções SELECT: se você usar várias instruções SELECT que se refiram à **mesma** entrada do hub de eventos, cada instrução SELECT fará com que um novo receptor seja criado.
* UNION: quando você usa UNION, é possível ter várias entradas que se refiram ao **mesmo** grupo de consumidores e hub de eventos.
* SELF JOIN: quando você usa uma operação SELF JOIN, é possível se referir ao **mesmo** hub de eventos várias vezes.

As práticas recomendadas a seguir podem ajudar a minimizar cenários nos quais o número de leitores por partição excede o limite de cinco de Hubs de Eventos.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Dividir a consulta em várias etapas usando uma cláusula WITH

A cláusula WITH especifica um conjunto de resultados nomeado temporário que pode ser referenciado por uma cláusula FROM na consulta. Você define a cláusula WITH no escopo de execução de uma única instrução SELECT.

Por exemplo, em vez desta consulta:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Use esta consulta:

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Garantir que as entradas se associem a diferentes grupos de consumidores

Para consultas em que três ou mais entradas estão conectadas ao mesmo grupo de consumidores de Hubs de Eventos, crie grupos de consumidores separados. Isso exige a criação de entradas adicionais do Stream Analytics.

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
