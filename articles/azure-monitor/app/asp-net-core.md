---
title: Azure Application Insights para ASP.NET Core | Microsoft Docs
description: Monitorar aplicativos web ASP.NET Core de disponibilidade, desempenho e uso.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: ae0d3658d9ae8534b1596fa7363495926cd0dfe7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693630"
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights para ASP.NET Core

Azure Application Insights fornece monitoramento detalhado do seu aplicativo web até o nível de código. Você pode monitorar facilmente o aplicativo web quanto à sua disponibilidade, desempenho e uso. Você também pode identificar e diagnosticar erros rapidamente em seu aplicativo sem esperar que um usuário os relate.

Este artigo o orienta pelo processo de criação de um exemplo do aplicativo ASP.NET Core [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) no Microsoft Visual Studio. Também mostra como iniciar o monitoramento usando o Application Insights.

## <a name="prerequisites"></a>Pré-requisitos

- SDK do .NET Core 2.0.0 ou posterior
- Versão do [Microsoft Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.7.3 ou superior com a carga de trabalho de desenvolvimento da Web e do ASP.NET

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Criar um projeto ASP.NET Core no Visual Studio

1. Clique com botão direito no **Microsoft Visual Studio 2017**e, em seguida, selecione **executar como administrador**.
2. Selecione **Arquivo** > **Novo** > **Projeto** (Ctrl-Shift-N).

   ![Captura de tela do novo menu do projeto do Microsoft Visual Studio](./media/asp-net-core/001-new-project.png)

3. Expanda **Visual C#**. Selecione **.NET Core** > **ASP.NET Core Aplicativo Web**. Insira um nome de projeto e um nome de solução e, em seguida, selecione **Criar novo repositório Git**.

   ![Captura de tela do assistente do novo projeto do Microsoft Visual Studio](./media/asp-net-core/002-asp-net-core-web-application.png)

4. Selecione **.NET Core** > **ASP.NET Core 2.0** **Aplicativo Web** > **OK**.

    ![Captura de tela da seleção do modelo do novo projeto do Microsoft Visual Studio](./media/asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Pesquisa do Application Insights

No Microsoft Visual Studio 2015 Atualização 2 ou superior com um projeto baseado em ASP.NET Core 2+, é possível aproveitar a [pesquisa do Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio) mesmo antes de adicionar explicitamente Insights de Aplicativo ao seu projeto.

Para testar essa funcionalidade:

1. Execute seu aplicativo. Para executar seu aplicativo, selecione o ícone **IIS Express** (![captura de tela do Microsoft Visual Studio ícone IIS Express](./media/asp-net-core/004-iis-express.png)).

2. Selecione **Exibir** > **Outras janelas** > **Pesquisa do Application Insights**.

   ![Captura de tela das Ferramentas de Diagnóstico do Microsoft Visual Studio](./media/asp-net-core/005-view-other-windows-search.png)

3. No momento, a telemetria da sessão de depuração está disponível apenas para análise local. Para habilitar completamente o Application Insights, selecione **Preparação para telemetria** no canto superior direito ou siga as etapas na próxima seção.

   ![Captura de tela da Pesquisa do Application Insights do Visual Studio](./media/asp-net-core/006-search.png)

> [!NOTE]
> Para saber mais sobre como os recursos de iluminação do Microsoft Visual Studio [Application Insights Search](../../azure-monitor/app/visual-studio.md) e [CodeLens](../../azure-monitor/app/visual-studio-codelens.md) localmente para o seu projeto ASP.NET Core, consulte [Application Insights Search continuação](#application-insights-search-continued).

## <a name="add-application-insights-telemetry"></a>Adicionar Application Insights Telemetry

1. Selecione **Projeto** > **Adicionar Application Insights Telemetry**. (Ou clique com o botão direito do mouse em **Serviços Conectados**, e selecione **Adicionar Serviço Conectado**.)

    ![Captura de tela do menu de seleção do novo projeto do Microsoft Visual Studio](./media/asp-net-core/007-project-add-telemetry.png)

2. Selecione **Introdução**. (Dependendo de sua versão do Visual Studio, o texto pode variar um pouco. Algumas versões mais antigas têm um botão **Iniciar gratuitamente**.)

    ![Captura de tela do botão de Início do Application Insights](./media/asp-net-core/008-get-started.png)

3. Selecione sua assinatura e, em seguida, selecione **Recurso** > **Registrar**.

## <a name="changes-made-to-your-project"></a>Alterações feitas ao seu projeto

Agora o Application Insights está com baixa sobrecarga. Para examinar as modificações feitas em seu projeto, adicionando a telemetria do Application Insights:

Selecione **Exibir** > **Team Explorer** (Ctrl +\, Ctrl + M) > **Projeto** > **Alterações**

- Total de quatro alterações são exibidas:

  ![Captura de tela de arquivos alterados, adicionando o Application Insights](./media/asp-net-core/009-changes.png)

- Um novo arquivo é criado:

  - _ConnectedService.json_

    ```json
    {
     "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
     "Version": "8.12.10405.1",
     "GettingStartedDocument": {
       "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
     }
    }
    ```

- Três arquivos são modificados: (comentários adicionais adicionados para realçar alterações):

  - _appsettings.json_:

    ```json
    {
      "Logging": {
        "IncludeScopes": false,
        "LogLevel": {
          "Default": "Warning"
        }
      },
    // Changes to file post adding Application Insights Telemetry:
      "ApplicationInsights": {
        "InstrumentationKey": "10101010-1010-1010-1010-101010101010"
      }
    }
    //
    ```

  - _ContosoDotNetCore.csproj_:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
      <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    <!--Changes to file post adding Application Insights Telemetry:-->
        <ApplicationInsightsResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsResourceId>
        <ApplicationInsightsAnnotationResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsAnnotationResourceId>
    <!---->
      </PropertyGroup>
      <ItemGroup>
    <!--Changes to file post adding Application Insights Telemetry:-->
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.1.1" />
    <!---->
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.8" />
      </ItemGroup>
      <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
      </ItemGroup>
    <!--Changes to file post adding Application Insights Telemetry:-->
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    <!---->
    </Project>
    ```

  -  _Program.cs_:

      ```csharp
      using System;
      using System.Collections.Generic;
      using System.IO;
      using System.Linq;
      using System.Threading.Tasks;
      using Microsoft.AspNetCore;
      using Microsoft.AspNetCore.Hosting;
      using Microsoft.Extensions.Configuration;
      using Microsoft.Extensions.Logging;

      namespace DotNetCore
      {
          public class Program
          {
              public static void Main(string[] args)
              {
                  BuildWebHost(args).Run();
              }

              public static IWebHost BuildWebHost(string[] args) =>
                  WebHost.CreateDefaultBuilder(args)
      // Change to file post adding Application Insights Telemetry:
                      .UseApplicationInsights()
      //
                      .UseStartup<Startup>()
                      .Build();
          }
      }
      ```

## <a name="send-ilogger-logs-to-application-insights"></a>Enviar logs de ILogger ao Application Insights

Application Insights oferece suporte à captura de logs enviados por meio de ILogger. Para configurar os exemplos de código de check-out de registro em log [aqui](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

## <a name="synthetic-transactions-with-powershell"></a>Transações sintéticas com o PowerShell

Para automatizar solicitações com relação ao seu aplicativo com transações sintéticas:

1. Para executar seu aplicativo, selecione o ![Captura de tela do ícone IIS Express do Visual Studio](./media/asp-net-core/004-iis-express.png) ícone.

2. Copie a URL da barra de endereços do navegador. A URL está no formato `http://localhost:<port number>`.

   ![Captura de tela da barra de endereço da URL na barra de endereço](./media/asp-net-core/0013-copy-url.png)

3. Execute o seguinte loop de PowerShell para criar 100 transações sintéticas usando seu aplicativo de teste. Modifique o número de porta após `localhost:` para corresponder com a URL que você copiou na etapa anterior. Por exemplo: 

   ```powershell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-the-application-insights-portal"></a>Abrir o portal do Application Insights

Após executar o PowerShell a partir da seção anterior, inicie o Application Insights para exibir as transações e confirmar que os dados estão sendo coletados. 

No menu do Visual Studio, selecione **Projeto** > **Application Insights** > **abrir Portal do Application Insights**.

   ![Captura de tela da Visão geral do Application Insights](./media/asp-net-core/010-portal.png)

> [!NOTE]
> A captura de tela de exemplo acima **Live Stream**, **Tempo de carregamento da exibição de página**, e **Solicitações com falha** não são coletados. A próxima seção explica as etapas para adicionar cada um deles. Se você já estiver coletando **Live Stream**, e **Tempo de carregamento da exibição de página**, siga as etapas de apenas de **Solicitações com falha**.

## <a name="collect-failed-requests-live-stream-and-page-view-load-time"></a>Coletar Solicitações com falha, Live Stream e o Tempo de carregamento da exibição de página

### <a name="failed-requests"></a>Solicitações com falha

Tecnicamente, solicitações com falha estão sendo coletadas, mas não ocorreu nenhuma ainda. Para acelerar o processo ao longo de uma exceção personalizada ao projeto existente para simular uma exceção do mundo real. Se seu aplicativo ainda estiver em execução no Microsoft Visual Studio antes de continuar **Interrompa a depuração** (Shift + F5).

1. No **Gerenciador de Soluções**, expanda **Páginas** > **About.cshtml**, em seguida, abra *About.cshtml.cs*.

   ![Captura de tela do Gerenciador de Soluções do Visual Studio](./media/asp-net-core/011-about.png)

2. Adicione uma exceção em ``Message=`` e salve a alteração no arquivo.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace DotNetCore.Pages
    {
        public class AboutModel : PageModel
        {
            public string Message { get; set; }

            public void OnGet()
            {
                Message = "Your application description page.";
                throw new Exception("Test Exception");
            }
        }
    }
    ```

### <a name="live-stream"></a>Live Stream

Para acessar a funcionalidade Live Stream do Application Insights com atualização do ASP.NET Core para os pacotes NuGet do Microsoft.ApplicationInsights.AspNetCore 2.2.0.

No Microsoft Visual Studio, selecione **Projeto** > **Gerenciar pacotes NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > (versão) **2.2.0** > **Atualização**.

  ![Captura de tela do Gerenciador de Pacotes NuGet](./media/asp-net-core/012-nuget-update.png)

Vários prompts de confirmação aparecerão. Leia e aceite se concordar com as alterações.

### <a name="page-view-load-time"></a>Tempo de Carregamento de Exibição de Página

1. No Microsfot Visual Studio, vá para **Gerenciador de Soluções** > **Páginas**. Você deve modificar dois arquivos: *Layout.cshtml* e *ViewImports.cshtml*.

2. Em *ViewImports.cshtml*, adicione este código:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. Em *layout. cshtml*, adicione o seguinte código antes da ``</head>`` marca e antes de quaisquer outros scripts:

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-and-live-stream"></a>Testar Solicitações com falha, Live Stream e o Tempo de carregamento da exibição de página

Para testar e confirmar que tudo está funcionando:

1. Execute seu aplicativo. Para executar seu aplicativo, selecione o ![Captura de tela do ícone IIS Express do Visual Studio](./media/asp-net-core/004-iis-express.png) ícone.

2. Vá até a página **Sobre** para disparar a exceção de teste. (Se você estiver no modo de depuração no Microsoft Visual Studio, selecione **Continuar** para a exceção ser exibida no Application Insights.)

3. Execute novamente o script de transação do PowerShell simulado que você usou anteriormente. (Talvez seja necessário ajustar o número da porta no script.)

4. Se a página **Visão geral** do Application Insights ainda não estiver aberta, no menu do Microsoft Visual Studio, selecione **Project** > **Application Insights**  >  **Abrir o Portal do Application Insights**. 

   > [!TIP]
   > Se você não vir seu novo tráfego, verifique o valor de **Intervalo de tempo**e, em seguida, selecione **Atualizar**.

   ![Captura de tela da janela de Visão geral](./media/asp-net-core/0019-overview-updated.png)

5. Selecionar **Live Stream**.

   ![Captura de tela de Live Metrics Stream](./media/asp-net-core/0020-live-metrics-stream.png)

   (Se o seu script do PowerShell ainda estiver em execução, você deverá ver métricas em tempo real. Se seu script do PowerShell interromper a execução, execute o script novamente com o Live Stream aberto)

## <a name="application-insights-sdk-comparison"></a>Comparação do SDK do Application Insights

O grupo de produto do Application Insights vem trabalhando duro para alcançar a paridade de recursos entre o [SDK do .NET Framework completo](https://github.com/Microsoft/ApplicationInsights-dotnet) e o SDK do .NET Core. A versão 2.2.0 do [SDK do ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore) para o Application Insights cobre amplamente a lacuna de recursos.

A tabela a seguir descreve mais das diferenças e compensações entre [.NET e .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server):

   | Comparação SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Live Metrics**      | **+** |**-** | **+** |
   | **Canal de Telemetria do Servidor** | **+** |**-** | **+**|
   |**Amostragem Adaptável**| **+** | **-** | **+**|
   | **Chamadas de Dependência SQL**     | **+** |**-** | **+**|
   | **Contadores de desempenho*** | **+** | **-**| **-**|

Contadores de Desempenho neste contexto refere-se a [contadores de desempenho do servidor](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) como processador, memória e utilização de disco.

## <a name="open-source-sdk"></a>SDK do código-fonte aberto
[Ler e contribuir para o código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="application-insights-search-continued"></a>Continuação da pesquisa do Application Insights

Esta seção pode ajudá-lo a entender melhor como pesquisa do Application Insights funciona no Microsoft Visual Studio para um projeto ASP.NET Core 2. Ele funciona dessa maneira, mesmo quando você explicitamente ainda não instalou os pacotes do NuGet do Application Insights ainda. Pode ser útil examinar a Saída de depuração.

Se você pesquisar a saída da palavra _insight_, serão realçados os resultados semelhantes aos seguintes estão destacados:

```DebugOutput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

Na saída, o CoreCLR carrega dois assemblies: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

A referência _desconfigurada_ em cada instância da telemetria do Application Insights indica que este aplicativo não está associado um ikey. Dados que são gerados enquanto o aplicativo é executado não serão enviados para o Azure. Os dados estão disponíveis apenas para análise e pesquisa local.

A funcionalidade é possível em parte porque o pacote NuGet _Microsoft.AspNetCore.All_ aceita como uma dependência [_Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1).

![Captura de tela do grafo de dependência do NuGet para Microsoft.AspNETCore.all](./media/asp-net-core/013-dependency.png)

Fora do Visual Studio, se você estava editando um projeto ASP.NET Core no VSCode ou outro editor, esses assemblies não carregados automaticamente durante a depuração se você ainda não adicionou explicitamente o Application Insights ao seu projeto.

No entanto, no Microsoft Visual Studio, esta iluminação de recursos locais do Application Insights de assemblies externos é realizada usando a [Interface IHostingStartup](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1). A interface adiciona dinamicamente o Application Insights durante a depuração.

Para saber mais sobre como aprimorar um aplicativo de um [assembly externo no ASP.NET Core com IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="disable-application-insights-in-visual-studio-net-core-projects"></a>Desabilitar o Application Insights em projetos .NET Core do Visual Studio

Embora a iluminação automática da funcionalidade de pesquisa do Application Insights possa ser útil para alguns, ver a telemetria de depuração gerada quando você não a estava esperando pode ser confuso.

Se desabilitar apenas a geração de telemetria for suficiente, será possível adicionar esse bloco de código ao **método de configuração** do seu arquivo _Startup.cs_:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

O CoreCLR ainda carregará _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ e _Microsoft.ApplicationInsights.AspNetCore.dll_, mas os arquivos não farão nada.

Se deseja desabilitar completamente o Application Insights em seu projeto .NET Core do Visual Studio, o método preferencial é selecionar **Ferramentas** > **Opções** > **Projetos e Soluções** > **Projetos Web**. Selecione **Desabilitar Insights de Aplicativo locais para projetos Web do Asp.Net Core** Essa funcionalidade foi adicionada no Visual Studio 15.6.

![Captura de tela da tela Projetos Web da Janela de Saída do Visual Studio](./media/asp-net-core/014-disable.png)

Se você estiver executando uma versão anterior do Microsoft Visual Studio e desejar remover completamente todos os assemblies carregados por meio da *IHostingStartup*,você terá duas opções:

* Adicionar `.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")` ao _Program.cs_:

  ```csharp
  using System;
  using System.Collections.Generic;
  using System.IO;
  using System.Linq;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore;
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.Configuration;
  using Microsoft.Extensions.Logging;

  namespace DotNetCore
  {
      public class Program
      {
          public static void Main(string[] args)
          {
              BuildWebHost(args).Run();
          }

          public static IWebHost BuildWebHost(string[] args) =>
              WebHost.CreateDefaultBuilder(args)
                  .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
                  .UseStartup<Startup>()
                  .Build();
      }
  }
  ```

* Adicionar ``"ASPNETCORE_preventHostingStartup": "True"`` às variável de ambiente _launchSettings.json_.

O problema com o uso de qualquer um desses métodos é que eles não desabilitam apenas o Application Insights. Os métodos também desabilitam qualquer coisa no Microsoft Visual Studio que estiver usando a funcionalidade *IHostingStartup*.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Próximas etapas
* [Explorar os Fluxos dos Usuários](../../azure-monitor/app/usage-flows.md) para entender como os usuários navegam por meio de seu aplicativo.
* [Configure a Coleção de Instantâneos](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) para ver o estado do código-fonte e as variáveis no momento em que uma exceção é lançada.
* [Use a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar seus próprios eventos e métricas para uma exibição mais detalhada do desempenho e do uso do aplicativo.
* Use os [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) para verificar seu aplicativo constante em todo o mundo.
