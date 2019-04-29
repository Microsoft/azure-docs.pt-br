---
title: Comparações e contrastes entre filas do Armazenamento do Azure e filas do Barramento de Serviço | Microsoft Docs
description: Analisa diferenças e semelhanças entre dois tipos de fila oferecidos pelo Azure.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 2086813b01de6cd06f3714477e56864b36196382
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714557"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Filas do Armazenamento e filas do Barramento de Serviço — comparações e contrastes
Este artigo analisa as diferenças e semelhanças entre os dois tipos de filas oferecidos pelo Microsoft Azure atualmente: filas de Armazenamento e filas de Barramento de Serviço. Usando essas informações, é possível comparar e contrastar as respectivas tecnologias e tomar uma decisão mais informada sobre qual solução atende melhor às suas necessidades.

## <a name="introduction"></a>Introdução
O Azure oferece suporte a dois tipos de mecanismos de fila: **filas de Armazenamento** e **filas de Barramento de Serviço**.

As **filas do Armazenamento**, que fazem parte da infraestrutura do [armazenamento do Azure](https://azure.microsoft.com/services/storage/), apresentam uma interface GET/PUT/PEEK baseada em REST, oferecendo um sistema de mensagens confiável e persistente em e entre serviços.

**As filas do Barramento de Serviço** fazem parte de uma infraestrutura mais ampla do [sistema de mensagens do Azure](https://azure.microsoft.com/services/service-bus/), que dá suporte ao enfileiramento, bem como a publicação/assinatura e outros padrões de integração avançados. Para obter mais informações sobre filas, tópicos/assinaturas do Barramento de Serviço, confira o artigo sobre a [visão geral do Barramento de Serviço](service-bus-messaging-overview.md).

Embora ambas as tecnologias de enfileiramento coexistam, as filas do Armazenamento foram introduzidas primeiro, como um mecanismo de armazenamento de filas dedicado criado com base nos serviços de armazenamento do Azure Storage. As filas do Barramento de Serviço são criadas com base na infraestrutura mais ampla do sistema de mensagens, desenvolvida para integrar aplicativos ou componentes de aplicativo que podem incluir vários protocolos de comunicação, contratos de dados, domínios confiáveis e/ou ambientes de rede.

## <a name="technology-selection-considerations"></a>Considerações de seleção da tecnologia
As filas do Armazenamento e do Barramento de Serviço são implementações do serviço de enfileiramento de mensagens atualmente oferecido pelo Microsoft Azure. Cada uma tem um conjunto de recursos ligeiramente diferente, o que significa que você pode escolher uma ou outra, ou usar ambas, dependendo das necessidades da sua solução específica ou do problema comercial/técnico que está resolvendo.

Ao determinar qual tecnologia de enfileiramento é adequada ao propósito de uma determinada solução, os desenvolvedores e arquitetos de soluções devem considerar essas recomendações. Para obter mais detalhes, consulte a próxima seção.

Como arquiteto/desenvolvedor de soluções, **você deve considerar o uso das filas do Armazenamento** quando:

* O aplicativo deve armazenar mais de 80 GB de mensagens em uma fila.
* Seu aplicativo desejar rastrear o progresso do processamento de uma mensagem na fila. Isso será útil se o trabalho de processamento de uma mensagem falhar. Um trabalho subsequente pode usar essas informações para continuar de onde o anterior parou.
* Você precisar de logs do servidor de todas as transações executadas em suas filas.

Como arquiteto/desenvolvedor de soluções, **você deve considerar o uso das filas do Barramento de Serviço** quando:

* Sua solução precisar ser capaz de receber mensagens sem precisar sondar a fila. Com o Barramento de Serviço, isso pode ser alcançado pela operação de recebimento de sondagem longa usando os protocolos baseados em TCP que têm suporte do Barramento de Serviço.
* Sua solução exigir que a fila forneça uma entrega ordenada PEPS (primeiro a entrar, primeiro a sair).
* Sua solução precisar ser capaz de oferecer suporte à detecção automática de duplicidades.
* Você desejar que seu aplicativo processe mensagens como fluxos paralelos de longa execução (mensagens associadas a um fluxo usando a propriedade [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) na mensagem). Nesse modelo, cada nó no aplicativo de consumo compete por fluxos, em oposição às mensagens. Quando um fluxo é fornecido a um nó de consumo, o nó pode examinar o estado do fluxo do aplicativo usando transações.
* Sua solução exigir comportamento transacional e atomicidade ao enviar ou receber várias mensagens de uma fila.
* Seu aplicativo tratar mensagens que podem exceder 64 KB, mas provavelmente não se aproximarão do limite de 256 KB.
* Você tiver que lidar com a necessidade de fornecer um modelo de acesso baseado em função às filas e diferentes direitos/permissões para remetentes e destinatários.
* O tamanho da fila não for exceder 80 GB.
* Você desejar usar o protocolo do sistema de mensagens baseado em padrões AMQP 1.0. Para obter mais informações sobre AMQP, confira [Visão geral do AMQP do Barramento de Serviço](service-bus-amqp-overview.md).
* Você puder prever uma migração final da comunicação ponto a ponto baseada em fila para um padrão de troca de mensagens que permita a integração perfeita de receptores (assinantes) adicionais, cada um dos quais recebe cópias independentes de algumas ou todas as mensagens enviadas à fila. O último se refere ao recurso de publicação/assinatura fornecido originalmente pelo Barramento de Serviço.
* Sua solução de sistema de mensagens tiver que oferecer suporte à garantia de entrega "No máximo uma vez", sem a necessidade de criar componentes de infraestrutura adicional.
* Você desejar publicar e consumir lotes de mensagens.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Comparando as filas do Armazenamento e as filas do Barramento de Serviço
As tabelas nas seções a seguir fornecem um agrupamento lógico de recursos de fila e permitem que você compare, rapidamente, as funcionalidades disponíveis nas filas do Armazenamento do Azure e nas filas do Barramento de Serviço.

## <a name="foundational-capabilities"></a>Recursos básicos
Esta seção compara alguns dos recursos básicos de enfileiramento fornecidos pelas filas do Armazenamento e filas do Barramento de Serviço.

| Critérios de comparação | Filas de armazenamento | Filas do Barramento de Serviço |
| --- | --- | --- |
| Garantia de ordenação |**Não** <br/><br>Para obter mais informações, confira a primeira observação na seção “Informações adicionais”.</br> |**Sim - Primeiro a Entrar, Primeiro a Sair (PEPS)**<br/><br>(por meio do uso de sessões de mensagens) |
| Garantia de entrega |**Pelo menos uma vez** |**Pelo menos uma vez**<br/><br/>**No máximo uma vez** |
| Suporte à operação atômica |**Não** |**Sim**<br/><br/> |
| Comportamento de recebimento |**Sem bloqueio**<br/><br/>(conclusão imediata se nenhuma mensagem nova for encontrada) |**Bloqueio com/sem tempo limite**<br/><br/>(oferece sondagem longa ou ["Técnica Comet"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Sem bloqueio**<br/><br/>(somente por meio do uso de API gerenciada .NET) |
| API de estilo push |**Não** |**Sim**<br/><br/>[OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) e sessões **OnMessage** API .NET. |
| Modo de recebimento |**Inspecionar e Conceder** |**Inspecionar e bloquear**<br/><br/>**Receber e excluir** |
| Modo de acesso exclusivo |**Com base em concessão** |**Com base em bloqueio** |
| Duração da concessão/do bloqueio |**30 segundos (padrão)**<br/><br/>**7 dias (máximo)** (Você pode renovar ou liberar uma concessão de mensagem usando a API [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage).) |**60 segundos (padrão)**<br/><br/>Você pode renovar um bloqueio de mensagem usando a API [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock). |
| Precisão da concessão/do bloqueio |**Nível da mensagem**<br/><br/>(cada mensagem pode ter um valor de tempo limite diferente, que pode ser atualizado conforme a necessidade durante o processamento da mensagem, usando a API [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage)) |**Nível da fila**<br/><br/>(cada fila tem uma precisão de bloqueio aplicada a todas as suas mensagens, mas você pode renovar o bloqueio usando a API [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock).) |
| Recebimento em lote |**Sim**<br/><br/>(especificando explicitamente a contagem de mensagens ao recuperar mensagens, até um máximo de 32 mensagens) |**Sim**<br/><br/>(habilitando implicitamente uma propriedade de pré-busca ou explicitamente pelo uso de transações) |
| Envio em lote |**Não** |**Sim**<br/><br/>(usando transações ou envio em lote no lado do cliente) |

### <a name="additional-information"></a>Informações adicionais
* Geralmente, as mensagens nas filas do Armazenamento são do tipo PEPS, mas, às vezes, elas podem estar fora de ordem; por exemplo, quando a duração do tempo limite da visibilidade de uma mensagem expirar (por exemplo, como resultado de um travamento do aplicativo cliente durante o processamento). Quando o tempo limite da visibilidade se esgotar, a mensagem se tornará visível novamente na fila de outro trabalho para que seja removida da fila. Nesse ponto, a mensagem recentemente visível pode ser colocada na fila (para ser removida da fila novamente) após uma mensagem que foi originalmente enfileirada depois dela.
* O padrão PEPS garantido nas filas do Barramento de Serviço exige o uso de sessões de mensagens. Se o aplicativo travar durante o processamento de uma mensagem recebida no modo **Inspecionar e Bloquear**, na próxima vez que um destinatário da fila aceitar uma sessão de mensagens, ele começará com a mensagem com falha após a expiração do seu período TTL (vida útil).
* As filas do Armazenamento foram projetadas para oferecer suporte a cenários de enfileiramento padrão, como desassociação dos componentes de aplicativo para aumentar a escalabilidade e a tolerância para falhas, redistribuição de carga e criação de fluxos de trabalho do processo.
* As filas do Barramento de Serviço oferecem suporte à garantia de entrega *Pelo menos uma vez*. 
* Inconsistências com relação ao tratamento de mensagens no contexto de sessões de barramento de serviço podem ser evitadas usando o estado de sessão para armazenar o estado do aplicativo em relação ao progresso de lidar com a sequência de mensagem da sessão e uso de transações em torno de Liquidar recebeu mensagens e atualizar o estado de sessão. Esse tipo de recurso de consistência, às vezes, é rotulado *exatamente-uma vez processamento* em outro fornecedor produtos, mas transação falhas, obviamente, fará com que as mensagens ser entregue novamente e, portanto, o termo é não exatamente adequado.
* As filas do Armazenamento fornecem um modelo de programação uniforme e consistente entre filas, tabelas e Blobs, para desenvolvedores e equipes de operações.
* As filas do Barramento de Serviço fornecem suporte para transações locais no contexto de uma fila única.
* O modo **Receber e Excluir** com suporte do Barramento de Serviço tem a capacidade de reduzir a contagem de operações de mensagens (e custos associados) em troca da garantia de entrega reduzida.
* As filas do Armazenamento fornecem concessões com a capacidade de estender as concessões para mensagens. Isso permite que os trabalhos mantenham concessões curtas nas mensagens. Desse modo, se um trabalho travar, a mensagem poderá ser processada rapidamente de novo por outro trabalho. Além disso, um trabalho poderá estender a concessão em uma mensagem se precisar processá-la por mais tempo do que o tempo de concessão atual.
* As filas do Armazenamento oferecem um tempo limite de visibilidade que você pode definir no enfileiramento de uma mensagem ou na remoção dela da fila. Além disso, é possível atualizar uma mensagem com diferentes valores no tempo de execução, bem como atualizar diferentes valores entre mensagens na mesma fila. Os tempos limite de bloqueio do Barramento de Serviço são definidos nos metadados da fila. No entanto, é possível renovar o bloqueio chamando o método [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock).
* O tempo limite máximo para um bloqueio de operação de recebimento nas filas do Barramento de Serviço é de 24 dias. No entanto, os tempos limite baseados em REST têm um valor máximo de 55 segundos.
* O envio em lote do lado do cliente fornecido pelo Barramento de Serviço permite que um cliente da fila coloque em lote várias mensagens em uma única operação de envio. O envio em lote está disponível apenas para operações de envio assíncronas.
* Recursos como o limite máximo de 200 TB das filas do Armazenamento (mais quando você virtualiza contas) e filas ilimitadas a tornam uma plataforma ideal para provedores de SaaS.
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
| Métricas de armazenamento |**Sim**<br/><br/>**Métricas de Minuto**: fornecem métricas em tempo real para disponibilidade, TPS, contagens de chamada de API, contagens de erros e muito mais, tudo em tempo real (agregados por minuto e relatados poucos minutos após o que acabou de acontecer em produção). Para obter mais informações, confira [Sobre as Métricas de Analítica de Armazenamento](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Sim**<br/><br/>(consultas em massa chamando [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Gerenciamento de estado |**Não** |**Sim**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Encaminhamento automático de mensagem |**Não** |**Sim** |
| Função Limpar fila |**Sim** |**Não** |
| Grupos de mensagens |**Não** |**Sim**<br/><br/>(por meio do uso de sessões de mensagens) |
| Estado do aplicativo por grupo de mensagens |**Não** |**Sim** |
| Detecção de duplicidade |**Não** |**Sim**<br/><br/>(configurável no lado do remetente) |
| Procurando grupos de mensagens |**Não** |**Sim** |
| Buscando sessões de mensagem por ID |**Não** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* Ambas as tecnologias de enfileiramento permitem que uma mensagem seja agendada para entrega em um momento posterior.
* O encaminhamento automático da fila permite que milhares de filas encaminhem automaticamente suas mensagens para uma única fila, na qual o aplicativo de recebimento consome a mensagem. É possível usar esse mecanismo para fins de segurança, controlar o fluxo e isolar o armazenamento entre cada editor de mensagem.
* As filas do Armazenamento dão suporte à atualização do conteúdo da mensagem. Você pode usar essa funcionalidade para informações de estado persistentes e atualizações incrementais de progresso na mensagem para que ela possa ser processada a partir do último ponto de verificação conhecido, em vez de começar do zero. Com filas do Barramento de Serviço, você pode habilitar o mesmo cenário usando as sessões de mensagens. As sessões permitem salvar e recuperar o estado de processamento do aplicativo (usando [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* As [mensagens mortas](service-bus-dead-letter-queues.md), que têm suporte apenas nas filas do Barramento de Serviço, podem ser úteis para isolar mensagens que não podem ser processadas com êxito pelo aplicativo de recebimento ou quando as mensagens não podem atingir seu destino devido a uma propriedade de TTL expirada. O valor de TTL especifica quanto tempo uma mensagem permanece na fila. Com o Barramento de Serviço, a mensagem será movida para uma fila especial chamada $DeadLetterQueue quando o período TTL expirar.
* Para localizar mensagens “suspeitas” nas filas do Armazenamento, ao remover uma mensagem da fila, o aplicativo examina a propriedade [DequeueCount](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.dequeuecount.aspx) da mensagem. Se **DequeueCount** for maior que um determinado limite, o aplicativo moverá a mensagem para uma fila de "mensagens mortas" definida pelo aplicativo.
* As filas do Armazenamento permitem que você obtenha um log detalhado de todas as transações executadas na fila, bem como as métricas agregadas. Essas duas opções são úteis para depurar e entender como o aplicativo usa as filas do Armazenamento. Elas também são úteis para o ajuste de desempenho do aplicativo e redução dos custos do uso de filas.
* O conceito de "sessões de mensagens" com suporte do Barramento de Serviço habilita mensagens que pertencem a um determinado grupo lógico a serem associadas a um destinatário específico que, por sua vez, cria uma afinidade de sessão entre as mensagens e seus respectivos destinatários. Você pode habilitar essa funcionalidade avançada no Barramento de Serviço definindo a propriedade [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) em uma mensagem. Os receptores podem escutar em uma ID de sessão específica e receber mensagens que compartilham o identificador de sessão especificado.
* A funcionalidade de detecção de duplicação com suporte das filas do Barramento de Serviço remove automaticamente mensagens duplicadas enviadas a uma fila ou um tópico, com base no valor da propriedade [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId).

## <a name="capacity-and-quotas"></a>Capacidade e cotas
Esta seção compara as filas do Armazenamento e as filas do Barramento de Serviço da perspectiva de [capacidade e cotas](service-bus-quotas.md) que podem ser aplicáveis.

| Critérios de comparação | Filas de armazenamento | Filas do Barramento de Serviço |
| --- | --- | --- |
| Tamanho máximo da fila |**500 TB**<br/><br/>(limitado a uma [capacidade de conta de armazenamento única](../storage/common/storage-introduction.md#queue-storage)) |**1 GB a 80 GB**<br/><br/>(definido na criação de uma fila e [habilitando particionamento](service-bus-partitioning.md) – confira a seção "Informações adicionais") |
| Tamanho máximo da mensagem |**64 KB**<br/><br/>(48 KB ao usar a codificação **Base64**)<br/><br/>O Azure oferece suporte a mensagens grandes combinando filas e blobs — nesse ponto, você pode enfileirar até 200 GBs para um único item. |**256 KB** ou **1 MB**<br/><br/>(incluindo cabeçalho e corpo, tamanho máximo do cabeçalho: 64 KB).<br/><br/>Depende da [camada de serviço](service-bus-premium-messaging.md). |
| TTL máxima da mensagem |**Infinito** (a partir da versão da API de 27/07/2017) |**TimeSpan.Max** |
| Número máximo de filas |**Ilimitado** |**10,000**<br/><br/>(por namespace do serviço) |
| Número máximo de clientes simultâneos |**Ilimitado** |**Ilimitado**<br/><br/>(o limite de 100 conexões simultâneas se aplica somente à comunicação baseada no protocolo TCP) |

### <a name="additional-information"></a>Informações adicionais
* O Barramento de Serviço impõe limites de tamanho de fila. O tamanho máximo da fila é especificado na criação da fila e pode ter um valor entre 1 e 80 GB. Se o valor do tamanho da fila definido na criação da fila for atingido, mensagens de entrada adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. Para obter mais informações sobre cotas no Barramento de Serviço, confira [Cotas do Barramento de Serviço](service-bus-quotas.md).
* Não há suporte para particionamento na [Camada Premium](service-bus-premium-messaging.md). Na camada Standard, é possível criar filas do Barramento de Serviço nos tamanhos 1, 2, 3, 4 ou 5 GB (o padrão é 1 GB). No tipo Standard, com o particionamento habilitado (que é o padrão), o Barramento de Serviço cria 16 partições para cada GB especificado. Assim, se você criar uma fila que tenha 5 GB, com 16 partições, o tamanho máximo da fila será (5 * 16) = 80 GB. É possível ver o tamanho máximo da fila ou do tópico particionado observando sua entrada no [Portal do Azure][Azure portal].
* Com as filas do Armazenamento, se o conteúdo da mensagem não for XML seguro, ele deverá ser codificado em **Base64**. Se você codificar a mensagem em **Base64**, a carga de usuário poderá ser de até 48 KB, em vez de 64 KB.
* Com as filas do Barramento de Serviço, cada mensagem armazenada em uma fila é composta por duas partes: um cabeçalho e um corpo. O tamanho total da mensagem não pode exceder o tamanho máximo da mensagem com suporte da camada de serviço.
* Quando os clientes se comunicam com as filas do Barramento de Serviço pelo protocolo TCP, o número máximo de conexões simultâneas para uma única fila do Barramento de Serviço é limitado a 100. Esse número é compartilhado entre remetentes e receptores. Se essa cota for atingida, as solicitações subsequentes de conexões adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. Esse limite não é imposto a clientes que se conectam às filas usando a API baseada em REST.
* Se precisar de mais de 10.000 filas em um único namespace do Barramento de Serviço, você poderá entrar em contato com a equipe de suporte do Azure e solicitar um aumento. Para escalar para além de 10.000 filas com o Barramento de Serviço, você também pode criar namespaces adicionais usando o [Portal do Azure][Azure portal].

## <a name="management-and-operations"></a>Gerenciamento e operações
Esta seção compara os recursos de gerenciamento fornecidos pelas filas do Armazenamento e do Barramento de Serviço.

| Critérios de comparação | Filas de armazenamento | Filas do Barramento de Serviço |
| --- | --- | --- |
| Protocolo de gerenciamento |**REST sobre HTTP/HTTPS** |**REST sobre HTTPS** |
| Protocolo de tempo de execução |**REST sobre HTTP/HTTPS** |**REST sobre HTTPS**<br/><br/>**AMQP 1.0 Standard (TCP com TLS)** |
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
* As APIs do sistema de mensagens agenciado .NET do Barramento de Serviço aproveitam as conexões de TCP full duplex para melhor desempenho quando comparadas ao REST por HTTP, além de oferecerem suporte ao protocolo padrão AMQP 1.0.
* Os nomes das filas do Armazenamento podem ter de 3 a 63 caracteres e podem conter letras minúsculas, número e hifens. Para obter mais informações, confira [Nomeando filas e metadados](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Os nomes de fila do Barramento de Serviço podem ter até 260 caracteres e têm menos regras restritivas de nomenclatura. Os nomes de fila do Barramento de Serviço podem conter letras, números, pontos, hifens e sublinhados.

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Esta seção aborda os recursos de autenticação e autorização com suporte nas filas do Armazenamento e do Barramento de Serviço.

| Critérios de comparação | Filas de armazenamento | Filas do Barramento de Serviço |
| --- | --- | --- |
| Authentication |**Chave simétrica** |**Chave simétrica** |
| Modelo de segurança |Acesso delegado via tokens de SAS. |SAS |
| Federação do provedor de identidade |**Não** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* Cada solicitação de ambas as tecnologias de enfileiramento deve ser autenticada. Não há suporte para filas públicas com acesso anônimo. Usando [SAS](service-bus-sas.md), você pode tratar esse cenário publicando uma SAS somente gravação, SAS somente leitura ou até mesmo uma SAS de acesso completo.
* O esquema de autenticação fornecido pelas filas do Armazenamento envolve o uso de uma chave simétrica, que é um HMAC (Message Authentication Code) baseado em hash, calculado com o algoritmo SHA-256 e codificado como uma cadeia de caracteres **Base64**. Para obter mais informações sobre o respectivo protocolo, veja [Autenticação para os Serviços de Armazenamento do Azure](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). As filas do Barramento de Serviço oferecem suporte a um modelo semelhante usando chaves simétricas. Para obter mais informações, confira [Autenticação de Assinatura de Acesso Compartilhado com Barramento de Serviço](service-bus-sas.md).

## <a name="conclusion"></a>Conclusão
Com uma compreensão mais aprofundada das duas tecnologias, você poderá tomar uma decisão mais informada sobre qual tecnologia de fila usar, e quando. A decisão sobre quando usar as filas do Armazenamento ou as filas do Barramento de Serviço depende claramente de vários fatores. Esses fatores podem depender consideravelmente das necessidades individuais de seu aplicativo e respectiva arquitetura. Se seu aplicativo já utiliza os principais recursos do Microsoft Azure, talvez você prefira optar pelas filas do Armazenamento, especialmente se precisar de comunicação básica e sistema de mensagens entre serviços ou filas necessárias que podem ter mais de 80 GB.

Como as filas do Barramento de Serviço fornecem vários recursos avançados, como sessões, transações, detecção de duplicidade, mensagens mortas automáticas e recursos de publicação/assinatura duráveis, elas podem ser uma opção melhor se você estiver criando um aplicativo híbrido ou se seu aplicativo de alguma forma exigir esses recursos.

## <a name="next-steps"></a>Próximas etapas
Os artigos a seguir fornecem mais orientação e informações sobre como usar as filas do Armazenamento ou as filas do Barramento de Serviço.

* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar o serviço de armazenamento de filas](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Práticas recomendadas para melhorias de desempenho usando o sistema de mensagens agenciado do Barramento de Serviço](service-bus-performance-improvements.md)
* [Introdução a filas e tópicos no Barramento de Serviço do Azure (postagem de blog)](https://www.code-magazine.com/article.aspx?quickid=1112041)
* [Guia do desenvolvedor do Barramento de Serviço](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Usando o serviço de enfileiramento do Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

