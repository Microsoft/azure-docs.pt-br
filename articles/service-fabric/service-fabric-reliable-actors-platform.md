---
title: Reliable Actors no Service Fabric | Microsoft Docs
description: "Descreve como os Reliable Actors são dispostos em camadas nos Reliable Services e como eles usam os recursos da plataforma do Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 43b3f758fe7017c0ec949ba6e28b76438cf1bc13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Como Reliable Actors usam a plataforma do Service Fabric
Este artigo explica sobre o funcionamento dos Reliable Actors na plataforma do Azure Service Fabric. Os Reliable Actors são executados em uma estrutura hospedada em uma implementação de um serviço confiável com estado chamado *serviço de ator*. O serviço de ator contém todos os componentes necessários para gerenciar o ciclo de vida e a expedição de mensagens para seus atores:

* O Tempo de Execução de Ator gerencia o ciclo de vida, a coleta de lixo e impõe o acesso single-threaded.
* Um ouvinte de comunicação remota do serviço de ator aceita chamadas de acesso remoto aos atores e as envia a um dispatcher para encaminhamento à instância de ator apropriada.
* O Provedor de Estado de Ator encapsula provedores de estado (como o provedor de estado das Coleções Confiáveis) e fornece um adaptador para o gerenciamento de estado de ator.

Juntos, esses componentes formam a estrutura do Reliable Actor.

## <a name="service-layering"></a>Disposição em camadas de serviço
Como o próprio serviço de ator é um serviço confiável, todos os conceitos de [modelo de aplicativo](service-fabric-application-model.md), ciclo de vida, [empacotamento](service-fabric-package-apps.md), [implantação](service-fabric-deploy-remove-applications.md), atualização e dimensionamento dos Reliable Services aplicam-se da mesma maneira aos serviços de ator.

![Disposição em camadas do serviço de ator][1]

O diagrama anterior mostra a relação entre as estruturas do aplicativo do Service Fabric e o código do usuário. Os elementos em azul representam a estrutura do aplicativo dos Reliable Services, os elementos em laranja representam a estrutura do Reliable Actor e os elementos em verde representam o código do usuário.

Nos Reliable Services, o serviço herda a classe `StatefulService`. Essa classe é derivada de `StatefulServiceBase` (ou `StatelessService` para serviços sem estado). Nos Reliable Actors, você usa o serviço de ator. O serviço de ator é uma implementação diferente da classe `StatefulServiceBase` que implementa o padrão de ator no qual seus atores são executados. Como o próprio serviço de ator é apenas uma implementação de `StatefulServiceBase`, é possível escrever seu próprio serviço que deriva de `ActorService` e implementar recursos no nível de serviço da mesma forma que você faria ao herdar `StatefulService`, tais como:

* Backup e restauração do serviço.
* Funcionalidade compartilhada para todos os atores, por exemplo, um disjuntor.
* Chamadas de procedimento remotas no próprio serviço de ator, bem como em cada ator individual.

> [!NOTE]
> Atualmente não há suporte para os serviços com estado no Java/Linux.

### <a name="using-the-actor-service"></a>Usando o serviço de ator
As instâncias de ator têm acesso ao serviço de ator no qual estão sendo executadas. Por meio do serviço de ator, as instâncias de ator podem obter programaticamente o contexto de serviço. O contexto de serviço tem a ID da partição, o nome do serviço, o nome do aplicativo e outras informações específicas da plataforma do Service Fabric:

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


Como todos os Reliable Services, o serviço de ator deve ser registrado com um tipo de serviço no tempo de execução do Service Fabric. Para que o serviço de ator seja executado em suas instâncias de ator, o tipo de ator também deve ser registrado no serviço de ator. O método de registro `ActorRuntime` realiza esse trabalho para os atores. No caso mais simples, é possível apenas registrar o tipo de ator, e o serviço de ator, com as configurações padrão, será usado implicitamente:

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

Como alternativa, você pode usar um lambda fornecido pelo método de registro para que você possa construir o serviço de ator por conta própria. Assim, você pode configurar o serviço de ator e construir explicitamente as instâncias de ator, nas quais é possível injetar dependências no ator por meio de seu construtor:

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

### <a name="actor-service-methods"></a>Métodos do serviço de ator
O Serviço de ator implementa `IActorService` (C#) ou `ActorService` (Java), que, por sua vez, implementa `IService` (C#) ou `Service` (Java). Essa é a interface usada pela comunicação remota dos Reliable Services, que possibilita chamadas de procedimento remoto em métodos de serviço. Ela contém métodos no nível de serviço que podem ser chamados remotamente por meio da comunicação remota do serviço.

#### <a name="enumerating-actors"></a>Enumerando os atores
O serviço de ator permite que um cliente enumere metadados sobre os atores que estão sendo hospedados pelo serviço. Como o serviço de ator é um serviço com estado particionado, a enumeração é realizada por partição. Como cada partição pode conter vários atores, a enumeração é retornada como um conjunto de resultados paginados. É executado um loop das páginas até que todas as páginas sejam lidas. O seguinte exemplo mostra como criar uma lista de todos os atores ativos em uma partição de um serviço de ator:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```

#### <a name="deleting-actors"></a>Excluindo atores
O serviço de ator também fornece uma função para a exclusão de atores:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Para obter mais informações sobre como excluir atores e seu estado, consulte a [documentação de ciclo de vida do ator](service-fabric-reliable-actors-lifecycle.md).

### <a name="custom-actor-service"></a>Serviço de ator personalizado
Usando o lambda de registro do ator, você pode registrar seu próprio serviço de ator personalizado que deriva de `ActorService` (C#) e `FabricActorService` (Java). Neste serviço de ator personalizado, você pode implementar sua própria funcionalidade de nível de serviço, escrevendo uma classe de serviço que herda `ActorService` (C#) ou `FabricActorService` (Java). Um serviço de ator personalizado herda toda a funcionalidade de tempo de execução do ator de `ActorService` (C#) ou `FabricActorService` (Java) e pode ser usado para implementar seus próprios métodos de serviço.

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

#### <a name="implementing-actor-backup-and-restore"></a>Implementando o backup e a restauração de ator
 No seguinte exemplo, o serviço de ator personalizado expõe um método para fazer backup dos dados do ator, aproveitando o ouvinte de comunicação remota já presente em `ActorService`:

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```


Neste exemplo, o `IMyActorService` é um contrato de comunicação remota que implementa `IService` (C#) e `Service` (Java) e que, em seguida, é implementado por `MyActorService`. Ao adicionar esse contrato de comunicação remota, os métodos em `IMyActorService` agora também ficam disponíveis para um cliente, por meio da criação de um proxy de comunicação remota através do `ActorServiceProxy`:

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

## <a name="application-model"></a>Modelo de aplicativo
Os serviços de ator são Reliable Services; portanto, o modelo do aplicativo é o mesmo. No entanto, as ferramentas de build da estrutura de ator geram alguns dos arquivos de modelo do aplicativo para você.

### <a name="service-manifest"></a>Manifesto do serviço
As ferramentas de build da estrutura de ator geram automaticamente o conteúdo do arquivo ServiceManifest.xml do serviço de ator. Este arquivo inclui:

* O tipo de serviço de ator. O nome do tipo é gerado de acordo com o nome de projeto do ator. Com base no atributo de persistência do ator, o sinalizador HasPersistedState também é definido de acordo.
* Pacote de códigos.
* Pacote de configuração.
* Recursos e pontos de extremidade.

### <a name="application-manifest"></a>Manifesto do aplicativo
As ferramentas de build da estrutura de ator criam automaticamente uma definição de serviço padrão para o serviço de ator. As ferramentas de build preenchem as propriedades padrão do serviço:

* A contagem de conjuntos de réplicas é determinada pelo atributo de persistência no ator. Sempre que o atributo de persistência no ator é alterado, a contagem de conjuntos de réplicas na definição de serviço padrão é redefinida de acordo.
* O esquema de partição e o intervalo são definidos como Int64 Uniforme com o intervalo de chaves Int64 completo.

## <a name="service-fabric-partition-concepts-for-actors"></a>Conceitos de partição de Malha do Serviço para atores
Serviços de ator são serviços com estado particionados. Cada partição de um serviço de ator contém um conjunto de atores. As partições de serviço são distribuídas automaticamente em vários nós no Service Fabric. As instâncias de ator são distribuídas como resultado disso.

![Particionamento e distribuição de ator][5]

Os Reliable Services podem ser criados com esquemas de partição diferentes e intervalos de chaves de partição. O serviço de ator usa o esquema de particionamento Int64 com o intervalo de chaves Int64 completo para mapear os atores para as partições.

### <a name="actor-id"></a>ID de ator
Cada ator criado no serviço tem uma ID exclusiva associada, representada pela classe `ActorId` . A `ActorId` é um valor de ID opaco que pode ser usado para a distribuição uniforme de atores nas partições de serviço por meio da geração de IDs aleatórias:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Cada `ActorId` é codificada em hash para um Int64. É por esse motivo que o serviço de ator deve usar um esquema de particionamento Int64 com o intervalo de chaves Int64 completo. No entanto, valores de ID personalizados podem ser usados para uma `ActorID`, incluindo GUIDs/UUIDs, cadeias de caracteres e Int64s.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

Ao usar GUIDs/UUIDs e cadeias de caracteres, os valores são codificados em hash para um Int64. No entanto, ao fornecer explicitamente um Int64 para uma `ActorId`, o Int64 será mapeado diretamente para uma partição sem hash adicional. Você pode usar essa técnica para controlar em qual partição os atores serão colocados.

## <a name="actor-using-remoting-v2-stack"></a>Ator utilizando Pilha V2 de Comunicação Remota
Com o pacote NuGet 2.8, os usuários agora podem usar a pilha V2 de Comunicação Remota que é mais eficaz e fornece recursos como Serialização personalizada. A V2 de Comunicação Remota não é compatível com a pilha de Comunicação Remota existente (agora denominada como Pilha de Comunicação Remota V1).

As seguintes alterações são necessárias para usar a Pilha V2 de Comunicação Remota.
 1. Adicione o seguinte atributo de assembly nas Interfaces de Ator.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. Compile e atualize os projetos ActorClient e ActorService para começar a usar a Pilha V2.

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>Atualize o ActorService para Pilha V2 de Comunicação Remota sem afetar a Disponibilidade do Serviço.
Essa alteração será uma atualização da etapa 2. Siga as etapas na mesma sequência, conforme listado.

1.  Adicione o seguinte atributo de assembly nas Interfaces de Ator. Este atributo iniciará dois ouvintes para ActorService, Ouvinte V1 (existente) e V2. Atualize ActorService com essa alteração.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. Atualize ActorClients após concluir a atualização anterior.
Esta etapa garante que o Proxy do Ator esteja usando a Pilha V2 de Comunicação Remota.

3. Esta etapa é opcional. Altere o atributo acima para remover o Ouvinte V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
    ```

## <a name="next-steps"></a>Próximas etapas
* [Gerenciamento de estado do ator](service-fabric-reliable-actors-state-management.md)
* [Ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API dos Atores](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de exemplo do .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo de Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
