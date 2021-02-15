---
title: Comunicação de serviço com o ASP.NET Core
description: Saiba como usar ASP.NET Core em aplicativos com e sem estado do Azure Service Fabric Reliable Services.
ms.topic: conceptual
ms.date: 10/12/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: a125c6a1972b51f518175a4c69248119f71ada7c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791587"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core no Service Fabric do Azure Reliable Services

ASP.NET Core é uma estrutura de software livre e de plataforma cruzada. Essa estrutura foi projetada para a criação de aplicativos conectados à Internet, baseados em nuvem, como aplicativos Web, aplicativos de IoT e back-ends móveis.

Este artigo é um guia detalhado para hospedar serviços de ASP.NET Core no Service Fabric Reliable Services usando o **Microsoft. AspNetCore** . conjunto de pacotes NuGet.

Para obter um tutorial introdutório sobre ASP.NET Core em Service Fabric e instruções sobre como configurar seu ambiente de desenvolvimento, consulte [tutorial: criar e implantar um aplicativo com um serviço de front-end de API Web ASP.NET Core e um serviço de back-end com estado](service-fabric-tutorial-create-dotnet-app.md).

O restante deste artigo pressupõe que você já esteja familiarizado com ASP.NET Core. Caso contrário, leia os [conceitos básicos do ASP.NET Core](/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core no ambiente do Service Fabric

Os aplicativos ASP.NET Core e Service Fabric podem ser executados no .NET Core ou no .NET Framework completo. Você pode usar ASP.NET Core de duas maneiras diferentes no Service Fabric:
 - **Hospedado como um executável convidado**. Dessa forma, é usada principalmente para executar aplicativos ASP.NET Core existentes no Service Fabric sem alterações de código.
 - **Execute dentro de um serviço confiável**. Dessa forma, permite uma melhor integração com o tempo de execução do Service Fabric e permite serviços de ASP.NET Core com estado.

O restante deste artigo explica como usar ASP.NET Core dentro de um serviço confiável, por meio dos componentes de integração do ASP.NET Core que acompanham o SDK do Service Fabric.

## <a name="service-fabric-service-hosting"></a>Hospedagem de serviços do Service Fabric

No Service Fabric, uma ou mais instâncias e/ou réplicas do serviço são executadas em um *processo de host de serviço*: um arquivo executável que executa o código de serviço. Você, como autor de um serviço, possui o processo de host de serviço e Service Fabric ativa e monitora para você.

O ASP.NET tradicional (até 5 MVC) está estreitamente relacionado ao IIS por meio de System.Web.dll. O Núcleo do ASP.NET fornece uma separação entre o servidor Web e o aplicativo Web. Essa separação permite que os aplicativos Web sejam portáteis entre diferentes servidores Web. Ele também permite que os servidores Web sejam *hospedados automaticamente*. Isso significa que você pode iniciar um servidor Web em seu próprio processo, em oposição a um processo que é de propriedade de um software de servidor Web dedicado, como o IIS.

Para combinar um serviço de Service Fabric e o ASP.NET, como um executável convidado ou em um serviço confiável, você deve ser capaz de iniciar o ASP.NET dentro do processo de host do serviço. A hospedagem interna do Núcleo do ASP.NET permite que você faça isso.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hospedando ASP.NET Core em um serviço confiável
Normalmente, aplicativos do Núcleo do ASP.NET auto-hospedados criam um WebHost no ponto de entrada do aplicativo, como o método `static void Main()` em `Program.cs`. Nesse caso, o ciclo de vida do Webhost está associado ao ciclo de vida do processo.

![Hospedando o Núcleo do ASP.NET em um processo][0]

Mas o ponto de entrada do aplicativo não é o lugar certo para criar um Webhost em um serviço confiável. Isso ocorre porque o ponto de entrada do aplicativo é usado apenas para registrar um tipo de serviço com o tempo de execução Service Fabric, para que ele possa criar instâncias desse tipo de serviço. O Webhost deve ser criado em um serviço confiável em si. Dentro do processo de host de serviço, as instâncias de serviço e/ou réplicas podem passar por vários ciclos de vida. 

Uma instância de um serviço confiável é representada por sua classe de serviço derivando de `StatelessService` ou `StatefulService`. A pilha de comunicação para um serviço está contida em uma implementação `ICommunicationListener` em sua classe de serviço. Os `Microsoft.ServiceFabric.AspNetCore.*` pacotes NuGet contêm implementações do `ICommunicationListener` que iniciam e gerenciam o ASP.NET Core Webhost para Kestrel ou HTTP.sys em um serviço confiável.

![Diagrama para hospedagem de ASP.NET Core em um serviço confiável][1]

## <a name="aspnet-core-icommunicationlisteners"></a>Núcleo do ASP.NET ICommunicationListeners
As `ICommunicationListener` implementações para Kestrel e HTTP.sys nos  `Microsoft.ServiceFabric.AspNetCore.*` pacotes NuGet têm padrões de uso semelhantes. Mas eles executam ações um pouco diferentes específicas para cada servidor Web. 

Ambos os ouvintes de comunicação fornecem um construtor que usa os seguintes argumentos:
 - **`ServiceContext serviceContext`**: Esse é o `ServiceContext` objeto que contém informações sobre o serviço em execução.
 - **`string endpointName`**: Este é o nome de uma `Endpoint` configuração no ServiceManifest.xml. É principalmente onde os dois ouvintes de comunicação diferem. HTTP.sys *requer* uma `Endpoint` configuração, enquanto Kestrel não.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: É um lambda que você implementa, no qual você cria e retorna um `IWebHost` . Ele permite que você configure `IWebHost` a maneira que normalmente faria em um aplicativo ASP.NET Core. O lambda fornece uma URL que é gerada para você, dependendo das opções de integração de Service Fabric que você usa e a `Endpoint` configuração que você fornece. Em seguida, você pode modificar ou usar essa URL para iniciar o servidor Web.

## <a name="service-fabric-integration-middleware"></a>Middleware de integração do Service Fabric
O `Microsoft.ServiceFabric.AspNetCore` pacote NuGet inclui o `UseServiceFabricIntegration` método de extensão no `IWebHostBuilder` , que adiciona o middleware com reconhecimento Service Fabric. Esse middleware configura o Kestrel ou o HTTP.sys `ICommunicationListener` para registrar uma URL de serviço exclusiva com o serviço de nomenclatura de Service Fabric. Em seguida, ele valida as solicitações do cliente para garantir que os clientes estejam se conectando ao serviço certo. 

Essa etapa é necessária para impedir que os clientes se conectem erroneamente ao serviço errado. Isso ocorre porque, em um ambiente de host compartilhado, como Service Fabric, vários aplicativos Web podem ser executados na mesma máquina física ou virtual, mas não usam nomes de host exclusivos. Esse cenário é descrito em mais detalhes na próxima seção.

### <a name="a-case-of-mistaken-identity"></a>Um caso de identidade incorreta
Independentemente do protocolo, as réplicas de serviço escutam em uma combinação de IP:porta exclusiva. Depois que uma réplica de serviço começa a escutar em um ponto de extremidade de IP: porta, ela relata esse endereço de ponto de extremidade para o Service Fabric Serviço de Nomenclatura. Lá, os clientes ou outros serviços podem descobri-lo. Se os serviços usarem portas de aplicativo atribuídas dinamicamente, uma réplica de serviço poderá usar coincidentemente o mesmo ponto de extremidade de IP: porta de outro serviço anteriormente na mesma máquina física ou virtual. Isso pode fazer com que um cliente se conecte por engano ao serviço incorreto. Esse cenário pode resultar se ocorrer a seguinte sequência de eventos:

 1. O serviço A escuta em 10.0.0.1:30000 via HTTP. 
 2. O cliente resolve o serviço A e Obtém o endereço 10.0.0.1:30000.
 3. O Serviço A move-se para um nó diferente.
 4. O serviço B é colocado em 10.0.0.1 e usa coincidentemente a mesma porta 30000.
 5. O cliente tenta se conectar ao serviço A com o endereço de cache 10.0.0.1:30000.
 6. O cliente agora está conectado com êxito ao serviço B, não percebendo que ele está conectado ao serviço errado.

Isso pode causar bugs em momentos aleatórios, que podem ser difíceis de diagnosticar.

### <a name="using-unique-service-urls"></a>Usando URLs de serviço exclusivas
Para evitar esses bugs, os serviços podem postar um ponto de extremidade no Serviço de Nomenclatura com um identificador exclusivo e, em seguida, validar esse identificador exclusivo durante as solicitações do cliente. Essa é uma ação cooperativa entre serviços em um ambiente confiável de locatário não hostil. Ele não fornece autenticação de serviço segura em um ambiente de locatário hostil.

Em um ambiente confiável, o middleware adicionado pelo `UseServiceFabricIntegration` método acrescenta automaticamente um identificador exclusivo ao endereço Postado para o serviço de nomenclatura. Ele valida esse identificador em cada solicitação. Se o identificador não corresponder, o middleware retornará imediatamente uma resposta HTTP 410 out.

Os serviços que usam uma porta atribuída dinamicamente devem fazer uso desse middleware.

Os serviços que usam uma porta exclusiva fixa não têm esse problema em um ambiente cooperativo. Uma porta fixa é normalmente usada para serviços voltados para o exterior que exigem uma porta conhecida para a conexão de aplicativos clientes. Por exemplo, a maioria dos aplicativos da Web voltados para a Internet usará a porta 80 ou 443 para conexões de navegador da Web. Nesse caso, o identificador exclusivo não deve ser habilitado.

O diagrama a seguir mostra o fluxo de solicitação com o middleware habilitado:

![Integração de núcleo do ASP.NET do Service Fabric][2]

As implementações Kestrel e HTTP.sys `ICommunicationListener` usam esse mecanismo exatamente da mesma maneira. Embora HTTP.sys possa diferenciar internamente as solicitações com base em caminhos de URL exclusivos usando o recurso de compartilhamento de porta **HTTP.sys** subjacente, essa funcionalidade *não* é usada pela implementação de HTTP.sys `ICommunicationListener` . Isso ocorre porque resulta em códigos de status de erro HTTP 503 e HTTP 404 no cenário descrito anteriormente. Isso, por sua vez, dificulta para os clientes determinarem a intenção do erro, pois HTTP 503 e HTTP 404 são comumente usados para indicar outros erros. 

Portanto, as implementações Kestrel e HTTP.sys `ICommunicationListener` padronizam o middleware fornecido pelo `UseServiceFabricIntegration` método de extensão. Portanto, os clientes precisam apenas executar uma ação de reresolução de ponto de extremidade de serviço em respostas HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys em Reliable Services
Você pode usar HTTP.sys em Reliable Services importando o pacote NuGet **Microsoft. AspNetCore. https** . Este pacote contém `HttpSysCommunicationListener` , uma implementação do `ICommunicationListener` . `HttpSysCommunicationListener` permite que você crie um Webhost ASP.NET Core dentro de um serviço confiável usando HTTP.sys como o servidor Web.

O HTTP.sys é criado na [API do servidor http do Windows](/windows/win32/http/http-api-start-page). Essa API usa o driver de kernel **HTTP.sys** para processar solicitações HTTP e roteá-las para processos que executam aplicativos Web. Isso permite que vários processos na mesma máquina física ou virtual hospedem aplicativos Web na mesma porta, sem ambigüidade por um caminho de URL exclusivo ou nome de host. Esses recursos são úteis no Service Fabric para hospedar vários sites no mesmo cluster.

>[!NOTE]
>HTTP.sys implementação funciona apenas na plataforma Windows.

O diagrama a seguir ilustra como o HTTP.sys usa o driver de kernel **HTTP.sys** no Windows para compartilhamento de porta:

![Diagrama de HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys em um serviço sem estado
Para usar `HttpSys` em um serviço sem estado, substitua o método `CreateServiceInstanceListeners` e retorne uma instância `HttpSysCommunicationListener`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys em um serviço com estado

`HttpSysCommunicationListener` Não está projetado atualmente para uso em serviços com estado devido a complicações com o recurso de compartilhamento de porta **HTTP.sys** subjacente. Para obter mais informações, consulte a seção a seguir sobre alocação de porta dinâmica com HTTP.sys. Para serviços com estado, Kestrel é o servidor Web sugerido.

### <a name="endpoint-configuration"></a>Configuração de ponto de extremidade

Uma `Endpoint` configuração é necessária para servidores Web que usam a API do servidor http do Windows, incluindo HTTP.sys. Os servidores Web que usam a API do servidor HTTP do Windows devem primeiro reservar sua URL com HTTP.sys (isso normalmente é feito com a ferramenta [netsh](/windows/win32/http/netsh-commands-for-http) ). 

Essa ação requer privilégios elevados que os serviços não têm, por padrão. As opções "http" ou "https" para a `Protocol` propriedade da `Endpoint` configuração em ServiceManifest.xml são usadas especificamente para instruir o tempo de execução Service Fabric para registrar uma URL com HTTP.sys em seu nome. Ele faz isso usando o prefixo de URL de [*curinga forte*](/windows/win32/http/urlprefix-strings) .

Por exemplo, para reservar `http://+:80` um serviço, use a seguinte configuração no ServiceManifest.xml:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

E o nome do ponto de extremidade deve ser passado para o construtor `HttpSysCommunicationListener`:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Usar HTTP.sys com uma porta estática
Para usar uma porta estática com HTTP.sys, forneça o número da porta na `Endpoint` configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Usar HTTP.sys com uma porta dinâmica
Para usar uma porta atribuída dinamicamente com HTTP.sys, omita a `Port` Propriedade na `Endpoint` configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Uma porta dinâmica alocada por uma `Endpoint` configuração fornece apenas uma porta *por processo de host*. O modelo de Hospedagem de Service Fabric atual permite que várias instâncias de serviço e/ou réplicas sejam hospedadas no mesmo processo. Isso significa que cada uma delas compartilhará a mesma porta quando alocada por meio da `Endpoint` configuração. Várias instâncias de **HTTP.sys** podem compartilhar uma porta usando o recurso de compartilhamento de porta **HTTP.sys** subjacente. Mas não há suporte para isso `HttpSysCommunicationListener` devido às complicações que ele introduz para solicitações de cliente. Para o uso de porta dinâmica, Kestrel é o servidor Web sugerido.

## <a name="kestrel-in-reliable-services"></a>Kestrel em Serviços Confiáveis
Você pode usar o Kestrel em Reliable Services importando o pacote NuGet **Microsoft. Microsoft. AspNetCore. Kestrel** . Este pacote contém `KestrelCommunicationListener` , uma implementação do `ICommunicationListener` . `KestrelCommunicationListener` permite que você crie um Webhost ASP.NET Core dentro de um serviço confiável usando o Kestrel como o servidor Web.

O Kestrel é um servidor Web multiplataforma para o ASP.NET Core. Ao contrário de HTTP.sys, Kestrel não usa um Gerenciador de ponto de extremidade centralizado. Além disso, ao contrário de HTTP.sys, o Kestrel não dá suporte ao compartilhamento de porta entre vários processos. Cada instância do Kestrel deve usar uma porta exclusiva. Para obter mais informações sobre Kestrel, consulte os [detalhes da implementação](/aspnet/core/fundamentals/servers/kestrel).

![Diagrama de Kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel em um serviço sem estado
Para usar `Kestrel` em um serviço sem estado, substitua o método `CreateServiceInstanceListeners` e retorne uma instância `KestrelCommunicationListener`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel em um serviço com estado
Para usar `Kestrel` em um serviço com estado, substitua o método `CreateServiceReplicaListeners` e retorne uma instância `KestrelCommunicationListener`:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

Neste exemplo, uma instância única de `IReliableStateManager` é fornecida ao contêiner de injeção de dependência do WebHost. Isso não é estritamente necessário, mas permite que você use `IReliableStateManager` e Reliable Collections em seus métodos de ação do controlador MVC.

Um nome de configuração `Endpoint`*não* é fornecido para `KestrelCommunicationListener` em um serviço com estado. Isso será explicado mais detalhadamente na seção a seguir.

### <a name="configure-kestrel-to-use-https"></a>Configurar o Kestrel para usar HTTPS
Ao habilitar HTTPS com Kestrel em seu serviço, você precisará definir várias opções de escuta. Atualize o `ServiceInstanceListener` para usar um ponto de extremidade *EndpointHttps* e escute em uma porta específica (como a porta 443). Ao configurar o host Web para usar o servidor Web Kestrel, você deve configurar o Kestrel para escutar endereços IPv6 em todas as interfaces de rede: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Para obter um exemplo completo em um tutorial, consulte [Configurar Kestrel para usar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Configuração de ponto de extremidade
Uma `Endpoint` configuração não é necessária para usar o Kestrel. 

Kestrel é um servidor Web autônomo simples. Ao contrário de HTTP.sys (ou HttpListener), ele não precisa de uma `Endpoint` configuração em ServiceManifest.xml porque ele não requer o registro de URL antes de iniciar. 

#### <a name="use-kestrel-with-a-static-port"></a>Usar Kestrel com uma porta estática
Você pode configurar uma porta estática na `Endpoint` configuração de ServiceManifest.xml para uso com Kestrel. Embora isso não seja estritamente necessário, ele oferece dois benefícios potenciais:
 - Se a porta não cair no intervalo de portas do aplicativo, ela será aberta por meio do firewall do sistema operacional pelo Service Fabric.
 - A URL fornecida através de `KestrelCommunicationListener` usará essa porta.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Se um `Endpoint` for configurado, o nome deverá ser passado para o construtor `KestrelCommunicationListener`: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Se ServiceManifest.xml não usar uma `Endpoint` configuração, omita o nome no `KestrelCommunicationListener` Construtor. Nesse caso, ele usará uma porta dinâmica. Consulte a próxima seção para obter mais informações sobre isso.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Usar Kestrel com uma porta dinâmica
Kestrel não pode usar a atribuição de porta automática da `Endpoint` configuração no ServiceManifest.xml. Isso ocorre porque a atribuição automática de porta de uma `Endpoint` configuração atribui uma porta exclusiva por *processo de host*, e um único processo de host pode conter várias instâncias de Kestrel. Isso não funciona com Kestrel porque não dá suporte ao compartilhamento de porta. Portanto, cada instância de Kestrel deve ser aberta em uma porta exclusiva.

Para usar a atribuição de porta dinâmica com Kestrel, omita a `Endpoint` configuração em ServiceManifest.xml inteiramente e não passe um nome de ponto de extremidade para o `KestrelCommunicationListener` Construtor, da seguinte maneira:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Nessa configuração, `KestrelCommunicationListener` selecionará automaticamente uma porta não utilizada do intervalo de portas de aplicativo.

Para HTTPS, ele deve ter o ponto de extremidade configurado com o protocolo HTTPS sem uma porta especificada em ServiceManifest.xml e passar o nome do ponto de extremidade para o Construtor KestrelCommunicationListener.


## <a name="service-fabric-configuration-provider"></a>Provedor de configuração de Service Fabric
A configuração do aplicativo no ASP.NET Core é baseada em pares chave-valor estabelecidos pelo provedor de configuração. Leia [configuração no ASP.NET Core](/aspnet/core/fundamentals/configuration/) para saber mais sobre o suporte geral ASP.NET Core configuração.

Esta seção descreve como o provedor de configuração do Service Fabric se integra com a configuração do ASP.NET Core importando o `Microsoft.ServiceFabric.AspNetCore.Configuration` pacote NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Extensões de inicialização do AddServiceFabricConfiguration
Depois de importar o `Microsoft.ServiceFabric.AspNetCore.Configuration` pacote NuGet, você precisa registrar a fonte de configuração Service Fabric com ASP.NET Core API de configuração. Faça isso verificando extensões **AddServiceFabricConfiguration** no `Microsoft.ServiceFabric.AspNetCore.Configuration` namespace em relação a `IConfigurationBuilder` .

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

Agora, o serviço de ASP.NET Core pode acessar as definições de configuração de Service Fabric, assim como qualquer outra configuração de aplicativo. Por exemplo, você pode usar o padrão de opções para carregar as configurações em objetos com rigidez de tipos.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Mapeamento de chave padrão
Por padrão, o provedor de configuração Service Fabric inclui o nome do pacote, o nome da seção e o nome da propriedade. Juntos, eles formam a chave de configuração ASP.NET Core, da seguinte maneira:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Por exemplo, se você tiver um pacote de configuração chamado `MyConfigPackage` com o conteúdo a seguir, o valor de configuração estará disponível em ASP.NET Core `IConfiguration` por meio de *MyConfigPackage: myconfigsection: MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opções de configuração do Service Fabric
O provedor de configuração de Service Fabric também dá suporte `ServiceFabricConfigurationOptions` para alterar o comportamento padrão do mapeamento de chave.

#### <a name="encrypted-settings"></a>Configurações criptografadas
O Service Fabric dá suporte a configurações criptografadas, assim como o provedor de configuração de Service Fabric. As configurações criptografadas não são descriptografadas para ASP.NET Core `IConfiguration` por padrão. Em vez disso, os valores criptografados são armazenados nesse local. Mas se você quiser descriptografar o valor a ser armazenado no ASP.NET Core IConfiguration, poderá definir o sinalizador *decryptvalue* como false na `AddServiceFabricConfiguration` extensão, da seguinte maneira:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Vários pacotes de configuração
O Service Fabric dá suporte a vários pacotes de configuração. Por padrão, o nome do pacote é incluído na chave de configuração. Mas você pode definir o `IncludePackageName` sinalizador como false, da seguinte maneira:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Mapeamento de chave personalizada, extração de valor e população de dados
O provedor de configuração de Service Fabric também dá suporte a cenários mais avançados para personalizar o mapeamento de chave com `ExtractKeyFunc` e extrair os valores personalizados com `ExtractValueFunc` . Você pode até mesmo alterar todo o processo de preenchimento de dados da configuração Service Fabric para ASP.NET Core configuração usando o `ConfigAction` .

Os exemplos a seguir ilustram como usar o `ConfigAction` para personalizar a população de dados:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Atualizações de configuração
O provedor de configuração de Service Fabric também dá suporte a atualizações de configuração. Você pode usar ASP.NET Core `IOptionsMonitor` para receber notificações de alteração e, em seguida, usar `IOptionsSnapshot` para recarregar os dados de configuração. Para obter mais informações, consulte [Opções de ASP.NET Core](/aspnet/core/fundamentals/configuration/options).

Essas opções têm suporte por padrão. Nenhuma codificação adicional é necessária para habilitar atualizações de configuração.

## <a name="scenarios-and-configurations"></a>Cenários e configurações
Esta seção fornece a combinação de servidor Web, configuração de porta, opções de integração de Service Fabric e configurações diversas que recomendamos para solucionar os seguintes cenários:
 - Serviços sem monitoração de estado do Núcleo do ASP.NET expostos externamente
 - Somente interno ASP.NET Core serviços sem monitoração de estado
 - Somente interno ASP.NET Core serviços com estado

Um **serviço exposto externamente** é aquele que expõe um ponto de extremidade chamado de fora do cluster, geralmente por meio de um balanceador de carga.

Um serviço **somente interno** é aquele cujo ponto de extremidade é chamado apenas de dentro do cluster.

> [!NOTE]
> Os pontos de extremidade de serviço com estado geralmente não devem ser expostos à Internet. Os clusters por trás de balanceadores de carga que não reconhecem Service Fabric resolução de serviço, como Azure Load Balancer, não poderão expor serviços com estado. Isso ocorre porque o balanceador de carga não poderá localizar e rotear o tráfego para a réplica de serviço com estado apropriada. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Serviços sem monitoração de estado do Núcleo do ASP.NET expostos externamente
Kestrel é o servidor Web sugerido para serviços de front-end que expõem pontos de extremidade HTTP externos para a Internet. No Windows, HTTP.sys pode fornecer a capacidade de compartilhamento de porta, que permite hospedar vários serviços Web no mesmo conjunto de nós usando a mesma porta. Nesse cenário, os serviços Web são diferenciados por nome de host ou caminho, sem depender de um proxy de front-end ou gateway para fornecer roteamento HTTP.
 
Quando exposto à Internet, um serviço sem estado deve usar um ponto de extremidade bem conhecido e estável acessível por meio de um balanceador de carga. Você fornecerá essa URL para os usuários do seu aplicativo. Recomendamos a seguinte configuração:

| Type | Recomendação | Observações |
| ---- | -------------- | ----- |
| Servidor Web | Kestrel | Kestrel é o servidor Web preferencial, pois tem suporte no Windows e no Linux. |
| Configuração de portas | static | Uma porta estática conhecida deve ser configurada na configuração do `Endpoints` de ServiceManifest.XML, como 80 para HTTP ou 443 para HTTPS. |
| ServiceFabricIntegrationOptions | Nenhum | Use a `ServiceFabricIntegrationOptions.None` opção ao configurar o middleware de integração Service Fabric, para que o serviço não tente validar solicitações de entrada para um identificador exclusivo. Os usuários externos do seu aplicativo não saberão as informações exclusivas de identificação que o middleware usa. |
| Contagem de Instâncias | -1 | Em casos de uso típicos, a configuração de contagem de instâncias deve ser definida como *-1*. Isso é feito para que uma instância esteja disponível em todos os nós que recebem o tráfego de um balanceador de carga. |

Se vários serviços expostos externamente compartilharem o mesmo conjunto de nós, você poderá usar HTTP.sys com um caminho de URL exclusivo, mas estável. Você pode fazer isso modificando a URL fornecida ao configurar o IWebHost. Observe que isso se aplica somente a HTTP.sys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Serviço do Núcleo do ASP.NET sem estado somente para uso interno
Os serviços sem monitoração de estado que são chamados apenas de dentro do cluster devem usar URLs exclusivas e portas atribuídas dinamicamente para garantir a cooperação entre vários serviços. Recomendamos a seguinte configuração:

| Type | Recomendação | Observações |
| ---- | -------------- | ----- |
| Servidor Web | Kestrel | Embora você possa usar HTTP.sys para serviços sem estado internos, o Kestrel é o melhor servidor para permitir que várias instâncias de serviço compartilhem um host.  |
| Configuração de portas | atribuídas dinamicamente | Várias réplicas de um serviço com estado podem compartilhar um processo de host ou sistema operacional do host e, portanto, precisarão de portas exclusivas. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Com a atribuição dinâmica de portas essa configuração impede o problema de confusão de identidade descrito anteriormente. |
| InstanceCount | any | A configuração de contagem de instâncias pode ser definida como qualquer valor necessário para operar o serviço. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Somente interno serviço com estado do Núcleo do ASP.NET
Os serviços com monitoração de estado que são chamados apenas de dentro do cluster devem usar portas atribuídas dinamicamente para garantir a cooperação entre vários serviços. Recomendamos a seguinte configuração:

| Type | Recomendação | Observações |
| ---- | -------------- | ----- |
| Servidor Web | Kestrel | O `HttpSysCommunicationListener` não é projetado para uso por serviços com estado em que as réplicas compartilham um processo de host. |
| Configuração de portas | atribuídas dinamicamente | Várias réplicas de um serviço com estado podem compartilhar um processo de host ou sistema operacional do host e, portanto, precisarão de portas exclusivas. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Com a atribuição dinâmica de portas essa configuração impede o problema de confusão de identidade descrito anteriormente. |

## <a name="next-steps"></a>Próximas etapas
[Depurar seu aplicativo do Service Fabric usando o Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
