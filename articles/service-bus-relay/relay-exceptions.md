---
title: "Exceções de Retransmissão do Azure e como resolvê-las | Microsoft Docs"
description: "Obtenha uma lista de exceções de Retransmissão do Azure e ações sugeridas que você pode realizar para ajudar a resolvê-las."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: 83ff97b59e428e7b617a7f5d1011ca5ddf3060b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-relay-exceptions"></a>Exceções de Retransmissão do Azure

Este artigo apresenta algumas exceções que podem ser geradas por APIs de Retransmissão do Azure. Essa referência está sujeita a alterações, então verifique se há atualizações.

## <a name="exception-categories"></a>Categorias de exceções

As APIs de Retransmissão geram exceções que podem se enquadrar nas categorias a seguir. Também são listadas as ações sugeridas que você pode adotar para ajudar a resolver as exceções.

*   **Erro de codificação do usuário**: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Ação geral**: tentar corrigir o código antes de prosseguir.
*   **Erro de instalação/configuração**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Ação geral**: examine sua configuração. Se necessário, altere a configuração.
*   **Exceções temporárias**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Ação geral**: repetir a operação ou notificar os usuários.
*   **Outras exceções**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Ação geral**: específica para o tipo de exceção. Consulte a tabela na seção a seguir. 

## <a name="exception-types"></a>Tipos de exceção

A tabela a seguir lista os tipos de exceção de mensagens e suas causas. Também indica as ações sugeridas que você pode executar para ajudar a resolver as exceções.

| **Tipo de exceção** | **Descrição** | **Ação sugerida** | **Observação sobre repetição automática ou imediata** |
| --- | --- | --- | --- |
| [Tempo limite](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu à operação solicitada dentro do tempo especificado que é controlado por [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). O servidor pode ter concluído a operação solicitada. Isso pode acontecer devido a rede ou outros atrasos de infraestrutura. |Verifique a consistência do estado do sistema e tente novamente, se necessário. Veja [TimeoutException](#timeoutexception). |Uma nova tentativa pode ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [Operação inválida](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação solicitada pelo usuário não é permitida no servidor ou serviço. Consulte a mensagem de exceção para obter detalhes. |Verifique o código e a documentação. Verifique se a operação solicitada é válida. |Tentar novamente não ajudará. |
| [Operação cancelada](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |É feita uma tentativa de invocar uma operação em um objeto que já foi fechado, anulado ou descartado. Em casos raros, a transação de ambiente já foi descartada. |Verifique o código e certifique-se de que ele não invocará operações em um objeto descartado. |Tentar novamente não ajudará. |
| [Acesso não autorizado](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |O [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) não pôde obter um token, o token é inválido ou não contém as declarações necessárias para executar a operação. |Verifique se o provedor de token foi criado com os valores corretos. Verifique a configuração do Serviço de Controle de Acesso. |Uma nova tentativa pode ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [Exceção de Argumento](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [Argumento Nulo](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argumento fora do intervalo](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Um ou mais dos seguintes ocorreu:<br />Um ou mais argumentos fornecidos para o método são inválidos.<br /> O URI fornecido para [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) contém um ou mais segmentos de caminho.<br />O esquema de URI fornecido para [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) é inválido. <br />O valor da propriedade é maior do que 32 KB. |Verifique o código de chamada e certifique-se de que os argumentos estão corretos. |Tentar novamente não ajudará. |
| [Servidor ocupado](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |O serviço não pode processar a solicitação neste momento. |O cliente pode esperar um período e então repetir a operação. |O cliente pode repetir a operação após um intervalo específico. Se uma nova tentativa resultar em uma exceção diferente, verifique o comportamento de repetição dessa exceção. |
| [Cota excedida](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |A entidade de mensagens atingiu seu tamanho máximo permitido. |Crie espaço na entidade ao receber mensagens da entidade ou de suas subfilas. Confira [QuotaExceededException](#quotaexceededexception). |Tentar novamente pode ajudar se as mensagens foram removidas nesse meio tempo. |
| [Tamanho da mensagem excedido](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |O conteúdo de uma mensagem excede o limite de 256 KB. Observe que o limite de 256 KB é o tamanho total da mensagem. O tamanho total da mensagem pode incluir propriedades do sistema e qualquer sobrecarga do Microsoft .NET. |Reduza o tamanho da carga de mensagem e repita a operação. |Tentar novamente não ajudará. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica que uma cota para uma entidade específica foi excedida.

Para a Retransmissão, essa exceção envolve [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), indicando que foi excedido o número máximo de ouvintes para esse ponto de extremidade. Isso é indicado no valor **MaximumListenersPerEndpoint** da mensagem de exceção.

## <a name="timeoutexception"></a>TimeoutException
Um [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo usuário está demorando mais do que o tempo limite da operação. 

Verifique o valor da propriedade [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit). Atingir esse limite também pode causar uma [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Para a Retransmissão, você poderá receber exceções de tempo limite ao abrir pela primeira vez uma conexão de remetente de retransmissão. Há duas causas comuns para essa exceção:

*   O valor [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) pode ser muito pequeno (mesmo que apenas por uma fração de segundo).
* Um ouvinte de retransmissão local pode não responder (ou encontrar problemas de regras de firewall que proíbam os ouvintes de aceitar novas conexões de cliente) e o valor [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) é menor que aproximadamente 20 segundos.

Exemplo:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Causas comuns
Há duas causas comuns para esse erro:

*   **Configuração incorreta**
    
    O tempo limite da operação pode ser muito pequeno para a condição operacional. O valor padrão para o tempo limite da operação no SDK do cliente é de 60 segundos. Verifique se o valor em seu código está definido para um nível pequeno demais. Observe que o uso da CPU e a condição da rede podem afetar o tempo necessário para a conclusão de uma operação. É uma boa ideia não definir o tempo limite da operação para um valor muito pequeno.
*   **Erro de serviço transitório**

    Ocasionalmente, o serviço Retransmissão pode sofrer atrasos no processamento de solicitações. Isso pode acontecer, por exemplo, durante períodos de tráfego intenso. Nesses casos, repita a operação após um atraso até que a operação seja bem-sucedida. Se a mesma operação continuar a falhar após várias tentativas, verifique o [Site de status do serviço do Azure](https://azure.microsoft.com/status/) para ver se há interrupções de serviço conhecidas.

## <a name="next-steps"></a>Próximas etapas
* [Perguntas frequentes sobre Retransmissão do Azure](relay-faq.md)
* [Criar um namespace de retransmissão](relay-create-namespace-portal.md)
* [Introdução à Retransmissão do Azure e .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução à Retransmissão do Azure e Nó](relay-hybrid-connections-node-get-started.md)

