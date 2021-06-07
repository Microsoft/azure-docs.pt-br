---
title: Replicação de mensagens e Federação entre regiões-barramento de serviço do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da replicação de eventos e da Federação entre regiões com o barramento de serviço do Azure.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: e47f633fcd9248eab6f47936aa7c45877decc1fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880820"
---
# <a name="message-replication-and-cross-region-federation"></a>Replicação de mensagem e federação entre regiões

Em namespaces, o barramento de serviço do Azure dá suporte à [criação de topologias de filas encadeadas e assinaturas de tópico usando o encaminhamento](service-bus-auto-forwarding.md) automático para permitir a implementação de vários padrões de roteamento. Por exemplo, você pode fornecer aos parceiros as filas dedicadas às quais eles têm permissões de envio ou recebimento e que podem ser temporariamente suspensos, se necessário, e conectá-los com flexibilidade a outras entidades que são privadas para o aplicativo. Você também pode criar topologias de roteamento de várias etapas complexas ou pode criar filas de estilo de caixa de correio, que esgotam as assinaturas de tópicos do tipo fila e permitem mais capacidade de armazenamento por assinante. 

Muitas soluções sofisticadas também exigem que as mensagens sejam replicadas entre limites de namespace para implementar esses e outros padrões. As mensagens podem ter que fluir entre namespaces associados a vários locatários de aplicativo diferentes ou entre várias regiões diferentes do Azure. 

Sua solução manterá vários namespaces de barramento de serviço em regiões diferentes e replicará mensagens entre filas e tópicos, e/ou que você irá trocar mensagens com origens e destinos como [hubs de eventos do Azure](../event-hubs/event-hubs-about.md), [Hub IOT do Azure](../iot-fundamentals/iot-introduction.md)ou [Apache Kafka](https://kafka.apache.org). 

Esses cenários são o foco deste artigo. 

## <a name="federation-patterns"></a>Padrões de Federação

Há várias motivações possíveis para o porquê de você querer mover mensagens entre entidades do barramento de serviço, como filas ou tópicos, ou entre o barramento de serviço e outras origens e destinos. 

Em comparação com o conjunto de padrões semelhantes para os [hubs de eventos](../service-bus-messaging/service-bus-federation-overview.md), a Federação para entidades do tipo fila é mais complexa porque as filas de mensagens prometem seus consumidores Propriedade exclusiva em qualquer mensagem única, espera-se preservar a ordem de chegada na entrega de mensagens e para que o agente coordene a distribuição justa de mensagens entre os [consumidores concorrentes](/azure/architecture/patterns/competing-consumers). 

Há impedimentos práticos, incluindo as restrições do [teorema Cap](https://en.wikipedia.org/wiki/CAP_theorem), que tornam difícil fornecer uma exibição unificada de uma fila que está disponível simultaneamente em várias regiões e que permite a distribuição regional dos [consumidores concorrentes](/azure/architecture/patterns/competing-consumers) para assumir a propriedade exclusiva das mensagens. Essa fila distribuída geograficamente exigirá uma replicação totalmente consistente, não apenas de mensagens, mas também do estado de entrega de cada mensagem antes que as mensagens possam ser disponibilizadas para os consumidores. Uma meta de uma consistência total para uma fila hipotética, geodistribuída e regional, está em conflito direto com o objetivo principal que praticamente todos os clientes do barramento de serviço do Azure têm ao considerar cenários de Federação: máxima disponibilidade e confiabilidade para suas soluções. 

Os padrões apresentados aqui, portanto, se concentram na disponibilidade e na confiabilidade, e também visando melhor evitar a perda de informações e o tratamento duplicado de mensagens. 

### <a name="resiliency-against-regional-availability-events"></a>Resiliência contra eventos de disponibilidade regional 

Embora a disponibilidade e a confiabilidade máximas sejam as principais prioridades operacionais para o barramento de serviço, existem, no entanto, muitas maneiras pelas quais um produtor ou consumidor pode ser impedido de se comunicar com seu barramento de serviço "primário" atribuído devido a problemas de resolução de nomes ou de rede, ou onde a entidade do barramento de serviço pode realmente não responder ou retornar erros. O processador de mensagens designado também pode ficar indisponível.

Essas condições não são "desastrosas", de modo que você desejará abandonar a implantação regional, como faria em uma situação de recuperação de desastre, mas o cenário de negócios de alguns aplicativos pode já estar impactado por eventos de disponibilidade que não durar mais do que alguns minutos ou até mesmo segundos. O barramento de serviço do Azure é geralmente usado em ambientes de nuvem híbrida e com clientes que residem na borda da rede, por exemplo, em lojas de varejo, restaurantes, branches bancários, sites de produção, instalações de logística e aeroportos. É possível que um problema de roteamento de rede ou de congestionamento afete o seu ponto de extremidade do barramento de serviço atribuído, enquanto um ponto de extremidade secundário em uma região diferente pode ser acessado. Ao mesmo tempo, os sistemas que processam mensagens que chegam a esses sites ainda podem ter acesso impedido aos pontos de extremidade do barramento de serviço primário e secundário. 

Há muitos exemplos práticos desses aplicativos de nuvem híbrida e de borda com uma baixa tolerância de negócios para o impacto de problemas de roteamento de rede ou problemas transitórios de disponibilidade de uma entidade do barramento de serviço. Eles incluem o processamento de pagamentos em sites de varejo, placas no aeroporto e pedidos de telefone celular em restaurantes, todos os quais são apresentados instantaneamente e concluídos sempre que o caminho de comunicação confiável não está disponível.

Nesta categoria, discutiremos três padrões distribuídos distintos: replicação "All-active", replicação "ativa-passiva" e replicação "excedente". 

#### <a name="all-active-replication"></a>Replicação de All-Active

O padrão de replicação "All-active" permite que uma réplica ativa do mesmo tópico lógico (ou fila) esteja disponível em vários namespaces (e regiões) e que todas as mensagens fiquem disponíveis em todas as réplicas, independentemente de onde elas foram enfileiradas. O padrão geralmente preserva a ordem das mensagens em relação a qualquer Publicador. 

![Todos os padrões ativos](media/service-bus-federation-overview/mirrored-topics.jpg)

Conforme mostrado na ilustração, o padrão geralmente é mais enxuto nos tópicos do barramento de serviço. Um tópico para cada namespace que deve participar do esquema de replicação. Cada um desses tópicos tem uma "assinatura de replicação" para qualquer um dos outros tópicos para os quais as mensagens devem ser replicadas. Na ilustração acima, nós simplesmente temos um par de tópicos e, portanto, uma única assinatura de replicação para o respectivo outro tópico. Em um cenário com três namespaces *{N1, N2, N3}*, um tópico no namespace *N1* teria duas assinaturas de replicação, uma para o tópico correspondente em *N2* e outra para o tópico correspondente em *N3*. 

Cada assinatura de replicação tem uma regra que combina uma expressão de filtro SQL ( `replicated <> 1` ) e uma ação SQL ( `set replicated = 1` ). O filtro da regra garante que apenas as mensagens nas quais a propriedade personalizada `replication` não esteja definida ou não tenham o valor `1` se torne qualificada para essa assinatura, e a ação defina essa propriedade exata para o valor `1` em cada mensagem selecionada logo em seguida. O efeito é que quando a mensagem é copiada para o tópico correspondente, ela não fica mais qualificada para replicação na direção oposta e, portanto, evitamos mensagens que saltam entre as réplicas.

Uma assinatura com uma regra respectiva pode ser facilmente adicionada a qualquer tópico usando o CLI do Azure assim.

```azurecli

az servicebus topic subscription rule create --resource-group myresourcegroup \
   --namespace mynamespace --topic-name mytopic 
   --subscription-name replication --name replication \
   --action-sql-expression "set replication = 1" \
   --filter-sql-expression "replication IS NULL"
```

Para modelar uma fila, cada tópico é restrito a apenas uma assinatura regular (além das assinaturas de replicação) que todos os consumidores compartilham.

> O modelo de replicação tudo ativo coloca uma cópia de todas as mensagens enviadas em qualquer um dos tópicos em cada um dos tópicos. Isso significa que o código do aplicativo em cada região verá e processará todas as mensagens.
> Esse padrão é adequado para cenários em que os dados estão sendo compartilhados em várias regiões ou se o processamento redundante geralmente é desejado. Se você precisar processar cada mensagem apenas uma vez, como com uma fila regular, você precisará considerar um dos dois padrões a seguir.  

#### <a name="active-passive-replication"></a>Replicação de Active-Passive

O padrão de replicação "ativo-passivo" é a variação do padrão anterior, em que apenas um dos tópicos (o "primário") é usado ativamente pelo aplicativo para enviar e receber mensagens e as mensagens são replicadas em um tópico secundário para o caso em que o tópico primário pode se tornar indisponível ou inacessível. 

![Padrão passivo ativo](media/service-bus-federation-overview/mirrored-topics-passive.jpg)

A principal diferença entre esse padrão e o padrão anterior é que a replicação é unidirecional do tópico primário para o tópico secundário. O tópico secundário nunca se torna o primário, mas é uma opção de backup para quando o tópico primário está temporariamente inutilizável. 

A vantagem de usar esse padrão é que ele tenta ajudar a minimizar o processamento duplicado. Durante a replicação, a `TimeToLive` Propriedade Message é definida como uma duração nas mensagens replicadas que refletem o tempo esperado durante o qual uma falha do primário levará a um failover. Por exemplo, se o seu cenário de caso de uso exigir uma alternância entre o consumidor e o secundário em, no máximo, 1 minuto de quando a recuperação de mensagens do início principal mostrar problemas, o secundário deve, idealmente, ter todas as mensagens disponíveis que você não pode acessar no primário, mas um número mínimo de mensagens que você já tinha processado da primária antes que os problemas apareçam. Se definirmos `TimeToLive` como duas vezes esse período, 2 minutos, durante a replicação ( `set sys.TimeToLive = '0:2:0'` na ação de regra), o secundário só manterá mensagens por 2 minutos e descartará as mais antigas. Isso significa que quando o receptor muda para o secundário, ele pode rapidamente ler e descartar mensagens mais antigas do que a última processada e, em seguida, processar a partir da primeira mensagem que ainda não foi vista. A duração da retenção real dependerá do caso de uso específico e, rapidamente, você poderá alternar para o secundário em seu aplicativo. A `TimeToLive` configuração é respeitada no intervalo de alguns segundos a dias. 

Embora o aplicativo esteja usando o secundário, ele também pode publicar diretamente no tópico secundário, que atua como um tópico regular. Após a comutação para o secundário, o consumidor verá, portanto, uma mistura de mensagens replicadas e mensagens publicadas diretamente no secundário. O aplicativo, portanto, deve primeiro mudar de volta para o primário e ainda permitir o descarregamento das mensagens publicadas localmente antes de alternar o consumidor de volta para o secundário. Por causa da retomada da replicação automaticamente quando o primário está disponível novamente, o consumidor também receberia novas mensagens publicadas no primário durante esse tempo, embora com uma latência um pouco maior. 

> Esse padrão é adequado para cenários em que as mensagens devem ser processadas apenas uma vez. O aplicativo precisa cooperar para manter o controle das mensagens processadas a partir do primário, pois ele encontrará duplicatas durante a janela de failover no secundário e, novamente, encontrará duplicatas ao mudar de volta.
> O critério de eliminação de duplicação deve ser o mais fornecido pelo aplicativo `MessageId` . O `EnqueuedTimeUtc` valor também é adequado como um indicador de marca-d ' água, mas o aplicativo precisa permitir alguma quantidade de descompasso de relógio (vários segundos) entre o primário e o secundário, como com qualquer sistema distribuído.


#### <a name="spillover-replication"></a>Replicação do excedente

O padrão de replicação "excedente" permite o uso ativo/ativo de várias entidades do barramento de serviço em várias regiões para lidar com o cenário em que o barramento de serviço está íntegro, mas o *consumidor* se torna sobrecarregado com o número de mensagens pendentes ou está totalmente indisponível. Um motivo para isso pode ser que um banco de dados que faz backup do processo do consumidor pode ser lento ou indisponível. Esse padrão funciona com filas simples e com assinaturas de tópico.  

![Replicação do excedente](media/service-bus-federation-overview/spillover.jpg)  

Conforme mostrado na ilustração, o padrão de replicação excedente Replica as mensagens da fila de mensagens [mortas](service-bus-dead-letter-queues.md) associada da fila ou da assinatura para uma fila emparelhada ou um tópico em um namespace diferente. 

Sem haver uma situação de falha, os dois namespaces são usados em paralelo, cada um recebendo algum subconjunto do tráfego de mensagens geral e seus consumidores associados que manipulam esse subconjunto. Depois que um dos consumidores começar a mostrar altas taxas de falha ou parar de funcionar, as respectivas mensagens acabarão na fila de mensagens mortas por exceder a contagem de entrega ou por expirar. As tarefas de replicação vão selecioná-las e reagrupá-las na fila emparelhada, em que elas são apresentadas para o consumidor íntegro presumido. 

Se o processamento deve ocorrer dentro de um determinado prazo, o `TimeToLive` para a fila e/ou mensagens deve ser definido de modo que o processamento ainda possa ocorrer no tempo pelo secundário excedente, por exemplo, `TimeToLive` pode ser definido para metade do tempo permitido.

Assim como no [padrão All-active](#all-active-replication), o aplicativo pode adicionar um indicador à mensagem, independentemente de a mensagem já ter sido replicada uma vez que elas não deparem entre o par de filas, mas são postadas em uma fila auxiliar que atua como a fila de mensagens mortas para o padrão composto.

> Esse padrão é adequado para cenários em que a principal preocupação é defender contra problemas de disponibilidade em consumidores ou recursos dos quais os consumidores dependem e também redistribuir picos de tráfego em uma das filas emparelhadas. Ele também fornece proteção contra um dos namespaces se tornando indisponíveis se os consumidores lêem de ambas as filas, mas o retardo de replicação imposto pela `TimeToLive` expiração pode fazer com que as mensagens dentro dessa janela de tempo sejam enviadas no namespace indisponível. 

### <a name="latency-optimization"></a>Otimização de latência 

Os tópicos são usados para distribuir informações para vários consumidores. Em alguns casos, especialmente os consumidores com distribuição geográfica ampla, pode ser benéfico replicar mensagens de um tópico para um tópico em um namespace secundário mais próximo dos consumidores.

![Otimização de latência](media/service-bus-federation-overview/latency-optimization.jpg)  

Por exemplo, ao compartilhar dados entre hubs regionais e continental, é mais eficiente transferir informações apenas uma vez entre os hubs e fazer com que os consumidores obtenham a cópia dos dados desses hubs. 

As transferências de replicação podem ser feitas em lotes, que os consumidores geralmente obtêm e liquidam mensagens uma a uma. Com uma latência de rede base de 100 ms entre, digamos, América do Norte e Europa, cada mensagem leva 200 MS para ser processada para as duas viagens de ida e volta para uma entidade remota para adquirir e liquidar as mensagens, em comparação a uma entidade na mesma região. 

### <a name="validation-reduction-and-enrichment"></a>Validação, redução e enriquecimento

As mensagens podem ser enviadas para uma fila ou um tópico do barramento de serviço por clientes externos à sua própria solução.

![Validação, redução, enriquecimento](media/service-bus-federation-overview/validation.jpg)  

Essas mensagens podem exigir a verificação de conformidade com um determinado esquema e para que as mensagens sem conformidade sejam descartadas ou inativas. Algumas mensagens podem ter que ser reduzidas em complexidade omitindo os dados e alguns podem ter que ser aprimorados adicionando dados com base nas pesquisas de dados de referência. As operações podem ser executadas com a funcionalidade personalizada na tarefa de replicação. 

### <a name="stream-to-queue-replication"></a>Fluxo para replicação de fila

Os hubs de eventos do Azure são uma solução ideal para lidar com volumes extremos de eventos de entrada. Mas nem os hubs de eventos nem os mecanismos semelhantes, como Apache Kafka fornecem um modelo de [consumidor competido](/azure/architecture/patterns/competing-consumers) gerenciado por serviço, em que vários consumidores podem lidar com mensagens da mesma fonte simultaneamente, sem arriscar o processamento duplicado, e finalmente liquidar essas mensagens depois que elas tiverem sido processadas. 

![Integração](media/service-bus-federation-overview/hub-to-queue.jpg)

Um fluxo para a replicação de fila transfere o conteúdo de uma única partição do hub de eventos ou o conteúdo de um hub de eventos completo para uma fila do barramento de serviço, de onde as mensagens podem ser processadas com segurança, transacionalmente e com consumidores concorrentes. Essa replicação também permite o uso de todos os outros recursos do barramento de serviço para essas mensagens, incluindo roteamento com tópicos e demultiplexação baseada em sessão. 

### <a name="consolidation-and-normalization"></a>Consolidação e normalização 

As soluções globais são geralmente compostas por superfície regionais que são amplamente independentes, incluindo ter seus próprios recursos de processamento, mas as perspectivas supras e globais exigirão a integração de dados e, portanto, uma consolidação central dos mesmos dados de mensagem que é avaliada nos respectivos vestígios regionais para a perspectiva local. 

![Consolidação](media/service-bus-federation-overview/merge.jpg)

A normalização é um tipo de cenário de consolidação, no qual duas ou mais sequências de entrada de mensagens carregam o mesmo tipo de informação, mas com estruturas diferentes ou codificações diferentes, e as mensagens devem ser transcodificadas ou transformadas antes que possam ser consumidas. 

A normalização também pode incluir trabalho criptográfico, como descriptografar cargas criptografadas de ponta a ponta e criptografá-la novamente com chaves e algoritmos diferentes para o público do consumidor downstream. 

### <a name="splitting-and-routing"></a>Divisão e roteamento

Os tópicos do barramento de serviço e suas regras de assinatura são frequentemente usados para filtrar um fluxo de mensagens para um público específico e essa audiência Obtém o conjunto filtrado de uma assinatura. 

![Divisão](media/service-bus-federation-overview/split.jpg)

Em um sistema global em que o público para essas mensagens é distribuído globalmente ou pertence a diferentes aplicativos, a replicação pode ser usada para transferir mensagens dessa assinatura para uma fila ou tópico em um namespace diferente de onde elas são consumidas.

### <a name="replication-applications-in-azure-functions"></a>Aplicativos de replicação no Azure Functions

A implementação dos padrões acima requer um ambiente de execução escalonável e confiável para as tarefas de replicação que você deseja configurar e executar. No Azure, o ambiente de tempo de execução mais adequado para tarefas sem estado é [Azure Functions](../azure-functions/functions-overview.md). 

Azure Functions pode ser executado em uma [identidade gerenciada do Azure](../active-directory/managed-identities-azure-resources/overview.md) de modo que as tarefas de replicação possam ser integradas às regras de controle de acesso baseado em função dos serviços de origem e de destino sem a necessidade de gerenciar segredos ao longo do caminho de replicação. Para fontes de replicação e destinos que exigem credenciais explícitas, Azure Functions pode conter os valores de configuração para essas credenciais no armazenamento controlado de acesso rigidamente dentro de [Azure Key Vault](../key-vault/general/overview.md).

Além disso, o Azure Functions permite que as tarefas de replicação sejam integradas diretamente às redes virtuais do Azure e aos [pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) para todos os serviços de mensagens do Azure e estão prontamente integradas ao [Azure monitor](../azure-monitor/overview.md).

O mais importante é que Azure Functions tem gatilhos pré-criados e escaláveis e associações de saída para [hubs de eventos do Azure](../azure-functions/functions-bindings-service-bus.md), [Hub IOT do](../azure-functions/functions-bindings-event-iot.md)Azure, [barramento de serviço do Azure](../azure-functions/functions-bindings-service-bus.md), [grade de eventos](../azure-functions/functions-bindings-event-grid.md)do Azure e armazenamento de [filas do Azure](../azure-functions/functions-bindings-storage-queue.md), extensões personalizadas para [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)e [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension). A maioria dos gatilhos se adaptará dinamicamente às necessidades de taxa de transferência, dimensionando o número de instâncias em execução simultaneamente com base em métricas documentadas. 

Com o plano de consumo de Azure Functions, os gatilhos pré-criados podem até mesmo reduzir para zero, enquanto nenhuma mensagem está disponível para replicação, o que significa que você não incorrerá nenhum custo para manter a configuração pronta para escalar o backup. A principal desvantagem de usar o plano de consumo é que a latência das tarefas de replicação "Ativando" desse Estado é significativamente maior do que com os planos de hospedagem em que a infraestrutura é mantida em execução.  

Em contraste com tudo isso, os mecanismos de replicação mais comuns para mensagens e eventos, como o [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) da Apache Kafka exigem que você forneça um ambiente de hospedagem e dimensione o mecanismo de replicação por conta própria. Isso inclui a configuração e a integração dos recursos de segurança e de rede, facilitando o fluxo de dados de monitoramento e, em seguida, você ainda não tem a oportunidade de injetar tarefas de replicação personalizadas no fluxo. 

## <a name="next-steps"></a>Próximas etapas

Neste artigo, exploramos uma variedade de padrões de Federação e explicamos a função de Azure Functions como o tempo de execução de replicação de evento e de mensagens no Azure. 

Em seguida, talvez você queira ler como configurar um aplicativo replicador com Azure Functions e como replicar fluxos de eventos entre os hubs de eventos e vários outros sistemas de mensagens e eventos:

- [Aplicativos de replicação no Azure Functions](service-bus-federation-replicator-functions.md)
- [Replicando eventos entre entidades do barramento de serviço](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)
- [Eventos de roteamento para hubs de eventos do Azure](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)
- [Adquirir eventos dos hubs de eventos do Azure](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)

[1]: ./media/service-bus-auto-forwarding/IC628632.gif