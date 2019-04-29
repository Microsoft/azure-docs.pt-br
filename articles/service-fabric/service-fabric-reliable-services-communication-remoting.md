---
title: Comunicação remota do serviço usando C# no Service Fabric | Microsoft Docs
description: A comunicação remota do Service Fabric permite que os clientes e serviços se comuniquem com serviços C# que usam a chamada de procedimento remoto.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: f9cd6e2fee738d2d42c790b4eb7b9a876a44b01d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772968"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Comunicação remota do serviço em C# com Reliable Services

> [!div class="op_single_selector"]
> * [C# em Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java no Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Para serviços que não estão vinculados a um protocolo de comunicação específico ou pilha, como API Web, Windows Communication Foundation ou outros, a estrutura do Reliable Services fornece um mecanismo de comunicação remota para configurar a chamada de procedimento remoto para serviços de forma rápida e fácil. Este artigo descreve como configurar chamadas de procedimento remoto para serviços criados com C#.

## <a name="set-up-remoting-on-a-service"></a>Configurar a comunicação remota em um serviço

Você pode configurar a comunicação remota para um serviço em duas etapas simples:

1. Crie uma interface para implementar o serviço. Essa interface define os métodos disponíveis para uma chamada de procedimento remoto no seu serviço. Os métodos devem ser métodos assíncronos que retornam tarefas. A interface deve implementar `Microsoft.ServiceFabric.Services.Remoting.IService` para sinalizar que o serviço tem uma interface de comunicação remota.
2. Use um ouvinte de comunicação remota em seu serviço. RemotingListener é uma implementação `ICommunicationListener` que fornece funcionalidades de comunicação remota. O namespace `Microsoft.ServiceFabric.Services.Remoting.Runtime` contém um método de extensão `CreateServiceRemotingListener` para serviços com e sem estado que pode ser usado para criar um ouvinte de comunicação remota usando o protocolo de transporte remoto padrão.

>[!NOTE]
>O `Remoting` namespace está disponível como um pacote NuGet separado chamado `Microsoft.ServiceFabric.Services.Remoting`.

Por exemplo, o serviço sem estado a seguir expõe um único método para obter "Hello World" pela chamada de procedimento remoto.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```

> [!NOTE]
> Os argumentos e os tipos de retorno na interface de serviço podem ser tipos simples, complexos ou personalizados, mas devem poder ser serializados pelo [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) do .NET.
>
>

## <a name="call-remote-service-methods"></a>Chamar métodos de serviços remotos

A chamada de métodos em um serviço usando a pilha de comunicação remota é feita usando um proxy local para o serviço por meio da classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` . O método `ServiceProxy` cria um proxy local usando a mesma interface que o serviço implementa. Com esse proxy, você pode chamar métodos na interface remotamente.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

A estrutura remota propaga exceções geradas pelo serviço para o cliente. Como resultado, ao usar `ServiceProxy`, o cliente é responsável pelo tratamento das exceções geradas pelo serviço.

## <a name="service-proxy-lifetime"></a>Tempo de vida do proxy do serviço

Criar o proxy do serviço é uma operação simples, de modo que os usuários podem criar tantos quantos forem necessários. As instâncias do proxy do serviço poderão ser reutilizadas enquanto os usuários precisarem. Se uma chamada de procedimento remoto gerar uma exceção, você ainda poderá reutilizar a mesma instância do proxy. Cada proxy de serviço contém um cliente de comunicação usado para enviar mensagens durante a transmissão. Ao chamar as chamadas remotas, internamente verifica para ver se o cliente de comunicação é válido. Com base nos resultados das verificações, o cliente de comunicação será recriado se necessário. Portanto, se ocorrer uma exceção, você não precisará recriar `ServiceProxy`.

### <a name="service-proxy-factory-lifetime"></a>Tempo de vida do alocador de proxy do serviço

[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) é um alocador que cria instâncias de proxy para interfaces remotas diferentes. Se você usar a API `ServiceProxyFactory.CreateServiceProxy` para criar um proxy, a estrutura criará um proxy do serviço de singleton.
É útil para criá-lo manualmente quando você precisa substituir propriedades [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory).
A criação do alocador é uma operação cara. Um alocador de proxy do serviço mantém um cache interno do cliente de comunicação.
Uma prática recomendada é armazenar em cache a fábrica de proxy de serviço pelo maior tempo possível.

## <a name="remoting-exception-handling"></a>Tratamento de exceções remotas

Todas as exceções remotas geradas pela API de serviço são reenviadas ao cliente como AggregateException. Exceções remotas devem ser poder ser serializadas por DataContract. Se não puderem, a API do proxy gerará [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) com o erro de serialização.

O proxy de serviço trata todas as exceções de failover para a partição de serviço para a qual ele foi criado. Ele resolverá novamente os pontos de extremidade em casos de exceções de failover (exceções não transitórias) e repetirá a chamada com o ponto de extremidade correto. O número de tentativas da exceção de failover é indefinido.
Se exceções transitórias ocorrerem, o proxy tentará novamente a chamada.

Os parâmetros de nova tentativa padrão são fornecidos por [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Um usuário pode configurar esses valores passando o objeto OperationRetrySettings para o construtor ServiceProxyFactory.

## <a name="use-the-remoting-v2-stack"></a>Usar a pilha de comunicação remota V2

Da versão 2.8 do pacote de comunicação remota do NuGet em diante, você tem a opção de usar a pilha de comunicação remota V2. A pilha de comunicação remota V2 tem um melhor desempenho. Ela também fornece recursos como serialização personalizada e mais APIs conectáveis.
O código de modelo continua a usar a pilha de comunicação remota V1.
A comunicação remota V2 não é compatível com a V1 (a pilha de comunicação remota anterior). Siga as instruções no artigo [Atualizar da V1 para a V2](#upgrade-from-remoting-v1-to-remoting-v2) para evitar efeitos sobre a disponibilidade do serviço.

As abordagens a seguir estão disponíveis para habilitar a pilha V2.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Usar um atributo de assembly para usar a pilha V2

Estas etapas alteram o modelo de código para usar a pilha V2 usando um atributo de assembly.

1. Altere o recurso do ponto de extremidade `"ServiceEndpoint"` para `"ServiceEndpointV2"` no manifesto de serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Use o método `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` de extensão para criar os ouvintes remotos (igual para V1 e V2).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Marque o assembly que contém as interfaces de comunicação remota com um atributo `FabricTransportServiceRemotingProvider`.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

Nenhuma alteração de código é necessária no projeto do cliente.
Compile o assembly de cliente com o assembly de interface para garantir que o atributo do assembly mostrado anteriormente seja usado.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Usar as classes V2 explícitas para usar a pilha V2

Como alternativa a usar um atributo de assembly, a pilha V2 também pode ser habilitada usando as classes V2 explícitas.

Estas etapas alteram o modelo de código para usar a pilha V2 usando classes V2 explícitas.

1. Altere o recurso do ponto de extremidade `"ServiceEndpoint"` para `"ServiceEndpointV2"` no manifesto de serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Use o [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) do namespace `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime`.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
   ```

3. Use o [FabricTransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) do namespace `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` para criar clientes.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Atualizar da comunica remota V1 para a comunicação remota V2

Para atualizar da V1 para a V2, atualizações em duas etapas são necessárias. Siga as etapas deste tutorial.

1. Atualize o serviço V1 para o serviço V2 usando este atributo.
Essa alteração garante que o serviço escute nos ouvintes V1 e V2.

     a. Adicione um recurso de ponto de extremidade com o nome "ServiceEndpointV2" no manifesto do serviço.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Use o método de extensão a seguir para criar um ouvinte de comunicação remota.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Adicione o atributo de assembly em interfaces de comunicação remota para usar os ouvintes V1 e V2 e o cliente V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Atualize o cliente V1 para um cliente V2 usando o atributo do cliente V2.
Essa etapa garante que o cliente use a pilha V2.
Nenhuma alteração no projeto/serviço do cliente é necessária. Compilar projetos de cliente com assembly de interface atualizada é suficiente.

3. Esta etapa é opcional. Use o atributo V2 e, em seguida, atualize o serviço V2.
Esta etapa garante que o serviço esteja escutando somente no ouvinte V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Use a pilha de comunicação remota V2 (compatível com a interface)

 A pilha de comunicação remota V2 (compatível com a interface, conhecida como V2_1) tem todos os recursos da pilha de comunicação remota V2. Sua pilha de interface é compatível com a pilha de comunicação remota V1, mas não é compatível com versões anteriores da V1 e da V2. Para atualizar da V1 para a V2_1 sem afetar a disponibilidade do serviço, siga as etapas no artigo Atualizar da V1 para a V2 (compatível com interface).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Use um atributo de assembly para usar a pilha de comunicação remota V2 (compatível com interface)

Siga estas etapas para alterar para uma pilha V2_1.

1. Adicione um recurso de ponto de extremidade com o nome "ServiceEndpointV2_1" no manifesto do serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Use o método de extensão de comunicação remota para criar um ouvinte de comunicação remota.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Adicione um [atributo de assembly](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) nas interfaces de comunicação remota.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Nenhuma alteração é necessária no projeto do cliente.
Compile o assembly de cliente com o assembly de interface para garantir que o atributo do assembly anterior esteja sendo usado.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Use classes de comunicação remota explícitas para criar uma fábrica de ouvinte/cliente para a versão V2 (compatível com interface)

Siga estas etapas:

1. Adicione um recurso de ponto de extremidade com o nome "ServiceEndpointV2_1" no manifesto do serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Use o [ouvinte de comunicação remota V2](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). O nome do recurso de ponto de extremidade de serviço padrão usado é "ServiceEndpointV2_1". Ele deve ser definido no manifesto do serviço.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
   ```

3. Use o [alocador de cliente](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) V2.
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Atualizar da comunicação remota V1 para a comunicação remota V2 (compatível com interface)

Para atualizar da V1 para a V2 (compatível com interface, conhecida como V2_1), são necessárias atualizações de duas etapas. Siga as etapas deste tutorial.

1. Atualize do serviço V1 para o serviço V2_1 usando o atributo a seguir.
Essa alteração garante que o serviço esteja escutando nos ouvintes V1 e V2_1.

     a. Adicione um recurso de ponto de extremidade com o nome "ServiceEndpointV2_1" no manifesto do serviço.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Use o método de extensão a seguir para criar um ouvinte de comunicação remota.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Adicione o atributo de assembly em interfaces de comunicação remota para usar os ouvintes V1, V2_1 e o cliente V2_1.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Atualize o cliente V1 para o cliente V2_1 usando o atributo do cliente V2_1.
Esta etapa garante que o cliente esteja usando a pilha V2_1.
Nenhuma alteração no projeto/serviço do cliente é necessária. Compilar projetos de cliente com assembly de interface atualizada é suficiente.

3. Esta etapa é opcional. Remova a versão do ouvinte V1 do atributo e, em seguida, atualize o serviço V2.
Esta etapa garante que o serviço esteja escutando somente no ouvinte V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Usar a serialização personalizada com mensagem encapsulada de comunicação remota

Para uma mensagem encapsulada de comunicação remota, criamos um único objeto encapsulado com todos os parâmetros como um campo.
Siga estas etapas:

1. Implemente a interface `IServiceRemotingMessageSerializationProvider` para fornecer a implementação para serialização personalizada.
    Esse snippet de código mostra a aparência da implementação.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2. Substitua o provedor de serialização padrão por `JsonSerializationProvider` para um ouvinte de comunicação remota.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
   ```

3. Substitua o provedor de serialização padrão por `JsonSerializationProvider` para um alocador de cliente de comunicação remota.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Próximas etapas

* [API Web com OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicação do Windows Communication Foundation com Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Como proteger a comunicação para Reliable Services](service-fabric-reliable-services-secure-communication.md)
