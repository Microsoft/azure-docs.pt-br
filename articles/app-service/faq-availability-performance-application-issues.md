---
title: Perguntas frequentes sobre desempenho de aplicativo – Serviço de Aplicativo do Azure | Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre disponibilidade, desempenho e problemas de aplicativo na funcionalidade de Aplicativos Web do Serviço de Aplicativo do Azure.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: ae59f54de9c02bfc4e538dd10c809ba398d599ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61222212"
---
> [!NOTE]
> Alguns das diretrizes abaixo podem funcionar apenas no Windows ou Linux App Services. Por exemplo, Linux App Services executado no modo de 64 bits por padrão.
>

# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Perguntas frequentes do desempenho do aplicativo para Aplicativos Web no Azure

Este artigo apresenta respostas para perguntas frequentes (FAQs) sobre problemas de desempenho de aplicativo da [funcionalidade Aplicativos Web do Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Por que o meu aplicativo está lento?

Vários fatores podem contribuir para diminuir o desempenho do aplicativo. Para obter as etapas detalhadas de solução de problemas, consulte [Solucionar problemas de desempenho lento do aplicativo Web](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Como solucionar problemas de um cenário de alto consumo de CPU?

Em alguns cenários de alto consumo de CPU, seu aplicativo pode exigir realmente mais recursos de computação. Nesse caso, considere a possibilidade de dimensionar para uma camada de serviço superior para que o aplicativo obtenha todos os recursos necessários. Outras vezes, o alto consumo de CPU pode ser causado por um loop ou uma prática de codificação incorretos. Obter informações sobre o que está causando o aumento de consumo da CPU é um processo de duas partes. Primeiro, crie um despejo de processo e, em seguida, analise o despejo de processo. Para obter mais informações, consulte [Capturar e analisar um arquivo de despejo para alto consumo de CPU para aplicativos Web](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Como solucionar problemas de um cenário de alto consumo de memória?

Em alguns cenários de alto consumo de memória, seu aplicativo pode exigir realmente mais recursos de computação. Nesse caso, considere a possibilidade de dimensionar para uma camada de serviço superior para que o aplicativo obtenha todos os recursos necessários. Outras vezes, um bug no código pode causar um vazamento de memória. Uma prática de codificação também pode aumentar o consumo de memória. Obter informações sobre o que está causando o aumento de consumo da memória é um processo de duas partes. Primeiro, crie um despejo de processo e, em seguida, analise o despejo de processo. Diagnóstico de falha da Galeria de extensão de Site do Azure pode executar com eficiência as seguintes etapas. Para obter mais informações, consulte [Capturar e analisar um arquivo de despejo de memória alta intermitente para aplicativos Web](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Como automatizar a Aplicativos Web do Serviço de Aplicativo usando o PowerShell?

Você pode usar os cmdlets do PowerShell para gerenciar e manter os aplicativos Web do Serviço de Aplicativo. Em nossa postagem do blog [Automatizar os aplicativos Web hospedados no Serviço de Aplicativo do Azure usando o PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/), descrevemos como usar Azure Resource Manager com base nos cmdlets do PowerShell para automatizar tarefas comuns. A postagem do blog também tem o código de exemplo para várias tarefas de gerenciamento de aplicativos web. Para obter descrições e sintaxe para todos os cmdlets de aplicativos Web do Serviço de Aplicativo, consulte [Az.Websites](/powershell/module/az.websites).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Como exibir os logs de eventos do aplicativo web?

Para exibir logs de eventos do aplicativo web:

1. Entre no seu [site Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. No menu, selecione **Console de depuração** > **CMD**.
3. Abra a pasta **LogFiles**.
4. Para exibir logs de eventos, selecione o ícone de lápis ao lado de **eventlog.xml**.
5. Para baixar os logs, execute o cmdlet do PowerShell `Save-AzureWebSiteLog -Name webappname`.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Como capturar um despejo de memória do modo de usuário do meu aplicativo web?

Para capturar um despejo de memória do modo de usuário do seu aplicativo web:

1. Entre no seu [site Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Selecione o menu **Process Explorer**.
3. Clique com botão direito no processo **w3wp.exe** ou no seu processo WebJob.
4. Selecione **Baixar o despejo de memória** > **Despejo completo**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Como exibir informações de nível de processo para meu aplicativo web?

Você tem duas opções para exibir as informações de nível de processo para seu aplicativo web:

*   No Portal do Azure:
    1. Abra o **Process Explorer** para o aplicativo web.
    2. Para ver os detalhes, selecione o **w3wp.exe** processo.
*   No console do Kudu:
    1. Entre no seu [site Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
    2. Selecione o menu **Process Explorer**.
    3. Para o processo **w3wp.exe**, selecione **Propriedades**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Ao navegar pelo meu aplicativo, vejo "Erro 403 - Este aplicativo web foi interrompido." Como resolver isso?

Três condições podem causar esse erro:

* O aplicativo web atingiu um limite de cobrança e seu site foi desabilitado.
* O aplicativo web foi interrompido no portal.
* O aplicativo web atingiu um limite de cota de recursos que pode se aplicar a um plano de serviço de escala gratuito ou compartilhado.

Para ver o que está causando o erro e resolver o problema, siga as etapas em [Aplicativos Web: "Erro 403 – este aplicativo Web foi interrompido"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Onde posso obter mais informações sobre cotas e limites de vários planos do Serviço de Aplicativo?

Para obter informações sobre cotas e limites, consulte [limites de Serviço de Aplicativo](../azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Como diminuir o tempo de resposta para a primeira solicitação após o tempo ocioso?

Por padrão, os aplicativos Web serão descarregados se estiverem ociosos por um período de tempo definido. Dessa forma, o sistema pode conservar recursos. A desvantagem é que a resposta à primeira solicitação depois que o aplicativo web é descarregado é mais longa, para permitir que o aplicativo web carregue e comece a enviar respostas. Em planos de serviço básico e padrão, você pode ativar a configuração **Sempre Ativo** para manter o aplicativo sempre carregado. Isso elimina tempos de carga após o aplicativo estar ocioso. Para alterar a configuração **Sempre Ativo**:

1. No portal do Azure, vá para seu aplicativo web.
2. Selecione **Configurações do aplicativo**.
3. Para **Sempre Ativo**, selecione **On**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Como ativar o rastreamento de solicitação com falha?

Para ativar o rastreamento de solicitação com falha:

1. No portal do Azure, vá para seu aplicativo web.
3. Selecione **Todas as configurações** > **Logs de diagnóstico**.
4. Para **Rastreamento de solicitação com falha**, selecione **On**.
5. Clique em **Salvar**.
6. Na folha de aplicativo Web, selecione **Ferramentas**.
7. Selecione **Visual Studio Online**.
8. Se a configuração não estiver **On**, selecione **On**.
9. Selecione **Ir**.
10. Selecione **Web.config**.
11. No system.webServer, adicione essa configuração (para capturar uma URL específica):

    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. Para solucionar problemas de desempenho lento, adicione essa configuração (se a solicitação de captura está demorando mais de 30 segundos):
    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. Para baixar os rastreamentos de solicitação com falha, no [portal](https://portal.azure.com), vá para seu site.
15. Selecione **Ferramentas** > **Kudu** > **Go**.
18. No menu, selecione **Console de depuração** > **CMD**.
19. Selecione a pasta **LogFiles** e, em seguida, selecione a pasta com um nome que começa com **W3SVC**.
20. Para ver o arquivo XML, selecione o ícone de lápis.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>É exibida a mensagem "O processo de trabalho solicitou reciclagem devido ao limite de 'Porcentagem de memória'." Como posso solucionar esse problema?

A quantidade máxima disponível de memória para um processo de 32 bits (mesmo em um sistema operacional de 64 bits) é 2 GB. Por padrão, o processo de trabalho é definido para 32 bits no Serviço de Aplicativo (para compatibilidade com aplicativos web herdada).

Considere alterar processos de 64 bits, de forma que você possa tirar proveito da memória adicional disponível em sua função Web Worker. Isso dispara uma reinicialização do aplicativo web, portanto agende adequadamente.

Observe também que um ambiente de 64 bits requer um plano de serviço básico ou padrão. Planos gratuito ou compartilhado são sempre executados em um ambiente de 32 bits.

Para obter mais informações, consulte [Configurar aplicativos web no Serviço de Aplicativo](web-sites-configure.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Por que minha solicitação atinge o tempo limite após 230 segundos?

O Azure Load Balancer tem uma configuração de tempo limite de ociosidade padrão de quatro minutos. Isso geralmente é um limite de tempo de resposta razoável para uma solicitação da web. Se seu aplicativo web requer o processamento em segundo plano, é recomendável usar o Azure WebJobs. O aplicativo web do Azure pode chamar WebJobs e ser notificado quando o processamento em segundo plano estiver concluído. Você pode escolher entre vários métodos para usar WebJobs, inclusive filas e gatilhos.

O WebJobs foi projetado para processamento em segundo plano. Você pode fazer quantos processamentos em segundo plano desejar em um WebJob. Para obter mais informações sobre WebJobs, consulte [Executar tarefas em segundo plano com o WebJobs](webjobs-create.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>Aplicativos ASP.NET Core que são hospedados no Serviço de Aplicativo, às vezes, param de responder. Como faço para corrigir esse problema?

Um problema conhecido com uma [versão Kestrel](https://github.com/aspnet/KestrelHttpServer/issues/1182) anterior pode fazer com que um aplicativo do ASP.NET Core 1.0 que está hospedado no Serviço de Aplicativo interrompa as respostas intermitentemente. Você também pode ver esta mensagem: "O aplicativo CGI especificado encontrou um erro e o servidor encerrou o processo."

Esse problema foi corrigido no Kestrel versão 1.0.2. Esta versão está incluída na atualização do ASP.NET Core 1.0.3. Para resolver esse problema, certifique-se de atualizar suas dependências de aplicativo para usar o Kestrel 1.0.2. Como alternativa, você pode usar uma das duas soluções alternativas que são descritas na postagem do blog [Problemas de desempenho lento do ASP.NET Core 1.0 em aplicativos web do Serviço de Aplicativo](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Não é possível localizar meus arquivos de log na estrutura de arquivos do meu aplicativo web. Como posso encontrá-los?

Se você usar o recurso de Cache Local do Serviço de Aplicativo, a estrutura de pasta de arquivos de log e das pastas de dados para a instância do Serviço de Aplicativo serão afetados. Quando o Cache Local é usado, as subpastas são criadas nas pastas de dados e arquivos de log de armazenamento. As subpastas usam padrão de nomenclatura "identificador exclusivo" + carimbo de data/hora. Cada uma das subpastas corresponde a uma instância de VM na qual o aplicativo Web está executando ou foi executado.

Para determinar se você está usando o Cache Local, verifique sua guia **Configurações de aplicativo** do Serviço de Aplicativo. Se o Cache Local está sendo usado, a configuração de aplicativo `WEBSITE_LOCAL_CACHE_OPTION` é definida como `Always`.

Se você não estiver usando o Cache Local e estiver enfrentando esse problema, envie uma solicitação de suporte.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>É exibida a mensagem "Foi feita uma tentativa para acessar um soquete de uma maneira proibida pelas permissões de acesso." Como resolver isso?

Esse erro normalmente ocorre se as conexões TCP de saída na instância de VM são esgotadas. No Serviço de Aplicativo, os limites são aplicados para o número máximo de conexões de saída que podem ser feitas para cada instância VM. Para obter mais informações, consulte [limites numéricos entre-VM](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Esse erro também pode ocorrer se você tentar acessar um endereço local do seu aplicativo. Para obter mais informações, consulte [Solicitações de endereço local](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Para obter mais informações sobre conexões de saída em seu aplicativo web, consulte a postagem de blog sobre [conexões de saída a sites do Azure](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Como usar o Visual Studio para depuração remota do o meu aplicativo web do Serviço de Aplicativo?

Para obter uma explicação detalhada que mostra como depurar seu aplicativo web usando o Visual Studio, consulte [Depuração remota do seu aplicativo web do Serviço de Aplicativo](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
