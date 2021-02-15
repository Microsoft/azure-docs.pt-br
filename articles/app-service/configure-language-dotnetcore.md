---
title: Configurar aplicativos ASP.NET Core
description: Saiba como configurar um aplicativo ASP.NET Core nas instâncias nativas do Windows ou em um contêiner do Linux predefinido, no serviço Azure App. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 1223ff5c56d3c7d58b324d2099980bc0b5408125
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655961"
---
# <a name="configure-an-aspnet-core-app-for-azure-app-service"></a>Configurar um aplicativo de ASP.NET Core para o serviço Azure App

> [!NOTE]
> Para ASP.NET em .NET Framework, consulte [configurar um aplicativo ASP.net para o serviço Azure app](configure-language-dotnet-framework.md)

ASP.NET Core aplicativos devem ser implantados no serviço Azure App como binários compilados. A ferramenta de publicação do Visual Studio cria a solução e implanta os binários compilados diretamente, enquanto o mecanismo de implantação do serviço de aplicativo implanta o repositório de código primeiro e, em seguida, compila os binários.

Este guia fornece os principais conceitos e instruções para os desenvolvedores de ASP.NET Core. Se você nunca usou Azure App serviço, siga o tutorial [ASP.NET Core início rápido](quickstart-dotnetcore.md) e [ASP.NET Core com Banco de dados SQL](tutorial-dotnetcore-sqldb-app.md) primeiro.

::: zone pivot="platform-windows"  

## <a name="show-supported-net-core-runtime-versions"></a>Mostrar versões de tempo de execução do .NET Core com suporte

No serviço de aplicativo, as instâncias do Windows já têm todas as versões do .NET Core com suporte instaladas. Para mostrar o tempo de execução do .NET Core e as versões do SDK disponíveis para você, navegue até `https://<app-name>.scm.azurewebsites.net/DebugConsole` e execute o seguinte comando no console baseado em navegador:

```azurecli-interactive
dotnet --info
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="show-net-core-version"></a>Mostrar versão do .NET Core

Para mostrar a versão atual do .NET Core, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões do .NET Core com suporte, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

::: zone-end

## <a name="set-net-core-version"></a>Definir versão do .NET Core

::: zone pivot="platform-windows"  

Defina a estrutura de destino no arquivo de projeto para seu projeto ASP.NET Core. Para obter mais informações, consulte [selecionar a versão do .NET Core a ser usada](/dotnet/core/versions/selection) na documentação do .NET Core.

::: zone-end

::: zone pivot="platform-linux"

Execute o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir a versão do .NET Core como 3,1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|3.1"
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Personalizar a automação de build

Se você implantar seu aplicativo usando pacotes Git ou zip com a automação de build ativada, a automação de build do Serviço de Aplicativo percorrerá a seguinte sequência:

1. Executar script personalizado se especificado por `PRE_BUILD_SCRIPT_PATH`.
1. Execute `dotnet restore` para restaurar as dependências do NuGet.
1. Execute `dotnet publish` para criar um binário para produção.
1. Executar script personalizado se especificado por `POST_BUILD_SCRIPT_PATH`.

`PRE_BUILD_COMMAND` e `POST_BUILD_COMMAND` são variáveis de ambiente vazias por padrão. Para executar comandos pré-build, defina `PRE_BUILD_COMMAND`. Para executar comandos pós-build, defina `POST_BUILD_COMMAND`.

O exemplo a seguir especifica as duas variáveis para uma série de comandos, separados por vírgulas.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Para obter variáveis de ambiente adicionais para personalizar a automação de build, confira [Configuração do Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Para obter mais informações sobre como o serviço de aplicativo é executado e compila ASP.NET Core aplicativos no Linux, consulte [a documentação do Oryx: como os aplicativos .NET Core são detectados e compilados](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

::: zone-end

## <a name="access-environment-variables"></a>Acessar variáveis de ambiente

No Serviço de Aplicativo, você pode [definir configurações de aplicativo](configure-common.md#configure-app-settings) fora do código do aplicativo. Em seguida, você pode acessá-los em qualquer classe usando o padrão de injeção de dependência de ASP.NET Core padrão:

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Se você definir uma configuração de aplicativo com o mesmo nome no serviço de aplicativo e no *appsettings.jsem*, por exemplo, o valor do serviço de aplicativo terá precedência sobre a *appsettings.jsno* valor. O *appsettings.jslocal no* valor permite que você depure o aplicativo localmente, mas o valor do serviço de aplicativo permite que você execute o aplicativo no produto com as configurações de produção. As cadeias de conexão funcionam da mesma maneira. Dessa forma, você pode manter os segredos do aplicativo fora do seu repositório de código e acessar os valores apropriados sem alterar seu código.

> [!NOTE]
> Observe que os [dados de configuração hierárquica](/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) no *appsettings.jsem* é acessado usando o `:` delimitador padrão para o .NET Core. Para substituir uma definição de configuração hierárquica específica no serviço de aplicativo, defina o nome da configuração do aplicativo com o mesmo formato delimitado na chave. Você pode executar o exemplo a seguir no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Implantar soluções de vários projetos

Quando uma solução do Visual Studio inclui vários projetos, o processo de publicação do Visual Studio já inclui a seleção do projeto a ser implantado. Quando você implanta no mecanismo de implantação do serviço de aplicativo, como com o Git ou com a implantação de ZIP, com a automação de compilação ativada, o mecanismo de implantação do serviço de aplicativo escolhe o primeiro site da Web ou o projeto de aplicativo Web que ele encontra como o aplicativo do serviço de aplicativo. Você pode especificar qual serviço de aplicativo do Project deve usar especificando a `PROJECT` configuração do aplicativo. Por exemplo, execute o seguinte no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

ASP.NET Core fornece um [provedor de registro em log interno para o serviço de aplicativo](/aspnet/core/fundamentals/logging/#azure-app-service). Em *Program.cs* do seu projeto, adicione o provedor ao seu aplicativo por meio do `ConfigureLogging` método de extensão, conforme mostrado no exemplo a seguir:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Em seguida, você pode configurar e gerar logs com o [padrão .NET Core](/aspnet/core/fundamentals/logging)padrão.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Para obter mais informações sobre como solucionar problemas de aplicativos ASP.NET Core no serviço de aplicativo, consulte [solucionar problemas ASP.NET Core no serviço Azure app e IIS](/aspnet/core/test/troubleshoot-azure-iis)

## <a name="get-detailed-exceptions-page"></a>Página obter exceções detalhadas

Quando seu aplicativo ASP.NET Core gera uma exceção no depurador do Visual Studio, o navegador exibe uma página de exceção detalhada, mas no serviço de aplicativo essa página é substituída por um erro genérico **HTTP 500** ou **ocorreu um erro ao processar sua solicitação.** . Para exibir a página de exceção detalhada no serviço de aplicativo, adicione a `ASPNETCORE_ENVIRONMENT` configuração do aplicativo ao seu aplicativo executando o comando a seguir no <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Detectar sessão HTTPS

No Serviço de Aplicativo, a [Terminação SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos balanceadores de carga de rede de modo que todas as solicitações HTTPS cheguem ao seu aplicativo como solicitações HTTP não criptografadas. Se a lógica do aplicativo precisar saber se as solicitações do usuário estão criptografadas ou não, configure o middleware de cabeçalhos encaminhados em *Startup.cs*:

- Configure o middleware com [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` em `Startup.ConfigureServices`.
- Adicione intervalos de endereços IP privados às redes conhecidas, para que o middleware possa confiar no balanceador de carga do serviço de aplicativo.
- Invoque o método [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) no `Startup.Configure` antes de chamar outro middleware.

Colocando todos os três elementos juntos, seu código é semelhante ao exemplo a seguir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        // These three subnets encapsulate the applicable Azure subnets. At the moment, it's not possible to narrow it down further.
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](/aspnet/core/host-and-deploy/proxy-load-balancer).

::: zone pivot="platform-linux"

## <a name="open-ssh-session-in-browser"></a>Abra a sessão SSH aberta no navegador

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo ASP.NET Core com o Banco de Dados SQL](tutorial-dotnetcore-sqldb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o Serviço de Aplicativo no Linux](faq-app-service-linux.md)

::: zone-end
