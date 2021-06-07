---
title: Usar um hub de eventos do aplicativo Apache Kafka – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre a compatibilidade do Apache Kafka com os Hubs de Eventos do Azure.
ms.topic: article
ms.date: 09/25/2020
ms.openlocfilehash: b0f0da76bba68f8a66695700d530e871cbd35e3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861344"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Usar Hubs de Eventos do Azure de aplicativos Apache Kafka
Os hubs de eventos fornecem um ponto de extremidade compatível com o Apache Kafka® as APIs de produtor e consumidor que podem ser usadas pela maioria dos aplicativos cliente Apache Kafka existentes como uma alternativa à execução de seu próprio cluster Apache Kafka. Os hubs de eventos dão suporte aos clientes de produtor e APIs de consumidor do Apache Kafka na versão 1,0 e posteriores.

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>O que os Hubs de eventos para o Kafka oferece?

O recurso hubs de eventos para Apache Kafka fornece um cabeçalho de protocolo sobre os hubs de eventos do Azure que é compatível com o protocolo com Apache Kafka clientes criados para o Apache Kafka Server versões 1,0 e posteriores e oferece suporte para leitura e gravação em hubs de eventos, que são equivalentes aos tópicos de Apache Kafka. 

Geralmente, você pode usar o ponto de extremidade Kafka dos hubs de eventos de seus aplicativos sem alterações de código em comparação com a configuração existente do Kafka e modificar apenas a configuração: atualizar a cadeia de conexão em configurações para apontar para o ponto de extremidade Kafka exposto pelo hub de eventos em vez de apontar para o cluster Kafka. Em seguida, você poderá começar a transmitir eventos dos aplicativos que usam o protocolo Kafka nos Hubs de Eventos. 

Conceitualmente, Kafka e hubs de eventos são muito semelhantes: ambos são logs particionados compilados para dados de streaming, no qual o cliente controla qual parte do log retido ele deseja ler. A tabela a seguir mapeia conceitos entre Kafka e Hubs de Eventos.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mapeamento conceitual de Kafka e Hub de Eventos

| Conceito de Kafka | Conceito de Hubs de Eventos|
| --- | --- |
| Cluster | Namespace |
| Tópico | Hub de evento |
| Partition | Partition|
| Grupo de Consumidores | Grupo de Consumidores |
| Deslocamento | Deslocamento|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>Principais diferenças entre Apache Kafka e hubs de eventos

Embora [Apache Kafka](https://kafka.apache.org/) seja o software que você normalmente precisa instalar e operar, os hubs de eventos são um serviço totalmente gerenciado e nativo da nuvem. Não há servidores, discos ou redes para gerenciar e monitorar e nenhum corretor a considerar ou configurar, nunca. Você cria um namespace, que é um ponto de extremidade com um nome de domínio totalmente qualificado e, em seguida, cria hubs de eventos (tópicos) dentro desse namespace. 

Para obter mais informações sobre namespaces e Hubs de Eventos, consulte [Recursos de Hubs de Eventos](event-hubs-features.md#namespace). Como um serviço de nuvem, os hubs de eventos usam um único endereço IP virtual estável como o ponto de extremidade, para que os clientes não precisem saber sobre os agentes ou computadores em um cluster. Embora os hubs de eventos implementem o mesmo protocolo, essa diferença significa que todo o tráfego Kafka de todas as partições é roteado de forma previsível por meio desse ponto de extremidade, em vez de exigir acesso de firewall para todos os agentes de um cluster.   

A escala nos hubs de eventos é controlada por quantas unidades de taxa de transferência você compra, com cada unidade de produtividade entitling a 1 megabyte por segundo ou 1000 eventos por segundo de entrada e duas vezes esse volume na saída. Os hubs de eventos podem escalar verticalmente unidades de produtividade automaticamente quando você atingir o limite de taxa de transferência se usar o recurso de [inflar automaticamente](event-hubs-auto-inflate.md) ; Esse recurso funciona também funciona com o suporte do protocolo Apache Kafka.  

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>Está Apache Kafka a solução certa para sua carga de trabalho?

Vindo da criação de aplicativos usando Apache Kafka, também será útil entender que os hubs de eventos do Azure fazem parte de uma frota de serviços que também inclui o [barramento de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md)e a [grade de eventos do Azure](../event-grid/overview.md). 

Embora alguns provedores de distribuições comerciais de Apache Kafka possam sugerir que Apache Kafka seja uma loja única para todas as suas necessidades de plataforma de mensagens, a realidade é que Apache Kafka não implementa, por exemplo, o padrão de fila de [consumidor concorrente](/azure/architecture/patterns/competing-consumers) , o não tem suporte para  [publicação-assinatura](/azure/architecture/patterns/publisher-subscriber) em um nível que permite que os assinantes acessem as mensagens de entrada com base em regras avaliadas pelo servidor, além de deslocamentos simples, e não tem recursos para acompanhar o ciclo de vida de um trabalho iniciado por uma mensagem ou sidelining mensagens com falha em uma fila de mensagens mortas, todas elas são fundamentais para muitos cenários de sistema de mensagem corporativo.

Para entender as diferenças entre padrões e qual padrão é melhor coberto por qual serviço, examine as [Opções de mensagens assíncronas no guia do Azure](/azure/architecture/guide/technology-choices/messaging) . Como usuário Apache Kafka, você pode descobrir que os caminhos de comunicação que você tem até agora com o Kafka, podem ser percebidos com complexidade muito menos básica e, ainda, com recursos mais poderosos usando a grade de eventos ou o barramento de serviço. 

Se você precisar de recursos específicos do Apache Kafka que não estão disponíveis por meio dos hubs de eventos para Apache Kafka interface ou se o seu padrão de implementação exceder as [cotas de hubs de eventos](event-hubs-quotas.md), você também poderá executar um [cluster de Apache Kafka nativo no Azure HDInsight](../hdinsight/kafka/apache-kafka-introduction.md).  

## <a name="security-and-authentication"></a>Segurança e autenticação
Sempre que você publica ou consome eventos de um hub de eventos para Kafka, seu cliente está tentando acessar os recursos dos hubs de eventos. Você deseja garantir que os recursos sejam acessados usando uma entidade autorizada. Ao usar Apache Kafka protocolo com seus clientes, você pode definir sua configuração para autenticação e criptografia usando os mecanismos SASL. Ao usar os hubs de eventos para o Kafka, é necessário ter criptografia TLS (pois todos os dados em trânsito com hubs de eventos são criptografados como TLS). Isso pode ser feito especificando a opção SASL_SSL no arquivo de configuração. 

Os hubs de eventos do Azure fornecem várias opções para autorizar o acesso aos seus recursos seguros. 

- OAuth 2.0
- Assinatura de acesso compartilhado (SAS)

#### <a name="oauth-20"></a>OAuth 2.0
Os hubs de eventos se integram com o Azure Active Directory (Azure AD), que fornece um servidor de autorização centralizada compatível com **OAuth 2,0** . Com o Azure AD, você pode usar o Azure RBAC (controle de acesso baseado em função) para conceder permissões refinadas às suas identidades de cliente. Você pode usar esse recurso com os clientes do Kafka especificando **SASL_SSL** para o protocolo e  **OAUTHBEARER** para o mecanismo. Para obter detalhes sobre as funções e os níveis do Azure para o acesso ao escopo, consulte [autorizar o acesso com o Azure ad](authorize-access-azure-active-directory.md).

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>SAS (Assinatura de Acesso Compartilhado)
Os hubs de eventos também fornecem as **SAS (assinaturas de acesso compartilhado)** para acesso delegado aos hubs de eventos para recursos Kafka. A autorização de acesso usando o mecanismo baseado em token do OAuth 2,0 fornece segurança superior e facilidade de uso sobre SAS. As funções internas também podem eliminar a necessidade de autorização baseada em ACL, que deve ser mantida e gerenciada pelo usuário. Você pode usar esse recurso com os clientes do Kafka especificando **SASL_SSL** para o protocolo e **sem formatação** para o mecanismo. 

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Substitua `{YOUR.EVENTHUBS.CONNECTION.STRING}` pela cadeia de conexão do seu namespace dos Hubs de Eventos. Para ver as instruções sobre como obter uma cadeia de conexão, confira [Obter cadeia de conexão para Hubs de Eventos](event-hubs-get-connection-string.md). Aqui está um exemplo de configuração: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

> [!NOTE]
> Ao usar a autenticação SAS com clientes Kafka, as conexões estabelecidas não são desconectadas quando a chave SAS é regenerada. 


#### <a name="samples"></a>Exemplos 
Para obter um **tutorial** com as instruções detalhadas para criar um hub de eventos e acessá-lo usando SAS ou OAuth, consulte [início rápido: streaming de dados com hubs de eventos usando o protocolo Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Para obter mais **exemplos** que mostram como usar o OAuth com hubs de eventos para Kafka, consulte [exemplos no GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features"></a>Outros recursos de hubs de eventos 

O recurso hubs de eventos para Apache Kafka é um dos três protocolos disponíveis simultaneamente nos hubs de eventos do Azure, complementando HTTP e AMQP. Você pode escrever com qualquer um desses protocolos e ler qualquer outro, para que seus produtores de Apache Kafka atuais possam continuar publicando via Apache Kafka, mas seu leitor pode se beneficiar da integração nativa com a interface AMQP dos hubs de eventos, como Azure Stream Analytics ou Azure Functions. Inversamente, você pode integrar rapidamente os hubs de eventos do Azure em redes de roteamento AMQP como um ponto de extremidade de destino e, ainda assim, ler dados por meio de integrações de Apache Kafka.  

Além disso, os recursos de hubs de eventos, como [captura](event-hubs-capture-overview.md), que permitem arquivamento de longo prazo extremamente eficiente por meio do armazenamento de BLOBs do Azure e do Azure data Lake Storage, e a [recuperação de desastres geográficos](event-hubs-geo-dr.md) também funciona com os hubs de eventos para o recurso Kafka.

## <a name="apache-kafka-feature-differences"></a>Diferenças de recursos de Apache Kafka 

O objetivo dos hubs de eventos para o Apache Kafka é fornecer acesso aos recursos do hub de eventos do Azure aos aplicativos que estão bloqueados na API do Apache Kafka e, de outra forma, teria de ser apoiado por um cluster Apache Kafka. 

Conforme explicado [acima](#is-apache-kafka-the-right-solution-for-your-workload), a frota de mensagens do Azure fornece cobertura avançada e robusta para uma infinidade de cenários de mensagens e, embora os recursos a seguir não tenham suporte no momento por meio do suporte de hubs de eventos para a API Apache Kafka, indicamos onde e como o recurso desejado está disponível.

### <a name="transactions"></a>Transactions

O [barramento de serviço do Azure](../service-bus-messaging/service-bus-transactions.md) tem suporte de transação robusto que permite receber e liquidar mensagens e sessões ao enviar mensagens de saída resultantes do processamento de mensagens para várias entidades de destino sob a proteção de consistência de uma transação. O conjunto de recursos não só permite o processamento exatamente uma vez de cada mensagem em uma sequência, mas também evita o risco de outro consumidor reprocessar inadvertidamente as mesmas mensagens como seria o caso com Apache Kafka. O barramento de serviço é o serviço recomendado para cargas de trabalho de mensagens transacionais.

### <a name="compression"></a>Compactação

O recurso de [compactação](https://cwiki.apache.org/confluence/display/KAFKA/Compression) do lado do cliente do Apache Kafka compacta um lote de várias mensagens em uma única mensagem no lado do produtor e descompacta o lote no lado do consumidor. O agente de Apache Kafka trata o lote como uma mensagem especial.

Esse recurso é fundamentalmente em chances com o modelo multiprotocolo dos hubs de eventos do Azure, que permite que as mensagens, mesmo aquelas enviadas em lotes, sejam individualmente recuperáveis do agente e por meio de qualquer protocolo. 

A carga de qualquer evento do hub de eventos é um fluxo de bytes e o conteúdo pode ser compactado com um algoritmo de sua escolha. O formato de codificação Apache Avro dá suporte à compactação nativamente.

### <a name="log-compaction"></a>Compactação de log

Apache Kafka o compactação de log é um recurso que permite remover todos, exceto o último registro de cada chave de uma partição, que efetivamente transforma um tópico de Apache Kafka em um repositório de chave-valor em que o último valor adicionado substitui o anterior. Este recurso não está implementado atualmente pelos hubs de eventos do Azure. O padrão de repositório de valor-chave, mesmo com atualizações frequentes, tem suporte muito melhor pelos serviços de banco de dados como [Azure Cosmos DB](../cosmos-db/introduction.md). Consulte o tópico [projeção de log](event-hubs-federation-overview.md#log-projections) nas diretrizes da Federação dos hubs de eventos para obter mais detalhes. 

### <a name="kafka-streams"></a>Kafka Streams

O Kafka streams é uma biblioteca de cliente para o Stream Analytics que faz parte do Apache Kafka projeto de software livre, mas é separado do Apache Kafka agente de fluxo de eventos. 

O motivo mais comum que os clientes dos hubs de eventos do Azure solicitam suporte ao Kafka streams é porque eles estão interessados no produto "ksqlDB" de seu próprio mundo. "ksqlDB" é um projeto de origem compartilhado proprietário que é [licenciado de modo](https://github.com/confluentinc/ksql/blob/master/LICENSE) que nenhum fornecedor "oferecendo software como serviço, plataforma como serviço, infraestrutura como serviço ou outros serviços online semelhantes que conpetem com produtos ou serviços confluentes" tem permissão para usar ou oferecer suporte a "ksqlDB". Praticamente, se você usar ksqlDB, você deve operar o Kafka sozinho ou deve usar as ofertas de nuvem do confluente. Os termos de licenciamento também podem afetar os clientes do Azure que oferecem serviços para uma finalidade excluída pela licença.

Autônomo e sem ksqlDB, os fluxos de Kafka têm menos recursos do que muitas estruturas e serviços alternativos, a maioria dos quais têm interfaces SQL de streaming internas e todas as quais se integram com os hubs de eventos do Azure hoje:

- [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)
- [Análise do Azure Synapse (via captura de hubs de eventos)](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](/azure/databricks/scenarios/databricks-stream-from-eventhubs)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- [Apache Spark](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink](event-hubs-kafka-flink-tutorial.md)
- [Fluxos de Akka](event-hubs-kafka-akka-streams-tutorial.md)

Os serviços e as estruturas listadas geralmente podem adquirir fluxos de eventos e dados de referência diretamente de um conjunto variado de fontes por meio de adaptadores. Os fluxos de Kafka só podem adquirir dados de Apache Kafka e seus projetos de análise são, portanto, bloqueados no Apache Kafka. Para usar dados de outras fontes, é necessário primeiro importar dados para Apache Kafka com a estrutura do Kafka Connect.

Se você precisar usar o Kafka streams Framework no Azure, [Apache Kafka no HDInsight](../hdinsight/kafka/apache-kafka-introduction.md) fornecerá essa opção. Apache Kafka no HDInsight fornece controle total sobre todos os aspectos de configuração do Apache Kafka, ao mesmo tempo em que está totalmente integrado a vários aspectos da plataforma Azure, desde o posicionamento do domínio de falha/atualização até o isolamento de rede até o monitoramento da integração. 

## <a name="next-steps"></a>Próximas etapas
Este artigo forneceu uma introdução aos Hubs de Eventos para Kafka. Para saber mais, confira [Guia do desenvolvedor do Apache Kafka para Hubs de Eventos do Azure](apache-kafka-developer-guide.md).