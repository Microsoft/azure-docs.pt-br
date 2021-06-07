---
title: Implementar recursos no Azure Service Fabric atores
description: Descreve como gravar seu próprio serviço de ator que implementa recursos de nível de serviço da mesma forma como você faria ao herdar StatefulService.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: d39ec93e0ad03d6c860bae9d0790e860c95457a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575560"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implementar recursos de nível de serviço em seu serviço de ator

Conforme descrito em [camadas de serviço](service-fabric-reliable-actors-platform.md#service-layering), o próprio serviço de ator é um serviço confiável. Você pode gravar seu próprio serviço que deriva de `ActorService`. Você também pode implementar recursos de nível de serviço da mesma forma que faria quando você herda um serviço com estado, como:

- Backup e restauração do serviço.
- Funcionalidade compartilhada para todos os atores, por exemplo, um disjuntor.
- Chamadas de procedimento remotas no próprio serviço de ator, bem como em cada ator individual.

## <a name="use-the-actor-service"></a>Usar o serviço de ator

As instâncias de ator têm acesso ao serviço de ator no qual estão sendo executadas. Por meio do serviço de ator, as instâncias de ator podem obter programaticamente o contexto de serviço. O contexto de serviço tem a ID da partição, o nome do serviço, o nome do aplicativo e outras informações específicas da plataforma do Microsoft Azure Service Fabric.

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

Como todos os Reliable Services, o serviço de ator deve ser registrado com um tipo de serviço no runtime do Service Fabric. Para que o serviço de ator seja executado em suas instâncias de ator, o tipo de ator também deve ser registrado no serviço de ator. O método de registro `ActorRuntime` realiza esse trabalho para os atores. No caso mais simples, é possível registrar o tipo de ator, e o serviço de ator usará as configurações padrão.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

Como alternativa, você pode usar um lambda fornecido pelo método de registro para que você possa construir o serviço de ator por conta própria. Em seguida, você pode configurar o serviço de ator e construir explicitamente as instâncias de ator. É possível injetar dependências no ator por meio de seu construtor.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>Métodos do serviço de ator

O Serviço de ator implementa `IActorService` (C#) ou `ActorService` (Java), que, por sua vez, implementa `IService` (C#) ou `Service` (Java). Essa interface é usada pela comunicação remota dos Reliable Services, que possibilita chamadas de procedimento remoto em métodos de serviço. Ela contém métodos no nível de serviço que podem ser chamados remotamente por meio da comunicação remota do serviço. Você pode usá-los para [enumerar](service-fabric-reliable-actors-enumerate.md) e [excluir](service-fabric-reliable-actors-delete-actors.md) atores.


## <a name="custom-actor-service"></a>Serviço de ator personalizado

Usando o lambda de registro do ator, você pode registrar seu próprio serviço de ator personalizado que deriva de `ActorService` (C#) e `FabricActorService` (Java). Você pode, então, implementar sua própria funcionalidade de nível de serviço, escrevendo uma classe de serviço que herda `ActorService` (C#) ou `FabricActorService` (Java). Um serviço de ator personalizado herda toda a funcionalidade de runtime do ator de `ActorService` (C#) ou `FabricActorService` (Java). Ele pode ser usado para implementar seus próprios métodos de serviço.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implement-actor-backup-and-restore"></a>Implementar o backup e a restauração de ator

Um serviço de ator personalizado pode expor um método para fazer backup dos dados do ator aproveitando o ouvinte de comunicação remota já presente em `ActorService`. Para obter um exemplo, consulte [Atores de backup e restauração](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Ator que usa uma pilha de comunicação remota V2 (compatível com a interface)

A pilha de comunicação remota V2 (compatível com a interface, conhecida como V2_1) tem todos os recursos da pilha de comunicação remota V2. Sua interface é compatível com a pilha de comunicação remota V1, mas não é compatível com versões anteriores da V1 e da V2. Para atualizar da V1 para a V2_1 sem afetar a disponibilidade do serviço, siga as etapas na próxima seção.

As seguintes alterações são necessárias para usar a pilha V2_1 de comunicação remota:

1. Adicione o seguinte atributo de assembly nas interfaces de ator.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Compile e atualize os projetos de cliente e de serviço do ator para começar a usar a pilha V2.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Atualize o serviço de ator para a pilha de comunicação remota V2 (compatível com a interface) sem afetar a disponibilidade do serviço

Essa alteração é uma atualização em duas etapas. Siga as etapas deste tutorial.

1. Adicione o seguinte atributo de assembly nas interfaces de ator. Esse atributo inicia dois ouvintes para o serviço de ator, os ouvintes V1 (existente) e V2_1. Atualize o serviço de ator com essa alteração.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Atualize os clientes de ator depois de concluir a atualização anterior.
   Esta etapa garante que o proxy do ator use a pilha de comunicação remota V2_1.

3. Esta etapa é opcional. Altere o atributo anterior para remover o ouvinte V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Ator que usa a pilha de comunicação remota V2

Com o pacote NuGet versão 2.8, os usuários agora podem usar a pilha de comunicação remota V2, que é mais eficaz e fornece recursos como serialização personalizada. A V2 de comunicação remota não é compatível com a pilha de comunicação remota existente (agora chamada de pilha de comunicação remota V1).

As seguintes alterações são necessárias para usar a pilha de comunicação remota V2.

1. Adicione o seguinte atributo de assembly nas interfaces de ator.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Compile e atualize os projetos de cliente e de serviço do ator para começar a usar a pilha V2.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Atualize o serviço de ator para a pilha de comunicação remota V2 sem afetar a disponibilidade do serviço

Essa alteração é uma atualização em duas etapas. Siga as etapas deste tutorial.

1. Adicione o seguinte atributo de assembly nas interfaces de ator. Esse atributo inicia dois ouvintes para o serviço de ator, os ouvintes V1 (existente) e V2. Atualize o serviço de ator com essa alteração.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Atualize os clientes de ator depois de concluir a atualização anterior.
   Esta etapa garante que o proxy do ator use a pilha de comunicação remota V2.

3. Esta etapa é opcional. Altere o atributo anterior para remover o ouvinte V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Próximas etapas

* [Gerenciamento de estado do ator](service-fabric-reliable-actors-state-management.md)
* [Ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API dos Atores](/previous-versions/azure/dn971626(v=azure.100))
* [Código de exemplo do .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
