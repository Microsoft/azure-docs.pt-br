---
title: Configurar políticas para o Azure Stream Analytics da ordenação de eventos
description: Este artigo descreve como sobre como configurar a ordenação, mesmo as configurações no Stream Analytics
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 970eeb871775e24abb87c8b977e214645e514d3b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789466"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Configurar políticas para o Azure Stream Analytics da ordenação de eventos

Este artigo descreve como configurar e usar a chegada tardia e políticas de eventos fora de ordem no Azure Stream Analytics. Essas políticas são aplicadas somente quando você usa o [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) cláusula em sua consulta.

## <a name="event-time-and-arrival-time"></a>Hora do evento e a hora de chegada

O trabalho do Stream Analytics pode processar eventos com base em uma *hora de evento* ou *hora de chegada*. **Hora do evento/aplicativo** é o carimbo de hora presente na carga do evento (quando o evento foi gerado). **Hora de chegada** é o carimbo de hora quando o evento foi recebido na fonte de entrada (eventos de Hubs/IoT Hub/Blob storage). 

Por padrão, o Stream Analytics processa eventos por *hora de chegada*, mas você pode optar por processar eventos por *hora do evento* usando [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) cláusula em sua consulta. Políticas de fora de ordem e de chegada tardias são aplicáveis somente se você processar eventos por hora do evento. Ao definir essas configurações, considere os requisitos de latência e a exatidão para seu cenário. 

## <a name="what-is-late-arrival-policy"></a>O que é política de entrada tardia?

Às vezes, eventos recebidos com atraso devido a vários motivos. Por exemplo, um evento que chega de 40 segundos atrasado terá a hora do evento = hora de chegada e de 10:00:00 = 10:00:40. Se você definir a política de chegada tardia como 15 segundos, qualquer evento que chegarem mais tarde do que 15 segundos será a ser descartada (não processados pelo Stream Analytics) ou ter seu tempo de eventos ajustado. No exemplo acima, como o evento foi recebido 40 segundos atrasado (mais de um conjunto de políticas), seu tempo de evento será ajustado para o máximo de chegada política 00:10:25 (hora de chegada – valor da política de chegada tardia). Política de entrada tardia padrão é 5 segundos.

## <a name="what-is-out-of-order-policy"></a>O que é a política fora de ordem? 

Eventos também podem chegar fora de ordem. Após a hora do evento é ajustada com base na política de entrada tardia, você também pode optar por automaticamente remover ou ajustar os eventos que estão fora de ordem. Se você definir essa política como 8 segundos, todos os eventos que chegam fora de ordem, mas dentro da janela de 8 segundos são reordenados por hora do evento. Eventos que chegam serão descartados ou ajustados para o valor da política de fora de ordem máximo. Política de fora de ordem padrão é 0 segundos. 

## <a name="adjust-or-drop-events"></a>Ajustar ou descartar eventos

Se os eventos chegam atrasadas ou fora de ordem com base nas políticas que você tenha configurado, você pode descartar esses eventos (não processados pelo Stream Analytics) ou ter seu tempo de eventos ajustado.

Vamos ver um exemplo dessas políticas em ação.
<br> **Política de entrada tardia:** 15 s
<br> **Política de fora de ordem:** 8 segundos

| Evento n º | Hora do Evento | Horário de chegada | System.Timestamp | Explicação |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Evento foi recebido o nível de tolerância atrasados e fora. Portanto, hora do evento obtém ajustada para a tolerância da chegada máximo.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Evento chegou tarde, mas dentro do nível de tolerância. Portanto, a hora do evento não é ajustada.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Evento chegou na hora. Nenhum ajuste necessário.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Eventos recebidos fora de ordem, mas dentro da tolerância de 8 segundos. Então, hora do evento não é ajustada. Para fins de análise, esse evento será considerado que o anterior o número do evento 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Evento foi recebido tolerância fora de ordem e fora de 8 segundos. Assim, a hora do evento é ajustada para máximo de tolerância fora de ordem. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Essas configurações podem atrasar a saída do meu trabalho? 

Sim. Por padrão, a política fora de ordem é definida como zero (00 minutos e 00 segundos). Se você alterar o padrão, o primeiro de saída do seu trabalho está atrasada por esse valor (ou superior). 

Se uma das partições de suas entradas não receber eventos, você deve esperar a saída a ser atrasado por valor de política de chegada tardia. Para saber por quê, leia a seção de erro InputPartition abaixo. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Vejo LateInputEvents mensagens no meu log de atividades

Essas mensagens são mostradas para informá-lo que eventos têm chegou tarde e são descartados ou ajustados de acordo com sua configuração. Se você tiver configurado a política de entrada tardia adequadamente, você pode ignorar essas mensagens. 

É um exemplo dessa mensagem: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Vejo InputPartitionNotProgressing em meu log de atividades

A fonte de entrada (Hub de eventos/de IoT Hub) provavelmente tem várias partições. O Azure Stream Analytics produz a saída para t1 de carimbo de data / hora somente depois que todas as partições são combinadas estiverem pelo menos na hora t1. Por exemplo, suponha que a consulta faça a leitura de uma partição do hub de eventos que tem duas partições. Uma das partições, P1, tem eventos até hora t1. A outra partição, P2, tem eventos até hora t1 + x. Em seguida, a saída será produzida até hora t1. Mas, se houver uma partição explícita pela cláusula PartitionId, ambas as partições progredirão de maneira independente. 

Quando várias partições do mesmo fluxo de entrada são combinadas, a tolerância a atrasos é a quantidade máxima de tempo que cada partição espera por novos dados. Se houver uma partição no Hub de eventos, ou se o IoT Hub não receber entradas, a linha do tempo para essa partição não progride até atingir o limite de tolerância de chegada tardia. Isso atrasa a saída pelo limite de tolerância de chegada tardia. Nesses casos, você poderá ver a seguinte mensagem:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Esta mensagem para informar que pelo menos uma partição na sua entrada está vazia e atrasará a sua saída pelo limite de chegada tardia. Para resolver este problema, é recomendável que você a: 1. Certifique-se de que todas as partições do Hub IoT/Hub de eventos receberem a entrada. 2. Use partição por PartitionID cláusula em sua consulta. 

## <a name="next-steps"></a>Próximas etapas
* [Considerações de tratamento de tempo](stream-analytics-time-handling.md)
* [Métricas disponíveis no Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
