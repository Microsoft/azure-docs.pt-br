---
title: Reliable Services pilha de comunicação do WCF
description: A comunicação de serviço WCF interna no Service Fabric fornece a comunicação WCF cliente-serviço para o Reliable Services.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: c5b5d413eee2528e2d5c7d04d06f1607949beaae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89012697"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Pilha de comunicação baseada no WCF para Reliable Services
A estrutura de Serviços confiáveis permite que os autores do serviço escolham qual pilha de comunicação desejam usar para seu serviço. Eles podem ligar a pilha de comunicação de sua escolha por meio do **ICommunicationListener** retornado dos métodos [CreateServiceReplicaListeners ou CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) . A estrutura fornece uma implementação da pilha de comunicação baseada no WCF (Windows Communication Foundation) da pilha de comunicação para autores de serviço que desejam usar comunicação baseada no WCF.

## <a name="wcf-communication-listener"></a>Ouvinte de comunicação do WCF
A implementação específica do WCF do **ICommunicationListener** é fornecida pela classe **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener**.

Digamos que temos um contrato de serviço do tipo `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Podemos criar um ouvinte de comunicação do WCF no serviço da seguinte maneira.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Desenvolvimento de clientes para a pilha de comunicação do WCF
Para o desenvolvimento de clientes que se comuniquem com serviços usando o WCF, a estrutura fornece **WcfClientCommunicationFactory**, que é a implementação específica do WCF do [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

O canal de comunicação do WCF pode ser acessado do **WcfCommunicationClient** criado pelo **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

O código do cliente pode usar o **WcfCommunicationClientFactory** com o **WcfCommunicationClient** que implementa o **ServicePartitionClient** para determinar o ponto de extremidade de serviço e se comunicar com o serviço.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> O ServicePartitionResolver padrão supõe que o cliente está em execução no mesmo cluster que o serviço. Se este não for o caso, crie um objeto ServicePartitionResolver e passe os pontos de extremidade de conexão do cluster.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* [Chamada de procedimento remoto com Reliable Services remoto](service-fabric-reliable-services-communication-remoting.md)
* [API Web com OWIN no Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Securing communication for Reliable Services](service-fabric-reliable-services-secure-communication-wcf.md)
