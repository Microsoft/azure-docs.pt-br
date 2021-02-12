---
title: Barramento de serviço do Azure-exceções de mensagens | Microsoft Docs
description: Este artigo fornece uma lista de exceções de mensagens do barramento de serviço do Azure e ações sugeridas a serem executadas quando a exceção ocorre.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 3b56aff2635593d6cb49adbcf3784ddd5cb4fa39
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219138"
---
# <a name="service-bus-messaging-exceptions"></a>Exceções de mensagens do Barramento de Serviço
Este artigo lista as exceções .NET geradas por .NET Framework APIs. 

## <a name="exception-categories"></a>Categorias de exceções
As APIs de mensagens geram exceções que podem se enquadrar nas categorias a seguir, junto com a ação associada que pode ser tomada para tentar corrigi-las. O significado e as causas de uma exceção podem variar dependendo do tipo de entidade de mensagens:

1. Erro de codificação do usuário ([System. ArgumentException](/dotnet/api/system.argumentexception), [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception), [System. OperationCanceledException](/dotnet/api/system.operationcanceledexception), [System. Runtime. Serialization. serializaexception](/dotnet/api/system.runtime.serialization.serializationexception)). Ação geral: tentar corrigir o código antes de prosseguir.
2. Erro de instalação/configuração ([Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception). Ação geral: examinar sua configuração e alterá-la, se necessário.
3. Exceções transitórias ([Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Ação geral: repetir a operação ou notificar os usuários. A `RetryPolicy` classe no SDK do cliente pode ser configurada para tratar repetições automaticamente. Para obter mais informações, consulte [diretrizes de repetição](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Outras exceções ([System.Transactions.TransactionException](/dotnet/api/system.transactions.transactionexception), [System.TimeoutException](/dotnet/api/system.timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Ação geral: específica para o tipo de exceção; consulte a tabela na seção a seguir: 

## <a name="exception-types"></a>Tipos de exceção
A tabela a seguir relaciona os tipos de mensagens de exceção e suas causas e aponta a ação sugerida que você pode tomar.

| **Tipo de exceção** | **Descrição/Causa/Exemplos** | **Ação sugerida** | **Observação sobre repetição automática/imediata** |
| --- | --- | --- | --- |
| [TimeoutException](/dotnet/api/system.timeoutexception) |O servidor não respondeu à operação solicitada dentro do tempo especificado, que é controlado por [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). O servidor pode ter concluído a operação solicitada. Isso pode acontecer devido à rede ou a outros atrasos de infraestrutura. |Verifique o estado do sistema para manter a consistência e tente novamente, se necessário. Confira [TimeoutException](#timeoutexception). |Uma nova tentativa pode ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception) |A operação de usuário solicitada não é permitida no servidor ou serviço. Consulte a mensagem de exceção para obter detalhes. Por exemplo, [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) gera essa exceção se a mensagem foi recebida no modo [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) . |Verifique o código e a documentação. Verifique se a operação solicitada é válida. |A repetição não ajuda. |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception) |É feita uma tentativa de invocar uma operação em um objeto que já foi fechado, anulado ou descartado. Em casos raros, a transação de ambiente já foi descartada. |Verifique o código e certifique-se de que ele não invoque operações em um objeto Descartado. |A repetição não ajuda. |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) |O objeto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) não pôde adquirir um token, o token é inválido ou o token não contém as declarações necessárias para realizar a operação. |Verifique se o provedor de token for criado com os valores corretos. Verifique a configuração do serviço de controle de acesso. |Uma nova tentativa pode ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [ArgumentException](/dotnet/api/system.argumentexception)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception) |Um ou mais argumentos fornecidos para o método são inválidos.<br /> O URI fornecido para [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contém segmento(s) de caminho.<br /> O esquema de URI fornecido para [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inválido. <br />O valor da propriedade é maior do que 32 KB. |Verifique o código de chamada e certifique-se de que os argumentos estão corretos. |A repetição não ajuda. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |A entidade associada à operação não existe ou foi excluída. |Certifique-se de que a entidade existe. |A repetição não ajuda. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Tentativa para receber uma mensagem com um número de sequência específico. Esta mensagem não foi encontrada. |Certifique-se de que a mensagem ainda não tenha sido recebida. Verifique a fila de mensagens mortas para ver se a mensagem foi colocada ali. |A repetição não ajuda. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |O cliente não é capaz de estabelecer uma conexão com o barramento de serviço. |Verifique se o nome de host fornecido está correto e se o host está acessível. <p>Se o seu código for executado em um ambiente com um firewall/proxy, verifique se o tráfego para o endereço IP/domínio do barramento de serviço e as portas não está bloqueado.</p>|Tentar novamente poderá ajudar se houver problemas intermitentes de conectividade. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |O serviço não é capaz de processar a solicitação no momento. |O cliente pode esperar um período de tempo e repetir a operação. |O cliente pode tentar novamente após um intervalo determinado. Se uma nova tentativa resultar em uma exceção diferente, verifique o comportamento de repetição de tal exceção. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |A exceção de mensagens genéricas pode ser lançada nos seguintes casos:<p>Uma tentativa é feita para criar um [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) usando um nome ou caminho que pertence a um tipo de entidade diferente (por exemplo, um tópico).</p><p>A tentativa de TAn é feita para enviar uma mensagem maior que 256 KB. </p>O servidor ou o serviço encontrou um erro durante o processamento da solicitação. Consulte a mensagem de exceção para obter detalhes. Geralmente é uma exceção transitória.</p><p>A solicitação foi encerrada porque a entidade está sendo limitada. Código de erro: 50001, 50002, 50008. </p> | Verifique o código e certifique-se de que somente objetos serializáveis sejam usados para o corpo da mensagem (ou use um serializador personalizado). <p>Verifique a documentação para os tipos de valor com suporte das propriedades e somente os tipos de uso com suporte.</p><p> Verifique a propriedade [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Se for **true**, você poderá repetir a operação. </p>| Se a exceção for devido à limitação, aguarde alguns segundos e repita a operação. O comportamento de repetição é indefinido e pode não ajudar em outros cenários.|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Tentativa de criar uma entidade com um nome que já está sendo usado por outra entidade no namespace de serviço. |Exclua a entidade existente ou escolha um nome diferente para a entidade a ser criada. |A repetição não ajuda. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |A entidade de mensagens atingiu o tamanho máximo permitido ou o número máximo de conexões com um namespace foi excedido. |Crie espaço na entidade ao receber mensagens da entidade ou de suas subfilas. Confira [QuotaExceededException](#quotaexceededexception). |Tentar novamente pode ajudar se as mensagens foram removidas nesse meio tempo. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |O Barramento de Serviço retorna essa exceção se você tentar criar uma ação de regra inválida. Barramento de Serviço anexa essa exceção a uma mensagem morta se ocorrer um erro ao processar a ação de regra para a mensagem. |Verifique se a ação da regra está correta. |A repetição não ajuda. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |O Barramento de Serviço retorna essa exceção se você tentar criar um filtro inválido. O Barramento de Serviço anexa essa exceção a uma mensagem morta se ocorreu um erro ao processar o filtro para a mensagem. |Verifique se o filtro está correto. |A repetição não ajuda. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Tentativa de aceitar uma sessão com uma ID de sessão específica, mas a sessão está atualmente bloqueada por outro cliente. |Verifique se a sessão foi desbloqueada por outros clientes. |Tentar novamente pode ajudar se a sessão foi liberada nesse meio tempo. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Muitas operações fazem parte da transação. |Reduza o número de operações que fazem parte dessa transação. |A repetição não ajuda. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Solicitação para uma operação de runtime em uma entidade desabilitada. |Ativar a entidade. |Tentar novamente poderá ajudar se a entidade tiver sido ativada durante o processo. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |O Barramento de Serviço retornará essa exceção se você enviar uma mensagem para um tópico que tem a pré-filtragem habilitada e se nenhum dos filtros for compatível. |Verifique se pelo menos um filtro é compatível. |A repetição não ajuda. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |O conteúdo de uma mensagem excede o limite de 256 KB. O limite de 256 KB é o tamanho total da mensagem, que pode incluir propriedades do sistema e qualquer sobrecarga do .NET. |Reduza o tamanho da carga de mensagem e repita a operação. |A repetição não ajuda. |
| [TransactionException](/dotnet/api/system.transactions.transactionexception) |A transação de ambiente (*Transaction.Current*) é inválida. Ela pode ter sido concluída ou anulada. A exceção interna pode fornecer informações adicionais. | |A repetição não ajuda. |
| [TransactionInDoubtException](/dotnet/api/system.transactions.transactionindoubtexception) |Uma operação é tentada em uma transação incerta, ou uma tentativa é feita para confirmar a transação e ela se torna incerta. |Seu aplicativo deve tratar essa exceção (como um caso especial), visto que a transação pode já ter sido confirmada. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) indica que uma cota para uma entidade específica foi excedida.

### <a name="queues-and-topics"></a>Filas e tópicos
Para filas e tópicos, geralmente é o tamanho da fila. A propriedade de mensagem de erro contém mais detalhes, como no exemplo a seguir:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

A mensagem informa que o tópico excedeu seu limite de tamanho, neste caso 1 GB (o limite de tamanho padrão). 

### <a name="namespaces"></a>Namespaces

Para namespaces, [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) pode indicar que um aplicativo excedeu o número máximo de conexões com um namespace. Por exemplo:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>Causas comuns
Há duas causas comuns desse erro: a fila de mensagens mortas e os destinatários de mensagem não estão funcionando.

1. **[Fila de mensagens mortas](service-bus-dead-letter-queues.md)** Um leitor está falhando em concluir mensagens e as mensagens são retornadas para a fila/tópico quando o bloqueio expira. Isso pode acontecer se o leitor encontrar uma exceção que impeça a chamada de [BrokeredMessage. Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Depois que uma mensagem foi lida 10 vezes, ela é movida para a fila de mensagens mortas por padrão. Esse comportamento é controlado pela propriedade [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) e tem um valor padrão de 10. Conforme as mensagens são acumuladas na fila de mensagens mortas, elas ocupam espaço.
   
    Para resolver o problema, leia e conclua as mensagens da fila de mensagens mortas, como faria em qualquer outra fila. Você pode usar o método [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) para ajudar a formatar o caminho da fila de mensagens mortas.
2. **Receptor parado**. Um receptor parou de receber mensagens de uma fila ou assinatura. A maneira de identificar isso é examinar a propriedade [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) , que mostra a análise completa das mensagens. Se a propriedade [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) for alta ou em crescimento, as mensagens não serão lidas tão rapidamente quanto estão sendo gravadas.

## <a name="timeoutexception"></a>TimeoutException
Um [TimeoutException](/dotnet/api/system.timeoutexception) indica que uma operação iniciada pelo usuário está demorando mais do que o tempo limite da operação. 

Você deve verificar o valor da propriedade [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit), pois atingir esse limite também pode causar um [TimeoutException](/dotnet/api/system.timeoutexception).

Espera-se que os tempos limite ocorram durante ou entre operações de manutenção, como atualizações de serviço do barramento de serviço (ou) atualizações do sistema operacional em recursos que executam o serviço. Durante as atualizações do sistema operacional, as entidades são movidas e os nós são atualizados ou reinicializados, o que pode causar tempos limite. Para obter detalhes de SLA (contrato de nível de serviço) para o serviço do barramento de serviço do Azure, consulte [SLA para o barramento de serviço](https://azure.microsoft.com/support/legal/sla/service-bus/).


### <a name="queues-and-topics"></a>Filas e tópicos
Para filas e tópicos, o tempo limite é especificado na propriedade [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings), como parte da cadeia de conexão, ou por meio de [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). A mensagem de erro pode variar, mas ele sempre contém o valor de tempo limite especificado para a operação atual. 

## <a name="messagelocklostexception"></a>MessageLockLostException

### <a name="cause"></a>Causa

O **MessageLockLostException** é gerado quando uma mensagem é recebida usando o modo de recebimento [Peeklock](message-transfers-locks-settlement.md#peeklock) e o bloqueio mantido pelo cliente expira no lado do serviço.

O bloqueio em uma mensagem pode expirar devido a vários motivos- 

  * O temporizador de bloqueio expirou antes de ser renovado pelo aplicativo cliente.
  * O aplicativo cliente adquiriu o bloqueio, o salvou em um repositório persistente e, em seguida, reiniciado. Após a reinicialização, o aplicativo cliente examinou as mensagens em andamento e tentou concluí-las.

### <a name="resolution"></a>Resolução

No caso de um **MessageLockLostException**, o aplicativo cliente não pode mais processar a mensagem. O aplicativo cliente pode, opcionalmente, considerar o registro em log da exceção para análise, mas o cliente *deve* descartar a mensagem.

Como o bloqueio na mensagem expirou, ele voltaria para a fila (ou assinatura) e pode ser processado pelo próximo aplicativo cliente que chama Receive.

Se o **MaxDeliveryCount** tiver excedido, a mensagem poderá ser movida para o **DeadLetterQueue**.

## <a name="sessionlocklostexception"></a>SessionLockLostException

### <a name="cause"></a>Causa

O **SessionLockLostException** é gerado quando uma sessão é aceita e o bloqueio mantido pelo cliente expira no lado do serviço.

O bloqueio em uma sessão pode expirar devido a vários motivos- 

  * O temporizador de bloqueio expirou antes de ser renovado pelo aplicativo cliente.
  * O aplicativo cliente adquiriu o bloqueio, o salvou em um repositório persistente e, em seguida, reiniciado. Depois de reiniciado, o aplicativo cliente examinou as sessões em andamento e tentou processar as mensagens nessas sessões.

### <a name="resolution"></a>Resolução

No caso de um **SessionLockLostException**, o aplicativo cliente não pode mais processar as mensagens na sessão. O aplicativo cliente pode considerar registrar a exceção para análise, mas o cliente *deve* descartar a mensagem.

Como o bloqueio na sessão expirou, ele voltaria para a fila (ou assinatura) e pode ser bloqueado pelo próximo aplicativo cliente que aceita a sessão. Como o bloqueio de sessão é mantido por um único aplicativo cliente em um determinado momento, o processamento em ordem é garantido.

## <a name="socketexception"></a>SocketException

### <a name="cause"></a>Causa

Uma **SocketException** é lançada nos casos abaixo-
   * Quando uma tentativa de conexão falha porque o host não respondeu corretamente após uma hora especificada (código de erro TCP 10060).
   * Uma conexão estabelecida falhou porque o host conectado falhou ao responder.
   * Ocorreu um erro ao processar a mensagem ou o tempo limite foi excedido pelo host remoto.
   * Problema de recurso de rede subjacente.

### <a name="resolution"></a>Resolução

Os erros **SocketException** indicam que a VM que hospeda os aplicativos não pode converter o nome `<mynamespace>.servicebus.windows.net` para o endereço IP correspondente. 

Verifique se o comando abaixo foi executado com sucesso no mapeamento para um endereço IP.

```Powershell
PS C:\> nslookup <mynamespace>.servicebus.windows.net
```

que deve fornecer uma saída como abaixo

```bash
Name:    <cloudappinstance>.cloudapp.net
Address:  XX.XX.XXX.240
Aliases:  <mynamespace>.servicebus.windows.net
```

Se o nome acima **não resolver** para um IP e o alias de namespace, verifique o administrador da rede para investigar mais detalhadamente. A resolução de nomes é feita por meio de um servidor DNS normalmente um recurso na rede do cliente. Se a resolução de DNS for feita pelo DNS do Azure, entre em contato com o suporte do Azure.

Se a resolução de nomes **funcionar conforme o esperado**, verifique se as conexões com o barramento de serviço do Azure são permitidas [aqui](service-bus-troubleshooting-guide.md#connectivity-certificate-or-timeout-issues)


## <a name="messagingexception"></a>MessagingException

### <a name="cause"></a>Causa

**MessagingException** é uma exceção genérica que pode ser lançada por vários motivos. Alguns dos motivos estão listados abaixo.

   * É feita uma tentativa de criar um **QueueClient** em um **tópico** ou uma **assinatura**.
   * O tamanho da mensagem enviada é maior que o limite para a camada especificada. Leia mais sobre as [cotas e limites](service-bus-quotas.md)do barramento de serviço.
   * A solicitação do plano de dados específico (enviar, receber, concluir, abandonar) foi encerrada devido à limitação.
   * Problemas transitórios causados devido a atualizações e reinicializações de serviço.

> [!NOTE]
> A lista de exceções acima não é exaustiva.

### <a name="resolution"></a>Resolução

As etapas de resolução dependem do que causou a geração de **MessagingException** .

   * Para **problemas transitórios** (em que **_istransitória_*_ é definido como _*_true_*_) ou para problemas de limitação _***, repetir a operação pode resolvê-lo. A política de repetição padrão no SDK pode ser utilizada para isso.
   * Para outros problemas, os detalhes na exceção indicam que o problema e as etapas de resolução podem ser deduzidos do mesmo.

## <a name="next-steps"></a>Próximas etapas
Para obter a referência completa da API do .NET de Barramento de Serviço, consulte a [Referência de API do .NET do Azure](/dotnet/api/overview/azure/service-bus).
Para obter dicas de solução de problemas, consulte o [Guia de solução de problemas](service-bus-troubleshooting-guide.md)
