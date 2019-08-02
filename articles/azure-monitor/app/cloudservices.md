---
title: Application Insights para serviços de nuvem do Azure | Microsoft Docs
description: Monitorar suas funções da Web e de trabalho com eficiência com o Application Insights
services: application-insights
documentationcenter: ''
keywords: WAD2AI, Diagnóstico do Azure
author: mrbullwinkle
manager: carmonm
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.workload: tbd
ms.date: 09/05/2018
ms.author: mbullwin
ms.openlocfilehash: 64995ad0560efd06bfa0084c948527e8a01e1890
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "67443328"
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights para serviços de nuvem do Azure
[Application insights][start] pode monitorar os [aplicativos de serviço de nuvem do Azure](https://azure.microsoft.com/services/cloud-services/) quanto à disponibilidade, ao desempenho, às falhas e ao uso combinando dados de SDKs de Application Insights com dados de [diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) de seus serviços de nuvem. Com os comentários que você obtiver sobre o desempenho e a eficiência de seu aplicativo em uso, você pode fazer escolhas informadas sobre a direção do projeto em cada ciclo de vida de desenvolvimento.

![Visão geral do dashboard](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, você precisa de:

* Uma assinatura do [Azure](https://azure.com). Entrar com sua conta da Microsoft para Windows, Xbox Live ou outros serviços de nuvem da Microsoft. 
* Ferramentas do Microsoft Azure 2.9 ou posteriores.
* Developer Analytics Tools 7.10 ou posterior.

## <a name="get-started-quickly"></a>Introdução rápida
A maneira mais rápida e fácil de monitorar seu serviço de nuvem com o Application Insights é escolher essa opção quando você publica seu serviço no Azure.

![Exemplo da página Configurações de Diagnóstico](./media/cloudservices/azure-cloud-application-insights.png)

Esta opção prepara o aplicativo em tempo de execução, oferecendo a você toda a telemetria necessária para monitorar solicitações, exceções e dependências na função Web. Ela também monitora os contadores de desempenho a partir das funções de trabalho. Os rastreamentos de diagnóstico gerados pelo aplicativo também são enviados para o Application Insights.

Se esta opção for tudo o que você precisa, então você terminou. 

Suas próximas etapas estão [exibindo métricas do seu aplicativo](../../azure-monitor/app/metrics-explorer.md), [consultando seus dados com a análise](../../azure-monitor/app/analytics.md). 

Para monitorar o desempenho no navegador, talvez você queira configurar os [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) e [adicionar código às suas páginas da Web](../../azure-monitor/app/javascript.md).

As seções a seguir apresentam estas opções adicionais:

* Enviar dados de vários componentes e criar configurações para separar recursos.
* Adicione telemetria personalizada do seu aplicativo.

## <a name="sample-app-instrumented-with-application-insights"></a>Aplicativo de exemplo preparado com o Application Insights
Neste [aplicativo de exemplo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService), o Application Insights é adicionado a um serviço de nuvem com duas funções de trabalho hospedadas no Azure. 

Na seção seguinte, você aprenderá a adaptar seu próprio projeto de serviço de nuvem da mesma maneira.

## <a name="plan-resources-and-resource-groups"></a>Planejar recursos e grupos de recursos
A telemetria de seu aplicativo é armazenada, analisada e exibida em um recurso do Azure do tipo Application Insights. 

Cada recurso pertence somente a um grupo de recursos. Os grupos de recursos são usados para gerenciar custos, conceder acesso a membros da equipe e implantar atualizações em uma única transação coordenada. Por exemplo, é possível [escrever um script para implantar](../../azure-resource-manager/resource-group-template-deploy.md) um serviço de nuvem do Azure e seus recursos de monitoramento do Application Insights em uma única operação.

### <a name="resources-for-components"></a>Recursos para componentes
É recomendável criar um recurso separado para cada componente do seu aplicativo. Ou seja, criar um recurso para cada função Web e função de trabalho. Você pode analisar cada componente separadamente, mas cria um [dashboard](../../azure-monitor/app/overview-dashboard.md) que reúne os principais gráficos de todos os componentes para poder compará-los e monitorá-los juntos em uma única exibição. 

Uma abordagem alternativa é enviar a telemetria de mais de uma função para o mesmo recurso, mas [adicionar uma propriedade de dimensão para cada item de telemetria](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer) que identifique sua função de origem. Nessa abordagem, os gráficos de métrica, como de exceções, normalmente apresentam uma agregação das contagens de diversas funções, mas é possível segmentar o gráfico pelo identificador de função, conforme necessário. Também é possível filtrar as pesquisas pela mesma dimensão. Essa alternativa facilita um pouco a visualização de tudo ao mesmo tempo, mas também pode causar uma certa confusão entre as funções.

A telemetria do navegador geralmente é incluída no mesmo recurso que sua função Web do lado servidor.

Coloque os recursos do Application Insights para os vários componentes em um grupo de recursos. Essa abordagem facilita o gerenciamento em conjunto. 

### <a name="separate-development-test-and-production"></a>Separação de desenvolvimento, teste e produção
Se você estiver desenvolvendo eventos personalizados para o próximo recurso enquanto a versão anterior estiver ativa, será recomendável enviar a telemetria de desenvolvimento para um recurso separado do Application Insights. Caso contrário, pode ser difícil encontrar a telemetria de teste entre todo o tráfego do site ativo.

Para evitar essa situação, crie recursos separados para cada configuração de build ou “stamp” (desenvolvimento, teste, produção, etc.) do sistema. Coloque os recursos para cada configuração de compilação em um grupo de recursos separado. 

Para enviar a telemetria para os recursos apropriados, você pode configurar o SDK do Application Insights para que ele escolha uma chave de instrumentação diferente dependendo da configuração de compilação. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Criar um recurso do Application Insights para cada função

Se você decidiu criar um recurso separado para cada função (e talvez um conjunto separado para cada configuração de build), é mais fácil criar todos eles no portal do Application Insights. Se você cria muitos recursos, pode [automatizar o processo](../../azure-monitor/app/powershell.md).

1. Na [portal do Azure][portal], selecione **novo** > **Serviços** > de desenvolvedor**Application insights**.  

    ![Painel do Application Insights](./media/cloudservices/01-new.png)

1. Na lista suspensa **Tipo de Aplicativo**, selecione **Aplicativo Web ASP.NET**.  
    Cada recurso é identificado por uma chave de instrumentação. Você pode precisar dessa chave mais tarde, caso deseje configurar ou verificar a configuração do SDK manualmente.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Configurar o diagnóstico do Azure para cada função
Defina esta opção para monitorar seu aplicativo com o Application Insights. Para funções Web, esta opção fornece monitoramento de desempenho, alertas, diagnóstico e análise de uso. Para as demais funções, você pode pesquisar e monitorar o Diagnóstico do Azure, como reinicialização, contadores de desempenho e chamadas para System.Diagnostics.Trace. 

1. No Gerenciador de Soluções do Visual Studio, em **\<SeuServiçoDeNuvem>**  > **Funções**, abra as propriedades de cada função.

1. Em **Configuração**, marque a caixa de seleção **Enviar dados de diagnóstico para o Application Insights** e, então, selecione o recurso do Application Insights criado anteriormente.

Se você tiver optado por usar um recurso Application Insights separado para cada configuração de compilação, selecione primeiro a configuração.

![Configurar o Application Insights](./media/cloudservices/configure-azure-diagnostics.png)

Essa ação faz com que as suas chaves de instrumentação do Application Insights sejam inseridas nos arquivos chamados *ServiceConfiguration.\*.cscfg*. Veja o [código de exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Se desejar variar o nível de informações de diagnóstico enviadas ao Application Insights, faça isso [editando os arquivos *.cscfg* diretamente](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

## <a name="sdk"></a>Instalar o SDK em cada projeto
Com essa opção, você pode adicionar telemetria personalizada de negócios em qualquer função. A opção fornece uma análise mais próxima de como seu aplicativo é usado e do desempenho dele.

No Visual Studio, configure o SDK do Application Insights para cada projeto de aplicativo de nuvem.

1. Para configurar as **funções Web**, clique com o botão direito do mouse no projeto e selecione **Configurar Application Insights** ou **Adicionar > Application Insights Telemetry**.

1. Para configurar as **funções de trabalho**: 

    a. Clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet**.

    b. Adicione [Application Insights para Windows Servers](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Pesquise "Application Insights"](./media/cloudservices/04-ai-nuget.png)

1. Para configurar o SDK para enviar dados ao recurso do Application Insights:

    a. Em uma função de inicialização adequada, defina a chave de instrumentação na configuração no arquivo *.cscfg*:
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. Repita a "etapa a" para cada função em seu aplicativo. Veja os exemplos:
   
    * [Função Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Função de trabalho](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Para páginas da Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Defina o arquivo *ApplicationInsights.config* para sempre ser copiado no diretório de saída.  
    Uma mensagem no arquivo *. config* solicitará que você coloque a chave de instrumentação lá. No entanto, para aplicativos de nuvem, é melhor defini-la no arquivo *.cscfg*. Essa abordagem garante que a função seja identificada corretamente no portal.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Configurar Status Monitor para coletar consultas SQL completas (opcional)

Esta etapa só será necessária se você quiser capturar consultas SQL completas em .NET Framework. 

1. Em `\*.csdef` arquivo adicionar [tarefa de inicialização](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks) para cada função semelhante a 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. Baixe [InstallAgent. bat](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) e [InstallAgent. ps1](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1), coloque `AppInsightsAgent` -os na pasta em cada projeto de função. Certifique-se de copiá-los para o diretório de saída por meio de propriedades de arquivo do Visual Studio ou scripts de compilação.

3. Em todas as funções de trabalho, adicione variáveis de ambiente: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>Executar e publicar o aplicativo

1. Execute o aplicativo e entre no Azure. 

1. Abra os recursos criados do Application Insights.  
    Os pontos de dados individuais são exibidos na [Pesquisa](../../azure-monitor/app/diagnostic-search.md), e os dados agregados são exibidos no [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md). 

1. Adicione mais telemetria (consulte as próximas seções) e, em seguida, publique seu aplicativo para obter comentários em tempo real sobre o diagnóstico e o uso. 

Se não houver nenhum dado, faça o seguinte:
1. Para exibir eventos individuais, abra o bloco [Pesquisar][diagnostic] .
1. No aplicativo, abra várias páginas para que ele gere alguma telemetria.
1. Aguarde alguns segundos e, então, clique em **Atualizar**.  
    Para saber mais, confira [Solução de problemas][qna].

## <a name="view-azure-diagnostics-events"></a>Exibir eventos do Diagnóstico do Azure
É possível encontrar as informações do [Diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) no Application Insights, nos seguintes locais:

* Os contadores de desempenho são exibidos como métricas personalizadas. 
* Os logs de eventos do Windows são mostrados como eventos de rastreamentos e personalizados.
* Logs do aplicativo, logs de ETW e todos os logs de infraestrutura de diagnóstico são exibidos como rastreamentos.

Para exibir os contadores de desempenho e as contagens de eventos, abra o [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) e adicione o seguinte gráfico:

![Dados de Diagnóstico do Azure](./media/cloudservices/23-wad.png)

Para pesquisar nos vários logs de rastreamento enviados pelo Diagnóstico do Azure, use a [Pesquisa](../../azure-monitor/app/diagnostic-search.md) ou uma [consulta do Analytics](../../azure-monitor/log-query/get-started-portal.md). Por exemplo, imagine que você tem uma exceção sem tratamento que causou a falha e a reciclagem de uma função. Essa informação será mostrada no canal do Aplicativo do Log de Eventos do Windows. É possível usar a Pesquisa para exibir o erro do Log de Eventos do Windows e obter o rastreamento de pilha completo da exceção. Isso lhe ajudará a encontrar a causa raiz do problema.

![Pesquisa no Diagnóstico do Azure](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Mais telemetria
As seções a seguir mostram como obter telemetria adicional de vários aspectos do seu aplicativo.

## <a name="track-requests-from-worker-roles"></a>Acompanhar as solicitações das funções de trabalho
Nas funções web, o módulo de solicitações automaticamente coleta dados sobre solicitações HTTP. Para obter exemplos de como substituir o comportamento de coleção padrão, consulte o [Exemplo MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Você pode capturar o desempenho das chamadas a funções de trabalho acompanhando-as da mesma maneira como faz com solicitações HTTP. No Application Insights, o tipo de telemetria Solicitação mede uma unidade de trabalho do servidor nomeada que pode ser cronometrada e ser bem-sucedida ou falhar de forma independente. Embora as solicitações HTTP sejam capturadas automaticamente pelo SDK, você pode inserir seu próprio código para acompanhar as solicitações para as funções de trabalho.

Consulte as duas funções de trabalho de exemplo instrumentadas para solicitações de relatório: 
* [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Exceções
Para saber mais sobre como coletar exceções sem tratamento de vários tipos de aplicativo Web, consulte o artigo [Monitorar exceções no Application Insights](../../azure-monitor/app/asp-net-exceptions.md).

A função web de exemplo tem controladores MVC5 e API Web 2. As exceções sem tratamento das duas são capturadas com os seguintes manipuladores:

* Configuração do [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) para controladores MVC5, [conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* Configuração do [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) para controladores Web API 2, [conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

Para as funções de trabalho, você pode acompanhar as exceções de duas formas:

* Usar TrackException(ex).
* Se você adicionou o pacote NuGet do ouvinte de rastreamento do Application Insights, use System.Diagnostics.Trace para registrar as exceções, [conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

## <a name="performance-counters"></a>Contadores de desempenho
Os seguintes contadores são coletados por padrão:

* \Process(??APP_WIN32_PROC??)\% Tempo do Processador
* \Memória\Bytes Disponíveis
* \.Exceções NET CLR (?. APP_CLR_PROC?)\# de exceções lançadas / s
* \Processo(??APP_WIN32_PROC??)\Bytes Privados
* \Processo(??APP_WIN32_PROC??)\Bytes de dados de ES/s
* \Processador(_Total)\% Tempo do processador

Para funções web, esses contadores também são coletados:

* \Aplicativos ASP.NET(??APP_W3SVC_PROC??)\Solicitções/S
* \Aplicativos ASP.NET (?. APP_W3SVC_PROC?)\Tempo de Execução de Solicitação
* \Aplicativos ASP.NET (?. APP_W3SVC_PROC?)\Solicitações na Fila do Aplicativo

É possível especificar contadores de desempenho personalizados adicionais ou outros contadores de desempenho do Windows editando o *ApplicationInsights.config*, [conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Contadores de desempenho](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Telemetria correlacionada para as funções de trabalho
Para obter uma experiência de diagnóstico avançada, é possível ver o que ocasionou uma alta solicitação de latência ou a falha dela. Com as funções Web, o SDK configura automaticamente a correlação com a telemetria relacionada. 

Para obter essas exibições das funções de trabalho, use um inicializador de telemetria personalizado para definir um atributo de contexto Operation.Id comum para todas as telemetrias. Ao fazer isso, você vê imediatamente se o problema de latência ou falha foi causado por uma dependência ou pelo código. 

Veja como:

* Defina a ID de correlação em uma CallContext, [conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). Nesse caso, estamos usando a ID de solicitação como correlationId.
* Adicione uma implementação personalizada de TelemetryInitializer para definir a Operation.Id à correlationId que foi definida anteriormente. Para ver um exemplo, consulte [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Adicione o inicializador de telemetria personalizado. Você pode fazer isso no arquivo *ApplicationInsights.config* ou no código, [conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Telemetria do cliente
Para obter telemetria baseada em navegador, como contagens de exibição de página, tempos de carregamento de página ou exceções de script, e para escrever telemetria personalizada em seus scripts de página, consulte [Adicionar o SDK do JavaScript às suas páginas da][client]Web.

## <a name="availability-tests"></a>Testes de disponibilidade
Para garantir que seu aplicativo permaneça dinâmico e responsivo, [Configure testes da Web][availability].

## <a name="display-everything-together"></a>Exibir tudo juntos
Para obter uma visão geral do seu sistema, é possível exibir os gráficos de monitoramento da chave em um [dashboard](../../azure-monitor/app/overview-dashboard.md). Por exemplo, você pode fixar as contagens de solicitação e de falha de cada função. 

Se seu sistema usa outros serviços do Azure, como o Stream Analytics, inclua os gráficos de monitoramento deles também. 

Caso tenha um aplicativo cliente móvel, use o [App Center](../../azure-monitor/learn/mobile-center-quickstart.md). Crie consultas no [Analytics](../../azure-monitor/app/analytics.md) para exibir a contagem de eventos e fixe-os ao painel.

## <a name="example"></a>Exemplo
[O exemplo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) monitora um serviço que tem uma função web e duas funções de trabalho.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exceção "método não encontrado" em execução nos serviços de nuvem do Azure
Você compilou para .NET 4.6? O .NET 4.6 não tem suporte automático nas funções dos serviços de nuvem do Azure. [Instale o .NET 4.6 em cada função](../../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de executar seu aplicativo.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Próximas etapas
* [Configurar o envio dos Diagnósticos do Azure ao Application Insights](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Criar automaticamente recursos do Application Insights](../../azure-monitor/app/powershell.md)
* [Automatizar o Diagnóstico do Azure](../../azure-monitor/app/powershell-azure-diagnostics.md)
* [Funções do Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[azure]: ../../azure-monitor/app/app-insights-overview.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md 
