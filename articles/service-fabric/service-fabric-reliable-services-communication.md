---
title: "Visão geral da comunicação dos Reliable Services | Microsoft Docs"
description: "Visão geral do modelo de comunicação dos Reliable Services, incluindo a abertura de ouvintes, a resolução de pontos de extremidade e a comunicação entre serviços."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: 36217988-420e-409d-b0a4-e0e875b6eac8
ms.service: service-fabric
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/07/2017
ms.author: vturecek
ms.openlocfilehash: b418904f50b772c12bfcdbb95beb9312c8b9fb00
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Como usar as APIs de comunicação dos Reliable Services
O Service Fabric do Azure como uma plataforma é totalmente independente quanto às comunicações entre serviços. Todos os protocolos e pilhas são aceitáveis, de UDP a HTTP. Cabe ao desenvolvedor determinar a forma de comunicação entre os serviços. A estrutura de aplicativo dos Reliable Services fornece algumas pilhas de comunicação internas, bem como APIs que você pode usar para criar componentes de comunicação personalizados.

## <a name="set-up-service-communication"></a>Configurar a comunicação de serviço
A API do Reliable Services usa uma interface simples para comunicação de serviço. Para abrir um ponto de extremidade para o serviço, basta implementar esta interface:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

```java
public interface CommunicationListener {
    CompletableFuture<String> openAsync(CancellationToken cancellationToken);

    CompletableFuture<?> closeAsync(CancellationToken cancellationToken);

    void abort();
}
```

Você pode adicionar a implementação do ouvinte de comunicação, retornando-a em uma substituição do método de classe com base no serviço.

Para serviços sem estado:

```csharp
class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```
```java
public class MyStatelessService extends StatelessService {

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ...
    }
    ...
}
```

Para serviços com estado:

> [!NOTE]
> Ainda não há suporte para Reliable Services com monitoração de estado em Java.
>
>

```csharp
class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

Em ambos os casos, você deve retornar uma coleção de ouvintes. Isso permite que o serviço ouça vários pontos de extremidade, potencialmente usando protocolos diferentes ao usar vários ouvintes. Por exemplo, você pode ter um ouvinte HTTP e um ouvinte WebSocket separado. Cada ouvinte recebe um nome e a coleção resultante dos pares *nome : endereço* é representada como um objeto JSON quando um cliente solicita os endereços de escuta para uma instância ou uma partição de serviço.

Em um serviço sem estado, a substituição retorna uma coleção de ServiceInstanceListeners. Um `ServiceInstanceListener` contém uma função para criar um `ICommunicationListener(C#) / CommunicationListener(Java)` e concede a ele um nome. Para os serviços com monitoração de estado, a substituição retorna uma coleção de ServiceReplicaListeners. Isso é um pouco diferente do equivalente sem estado, pois um `ServiceReplicaListener` tem uma opção para abrir um `ICommunicationListener` em réplicas secundárias. Você pode usar vários ouvintes de comunicação em um serviço, além de especificar aqueles que aceitem solicitações em réplicas secundárias e os que escutam apenas em réplicas primárias.

Por exemplo, você pode ter um ServiceRemotingListener que faz chamadas RPC apenas em réplicas primárias e um segundo ouvinte personalizado que faz solicitações de leitura em réplicas secundárias sobre HTTP:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [!NOTE]
> Ao se criar vários ouvintes para um serviço, cada ouvinte **deve** receber um nome exclusivo.
>
>

Por fim, você pode descrever os pontos de extremidade necessários para o serviço no [manifesto do serviço](service-fabric-application-model.md) , na seção sobre pontos de extremidade.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

O ouvinte de comunicação pode acessar os recursos do ponto de extremidade alocados para ele no `CodePackageActivationContext`, no `ServiceContext`. O ouvinte pode então começar a escutar solicitações quando é aberto.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Os recursos do ponto de extremidade são comuns a todo o pacote de serviço e são alocados pelo Service Fabric quando o pacote de serviço é ativado. Várias réplicas de serviço hospedadas no mesmo ServiceHost podem compartilhar a mesma porta. Isso significa que o ouvinte de comunicação deve oferecer suporte ao compartilhamento de porta. A maneira recomendada de fazer isso é que o ouvinte de comunicação use a ID da partição e a ID de réplica/instância ao gerar o endereço de escuta.
>
>

### <a name="service-address-registration"></a>Registro de endereço do serviço
Um serviço do sistema chamado de *Serviço de Nomenclatura* é executado em clusters do Service Fabric. O Serviço de Nomenclatura é um registrador para os serviços e seus endereços que cada instância ou réplica do serviço está escutando. Quando o método `OpenAsync(C#) / openAsync(Java)` de um `ICommunicationListener(C#) / CommunicationListener(Java)` é concluído, seu valor retornado é registrado no Serviço de Nomenclatura. Esse valor retornado que é publicado no Serviço de Nomenclatura é uma cadeia de caracteres cujo valor pode ser absolutamente qualquer coisa. Esse valor de cadeia de caracteres é o que os clientes verão quando perguntarem o endereço do Serviço de Nomenclatura.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```
```java
public CompletableFuture<String> openAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.getCodePackageActivationContext.getEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.getPort();

    this.publishAddress = String.format("http://%s:%d/", FabricRuntime.getNodeContext().getIpAddressOrFQDN(), port);

    this.webApp = new WebApp(port);
    this.webApp.start();

    /* the string returned here will be published in the Naming Service.
     */
    return CompletableFuture.completedFuture(this.publishAddress);
}
```

O Service Fabric fornece uma API que permite aos clientes e outros serviços perguntarem esse endereço pelo nome do serviço. Isso é importante porque o endereço do serviço não é estático. Os serviços são movimentados no cluster para fins de disponibilidade e balanceamento de recursos. Esse é o mecanismo que permite aos clientes resolver o endereço de escuta de um serviço.

> [!NOTE]
> Para obter uma explicação completa de como escrever um ouvinte de comunicação, consulte [Serviços de API Web do Service Fabric com auto-hospedagem OWIN](service-fabric-reliable-services-communication-webapi.md) para C#, enquanto para Java você pode escrever sua própria implementação do servidor HTTP; consulte o exemplo de aplicativo EchoServer em https://github.com/Azure-Samples/service-fabric-java-getting-started.
>
>

## <a name="communicating-with-a-service"></a>Comunicando-se com um serviço
A API dos Reliable Services fornece as bibliotecas a seguir para escrever clientes que se comunicam com serviços.

### <a name="service-endpoint-resolution"></a>Resolução de ponto de extremidade de serviço
A primeira etapa para a comunicação com um serviço é resolver um endereço do ponto de extremidade da partição ou instância do serviço com o qual você deseja falar. A classe de utilitário `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` é um primitivo básico que ajuda os clientes a determinar o ponto de extremidade de um serviço no tempo de execução. Na terminologia do Service Fabric, o processo de determinar o ponto de extremidade de um serviço é conhecido como *resolução do ponto de extremidade de serviço*.

Para se conectar aos serviços em um cluster, ServicePartitionResolver pode ser criado usando as configurações padrão. Este é o uso recomendado na maioria das situações:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Para se conectar aos serviços em um cluster diferente, um ServicePartitionResolver pode ser criado com um conjunto de pontos de extremidade de gateway do cluster. Observe que os pontos de extremidade de gateway são apenas pontos de extremidade diferentes para se conectar ao mesmo cluster. Por exemplo:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Como alternativa, um `ServicePartitionResolver` pode receber uma função para criar um `FabricClient` para uso interno:

```csharp
public delegate FabricClient CreateFabricClientDelegate();
```
```java
public FabricServicePartitionResolver(CreateFabricClient createFabricClient) {
...
}

public interface CreateFabricClient {
    public FabricClient getFabricClient();
}
```

`FabricClient` é o objeto usado para se comunicar com o cluster do Service Fabric em várias operações de gerenciamento no cluster. Isso é útil quando você quiser mais controle sobre como um resolvedor de partição de serviço interage com o cluster. O `FabricClient` realiza armazenamento em cache internamente e é geralmente caro de criar, portanto, é importante reutilizar instâncias do `FabricClient` tanto quanto possível.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Um método de resolução é então usado para recuperar o endereço de um serviço ou uma partição de serviço para serviços particionados.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();

CompletableFuture<ResolvedServicePartition> partition =
    resolver.resolveAsync(new URI("fabric:/MyApp/MyService"), new ServicePartitionKey());
```

Um endereço de serviço pode ser resolvido facilmente usando um ServicePartitionResolver, porém é necessário mais trabalho para garantir que o endereço resolvido possa ser usado corretamente. O cliente precisa detectar se a tentativa de conexão falhou devido a um erro transitório e pode ser repetida (por exemplo, o serviço foi movido ou está temporariamente indisponível) ou a um erro permanente (por exemplo, o serviço foi excluído ou o recurso solicitado não existe mais). Réplicas ou instâncias de serviço podem se mover de nó para nó a qualquer momento por várias razões. O endereço de serviço resolvido por meio de ServicePartitionResolver pode estar obsoleto no momento em seu código de cliente tentar se conectar. Nesse caso, o cliente precisa resolver o endereço novamente. Fornecer o `ResolvedServicePartition` anterior indica que o resolvedor deve tentar novamente em vez de simplesmente recuperar um endereço armazenado em cache.

Normalmente o código do cliente não precisa trabalhar diretamente com o ServicePartitionResolver. Ele é criado e passado fábricas clientes de comunicação na API dos Reliable Services. As fábricas usam o resolvedor internamente para gerar um objeto cliente que pode ser usado para se comunicar com os serviços.

### <a name="communication-clients-and-factories"></a>Fábricas e clientes de comunicação
A biblioteca de fábrica de comunicação implementa um padrão típico de repetição de manipulação de falhas que facilita a repetição de novas tentativas de conexão para pontos de extremidade de serviço resolvidos. A biblioteca de fábrica fornece o mecanismo de repetição enquanto você fornece os manipuladores de erro.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` define a interface base implementada por uma fábrica de cliente de comunicação que produz clientes que podem se comunicar com um serviço do Service Fabric. A implementação de CommunicationClientFactory dependerá da pilha de comunicação usada pelo serviço do Service Fabric em que o cliente quer se comunicar. A API dos Reliable Services fornece um `CommunicationClientFactoryBase<TCommunicationClient>`. Ele fornece uma implementação básica da interface do CommunicationClientFactory e executa as tarefas comuns a todas as pilhas de comunicação. (Essas tarefas incluem o uso de um ServicePartitionResolver para determinar o ponto de extremidade de serviço). Os clientes geralmente implementam a classe abstrata CommunicationClientFactoryBase para tratar da lógica específica da pilha de comunicação.

O cliente de comunicação apenas recebe um endereço e o utiliza para se conectar a um serviço. O cliente pode usar qualquer protocolo que desejar.

```csharp
class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```
```java
public class MyCommunicationClient implements CommunicationClient {

    private ResolvedServicePartition resolvedServicePartition;
    private String listenerName;
    private ResolvedServiceEndpoint endPoint;

    /*
     * Getters and Setters
     */
}
```

A fábrica do cliente é responsável principalmente pela criação de clientes de comunicação. Para clientes que não mantém uma conexão persistente, tal como um cliente HTTP, a fábrica só precisa criar e retornar o cliente. Outros protocolos que mantêm uma conexão persistente, tais como alguns protocolos binários, também devem ser validados pela fábrica para determinar se a conexão precisa ou não ser criada novamente.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```
```java
public class MyCommunicationClientFactory extends CommunicationClientFactoryBase<MyCommunicationClient> {

    @Override
    protected boolean validateClient(MyCommunicationClient clientChannel) {
    }

    @Override
    protected boolean validateClient(String endpoint, MyCommunicationClient client) {
    }

    @Override
    protected CompletableFuture<MyCommunicationClient> createClientAsync(String endpoint) {
    }

    @Override
    protected void abortClient(MyCommunicationClient client) {
    }
}
```

Por fim, um manipulador de exceção é responsável por determinar a ação a ser executada quando uma exceção ocorre. Exceções são categorizadas como **repetíveis** e **não repetíveis**.

* Exceções **não repetíveis** simplesmente são retornadas para o chamador.
* Exceções **repetíveis** são categorizadas novamente em **transitórias** e **não transitórias**.
  * **transitórias** são aquelas que podem ser recuperadas simplesmente sem resolver novamente o endereço do ponto de extremidade de serviço. Elas incluem problemas de rede transitórios ou respostas de erros de serviço diferentes daqueles que indicam que o endereço do ponto de extremidade de serviço não existe.
  * **Non-transient** são aquelas que exigem que o endereço do ponto de extremidade de serviço seja resolvido novamente. Elas incluem exceções que indicam que não foi possível alcançar o ponto de extremidade de serviço, indicando que o serviço foi movido para outro nó.

O `TryHandleException` toma uma decisão sobre uma determinada exceção. Se ele **não souber** quais decisões devem ser tomadas sobre uma exceção, ele deverá retornar **false**. Se ele **souber** qual decisão tomar, deverá definir o resultado corretamente e retornar **true**.

```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
```java
public class MyExceptionHandler implements ExceptionHandler {

    @Override
    public ExceptionHandlingResult handleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings) {        

        /* if exceptionInformation.getException() is known and is transient (can be retried without re-resolving)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), true, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;


        /* if exceptionInformation.getException() is known and is not transient (indicates a new service endpoint address must be resolved)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), false, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;

        /* if exceptionInformation.getException() is unknown (let the next ExceptionHandler attempt to handle it)
         */
        result = null;
        return false;

    }
}
```
### <a name="putting-it-all-together"></a>Juntando as peças
Com um `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` e `IExceptionHandler(C#) / ExceptionHandler(Java)` criados em torno de um protocolo de comunicação, um `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` reúne tudo isso e fornece o loop de manipulação de falhas e de resolução de endereço da partição de serviço em torno desses componentes.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```
```java
private MyCommunicationClientFactory myCommunicationClientFactory;
private URI myServiceUri;

FabricServicePartitionClient myServicePartitionClient = new FabricServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

CompletableFuture<?> result = myServicePartitionClient.invokeWithRetryAsync(client -> {
      /* Communicate with the service using the client.
       */
   });

```

## <a name="next-steps"></a>Próximas etapas
* Confira um exemplo de comunicação HTTP entre serviços em um [projeto de exemplo C# no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/WordCount) ou [Projeto de exemplo Java no GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/Services/WatchDog).
* [Comunicação remota de serviço com os Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [API Web que usa o OWIN nos Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicação WCF usando os Reliable Services](service-fabric-reliable-services-communication-wcf.md)
