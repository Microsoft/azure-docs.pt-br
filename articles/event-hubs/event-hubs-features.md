---
title: Visão geral dos recursos - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece detalhes sobre os recursos e a terminologia dos Hubs de Eventos do Azure.
ms.topic: article
ms.date: 02/19/2021
ms.openlocfilehash: 8bb63bfdbeb5b875b1e461fbd93fb48dcbb43054
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739068"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Recursos e terminologia em Hubs de Eventos do Azure

Os Hubs de Evento do Azure é um serviço de processamento de evento escalonável que recebe e processa grandes volumes de eventos e dados, com baixa latência e alta confiabilidade. Confira [O que são Hubs de Eventos?](./event-hubs-about.md) para obter uma visão geral de alto nível.

Este artigo se baseia nas informações do [artigo de visão geral](./event-hubs-about.md) e fornece detalhes técnicos e de implementação sobre recursos e componentes dos Hubs de Eventos.

> [!TIP]
> [O suporte de protocolo para clientes **Apache Kafka**](event-hubs-for-kafka-ecosystem-overview.md)  (versões >= 1,0) fornece pontos de extremidade de rede que habilitam aplicativos criados para usar Apache Kafka com qualquer cliente para usar os hubs de eventos. A maioria dos aplicativos Kafka existentes pode simplesmente ser reconfigurada para apontar para um namespace de Hub de eventos em vez de um servidor de inicialização de cluster Kafka. 
>
>Da perspectiva do custo, do esforço operacional e da confiabilidade, os hubs de eventos do Azure são uma ótima alternativa para implantar e operar seus próprios clusters Kafka e Zookeeper e para ofertas Kafka como serviço não nativas para o Azure. 
>
> Além de obter a mesma funcionalidade básica do agente de Apache Kafka, você também obtém acesso aos recursos do hub de eventos do Azure, como o envio em lote automático e o arquivamento via [captura de hubs de eventos](event-hubs-capture-overview.md), dimensionamento automático e balanceamento, recuperação de desastres, suporte a zona de disponibilidade neutra de custo, integração de rede flexível e segura e suporte a vários protocolos, incluindo o protocolo AMQP-over


## <a name="namespace"></a>Namespace
Um namespace de hubs de eventos fornece pontos de extremidade de rede integrada de DNS e uma variedade de recursos de gerenciamento de integração de rede e controle de acesso, como [filtragem de IP](event-hubs-ip-filtering.md), ponto de extremidade de [serviço de rede virtual](event-hubs-service-endpoints.md)e [link privado](private-link-service.md) e é o contêiner de gerenciamento para uma das várias instâncias de Hub de eventos (ou tópicos, na linguagem Kafka).

## <a name="event-publishers"></a>Editores de eventos

Qualquer entidade que envia dados para um hub de eventos é um *Editor de eventos* (como sinônimos usado com o produtor de *eventos*). Os editores de eventos podem publicar eventos usando HTTPS ou AMQP 1,0 ou o protocolo Kafka. Os editores de eventos usam a autorização baseada em Azure Active Directory com tokens JWT emitidos por OAuth2 ou um token SAS (assinatura de acesso compartilhado) específico ao Hub de eventos.

### <a name="publishing-an-event"></a>Publicar um evento

Você pode publicar um evento via AMQP 1,0, o protocolo Kafka ou HTTPS. O serviço de hubs de eventos fornece a [API REST](/rest/api/eventhub/) e as bibliotecas de cliente [.net](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [JavaScript](event-hubs-node-get-started-send.md)e [go](event-hubs-go-get-started-send.md) para publicar eventos em um hub de eventos. Para outras plataformas e runtimes, você pode usar qualquer cliente AMQP 1.0, como o [Apache Qpid](https://qpid.apache.org/). 

A opção de usar AMQP ou HTTPS é específica para o cenário de uso. O AMQP requer o estabelecimento de um soquete bidirecional persistente, além do TLS (segurança de nível de transporte) ou SSL/TLS. O AMQP tem custos de rede mais altos ao inicializar a sessão; no entanto, o HTTPS requer sobrecarga adicional de TLS para cada solicitação. O AMQP tem um desempenho significativamente maior para editores frequentes e pode atingir latências muito menores quando usado com código de publicação assíncrono.

Você pode publicar eventos individualmente ou em lote. Uma única publicação tem um limite de 1 MB, independentemente de ser um único evento ou um lote. A publicação de eventos maiores que esse limite será rejeitada. 

A taxa de transferência dos hubs de eventos é dimensionada usando partições e alocações de unidades de produtividade (veja abaixo). É uma prática recomendada para os editores permanecerem cientes do modelo de particionamento específico escolhido para um hub de eventos e especificar apenas uma *chave de partição* que seja usada para atribuir eventos relacionados de forma consistente à mesma partição.

![Chaves de partição](./media/event-hubs-features/partition_keys.png)

Os hubs de eventos garantem que todos os eventos que compartilham um valor de chave de partição sejam armazenados juntos e entregues em ordem de chegada. Se forem usadas chaves de partição com políticas de editor, a identidade do editor e o valor da chave de partição devem corresponder. Caso contrário, ocorrerá um erro.

### <a name="event-retention"></a>Retenção de eventos

Os eventos publicados são removidos de um hub de eventos com base em uma política de retenção com base em tempo configurável. Aqui estão alguns pontos importantes:

- O valor **padrão** e o período de retenção **mais curto** possível é **1 dia (24 horas)**.
- Para os hubs de eventos **Standard**, o período de retenção máximo é de **7 dias**. 
- Para os hubs de eventos **dedicados**, o período de retenção máximo é de **90 dias**.
- Se você alterar o período de retenção, ele se aplicará a todas as mensagens, incluindo mensagens que já estão no Hub de eventos. 

> [!NOTE]
> Os hubs de eventos são um mecanismo de fluxo de eventos em tempo real e não são projetados para serem usados em vez de um banco de dados e/ou como um armazenamento permanente para fluxos de eventos infinitamente mantidos. 
> 
> Quanto mais profunda for o histórico de um fluxo de eventos, mais você precisará de índices auxiliares para encontrar uma determinada fatia histórica de um determinado fluxo. A inspeção de cargas de eventos e a indexação não estão dentro do escopo do recurso dos hubs de eventos (ou Apache Kafka). Bancos de dados e repositórios e mecanismos de análise especializados, como [Azure data Lake Store](../data-lake-store/data-lake-store-overview.md), [Azure data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) e [Synapse do Azure](../synapse-analytics/overview-what-is.md) , são, portanto, muito mais adequados para armazenar eventos históricos.
>
> A [captura de hubs de eventos](event-hubs-capture-overview.md) integra-se diretamente com o armazenamento de BLOBs do azure e Azure data Lake Storage e, por meio dessa integração, também permite o fluxo de [eventos diretamente no Azure Synapse](store-captured-data-data-warehouse.md).
>
> Se você quiser usar o padrão de [fornecimento do evento](/azure/architecture/patterns/event-sourcing) para seu aplicativo, você deve alinhar sua estratégia de instantâneo com os limites de retenção dos hubs de eventos. Não se desempenhe para recompilar exibições materializadas de eventos brutos a partir do início do tempo. Com certeza, você teria de alterar essa estratégia depois que seu aplicativo estiver em produção por um tempo e estiver bem utilizado, e seu construtor de projeção precisará fazer a variação por anos de eventos de alteração ao tentar obter as alterações mais recentes e em andamento. 


### <a name="publisher-policy"></a>Política de editor

Os Hubs de Eventos permitem um controle granular sobre os editores de eventos por meio de *políticas do editor*. As políticas do editor são recursos de tempo de execução criado para facilitar um grande número de editores de eventos independentes. Com as políticas do editor, cada editor usa seu próprio identificador exclusivo ao publicar eventos em um hub de eventos usando o mecanismo a seguir:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

Você não precisa criar nomes de editor com antecedência, mas eles devem coincidir com o token SAS usado ao publicar um evento, para garantir identidades de editores independentes. Ao usar as políticas do publicador, o valor **PartitionKey** é definido como o nome do publicador. Para funcionar adequadamente, esses valores devem corresponder.

## <a name="capture"></a>Capturar

A [Captura dos Hubs de Eventos](event-hubs-capture-overview.md) permite que você capture automaticamente os dados de streaming em Hubs de Eventos e salve-os em uma conta de armazenamento de blobs ou em uma conta de serviço do Azure Data Lake de sua escolha. Você pode habilitar a Captura do Portal do Azure e especificar um tamanho mínimo e a janela de tempo para executar a captura. A Captura de Hubs de Eventos permite que você especifique sua própria conta de Armazenamento de Blobs do Azure e o contêiner, ou conta de serviço do Azure Data Lake, uma das quais será usada para armazenar os dados capturados. Os dados capturados são gravados no formato Apache Avro.

## <a name="partitions"></a>Partições
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>Tokens SAS

Os hubs de eventos usam *assinaturas de acesso compartilhado*, que estão disponíveis no namespace e no nível do hub de eventos. Um token SAS é gerado a partir de uma chave de SAS e é um hash SHA de uma URL, codificado em um formato específico. Usando o nome da chave (política) e o token, os Hubs de Evento podem regenerar o hash e, portanto, autenticar o remetente. Normalmente, os tokens SAS para editores de eventos são criados apenas com privilégios de **envio** em um hub de eventos específico. Esse mecanismo de URL de token SAS é a base para a identificação de editor abordada na política do editor. Para saber mais sobre como trabalhar com SAS, confira [Autenticação de assinatura de acesso compartilhado com o Barramento de Serviço](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Consumidores de evento

Qualquer entidade que lê dados de eventos de um hub de eventos é um *consumidor de eventos*. Todos os consumidores de Hubs de Eventos se conectam por meio de sessão do AMQP 1.0, e os eventos são entregues por meio da sessão à medida que são disponibilizados. O cliente não precisa buscar pela disponibilidade de dados.

### <a name="consumer-groups"></a>Grupos de consumidores

O mecanismo de publicação/assinatura dos Hubs de eventos é habilitado por meio de *grupos de consumidores*. Um grupo de consumidores é uma exibição (estado, posição ou deslocamento) de todo um hub de eventos. Os grupos de consumidores permitem que vários aplicativos de consumo tenham um modo de exibição separado do fluxo de eventos e leiam o fluxo de forma independente em seu próprio ritmo e com seus próprios deslocamentos.

Em um arquitetura de processamento de fluxo, cada aplicativo downstream equivale a um grupo de consumidores. Se você quiser gravar dados de evento em um armazenamento de longo prazo, isso quer dizer que esse aplicativo gravador de armazenamento é um grupo de consumidores. O processamento de eventos complexos pode então ser executado por outro grupo separado de consumidores. Você pode acessar partições somente por meio de um grupo de consumidores. Sempre há um grupo de consumidores padrão em um hub de eventos, e você pode criar até 20 grupos de consumidores para um hub de eventos de camada padrão.

Pode haver no máximo cinco leitores simultâneos em uma partição por grupo de consumidores. No entanto **recomenda-se que haja apenas um receptor ativo em uma partição por grupo de consumidores**. Dentro de uma única partição, cada leitor recebe todas as mensagens. Se você tiver vários leitores na mesma partição, processará mensagens duplicadas. Você precisa lidar com isso em seu código, o que pode não ser trivial. No entanto, é uma abordagem válida em alguns cenários.

Alguns clientes oferecidos pelos SDKs do Azure são agentes de consumidor inteligentes que gerenciam automaticamente os detalhes da garantia de que cada partição tem um único leitor e que todas as partições para um hub de eventos estão sendo lidas. Isso permite que seu código se concentre no processamento dos eventos que estão sendo lidos do hub de eventos para que ele possa ignorar muitos dos detalhes das partições. Para obter mais informações, consulte [conectar-se a uma partição](#connect-to-a-partition).

Os exemplos a seguir mostram a Convenção de URI do grupo de consumidores:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

A figura a seguir mostra a arquitetura de processamento de fluxo dos Hubs de Eventos:

![Arquitetura dos hubs de eventos](./media/event-hubs-about/event_hubs_architecture.svg)

### <a name="stream-offsets"></a>Deslocamentos de fluxo

Um *deslocamento* é a posição de um evento dentro de uma partição. Você pode pensar em um deslocamento como um cursor do lado do cliente. O deslocamento é uma numeração em bytes do evento. Esse deslocamento permite que um consumidor de eventos (leitor) especifique um ponto no fluxo de eventos a partir do qual deseja começar a ler eventos. Você pode especificar o deslocamento como um carimbo de data hora ou um valor de deslocamento. Os consumidores são responsáveis por armazenar seu próprios valores de deslocamento fora do serviço de Hubs de Evento. Dentro de uma partição, cada evento inclui um deslocamento.

![Deslocamento da partição](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Definindo o ponto de verificação

*Ponto de verificação* é um processo pelo qual os leitores marcam ou confirmam sua posição em uma sequência de eventos da partição. O ponto de verificação é responsabilidade do consumidor e ocorre em uma base por partição dentro de um grupo de consumidores. Essa responsabilidade significa que, para cada grupo de consumidores, cada leitor de partição deve manter o controle da sua posição atual no fluxo de eventos e pode informar o serviço quando considerar o fluxo de dados concluído.

Se um leitor se desconecta de uma partição, ao se reconectar, ele começa a ler no ponto de verificação que foi anteriormente enviado pelo último leitor dessa partição nesse grupo de consumidores. Quando o leitor se conecta, ele passa esse deslocamento para o hub de eventos para especificar o local para começar a ler. Assim, você pode usar o ponto de verificação para marcar eventos como "concluídos" por aplicativos de downstream e oferecer resiliência caso ocorra um failover entre leitores em execução em máquinas diferentes. É possível retornar aos dados mais antigos, especificando um deslocamento inferior desse processo de ponto de verificação. Por meio desse mecanismo, o ponto de verificação permite resiliência de failover e reprodução de fluxo de eventos.

> [!IMPORTANT]
> Os deslocamentos são fornecidos pelo serviço de hubs de eventos. É responsabilidade do consumidor fazer o ponto de verificação conforme os eventos são processados.

> [!NOTE]
> Se você estiver usando o armazenamento de BLOBs do Azure como o armazenamento de ponto de verificação em um ambiente que dá suporte a uma versão diferente do SDK do Storage BLOB que os normalmente estão disponíveis no Azure, você precisará usar o código para alterar a versão da API do serviço de armazenamento para a versão específica com suporte desse ambiente. Por exemplo, se você estiver executando os [hubs de eventos em um hub de Azure Stack versão 2002](/azure-stack/user/event-hubs-overview), a versão mais alta disponível para o serviço de armazenamento é a versão 2017-11-09. Nesse caso, você precisa usar o código para direcionar a versão da API do serviço de armazenamento para 2017-11-09. Para obter um exemplo de como direcionar uma versão de API de armazenamento específica, consulte estes exemplos no GitHub: 
> - [.Net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) ou  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>Tarefas comuns do consumidor

Todos os consumidores de Hubs de Eventos se conectam por meio de uma sessão do AMQP 1.0, um canal de comunicação bidirecional com reconhecimento de estado. Cada partição tem uma sessão de AMQP 1.0 que facilita o transporte de eventos separados por partição.

#### <a name="connect-to-a-partition"></a>Conectar-se a uma partição

Ao se conectar a partições, é uma prática comum usar um mecanismo de leasing para coordenar conexões de leitor a partições específicas. Dessa forma, é possível que todas as partições em um grupo de consumidores tenham apenas um leitor ativo. Os leitores de ponto de verificação, leasing e gerenciamento são simplificados usando os clientes nos SDKs dos hubs de eventos, que atuam como agentes inteligentes de consumidor. Eles são:

- O [EventProcessorClient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient) para .net
- O [EventProcessorClient](/java/api/com.azure.messaging.eventhubs.eventprocessorclient) para Java
- O [EventHubConsumerClient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient) para Python
- O [EventHubConsumerClient](/javascript/api/@azure/event-hubs/eventhubconsumerclient) para JavaScript/TypeScript

#### <a name="read-events"></a>Ler eventos

Depois de uma sessão do AMQP 1.0 e o link ser aberto para uma partição específica, os eventos são entregues ao cliente AMQP 1.0 pelo serviço de Hubs de Evento. Esse mecanismo de entrega permite uma maior taxa de transferência e menor latência que mecanismos baseado em pull, como HTTP GET. Como os eventos são enviados para o cliente, cada instância de dados do evento contém metadados importantes, como o deslocamento e número da sequência que são usados para facilitar o ponto de verificação na sequência de eventos.

Dados de evento:
* Deslocamento
* Número de sequência
* Corpo
* Propriedades do usuário
* Propriedades do sistema

Ele é responsável por gerenciar o deslocamento.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Hubs de Eventos, acesse os seguintes links:

- Introdução aos Hubs de Eventos
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Guia de programação dos Hubs de Eventos](event-hubs-programming-guide.md)
* [Disponibilidade e consistência nos Hubs de Eventos](event-hubs-availability-and-consistency.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
* [Exemplos de Hubs de Eventos](event-hubs-samples.md)