---
title: Hubs de eventos do Azure-exceções (herdadas)
description: Este artigo fornece uma lista de exceções de mensagens dos Hubs de Eventos do Azure e ações sugeridas.
ms.topic: article
ms.date: 11/02/2020
ms.openlocfilehash: 357a87c53023962dd9195a616bd9ce9e01c55bf9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340960"
---
# <a name="event-hubs-messaging-exceptions---net-legacy"></a>Exceções de mensagens dos hubs de eventos – .NET (Herdado)
Esta seção lista as exceções .NET geradas por .NET Framework APIs. 

> [!IMPORTANT]
> Algumas das exceções listadas no artigo se aplicam somente à biblioteca .NET dos hubs de eventos herdados. Por exemplo: exceções Microsoft. ServiceBus. *.
> 
> Para obter informações sobre o EventHubsException gerado pela nova biblioteca .NET, consulte [EventHubsException-.net](exceptions-dotnet.md)

## <a name="exception-categories"></a>Categorias de exceções

As APIs do .NET dos hubs de eventos geram exceções que podem se enquadrar nas categorias a seguir, juntamente com a ação associada que você pode executar para tentar corrigi-las:

 - Erro de codificação do usuário: 
 
   - [System. ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true)
   - [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true)
   - [System.OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true)
   - [System. Runtime. Serialization. Serializaexception](/dotnet/api/system.runtime.serialization.serializationexception?view=netcore-3.1&preserve-view=true)
   
   Ação geral: tente corrigir o código antes de continuar.
 
 - Erro de instalação/configuração: 
 
   - [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception)
   - [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception)
   - [System.UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true)
   
   Ação geral: revise sua configuração e altere se necessário.
   
 - Exceções temporárias: 
 
   - [Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)
   - [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception)
   - [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception)
   - [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)
   
   Ação geral: repetir a operação ou notificar os usuários.
 
 - Outras exceções: 
 
   - [System. TransactionException](/dotnet/api/system.transactions.transactionexception?view=netcore-3.1&preserve-view=true)
   - [System.TimeoutException](#timeoutexception)
   - [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception)
   - [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)
   
   Ação geral: específica para o tipo de exceção; consulte a tabela na seção a seguir. 

## <a name="exception-types"></a>Tipos de exceção
A tabela a seguir relaciona os tipos de mensagens de exceção e suas causas e aponta a ação sugerida que você pode tomar.

| Tipo de exceção | Descrição/Causa/Exemplos | Ação sugerida | Observação sobre repetição automática/imediata |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) |O servidor não respondeu à operação solicitada dentro do tempo especificado, que é controlado por [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). O servidor pode ter concluído a operação solicitada. Essa exceção pode ocorrer devido à rede ou a outros atrasos de infraestrutura. |Verifique o estado do sistema para manter a consistência e tente novamente, se necessário.<br /> Veja [TimeoutException](#timeoutexception). | Uma nova tentativa pode ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true) |A operação de usuário solicitada não é permitida no servidor ou serviço. Consulte a mensagem de exceção para obter detalhes. Por exemplo, [Concluir](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) gerará essa exceção se a mensagem tiver sido recebida no modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) . | Verifique o código e a documentação. Verifique se a operação solicitada é válida. | A repetição não ajudará. |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true) | É feita uma tentativa de invocar uma operação em um objeto que já foi fechado, anulado ou descartado. Em casos raros, a transação de ambiente já foi descartada. | Verifique o código e certifique-se de que ele não invoque operações em um objeto Descartado. | A repetição não ajudará. |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true) | O objeto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) não pôde adquirir um token, o token é inválido ou o token não contém as declarações necessárias para realizar a operação. | Verifique se o provedor de token for criado com os valores corretos. Verifique a configuração do serviço de controle de acesso. | Uma nova tentativa pode ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception?view=netcore-3.1&preserve-view=true)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception?view=netcore-3.1&preserve-view=true) | Um ou mais argumentos fornecidos para o método são inválidos. O URI fornecido para [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contém segmento(s) de caminho. O esquema de URI fornecido para [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inválido. O valor da propriedade é maior do que 32 KB. | Verifique o código de chamada e certifique-se de que os argumentos estão corretos. | Tentar novamente não ajudará. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | A entidade associada à operação não existe ou foi excluída. | Certifique-se de que a entidade existe. | Tentar novamente não ajudará. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | O cliente não pode estabelecer uma conexão com o Hub de Eventos. |Verifique se o nome de host fornecido está correto e se o host está acessível. | Tentar novamente poderá ajudar se houver problemas intermitentes de conectividade. |
| [Microsoft. ServiceBus. Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | O serviço não pode processar a solicitação neste momento. | O cliente pode esperar um período de tempo e repetir a operação. <br /> Veja [ServerBusyException](#serverbusyexception). | O cliente pode tentar novamente após um intervalo determinado. Se uma nova tentativa resultar em uma exceção diferente, verifique o comportamento de repetição de tal exceção. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | A exceção de mensagens genéricas pode ser lançada nos seguintes casos: uma tentativa é feita para criar um [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) usando um nome ou caminho que pertence a um tipo de entidade diferente (por exemplo, um tópico). A tentativa é feita para enviar uma mensagem maior que 1 MB. O servidor ou o serviço encontrou um erro durante o processamento da solicitação. Consulte a mensagem de exceção para obter detalhes. Essa exceção geralmente é uma exceção transitória. | Verifique o código e certifique-se de que somente objetos serializáveis sejam usados para o corpo da mensagem (ou use um serializador personalizado). Verifique a documentação para os tipos de valor com suporte das propriedades e somente os tipos de uso com suporte. Verifique a propriedade [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Se for **true**, você pode tentar a operação novamente. | O comportamento de repetição é indefinido e pode não ajudar. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Tentativa de criar uma entidade com um nome que já está sendo usado por outra entidade no namespace de serviço. | Exclua a entidade existente ou escolha um nome diferente para a entidade a ser criada. | Tentar novamente não ajudará. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | A entidade de mensagens atingiu seu tamanho máximo permitido. Essa exceção pode acontecer se o número máximo de receptores (que é 5) já tiver sido aberto em um nível de grupo por consumidor. | Crie espaço na entidade ao receber mensagens da entidade ou de suas subfilas. <br /> Consulte [QuotaExceededException](#quotaexceededexception) | Tentar novamente pode ajudar se as mensagens foram removidas nesse meio tempo. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Solicitação para uma operação de runtime em uma entidade desabilitada. |Ativar a entidade. | Tentar novamente poderá ajudar se a entidade tiver sido ativada durante o processo. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Uma carga de mensagem excede o limite de 1 MB. Esse limite de 1 MB é para a mensagem total, que pode incluir propriedades do sistema e qualquer sobrecarga do .NET. | Reduza o tamanho da carga de mensagem e repita a operação. |Tentar novamente não ajudará. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica que uma cota para uma entidade específica foi excedida.

Essa exceção pode acontecer se o número máximo de receptores (5) já tiver sido aberto em um nível de grupo por consumidor.

### <a name="event-hubs"></a>Hubs de Eventos
Os Hubs de Eventos têm um limite de 20 grupos de consumidores por Hub de Eventos. Quando você tenta criar mais, recebe [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
Um [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) indica que uma operação iniciada pelo usuário está demorando mais do que o tempo limite da operação. 

Para os Hubs de Eventos, o tempo limite é especificado como parte da cadeia de conexão ou por meio de [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). A mensagem de erro pode variar, mas ele sempre contém o valor de tempo limite especificado para a operação atual. 

Espera-se que os tempos limite ocorram durante ou entre operações de manutenção, como atualizações de serviço de hubs de eventos (ou) atualizações de sistema operacional em recursos que executam o serviço. Durante as atualizações do sistema operacional, as entidades são movidas e os nós são atualizados ou reinicializados, o que pode causar tempos limite. Para obter detalhes de SLA (contrato de nível de serviço) para o serviço de hubs de eventos do Azure, consulte [SLA para hubs de eventos](https://azure.microsoft.com/support/legal/sla/event-hubs/). 


### <a name="common-causes"></a>Causas comuns
Há duas causas comuns desse erro: configuração incorreta ou um erro de serviço transitório.

- **Configuração incorreta** O tempo limite da operação pode ser muito pequeno para a condição operacional. O valor padrão para o tempo limite da operação no SDK do cliente é de 60 segundos. Verifique para ver se o código tem o valor definido para algo muito pequeno. A condição de uso de CPU e de rede pode afetar o tempo que leva para a conclusão de uma determinada operação, de modo que o tempo limite da operação não deve ser definido como um valor pequeno.
- **Erro de serviço temporário** Às vezes, o serviço de Hubs de Eventos pode sofrer atrasos nas solicitações de processamento; por exemplo, durante os períodos de tráfego intenso. Nesses casos, você pode repetir a operação após um atraso, até que a operação seja bem-sucedida. Se a mesma operação continuar falhando após várias tentativas, visite o [site de status do serviço do Azure](https://azure.microsoft.com/status/) para ver se há qualquer interrupção de serviço conhecida.

## <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) ou [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) indica que um servidor está sobrecarregado. Há dois códigos de erro relevantes para essa exceção.

### <a name="error-code-50002"></a>Código do erro 50002
Esse erro pode ocorrer por um dos seguintes motivos:

- A carga não é distribuída uniformemente entre todas as partições no Hub de eventos e uma partição atinge a limitação da unidade de produtividade local.
    
    **Resolução**: a revisão da estratégia de distribuição de partição ou a tentativa de [EventHubClient. Send (eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) pode ajudar.

- O namespace de hubs de eventos não tem unidades de produtividade suficientes (você pode verificar a tela de **métricas** na janela de namespace de hubs de eventos no [portal do Azure](https://portal.azure.com) para confirmar). O portal mostra informações agregadas (1 minuto), mas medimos a taxa de transferência em tempo real – portanto, é apenas uma estimativa.

    **Resolução**: aumentar as unidades de produtividade no namespace pode ajudar. 

    Você pode configurar unidades de produtividade na página **escala** ou na página **visão geral** da página de namespace de seus **hubs de eventos** no portal do Azure. Ou, você pode usar o [inflar](event-hubs-auto-inflate.md)automaticamente, que é escalado verticalmente aumentando o número de unidades de produtividade para atender às necessidades de uso.

    As unidades de taxa de transferência (TUs) se aplicam a todos os hubs de eventos em um namespace de hubs de eventos. Isso significa que você compra TUs no nível do namespace e elas são compartilhadas entre os hubs de eventos no namespace. Cada TU proporciona ao namespace os seguintes recursos:

    - Até 1 MB por segundo de eventos de entrada (eventos enviados para um Hub de Eventos), mas não mais de 1.000 eventos de entrada, operações de gerenciamento ou chamadas por segundo à API de controle.
    - Até 2 MB por segundo de eventos de saída (eventos consumidos de um Hub de Eventos), mas não mais do que 4096 eventos de saída.
    - Até 84 GB de armazenamento de eventos (suficiente para o período de retenção padrão de 24 horas).
    
    Na página **visão geral** , na seção **Mostrar métricas** , alterne para a guia **taxa de transferência** . Selecione o gráfico para abri-lo em uma janela maior com intervalos de 1 minuto no eixo x. Examine os valores de pico e divida-os por 60 para obter bytes de entrada/segundo ou bytes de saída/segundo. Use uma abordagem semelhante para calcular o número de solicitações por segundo em horários de pico na guia **solicitações** . 

    Se você vir valores maiores do que o número de limites de TUs * (1 MB por segundo para solicitações de entrada ou 1000 para entrada/segundo, 2 MB por segundo para saída), aumente o número de TUs usando o **dimensionamento** (no menu à esquerda) página de um namespace de hubs de eventos para dimensionar manualmente mais ou usar o recurso de [inflar automaticamente](event-hubs-auto-inflate.md) dos hubs de eventos. Observe que o inflar automaticamente pode aumentar até 20 TUS. Para prometê-lo a exatamente 40 TUs, envie uma [solicitação de suporte](../azure-portal/supportability/how-to-create-azure-support-request.md).

### <a name="error-code-50001"></a>Código do erro 50001

Esse erro deve ocorrer raramente. Isso acontece quando o contêiner executando o código para seu namespace é insuficiente na CPU – não mais do que alguns segundos, antes de o balanceador de carga dos Hubs de Eventos ser iniciado.

**Resolução**: limite em chamadas para o método GetRuntimeInformation. Os hubs de eventos do Azure dão suporte a até 50 chamadas por segundo para o GetRuntimeInfo por segundo. Você pode receber uma exceção semelhante à seguinte uma vez que o limite for atingido:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```


## <a name="next-steps"></a>Próximas etapas

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão geral de Hubs de Eventos](./event-hubs-about.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)