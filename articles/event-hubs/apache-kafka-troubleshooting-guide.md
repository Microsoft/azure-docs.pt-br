---
title: Solucionar problemas com os hubs de eventos do Azure para Apache Kafka
description: Este artigo mostra como solucionar problemas com os hubs de eventos do Azure para Apache Kafka
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e32e02947b9f004755381d562fd3f3c897b70674
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90061420"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Guia de solução de problemas Apache Kafka para hubs de eventos
Este artigo fornece dicas de solução de problemas que você pode encontrar ao usar os hubs de eventos para Apache Kafka. 

## <a name="server-busy-exception"></a>Exceção do servidor ocupado
Você pode receber a exceção do servidor ocupado devido à limitação do Kafka. Com os clientes AMQP, os hubs de eventos retornam imediatamente uma exceção de **servidor ocupado** na limitação de serviço. É equivalente a uma mensagem "tentar novamente mais tarde". No Kafka, as mensagens são atrasadas antes de serem concluídas. O comprimento do atraso é retornado em milissegundos como `throttle_time_ms` na resposta de produzir/buscar. Na maioria dos casos, essas solicitações atrasadas não são registradas como exceções de servidor ocupado em painéis de hubs de eventos. Em vez disso, o `throttle_time_ms` valor da resposta deve ser usado como um indicador de que a taxa de transferência excedeu a cota provisionada.

Se o tráfego for excessivo, o serviço terá o seguinte comportamento:

- Se o atraso da solicitação de produção exceder o tempo limite da solicitação, os hubs de eventos retornarão o código de erro de **violação**
- Se o atraso da solicitação de busca exceder o tempo limite da solicitação, os hubs de eventos registrarão a solicitação como limitada e responderão com um conjunto vazio de registros e nenhum código de erro.

Os [clusters dedicados](event-hubs-dedicated-overview.md) não têm mecanismos de limitação. Você está livre para consumir todos os seus recursos de cluster.

## <a name="no-records-received"></a>Nenhum registro recebido
Você pode ver que os consumidores não estão recebendo registros e rebalanceando constantemente. Nesse cenário, os consumidores não obtêm nenhum registro e reequilibram constantemente. Não há exceção ou erro quando isso acontece, mas os logs de Kafka mostrarão que os consumidores estão presos tentando reingressar no grupo e atribuir partições. Há algumas causas possíveis:

- Verifique se seu `request.timeout.ms` é pelo menos o valor recomendado de 60000 e `session.timeout.ms` se é pelo menos o valor recomendado de 30000. Ter essas configurações muito baixas pode causar tempos limite do consumidor, o que causa recargas (que, em seguida, causa mais tempos limite, o que causa mais rebalanceamento e assim por diante) 
- Se sua configuração corresponder a esses valores recomendados e você ainda estiver vendo o rebalanceamento constante, sinta-se à vontade para abrir um problema (certifique-se de incluir toda a configuração no problema para que possamos ajudar a depurar)!

## <a name="compressionmessage-format-version-issue"></a>Problema de versão de formato de mensagem/compactação
O Kafka dá suporte à compactação e os hubs de eventos para o Kafka atualmente não. Os erros que mencionam uma versão de formato de mensagem (por exemplo, `The message format version on the broker does not support the request.` ) são causados quando um cliente tenta enviar mensagens Kafka compactadas para nossos agentes.

Se forem necessários dados compactados, compactar os dados antes de enviá-los aos agentes e descompactar após o recebimento é uma solução alternativa válida. O corpo da mensagem é apenas uma matriz de bytes para o serviço, portanto, a compactação/descompactação do lado do cliente não causará problemas.

## <a name="unknownserverexception"></a>UnknownServerException
Você pode receber um UnknownServerException de bibliotecas de cliente do Kafka semelhante ao exemplo a seguir: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Abra um tíquete com o suporte da Microsoft.  O log de nível de depuração e os carimbos de data/hora de exceção em UTC são úteis para depurar o problema. 

## <a name="other-issues"></a>Outros problemas
Verifique os itens a seguir se você vir problemas ao usar o Kafka nos hubs de eventos.

- **Firewall bloqueando o tráfego** -Verifique se a porta **9093** não está bloqueada pelo firewall.
- **TopicAuthorizationException** -as causas mais comuns dessa exceção são:
    - Uma grafia na cadeia de conexão no arquivo de configuração ou
    - Tentando usar os hubs de eventos para Kafka em um namespace de camada básica. Os hubs de eventos para o recurso Kafka [só têm suporte para namespaces de camada padrão e dedicados](https://azure.microsoft.com/pricing/details/event-hubs/).
- **Incompatibilidade de versão do Kafka** -hubs de eventos para ecossistemas Kafka dão suporte às versões 1,0 e posteriores do Kafka. Alguns aplicativos que usam o Kafka versão 0,10 e posterior podem ocasionalmente funcionar devido à compatibilidade com versões anteriores do protocolo Kafka, mas é altamente recomendável em relação ao uso da versão antiga da API. As versões 0,9 e anteriores do Kafka não dão suporte aos protocolos SASL necessários e não podem se conectar aos hubs de eventos.
- **Codificações estranhas em cabeçalhos AMQP ao consumir com Kafka** -ao enviar eventos para um hub de eventos em AMQP, quaisquer cabeçalhos de carga AMQP são serializados na codificação AMQP. Os consumidores de Kafka não desserializam os cabeçalhos de AMQP. Para ler valores de cabeçalho, decodifique manualmente os cabeçalhos AMQP. Como alternativa, você pode evitar o uso de cabeçalhos AMQP se souber que estará consumindo por meio do protocolo Kafka. Para obter mais informações, consulte [este problema do GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56).
- **Autenticação SASL** -obter sua estrutura para cooperar com o protocolo de autenticação SASL exigido pelos hubs de eventos pode ser mais difícil do que atender aos olhos. Veja se você pode solucionar problemas de configuração usando os recursos de sua estrutura na autenticação SASL. 

## <a name="limits"></a>limites
Apache Kafka vs. Kafka de hubs de eventos. Na maior parte, a interface Kafka dos hubs de eventos do Azure tem os mesmos padrões, propriedades, códigos de erro e comportamento geral que Apache Kafka faz. As instâncias que esses dois explicitamente diferem (ou onde os hubs de eventos impõem um limite de que Kafka não) estão listadas abaixo:

- O comprimento máximo da `group.id` propriedade é de 256 caracteres
- O tamanho máximo de `offset.metadata.max.bytes` é de 1024 bytes
- As confirmações de deslocamento são limitadas a 4 chamadas/segundo por partição com um tamanho de log interno máximo de 1 MB


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os Hubs de Eventos e Hubs de Eventos para o Kafka, confira os artigos a seguir:  

- [Guia do desenvolvedor de Apache Kafka para hubs de eventos](apache-kafka-developer-guide.md)
- [Guia de migração de Apache Kafka para hubs de eventos](apache-kafka-migration-guide.md)
- [Perguntas frequentes-hubs de eventos para Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Configurações recomendadas](apache-kafka-configurations.md)
