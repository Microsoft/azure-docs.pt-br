---
title: Remover Application Insights no Visual Studio-Azure Monitor
description: Como remover Application Insights SDK para ASP.NET e ASP.NET Core no Visual Studio.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1d70413fa6a47e2d41693db6eb705f31b2a2b1b2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704303"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Como remover Application Insights no Visual Studio

Este artigo mostrará como remover o SDK do ASP.NET e do ASP.NET Core Application Insights no Visual Studio.

Para remover Application Insights, você precisará remover os pacotes NuGet e as referências da API em seu aplicativo. Você pode desinstalar os pacotes NuGet usando o console do Gerenciamento de Pacotes ou gerenciar a solução NuGet no Visual Studio. As seções a seguir mostrarão duas maneiras de remover pacotes NuGet e o que foi adicionado automaticamente ao seu projeto. Certifique-se de confirmar os arquivos adicionados e as áreas com em seu próprio código em que você fez chamadas para a API são removidas.

## <a name="uninstall-using-the-package-management-console"></a>Desinstalar usando o console do Gerenciamento de Pacotes

# <a name="net"></a>[.NET](#tab/net)

1. Para abrir o console do Gerenciamento de Pacotes, no menu superior, selecione Ferramentas > Gerenciador de pacotes NuGet > console do Gerenciador de pacotes.
     
    ![No menu superior, clique em ferramentas > Gerenciador de pacotes NuGet > console do Gerenciador de pacotes](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Se a coleta de rastreamento estiver habilitada, primeiro você precisará desinstalar Microsoft. ApplicationInsights. TraceListener. Insira `Uninstall-package Microsoft.ApplicationInsights.TraceListener` , em seguida, siga a etapa abaixo para remover Microsoft. ApplicationInsights. Web.

1. Digite o seguinte comando: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    Depois de inserir o comando, o pacote de Application Insights e todas as suas dependências serão desinstalados do projeto.
    
    ![Inserir comando no console](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Para abrir o console do Gerenciamento de Pacotes, no menu superior, selecione Ferramentas > Gerenciador de pacotes NuGet > console do Gerenciador de pacotes.

    ![No menu superior, clique em ferramentas > Gerenciador de pacotes NuGet > console do Gerenciador de pacotes](./media/remove-application-insights/package-manager.png)

1. Digite o seguinte comando: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    Depois de inserir o comando, o pacote de Application Insights e todas as suas dependências serão desinstalados do projeto.

---

## <a name="uninstall-using-the-visual-studio-nuget-ui"></a>Desinstalar usando a interface do usuário do NuGet do Visual Studio

# <a name="net"></a>[.NET](#tab/net)

1. Na *Gerenciador de soluções* à direita, clique com o botão direito do mouse em **solução** e selecione **gerenciar pacotes NuGet para solução**.

    Em seguida, você verá uma tela que permite editar todos os pacotes NuGet que fazem parte do projeto.
    
     ![Clique com o botão direito do mouse em solução, na Gerenciador de Soluções e selecione gerenciar pacotes NuGet para a solução](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Se a coleta de rastreamento estiver habilitada, primeiro você precisará desinstalar Microsoft. ApplicationInsights. TraceListener sem remover dependências selecionadas e, em seguida, seguir as etapas abaixo para desinstalar Microsoft. ApplicationInsights. Web com remover dependências selecionadas.

1. Clique no pacote **Microsoft. ApplicationInsights. Web** . À direita, marque a caixa de seleção ao lado de **projeto** para selecionar todos os projetos.

1. Para remover todas as dependências ao desinstalar o, selecione o botão suspenso **Opções** abaixo da seção em que você selecionou projeto.

    Em *Opções de desinstalação*, marque a caixa de seleção ao lado de *remover dependências*.

1. Selecione **Desinstalar**.
    
    ![Captura de tela mostra a janela Microsoft. ApplicationInsights. Web com remover dependências marcadas e desinstalação realçadas.](./media/remove-application-insights/uninstall-framework.png)

    Será exibida uma caixa de diálogo mostrando todas as dependências a serem removidas do aplicativo. Selecione **OK** para desinstalar.
    
    ![Captura de tela mostra uma caixa de diálogo com as dependências a serem removidas.](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  Depois que tudo for desinstalado, você ainda poderá ver "ApplicationInsights.config" e "AiHandleErrorAttribute.cs" na *Gerenciador de soluções*. Você pode excluir os dois arquivos manualmente.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Na *Gerenciador de soluções* à direita, clique com o botão direito do mouse em **solução** e selecione **gerenciar pacotes NuGet para solução**.

    Em seguida, você verá uma tela que permite editar todos os pacotes NuGet que fazem parte do projeto.

    ![Clique com o botão direito do mouse em solução, na Gerenciador de Soluções e selecione gerenciar pacotes NuGet para a solução](./media/remove-application-insights/manage-nuget-core.png)

1. Clique no pacote "Microsoft. ApplicationInsights. AspNetCore". À direita, marque a caixa de seleção ao lado de *projeto* para selecionar todos os projetos e selecione **desinstalar**.

    ![Marque remover dependências e desinstalar](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>O que é criado quando você adiciona Application Insights

Quando você adiciona Application Insights ao seu projeto, ele cria arquivos e adiciona código a alguns dos seus arquivos. A desinstalação exclusiva dos pacotes NuGet nem sempre descartará os arquivos e o código. Para remover completamente Application Insights, você deve verificar e excluir manualmente o código ou os arquivos adicionados juntamente com as chamadas à API que você adicionou em seu projeto.

# <a name="net"></a>[.NET](#tab/net)

Quando você adiciona Application Insights Telemetry a um projeto ASP.NET do Visual Studio, ele adiciona os seguintes arquivos:

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

As seguintes partes de código são adicionadas:

- [Nome do seu projeto]. csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Packages.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout. cshtml

    Se o seu projeto tiver um arquivo layout. cshtml, o código a seguir será adicionado.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.json

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig.cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Quando você adiciona Application Insights Telemetry a um projeto de modelo de ASP.NET Core do Visual Studio, ele adiciona o seguinte código:

- [Nome do seu projeto]. csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings.jsem:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.json
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>Próximas etapas

- [Azure Monitor](../overview.md)
