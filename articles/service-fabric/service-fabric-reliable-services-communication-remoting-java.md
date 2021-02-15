---
title: Comunicação remota do serviço usando Java no Azure Service Fabric
description: A comunicação remota do Service Fabric permite que os clientes e serviços se comuniquem com serviços Java que usam a chamada de procedimento remoto.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: d53d20510db70d81aab796efab48de40c880bb3a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87316117"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Comunicação Remota do Serviço em Java com Reliable Services
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java no Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Para serviços que não estão vinculados a um protocolo de comunicação específico ou pilha, como WebAPI, WCF (Windows Communication Foundation) ou outros, a estrutura do Reliable Services fornece um mecanismo de comunicação remota para configurar a chamada de procedimento remoto para serviços de forma rápida e fácil.  Este artigo descreve como configurar chamadas de procedimento remoto para serviços criados com Java.

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
A criação do Proxy do Serviço é uma operação simples, de modo que os usuários podem criar quantos forem necessários. As instâncias do Proxy do Serviço podem ser reutilizadas enquanto os usuários precisarem. Se uma chamada de procedimento remoto lança uma exceção, você ainda pode reutilizar a mesma instância do proxy. Cada ServiceProxy contém um cliente de comunicação usado para enviar mensagens durante a transmissão. Ao chamar as chamadas remotas, internamente verifica para ver se o cliente de comunicação é válido. Com base nos resultados das verificações, o cliente de comunicação é recriado se necessário. Portanto, se ocorrer uma exceção, você não precisa recriar `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>Tempo de vida de ServiceProxyFactory
[FabricServiceProxyFactory](/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) é um alocador que cria um proxy para interfaces remotas diferentes. Se você usar a API `ServiceProxyBase.create` para criar um proxy, o framework cria um `FabricServiceProxyFactory`.
É útil para criá-lo manualmente quando você precisa substituir propriedades [ServiceRemotingClientFactory](/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory).
Alocador é uma operação cara. `FabricServiceProxyFactory` mantém o cache dos clientes de comunicação.
A prática recomendada é armazenar em cache `FabricServiceProxyFactory` por mais tempo possível.

## <a name="remoting-exception-handling"></a>Tratamento de exceções remotas
Todas as exceções remotas lançadas pela API de serviço são reenviadas para o cliente como RuntimeException ou FabricException.

ServiceProxy trata todas as exceções de failover da partição de serviço para a qual foi criado. Ele resolverá novamente os pontos de extremidade se houver exceções de failover (exceções não transitórias) e repetirá a chamada com o ponto de extremidade correto. O número de tentativas da exceção de failover é indefinido.
No caso de TransientExceptions, ele repete apenas a chamada.

Os parâmetros de repetição padrão são fornecidos por [OperationRetrySettings](/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Você pode configurar esses valores, passando o objeto OperationRetrySettings para o construtor ServiceProxyFactory.

## <a name="next-steps"></a>Próximas etapas
* [Securing communication for Reliable Services](service-fabric-reliable-services-secure-communication-java.md)
