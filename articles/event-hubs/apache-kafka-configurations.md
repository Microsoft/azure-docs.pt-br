---
title: Configurações recomendadas para clientes Apache Kafka-hubs de eventos do Azure
description: Este artigo fornece configurações de Apache Kafka recomendadas para clientes que interagem com os hubs de eventos do Azure para Apache Kafka.
ms.topic: reference
ms.date: 03/03/2021
ms.openlocfilehash: be009aae41b2cb26ab02fdbe14bc4e18311ad235
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042344"
---
# <a name="recommended-configurations-for-apache-kafka-clients"></a>Configurações recomendadas para clientes Apache Kafka
Aqui estão as configurações recomendadas para usar os hubs de eventos do Azure de Apache Kafka aplicativos cliente. 

## <a name="java-client-configuration-properties"></a>Propriedades de configuração do cliente Java

### <a name="producer-and-consumer-configurations"></a>Configurações de produtor e consumidor

Propriedade | Valores recomendados | Intervalo permitido | Observações
---|---:|-----:|---
`metadata.max.age.ms` | 180000 (aproximado) | < 240000 | Pode ser reduzido para pegar as alterações de metadados mais cedo.
`connections.max.idle.ms`   | 180000 | < 240000 | O Azure fecha a entrada TCP ocioso > 240.000 MS, o que pode resultar no envio de conexões inativas (mostradas como lotes expirados devido ao tempo limite de envio).

### <a name="producer-configurations-only"></a>Somente configurações de produtor
As configurações do produtor podem ser encontradas [aqui](https://kafka.apache.org/documentation/#producerconfigs).

Propriedade | Valores recomendados | Intervalo permitido | Observações
---|---:|---:|---
`max.request.size` | 1.000.000 | < 1046528 | O serviço fechará conexões se solicitações maiores que 1.046.528 bytes forem enviadas.  *Esse valor **deve** ser alterado e causará problemas em cenários de produção de alta taxa de transferência.*
`retries` | > 0 | | Pode ser necessário aumentar o valor de delivery.timeout.ms, consulte a documentação.
`request.timeout.ms` | 30000.. 60000 | > 20000| O EH será padronizado internamente para um mínimo de 20.000 MS.  *Embora as solicitações com valores de tempo limite inferiores sejam aceitas, o comportamento do cliente não é garantido.*
`metadata.max.idle.ms` | 180000 | > 5000 | Controla por quanto tempo o produtor armazenará em cache os metadados de um tópico que está ocioso. Se o tempo decorrido desde que um tópico foi produzido pela última vez exceder a duração de ociosidade de metadados, os metadados do tópico serão esquecidos e o próximo acesso a ele forçará uma solicitação de busca de metadados.
`linger.ms` | > 0 | | Para cenários de alta taxa de transferência, o valor remanescente deve ser igual ao valor tolerável mais alto para tirar proveito do envio em lote.
`delivery.timeout.ms` | | | Defina de acordo com a fórmula ( `request.timeout.ms`  +  `linger.ms` ) * `retries` .
`compression.type` | `none` | | Não há suporte para a compactação no momento.

### <a name="consumer-configurations-only"></a>Somente configurações de consumidor
As configurações do consumidor podem ser encontradas [aqui](https://kafka.apache.org/documentation/#consumerconfigs).

Propriedade | Valores recomendados | Intervalo permitido | Observações
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 | | 3000 é o valor padrão e não deve ser alterado.
`session.timeout.ms` | 30000 |6000.. 300000| Comece com 30000, aumente se estiver vendo rebalanceamento frequente devido a pulsações perdidas.


## <a name="librdkafka-configuration-properties"></a>Propriedades de configuração do librdkafka
O `librdkafka` arquivo de configuração principal ([link](https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md)) contém descrições estendidas para as propriedades abaixo.

### <a name="producer-and-consumer-configurations"></a>Configurações de produtor e consumidor

Propriedade | Valores recomendados | Intervalo permitido | Observações
---|---:|-----:|---
`socket.keepalive.enable` | true | | Necessário se a conexão se espera ociosa.  O Azure fechará a entrada TCP ocioso > 240.000 MS.
`metadata.max.age.ms` | ~ 180000| < 240000 | Pode ser reduzido para pegar as alterações de metadados mais cedo.

### <a name="producer-configurations-only"></a>Somente configurações de produtor

Propriedade | Valores recomendados | Intervalo permitido | Observações
---|---:|-----:|---
`retries` | > 0 | | O padrão é 2. Recomendamos que você mantenha esse valor. 
`request.timeout.ms` | 30000.. 60000 | > 20000| O EH será padronizado internamente para um mínimo de 20.000 MS.  `librdkafka` o valor padrão é 5000, que pode ser problemático. *Embora as solicitações com valores de tempo limite inferiores sejam aceitas, o comportamento do cliente não é garantido.*
`partitioner` | `consistent_random` | Consulte a documentação do librdkafka | `consistent_random` é padrão e melhor.  As chaves vazias e nulas são manipuladas idealmente para a maioria dos casos.
`compression.codec` | `none` || Não há suporte para a compactação no momento.

### <a name="consumer-configurations-only"></a>Somente configurações de consumidor

Propriedade | Valores recomendados | Intervalo permitido | Observações
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 || 3000 é o valor padrão e não deve ser alterado.
`session.timeout.ms` | 30000 |6000.. 300000| Comece com 30000, aumente se estiver vendo rebalanceamento frequente devido a pulsações perdidas.


## <a name="further-notes"></a>Mais observações

Verifique a tabela a seguir de cenários de erro comuns relacionados à configuração.

Sintomas | Problema | Solução
----|---|-----
Falhas de confirmação de deslocamento devido à redistribuição | Seu consumidor está aguardando muito tempo entre chamadas para sondagem () e o serviço está iniciarndo o consumidor do grupo. | Você tem várias opções: <ul><li>Aumentar tempo limite de processamento de sondagem ( `max.poll.interval.ms` )</li><li>Diminuir o tamanho do lote de mensagens para acelerar o processamento</li><li>Melhorar a paralelização de processamento para evitar o bloqueio de consumidor. Poll ()</li></ul> Aplicar alguma combinação dos três é provavelmente mais prudente.
Exceções de rede em alta geração de taxa de transferência | Você está usando o cliente Java + padrão máx. de solicitação. tamanho?  Suas solicitações podem ser muito grandes. | Consulte Configurações de Java acima.

## <a name="next-steps"></a>Próximas etapas
Consulte [Limites, cotas e restrições de assinatura e serviço do Azure](..//azure-resource-manager/management/azure-subscription-service-limits.md) para cotas e limites de todos os serviços do Azure. 
