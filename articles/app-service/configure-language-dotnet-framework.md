---
title: Configurar aplicativos ASP.NET
description: Saiba como configurar um aplicativo ASP.NET no serviço Azure App. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 8ed6835583cc4881b19eee14ed392b193324535e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92744162"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>Configurar um aplicativo ASP.NET para o serviço Azure App

> [!NOTE]
> Para ASP.NET Core, consulte [configurar um aplicativo ASP.NET Core para Azure app serviço](configure-language-dotnetcore.md)

Os aplicativos ASP.NET devem ser implantados no serviço Azure App como binários compilados. A ferramenta de publicação do Visual Studio cria a solução e implanta os binários compilados diretamente, enquanto o mecanismo de implantação do serviço de aplicativo implanta o repositório de código primeiro e, em seguida, compila os binários.

Este guia fornece os principais conceitos e instruções para desenvolvedores de ASP.NET. Se você nunca usou Azure App serviço, siga o tutorial [ASP.net início rápido](quickstart-dotnet-framework.md) e [ASP.NET com Banco de dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md) primeiro.

## <a name="show-supported-net-framework-runtime-versions"></a>Mostrar versões de tempo de execução .NET Framework com suporte

No serviço de aplicativo, as instâncias do Windows já têm todas as versões do .NET Framework com suporte instaladas. Para mostrar as versões do .NET Framework Runtime e do SDK disponíveis para você, navegue até `https://<app-name>.scm.azurewebsites.net/DebugConsole` e execute o comando apropriado no console baseado em navegador:

Para versões de tempo de execução do CLR 4 (.NET Framework 4 e acima):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

A versão mais recente do .NET Framework pode não estar disponível imediatamente.

Para versões de tempo de execução do CLR 2 (.NET Framework 3,5 e inferior):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>Mostrar versão atual do .NET Framework Runtime

Execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

Um valor de `v4.0` significa que a versão mais recente do CLR 4 (.NET Framework 4. x) é usada. Um valor de `v2.0` significa que uma versão CLR 2 (.NET Framework 3,5) é usada.

## <a name="set-net-framework-runtime-version"></a>Definir .NET Framework versão de tempo de execução

Por padrão, o serviço de aplicativo usa a versão de .NET Framework mais recente com suporte para executar o aplicativo ASP.NET. Para executar seu aplicativo usando .NET Framework 3,5 em vez disso, execute o seguinte comando na [Cloud Shell](https://shell.azure.com) (v 2.0 significa CLR 2):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a>Acessar variáveis de ambiente

No serviço de aplicativo, você pode [definir configurações de aplicativo](configure-common.md#configure-app-settings) e cadeias de conexão fora do seu código do aplicativo. Em seguida, você pode acessá-los em qualquer classe usando o padrão ASP.NET padrão:

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

Se você definir uma configuração de aplicativo com o mesmo nome no serviço de aplicativo e no *web.config*, o valor do serviço de aplicativo terá precedência sobre o valor de *web.config* . O valor *web.config* local permite que você depure o aplicativo localmente, mas o valor do serviço de aplicativo permite que você execute o aplicativo no produto com as configurações de produção. As cadeias de conexão funcionam da mesma maneira. Dessa forma, você pode manter os segredos do aplicativo fora do seu repositório de código e acessar os valores apropriados sem alterar seu código.

## <a name="deploy-multi-project-solutions"></a>Implantar soluções de vários projetos

Quando uma solução do Visual Studio inclui vários projetos, o processo de publicação do Visual Studio já inclui a seleção do projeto a ser implantado. Quando você implanta no mecanismo de implantação do serviço de aplicativo, como com o Git ou com a implantação de ZIP, com a automação de compilação ativada, o mecanismo de implantação do serviço de aplicativo escolhe o primeiro site da Web ou o projeto de aplicativo Web que ele encontra como o aplicativo do serviço de aplicativo. Você pode especificar qual serviço de aplicativo do Project deve usar especificando a `PROJECT` configuração do aplicativo. Por exemplo, execute o seguinte no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>Página obter exceções detalhadas

Quando seu aplicativo ASP.NET gera uma exceção no depurador do Visual Studio, o navegador exibe uma página de exceção detalhada, mas no serviço de aplicativo que a página é substituída por uma mensagem de erro genérica. Para exibir a página de exceção detalhada no serviço de aplicativo, abra o arquivo *Web.config* e adicione o `<customErrors mode="Off"/>` elemento sob o `<system.web>` elemento. Por exemplo:

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

Reimplante seu aplicativo com o *Web.config* atualizado. Agora você deve ver a mesma página de exceção detalhada.

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

Você pode adicionar mensagens de diagnóstico em seu código de aplicativo usando [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace). Por exemplo: 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: criar um aplicativo ASP.NET no Azure com Banco de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)
