---
title: Monitorar um aplicativo Web ASP.NET dinâmico com o Azure Application Insights | Microsoft Docs
description: Monitore o desempenho do site sem implantá-lo novamente. Funciona com aplicativos Web ASP.NET hospedado localmente ou em máquinas virtuais.
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: devx-track-dotnet
ms.openlocfilehash: 79e14c171adde89c43c5ea82a60db39133157293
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576434"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-codeless-attach"></a>Instrumentar aplicativos Web em tempo de execução com Application Insights anexação sem código

> [!IMPORTANT]
> O Status Monitor não é mais recomendado para uso e a **partir de 1º de junho de 2021** esta versão do monitor de status não terá suporte. Ele foi substituído pelo agente de Application Insights de Azure Monitor (anteriormente denominado Status Monitor v2). Consulte nossa documentação para [implantações de servidor local](./status-monitor-v2-overview.md) ou [máquinas virtuais do Azure e implantações do conjunto de dimensionamento de máquinas virtuais](./azure-vm-vmss-apps.md).

Você pode instrumentar um aplicativo Web ativo com o Application Insights do Azure, sem a necessidade de modificar ou reimplantar o código. É necessário ter uma assinatura do [Microsoft Azure](https://azure.com) .

O Status Monitor é usado para instrumentar um aplicativo .NET hospedado no IIS seja localmente ou em uma VM.

- Se seu aplicativo for implantado na VM do Azure ou no conjunto de dimensionamento de máquinas virtuais do Azure, siga [estas instruções](azure-vm-vmss-apps.md).
- Se o aplicativo é implantado para os Serviços de Aplicativos do Azure, siga [estas instruções](azure-web-apps.md).
- Se o aplicativo é implantado em uma VM do Azure, você pode alternar no monitoramento do Application Insights usando o painel de controle do Azure.
- (Também há artigos separados sobre instrumentação de [serviços de nuvem do Azure](./cloudservices.md).)


![Captura de tela dos gráficos de visão geral do App Insights contendo informações sobre solicitações com falha, tempo de resposta do servidor e solicitações do servidor](./media/monitor-performance-live-website-now/overview-graphs.png)

Você tem duas opções de rotas para aplicar o Application Insights nos aplicativos Web .NET:

* **Tempo de compilação:** [Adicionar o SDK do Application Insights][greenbrown] ao código do aplicativo Web.
* **Tempo de execução:** instrumente seu aplicativo Web no servidor, conforme descrito abaixo, sem recompilar e reimplantar o código.

> [!NOTE]
> Se você usar a instrumentação de tempo de compilação, a instrumentação de tempo de execução não funcionará mesmo se estiver ativada.

Aqui está um resumo do que você tem com cada rota:

|  | Tempo de compilação | Tempo de execução |
| --- | --- | --- |
| **Solicitações & exceções** |Sim |Sim |
| **[Exceções mais detalhadas](./asp-net-exceptions.md)** | |Yes |
| **[Diagnóstico de dependência](./asp-net-dependencies.md)** |No .NET 4.6+, mas menos detalhes |Sim, detalhes completos: códigos de resultado, texto do comando SQL, verbo HTTP|
| **[Contadores de desempenho do sistema](./performance-counters.md)** |Sim |Sim |
| **[API para telemetria personalizada][api]** |Sim |Não |
| **[Integração de log de rastreamento](./asp-net-trace-logs.md)** |Sim |Não |
| **[Exibição de página & dados de usuário](./javascript.md)** |Sim |Não |
| **É necessário recompilar o código** |Sim | Não |



## <a name="monitor-a-live-iis-web-app"></a>Monitorar um aplicativo de web IIS ao vivo

Se seu aplicativo estiver hospedado em um servidor do IIS, habilite o Application Insights usando o Monitor de Status.

1. No servidor Web IIS, entre com as credenciais de administrador.
2. Se o Application Insights Status Monitor ainda não estiver instalado, [baixe e execute o instalador](#download)
3. No Monitor de Status, selecione o aplicativo Web ou o site que você deseja monitorar. Entre com suas credenciais do Azure.

    Configure o recurso onde você deseja ver os resultados no portal do Application Insights. (Normalmente, é melhor criar um novo recurso. Selecione um recurso existente se você já tiver [testes da web][availability] ou [monitoramento de cliente][client] para esse aplicativo). 

    ![Escolha um aplicativo e um recurso.](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Reinicie o IIS.

    ![Escolha Reiniciar na parte superior da caixa de diálogo.](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    O serviço Web é interrompido por um período curto.

## <a name="customize-monitoring-options"></a>Personalizar opções de monitoramento

Habilitar o Application Insights adiciona DLLs e applicationinsights.config ao seu aplicativo Web. Você pode [editar o arquivo .config](./configuration-with-applicationinsights-config.md) para alterar algumas opções.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Quando você publicar novamente seu aplicativo, habilite novamente o Application Insights

Antes de publicar novamente seu aplicativo, considere [adicionar Application Insights ao código no Visual Studio][greenbrown]. Você obterá uma telemetria mais detalhada e a capacidade de escrever telemetria personalizada.

Se você deseja publicar novamente sem adicionar Application Insights no código, lembre-se de que o processo de implantação pode excluir as DLLs e applicationinsights.config do site publicado. Portanto:

1. Se você tiver editado applicationinsights.config, faça uma cópia dele antes de publicar seu aplicativo novamente.
2. Republique seu aplicativo.
3. Habilite novamente o monitoramento do Application Insights. (Use o método apropriado: o painel de controle do aplicativo Web do Azure ou o Monitor de Status em um host do IIS).
4. Reaplique as edições realizadas no arquivo .config.


## <a name="troubleshooting"></a><a name="troubleshoot"></a>Solução de problemas

### <a name="confirm-a-valid-installation"></a>Confirmar uma instalação válida 

Estas são algumas etapas que você pode executar para confirmar se a instalação foi bem-sucedida.

- Verifique se o arquivo applicationInsights.config está presente no diretório de aplicativo de destino e contém sua ikey.

- Se você suspeitar de que os dados estão ausentes, poderá executar uma consulta no [Analytics](../logs/log-analytics-tutorial.md) para listar todas as funções de nuvem que estão enviando telemetria no momento.
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Se você precisar confirmar se o Application Insights foi anexado com êxito, poderá executar o [identificador do Sysinternals](/sysinternals/downloads/handle) em uma janela de comando para confirmar que applicationinsights.dll foi carregado pelo IIS.

  ```console
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>Não consegue se conectar? Sem telemetria?

* Abra [as portas de saída necessárias](./ip-addresses.md#outgoing-ports) no firewall de seu servidor para permitir o funcionamento do Status Monitor.

### <a name="unable-to-login"></a>Não é possível efetuar logon

Se o Status Monitor não conseguir efetuar logon, faça uma instalação de linha de comando em vez disso. O Status Monitor tenta fazer logon para coletar sua ikey, mas você pode fornecê-la manualmente usando o comando:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Não foi possível carregar o arquivo ou o assembly 'System.Diagnostics.DiagnosticSource'

Você poderá receber esse erro após habilitar o Application Insights. Isso ocorre porque o instalador substitui essa dll no seu diretório bin.
Para consertar, atualize seu web.config:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Estamos acompanhando esse problema [aqui](https://github.com/MohanGsk/ApplicationInsights-Home).


### <a name="application-diagnostic-messages"></a>Mensagens de diagnóstico de aplicativo

* Abra o Monitor de Status e selecione seu aplicativo no painel esquerdo. Verifique se há mensagens de diagnóstico para este aplicativo na seção "Configuração de notificações":

  ![Abra a folha de Desempenho para ver as solicitações, tempos de resposta, dependências e outros dados](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Logs detalhados

* Por padrão, o Status Monitor produzirá os logs de diagnóstico em: `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* Para produzir logs detalhados, modifique o arquivo de configuração: `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` e adicione `<add key="TraceLevel" value="All" />` ao `appsettings`.
Em seguida, reinicie o monitor de status.

* Como Status Monitor é um aplicativo .NET, você também pode habilitar [o rastreamento do .net adicionando o diagnóstico apropriado ao arquivo de configuração](/dotnet/framework/configure-apps/file-schema/trace-debug/system-diagnostics-element). Por exemplo, em alguns cenários, pode ser útil ver o que está acontecendo no nível da rede [Configurando o rastreamento de rede](/dotnet/framework/network-programming/how-to-configure-network-tracing)

### <a name="insufficient-permissions"></a>Permissões insuficientes
  
* No servidor, se você encontrar uma mensagem sobre "permissões insuficientes", tente fazer o seguinte:
  * No Gerenciador do IIS, selecione o pool de aplicativos, abra **Configurações Avançadas** e no **Modelo de Processo**, anote a identidade.
  * No painel de controle de gerenciamento do computador, adicione essa identidade ao grupo Usuários do Monitor de Desempenho.

### <a name="conflict-with-systems-center-operations-manager"></a>Conflito com o Systems Center Operations Manager

* Se você tiver o MMA/SCOM (Systems Center Operations Manager) instalado em seu servidor, algumas versões poderão entrar em conflito. Desinstale o SCOM e o Monitor de Status e reinstale as versões mais recentes.

### <a name="failed-or-incomplete-installation"></a>Instalação com falha ou incompleta

Se o Monitor de Status falhar durante a instalação, você poderá ficar com uma instalação incompleta da qual o Status Monitor não pode se recuperar. Isso exigirá uma reinicialização manual.

Exclua qualquer um destes arquivos encontrados no diretório do aplicativo:
- Todas as DLLs no diretório bin começando com "Microsoft.AI." ou "Microsoft.ApplicationInsights.".
- Essa DLL no diretório bin "Microsoft.Web.Infrastructure.dll"
- Essa DLL no diretório bin "System.Diagnostics.DiagnosticSource.dll"
- No diretório do aplicativo, remova "App_Data\packages"
- No diretório do aplicativo, remova "applicationinsights.config"


### <a name="additional-troubleshooting"></a>Solução de problemas adicionais

* Confira [Solução de problemas][qna] adicional.

## <a name="system-requirements"></a>Requisitos do Sistema
Suporte de sistema operacional para Application Insights Status Monitor no servidor:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

com o SP mais recente e o .NET Framework 4,5 (Status Monitor é criado nesta versão da estrutura)

No lado do cliente, Windows 7, 8, 8.1 e 10, novamente com o .NET Framework 4.5

Suporte ao IIS: IIS 7, 7,5, 8 e 8.5 (o IIS é obrigatório)

## <a name="automation-with-powershell"></a>Automação com o PowerShell
Você pode iniciar e interromper o monitoramento usando o PowerShell no servidor IIS.

Primeiro, importe o módulo do Application Insights:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
```

Saiba quais aplicativos estão sendo monitorados:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Opcional) O nome de um aplicativo Web.
* Exibe o status de monitoramento do Application Insights para cada aplicativo Web (ou o aplicativo nomeado) nesse servidor IIS.
* Retorna `ApplicationInsightsApplication` para cada aplicativo:

  * `SdkState==EnabledAfterDeployment`: o aplicativo está sendo monitorado e foi instrumentado em tempo de execução, pela ferramenta Monitor de Status ou pelo `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: o aplicativo não é instrumentado para o Application Insights. Ele nunca foi instrumentado ou o monitoramento em tempo de execução foi desabilitado com a ferramenta Monitor de Status ou com o `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: o aplicativo foi instrumentado por meio da adição do SDK ao código-fonte. Seu SDK não pode ser atualizado ou interrompido.
  * `SdkVersion` mostra a versão em uso para o monitoramento do aplicativo.
  * `LatestAvailableSdkVersion`mostra a versão atualmente disponível na galeria do NuGet. Para atualizar o aplicativo para esta versão, use `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` O nome do aplicativo no IIS
* `-InstrumentationKey` O ikey do recurso Application Insights em que você deseja que os resultados sejam exibidos.
* Este cmdlet afeta apenas os aplicativos que ainda não estão instrumentados - ou seja, SdkState==NotInstrumented.

    O cmdlet não afeta um aplicativo que já é instrumentado. Não importa se o aplicativo tiver sido instrumentado no momento da compilação por meio da adição do SDK ao código, ou em tempo de execução por meio de um uso anterior desse cmdlet.

    A versão do SDK usada para instrumentar o aplicativo é a versão baixada mais recentemente para este servidor.

    Para baixar a versão mais recente, use Update-ApplicationInsightsVersion.
* Retorna `ApplicationInsightsApplication` se há êxito. Se ele falhar, registrará em log um rastreamento para stderr.

   ```output
   Name                      : Default Web Site/WebApp1
   InstrumentationKey        : 00000000-0000-0000-0000-000000000000
   ProfilerState             : ApplicationInsights
   SdkState                  : EnabledAfterDeployment
   SdkVersion                : 1.2.1
   LatestAvailableSdkVersion : 1.2.3
   ```

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` O nome de um aplicativo no IIS
* `-All` Para o monitoramento de todos os aplicativos desse servidor IIS para o qual `SdkState==EnabledAfterDeployment`
* Para o monitoramento de aplicativos especificados e remove a instrumentação. Ele só funciona para aplicativos que foram instrumentados em tempo de execução usando a ferramenta Monitoramento de Status ou Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Retorna ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: o nome de um aplicativo Web no IIS.
* `-InstrumentationKey` (Opcional.) Use isso para alterar o recurso para o qual a telemetria do aplicativo é enviada.
* Este cmdlet:
  * Atualiza o aplicativo nomeado para a versão do SDK baixado mais recentemente para esta máquina. (Só funciona se `SdkState==EnabledAfterDeployment`)
  * Se você fornecer uma chave de instrumentação, o aplicativo nomeado será reconfigurado para enviar telemetria para o recurso com essa chave. (Funciona se `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Baixa o SDK mais recente do Application Insights para o servidor.

## <a name="questions-about-status-monitor"></a><a name="questions"></a>Perguntas sobre o Status Monitor

### <a name="what-is-status-monitor"></a>O que é o Status Monitor?

Um aplicativo de desktop instalado no servidor web IIS. Ele ajuda você instrumentar e configurar aplicativos web. 

### <a name="when-do-i-use-status-monitor"></a>Quando eu devo usar o Status Monitor?

* Para instrumentar qualquer aplicativo web em execução no servidor IIS - mesmo se ele já esteja em execução.
* Para habilitar a telemetria adicional para aplicativos web que foram [compilados com o SDK do Application Insights](./asp-net.md). 

### <a name="can-i-close-it-after-it-runs"></a>Eu posso fechá-lo depois de ser executado?

Sim. Depois dele instrumentar os sites selecionados, você pode fechá-lo.

Ele não coleta telemetria por si só. Ele apenas configura os aplicativos web e define algumas permissões.

### <a name="what-does-status-monitor-do"></a>O que o Status Monitor faz?

Quando você seleciona um aplicativo web para o Status Monitor para instrumentar:

* Baixa e coloca os assemblies do Application Insights e o arquivo ApplicationInsights.config na pasta de binários do aplicativo Web.
* Permite a criação de perfil do CLR para coletar chamadas de dependência.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Qual versão do SDK do Application Insights o Status Monitor instala?

Agora, o Status Monitor só pode instalar as versões 2.3 ou 2.4 do SDK do Application Insights. 

O SDK do Application Insights versão 2,4 é a [última versão para dar suporte ao .net 4,0](https://github.com/microsoft/ApplicationInsights-dotnet/releases/tag/v2.5.0-beta1) , que foi o [EOL de janeiro de 2016](https://devblogs.microsoft.com/dotnet/support-ending-for-the-net-framework-4-4-5-and-4-5-1/). Portanto, a partir de agora Status Monitor pode ser usado para instrumentar um aplicativo .NET 4,0. 

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>É necessário executar o Status Monitor sempre que eu atualizar o aplicativo?

Não ocorre se você reimplantar incrementalmente. 

Se você selecionar a opção "Excluir arquivos existentes" no processo de publicação, você precisará executar novamente o Status Monitor para configurar o Application Insights.

### <a name="what-telemetry-is-collected"></a>Qual telemetria é coletada?

Para aplicativos que você instrumenta apenas em tempo de execução usando o Status Monitor:

* Solicitações HTTP
* Chamadas para dependências
* Exceções
* Contadores de desempenho

Para aplicativos já instrumentados em tempo de compilação:

 * Contadores de processo.
 * Chamadas de dependência (.NET 4.5); valores de retorno em chamadas de dependência (.NET 4.6).
 * Exceção dos valores do rastreamento de pilha.

[Saiba mais](https://apmtips.com/posts/2016-11-18-how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download-status-monitor"></a><a name="download"></a>Baixar o Status Monitor

- Usar o novo [módulo do PowerShell](./status-monitor-v2-overview.md)
- Baixar e executar o [instalador do status monitor](https://go.microsoft.com/fwlink/?LinkId=506648)
- Ou execute o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) e pesquise o Application Insights Status Monitor.

## <a name="next-steps"></a><a name="next"></a>Próximas etapas

Exiba sua telemetria:

* [Explore as métricas](../essentials/metrics-charts.md) para monitorar o desempenho e o uso
* [Pesquise eventos e logs][diagnostic] para diagnosticar problemas
* [Analise](../logs/log-query-overview.md) para obter mais consultas avançadas

Adicione mais telemetria:

* [Crie testes na Web][availability] para ter a certeza de que seu site continua ativo.
* [Adicione telemetria de cliente Web][usage] para ver exceções de código de página Web e permitir que você insira rastreamento de chamadas.
* [Adicione SDK do Application Insights ao seu código][greenbrown] para que você possa inserir o rastreamento de chamadas de log

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[greenbrown]: ./asp-net.md
[qna]: ../faq.md
[roles]: ./resources-roles-access-control.md
[usage]: ./javascript.md