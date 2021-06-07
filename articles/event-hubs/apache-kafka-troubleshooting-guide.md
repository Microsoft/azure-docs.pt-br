---
title: Solucionar problemas com Hubs de Eventos do Azure para Apache Kafka
description: Este artigo mostra como solucionar problemas com Hubs de Eventos do Azure para Apache Kafka
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a9d4a93f0074f206cd4627913505c66eb6480cbd
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314075"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Guia de solução de problemas do Apache Kafka para os Hubs de Eventos
Este artigo fornece dicas de solução de problemas que você pode encontrar ao usar os Hubs de Eventos para Apache Kafka. 

## <a name="server-busy-exception"></a>Exceção de servidor ocupado
Você pode receber a exceção de Servidor Ocupado devido à limitação do Kafka. Com clientes AMQP, os Hubs de Eventos retornam imediatamente uma exceção de **servidor ocupado** na limitação de serviço. É equivalente a uma mensagem "tente novamente mais tarde". No Kafka, as mensagens são atrasadas antes de serem concluídas. O comprimento do atraso é retornado em milissegundos como `throttle_time_ms` na resposta de produzir/buscar. Na maioria dos casos, essas solicitações atrasadas não são registradas como exceções de servidor ocupado nos dashboards de Hubs de Eventos. Em vez disso, o valor `throttle_time_ms` da resposta deve ser usado como um indicador de que a taxa de transferência excedeu a cota provisionada.

Se o tráfego for excessivo, o serviço terá o seguinte comportamento:

- Se o atraso da solicitação de produção exceder o tempo limite da solicitação, os Hubs de Eventos retornarão o código de erro de **Violação da Política**.
- Se o atraso da solicitação de busca exceder o tempo limite da solicitação, os Hubs de Eventos registrarão a solicitação como limitada e responderão com um conjunto vazio de registros e nenhum código de erro.

Os [clusters dedicados](event-hubs-dedicated-overview.md) não têm mecanismos de limitação. Você está livre para consumir todos os seus recursos de cluster.

## <a name="no-records-received"></a>Nenhum registro recebido
Você pode ver os consumidores não receberem registros e se redistribuírem constantemente. Nesse cenário, os consumidores não obtêm nenhum registro e se redistribuem constantemente. Não há exceção ou erro quando isso acontece, mas os logs do Kafka mostrarão que os consumidores estão presos tentando reingressar no grupo e atribuir partições. Há algumas causas possíveis:

- Verifique se o `request.timeout.ms` tem no mínimo o valor recomendado de 60000 e o `session.timeout.ms` tem no mínimo o valor recomendado de 30000. Ter essas configurações com valores muito baixos pode fazer com que os consumidores atinjam o tempo limite, o que causa redistribuições (que fazem com que mais tempos limites sejam atingidos, o que causa mais redistribuições e assim por diante) 
- Se a sua configuração corresponder a esses valores recomendados e ainda houver uma redistribuição constante, sinta-se à vontade para abrir um problema (inclua toda a configuração no problema para que possamos ajudar a depurar).

## <a name="compressionmessage-format-version-issue"></a>Problema de versão de formato de mensagem/compactação
O Kafka dá suporte à compactação, e os Hubs de Eventos para o Kafka atualmente não fazem isso. Os erros que mencionam uma versão de formato de mensagem (por exemplo, `The message format version on the broker does not support the request.`) são causados quando um cliente tenta enviar mensagens do Kafka compactadas para nossos agentes.

Se forem necessários dados compactados, compactar os dados antes de enviá-los aos agentes e descompactar após o recebimento é uma solução alternativa válida. O corpo da mensagem é apenas uma matriz de bytes para o serviço. Portanto, a compactação/descompactação do lado do cliente não causará problemas.

## <a name="unknownserverexception"></a>UnknownServerException
Você pode receber um UnknownServerException de bibliotecas de cliente do Kafka semelhantes ao seguinte exemplo: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Abra um tíquete com o suporte da Microsoft.  O log de nível de depuração e os carimbos de data/hora de exceção em UTC são úteis para depurar o problema. 

## <a name="other-issues"></a>Outros problemas
Verifique os itens a seguir se você encontrar problemas ao usar o Kafka nos Hubs de Eventos.

- **Firewall bloqueando o tráfego** – verifique se a porta **9093** não está bloqueada pelo firewall.
- **TopicAuthorizationException** – as causas mais comuns dessa exceção incluem:
    - Um erro de grafia na cadeia de conexão no arquivo de configuração ou
    - Tentar usar os Hubs de Eventos para Kafka em um namespace de camada Básica. O recurso Hubs de Eventos para Kafka tem [suporte apenas para os namespaces de camada Padrão e Dedicado](https://azure.microsoft.com/pricing/details/event-hubs/).
- **Incompatibilidade de versão do Kafka** – Hubs de Eventos para ecossistemas Kafka dão suporte às versões 1.0 e posteriores do Kafka. Alguns aplicativos que usam o Kafka versão 0.10 e posterior podem ocasionalmente funcionar devido à compatibilidade com versões anteriores do protocolo Kafka, mas é altamente recomendável não usar versões antigas da API. As versões 0.9 e anteriores do Kafka não dão suporte aos protocolos SASL necessários e não podem se conectar aos Hubs de Eventos.
- **Codificações estranhas em cabeçalhos AMQP ao consumir com Kafka** – ao enviar eventos para um hub de eventos em AMQP, quaisquer cabeçalhos de payload AMQP são serializados na codificação AMQP. Os consumidores do Kafka não desserializam os cabeçalhos do AMQP. Para ler os valores de cabeçalho, decodifique manualmente os cabeçalhos do AMQP. Como alternativa, você poderá evitar o uso de cabeçalhos do AMQP se souber que estará consumindo por meio do protocolo Kafka. Saiba mais neste [tópico do GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56).
- **Autenticação SASL** – obter sua estrutura para cooperar com o protocolo de autenticação SASL exigido pelos Hubs de Eventos poderá ser mais difícil do que parece. Confira se você pode solucionar problemas de configuração usando os recursos da estrutura na autenticação SASL. 

## <a name="limits"></a>Limites
Apache Kafka vs. Hubs de Eventos do Kafka. Na maior parte, a interface Kafka dos Hubs de Eventos do Azure tem os mesmos padrões, propriedades, códigos de erro e comportamento geral que o Apache Kafka. Os casos em que esses dois explicitamente diferem (ou em que os Hubs de Eventos impõem um limite que o Kafka não impõe) estão listados abaixo:

- O comprimento máximo da propriedade `group.id` é de 256 caracteres
- O tamanho máximo do `offset.metadata.max.bytes` é de 1024 bytes
- Os commits de deslocamento são limitados a quatro chamadas/segundo por partição com um tamanho de log interno máximo de 1 MB


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os Hubs de Eventos e Hubs de Eventos para o Kafka, confira os artigos a seguir:  

- [Guia de desenvolvedores do Apache Kafka para os Hubs de Eventos](apache-kafka-developer-guide.md)
- [Guia de migração do Apache Kafka para os Hubs de Eventos](apache-kafka-migration-guide.md)
- [Perguntas frequentes – Hubs de Eventos para Apache Kafka](apache-kafka-frequently-asked-questions.yml)
- [Configurações recomendadas](apache-kafka-configurations.md)
