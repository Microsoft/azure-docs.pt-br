---
title: Visão geral dos recursos - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece detalhes sobre os recursos e a terminologia dos Hubs de Eventos do Azure.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e0505960a413308283c4e67e33ec495eedd3b092
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827717"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Recursos e terminologia em Hubs de Eventos do Azure

Os Hubs de Evento do Azure é um serviço de processamento de evento escalonável que recebe e processa grandes volumes de eventos e dados, com baixa latência e alta confiabilidade. Confira [O que são Hubs de Eventos?](event-hubs-what-is-event-hubs.md) para obter uma visão geral de alto nível.

Este artigo se baseia nas informações do [artigo de visão geral](event-hubs-what-is-event-hubs.md) e fornece detalhes técnicos e de implementação sobre recursos e componentes dos Hubs de Eventos.

## <a name="namespace"></a>Namespace
Um namespace de Hubs de Eventos fornece um contêiner de escopo exclusivo, referenciado pelo [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), em que você cria uma ou mais hubs de eventos tópicos Kafka. 

## <a name="event-hubs-for-apache-kafka"></a>Hubs de Eventos do Apache Kafka

[Esse recurso](event-hubs-for-kafka-ecosystem-overview.md) fornece um ponto de extremidade que permite aos clientes se comunicarem com Hubs de Eventos usando o protocolo Kafka. Essa integração oferece aos clientes um ponto de extremidade do Kafka. Isso permite que os clientes configurem seus aplicativos Kafka existentes para se comunicar com Hubs de Eventos, fornecendo uma alternativa para executar seus próprios clusters do Kafka. Os Hubs de Eventos do Apache Kafka dá suporte ao protocolo Kafka 1.0 e posterior. 

Com essa integração, você não precisa executar clusters de Kafka ou gerenciá-los com o Zookeeper. Isso também permite que você trabalhe com alguns dos recursos mais exigentes de Hubs de Eventos, como Capturar, Inflação Automática e Recuperação de Desastre Geográfico.

Essa integração também permite que aplicativos como o Mirror Maker ou estruturas como o Kafka Connect para trabalhar sem clusters somente com alterações de configuração. 

## <a name="event-publishers"></a>Editores de eventos

Qualquer entidade que envie dados para um hub de eventos é um produtor de eventos ou *editor de eventos*. Os editores de eventos podem publicar eventos usando HTTPS ou AMQP 1.0 ou Kafka e posterior. Os editores de eventos usam um token de SAS (Assinatura de Acesso Compartilhado) para se identificar para um hub de eventos e podem ter uma identidade exclusiva ou usar um token SAS comum.

### <a name="publishing-an-event"></a>Publicar um evento

Você pode publicar um evento por meio do AMQP 1.0, Kafka 1.0 (e posterior) ou HTTPS. Hubs de Eventos fornecem [classes e bibliotecas de cliente](event-hubs-dotnet-framework-api-overview.md) para a publicação de eventos em um hub de eventos de clientes .NET. Para outras plataformas e tempos de execução, você pode usar qualquer cliente AMQP 1.0, como o [Apache Qpid](https://qpid.apache.org/). Você pode publicar eventos individualmente ou em lotes. Uma única publicação (instância de dados do evento) tem um limite de 1 MB, independentemente de ser um único evento ou um lote. Publicar eventos maiores que esse limite resulta em erro. Uma prática recomendada para editores é não conhecer as partições no hub de eventos e especificar apenas uma *chave de partição* (abordada na próxima seção), ou sua identidade por meio de seu token SAS.

A opção de usar AMQP ou HTTPS é específica para o cenário de uso. O AMQP requer o estabelecimento de um soquete bidirecional persistente, além do TLS (segurança de nível de transporte) ou SSL/TLS. O AMQP tem custos mais altos de rede ao inicializar a sessão, mas o HTTPS requer SSL adicional de sobrecarga para cada solicitação. O AMQP tem um melhor desempenho para editores frequentes.

![Hubs de Eventos](./media/event-hubs-features/partition_keys.png)

Os Hubs de Eventos garantem que todos os eventos que compartilham um valor de chave de partição sejam entregues na ordem e para a mesma partição. Se forem usadas chaves de partição com políticas de editor, a identidade do editor e o valor da chave de partição devem corresponder. Caso contrário, ocorrerá um erro.

### <a name="publisher-policy"></a>Política de editor

Os Hubs de Eventos permitem um controle granular sobre os editores de eventos por meio de *políticas do editor*. As políticas do editor são recursos de tempo de execução criado para facilitar um grande número de editores de eventos independentes. Com as políticas do editor, cada editor usa seu próprio identificador exclusivo ao publicar eventos em um hub de eventos usando o mecanismo a seguir:

```http
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

Você não precisa criar nomes de editor com antecedência, mas eles devem coincidir com o token SAS usado ao publicar um evento, para garantir identidades de editores independentes. Ao usar políticas de editor, o valor **PartitionKey** é definido como o nome do editor. Para funcionar adequadamente, esses valores devem corresponder.

## <a name="capture"></a>Captura

A [Captura dos Hubs de Eventos](event-hubs-capture-overview.md) permite que você capture automaticamente os dados de streaming em Hubs de Eventos e salve-os em uma conta de armazenamento de blobs ou em uma conta de serviço do Azure Data Lake de sua escolha. Você pode habilitar a Captura do Portal do Azure e especificar um tamanho mínimo e a janela de tempo para executar a captura. A Captura de Hubs de Eventos permite que você especifique sua própria conta de Armazenamento de Blobs do Azure e o contêiner, ou conta de serviço do Azure Data Lake, uma das quais será usada para armazenar os dados capturados. Os dados capturados são gravados no formato Apache Avro.

## <a name="partitions"></a>Partições
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>Tokens SAS

Os hubs de eventos usam *assinaturas de acesso compartilhado*, que estão disponíveis nos níveis de namespace e de hub de eventos. Um token SAS é gerado a partir de uma chave de SAS e é um hash SHA de uma URL, codificado em um formato específico. Usando o nome da chave (política) e o token, os Hubs de Evento podem regenerar o hash e, portanto, autenticar o remetente. Normalmente, os tokens SAS para editores de eventos são criados apenas com privilégios de **enviar** em um hub de eventos específico. Esse mecanismo de URL de token SAS é a base para a identificação de editor abordada na política do editor. Para saber mais sobre como trabalhar com SAS, confira [Autenticação de assinatura de acesso compartilhado com o Barramento de Serviço](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Consumidores de evento

Qualquer entidade que lê dados de evento de um hub de eventos é um *consumidor de eventos*. Todos os consumidores de Hubs de Eventos se conectam por meio de sessão do AMQP 1.0, e os eventos são entregues por meio da sessão à medida que são disponibilizados. O cliente não precisa buscar pela disponibilidade de dados.

### <a name="consumer-groups"></a>Grupos de consumidores

O mecanismo de publicação/assinatura dos Hubs de Evento é habilitado por meio de *grupos de consumidores*. Um grupo de consumidores é uma exibição (estado, posição ou deslocamento) de todo um hub de eventos. Os grupos de consumidores habilitam vários aplicativos de consumo para ter um modo de exibição separado do fluxo de eventos e para ler o fluxo de forma independente em seu próprio ritmo e com seus próprios deslocamentos.

Em um arquitetura de processamento de fluxo, cada aplicativo downstream equivale a um grupo de consumidores. Se você quiser gravar dados de evento em um armazenamento de longo prazo, isso quer dizer que esse aplicativo gravador de armazenamento é um grupo de consumidores. O processamento de eventos complexos pode então ser executado por outro grupo separado de consumidores. Você pode acessar partições somente por meio de um grupo de consumidores. Sempre há um grupo de consumidores padrão em um hub de eventos, e você pode criar até 20 grupos de consumidores para um hub de eventos de camada padrão.

Pode haver no máximo cinco leitores simultâneos em uma partição por grupo de consumidores. No entanto **recomenda-se que haja apenas um receptor ativo em uma partição por grupo de consumidores**. Dentro de uma única partição, cada leitor recebe todas as mensagens. Se você tiver vários leitores na mesma partição, processará mensagens duplicadas. Você precisa lidar com isso em seu código, o que pode não ser trivial. No entanto, é uma abordagem válida em alguns cenários.


Veja estes exemplos de convenção de URI de grupo de consumidores:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

A figura a seguir mostra a arquitetura de processamento de fluxo dos Hubs de Eventos:

![Hubs de Eventos](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Deslocamentos de fluxo

Um *deslocamento* é a posição de um evento dentro de uma partição. Você pode pensar em um deslocamento como um cursor do lado do cliente. O deslocamento é uma numeração em bytes do evento. Esse deslocamento permite que um consumidor de eventos (leitor) especifique um ponto no fluxo de eventos a partir do qual deseja começar a ler eventos. Você pode especificar o deslocamento como um carimbo de data hora ou um valor de deslocamento. Os consumidores são responsáveis por armazenar seu próprios valores de deslocamento fora do serviço de Hubs de Evento. Dentro de uma partição, cada evento inclui um deslocamento.

![Hubs de Eventos](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Ponto de verificação

*Ponto de verificação* é um processo pelo qual os leitores marcam ou confirmam sua posição em uma sequência de eventos da partição. O ponto de verificação é responsabilidade do consumidor e ocorre em uma base por partição dentro de um grupo de consumidores. Essa responsabilidade significa que, para cada grupo de consumidores, cada leitor de partição deve manter o controle da sua posição atual no fluxo de eventos e pode informar o serviço quando considerar o fluxo de dados concluído.

Se um leitor se desconecta de uma partição, ao se reconectar, ele começa a ler no ponto de verificação que foi anteriormente enviado pelo último leitor dessa partição nesse grupo de consumidores. Quando o leitor se conecta, ele passa esse deslocamento para o hub de eventos para especificar o local para começar a ler. Assim, você pode usar o ponto de verificação para marcar eventos como "concluídos" por aplicativos de downstream e oferecer resiliência caso ocorra um failover entre leitores em execução em máquinas diferentes. É possível retornar aos dados mais antigos, especificando um deslocamento inferior desse processo de ponto de verificação. Por meio desse mecanismo, o ponto de verificação permite resiliência de failover e reprodução de fluxo de eventos.

### <a name="common-consumer-tasks"></a>Tarefas comuns do consumidor

Todos os consumidores de Hubs de Eventos se conectam por meio de uma sessão do AMQP 1.0, um canal de comunicação bidirecional com reconhecimento de estado. Cada partição tem uma sessão de AMQP 1.0 que facilita o transporte de eventos separados por partição.

#### <a name="connect-to-a-partition"></a>Conectar-se a uma partição

Ao se conectar a partições, é uma prática comum usar um mecanismo de concessão para coordenar conexões de leitores a partições específicas. Dessa forma, é possível que cada partição em um grupo de consumidores tenha apenas um leitor ativo. Os leitores de ponto de verificação, de concessão e de gerenciamento são simplificados pelo uso da classe [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) para clientes .NET. O Host do processador de eventos é um agente de consumidor inteligente.

#### <a name="read-events"></a>Ler eventos

Depois de uma sessão do AMQP 1.0 e o link ser aberto para uma partição específica, os eventos são entregues ao cliente AMQP 1.0 pelo serviço de Hubs de Evento. Esse mecanismo de entrega permite uma maior taxa de transferência e menor latência que mecanismos baseado em pull, como HTTP GET. Como os eventos são enviados para o cliente, cada instância de dados do evento contém metadados importantes, como o deslocamento e número da sequência que são usados para facilitar o ponto de verificação na sequência de eventos.

Dados de evento:
* Offset
* Número de sequência
* Corpo
* Propriedades do usuário
* Propriedades do sistema

Ele é responsável por gerenciar o deslocamento.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Hubs de Eventos, acesse os seguintes links:

* Introdução com um [Tutorial de Hubs de Eventos][Event Hubs tutorial]
* [Guia de programação dos Hubs de Eventos](event-hubs-programming-guide.md)
* [Disponibilidade e consistência nos Hubs de Eventos](event-hubs-availability-and-consistency.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
* [Exemplos de Hubs de Eventos][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[Exemplos de Hubs de Eventos]: https://github.com/Azure/azure-event-hubs/tree/master/samples
