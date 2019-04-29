---
title: Telemetria dos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da telemetria dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 8e8b493881662483e66dd835d1cc68a471b18454
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60545513"
---
# <a name="azure-media-services-telemetry"></a>Telemetria dos Serviços de Mídia do Azure  


> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [diretrizes de migração da v2 para v3](../latest/migrate-from-v2-to-v3.md)

O AMS (Serviços de Mídia do Azure) permite que você acesse dados de telemetria/métricas de seus serviços. A versão atual do AMS permite a coleta de dados de telemetria para entidades **Channel**, **StreamingEndpoint** e **arquivamento** dinâmicas. 

A telemetria é gravada em uma tabela de armazenamento em uma conta de Armazenamento do Azure especificada por você (normalmente, você usaria a conta de armazenamento associada à sua conta do AMS). 

O sistema de telemetria não gerencia a retenção de dados. Você pode remover os dados de telemetria antigos excluindo as tabelas de armazenamento.

Este tópico discute como configurar e consumir a telemetria do AMS.

## <a name="configuring-telemetry"></a>Configurar a telemetria

Você pode configurar a telemetria com granularidade no nível de componente. Há dois níveis de detalhe, "Normal" e "Detalhado". No momento, os dois níveis retornam as mesmas informações. Recomendamos o uso do nível "Normal. 

Os tópicos a seguir mostram como habilitar a telemetria:

[Habilitar a telemetria com .NET](media-services-dotnet-telemetry.md) 

[Habilitar a telemetria com REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Consumindo informações de telemetria

A telemetria é gravada em uma Tabela de Armazenamento do Azure na conta de armazenamento especificada por você durante a configuração da telemetria para a conta dos Serviços de Mídia. Esta seção descreve as tabelas de armazenamento para as métricas.

É possível consumir os dados de telemetria das seguintes maneiras:

- Leia os dados diretamente no Armazenamento de Tabelas do Azure (por exemplo, usando o SDK de Armazenamento). Para obter a descrição das tabelas de armazenamento de telemetria, confira **Consumindo informações de telemetria**[neste](https://msdn.microsoft.com/library/mt742089.aspx) tópico.

Ou

- Use o suporte no SDK para .NET dos Serviços de Mídia para leitura dos dados de armazenamento, conforme descrito [neste](media-services-dotnet-telemetry.md) tópico. 


O esquema de telemetria descrito a seguir foi projetado para oferecer bom desempenho dentro dos limites de Armazenamento de Tabelas do Azure:

- Os dados são particionados por ID da conta e ID do serviço a fim de permitir que a telemetria de cada serviço seja consultada independentemente.
- As partições contêm a data para dar um limite superior razoável ao tamanho da partição.
- As chaves de linha estão em ordem de tempo inversa para permitir que os itens de telemetria mais recentes sejam consultados para um determinado serviço.

Isso permite a eficiência de muitas consultas comuns:

- Download paralelo e independente de dados para serviços separados.
- Recuperação de todos os dados de um serviço específico em um intervalo de datas.
- Recuperação dos dados mais recentes de um serviço.

### <a name="telemetry-table-storage-output-schema"></a>Esquema de saída do armazenamento de tabelas de telemetria

Os dados de telemetria são armazenados de forma agregada em uma tabela, "TelemetryMetrics20160321", em que "20160321" é a data de criação da tabela. O sistema de telemetria cria uma tabela separada para cada dia novo com base em 00:00 UTC. A tabela é usada para armazenar valores recorrentes, como taxa de bits de ingestão dentro de um determinado intervalo de tempo, bytes enviados etc. 

Propriedade|Value|Exemplos/notas
---|---|---
PartitionKey|{account ID}_{entity ID}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>A ID da conta está incluída na chave de partição para simplificar os fluxos de trabalho nos quais várias contas dos Serviços de Mídia gravam na mesma conta de armazenamento.
RowKey|{seconds to midnight}_{random value}|01688_00199<br/><br/>A chave de linha começa com o número de segundos para meia-noite a fim de permitir n consultas de estilo superior dentro de uma partição. Para saber mais, confira [este artigo](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern). 
Timestamp|Data/hora|Carimbo de hora automática da tabela do Azure 2016-09-09T22:43:42.241Z
Type|O tipo de entidade que fornece dados de telemetria|Channel/StreamingEndpoint/Archive<br/><br/>Tipo de evento é apenas um valor de cadeia de caracteres.
NOME|O nome do evento de telemetria|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|A hora na qual o evento de telemetria ocorreu (UTC)|2016-09-09T22:42:36.924Z<br/><br/>A hora observada é fornecida pela entidade que envia a telemetria (por exemplo, um canal). Pode haver problemas de sincronização de hora entre os componentes para que esse valor seja aproximado
ServiceID|{service ID}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Propriedades específicas da entidade|Conforme definido pelo evento|StreamName: stream1, Bitrate 10123, …<br/><br/>As propriedades restantes são definidas para o tipo de evento específico. O conteúdo da Tabela do Azure são pares de chave/valor.  (ou seja, linhas diferentes na tabela têm conjuntos diferentes de propriedades).

### <a name="entity-specific-schema"></a>Esquema específico à entidade

Há três tipos de entradas de dados telemétricos específicos à entidade, cada um deles enviado com a frequência a seguir:

- Pontos de extremidade de streaming: Cada 30 segundos
- Canais ao vivo: A cada minuto
- Arquivamento dinâmico: A cada minuto

**Ponto de Extremidade de Streaming**

Propriedade|Value|Exemplos
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Carimbo de hora automática da Tabela do Azure 2016-09-09T22:43:42.241Z
Type|Type|StreamingEndpoint
NOME|NOME|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|ID do Serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
HostName|Nome do host do ponto de extremidade|builddemoserver.origin.mediaservices.windows.net
StatusCode|Status HTTP dos registros|200
ResultCode|Detalhe do código do resultado|S_OK
RequestCount|Solicitação total da agregação|3
BytesSent|Bytes agregados enviados|2987358
ServerLatency|Latência média do servidor (incluindo armazenamento)|129
E2ELatency|Latência média de ponta a ponta|250

**Canal dinâmico**

Propriedade|Value|Exemplos/notas
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Carimbo de hora automática da tabela do Azure 2016-09-09T22:43:42.241Z
Type|Type|Canal
NOME|NOME|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|ID do Serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Tipo de faixa de áudio/vídeo/texto|vídeo/áudio
TrackName|Nome da faixa|vídeo/áudio_1
Bitrate|Controlar taxa de bits|785000
CustomAttributes||   
IncomingBitrate|Taxa de bits de entrada real|784548
OverlapCount|Sobreposição na ingestão|0
DiscontinuityCount|Descontinuidade para controle|0
LastTimestamp|Carimbo de hora dos últimos dados ingeridos|1800488800
NonincreasingCount|Contagem de fragmentos descartados devido a um carimbo de data e hora estático|2
UnalignedKeyFrames|Se recebemos fragmentos (em níveis de qualidade) quando os quadros-chave não estão alinhados |True
UnalignedPresentationTime|Se recebemos fragmentos (em níveis/controle de qualidade) quando o tempo de apresentação não estiver alinhado|True
UnexpectedBitrate|True, se a taxa de bits calculada/real para controle de áudio/vídeo for maior do que 40.000 bps, e IncomingBitrate == 0 OU IncomingBitrate e actualBitrate diferirem em 50% |True
Healthy|True, se <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> são todos 0|Verdadeiro<br/><br/>Healthy é uma função composta que retorna falso quando qualquer uma das seguintes condições contiverem:<br/><br/>- OverlapCount > 0<br/>- DiscontinuityCount > 0<br/>- NonincreasingCount > 0<br/>- UnalignedKeyFrames == True<br/>- UnalignedPresentationTime == True<br/>- UnexpectedBitrate == True

**Arquivamento dinâmico**

Propriedade|Value|Exemplos/notas
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Carimbo de hora automática da tabela do Azure 2016-09-09T22:43:42.241Z
Type|Type|Arquivo
NOME|NOME|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|ID do Serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|URL do programa|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
TrackName|Nome da faixa|áudio_1
TrackType|Tipo da faixa|Áudio/vídeo
CustomAttribute|Cadeia de caracteres hexadecimal que diferencia faixas diferentes com o mesmo nome e a taxa de bits (ângulo da câmeras múltiplas)|
Bitrate|Controlar taxa de bits|785000
Healthy|True, se FragmentDiscardedCount == 0 && ArchiveAcquisitionError == False|True (esses dois valores não estão presentes na métrica, mas estão presentes no evento de origem)<br/><br/>Healthy é uma função composta que retorna falso quando qualquer uma das seguintes condições contiverem:<br/><br/>- FragmentDiscardedCount > 0<br/>- ArchiveAcquisitionError == True

## <a name="general-qa"></a>Perguntas e respostas gerais

### <a name="how-to-consume-metrics-data"></a>Como consumir dados de métrica?

Dados de métricas são armazenados como uma série de Tabelas do Azure na conta de armazenamento do cliente. Esses dados podem ser consumidos usando as seguintes ferramentas:

- SDK do AMS
- Gerenciador de Armazenamento do Microsoft Azure (oferece suporte à exportação para o formato de valores separados por vírgulas e processada no Excel)
- API REST

### <a name="how-to-find-average-bandwidth-consumption"></a>Como localizar o consumo médio de largura de banda?

O consumo médio de largura de banda é a média de BytesSent um durante um período.

### <a name="how-to-define-streaming-unit-count"></a>Como definir a contagem de unidades de streaming?

A contagem de unidades de streaming pode ser definida como a taxa de transferência de pico de pontos de extremidade de streaming do serviço dividido pela taxa de transferência de pico de um ponto de extremidade de streaming. A taxa de transferência de pico utilizável de um ponto de extremidade de streaming é 160 Mbps.
Por exemplo, vamos supor que a taxa de transferência de pico do serviço de um cliente é de 40 MBps (o valor máximo de BytesSent durante um período). Em seguida, a contagem de unidades de streaming é igual a (40 MBps)*(8 bits/byte)/(160 Mbps) = 2 unidades de streaming.

### <a name="how-to-find-average-requestssecond"></a>Como localizar a média de solicitações por segundo?

Para localizar o número médio de solicitações por segundo, calcule o número médio de solicitações (RequestCount) durante um período.

### <a name="how-to-define-channel-health"></a>Como definir a integridade do canal?

A integridade do canal pode ser definida como uma função booliana composta, que resulta em False quando qualquer uma das condições a seguir existirem:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames == True 
- UnalignedPresentationTime == True 
- UnexpectedBitrate == True


### <a name="how-to-detect-discontinuities"></a>Como detectar descontinuidades?

Para detectar descontinuidades, localize todas as entradas de dados do Canal nas quais DiscontinuityCount > 0. O carimbo de hora ObservedTime correspondente indica os horários nos quais ocorreram as descontinuidades.

### <a name="how-to-detect-timestamp-overlaps"></a>Como detectar sobreposições de carimbo de hora?

Para detectar sobreposições de carimbo de hora, localize todas as entradas de dados de Canal onde OverlapCount > 0. O carimbo de hora ObservedTime correspondente indica os horários nos quais ocorreram as sobreposições de carimbo de hora.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Como encontrar falhas solicitação de streaming e seus motivos?

Para encontrar falhas de solicitação de streaming e seus motivos, localize todas as entradas de dados de Ponto de extremidade de streaming nas quais ResultCode não é igual a S_OK. O campo StatusCode correspondente indica o motivo da falha de solicitação.

### <a name="how-to-consume-data-with-external-tools"></a>Como consumir dados com ferramentas externas?

Dados telemétricos podem ser processados e visualizados com as seguintes ferramentas:

- PowerBI
- Application Insights
- Azure Monitor (anteriormente Shoebox)
- Painel dinâmico do AMS
- Portal do Azure (pendente versão)

### <a name="how-to-manage-data-retention"></a>Como gerenciar a retenção de dados?

O sistema de telemetria não oferece gerenciamento de retenção de dados ou exclusão automática de registros antigos. Portanto, você precisa gerenciar e excluir manualmente os registros antigos da tabela de armazenamento. Você pode consultar o SDK de armazenamento para isso.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
