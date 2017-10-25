---
title: "Comunicação remota do serviço no Service Fabric | Microsoft Docs"
description: "A comunicação remota do Service Fabric permite que os clientes e serviços se comuniquem com serviços que usam a chamada de procedimento remoto."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 655bc3dd3735a35fbe7437e8dda92b2adf15f7bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-remoting-with-reliable-services"></a>Comunicação Remota do Serviço com Reliable Services
Para serviços que não estão vinculados a um protocolo de comunicação específico ou pilha, como WebAPI, WCF (Windows Communication Foundation) ou outros, a estrutura do Reliable Services fornece um mecanismo de comunicação remota para configurar a chamada de procedimento remoto para serviços de forma rápida e fácil.

## <a name="set-up-remoting-on-a-service"></a>Configurar a Comunicação Remota em um Serviço
A configuração da comunicação remota de um serviço é feita em duas etapas simples:

1. Crie uma interface para implementar o serviço. Essa interface define os métodos disponíveis para uma chamada de procedimento remoto no seu serviço. Os métodos devem ser métodos assíncronos que retornam tarefas. A interface deve implementar `Microsoft.ServiceFabric.Services.Remoting.IService` para sinalizar que o serviço tem uma interface de comunicação remota.
2. Use um ouvinte de comunicação remota em seu serviço. RemotingListener é uma implementação `ICommunicationListener` que fornece funcionalidades de comunicação remota. O namespace `Microsoft.ServiceFabric.Services.Remoting.Runtime` contém um método de extensão, `CreateServiceRemotingListener`, para serviços com e sem estado que podem ser usados para criar um ouvinte de comunicação remota usando o protocolo de transporte remoto padrão.

>[!NOTE]
>O `Remoting` namespace está disponível como um pacote NuGet separado chamado `Microsoft.ServiceFabric.Services.Remoting`

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

    public Task HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context =>            this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> Os argumentos e os tipos de retorno na interface de serviço podem ser tipos simples, complexos ou personalizados, mas devem ser serializáveis pelo [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx)do .NET.
>
>

## <a name="call-remote-service-methods"></a>Chamar Métodos de Serviços Remotos
A chamada de métodos em um serviço usando a pilha de comunicação remota é feita usando um proxy local para o serviço por meio da classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` . O método `ServiceProxy` cria um proxy local usando a mesma interface que o serviço implementa. Com esse proxy, você pode chamar métodos na interface remotamente.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

A estrutura remota propaga exceções lançadas no serviço para o cliente. A lógica de manipulação de exceção no cliente usando `ServiceProxy` pode manipular diretamente as exceções que o serviço lança.

## <a name="service-proxy-lifetime"></a>Tempo de vida de Proxy do Serviço
A criação do Proxy do Serviço é uma operação simples, de modo que os usuários podem criar quantos forem necessários. O Proxy do Serviço poderá ser reutilizado enquanto os usuários precisarem. Se API remota gerar Exceção, os usuários ainda poderão reutilizar o mesmo proxy. Cada ServiceProxy contém um cliente de comunicação usado para enviar mensagens durante a transmissão. Ao invocar a API, temos uma verificação interna para saber se o cliente de comunicação usado é válido. Com base nesse resultado, recriamos o cliente da comunicação. Por isso, se ocorrer Exceção, os usuários não precisarão recriar o proxy do serviço.

### <a name="serviceproxyfactory-lifetime"></a>Tempo de vida de ServiceProxyFactory
[ServiceProxyFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) é um alocador que cria um proxy para interfaces remotas diferentes. Se você utiliza a API ServiceProxy.Create para criar proxy, então, a estrutura criará o ServiceProxyFactory singleton.
É útil para criá-lo manualmente quando você precisa substituir propriedades [IServiceRemotingClientFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory).
Alocador é uma operação cara. ServiceProxyFactory mantém o cache do cliente de comunicação.
A prática recomendada é armazenar em cache ServiceProxyFactory por mais tempo possível.

## <a name="remoting-exception-handling"></a>Tratamento de exceções remotas
Todas as exceções remotas lançadas pela API de serviço são reenviadas para o cliente como AggregateException. RemoteExceptions deve ser serializáveis DataContract serializável, ou [ServiceException](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) é lançada para a API do proxy com o erro de serialização.

ServiceProxy trata todas as exceções de failover da partição de serviço para a qual foi criado. Ele resolverá novamente os pontos de extremidade se houver exceções de failover (exceções não transitórias) e repetirá a chamada com o ponto de extremidade correto. O número de tentativas da exceção de failover é indefinido.
Se ocorrer Exceções Transitórias, o proxy tentará novamente a chamada.

Os parâmetros de repetição padrão são fornecidos por [OperationRetrySettings]. (https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) O usuário pode configurar esses valores passando o objeto OperationRetrySettings para ServiceProxyFactory construtor.
## <a name="how-to-use-remoting-v2-stack"></a>Como utiliza a Pilha V2 de Comunicação Remota
Com o pacote de Comunicação Remota NuGet 2.8, você tem a opção de usar a Pilha V2 de Comunicação Remota. A Pilha V2 de Comunicação Remota é mais eficaz e fornece recursos como APIs mais conectáveis e serializáveis.
Por padrão, se você não fizer as seguintes alterações, continuará utilizando a Pilha V1 de Comunicação Remota.
A V2 de Comunicação Remota não é compatível com V1 (Pilha de Comunicação Remota anterior), então, acompanhe o artigo a seguir sobre como atualizar de V1 para V2 sem afetar a disponibilidade do serviço.

### <a name="using-assembly-attribute-to-use-v2-stack"></a>Utilizando o Atributo de Assembly para usar a Pilha V2.

A seguir, são apresentadas as etapas para alterar a Pilha V2.

1. Adicione um Recurso de Ponto de Extremidade com nome "ServiceEndpointV2" no manifesto do serviço.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2.  Utilize o Método de Extensão de Comunicação Remota para criar Ouvinte de Comunicação Remota.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  Adicione o Atributo de Assembly nas Interfaces de Comunicação Remota.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
  ```
Nenhuma alteração é necessária no Projeto do Cliente.
Compile o assembly de Cliente com o assembly de interface para garantir que o atributo do assembly acima está sendo utilizado.

### <a name="using-explicit-v2-classes-to-create-listener-clientfactory"></a>Utilizando Classes V2 Explícitas para criar Ouvinte/ClientFactory
Execute as etapas a seguir.
1.  Adicione um Recurso de Ponto de Extremidade com nome "ServiceEndpointV2" no manifesto do serviço.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2. Utilize o [Ouvinte V2 de Comunicação Remota](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingistener?view=azure-dotnet). O nome do Recurso de Ponto de Extremidade de Serviço padrão utilizado é "ServiceEndpointV2" e deve ser definido no Manifesto do Serviço.

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

3. Utilize [Fábrica do Cliente](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) V2.
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Como atualizar de V1 de Comunicação Remota para V2 de Comunicação Remota.
Para atualizar de V1 para V2, são necessárias as atualizações na etapa 2. Siga as etapas a serem executadas na sequência listada.

1. Atualize o Serviço V1 para o Serviço V2 utilizando o Atributo CompactListener.
Essa alteração garante que o serviço está escutando no Ouvinte V2 e V1.

    a) Adicione um  Recurso de Ponto de Extremidade com nome "ServiceEndpointV2" no manifesto do serviço.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b) Utilize o Método de Extensão para criar Ouvinte de Comunicação Remota.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) Adicione o Atributo de Assembly em Interfaces de Comunicação Remota para utilizar CompatListener e Cliente V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.CompatListener, RemotingClient = RemotingClient.V2Client)]

      ```
2. Atualize o Cliente V1 para Cliente V2 utilizando o Atributo do Cliente V2.
Essa etapa garante que o Cliente está utilizando uma Pilha V2.
Nenhuma alteração no projeto do cliente/serviço é necessária. Compilar projetos de Cliente com assembly de interface atualizada é suficiente.

3. Esta etapa é opcional. Utilizar o Atributo V2Listener e, em seguida, Atualizar o Serviço V2.
Esta etapa garante que o serviço está escutando somente no Ouvinte V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
```

## <a name="how-to-use-custom-serialization-with-remoting-v2"></a>Como utilizar a Serialização Personalizada com V2 de Comunicação Remota.
O exemplo a seguir utiliza Serialização Json com V2 de Comunicação Remota.
1. Implemente a interface IServiceRemotingMessageSerializationProvider para fornecer implementação de serialização personalizada.
    A seguir, é apresentado o trecho de código sobre como a implementação se parece.

    ```csharp
    public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
    {
      public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType,
          IEnumerable<Type> requestBodyTypes)
      {
          return new ServiceRemotingRequestJsonMessageBodySerializer(serviceInterfaceType, requestBodyTypes);
      }

      public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType,
          IEnumerable<Type> responseBodyTypes)
      {
          return new ServiceRemotingResponseJsonMessageBodySerializer(serviceInterfaceType, responseBodyTypes);
      }

      public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
      {
          return new JsonMessageFactory();
      }
     }

  class JsonMessageFactory: IServiceRemotingMessageBodyFactory
  {
      public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName,
          int numberOfParameters)
      {
          return new JsonRemotingRequestBody(new JObject());
      }

      public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName)
      {
          return  new JsonRemotingResponseBody();
      }
   }

  class ServiceRemotingRequestJsonMessageBodySerializer: IServiceRemotingRequestMessageBodySerializer
  {
      public ServiceRemotingRequestJsonMessageBodySerializer(Type serviceInterfaceType,
          IEnumerable<Type> parameterInfo)
      {
      }

      public OutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
      {
          if (serviceRemotingRequestMessageBody == null)
          {
              return null;
          }

          var json = serviceRemotingRequestMessageBody.ToString();
          var bytes = Encoding.UTF8.GetBytes(json);
          var segment = new ArraySegment<byte>(bytes);
          var segments = new List<ArraySegment<byte>> {segment};
          return new OutgoingMessageBody(segments);
      }

      public IServiceRemotingRequestMessageBody Deserialize(IncomingMessageBody messageBody)
      {
          using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

          using (JsonReader reader = new JsonTextReader(sr))
          {
              var serializer = new JsonSerializer();
              var ob = serializer.Deserialize<JObject>(reader);
              var ob2 = new JsonRemotingRequestBody(ob);
              return ob2;
          }
      }
  }

  class ServiceRemotingResponseJsonMessageBodySerializer: IServiceRemotingResponseMessageBodySerializer
  {

      public ServiceRemotingResponseJsonMessageBodySerializer(Type serviceInterfaceType,
          IEnumerable<Type> parameterInfo)
      {
      }

      public OutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
      {
          var json = JsonConvert.SerializeObject(responseMessageBody,new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
          });
          var bytes = Encoding.UTF8.GetBytes(json);
          var segment = new ArraySegment<byte>(bytes);
          var list = new List<ArraySegment<byte>> {segment};
          return new OutgoingMessageBody(list);
      }

      public IServiceRemotingResponseMessageBody Deserialize(IncomingMessageBody messageBody)
      {
          using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

          using (var reader = new JsonTextReader(sr))
          {
              var serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                  TypeNameHandling = TypeNameHandling.All
              });
              return serializer.Deserialize<JsonRemotingResponseBody>(reader);
          }
      }
  }
  internal class JsonRemotingResponseBody: IServiceRemotingResponseMessageBody
  {
      public object Value;

      public void Set(object response)
      {
          this.Value = response;
      }

      public object Get(Type paramType)
      {
          return this.Value;
      }
  }

  class JsonRemotingRequestBody: IServiceRemotingRequestMessageBody
    {
      private readonly JObject jobject;

        public JsonRemotingRequestBody(JObject ob)
        {
            this.jobject = ob;
        }

        public void SetParameter(int position, string parameName, object parameter)
        {
            this.jobject.Add(parameName, JToken.FromObject(parameter));
        }

        public object GetParameter(int position, string parameName, Type paramType)
       {
           var ob = this.jobject[parameName];
           return ob.ToObject(paramType);
       }

       public override string ToString()
        {
            return this.jobject.ToString();
        }
    }
    ```

2.    Substitua o Provedor de Serialização Padrão com JsonSerializationProvider para Ouvinte de Comunicação Remota.

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

3.    Substitua o Provedor de Serialização Padrão com JsonSerializationProvider para Fábrica do Cliente de Comunicação Remota.

```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
            {
                return new FabricTransportServiceRemotingClientFactory(
                    serializationProvider: new ServiceRemotingJsonSerializationProvider());
            });
  ```

## <a name="next-steps"></a>Próximas etapas
* [API Web com OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicação WCF com o Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Securing communication for Reliable Services](service-fabric-reliable-services-secure-communication.md)
