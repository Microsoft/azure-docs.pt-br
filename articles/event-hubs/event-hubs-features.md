---
title: "Visão geral dos recursos de Hubs de eventos do Azure | Microsoft Docs"
description: "Visão geral e detalhes sobre os recursos de Hubs de Eventos do Azure"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2017
ms.author: sethm
ms.openlocfilehash: a74d767d57eb5ce2b3a716f9ba908a451f25f538
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-features-overview"></a>Visão geral dos recursos de Hubs de Eventos

Os Hubs de Evento do Azure é um serviço de processamento de evento escalonável que recebe e processa grandes volumes de eventos e dados, com baixa latência e alta confiabilidade. Consulte [O que são Hubs de Eventos?](event-hubs-what-is-event-hubs.md) para obter uma visão geral de alto nível do serviço.

Este artigo se baseia nas informações da [visão geral](event-hubs-what-is-event-hubs.md) e fornece detalhes técnicos e de implementação sobre recursos e componentes dos Hubs de eventos.

## <a name="event-publishers"></a>Editores de eventos

Qualquer entidade que envie dados para um hub de eventos é um produtor de eventos ou *editor de eventos*. Os editores de eventos podem publicar eventos usando HTTPS ou AMQP 1.0. Os editores de eventos usam um token de SAS (Assinatura de Acesso Compartilhado) para se identificar para um hub de eventos e podem ter uma identidade exclusiva ou usar um token SAS comum.

### <a name="publishing-an-event"></a>Publicar um evento

Você pode publicar um evento por meio do AMQP 1.0 ou HTTPS. Hubs de Eventos fornecem [classes e bibliotecas de cliente](event-hubs-dotnet-framework-api-overview.md) para a publicação de eventos em um hub de eventos de clientes .NET. Para outras plataformas e tempos de execução, você pode usar qualquer cliente AMQP 1.0, como o [Apache Qpid](http://qpid.apache.org/). Você pode publicar eventos individualmente ou em lotes. Uma única publicação (instância de dados do evento) tem um limite de 256 KB, independentemente de ser um único evento ou um lote. Publicar eventos maiores que esse limite resulta em erro. Uma prática recomendada para editores é não conhecer as partições no hub de eventos e especificar apenas uma *chave de partição* (abordada na próxima seção), ou sua identidade por meio de seu token SAS.

A opção de usar AMQP ou HTTPS é específica para o cenário de uso. O AMQP requer o estabelecimento de um soquete bidirecional persistente, além do TLS (segurança de nível de transporte) ou SSL/TLS. O AMQP tem custos mais altos de rede ao inicializar a sessão, mas o HTTPS requer SSL adicional de sobrecarga para cada solicitação. O AMQP tem um melhor desempenho para editores frequentes.

![Hubs de Eventos](./media/event-hubs-features/partition_keys.png)

Os Hubs de Eventos garantem que todos os eventos que compartilham um valor de chave de partição sejam entregues na ordem e para a mesma partição. Se forem usadas chaves de partição com políticas de editor, a identidade do editor e o valor da chave de partição devem corresponder. Caso contrário, ocorrerá um erro.

### <a name="publisher-policy"></a>Política de editor

Os Hubs de Eventos permitem um controle granular sobre os editores de eventos por meio de *políticas do editor*. As políticas do editor são recursos de tempo de execução criado para facilitar um grande número de editores de eventos independentes. Com as políticas do editor, cada editor usa seu próprio identificador exclusivo ao publicar eventos em um hub de eventos usando o mecanismo a seguir:

```
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

Você não precisa criar nomes de editor com antecedência, mas eles devem coincidir com o token SAS usado ao publicar um evento, para garantir identidades de editores independentes. Ao usar políticas de editor, o valor **PartitionKey** é definido como o nome do editor. Para funcionar adequadamente, esses valores devem corresponder.

## <a name="capture"></a>Captura

A [Captura dos Hubs de Eventos](event-hubs-capture-overview.md) permite que você capture automaticamente os dados de streaming em Hubs de Eventos e salve-os em uma conta de armazenamento de blobs ou em uma conta de serviço do Azure Data Lake de sua escolha. Você pode habilitar a Captura do Portal do Azure e especificar um tamanho mínimo e a janela de tempo para executar a captura. A Captura de Hubs de Eventos permite que você especifique sua própria conta de Armazenamento de Blobs do Azure e o contêiner, ou conta de serviço do Azure Data Lake, que será usada para armazenar os dados capturados. Os dados capturados são gravados no formato Apache Avro.

## <a name="partitions"></a>Partições

Os Hubs de Evento fornecem streaming de mensagens por meio de um padrão de consumidor particionado no qual cada consumidor lê somente um subconjunto específico, ou partição, do fluxo de mensagens. Esse padrão permite a escala horizontal para processamento de eventos e fornece outros recursos centrados no fluxo que não estão disponíveis em filas e tópicos.

Uma partição é uma sequência ordenada de eventos que é mantida em um hub de eventos. À medida que novos eventos chegam, eles são adicionados ao final dessa sequência. Uma partição pode ser pensada como "log de confirmação".

![Hubs de Eventos](./media/event-hubs-features/partition.png)

Os Hubs de Eventos mantêm os dados por um período de retenção configurado que se aplica a todas as partições no hub de eventos. Eventos expiram periodicamente; não é possível excluí-los explicitamente. Como as partições são independentes e contêm sua própria sequência de dados, elas geralmente crescem a taxas diferentes.

![Hubs de evento](./media/event-hubs-features/multiple_partitions.png)

O número de partições é especificado na criação do Hub de Eventos e deve estar entre 2 e 32. A contagem de partições não é mutável, portanto você deve considerar a escala de longo prazo ao definir a contagem de partições. As partições são um mecanismo de organização de dados relacionados ao paralelismo de downstream necessário no consumo de aplicativos. O número de partições em um hub de eventos está diretamente relacionado ao número de leitores simultâneos que você espera ter. Você pode aumentar o número de partições para mais de 32 entrando em contato com a equipe de Hubs de Eventos.

Embora as partições possam ser identificadas e seja possível enviar diretamente a elas, não é recomendável enviar diretamente a uma partição. Em vez disso, você pode usar construções de nível superior abordadas nas seções [Editor de eventos](#event-publishers) e [Capacidade](#capacity). 

As partições são preenchidas com uma sequência de dados de evento que contêm o corpo do evento, um recipiente de propriedades definidas pelo usuário e metadados como seu deslocamento na partição e seu número na sequência de fluxo.

Para saber mais sobre partições e a compensação entre disponibilidade e confiabilidade, consulte os artigos [Guia de programação dos Hubs de Eventos](event-hubs-programming-guide.md#partition-key) e [Disponibilidade e consistência em Hubs de Eventos](event-hubs-availability-and-consistency.md).

### <a name="partition-key"></a>Chave de partição

Use uma [chave de partição](event-hubs-programming-guide.md#partition-key) para mapear dados de evento de entrada em partições específicas para fins de organização de dados. A chave de partição é um valor fornecido pelo remetente passado para um hub de eventos. Ela é processada por meio de uma função de hash estática, que cria a atribuição de partição. Se você não especificar uma chave de partição ao publicar um evento, uma atribuição de round robin será usada.

O editor de eventos só está ciente da sua chave de partição, não da partição para a qual os eventos são publicados. Essa desassociação de chave e partição isenta o remetente da necessidade de saber muito sobre o processamento de downstream. Uma identidade por dispositivo ou exclusiva do usuário é uma boa chave de partição, mas outros atributos, como geografia, também podem ser usados para agrupar eventos relacionados em uma única partição.

## <a name="sas-tokens"></a>Tokens SAS

Os hubs de eventos usam *assinaturas de acesso compartilhado*, que estão disponíveis nos níveis de namespace e de hub de eventos. Um token SAS é gerado a partir de uma chave de SAS e é um hash SHA de uma URL, codificado em um formato específico. Usando o nome da chave (política) e o token, os Hubs de Evento podem regenerar o hash e, portanto, autenticar o remetente. Normalmente, os tokens SAS para editores de eventos são criados apenas com privilégios de **enviar** em um hub de eventos específico. Esse mecanismo de URL de token SAS é a base para a identificação de editor abordada na política do editor. Para saber mais sobre como trabalhar com SAS, confira [Autenticação de assinatura de acesso compartilhado com o Barramento de Serviço](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Consumidores de evento

Qualquer entidade que lê dados de evento de um hub de eventos é um *consumidor de eventos*. Todos os consumidores de Hubs de Eventos se conectam por meio de sessão do AMQP 1.0, e os eventos são entregues por meio da sessão à medida que são disponibilizados. O cliente não precisa buscar pela disponibilidade de dados.

### <a name="consumer-groups"></a>Grupos de consumidores

O mecanismo de publicação/assinatura dos Hubs de Evento é habilitado por meio de *grupos de consumidores*. Um grupo de consumidores é uma exibição (estado, posição ou deslocamento) de todo um hub de eventos. Os grupos de consumidores habilitam vários aplicativos de consumo para ter um modo de exibição separado do fluxo de eventos e para ler o fluxo de forma independente em seu próprio ritmo e com seus próprios deslocamentos.

Em um arquitetura de processamento de fluxo, cada aplicativo downstream equivale a um grupo de consumidores. Se você quiser gravar dados de evento em um armazenamento de longo prazo, isso quer dizer que esse aplicativo gravador de armazenamento é um grupo de consumidores. O processamento de eventos complexos pode então ser executado por outro grupo separado de consumidores. Você pode acessar partições somente por meio de um grupo de consumidores. Pode haver no máximo cinco leitores simultâneos em uma partição por grupo de consumidores. No entanto **recomenda-se que haja apenas um receptor ativo em uma partição por grupo de consumidores**. Sempre há um grupo de consumidores padrão em um hub de eventos, e você pode criar até 20 grupos de consumidores para um hub de eventos de camada padrão.

Veja estes exemplos de convenção de URI de grupo de consumidores:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

A figura a seguir mostra a arquitetura de processamento de fluxo dos Hubs de Eventos:

![Hubs de Eventos](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Deslocamentos de fluxo

Um *deslocamento* é a posição de um evento dentro de uma partição. Você pode pensar em um deslocamento como um cursor do lado do cliente. O deslocamento é uma numeração em bytes do evento. Esse deslocamento permite que um consumidor de eventos (leitor) especifique um ponto no fluxo de eventos a partir do qual deseja começar a ler eventos. Você pode especificar o deslocamento como um carimbo de data hora ou um valor de deslocamento. Os consumidores são responsáveis por armazenar seu próprios valores de deslocamento fora do serviço de Hubs de Evento. Dentro de uma partição, cada evento inclui um deslocamento.

![Hubs de evento](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Ponto de verificação

*Ponto de verificação* é um processo pelo qual os leitores marcam ou confirmam sua posição em uma sequência de eventos da partição. O ponto de verificação é responsabilidade do consumidor e ocorre em uma base por partição dentro de um grupo de consumidores. Essa responsabilidade significa que, para cada grupo de consumidores, cada leitor de partição deve manter o controle da sua posição atual no fluxo de eventos e pode informar o serviço quando considerar o fluxo de dados concluído.

Se um leitor se desconecta de uma partição, ao se reconectar, ele começa a ler no ponto de verificação que foi anteriormente enviado pelo último leitor dessa partição nesse grupo de consumidores. Quando o leitor se conecta, ele passa esse deslocamento para o hub de eventos para especificar o local para começar a ler. Assim, você pode usar o ponto de verificação para marcar eventos como "concluídos" por aplicativos de downstream e oferecer resiliência caso ocorra um failover entre leitores em execução em máquinas diferentes. É possível retornar aos dados mais antigos, especificando um deslocamento inferior desse processo de ponto de verificação. Por meio desse mecanismo, o ponto de verificação permite resiliência de failover e reprodução de fluxo de eventos.

### <a name="common-consumer-tasks"></a>Tarefas comuns do consumidor

Todos os consumidores de Hubs de Eventos se conectam por meio de uma sessão do AMQP 1.0, um canal de comunicação bidirecional com reconhecimento de estado. Cada partição tem uma sessão de AMQP 1.0 que facilita o transporte de eventos separados por partição.

#### <a name="connect-to-a-partition"></a>Conectar-se a uma partição

Ao se conectar a partições, é uma prática comum usar um mecanismo de concessão para coordenar conexões de leitores a partições específicas. Dessa forma, é possível que cada partição em um grupo de consumidores tenha apenas um leitor ativo. Os leitores de ponto de verificação, de concessão e de gerenciamento são simplificados pelo uso da classe [EventProcessorHost](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost) para clientes .NET. O Host do processador de eventos é um agente de consumidor inteligente.

#### <a name="read-events"></a>Ler eventos

Depois de uma sessão do AMQP 1.0 e o link ser aberto para uma partição específica, os eventos são entregues ao cliente AMQP 1.0 pelo serviço de Hubs de Evento. Esse mecanismo de entrega permite uma maior taxa de transferência e menor latência que mecanismos baseado em pull, como HTTP GET. Como os eventos são enviados para o cliente, cada instância de dados do evento contém metadados importantes, como o deslocamento e número da sequência que são usados para facilitar o ponto de verificação na sequência de eventos.

Dados de evento:
* Deslocamento
* Número de sequência
* Corpo
* Propriedades do usuário
* Propriedades do sistema

Ele é responsável por gerenciar o deslocamento.

## <a name="capacity"></a>Capacidade

Os Hubs de Eventos têm uma arquitetura paralela altamente dimensionável e há vários fatores importantes a serem considerados durante o dimensionamento.

### <a name="throughput-units"></a>Unidades de transferência

A capacidade de transferência dos Hubs de Eventos é controlada pelas *unidades de transferência*. As unidades de taxa de transferência são unidades de capacidade pré-adquiridas. Uma única unidade de transferência inclui a seguinte capacidade:

* Entrada: até 1 MB por segundo ou 1000 eventos por segundo (o que ocorrer primeiro)
* Saída: até 2 MB por segundo

Além da capacidade de unidades de transferência adquiridas, a entrada está limitada e uma [ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) é retornada. A saída não gera exceções de limitação, mas ainda é limitada à capacidade das unidades de produtividade adquiridas. Se você receber exceções de taxa de publicação ou estiver esperando ver mais saída, verifique quantas unidades de transferência você comprou para o namespace. Você pode gerenciar unidades de produtividade na folha **Escala** dos namespaces no [portal do Azure](https://portal.azure.com). Você também pode gerenciar unidades de produtividade programaticamente usando as [APIs de Hubs de Eventos](event-hubs-api-overview.md).

As unidades de taxa de transferência são cobradas por hora e são pré-adquiridas. Depois de adquiridas, as unidades de taxa de transferência são cobradas por um mínimo de uma hora. Até 20 unidades de produtividade podem ser adquiridas para um namespace de Hubs de Eventos e elas são compartilhadas entre todos os Hubs de Eventos no namespace.

Você poderá adquirir mais unidades de produtividade, em blocos de 20 até 100 unidades de produtividade, entrando em contato com o suporte do Azure. Além disso, você também pode comprar blocos de 100 unidades de transferência.

É recomendável balancear partições e unidades de produtividade para obter uma escala ideal. Uma única partição tem uma escala máxima de uma unidade de transferência. O número de unidades de taxa de transferência deve ser menor ou igual ao número de partições em um hub de eventos.

Para obter informações detalhadas sobre o preço de Hubs de Eventos, consulte [Preço de Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Hubs de Eventos, acesse os seguintes links:

* Introdução com um [Tutorial de Hubs de Eventos][Event Hubs tutorial]
* [Guia de programação dos Hubs de Eventos](event-hubs-programming-guide.md)
* [Disponibilidade e consistência nos Hubs de Eventos](event-hubs-availability-and-consistency.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
* [Exemplos de Hubs de Eventos][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[Exemplos de Hubs de Eventos]: https://github.com/Azure/azure-event-hubs/tree/master/samples
