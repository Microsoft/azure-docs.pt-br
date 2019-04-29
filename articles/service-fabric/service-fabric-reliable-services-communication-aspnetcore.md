---
title: Comunicação de serviço com o Núcleo do ASP.NET | Microsoft Docs
description: Aprenda a usar o Núcleo do ASP.NET em Serviços Confiáveis com e sem estado.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 5a4b7514005da9e9a998dba014fa0ea6c014397a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774554"
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>Núcleo do ASP.NET em Serviços Confiáveis do Service Fabric

O Núcleo do ASP.NET é uma nova estrutura de software livre e plataforma cruzada para criar aplicativos modernos baseados em nuvem conectados à Internet, como aplicativos Web, aplicativos de IoT e back-ends móveis. 

Este artigo é um guia detalhado de hospedagem de serviços do ASP.NET Core nos Reliable Services do Service Fabric usando o conjunto **Microsoft.ServiceFabric.AspNetCore.*** de pacotes NuGet.

Para um tutorial de lançamento sobre o ASP.NET Core no Microsoft Azure Service Fabric e as instruções de como configurar o ambiente de desenvolvimento, consulte [Criar um aplicativo .NET](service-fabric-tutorial-create-dotnet-app.md).

O restante deste artigo pressupõe que você já esteja familiarizado com o ASP.NET Core. Se não estiver, recomendamos ler os [Conceitos básicos do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core no ambiente do Service Fabric

Os aplicativos ASP.NET Core e Service Fabric podem ser executados no .NET Core e no .NET Framework completo. O Núcleo do ASP.NET pode ser usado de duas maneiras diferentes no Service Fabric:
 - **Hospedado como um executável convidado**. Isso é usado principalmente para executar aplicativos do Núcleo do ASP.NET existentes no Service Fabric sem alterações de código.
 - **Executar em um Serviço Confiável**. Isso permite uma melhor integração com o tempo de execução do Service Fabric e permite os serviços de Núcleo do ASP.NET com monitoração de estado.

O restante deste artigo explica como usar o Núcleo do ASP.NET em um Serviço Confiável usando os componentes de integração de Núcleo do ASP.NET que são fornecidos com o SDK do Service Fabric. 

## <a name="service-fabric-service-hosting"></a>Hospedagem de serviços do Service Fabric

No Service Fabric, uma ou mais instâncias e/ou réplicas do serviço são executadas em um *processo de host do serviço*, um arquivo executável que executa o código de serviço. Como autor do serviço, você é responsável pelo processo de host do serviço, e o Service Fabric o ativa e monitora para você.

O ASP.NET tradicional (até 5 MVC) está estreitamente relacionado ao IIS por meio de System.Web.dll. O Núcleo do ASP.NET fornece uma separação entre o servidor Web e o aplicativo Web. Isso permite a portabilidade de aplicativos Web entre diferentes servidores Web e  permite também que os servidores Web sejam *auto-hospedados*, o que significa que você pode iniciar um servidor Web em seu próprio processo, em vez de um processo que pertence ao software do servidor Web dedicado, como o IIS. 

Para combinar um serviço do Service Fabric ao ASP.NET, como um executável de convidado ou em um Serviço Confiável, você deve poder iniciar o ASP.NET no processo de host de serviço. A hospedagem interna do Núcleo do ASP.NET permite que você faça isso.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hospedando o Núcleo do ASP.NET em um Serviço Confiável
Normalmente, aplicativos do Núcleo do ASP.NET auto-hospedados criam um WebHost no ponto de entrada do aplicativo, como o método `static void Main()` em `Program.cs`. Nesse caso, o ciclo de vida do WebHost está associado ao ciclo de vida do processo.

![Hospedando o Núcleo do ASP.NET em um processo][0]

No entanto, o ponto de entrada do aplicativo não é o lugar certo para criar um WebHost em um serviço confiável, pois o ponto de entrada do aplicativo só é usado para registrar um tipo de serviço com o tempo de execução do Service Fabric, para que possa criar instâncias desse tipo de serviço. O WebHost deve ser criado em um Serviço Confiável em si. No processo de host do serviço, instâncias de serviço e/ou réplicas podem passar por vários ciclos de vida. 

Uma instância de um serviço confiável é representada por sua classe de serviço derivando de `StatelessService` ou `StatefulService`. A pilha de comunicação para um serviço está contida em uma implementação `ICommunicationListener` em sua classe de serviço. Os pacotes NuGet `Microsoft.ServiceFabric.AspNetCore.*` contêm implementações de `ICommunicationListener` que iniciam e gerenciam o WebHost de Núcleo do ASP.NET para Kestrel ou HttpSys em um Reliable Service.

![Hospedando o Núcleo do ASP.NET em um Serviço Confiável][1]

## <a name="aspnet-core-icommunicationlisteners"></a>Núcleo do ASP.NET ICommunicationListeners
As implementações `ICommunicationListener` para Kestrel e HttpSys nos pacotes NuGet `Microsoft.ServiceFabric.AspNetCore.*` têm padrões de uso semelhantes, mas executam ações específicas ligeiramente diferentes para cada servidor Web. 

Ambos os ouvintes de comunicação fornecem um construtor que usa os seguintes argumentos:
 - **`ServiceContext serviceContext`**: O objeto `ServiceContext` que contém informações sobre o serviço em execução.
 - **`string endpointName`**: o nome de uma configuração `Endpoint` em ServiceManifest.XML. Isso ocorre principalmente quando os dois ouvintes da comunicação são diferentes: HttpSys **exige** uma configuração `Endpoint`, ao contrário do Kestrel.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: um lambda que você implementa e no qual cria e retorna um `IWebHost`. Isso permite que você configure `IWebHost` da maneira como faria normalmente em um aplicativo do Núcleo do ASP.NET. O lambda fornece uma URL que é gerada para você, dependendo das opções de integração do Service Fabric que você usar e da configuração `Endpoint` que você fornecer. Essa URL pode então ser modificada ou usada como está para iniciar o servidor Web.

## <a name="service-fabric-integration-middleware"></a>Middleware de integração do Service Fabric
O pacote do NuGet `Microsoft.ServiceFabric.AspNetCore` inclui o método de extensão `UseServiceFabricIntegration` no `IWebHostBuilder` que adiciona o middleware com reconhecimento do Service Fabric. Esse middleware configura o `ICommunicationListener` do Kestrel ou HttpSys para registrar uma URL de serviço exclusivo no Serviço de Nomenclatura do Service Fabric e valida solicitações do cliente para assegurar que os clientes se conectem ao serviço certo. Isso é necessário em um ambiente de host compartilhado como o Service Fabric, em que vários aplicativos Web podem ser executados na mesma máquina física ou virtual, mas não usam nomes de host exclusivos, para impedir que os clientes se conectem ao serviço errado por engano. Esse cenário é descrito em mais detalhes na próxima seção.

### <a name="a-case-of-mistaken-identity"></a>Um caso de identidade incorreta
Independentemente do protocolo, as réplicas de serviço escutam em uma combinação de IP:porta exclusiva. Depois que uma réplica de serviço começa a escutar em um ponto de extremidade IP:porta, ela relata esse endereço do ponto de extremidade para o Serviço de Nomenclatura do Service Fabric, onde pode ser descoberto por clientes ou por outros serviços. Se os serviços usarem portas de aplicativos atribuídas dinamicamente, uma réplica de serviço poderá usar coincidentemente o mesmo ponto de extremidade IP:porta que outro serviço que estava anteriormente na mesma máquina física ou virtual. Isso pode fazer com que um cliente se conecte por engano ao serviço incorreto. Isso poderá acontecer se a seguinte sequência de eventos ocorrer:

 1. O serviço A escuta em 10.0.0.1:30000 via HTTP. 
 2. O cliente resolve o serviço A e obtém o endereço 10.0.0.1:30000
 3. O Serviço A move-se para um nó diferente.
 4. O serviço B é colocado em 10.0.0.1 e usa coincidentemente a mesma porta 30000.
 5. O cliente tenta se conectar ao serviço A com o endereço de cache 10.0.0.1:30000.
 6. Agora o cliente é conectado com êxito ao serviço B sem perceber que está conectado ao serviço errado.

Isso pode causar bugs em momentos aleatórios, que podem ser difíceis de diagnosticar. 

### <a name="using-unique-service-urls"></a>Usando URLs de serviço exclusivas
Para evitar isso, os serviços podem postar um ponto de extremidade para o Serviço de Nomenclatura com um identificador exclusivo e validar esse identificador exclusivo durante as solicitações do cliente. Essa é uma ação cooperativa entre serviços em um ambiente confiável de locatário não hostil. Isso não fornece autenticação segura em um ambiente de locatário hostil.

Em um ambiente confiável, o middleware adicionado pelo método `UseServiceFabricIntegration` anexa automaticamente um identificador exclusivo ao endereço que é postado para o Serviço de Nomes e valida esse identificador em cada solicitação. Se o identificador não for correspondente, o middleware retornará imediatamente uma resposta HTTP 410 Não existe mais.

Os serviços que usam uma porta atribuída dinamicamente devem fazer uso desse middleware.

Os serviços que usam uma porta exclusiva fixa não têm esse problema em um ambiente cooperativo. Uma porta fixa é normalmente usada para serviços voltados para o exterior que exigem uma porta conhecida para a conexão de aplicativos clientes. Por exemplo, a maioria dos aplicativos Web voltados para a Internet usará a porta 80 ou 443 para conexões do navegador da Web. Nesse caso, o identificador exclusivo não deve ser habilitado.

O diagrama a seguir mostra o fluxo de solicitação com o middleware habilitado:

![Integração de núcleo do ASP.NET do Service Fabric][2]

As implementações `ICommunicationListener` do Kestrel e do HttpSys usam esse mecanismo exatamente da mesma maneira. Embora o HttpSys possa diferenciar internamente as solicitações com base em caminhos de URL exclusivos usando o recurso subjacente de compartilhamento de porta *http.sys*, essa funcionalidade *não* é usada pela implementação `ICommunicationListener` do HttpSys, pois isso resultará em códigos de status de erro HTTP 503 e HTTP 404 no cenário descrito anteriormente. Isso, por sua vez, dificulta que os clientes determinem a intenção do erro, já que o HTTP 503 e HTTP 404 já são frequentemente usados para indicar outros erros. Dessa forma, as implementações `ICommunicationListener` do Kestrel e do HttpSys são padronizadas com o middleware fornecido pelo método de extensão `UseServiceFabricIntegration` para que os clientes só precisem executar uma ação de nova resolução de ponto de extremidade de serviço em respostas HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HttpSys em Reliable Services
O HttpSys pode ser usado em um Reliable Service importando o pacote NuGet **Microsoft.ServiceFabric.AspNetCore.HttpSys**. Esse pacote contém o `HttpSysCommunicationListener`, uma implementação do `ICommunicationListener`, que permite que você crie um WebHost de Núcleo do ASP.NET em um Reliable Service usando o HttpSys como o servidor Web.

O HttpSys se baseia na [API do Windows HTTP Server](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Isso usa o driver de kernel *http.sys* usado pelo IIS para processar solicitações HTTP e roteá-las para processos que executam os aplicativos Web. Isso permite que vários processos na mesma máquina física ou virtual hospedem aplicativos Web na mesma porta, sem ambiguidade graças a um caminho de URL ou nome do host exclusivo. Esses recursos são úteis no Service Fabric para hospedar vários sites no mesmo cluster.

>[!NOTE]
>A implementação do HttpSys funciona apenas na plataforma Windows.

O diagrama a seguir ilustra como o HttpSys usa o driver de kernel *http.sys* no Windows para o compartilhamento de porta:

![http.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HttpSys em um serviço sem estado
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

### <a name="httpsys-in-a-stateful-service"></a>HttpSys em um serviço com estado

O `HttpSysCommunicationListener` no momento não é projetado para uso em serviços com estado, devido a complicações com o recurso de compartilhamento de porta subjacente, *http.sys*. Para obter mais informações, consulte a seção a seguir sobre a alocação de porta dinâmica com o HttpSys. Para serviços com estado, o Kestrel é o servidor Web recomendado.

### <a name="endpoint-configuration"></a>Configuração de ponto de extremidade

Uma configuração `Endpoint` é necessária para servidores Web que usam a API do Windows HTTP Server, incluindo o HttpSys. Servidores Web que usam a API do Windows HTTP Server primeiro devem reservar a URL com *http.sys* (isso normalmente é feito com a ferramenta [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx)). Esta ação exige privilégios elevados que seus serviços não têm por padrão. As opções "http" ou "https" para a propriedade `Protocol` da configuração `Endpoint` em *ServiceManifest.xml* são usadas especificamente para instruir o tempo de execução do Service Fabric a registrar uma URL com *http.sys* em seu nome usando o prefixo de URL [*curinga forte*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx).

Por exemplo, para reservar `http://+:80` para um serviço, a seguinte configuração deve ser usada em ServiceManifest.XML:

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

#### <a name="use-httpsys-with-a-static-port"></a>Usar HttpSys com uma porta estática
Para usar uma porta estática com o HttpSys, forneça o número da porta na configuração `Endpoint`:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Usar HttpSys com uma porta dinâmica
Para usar uma porta atribuída dinamicamente com o HttpSys, omita a propriedade `Port` na configuração `Endpoint`:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Uma porta dinâmica alocada por uma configuração `Endpoint` fornece apenas uma porta *por processo de host*. O modelo de hospedagem do Service Fabric atual permite que várias instâncias de serviço e/ou réplicas sejam hospedadas no mesmo processo, o que significa que cada uma delas compartilhará a mesma porta quando for alocada por meio da configuração `Endpoint`. Várias instâncias do HttpSys podem compartilhar uma porta usando o recurso subjacente de compartilhamento de porta *http.sys*, mas não há suporte para isso no `HttpSysCommunicationListener` devido às complicações introduzidas para solicitações do cliente. Para o uso de portas dinâmicas, o Kestrel é o servidor Web recomendado.

## <a name="kestrel-in-reliable-services"></a>Kestrel em Serviços Confiáveis
O Kestrel pode ser usado em um Serviço Confiável importando o pacote **Microsoft.ServiceFabric.AspNetCore.Kestrel** do NuGet. Esse pacote contém o `KestrelCommunicationListener`, uma implementação do `ICommunicationListener`, que permite que você crie um WebHost de Núcleo do ASP.NET em um serviço confiável usando o Kestrel como o servidor Web.

O Kestrel é um servidor Web de plataforma cruzada para o Núcleo do ASP.NET com base em libuv, uma biblioteca de E/S assíncrona de plataforma cruzada. Diferentemente do HttpSys, o Kestrel não usa um gerenciador de ponto de extremidade centralizado como *http.sys*. Diferentemente do HttpSys, Kestrel não dá suporte ao compartilhamento de porta entre vários processos. Cada instância do Kestrel deve usar uma porta exclusiva.

![kestrel][4]

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

Neste exemplo, uma instância única de `IReliableStateManager` é fornecida ao contêiner de injeção de dependência do WebHost. Isso não é estritamente necessário, mas permite que você use `IReliableStateManager` e Coleções Confiáveis em seus métodos de ação do controlador MVC.

Um nome de configuração `Endpoint` **não** é fornecido para `KestrelCommunicationListener` em um serviço com estado. Isso será explicado mais detalhadamente na seção a seguir.

### <a name="configure-kestrel-to-use-https"></a>Configurar o Kestrel para usar HTTPS
Ao habilitar o HTTPS com o Kestrel no seu serviço, você precisará definir várias opções de escuta.  Atualize o `ServiceInstanceListener` para usar um ponto de extremidade EndpointHttps e escutar uma porta específica (como a porta 443). Ao configurar o host da Web para usar o servidor Kestrel, será preciso configurar o Kestrel para escutar endereços IPv6 em todos os adaptadores de rede: 

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

Para ver um exemplo completo usado em um tutorial, confira [Configurar o Kestrel para usar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Configuração de ponto de extremidade
Uma configuração `Endpoint` não é necessária para usar o Kestrel. 

O Kestrel é um servidor Web autônomo simples. Diferentemente do HttpSys (ou HttpListener), ele não precisa de uma configuração `Endpoint` em *ServiceManifest.xml* porque não requer o registro de URL antes de iniciar. 

#### <a name="use-kestrel-with-a-static-port"></a>Usar Kestrel com uma porta estática
Uma porta estática pode ser configurada na configuração do `Endpoint` de ServiceManifest.XML para uso com Kestrel. Embora não seja estritamente necessário, há dois benefícios potenciais:
 1. Se a porta não estiver no intervalo de portas do aplicativo, ela será aberta por meio do firewall do sistema operacional pelo Service Fabric.
 2. A URL fornecida através de `KestrelCommunicationListener` usará essa porta.

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

Se uma configuração `Endpoint` não for usada, omita o nome no construtor `KestrelCommunicationListener`. Nesse caso, uma porta dinâmica será usada. Para obter mais informações, confira a próxima seção.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Usar Kestrel com uma porta dinâmica
O Kestrel não pode usar a atribuição automática de porta da configuração `Endpoint` em ServiceManifest.XML, pois a atribuição automática de porta de uma configuração `Endpoint` atribui uma porta exclusiva por *processo de host*, e um processo de host único pode conter várias instâncias do Kestrel. Como o Kestrel não dá suporte ao compartilhamento de porta, isso não funciona, pois cada instância do Kestrel deve ser aberta em uma porta exclusiva.

Para usar a atribuição de porta dinâmica com Kestrel, omita inteiramente a configuração `Endpoint` no ServiceManifest.xml e não passe um nome de ponto de extremidade para o construtor `KestrelCommunicationListener`:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Nessa configuração, `KestrelCommunicationListener` selecionará automaticamente uma porta não utilizada do intervalo de portas de aplicativo.

## <a name="service-fabric-configuration-provider"></a>Provedor de configuração do Service Fabric
Configuração de aplicativo no ASP.NET Core é com base nos pares de chave-valor estabelecidas por provedores de configuração, leia [configuração no ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/) para entender mais sobre ASP.NET Core configuração suporte geral.

Esta seção descreve o provedor de configuração do serviço do Fabric para integrar com a configuração do ASP.NET Core, importando o `Microsoft.ServiceFabric.AspNetCore.Configuration` pacote do NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Extensões de inicialização AddServiceFabricConfiguration
Depois que você importar `Microsoft.ServiceFabric.AspNetCore.Configuration` pacote do NuGet, você precisa registrar a origem de configuração do Service Fabric com a API de configuração do ASP.NET Core por **AddServiceFabricConfiguration** extensões no `Microsoft.ServiceFabric.AspNetCore.Configuration` namespace em relação a `IConfigurationBuilder`

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

Agora, o serviço ASP.NET Core pode acessar as definições de configuração assim como outras configurações de aplicativo do Service Fabric. Por exemplo, você pode usar o padrão de opções para carregar configurações em objetos fortemente tipados.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Mapeamento de chave padrão
Por padrão, o provedor de configuração do Service Fabric inclui o nome do pacote, o nome da seção e o nome da propriedade para formar a configuração do asp.net core usando a seguinte função da chave:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Por exemplo, se você tiver um pacotes de configuração chamado `MyConfigPackage` com o conteúdo, abaixo, em seguida, o valor de configuração estarão disponível no ASP.NET Core `IConfiguration` por meio da chave *MyConfigPackage:MyConfigSection:MyParameter*
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opções de configuração do Service Fabric
Provedor de configuração do Service Fabric também dá suporte a `ServiceFabricConfigurationOptions` para alterar o comportamento padrão de mapeamento de chaves.

#### <a name="encrypted-settings"></a>Configurações criptografadas
Service Fabric dá suporte para criptografar as configurações, o provedor de configuração do Service Fabric também dá suporte a isso. A seguir seguro pelo princípio do padrão, o descrypted de are't configurações criptografadas por padrão para o ASP.NET Core `IConfiguration`, o valor criptografado são armazenados ali em vez disso. No entanto, se você quiser descriptografar o valor a ser armazenado no ASP.NET Core IConfiguration você pode definir DecryptValue sinalizador como false no `AddServiceFabricConfiguration` extensões da seguinte maneira:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = true); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Vários pacotes de configuração
O Service Fabric dá suporte a vários pacotes de configuração. Por padrão, o nome do pacote está incluído na configuração da chave. Você pode definir o `IncludePackageName` sinalizador para alterar o comportamento padrão.
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Mapeamento de chaves personalizado, extração de valor e preenchimento dos dados
Além de acima 2 sinalizadores para alterar o comportamento padrão, provedor de configuração do Service Fabric também dá suporte a cenários mais avançados de para personalizar o mapeamento de chaves por meio `ExtractKeyFunc` e como personalizado, extraia os valores por meio de `ExtractValueFunc`. Você poderia até alterar todo o processo para preencher os dados de configuração do Service Fabric para a configuração do ASP.NET Core por meio de `ConfigAction`.

Os exemplos a seguir ilustram para usar `ConfigAction` para personalizar o preenchimento dos dados.
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
### <a name="configuration-update"></a>Atualização da configuração
Provedor de configuração do Service Fabric também dá suporte à atualização de configuração e você pode usar o ASP.NET Core `IOptionsMonitor` para receber notificações de alteração e também com `IOptionsSnapshot` para recarregar os dados de configuração. Para obter mais informações, consulte [opções do ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options).

Isso é suportado por padrão e nenhuma outra codificação é necessária para habilitar a atualização de configuração.

## <a name="scenarios-and-configurations"></a>Cenários e configurações
Esta seção descreve os cenários a seguir e fornece a combinação recomendada de servidor Web, configuração de porta, opções de integração do Service Fabric e diversos parâmetros para obter um serviço corretamente funcional:
 - serviço sem estado do Núcleo do ASP.NET exposto externamente
 - Serviço do Núcleo do ASP.NET sem estado somente para uso interno
 - serviço com estado do Núcleo do ASP.NET somente interno

Um serviço **exposto externamente** é aquele que expõe um ponto de extremidade acessível de fora do cluster, geralmente por meio de um balanceador de carga.

Um serviço **somente interno** é aquele cujo ponto de extremidade só é acessível por meio do cluster.

> [!NOTE]
> Os pontos de extremidade de serviço com estado geralmente não devem ser expostos à Internet. Clusters que estiverem por trás de balanceadores de carga que não estiverem cientes da resolução de serviços do Service Fabric, como o Azure Load Balancer, não poderão expor serviços com estado, pois o balanceador de carga não poderá localizar e rotear o tráfego para a réplica de serviço com estado apropriada. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Serviços sem monitoração de estado do Núcleo do ASP.NET expostos externamente
O Kestrel é o servidor Web recomendado para serviços de front-end que expõem pontos de extremidade HTTP externos para a Internet. No Windows, o HttpSys pode ser usado para fornecer funcionalidade de compartilhamento de porta, que permite hospedar vários serviços Web no mesmo conjunto de nós usando a mesma porta, diferenciada por nome de host ou caminho, sem depender de um proxy ou gateway de front-end para fornecer roteamento HTTP.
 
Quando exposto à Internet, um serviço sem estado deve usar um ponto de extremidade conhecido e estável que possa ser acessado por meio de um balanceador de carga. Essa é a URL que você fornecerá aos usuários do aplicativo. Recomenda-se a seguinte configuração:

|  |  | **Observações** |
| --- | --- | --- |
| Servidor Web | Kestrel | O Kestrel é o servidor Web preferencial, pois ele tem suporte no Windows e no Linux. |
| Configuração de portas | estático | Uma porta estática conhecida deve ser configurada na configuração do `Endpoints` de ServiceManifest.XML, como 80 para HTTP ou 443 para HTTPS. |
| ServiceFabricIntegrationOptions | Nenhum | A opção `ServiceFabricIntegrationOptions.None` deve ser usada quando a configuração de middleware de integração do Service Fabric para o serviço não tentar validar as solicitações de entrada para um identificador exclusivo. Os usuários externos do aplicativo não saberão as informações de identificação exclusivas usadas pelo middleware. |
| Contagem de Instâncias | -1 | Em casos de uso típicos, a configuração de contagem de instâncias deve ser definida como "-1" para que uma instância esteja disponível em todos os nós que recebem o tráfego de um balanceador de carga. |

Se vários serviços expostos externamente compartilharem o mesmo conjunto de nós, o HttpSys poderá ser usado com um caminho de URL exclusivo, mas estável. Isso pode ser feito modificando a URL fornecida ao configurar IWebHost. Observe que isso se aplica somente ao HttpSys.

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
Os serviços sem monitoração de estado que são chamados apenas de dentro do cluster devem usar URLs exclusivas e portas atribuídas dinamicamente para garantir a cooperação entre vários serviços. Recomenda-se a seguinte configuração:

|  |  | **Observações** |
| --- | --- | --- |
| Servidor Web | Kestrel | Embora o HttpSys possa ser usado para serviços internos sem estado, o Kestrel é o servidor recomendado para permitir que várias instâncias do serviço compartilhem um host.  |
| Configuração de portas | atribuídas dinamicamente | Várias réplicas de um serviço com estado podem compartilhar um processo de host ou o sistema operacional do host e, assim, precisarão de portas exclusivas. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Com a atribuição dinâmica de portas essa configuração impede o problema de confusão de identidade descrito anteriormente. |
| InstanceCount | qualquer | A configuração de contagem de instâncias pode ser definida como qualquer valor necessário para operar o serviço. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Somente interno serviço com estado do Núcleo do ASP.NET
Os serviços com monitoração de estado que são chamados apenas de dentro do cluster devem usar portas atribuídas dinamicamente para garantir a cooperação entre vários serviços. Recomenda-se a seguinte configuração:

|  |  | **Observações** |
| --- | --- | --- |
| Servidor Web | Kestrel | O `HttpSysCommunicationListener` não foi projetado para uso pelos serviços com monitoração de estado em que réplicas compartilham um processo de host. |
| Configuração de portas | atribuídas dinamicamente | Várias réplicas de um serviço com estado podem compartilhar um processo de host ou o sistema operacional do host e, assim, precisarão de portas exclusivas. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Com a atribuição dinâmica de portas essa configuração impede o problema de confusão de identidade descrito anteriormente. |

## <a name="next-steps"></a>Próximas etapas
[Depurar seu aplicativo do Service Fabric usando o Visual Studio](service-fabric-debugging-your-application.md)

<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
