---
title: Comparar filas do Armazenamento do Azure e filas do Barramento de Serviço
description: Analisa diferenças e semelhanças entre dois tipos de fila oferecidos pelo Azure.
ms.topic: article
ms.date: 11/04/2020
ms.openlocfilehash: 31992aa2012009c51cbeae78010ae8ced65fc872
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928300"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Filas do Armazenamento e filas do Barramento de Serviço — comparações e contrastes
Este artigo analisa as diferenças e semelhanças entre os dois tipos de filas oferecidos por Microsoft Azure: filas de armazenamento e filas do barramento de serviço. Usando essas informações, você pode tomar uma decisão mais informada sobre qual solução atende melhor às suas necessidades.

## <a name="introduction"></a>Introdução
O Azure dá suporte a dois tipos de mecanismo de fila: **filas do Armazenamento** e **filas do Barramento de Serviço**.

As **filas de armazenamento** fazem parte da infraestrutura de [armazenamento do Azure](https://azure.microsoft.com/services/storage/) . Eles permitem que você armazene grandes números de mensagens. Você acessa as mensagens em qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma mensagem da fila pode ter até 64 KB. Uma fila pode conter milhões de mensagens, até o limite da capacidade total de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processamento assíncrono. Para obter mais informações, consulte [o que são filas do armazenamento do Azure](../storage/queues/storage-queues-introduction.md).

As **filas do barramento de serviço** fazem parte de uma infraestrutura mais ampla de [mensagens do Azure](https://azure.microsoft.com/services/service-bus/) que dá suporte ao enfileiramento, publicação/assinatura e padrões de integração mais avançados. Elas foram projetadas para integrar aplicativos ou componentes de aplicativos que podem abranger vários protocolos de comunicação, contratos de dados, domínios de confiança ou ambientes de rede. Para obter mais informações sobre filas/tópicos/assinaturas do barramento de serviço, consulte as [filas, os tópicos e as assinaturas do barramento de serviço](service-bus-queues-topics-subscriptions.md).


## <a name="technology-selection-considerations"></a>Considerações de seleção da tecnologia
Filas de armazenamento e filas do barramento de serviço têm um conjunto de recursos um pouco diferente. Você pode escolher um ou ambos, dependendo das necessidades de sua solução específica.

Ao determinar qual tecnologia de enfileiramento se adapta à finalidade de uma determinada solução, os arquitetos e desenvolvedores de soluções devem considerar essas recomendações. 

### <a name="consider-using-storage-queues"></a>Considere o uso de filas de armazenamento
Como arquiteto/desenvolvedor de soluções, **você deve considerar o uso das filas do Armazenamento** quando:

* Seu aplicativo deve armazenar mais de 80 gigabytes de mensagens em uma fila.
* Seu aplicativo deseja acompanhar o progresso para processar uma mensagem na fila. É útil se o trabalho que está processando uma mensagem falhar. Outro operador pode usar essas informações para continuar de onde o trabalho anterior parou.
* Você precisar de logs do servidor de todas as transações executadas em suas filas.

### <a name="consider-using-service-bus-queues"></a>Considere o uso de filas do barramento de serviço
Como arquiteto/desenvolvedor de soluções, **você deve considerar o uso das filas do Barramento de Serviço** quando:

* Sua solução precisa receber mensagens sem precisar sondar a fila. Com o barramento de serviço, você pode obtê-lo usando uma operação de recebimento de sondagem longa usando os protocolos baseados em TCP aos quais o barramento de serviço dá suporte.
* Sua solução exigir que a fila forneça uma entrega ordenada PEPS (primeiro a entrar, primeiro a sair).
* Sua solução precisa dar suporte à detecção automática de duplicidades.
* Você desejar que seu aplicativo processe mensagens como fluxos paralelos de longa execução (mensagens associadas a um fluxo usando a propriedade [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) na mensagem). Nesse modelo, cada nó no aplicativo de consumo compete por fluxos, em oposição às mensagens. Quando um fluxo é fornecido a um nó de consumo, o nó pode examinar o estado do fluxo do aplicativo usando transações.
* Sua solução exigir comportamento transacional e atomicidade ao enviar ou receber várias mensagens de uma fila.
* Seu aplicativo lida com mensagens que podem exceder 64 KB, mas que provavelmente não abordarão o limite de 256 KB.
* Você tiver que lidar com a necessidade de fornecer um modelo de acesso baseado em função às filas e diferentes direitos/permissões para remetentes e destinatários. Para obter mais informações, consulte os seguintes artigos:
    - [Autenticar com identidades gerenciadas](service-bus-managed-service-identity.md)
    - [Autenticar a partir de um aplicativo](authenticate-application.md)
* O tamanho da fila não crescerá maior que 80 GB.
* Você desejar usar o protocolo do sistema de mensagens baseado em padrões AMQP 1.0. Para obter mais informações sobre AMQP, confira [Visão geral do AMQP do Barramento de Serviço](service-bus-amqp-overview.md).
* Você deve prever uma eventual migração da comunicação ponto a ponto baseada em fila para um padrão de mensagens de publicação/assinatura. Esse padrão permite a integração de receptores adicionais (assinantes). Cada destinatário recebe cópias independentes de algumas ou todas as mensagens enviadas para a fila. 
* Sua solução de mensagens precisa dar suporte à garantia de entrega "no máximo uma vez" sem a necessidade de você criar os componentes de infraestrutura adicionais.
* Sua solução precisa publicar e consumir lotes de mensagens.

## <a name="compare-storage-queues-and-service-bus-queues"></a>Comparar filas de armazenamento e filas do barramento de serviço
As tabelas nas seções a seguir fornecem um agrupamento lógico de recursos de fila. Eles permitem que você compare rapidamente os recursos disponíveis nas filas do armazenamento do Azure e nas filas do barramento de serviço.

## <a name="foundational-capabilities"></a>Recursos básicos
Esta seção compara alguns dos recursos básicos de enfileiramento fornecidos pelas filas do Armazenamento e filas do Barramento de Serviço.

| Critérios de comparação | Filas de armazenamento | Filas do Barramento de Serviço |
| --- | --- | --- |
| Garantia de ordenação |**Não** <br/><br>Para obter mais informações, consulte a primeira observação na seção [informações adicionais](#additional-information) .</br> | **Sim - Primeiro a Entrar, Primeiro a Sair (PEPS)**<br/><br>(com o uso de [sessões de mensagens](message-sessions.md)) |
| Garantia de entrega |**Pelo menos uma vez** |**Pelo menos uma vez** (usando o modo de recebimento Peeklock. É o padrão) <br/><br/>**No máximo uma vez** (usando o modo de recebimento ReceiveAndDelete) <br/> <br/> Saiba mais sobre vários [modos de recebimento](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Suporte à operação atômica |**Não** |**Sim**<br/><br/> |
| Comportamento de recebimento |**Sem bloqueio**<br/><br/>(conclusão imediata se nenhuma mensagem nova for encontrada) |**Bloqueando com ou sem tempo limite**<br/><br/>(oferece sondagem longa ou ["Técnica Comet"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Sem bloqueio**<br/><br/>(somente por meio do uso de API gerenciada .NET) |
| API de estilo push |**Não** |**Sim**<br/><br/>API .NET de sessões [QueueClient. onMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) e [MessageSessionHandler. onMessage](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) . |
| Modo de recebimento |**Inspecionar e Conceder** |**Inspecionar e bloquear**<br/><br/>**Receber e excluir** |
| Modo de acesso exclusivo |**Com base em concessão** |**Com base em bloqueio** |
| Duração da concessão/do bloqueio |**30 segundos (padrão)**<br/><br/>**7 dias (máximo)** (Você pode renovar ou liberar uma concessão de mensagem usando a API [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage).) |**60 segundos (padrão)**<br/><br/>Você pode renovar um bloqueio de mensagem usando a API [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock). |
| Precisão da concessão/do bloqueio |**Nível da mensagem**<br/><br/>Cada mensagem pode ter um valor de tempo limite diferente, que você pode atualizar conforme necessário durante o processamento da mensagem, usando a API [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) . |**Nível da fila**<br/><br/>(cada fila tem uma precisão de bloqueio aplicada a todas as suas mensagens, mas você pode renovar o bloqueio usando a API [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock).) |
| Recebimento em lote |**Sim**<br/><br/>(especificando explicitamente a contagem de mensagens ao recuperar mensagens, até um máximo de 32 mensagens) |**Sim**<br/><br/>(habilitando implicitamente uma propriedade de pré-busca ou explicitamente pelo uso de transações) |
| Envio em lote |**Não** |**Sim**<br/><br/>(usando transações ou envio em lote no lado do cliente) |

### <a name="additional-information"></a>Informações adicionais
* As mensagens nas filas de armazenamento são normalmente o primeiro a entrar, mas, às vezes, podem estar fora de ordem. Por exemplo, quando a duração do tempo limite de visibilidade de uma mensagem expirar porque um aplicativo cliente falhou durante o processamento de uma mensagem. Quando o tempo limite da visibilidade se esgotar, a mensagem se tornará visível novamente na fila de outro trabalho para que seja removida da fila. Nesse ponto, a mensagem recentemente visível pode ser colocada na fila para ser removida da fila novamente.
* O padrão PEPS garantido nas filas do Barramento de Serviço exige o uso de sessões de mensagens. Se o aplicativo falhar durante o processamento de uma mensagem recebida no modo de **bloqueio de & de Peek** , na próxima vez que um destinatário da fila aceitar uma sessão de mensagens, ele começará com a mensagem com falha depois que o período de vida útil da mensagem expirar.
* As filas de armazenamento são projetadas para oferecer suporte a cenários de enfileiramento padrão, como os seguintes:
    - Desacoplando componentes de aplicativos para aumentar a escalabilidade e a tolerância a falhas
    - Nivelamento de carga
    - Criando fluxos de trabalho de processo.
* Inconsistências em relação à manipulação de mensagens no contexto de sessões do barramento de serviço podem ser evitadas usando o estado de sessão para armazenar o estado do aplicativo em relação ao progresso da manipulação da sequência de mensagens da sessão e ao usar transações em ordem de liquidação de mensagens recebidas e atualização do estado da sessão. Esse tipo de recurso de consistência, às vezes, é rotulado *exatamente como processamento* nos produtos de outros fornecedores. As falhas de transação certamente farão com que as mensagens sejam entregues novamente e é por isso que o termo não é exatamente adequado.
* As filas do Armazenamento fornecem um modelo de programação uniforme e consistente entre filas, tabelas e Blobs, para desenvolvedores e equipes de operações.
* As filas do Barramento de Serviço fornecem suporte para transações locais no contexto de uma fila única.
* O modo **Receber e Excluir** com suporte do Barramento de Serviço tem a capacidade de reduzir a contagem de operações de mensagens (e custos associados) em troca da garantia de entrega reduzida.
* As filas do Armazenamento fornecem concessões com a capacidade de estender as concessões para mensagens. Esse recurso permite que os processos de trabalho mantenham concessões curtas em mensagens. Portanto, se um trabalho falhar, a mensagem poderá ser processada rapidamente novamente por outro operador. Além disso, um trabalhador pode estender a concessão em uma mensagem se precisar processá-la por mais tempo do que a atual.
* As filas do Armazenamento oferecem um tempo limite de visibilidade que você pode definir no enfileiramento de uma mensagem ou na remoção dela da fila. Além disso, você pode atualizar uma mensagem com valores de concessão diferentes em tempo de execução e atualizar valores diferentes entre as mensagens na mesma fila. Os tempos limite de bloqueio do barramento de serviço são definidos nos metadados da fila. No entanto, você pode renovar o bloqueio chamando o método [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) .
* O tempo limite máximo para um bloqueio de operação de recebimento nas filas do Barramento de Serviço é de 24 dias. No entanto, os tempos limite baseados em REST têm um valor máximo de 55 segundos.
* O envio em lote do lado do cliente fornecido pelo Barramento de Serviço permite que um cliente da fila coloque em lote várias mensagens em uma única operação de envio. O envio em lote está disponível apenas para operações de envio assíncronas.
* Recursos como o teto de 200 TB de filas de armazenamento (mais quando você virtualiza contas) e filas ilimitadas o tornam uma plataforma ideal para provedores de SaaS.
* As filas do Armazenamento fornecem um mecanismo de controle de acesso flexível e delegado ao desempenho.

## <a name="advanced-capabilities"></a>Recursos avançados
Esta seção compara recursos avançados fornecidos pelas filas do Armazenamento e do Barramento de Serviço.

| Critérios de comparação | Filas de armazenamento | Filas do Barramento de Serviço |
| --- | --- | --- |
| Entrega agendada |**Sim** |**Sim** |
| Mensagens mortas automáticas |**Não** |**Sim** |
| Aumentando o valor da vida útil da fila |**Sim**<br/><br/>(por meio da atualização in-loco do tempo limite de visibilidade) |**Sim**<br/><br/>(fornecido por uma função de API dedicada) |
| Suporte a mensagens suspeitas |**Sim** |**Sim** |
| Atualização in-loco |**Sim** |**Sim** |
| Log de transações do servidor |**Sim** |**Não** |
| Métricas de armazenamento |**Sim**<br/><br/>As **métricas de minuto** fornecem métricas em tempo real para disponibilidade, TPS, contagens de chamada de API, contagens de erros e muito mais. Elas estão todas em tempo real, agregadas por minuto e relatadas dentro de alguns minutos a partir do que acabou de acontecer na produção. Para obter mais informações, confira [Sobre as Métricas de Analítica de Armazenamento](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Sim**<br/><br/>(consultas em massa chamando [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Gerenciamento de estado |**Não** |**Sim**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Encaminhamento automático de mensagens |**Não** |**Sim** |
| Função Limpar fila |**Sim** |**Não** |
| Grupos de mensagens |**Não** |**Sim**<br/><br/>(por meio do uso de sessões de mensagens) |
| Estado do aplicativo por grupo de mensagens |**Não** |**Sim** |
| Detecção de duplicidade |**Não** |**Sim**<br/><br/>(configurável no lado do remetente) |
| Procurando grupos de mensagens |**Não** |**Sim** |
| Buscando sessões de mensagem por ID |**Não** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* Ambas as tecnologias de enfileiramento permitem que uma mensagem seja agendada para entrega em um momento posterior.
* O encaminhamento automático de fila permite que milhares de filas encaminhem automaticamente suas mensagens para uma única fila, da qual o aplicativo de recebimento consome a mensagem. É possível usar esse mecanismo para fins de segurança, controlar o fluxo e isolar o armazenamento entre cada editor de mensagem.
* As filas do Armazenamento dão suporte à atualização do conteúdo da mensagem. Você pode usar essa funcionalidade para informações de estado persistentes e atualizações incrementais de progresso na mensagem para que ela possa ser processada a partir do último ponto de verificação conhecido, em vez de começar do zero. Com filas do Barramento de Serviço, você pode habilitar o mesmo cenário usando as sessões de mensagens. As sessões permitem salvar e recuperar o estado de processamento do aplicativo (usando [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* As filas do barramento de serviço dão suporte a [mensagens mortas](service-bus-dead-letter-queues.md). Pode ser útil para isolar mensagens que atendam aos seguintes critérios:
    - As mensagens não podem ser processadas com êxito pelo aplicativo de recebimento 
    - As mensagens não podem alcançar seu destino devido a uma propriedade TTL (vida útil) expirada. O valor de TTL especifica quanto tempo uma mensagem permanece na fila. Com o Barramento de Serviço, a mensagem será movida para uma fila especial chamada $DeadLetterQueue quando o período TTL expirar.
* Para localizar mensagens “suspeitas” nas filas do Armazenamento, ao remover uma mensagem da fila, o aplicativo examina a propriedade [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) da mensagem. Se **DequeueCount** for maior que um determinado limite, o aplicativo moverá a mensagem para uma fila de "mensagens mortas" definida pelo aplicativo.
* As filas de armazenamento permitem que você obtenha um log detalhado de todas as transações executadas na fila e as métricas agregadas. Essas duas opções são úteis para depurar e entender como o aplicativo usa as filas do Armazenamento. Eles também são úteis para o ajuste de desempenho de seu aplicativo e a redução dos custos de uso de filas.
* As sessões de mensagens com suporte do barramento de serviço permitem que as mensagens que pertencem a um grupo lógico sejam associadas a um receptor. Ele cria uma afinidade de sessão entre as mensagens e seus respectivos destinatários. Você pode habilitar essa funcionalidade avançada no Barramento de Serviço definindo a propriedade [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) em uma mensagem. Os receptores podem escutar em uma ID de sessão específica e receber mensagens que compartilham o identificador de sessão especificado.
* O recurso de detecção de duplicação das filas do barramento de serviço remove automaticamente as mensagens duplicadas enviadas a uma fila ou tópico, com base no valor da propriedade [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) .

## <a name="capacity-and-quotas"></a>Capacidade e cotas
Esta seção compara as filas do Armazenamento e as filas do Barramento de Serviço da perspectiva de [capacidade e cotas](service-bus-quotas.md) que podem ser aplicáveis.

| Critérios de comparação | Filas de armazenamento | Filas do Barramento de Serviço |
| --- | --- | --- |
| Tamanho máximo da fila |**500 TB**<br/><br/>(limitado a uma [única capacidade de conta de armazenamento](../storage/common/storage-introduction.md#queue-storage)) |**1 GB a 80 GB**<br/><br/>(definido na criação de uma fila e [habilitando particionamento](service-bus-partitioning.md) – confira a seção "Informações adicionais") |
| Tamanho máximo da mensagem |**64 KB**<br/><br/>(48 KB ao usar a codificação **Base64**)<br/><br/>O Azure oferece suporte a mensagens grandes combinando filas e blobs — nesse ponto, você pode enfileirar até 200 GBs para um único item. |**256 KB** ou **1 MB**<br/><br/>(incluindo cabeçalho e corpo, tamanho máximo do cabeçalho: 64 KB).<br/><br/>Depende da [camada de serviço](service-bus-premium-messaging.md). |
| TTL máxima da mensagem |**Infinito** (API-Version 2017-07-27 ou posterior) |**TimeSpan.Max** |
| Número máximo de filas |**Ilimitado** |**10.000**<br/><br/>(por namespace do serviço) |
| Número máximo de clientes simultâneos |**Ilimitado** |**5\.000** |

### <a name="additional-information"></a>Informações adicionais
* O Barramento de Serviço impõe limites de tamanho de fila. O tamanho máximo da fila é especificado ao criar uma fila. Pode ser entre 1 GB e 80 GB. Se o tamanho da fila atingir esse limite, as mensagens de entrada adicionais serão rejeitadas e o chamador receberá uma exceção. Para obter mais informações sobre cotas no Barramento de Serviço, confira [Cotas do Barramento de Serviço](service-bus-quotas.md).
* O particionamento não tem suporte na [camada Premium](service-bus-premium-messaging.md). Na camada de mensagens Standard, você pode criar filas e tópicos do barramento de serviço em tamanhos 1 (padrão), 2, 3, 4 ou 5 GB. Com o particionamento habilitado, o barramento de serviço cria 16 cópias (16 partições) da entidade, cada um com o mesmo tamanho especificado. Assim, se você criar uma fila com 5 GB de tamanho, com 16 partições, o tamanho máximo da fila se tornará (5 * 16) = 80 GB.  Você pode ver o tamanho máximo da fila ou do tópico particionado na [portal do Azure][Azure portal].
* Com as filas de armazenamento, se o conteúdo da mensagem não for XML seguro, ele deverá ser codificado em **Base64** . Se você codificar a mensagem em **Base64**, a carga de usuário poderá ser de até 48 KB, em vez de 64 KB.
* Com as filas do Barramento de Serviço, cada mensagem armazenada em uma fila é composta por duas partes: um cabeçalho e um corpo. O tamanho total da mensagem não pode exceder o tamanho máximo de mensagem suportado pela camada de serviço.
* Quando os clientes se comunicam com as filas do Barramento de Serviço pelo protocolo TCP, o número máximo de conexões simultâneas para uma única fila do Barramento de Serviço é limitado a 100. Esse número é compartilhado entre remetentes e receptores. Se essa cota for atingida, as solicitações de conexões adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. Esse limite não é imposto em clientes que se conectam às filas usando a API baseada em REST.
* Se precisar de mais de 10.000 filas em um único namespace do Barramento de Serviço, você poderá entrar em contato com a equipe de suporte do Azure e solicitar um aumento. Para escalar para além de 10.000 filas com o Barramento de Serviço, você também pode criar namespaces adicionais usando o [Portal do Azure][Azure portal].

## <a name="management-and-operations"></a>Gerenciamento e operações
Esta seção compara os recursos de gerenciamento fornecidos pelas filas do Armazenamento e do Barramento de Serviço.

| Critérios de comparação | Filas de armazenamento | Filas do Barramento de Serviço |
| --- | --- | --- |
| Protocolo de gerenciamento |**REST sobre HTTP/HTTPS** |**REST sobre HTTPS** |
| Protocolo de runtime |**REST sobre HTTP/HTTPS** |**REST sobre HTTPS**<br/><br/>**AMQP 1.0 Standard (TCP com TLS)** |
| API do .NET |**Sim**<br/><br/>(API do Cliente de Armazenamento .NET) |**Sim**<br/><br/>(API do Barramento de serviço do .NET) |
| C++ nativo |**Sim** |**Sim** |
| API Java |**Sim** |**Sim** |
| API PHP |**Sim** |**Sim** |
| API Node.js |**Sim** |**Sim** |
| Suporte a metadados arbitrários |**Sim** |**Não** |
| Regras de nomenclatura da fila |**Até 63 caracteres**<br/><br/>(As letras em um nome de fila devem estar em minúsculas.) |**Até 260 caracteres**<br/><br/>(Nomes e caminhos de fila não diferenciam maiúsculas de minúsculas.) |
| Função Obter tamanho da fila |**Sim**<br/><br/>(Valor aproximado se as mensagens expirarem depois da TTL sem que sejam excluídas.) |**Sim**<br/><br/>(Valor exato, pontual.) |
| Função Inspecionar |**Sim** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* As filas do Armazenamento dão suporte a atributos arbitrários que podem ser aplicados à descrição da fila, na forma de pares de nome/valor.
* As duas tecnologias oferecem a capacidade de inspecionar uma mensagem sem precisar bloqueá-la, o que pode ser útil ao implementar uma ferramenta de gerenciador/navegador de filas.
* As APIs de mensagens orientadas .NET do Service Bus usam conexões TCP Full duplex para melhorar o desempenho em comparação com REST sobre HTTP e dão suporte ao protocolo padrão AMQP 1,0.
* Os nomes das filas do Armazenamento podem ter de 3 a 63 caracteres e podem conter letras minúsculas, número e hifens. Para obter mais informações, confira [Nomeando filas e metadados](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Os nomes de fila do Barramento de Serviço podem ter até 260 caracteres e têm menos regras restritivas de nomenclatura. Os nomes de fila do Barramento de Serviço podem conter letras, números, pontos, hifens e sublinhados.

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Esta seção aborda os recursos de autenticação e autorização com suporte nas filas do Armazenamento e do Barramento de Serviço.

| Critérios de comparação | Filas de armazenamento | Filas do Barramento de Serviço |
| --- | --- | --- |
| Autenticação |**Chave simétrica** |**Chave simétrica** |
| Modelo de segurança |Acesso delegado via tokens de SAS. |SAS |
| Federação do provedor de identidade |**Não** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* Cada solicitação de ambas as tecnologias de enfileiramento deve ser autenticada. Não há suporte para filas públicas com acesso anônimo. Usando [SAS](service-bus-sas.md), você pode tratar esse cenário publicando uma SAS somente gravação, SAS somente leitura ou até mesmo uma SAS de acesso completo.
* O esquema de autenticação fornecido pelas filas de armazenamento envolve o uso de uma chave simétrica. Essa chave é um HMAC (Message Authentication Code baseado em hash), computado com o algoritmo SHA-256 e codificado como uma cadeia de caracteres **Base64** . Para obter mais informações sobre o respectivo protocolo, veja [Autenticação para os Serviços de Armazenamento do Azure](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). As filas do Barramento de Serviço oferecem suporte a um modelo semelhante usando chaves simétricas. Para obter mais informações, confira [Autenticação de Assinatura de Acesso Compartilhado com Barramento de Serviço](service-bus-sas.md).

## <a name="conclusion"></a>Conclusão
Ao obter uma compreensão mais profunda das duas tecnologias, você pode tomar uma decisão mais informada sobre qual tecnologia de fila usar e quando. A decisão sobre quando usar as filas do Armazenamento ou as filas do Barramento de Serviço depende claramente de vários fatores. Esses fatores podem depender consideravelmente das necessidades individuais de seu aplicativo e respectiva arquitetura. 

Você pode preferir escolher filas de armazenamento por razões como as seguintes:

- Se seu aplicativo já usa os principais recursos do Microsoft Azure 
- Se você precisar de comunicação básica e mensagens entre serviços 
- Precisa de filas que podem ter mais de 80 GB de tamanho

As filas do barramento de serviço fornecem uma série de recursos avançados, como os descritos a seguir. Portanto, eles podem ser uma opção preferida se você estiver criando um aplicativo híbrido ou se seu aplicativo também exigir esses recursos.

- [Sessões](message-sessions.md)
- [Transações](service-bus-transactions.md)
- [Detecção de duplicidade](duplicate-detection.md)
- [Mensagens mortas automáticas](service-bus-dead-letter-queues.md)
- [Recursos de publicação e assinatura duráveis](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) 



## <a name="next-steps"></a>Próximas etapas
Os artigos a seguir fornecem mais orientação e informações sobre como usar as filas do Armazenamento ou as filas do Barramento de Serviço.

* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar o serviço de armazenamento de filas](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Práticas recomendadas para melhorias de desempenho usando o sistema de mensagens agenciado do Barramento de Serviço](service-bus-performance-improvements.md)
* [Introdução a filas e tópicos no Barramento de Serviço do Azure (postagem de blog)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [Guia do desenvolvedor do Barramento de Serviço](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Usando o serviço de enfileiramento do Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

