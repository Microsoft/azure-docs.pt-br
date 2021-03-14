---
title: Solucionar problemas com o Aplicativo Azure insights Profiler
description: Este artigo apresenta as etapas e as informações de solução de problemas para ajudar os desenvolvedores a habilitar e usar Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 622a83c6d91bf2a30c2844e3279d6fd4b89d429f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213786"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Solucionar problemas ao habilitar ou exibir o Profiler do Application Insights

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Solução de problemas gerais

### <a name="make-sure-youre-using-the-appropriate-profiler-endpoint"></a>Verifique se você está usando o ponto de extremidade do criador de perfil apropriado

Atualmente, as únicas regiões que exigem modificações de ponto de extremidade são o [Azure governamental](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) e o [Azure China](https://docs.microsoft.com/azure/china/resources-developer-guide).

|Configurações de Aplicativo    | Nuvem do governo dos EUA | Nuvem da China |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Os perfis são carregados apenas se há solicitações para seu aplicativo enquanto o Profiler está em execução

Aplicativo Azure o criador de perfil de informações coleta dados por dois minutos a cada hora. Ele também pode coletar dados quando você seleciona o botão **perfil agora** no painel **Configurar Application insights Profiler** .

> [!NOTE]
> Os dados de criação de perfil são carregados somente quando podem ser anexados a uma solicitação que ocorreu enquanto o criador de perfil estava em execução. 

O Profiler grava mensagens de rastreamento e eventos personalizados no recurso do Application Insights. Você pode usar esses eventos para ver como o profiler está em execução:

1. Procure por mensagens de rastreamento e eventos personalizados enviados pelo Profiler para o recurso Application Insights. Você pode usar essa cadeia de caracteres de pesquisa para encontrar os dados relevantes:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    A imagem a seguir exibe dois exemplos de pesquisas de dois recursos de IA: 
    
   * No exemplo à esquerda, o aplicativo não está recebendo solicitações enquanto o Profiler está em execução. A mensagem explica que o upload foi cancelado devido à ausência de atividade. 

   * No exemplo à direita, você pode ver que o Profiler iniciou e enviou eventos personalizados quando detectou solicitações que ocorreram enquanto o Profiler estava em execução. Se o `ServiceProfilerSample` evento personalizado for exibido, significa que um perfil foi capturado e está disponível no painel de **desempenho Application insights** .

     Se nenhum registro for exibido, o criador de perfil não estará em execução. Para solucionar problemas, confira as seções de solução de problemas para o seu tipo de aplicativo específico posteriormente neste artigo.  

     ![Pesquisar por telemetria do Profiler][profiler-search-telemetry]

### <a name="other-things-to-check"></a>Outros itens a serem verificados
* Verifique se o aplicativo está sendo executado no .NET Framework 4.6.
* Se seu aplicativo web é um aplicativo ASP.NET Core, deve estar executando pelo menos ASP.NET Core 2.0.
* Se os dados que você está tentando exibir forem mais antigos do que duas semanas, tente limitar seu filtro de tempo e tente novamente. Rastreamentos são excluídos após sete dias.
* Verifique se os proxies ou um firewall não bloqueou o acesso ao https://gateway.azureserviceprofiler.net .
* O criador de perfil não tem suporte em planos de serviço de aplicativo gratuitos ou compartilhados. Se você estiver usando um desses planos, tente escalar verticalmente para um dos planos básicos e o criador de perfil deve começar a funcionar.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Dois segmentos paralelos de contagem

Em alguns casos, a métrica de tempo toral no visualizador de pilha é maior que a duração da solicitação.

Essa situação pode ocorrer quando dois ou mais threads paralelos são associados a uma solicitação. Nesse caso, o tempo total de thread é maior que o tempo decorrido.

Um thread pode estar aguardando até que o outro seja concluído. O visualizador tenta detectar essa situação e omite a espera desinteressante. Ao fazê-lo, ele erra ao exibir muitas informações em vez de omitir o que poderiam ser informações críticas.

Quando você vir threads paralelos em seus rastreamentos, determine quais threads estão aguardando para que você possa identificar o caminho quente para a solicitação.

Geralmente, o thread rapidamente entra em estado de espera é simplesmente aguardando outros threads. Concentre-se nos outros threads e ignore o tempo nos threads em espera.

### <a name="error-report-in-the-profile-viewer"></a>Relatório de erros no visualizador de perfil
Envie um tíquete de suporte no portal. Certifique-se de incluir uma ID de correlação da mensagem de erro.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Solucionar problemas do Profiler no Serviço de Aplicativo do Azure

Para o Profiler funcionar corretamente:
* Seu plano de serviço de aplicativo da web deve ser de nível Básico ou superior.
* Seu aplicativo da web deve ter o Application Insights ativado.
* Seu aplicativo Web deve ter as seguintes configurações de aplicativo:

    |Configurações de Aplicativo    | Valor    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para seu recurso do Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* O trabalho Web **ApplicationInsightsProfiler3** precisa estar em execução. Para verificar o trabalho Web:
   1. Vá para o [Kudu](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret).
   1. No menu **Ferramentas**, selecione **Painel do WebJobs**.  
      O painel **WebJobs** é aberto. 
   
      ![Captura de tela mostra o painel trabalhos Web, que exibe o nome, o status e a hora da última execução dos trabalhos.][profiler-webjob]   
   
   1. Para exibir os detalhes do webjob, incluindo o log, selecione o link **ApplicationInsightsProfiler3** .  
     O painel **Detalhes do WebJob Contínuo**.

      ![Captura de tela mostra o painel de detalhes do WebJob contínuo.][profiler-webjob-log]

Se o profiler não estiver funcionando para você, você poderá baixar o log e enviá-lo para nossa equipe para obter assistência serviceprofilerhelp@microsoft.com .

### <a name="check-the-diagnostic-services-site-extension-status-page"></a>Verificar a página de status da extensão de site dos serviços de diagnóstico
Se o criador de perfil foi habilitado por meio do [painel de Application insights](profiler.md) no portal, ele foi habilitado pela extensão de site dos serviços de diagnóstico.

> [!NOTE]
> A instalação sem código do Application Insights Profiler segue a política de suporte do .NET Core.
> Para obter mais informações sobre tempos de execução com suporte, consulte [política de suporte do .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

Você pode verificar a página de status dessa extensão indo para a seguinte URL: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> O domínio do link da página de status irá variar dependendo da nuvem.
Esse domínio será o mesmo que o site de gerenciamento do kudu para o serviço de aplicativo.

Esta página de status mostra o estado de instalação do criador de perfil e dos agentes de Snapshot Collector. Se houver um erro inesperado, ele será exibido e mostrará como corrigi-lo.

Você pode usar o site de gerenciamento do kudu para o serviço de aplicativo para obter a URL base desta página de status:
1. Abra seu aplicativo de Serviço de Aplicativo no portal do Azure.
2. Selecione **ferramentas avançadas** ou procure **kudu**.
3. Selecione **Ir**.
4. Quando você estiver no site de gerenciamento do kudu, na URL, **acrescente o seguinte `/DiagnosticServices` e pressione Enter**.
 Ele terminará da seguinte maneira: `https://<kudu-url>/DiagnosticServices`

Ele exibirá uma página de status semelhante à seguinte: ![ página de status dos serviços de diagnóstico](./media/diagnostic-services-site-extension/status-page.png)
    
### <a name="manual-installation"></a>Instalação manual

Quando você configura o Profiler, são feitas atualizações nas configurações do aplicativo Web. Será possível aplicar as atualizações manualmente se o ambiente exigir. Um exemplo pode ser que seu aplicativo está sendo executado em um ambiente de Aplicativos Web para o PowerApps. Para aplicar atualizações manualmente:

1. No painel de **controle do aplicativo Web** , abra **configurações**.

1. Defina **.NET Framework versão** como **v 4.6**.

1. Defina **Sempre ativo** como **Ativado**.
1. Crie estas configurações de aplicativo:

    |Configurações de Aplicativo    | Valor    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para seu recurso do Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>Número excessivo de sessões de criação de perfil ativas

Você pode habilitar o Profiler em um máximo de quatro aplicativos Web em execução no mesmo plano de serviço. Se você tiver mais de quatro, o profiler poderá gerar um *Microsoft. Createprofileer. Exceptions. TooManyETWSessionException*. Para solucioná-lo, mova alguns aplicativos Web para um plano de serviço diferente.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Erro de implantação: diretório não vazio 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Se estiver reimplementando seu aplicativo Web em um recurso de Aplicativos Web com o Profiler habilitado, você poderá ver a seguinte mensagem:

*Diretório não vazio ' d \\ : \\ site inicial \\ wwwroot \\ App_Data \\ trabalhos '*

Esse erro ocorrerá se você executar Implantação da Web de scripts ou do Azure Pipelines. A solução é adicionar os seguintes parâmetros de implantação à tarefa de Implantação da Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Esses parâmetros excluem a pasta usada pelo Application Insights Profiler e desbloqueiam o processo de reimplantação. Eles não afetam a instância do Profiler em execução no momento.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Como fazer para determinar se o Application Insights Profiler está em execução?

O Profiler é executado como um trabalho Web contínuo no aplicativo Web. Você pode abrir o recurso de aplicativo Web no [portal do Azure](https://portal.azure.com). Se o painel **WebJobs**, verifique o status de **ApplicationInsightsProfiler**. Se ele não estiver em execução, abra **Logs** para obter mais informações.

## <a name="troubleshoot-vms-and-cloud-services"></a>Solucionar problemas de VMs e serviços de nuvem

>**O bug no criador de perfil que acompanha o WAD para serviços de nuvem foi corrigido.** A versão mais recente do WAD (1.12.2.0) para serviços de nuvem funciona com todas as versões recentes do SDK do App insights. Os hosts do serviço de nuvem atualizarão automaticamente o WAD, mas não será imediato. Para forçar uma atualização, você pode reimplantar o serviço ou reinicializar o nó.

Para ver se o criador de perfil está configurado corretamente pelo Diagnóstico do Azure, siga as etapas abaixo: 
1. Verifique se o conteúdo da configuração de Diagnóstico do Azure implantada é o que você espera. 

1. Segundo, verifique se o Diagnóstico do Azure passa a iKey adequada na linha de comando do Profiler. 

1. Terceiro, verifique o arquivo de log do Profiler para ver se o Profiler foi executado, mas retornou um erro. 

Para verificar as configurações que foram usadas para configurar o Diagnóstico do Azure:

1. Entre na máquina virtual (VM) e, em seguida, abra o arquivo de log neste local. A versão do plug-in pode ser mais nova no seu computador.
    
    Para VMs:
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    Para Serviços de Nuvem:
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. No arquivo, você pode pesquisar pela cadeia de caracteres **WadCfg** para encontrar as configurações que foram passadas para a VM para configurar o Diagnóstico do Azure. Você pode verificar se a iKey utilizado pelo coletor do Profiler está correta.

1. Verifique a linha de comando que é usada para iniciar o Profiler. Os argumentos que são usados para iniciar o profiler estão no arquivo a seguir. (A unidade pode ser c: ou d: e o diretório pode estar oculto.)

    Para VMs:
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    para serviços de nuvem:
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Verifique se o iKey na linha de comando do Profiler está correto. 

1. Usando o caminho encontrado noconfig.jsanterior *no* arquivo, verifique o arquivo de log do criador de perfil, chamado **bootstrapn. log**. Ele exibe as informações de depuração que indicam as configurações que o Profiler está usando. Ele também exibe mensagens de erro e de status do Profiler.  

    Para VMs, o arquivo está aqui:
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Para Serviços de Nuvem:
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Se o profiler estiver em execução enquanto seu aplicativo estiver recebendo solicitações, a seguinte mensagem será exibida: *atividade detectada em iKey*. 

    Quando o rastreamento está sendo carregado, a seguinte mensagem é exibida: *Iniciar para carregar o rastreamento*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Editar regras de firewall ou proxy de rede

Se seu aplicativo se conectar à Internet por meio de um proxy ou um firewall, talvez seja necessário atualizar as regras para se comunicar com o serviço do criador de perfil.

Os IPs usados pelo Application Insights Profiler são incluídos na marca de serviço do Azure Monitor. Para obter mais informações, consulte a [documentação de marcas de serviço](../../virtual-network/service-tags-overview.md).


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png