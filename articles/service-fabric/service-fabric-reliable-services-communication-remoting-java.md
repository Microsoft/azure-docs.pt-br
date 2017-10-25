---
title: "Comunicação remota do serviço no Azure Service Fabric | Microsoft Docs"
description: "A comunicação remota do Service Fabric permite que os clientes e serviços se comuniquem com serviços que usam a chamada de procedimento remoto."
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: dc4a362b5737bb424ca2c196c85f4c51b6ee5e30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-remoting-with-reliable-services"></a>Comunicação remota de serviço com o Reliable Services
> [!div class="op_single_selector"]
> * [C# em Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java no Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

A estrutura do Reliable Services fornece um mecanismo de comunicação remota rápida para configurar de forma fácil e rápida a chamada de procedimento remoto para serviços.

## <a name="set-up-remoting-on-a-service"></a>Configurar a comunicação remota em um serviço
A configuração da comunicação remota de um serviço é feita em duas etapas simples:

1. Crie uma interface para implementar o serviço. Essa interface define os métodos disponíveis para uma chamada de procedimento remoto no seu serviço. Os métodos devem ser métodos assíncronos que retornam tarefas. A interface deve implementar `microsoft.serviceFabric.services.remoting.Service` para sinalizar que o serviço tem uma interface de comunicação remota.
2. Use um ouvinte de comunicação remota em seu serviço. Esta é uma implementação de `CommunicationListener` que fornece recursos de comunicação remota. `FabricTransportServiceRemotingListener` pode ser usado para criar um ouvinte de comunicação remota usando o protocolo de transporte de comunicação remota padrão.

Por exemplo, o serviço sem estado a seguir expõe um único método para obter "Hello World" pela chamada de procedimento remoto.

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> Os argumentos e os tipos de retorno na interface de serviço podem ser de tipos simples, complexos ou personalizados, mas devem ser serializáveis.
>
>

## <a name="call-remote-service-methods"></a>Chamar métodos de serviços remotos
A chamada de métodos em um serviço usando a pilha de comunicação remota é feita usando um proxy local para o serviço por meio da classe `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` . O método `ServiceProxyBase` cria um proxy local usando a mesma interface que o serviço implementa. Com esse proxy, você pode simplesmente chamar métodos na interface remotamente.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

A estrutura remota propaga exceções lançadas no serviço para o cliente. A lógica de manipulação de exceção no cliente usando `ServiceProxyBase` pode manipular diretamente as exceções que o serviço lança.

## <a name="service-proxy-lifetime"></a>Tempo de vida de Proxy do Serviço
A criação do ServiceProxy é uma operação simples, logo, o usuário pode criar quantos precisar. O Proxy do Serviço pode ser reutilizado desde que o usuário precise. O usuário pode reutilizar o mesmo proxy em caso de exceção. Cada ServiceProxy contém um cliente de comunicação usado para enviar mensagens durante a transmissão. Ao invocar a API, temos uma verificação interna para saber se o cliente de comunicação usado é válido. Com base nesse resultado, recriamos o cliente da comunicação. Portanto, o usuário não precisa recriar serviceproxy em caso de exceção.

### <a name="serviceproxyfactory-lifetime"></a>Tempo de vida de ServiceProxyFactory
[FabricServiceProxyFactory](https://docs.microsoft.com/en-us/java/api/microsoft.servicefabric.services.remoting.client._fabric_service_proxy_factory) é um alocador que cria um proxy para interfaces remotas diferentes. Se você usar a API `ServiceProxyBase.create` para criar um proxy, o framework cria um `FabricServiceProxyFactory`.
É útil para criá-lo manualmente quando você precisa substituir propriedades [ServiceRemotingClientFactory](https://docs.microsoft.com/en-us/java/api/microsoft.servicefabric.services.remoting.client._service_remoting_client_factory).
Alocador é uma operação cara. `FabricServiceProxyFactory` mantém o cache dos clientes de comunicação.
A prática recomendada é armazenar em cache `FabricServiceProxyFactory` por mais tempo possível.

## <a name="remoting-exception-handling"></a>Tratamento de exceções remotas
Todas as exceções remotas lançadas pela API de serviço são reenviadas para o cliente como RuntimeException ou FabricException.

ServiceProxy trata todas as exceções de failover da partição de serviço para a qual foi criado. Ele resolverá novamente os pontos de extremidade se houver exceções de failover (exceções não transitórias) e repetirá a chamada com o ponto de extremidade correto. O número de tentativas da exceção de failover é indefinido.
No caso de TransientExceptions, ele repete apenas a chamada.

Os parâmetros de repetição padrão são fornecidos por [OperationRetrySettings]. (https://docs.microsoft.com/en-us/java/api/microsoft.servicefabric.services.communication.client._operation_retry_settings) O usuário pode configurar esses valores passando o objeto OperationRetrySettings para o construtor ServiceProxyFactory.

## <a name="next-steps"></a>Próximas etapas
* [Securing communication for Reliable Services](service-fabric-reliable-services-secure-communication.md)
