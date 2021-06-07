---
title: Entradas de solução de problemas do Azure Stream Analytics
description: Este artigo descreve técnicas para solucionar problemas de suas conexões de entrada em tarefas do Stream Analytics do Azure.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 05/01/2020
ms.custom: seodec18
ms.openlocfilehash: 2d7171c9ec1e60447fb3342caa72098fb2eb9337
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019900"
---
# <a name="troubleshoot-input-connections"></a>Solucionar problemas de conexões de entrada

Esse artigo descreve problemas comuns com conexões de entrada do Azure Stream Analytics, como solucionar problemas de entrada e como corrigir os problemas. Muitas etapas de solução de problemas exigem que os logs de recursos sejam habilitados para seu trabalho do Stream Analytics. Se você não tiver os logs de recursos habilitados, confira [Solucionar problemas do Azure Stream Analytics usando os logs de recursos](stream-analytics-job-diagnostic-logs.md).

## <a name="input-events-not-received-by-job"></a>Eventos de entrada não são recebidos pelo trabalho 

1.  Teste sua conectividade de entrada e saída. Verifique a conectividade com as entradas e saídas usando o botão **Testar Conexão** em cada entrada e saída.

2.  Examine os dados de entrada.

    1. Use o botão [**Dados de Exemplo**](./stream-analytics-test-query.md) para cada entrada. Baixe os dados de exemplo de entrada.
        
    1. Inspecione os dados de exemplo para entender o esquema e os [tipos de dados](/stream-analytics-query/data-types-azure-stream-analytics).
    
    1. Verifique as [Métricas do Hub de Eventos](../event-hubs/event-hubs-metrics-azure-monitor.md) para garantir que os eventos estejam sendo enviados. As métricas de mensagem devem ser maiores que zero se os Hubs de Eventos estiverem recebendo mensagens.

3.  Verifique se você selecionou um intervalo de tempo na visualização da entrada. Escolha **Selecionar intervalo de tempo** e, em seguida, insira uma duração de amostra antes de testar sua consulta.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Eventos de entrada malformados causam erros de desserialização 

Os problemas de desserialização são causados quando o fluxo de entrada do seu trabalho do Stream Analytics contém mensagens malformadas. Por exemplo, uma mensagem malformada pode ser causada por um parêntese ou uma chave ausente em um objeto JSON ou um formato de carimbo de data/hora incorreto no campo de hora. 
 
Quando um trabalho do Stream Analytics recebe uma mensagem malformada de uma entrada, ela solta a mensagem e notifica você com um aviso. Um símbolo de aviso é mostrado no bloco **Entradas** do seu trabalho do Stream Analytics. O seguinte símbolo de aviso existe desde que o trabalho esteja em estado de execução:

![Bloco entradas do Stream Analytics do Azure](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Habilite os logs de recursos para visualizar os detalhes do erro e a mensagem (conteúdo) que causou o erro. Há várias razões pelas quais podem ocorrer erros de desserialização. Para obter mais informações sobre erros de desserialização específicos, confira [Erros de dados de entrada](data-errors.md#input-data-errors). Se os logs de recursos não estiverem habilitados, uma breve notificação estará disponível no portal do Azure.

![Notificação de aviso de detalhes de entrada](media/stream-analytics-malformed-events/warning-message-with-offset.png)

Nos casos em que o conteúdo da mensagem é maior que 32 KB ou está em formato binário, execute o código CheckMalformedEvents.cs, disponível no repositório de exemplos do [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Este código lê a partição ID, deslocamento, e imprime os dados que estão localizados nesse deslocamento. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>O trabalho excede o máximo de receptores do Hub de Eventos

Uma prática recomendada para usar os Hubs de Eventos é usar vários grupos de consumidores para a escalabilidade de tarefas. O número de leitores no trabalho do Stream Analytics para uma entrada específica afeta o número de leitores em um único grupo de consumidores. O número preciso de receptores é baseado em detalhes de implementação interna para a lógica de topologia de scale-out e não é exposto externamente. O número de leitores pode mudar quando um trabalho é iniciado ou durante os upgrades de trabalho.

As mensagens de erro a seguir são mostradas quando o número de destinatários excede o máximo. A mensagem de erro inclui uma lista de conexões existentes feitas ao Hub de Eventos em um grupo de consumidores. A tag `AzureStreamAnalytics` indica que as conexões são do serviço de streaming do Azure.

```
The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.

The following information may be helpful in identifying the connected receivers: Exceeded the maximum number of allowed receivers per partition in a consumer group which is 5. List of connected receivers – 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1.
```

> [!NOTE]
> Quando o número de leitores muda durante o upgrade de um trabalho, avisos temporários são gravados nos logs de auditoria. Os trabalhos do Stream Analytics são recuperados automaticamente desses problemas transitórios.

### <a name="add-a-consumer-group-in-event-hubs"></a>Adicionar um grupo de consumidores nos Hubs de Eventos

Para adicionar um novo grupo de consumidores à instância dos Hubs de Eventos, execute estas etapas:

1. Entre no portal do Azure.

2. Localize seu Hub de Eventos.

3. Selecione **Hubs de Eventos** sob o **Entidades**.

4. Selecione o Hub de Eventos por nome.

5. Na página **Instância de Hubs de Eventos** no cabeçalho **Entidades**, selecione **Grupos de consumidores**. Um grupo de consumidores com nome **$Default** está listado.

6. Selecione **+ Grupo de Consumidores** para adicionar um novo grupo de consumidores. 

   ![Adicionar um grupo de consumidores nos Hubs de Eventos](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Quando criou a entrada no trabalho do Stream Analytics para apontar para o Hub de Eventos, você especificou o grupo de consumidores nele. **$Default** é usado quando nenhum é especificado. Depois de criar um novo grupo de consumidores, edite a entrada do Hub de Eventos no trabalho do Stream Analytics e especifique o nome do novo grupo de consumidores.

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

### <a name="create-separate-inputs-with-different-consumer-groups"></a>Criar entradas separadas com diferentes grupos de consumidores

Você pode criar entradas separadas com diferentes grupos de consumidores para o mesmo Hub de eventos. A seguinte consulta de União é um exemplo em que *InputOne* e *InputTwo* se referem à mesma origem do hub de eventos. Qualquer consulta pode ter entradas separadas com diferentes grupos de consumidores. A consulta UNION é apenas um exemplo.

```sql
WITH 
DataOne AS 
(
SELECT * FROM InputOne 
),

DataTwo AS 
(
SELECT * FROM InputTwo 
),

SELECT foo FROM DataOne
UNION 
SELECT foo FROM DataTwo

```

## <a name="readers-per-partition-exceeds-iot-hub-limit"></a>Os leitores por partição excedem o limite do Hub IoT

Stream Analytics trabalhos usam o [ponto de extremidade compatível com o Hub de eventos](../iot-hub/iot-hub-devguide-messages-read-builtin.md) interno do Hub IOT para se conectar e ler eventos do Hub IOT. Se sua leitura por partição exceder os limites do Hub IoT, você poderá usar as [soluções para o Hub de eventos](#readers-per-partition-exceeds-event-hubs-limit) para resolvê-lo. Você pode criar um grupo de consumidores para o ponto de extremidade interno por meio da sessão de ponto de extremidade do portal do Hub IoT ou por meio do [SDK do Hub IOT](/rest/api/iothub/IotHubResource/CreateEventHubConsumerGroup).

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, confira nossa [página de Perguntas e respostas do Microsoft do Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](/rest/api/streamanalytics/)