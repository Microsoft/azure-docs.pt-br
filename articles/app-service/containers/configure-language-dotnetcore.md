---
title: Configurar aplicativos do ASP.NET Core - serviço de aplicativo do Azure | Microsoft Docs
description: Saiba como configurar aplicativos do ASP.NET Core para trabalhar no serviço de aplicativo do Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: e203877b2bc939c1d7fb9390df39f3e2451d12d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852305"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Configurar uma Linux aplicativo ASP.NET Core para o serviço de aplicativo do Azure

Aplicativos ASP.NET Core devem ser implantados como binários compilados. A ferramenta de publicação do Visual Studio compila a solução e, em seguida, implanta os binários compilados diretamente, enquanto que o mecanismo de implantação do serviço de aplicativo implanta o repositório de código primeiro e, em seguida, compila os binários.

Este guia fornece instruções para o ASP.NET Core e conceitos principais desenvolvedores que usam um contêiner interno do Linux no serviço de aplicativo. Se você nunca usou o serviço de aplicativo do Azure, siga as [início rápido do ASP.NET Core](quickstart-dotnetcore.md) e [ASP.NET Core com o tutorial do banco de dados SQL](tutorial-dotnetcore-sqldb-app.md) primeiro.

## <a name="show-net-core-version"></a>Mostrar a versão do .NET Core

Para mostrar a versão atual do .NET Core, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões do .NET Core, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Defina a versão do .NET Core

Execute o seguinte comando [Cloud Shell](https://shell.azure.com) para definir a versão do .NET Core para o 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Acessar variáveis de ambiente

No Serviço de Aplicativo, você pode [definir configurações de aplicativo](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) fora do código do aplicativo. Em seguida, você pode acessá-los usando o padrão do ASP.NET:

```csharp
include Microsoft.Extensions.Configuration;
// retrieve App Service app setting
System.Configuration.ConfigurationManager.AppSettings["MySetting"]
// retrieve App Service connection string
Configuration.GetConnectionString("MyDbConnection")
```

Se você configurar uma configuração de aplicativo com o mesmo nome no serviço de aplicativo e, na *Web. config*, o valor do serviço de aplicativo tem precedência sobre o valor de Web. config. O valor de Web. config permite que você depure o aplicativo localmente, mas o valor do serviço de aplicativo permite que seu tempo de execução do aplicativo no produto com as configurações de produção. Cadeias de caracteres de Conexão funcionam da mesma maneira. Dessa forma, você pode manter seus segredos de aplicativo fora do seu repositório de código e acessar os valores apropriados sem alterar seu código.

## <a name="get-detailed-exceptions-page"></a>Acessar a página de exceções detalhadas

Quando seu aplicativo ASP.NET gera uma exceção no depurador do Visual Studio, o navegador exibirá uma página de exceção detalhadas, mas no serviço de aplicativo nessa página é substituída por um genérico **HTTP 500** erro ou **um erro ocorreu enquanto processando sua solicitação.** mensagem. Para exibir a página de exceção detalhadas no serviço de aplicativo, adicione a `ASPNETCORE_ENVIRONMENT` configuração do aplicativo ao seu aplicativo, executando o seguinte comando em de <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Detectar sessão HTTPS

No Serviço de Aplicativo, a [Terminação SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos balanceadores de carga de rede de modo que todas as solicitações HTTPS cheguem ao seu aplicativo como solicitações HTTP não criptografadas. Se sua lógica de aplicativo precisa saber se o usuário solicita são criptografados ou não, configure o Middleware de cabeçalhos encaminhados no *Startup.cs*:

- Configure o middleware com [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) para encaminhar a `X-Forwarded-For` e `X-Forwarded-Proto` cabeçalhos no `Startup.ConfigureServices`.
- Adicione intervalos de endereços IP privados para redes conhecidas, para que o middleware pode confiar o balanceador de carga do serviço de aplicativo.
- Invocar o [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) método na `Startup.Configure` antes de chamar outros middlewares.

Reunindo todos os três elementos, o seu código se parece com o exemplo a seguir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
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

Para obter mais informações, consulte [configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Implantar soluções multiprojeto

Quando você implanta um repositório do ASP.NET para o mecanismo de implantação com um *. csproj* arquivo no diretório raiz, o mecanismo implanta o projeto. Quando você implanta um repositório do ASP.NET com um *. sln* arquivo no diretório raiz, o mecanismo seleciona o primeiro Site ou projeto de aplicativo Web encontra-se que o aplicativo de serviço de aplicativo. É possível para o mecanismo não escolher o projeto desejado.

Para implantar uma solução multiprojeto, você pode especificar o projeto para usar no serviço de aplicativo de duas maneiras diferentes:

### <a name="using-deployment-file"></a>Usando o arquivo. Deployment

Adicionar um *. Deployment* arquivo para a raiz do repositório e adicione o código a seguir:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Usando configurações do aplicativo

No <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>, adicione uma configuração de aplicativo ao seu aplicativo de serviço de aplicativo, executando o seguinte comando CLI. Substitua  *\<app-name >*,  *\<resource-group-name >*, e  *\<nome do projeto >* com os valores apropriados .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abra a sessão SSH aberta no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo ASP.NET Core com o Banco de Dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o Serviço de Aplicativo no Linux](app-service-linux-faq.md)