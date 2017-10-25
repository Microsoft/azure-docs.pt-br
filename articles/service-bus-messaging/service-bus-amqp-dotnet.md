---
title: "Barramento de Serviço com o .NET e AMQP 1.0 | Microsoft Docs"
description: "Usando o Barramento de Serviço do Azure no .NET com AMQP"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 332bcb13-e287-4715-99ee-3d7d97396487
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2017
ms.author: sethm
ms.openlocfilehash: 58a37c0dd24d54996f517961f3a7f1ec36639cfe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-service-bus-from-net-with-amqp-10"></a>Usando o Barramento de Serviço do .NET com AMQP 1.0

## <a name="downloading-the-service-bus-sdk"></a>Baixar o SDK do Barramento de Serviço

O suporte para o AMQP 1.0 está disponível na versão 2.1 ou posterior do SDK do Barramento de Serviço. Você pode garantir que tem a versão mais recente baixando os bits do Barramento de Serviço do [NuGet][NuGet].

## <a name="configuring-net-applications-to-use-amqp-10"></a>Configurando aplicativos .NET para usar o AMQP 1.0

Por padrão, a biblioteca de cliente do .NET do Barramento de Serviço se comunica com o serviço do Barramento de Serviço usando um protocolo dedicado baseado em SOAP. O uso do AMQP 1.0 em vez do protocolo padrão requer a configuração explícita na cadeia de conexão do Service Bus, conforme descrito na próxima seção. Além dessa alteração, o código do aplicativo permanece inalterado ao usar o AMQP 1.0.

Na versão atual, existem alguns outros recursos da API que não são suportados com o uso do AMQP. Esses recursos sem suporte são listados posteriormente na seção [Recursos sem suporte, restrições e diferenças de comportamento](#unsupported-features-restrictions-and-behavioral-differences). Algumas das definições de configuração avançadas também apresentam um significado diferente com o uso do AMQP.

### <a name="configuration-using-appconfig"></a>Configuração usando App.config

É uma boa prática que os aplicativos usem o arquivo de configuração do App.config para armazenar as configurações. Para aplicativos do Barramento de Serviço, você pode usar o App.config para armazenar a cadeia de conexão do Barramento de Serviço. Um exemplo do arquivo App.config é mostrado abaixo:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

O valor da configuração `Microsoft.ServiceBus.ConnectionString` é a cadeia de conexão do Barramento de Serviço usada para configurar a conexão ao Barramento de Serviço. O formato é o seguinte:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Onde `namespace` e `SAS key` são obtidos a partir de [Portal do Azure][Azure portal] quando você cria um namespace do Barramento de Serviço. Para saber mais, veja [Como criar um namespace do Barramento de Serviço usando o Portal do Azure][Create a Service Bus namespace using the Azure portal].

Ao usar AMQP, anexe a cadeia de conexão com `;TransportType=Amqp`. Essa notação orienta a biblioteca de cliente a fazer sua conexão com o Barramento de Serviço usando o AMQP 1.0.

## <a name="message-serialization"></a>Serialização de mensagem

Ao usar o protocolo padrão, o comportamento de serialização padrão da biblioteca de cliente .NET é usar o tipo [DataContractSerializer][DataContractSerializer] para serializar uma instância [BrokeredMessage][BrokeredMessage] para transporte entre a biblioteca de cliente e o serviço de Barramento de Serviço. Ao usar o modo de transporte do AMQP, a biblioteca de cliente usa o sistema do tipo AMQP para a serialização da [mensagem agenciada][BrokeredMessage] em uma mensagem do AMQP. Essa serialização permite que a mensagem seja recebida e interpretada por um aplicativo receptor que está potencialmente em execução em uma plataforma diferente, por exemplo, um aplicativo Java que usa a API JMS para acessar o Barramento de Serviço.

Ao construir uma instância [BrokeredMessage][BrokeredMessage], você pode fornecer um objeto .NET como um parâmetro para o construtor para servir como o corpo da mensagem. Para objetos que podem ser mapeados para tipos primitivos do AMQP, o corpo é serializado em tipos de dados do AMQP. Se o objeto não puder ser mapeado diretamente para um tipo primitivo do AMQP, ou seja, um tipo personalizado definido pelo aplicativo, o objeto será serializado usando o [DataContractSerializer][DataContractSerializer], e os bytes serializados serão enviados em uma mensagem de dados AMQP.

Para facilitar a interoperabilidade com clientes não .NET, use somente tipos .NET que podem ser serializados diretamente em tipos do AMQP para o corpo da mensagem. A tabela a seguir detalha os tipos e o mapeamento correspondente ao sistema de tipos do AMQP.

| Tipo de objeto de corpo .NET | Tipo do AMQP mapeado | Tipo de seção de corpo do AMQP |
| --- | --- | --- |
| bool |Booliano |Valor do AMQP |
| byte |ubyte |Valor do AMQP |
| ushort |ushort |Valor do AMQP |
| uint |uint |Valor do AMQP |
| ulong |ulong |Valor do AMQP |
| sbyte |byte |Valor do AMQP |
| short |short |Valor do AMQP |
| int |int |Valor do AMQP |
| longo |longo |Valor do AMQP |
| flutuante |flutuante |Valor do AMQP |
| double |double |Valor do AMQP |
| decimal |decimal128 |Valor do AMQP |
| char |char |Valor do AMQP |
| DateTime |timestamp |Valor do AMQP |
| Guid |uuid |Valor do AMQP |
| byte[] |binário |Valor do AMQP |
| string |string |Valor do AMQP |
| System.Collections.IList |list |Valor AMQP: os itens contidos na coleção só podem ser aqueles definidos nesta tabela. |
| System.Array |array |Valor AMQP: os itens contidos na coleção só podem ser aqueles definidos nesta tabela. |
| System.Collections.IDictionary |map |Valor AMQP: os itens contidos na coleção só podem ser aqueles definidos nesta tabela. Observação: apenas as chaves de cadeia de caracteres são suportadas. |
| Uri |Cadeia de caracteres descrita (consulte a tabela a seguir) |Valor do AMQP |
| DateTimeOffset |Longo descrito (consulte a tabela a seguir) |Valor do AMQP |
| TimeSpan |Longo descrito (consulte a seguir) |Valor do AMQP |
| Fluxo |binário |Dados do AMQP (podem ser múltiplos). As seções de Dados contêm os bytes brutos lidos do objeto Stream. |
| Outro Objeto |binário |Dados do AMQP (podem ser múltiplos). Contém o binário serializado do objeto que usa o DataContractSerializer ou um serializador fornecido pelo aplicativo. |

| Tipo .NET | Tipo descrito do AMQP mapeado | Observações |
| --- | --- | --- |
| Uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |URI.AbsoluteUri |
| Datetimeoffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` |TimeSpan.Ticks |

## <a name="unsupported-features-restrictions-and-behavioral-differences"></a>Recursos sem suporte, restrições e diferenças de comportamento

Os seguintes recursos da API .NET do Barramento de Serviço não são atualmente suportados com o uso do AMQP:

* Transações
* Enviar por meio de destino da transferência

Também há algumas pequenas diferenças no comportamento da API .NET do Barramento de Serviço ao usar AMQP, em comparação com o protocolo padrão:

* A propriedade [OperationTimeout][OperationTimeout] será ignorada.
* `MessageReceiver.Receive(TimeSpan.Zero)` é implementado como `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* Só é possível concluir mensagens com tokens de bloqueio pelos destinatários de mensagem que inicialmente receberam as mensagens.

## <a name="controlling-amqp-protocol-settings"></a>Controlando configurações do protocolo AMQP

As [APIs .NET](/dotnet/api/) expõem diversas configurações que controlam o comportamento do protocolo AMQP:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: controla o crédito inicial aplicado a um link. O padrão é 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: controla o tamanho máximo do quadro do AMQP oferecido durante a negociação em tempo de conexão aberta. O padrão é de 65.536 bytes.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: caso as transferências possam ser em lote, esse valor determina o atraso máximo para envio de disposições. Herdado pelos remetentes/destinatários por padrão. O remetente/destinatário individual pode substituir o padrão, que é 20 milissegundos.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: controla se as conexões do AMQP são estabelecidas por uma conexão SSL. O padrão é **true**.

## <a name="next-steps"></a>Próximas etapas

Está pronto(a) para saber mais? Visite os links a seguir:

* [Visão geral do Barramento de Serviço para AMQP]
* [Guia do protocolo AMQP 1.0]
* [AMQP no Barramento de Serviço para Windows Server]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Visão geral do Barramento de Serviço para AMQP]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP no Barramento de Serviço para Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
