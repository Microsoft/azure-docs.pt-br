---
title: Solução de problemas de ausência de dados - Application Insights para .NET
description: Não consegue ver os dados no Application Insights do Azure? Tente aqui.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2018
ms.author: mbullwin
ms.openlocfilehash: 2966f90dcb381e439c00a6540ef9a01bd24f8743
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561184"
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Solução de problemas de ausência de dados - Application Insights para .NET
## <a name="some-of-my-telemetry-is-missing"></a>Parte da minha telemetria está ausente
*No Application Insights, vejo apenas uma fração dos eventos que são gerados pelo meu aplicativo.*

* Caso você esteja vendo consistentemente a mesma fração, isso provavelmente se deve à [amostragem](../../azure-monitor/app/sampling.md)adaptável. Para confirmar isso, abra a Pesquisa (na folha de visão geral) e examine uma instância de uma Solicitação ou outro evento. Na parte inferior da seção de propriedades, clique em "…" para obter detalhes completos da propriedade. Se Contagem de solicitações for > 1, a amostragem estará em operação.
* Caso contrário, é possível que você esteja atingindo um [limite de taxa de dados](../../azure-monitor/app/pricing.md#limits-summary) para seu plano de preços. Esses limites são aplicados por minuto.

*Estou tendo perda de dados aleatoriamente.*

* Verifique se você estiver com perda de dados no [canal de telemetria](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost)

* Verificar se há problemas conhecidos no canal de telemetria [repositório do Github](https://github.com/Microsoft/ApplicationInsights-dotnet/issues)

*Estou tendo perda de dados no aplicativo de Console ou no aplicativo Web quando o aplicativo está prestes a parar.*

* Canal do SDK mantém telemetria em buffer e envia-os em lotes. Se o aplicativo está sendo desligado, você precisa chamar explicitamente [flush ()](api-custom-events-metrics.md#flushing-data). Comportamento de `Flush()` real depende [canal](telemetry-channels.md#built-in-telemetry-channels) usado.

## <a name="no-data-from-my-server"></a>Nenhum dado do meu servidor
*Instalei meu aplicativo em meu servidor Web e agora não vejo nenhuma telemetria dele. Ele funcionou corretamente no meu computador de desenvolvimento.*

* Provavelmente um problema de firewall. [Defina exceções de firewall para o Application Insights enviar dados](../../azure-monitor/app/ip-addresses.md).
* Podem estar faltando alguns pré-requisitos no servidor IIS: .NET Extensibility 4.5 e ASP.NET 4.5.

*Eu [instalei o Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md) no meu servidor Web para monitorar os aplicativos existentes. Não consigo ver todos os resultados.*

* Veja [Solução de problemas do Monitor de Status](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="q01"></a>A opção “Adicionar o Application Insights” não existe no Visual Studio
*Quando clico com o botão direito em um projeto existente no Gerenciador de Soluções, não vejo opções do Application Insights.*

* Nem todos os tipos de projeto do .NET têm suporte das ferramentas. Há suporte para projetos Web e WCF. Para outros tipos de projeto, como aplicativos de área de trabalho ou de serviço, você ainda pode [adicionar manualmente um SDK do Application Insights ao seu projeto](../../azure-monitor/app/windows-desktop.md).
* Certifique-se de que você tem o [Visual Studio 2013 Atualização 3 ou posterior](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs). Ele vem pré-instalado com ferramentas de análise do desenvolvedor, que fornecem o SDK do Application Insights.
* Escolha **Ferramentas**, **Extensões e Atualizações** e verifique se as **Ferramentas de Análise do Desenvolvedor** estão instaladas e habilitadas. Nesse caso, clique em **Atualizações** para ver se há uma atualização disponível.
* Abra o diálogo Novo Projeto e escolha aplicativo Web ASP.NET. Se você vir a opção de Application Insights, as ferramentas estão instaladas. Caso contrário, tente desinstalar e, em seguida, reinstalar o Developer Analytics Tools.

## <a name="q02"></a>Falha ao adicionar o Application Insights
*Quanto tento adicionar o Application Insights a um projeto existente, vejo uma mensagem de erro.*

Causas mais prováveis:

* A comunicação com o portal do Application Insights tiver falhado ou
* Há algum problema com sua conta do Azure;
* Você tem apenas [acesso de leitura para a assinatura ou grupo em que está tentando criar o novo recurso](../../azure-monitor/app/resources-roles-access-control.md).

Correção:

* Verifique se você forneceu as credenciais corretas de entrada da conta do Azure.
* No navegador, verifique se você tem acesso ao [portal do Azure](https://portal.azure.com). Abra Configurações e veja se há alguma restrição.
* [Adicione o Application Insights ao seu projeto](../../azure-monitor/app/asp-net.md): Em Gerenciador de Soluções, clique com o botão direito no seu projeto e escolha "Adicionar Application Insights."

## <a name="emptykey"></a>Recebo um erro "Chave de instrumentação não pode ser vazio"
Parece que algo deu errado enquanto você instalava o Application Insights, ou talvez um adaptador de registro em log.

No Gerenciador de Soluções, clique com o botão direito no projeto e escolha **Application Insights > Configurar Application Insights**. Você obterá uma caixa de diálogo que solicita que você entre no Azure e crie um recurso de Application Insights, ou então reutilize um recurso existente.

## <a name="NuGetBuild"></a> "Pacotes NuGet estão ausentes" no meu servidor de build
*Tudo é compilado corretamente quando eu estou depurando em meu computador de desenvolvimento, mas obtenho um erro do NuGet no servidor de compilação.*

Consulte [Restauração do Pacote NuGet](https://docs.nuget.org/Consume/Package-Restore) e [Restauração Automática do Pacote](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Comando de menu ausente para abrir o Application Insights do Visual Studio
*Quando eu clico com o botão direito do mouse no Gerenciador de Soluções do meu projeto, não vejo os comandos do Application Insights ou não vejo um comando Abrir Application Insights.*

Causas mais prováveis:

* Se você tiver criado o recurso Application Insights manualmente, ou se o projeto for de um tipo que não seja compatível com as ferramentas do Application Insights.
* As ferramentas de Análise do Desenvolvedor estão desabilitadas no Visual Studio.
* O Visual Studio é anterior à versão 2013 Atualização 3.

Correção:

* Verifique se a versão do Visual Studio é a 2013 atualização 3 ou posterior.
* Escolha **Ferramentas**, **Extensões e Atualizações** e verifique se as **Ferramentas de Análise do Desenvolvedor** estão instaladas e habilitadas. Nesse caso, clique em **Atualizações** para ver se há uma atualização disponível.
* Clique com o botão direito do mouse no projeto no Gerenciador de Soluções. Se você vir o comando **Application Insights > Configurar Application Insights**, use-o para conectar seu projeto ao recurso no serviço Application Insights.

Caso contrário, o tipo de projeto não tem suporte diretamente pelas ferramentas de análise do desenvolvedor. Para ver a telemetria, entre no [portal do Azure](https://portal.azure.com), escolha Application Insights na barra de navegação à esquerda e selecione seu aplicativo.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>“Acesso negado” ao abrir o Application Insights a partir do Visual Studio
*O comando de menu “Abrir Application Insights” me leva ao portal do Azure, mas eu recebo um erro de “acesso negado”.*

As credenciais da Microsoft que você usou pela última vez no navegador padrão não têm acesso ao [recurso que foi criado quando o Application Insights foi adicionado a este aplicativo](../../azure-monitor/app/asp-net.md). Há duas razões prováveis:

* Você tem mais de uma conta da Microsoft. Talvez uma conta de trabalho e uma conta pessoal da Microsoft? As credenciais usadas pela última vez no navegador padrão foram para uma conta diferente daquela que tem acesso a [adicionar o Application Insights ao projeto](../../azure-monitor/app/asp-net.md).
  * Correção: Clique em seu nome no canto superior direito da janela do navegador e saia. Em seguida, entre com a conta que tem acesso. Na barra de navegação à esquerda, clique em Application Insights e escolha seu aplicativo.
* Alguém adicionou o Application Insights ao projeto e esqueceu-se de dar [acesso ao grupo de recursos](../../azure-monitor/app/resources-roles-access-control.md) no qual ele foi criado.
  * Correção: Se eles tiverem usado uma conta organizacional, poderão adicionar você à equipe, ou podem conceder acesso individual ao grupo de recursos.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>“Ativo não encontrado” ao abrir o Application Insights do Visual Studio
*O comando de menu “Abrir Application Insights” me leva ao portal do Azure, mas eu obtenho um erro de “ativo não encontrado”.*

Causas mais prováveis:

* O recurso Application Insights para seu aplicativo foi excluído ou
* A chave de instrumentação foi definida ou alterada em applicationinsights.config quando ele foi editado diretamente sem a atualização do arquivo do projeto.

A chave de instrumentação em applicationinsights.config controla o local para onde a telemetria é enviada. Uma linha no arquivo do projeto controla qual recurso será aberto quando você usar o comando no Visual Studio.

Correção:

* No Gerenciador de Soluções, clique com o botão direito do mouse no projeto no Gerenciador de Soluções e escolha Application Insights, Configurar Application Insights. No diálogo, você pode optar por enviar a telemetria para um recurso existente ou criar um novo. Ou:
* Abra o recurso diretamente. Entre no [portal do Azure](https://portal.azure.com), clique em Application Insights na barra de navegação à esquerda e selecione seu aplicativo.

## <a name="where-do-i-find-my-telemetry"></a>Onde posso encontrar minha telemetria?
*Entrei no [portal do Microsoft Azure](https://portal.azure.com) e estou olhando para o painel inicial do Azure. Como eu encontro meus dados no Application Insights?*

* Na barra de navegação à esquerda, clique em Application Insights e no nome do aplicativo. Se você não tiver projetos, precisará [adicionar ou configurar o Application Insights ao seu projeto Web](../../azure-monitor/app/asp-net.md).  
  Lá, você verá alguns gráficos de resumo. Clique em qualquer gráfico para ver mais detalhes.
* No Visual Studio, enquanto estiver depurando o aplicativo, clique no botão Application Insights.

## <a name="q03"></a> Nenhum dado do servidor (ou nenhum dado)
*Executei meu aplicativo e abri o serviço Application Insights no Microsoft Azure, mas todos os gráficos mostram “Saiba como coletar...” ou “Não configurado”.* Ou *somente Exibição de Página e dados de usuário, mas nenhum dado do servidor.*

* Execute seu aplicativo em modo de depuração no Visual Studio (F5). Use o aplicativo para gerar alguma telemetria. Verifique se você pode ver os eventos registrados na janela de saída do Visual Studio.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* No portal do Application Insights, abra a [Pesquisa de Diagnóstico](../../azure-monitor/app/diagnostic-search.md). Os dados normalmente aparecem aqui primeiro.
* Clique no botão Atualizar. A folha se atualiza periodicamente, mas você também pode fazê-lo manualmente. O intervalo de atualização é maior para intervalos de tempo maiores.
* Verifique se as chaves de instrumentação correspondem. Na folha principal do aplicativo no portal do Application Insights, na lista suspensa **Essentials**, examine a **Chave de instrumentação**. Em seguida, no projeto no Visual Studio, abra ApplicationInsights.config e localize a `<instrumentationkey>`. Verifique se as duas chaves são iguais. Caso contrário:  
  * No portal, clique em Application Insights e procure o recurso de aplicativo com a chave correta ou
  * No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto e escolha Application Insights, Configurar. Redefina o aplicativo para enviar telemetria ao recurso correto.
  * Se você não conseguir localizar as chaves correspondentes, verifique se está usando as mesmas credenciais de logon no Visual Studio e no portal.
* No [painel inicial do Microsoft Azure](https://portal.azure.com), veja o mapa de Integridade do Serviço. Se houver indicações de alerta, espere até que elas tenham voltado a OK; então, feche e abra novamente a folha do Application Insights de seu aplicativo.
* Verifique também o [nosso blog de status](https://blogs.msdn.microsoft.com/servicemap-status/).
* Você gravou algum código para o [SDK do lado do servidor](../../azure-monitor/app/api-custom-events-metrics.md) que possa alterar a chave de instrumentação em instâncias do `TelemetryClient` ou no `TelemetryContext`? Ou gravou uma [configuração de filtro ou de amostragem](../../azure-monitor/app/api-filtering-sampling.md) que possa estar filtrando em excesso?
* Se você tiver editado o ApplicationInsights.config, verifique cuidadosamente a configuração de [TelemetryInitializers e TelemetryProcessors](../../azure-monitor/app/api-filtering-sampling.md). Um tipo ou parâmetro nomeado incorretamente pode fazer com que o SDK não envie nenhum dado.

## <a name="q04"></a>Nenhum dado sobre Exibições de Página, Navegadores, Uso
*Vejo dados nos gráficos Tempo de Resposta do Servidor e Solicitações de Servidor, mas nenhum dado em tempo de Carregamento da Exibição de Página ou nas folhas Navegador ou Uso.*

Os dados vêm de scripts nas páginas da Web. 

* Se tiver adicionado o Application Insights a um projeto Web existente, [você terá que adicionar os scripts manualmente](../../azure-monitor/app/javascript.md).
* Tenha certeza de que o Internet Explorer não está exibindo o site no modo de Compatibilidade.
* Use o recurso de depuração do navegador (F12 em alguns navegadores e, em seguida, escolha Rede) para verificar se os dados estão sendo enviados para `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Nenhum dado de dependência ou de exceção
Confira [telemetria de dependência](../../azure-monitor/app/asp-net-dependencies.md) e [telemetria de exceção](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Nenhum dado de desempenho
Haverá dados de desempenho (CPU, taxa de E/S, etc.) disponíveis para [serviços Web Java](../../azure-monitor/app/java-collectd.md), [aplicativos da área de trabalho do Windows](../../azure-monitor/app/windows-desktop.md), [aplicativos Web e serviços do IIS se você instalar o Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md) e os [Serviços de Nuvem do Azure](../../azure-monitor/app/app-insights-overview.md). Encontre-os em Configurações, Servidores.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Nenhum dado (servidor) desde que publiquei o aplicativo no servidor
* Verifique que você copiou todos da Microsoft. DLLs do ApplicationInsights no servidor, junto com Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* Em seu firewall, você talvez precise [abrir algumas portas TCP](../../azure-monitor/app/ip-addresses.md).
* Se você tiver que usar um proxy para envio fora de sua rede corporativa, defina [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) em Web.config
* Windows Server 2008: Verifique se você instalou as atualizações a seguir: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Eu costumava ver os dados, mas eles foram interrompidos
* Verifique o [blog de status](https://blogs.msdn.com/b/applicationinsights-status/).
* Você atingiu sua cota mensal de pontos de dados? Abra configurações/Cota e Preços para descobrir. Nesse caso, você pode atualizar seu plano ou então pagar por capacidade adicional. Consulte o [esquema de preços](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Não vejo todos os dados que eu esperava
Se o aplicativo enviar muitos dados e se você estiver usando o SDK do Application Insights para o ASP.NET versão 2.0.0-beta3 ou posterior, o recurso de [amostragem adaptável](../../azure-monitor/app/sampling.md) poderá operar e enviar apenas um percentual de sua telemetria.

Você pode desativá-lo, mas isso não é recomendado. A amostragem é projetada para que a telemetria relacionada seja corretamente transmitida para fins de diagnóstico.

## <a name="client-ip-address-is-0000"></a>O Endereço IP do cliente é 0.0.0.0

Em fevereiro de 5 2018, anunciamos que removemos o registro em log do endereço IP do cliente. Isso não afeta a localização geográfica.

> [!NOTE]
> Se você precisar dos primeiros 3 octetos do endereço IP, poderá usar um [inicializador de telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer) para adicionar um atributo personalizado.
> Isso não afeta os dados coletados antes de 5 de fevereiro de 2018.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Dados geográficos errados na telemetria do usuário
A dimensões de cidade, de região e de país são derivadas de endereços IP e nem sempre são precisas. Esses endereços IP são processados para o local primeiro e, em seguida, alterados para 0.0.0.0 para armazenamento.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exceção "método não encontrado" em execução nos Serviços de Nuvem do Azure
Você compilou para .NET 4.6? O 4.6 não tem suporte automático nas funções dos Serviços de Nuvem do Azure. [Instale o 4.6 em cada função](../../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de executar seu aplicativo.

## <a name="troubleshooting-logs"></a>Solucionar problemas de logs

Siga estas instruções para capturar logs de solução de problemas para sua estrutura.

### <a name="net-framework"></a>.NET Framework

1. Instale o pacote [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) do NuGet. A versão que você instala deve corresponder à versão instalada atual do `Microsoft.ApplicationInsighs`

2. Modifique o arquivo applicationinsights.config para incluir o seguinte:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Seu aplicativo deve ter permissões de gravação para o local configurado

3. Reinicie o processo para que essas novas configurações sejam captadas pelo SDK

4. Reverta essas alterações quando tiver terminado.

### <a name="net-core"></a>.NET Core

1. Instale o pacote [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) do NuGet. A versão que você instala deve corresponder à versão instalada atual do `Microsoft.ApplicationInsights`

A última versão do aspnetcore é 2.7.1, e ele se refere ao applicationinsights versão 2.10. Portanto, a versão do Microsoft.AspNet.ApplicationInsights.HostingStartup a serem instalados deve ser 2.10.0

2. Modifique o método `ConfigureServices` na sua classe `Startup.cs`:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Seu aplicativo deve ter permissões de gravação para o local configurado

3. Reinicie o processo para que essas novas configurações sejam captadas pelo SDK

4. Reverta essas alterações quando tiver terminado.


## <a name="PerfView"></a> Coletar logs com PerfView
[O PerfView](https://github.com/Microsoft/perfview) é uma ferramenta gratuita de diagnóstico e análise de desempenho que ajudam a isolar a CPU, memória e outros problemas ao coletar e visualizar informações de diagnóstico de várias fontes.

SDK do Application Insights log EventSource logs automaticamente para solução de problemas que podem ser capturados por PerfView.

Para coletar logs, baixe o PerfView e execute este comando:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Você pode modificar esses parâmetros conforme necessário:
- **MaxCollectSec**. Defina esse parâmetro para impedir que o PerfView da execução indefinidamente e afetar o desempenho de seu servidor.
- **OnlyProviders**. Defina esse parâmetro para apenas coletar logs do SDK. Você pode personalizar essa lista com base em suas investigações específicas. 
- **NoGui**. Defina esse parâmetro para coletar logs sem a Gui.


Para obter mais informações,
- [Registrar rastreamentos de desempenho com o PerfView](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Fontes de evento do Application Insights](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/ETW)

## <a name="still-not-working"></a>Ainda não está funcionando...
* [Fórum do Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
