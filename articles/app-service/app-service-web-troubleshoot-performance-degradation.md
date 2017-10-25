---
title: "Desempenho lento do aplicativo Web no Serviço de Aplicativo | Microsoft Docs"
description: "Este artigo ajuda você a solucionar problemas de desempenho de aplicativo Web lento no Serviço de Aplicativo do Azure."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: top-support-issue
keywords: "desempenho do aplicativo Web, aplicativo lento, lentidão do aplicativo"
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: cephalin
ms.openlocfilehash: 1cfe7ec37ad8b24a8bd9ab2bf67e95675a57b675
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>Solucionar problemas de desempenho de aplicativo Web lento no Serviço de Aplicativo do Azure
Este artigo ajuda você a solucionar problemas de desempenho de aplicativo Web lento no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Se você precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure MSDN e Excedente de Pilha](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter Suporte**.

## <a name="symptom"></a>Sintoma
Quando você procura o aplicativo Web, as páginas carregam lentamente e, às vezes, o tempo limite é atingido.

## <a name="cause"></a>Causa
Esse problema geralmente é causado por questões no nível de aplicativo, como:

* solicitações de rede demorando muito
* código de aplicativo ou consultas de banco de dados sendo ineficientes
* aplicativo usando muita memória/CPU
* aplicativo falhando devido a uma exceção

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A solução de problemas pode ser dividida em três tarefas distintas, em ordem sequencial:

1. [Observar e monitorar o comportamento do aplicativo](#observe)
2. [Coletar dados](#collect)
3. [Atenuar o problema](#mitigate)

[Os Aplicativos Web do Serviço de Aplicativo](/services/app-service/web/) oferecem várias opções em cada etapa.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observar e monitorar o comportamento do aplicativo
#### <a name="track-service-health"></a>Controlar a integridade do serviço
O Microsoft Azure publica sempre que há uma degradação no desempenho ou interrupção do serviço. Você pode controlar a integridade do serviço no [Portal do Azure](https://portal.azure.com/). Para obter mais informações, confira [Controlar a integridade do serviço](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Monitorar seu aplicativo Web
Essa opção permite que você descubra se seu aplicativo está com problemas. Na folha de seu aplicativo Web, clique no bloco **Solicitações e erros** . A folha **Métrica** mostra todas as métricas que você pode adicionar.

Algumas das métricas que deseja monitorar para seu aplicativo Web são

* Conjunto de trabalho de memória média
* Tempo médio de resposta
* Tempo de CPU
* Conjunto de trabalho de memória
* Solicitações

![monitorar o desempenho do aplicativo Web](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Para obter mais informações, consulte:

* [Monitorar aplicativos Web no Serviço de Aplicativo do Azure](web-sites-monitor.md)
* [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Monitorar o status de ponto de extremidade da Web
Se você estiver executando seu aplicativo Web no tipo de preço **Standard**, Aplicativos Web permitem que você monitore dois pontos de extremidade de três locais geográficos.

O monitoramento de ponto de extremidade configura testes da web de locais distribuídos geograficamente que testa o tempo de resposta e tempo de atividade de URLs de web. O teste executa uma operação HTTP GET na URL da Web para determinar o tempo de resposta e o tempo de atividade de cada local. Cada local configurado executa um teste a cada cinco minutos.

O tempo de atividade é monitorado usando códigos de resposta HTTP e o tempo de resposta é medido em milissegundos. Um teste de monitoramento falhará se o código de resposta HTTP for maior ou igual a 400 ou se a resposta demorar mais de 30 segundos. Um ponto de extremidade será considerado disponível caso seus testes de monitoramento tenham êxito a partir de todos os locais especificados.

Para configurá-lo, veja [Monitorar aplicativos Web no Serviço de Aplicativo do Azure](web-sites-monitor.md).

Veja também um vídeo sobre monitoramento de pontos de extremidade em [Mantendo os sites do Azure ativos e monitorando pontos de extremidade - com Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow)

#### <a name="application-performance-monitoring-using-extensions"></a>Monitoramento de desempenho de aplicativos usando extensões
Você também pode monitorar o desempenho do aplicativo usando *extensões de site*.

Cada aplicativo Web do Serviço de Aplicativo fornece um ponto de extremidade de gerenciamento extensível que permite que você use um conjunto poderoso de ferramentas implantados como extensões de site. As extensões incluem: 

- Editores de código-fonte, como o [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Ferramentas de gerenciamento de recursos conectados, tais como um banco de dados MySQL conectado a um aplicativo Web.

O [Azure Application Insights](/services/application-insights/) e o [New Relic](/marketplace/partners/newrelic/newrelic/) são duas das extensões de site para monitoramento de desempenho que estão disponíveis. Para usar o New Relic, instale um agente em tempo de execução. Para usar o Application Insights do Azure, recrie seu código com um SDK. Você também pode instalar uma extensão que fornece acesso a dados adicionais. O SDK permite que você escreva código para monitorar o uso e o desempenho de seu aplicativo em mais detalhes.

Para usar o Application Insights, consulte [Monitorar o desempenho em aplicativos Web](../application-insights/app-insights-web-monitor-performance.md).

Consulte [Gerenciamento de desempenho do aplicativo New Relic nos sites do Azure](../store-new-relic-cloud-services-dotnet-application-performance-management.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Coletar dados
O ambiente do Aplicativos Web oferece funcionalidade de diagnóstico para registro em log tanto de informações do servidor Web quanto do aplicativo Web. As informações estão separadas em diagnóstico de servidor Web e diagnóstico de aplicativos.

#### <a name="enable-web-server-diagnostics"></a>Habilitar o diagnóstico de servidor Web
Você pode habilitar ou desabilitar os seguintes tipos de logs:

* **Registro em Log Detalhado de Erros** - informações detalhadas de erros para códigos de status HTTP que indiquem uma falha (código de status 400 ou superior). Isto pode conter informações que podem ajudar a determinar por que o servidor retornou o código de erro.
* **Falha no Rastreamento de Solicitação** - informações detalhadas sobre solicitações com falha, incluindo um rastreamento dos componentes IIS usados para processar a solicitação e o tempo levado em cada componente. Isto pode ser útil se você está tentando melhorar o desempenho do aplicativo Web ou isolar o que está causando um erro específico de HTTP.
* **Registro em Log de Servidor Web** - informações sobre transações HTTP usando o formato de arquivo de log estendido W3C. Este é útil para determinar as métricas gerais do aplicativo Web, como o número de solicitações manipuladas e quantas solicitações existem vindas de um endereço IP específico.

#### <a name="enable-application-diagnostics"></a>Habilitar o diagnóstico de aplicativos
Há várias opções para coletar dados de desempenho do aplicativo de Aplicativos Web, criar o perfil do aplicativo em tempo real por meio do Visual Studio ou modificar o código do aplicativo para registrar mais informações e rastreamentos em log. Você pode escolher as opções com base no nível de acesso que tem ao aplicativo e no que observou por meio das ferramentas de monitoramento.

##### <a name="use-application-insights-profiler"></a>Use o Application Insights Profiler
Você pode habilitar o Application Insights Profiler para iniciar a captura de rastreamentos de desempenho detalhados. Quando é necessário investigar problemas ocorridos no passado, você pode acessar os rastreamentos até cinco dias atrás. Você pode escolher essa opção, contanto que tenha acesso ao recurso do Application Insights do aplicativo Web no Portal do Azure.

O Application Insights Profiler fornece estatísticas em tempo de resposta para cada chamada Web e os rastreamentos que indicam qual linha de código causou as respostas lentas. Às vezes, o aplicativo de Serviço de Aplicativo está lento porque determinados códigos não são gravados de uma maneira mais eficaz. Exemplos incluem um código sequencial que pode ser executado em contenções de bloqueio do banco de dados paralelas e indesejadas. Remover esses afunilamentos no código aumenta o desempenho do aplicativo, mas eles são difíceis de detectar sem configurar logs e rastreamentos elaborados. Os rastreamentos coletados pelo Application Insights Profiler ajudam a identificar as linhas de código que deixam o aplicativo lento e a superar esse desafio para os aplicativos do Serviço de Aplicativo.

 Para obter mais informações, consulte [Criação de perfil de aplicativos Web do Azure ativos com o Application Insights](../application-insights/app-insights-profiler.md).

##### <a name="use-remote-profiling"></a>Usar a criação de perfil remota
No Serviço de Aplicativo do Azure, Aplicativos Web, Aplicativos de API e WebJobs podem ter perfis criados remotamente. Escolha esta opção se você tem acesso ao recurso de aplicativo Web e sabe como reproduzir o problema ou se você sabe o intervalo de hora exata em que o problema de desempenho ocorre.

A criação de perfil remota é útil se o uso da CPU do processo é alto e o processo está sendo executado mais lentamente do que o esperado ou se a latência de solicitações HTTP estão acima do normal; você pode criar remotamente o perfil de seu processo e obter as pilhas de chamadas de amostragem de CPU para analisar a atividade do processo e afunilamentos de código.

Para obter mais informações, consulte [Suporte à criação de perfil remota no Serviço de Aplicativo do Azure](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Configurar manualmente os rastreamentos de diagnóstico
Se você tem acesso ao código-fonte do aplicativo Web, o Diagnóstico de Aplicativos permite que você capture informações produzidas por um aplicativo Web. Os aplicativos ASP.NET podem usar a classe `System.Diagnostics.Trace` para registrar informações no log de diagnóstico do aplicativo. No entanto, você precisa alterar o código e reimplantar o aplicativo. Esse método é recomendado se o aplicativo está sendo executado em um ambiente de teste.

Para obter instruções detalhadas sobre como configurar seu aplicativo para registro em log, consulte [Habilitar o log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure](web-sites-enable-diagnostic-log.md).

#### <a name="use-the-azure-app-service-support-portal"></a>Usar o portal de suporte do Serviço de Aplicativo do Azure
Os Aplicativos Web fornecem a capacidade de solucionar problemas relacionados ao seu aplicativo Web examinando logs de HTTP, logs de eventos, despejos de processo e muito mais. Você pode acessar todas essas informações usando nosso portal de Suporte em **http://&lt;nome do aplicativo>.scm.azurewebsites.net/Support**

O portal de suporte do Serviço de Aplicativo do Azure fornece três guias separadas para dar suporte às três etapas de um cenário de solução de problemas comum:

1. Observar o comportamento atual
2. Analisar por meio da coleta das informações de diagnóstico e da execução dos analisadores internos
3. Atenuar

Se o problema estiver ocorrendo no momento, clique em **Analisar** > **Diagnóstico** > **Diagnosticar Agora** para criar uma sessão de diagnóstico para você, a qual coletará logs de HTTP, logs do visualizador de eventos, despejos de memória, logs de erros de PHP e relatórios de processo de PHP.

Depois que os dados forem coletados, ela também executará uma análise dos dados e fornecerá um relatório HTML.

Caso queira baixar os dados, por padrão, estariam armazenados na pasta D:\home\data\DaaS.

Para saber mais sobre o portal de suporte do Serviço de Aplicativo do Azure, consulte [Novas atualizações para suporte de extensão de sites para sites do Azure](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Usar o Console de Depuração Kudu
Aplicativos Web vêm com um console de depuração que você pode usar para depuração, exploração, carregamento de arquivos, bem como para pontos de extremidade JSON para obter informações sobre seu ambiente. Este console chama-se *Console Kudu* ou *Painel SCM* de seu aplicativo Web.

Você pode acessar este painel acessando o link **https://&lt;Nome do aplicativo>.scm.azurewebsites.net/**.

Estas são algumas das coisas que o Kudu fornece:

* configurações de ambiente para seu aplicativo
* fluxo de logs
* despejos de diagnóstico
* console de depuração no qual você pode executar os cmdlets do Powershell e os comandos básicos de DOS.

Outro recurso útil do Kudu é que, caso seu aplicativo esteja lançando exceções de primeira chance, você pode usar o Kudu e o despejo de processo da ferramenta SysInternals para criar despejos de memória. Esses despejos de memória são instantâneos do processo e podem frequentemente ajudá-lo a solucionar problemas mais complexos com seu aplicativo Web.

Para saber mais sobre os recursos disponíveis no Kudu, veja [Ferramentas online do Azure Websites Team Services que você precisa conhecer](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Atenuar o problema
#### <a name="scale-the-web-app"></a>Escalar o aplicativo Web
No Serviço de Aplicativo do Azure, para um melhor desempenho e taxa de transferência, você pode ajustar a escala na qual você está executando seu aplicativo. Escalar verticalmente aplicativos Web envolve duas ações relacionadas: alterar seu plano do Serviço de Aplicativo para um tipo de preço mais alto e configurar determinadas configurações depois de ter mudado para o tipo de preço mais alto.

Para saber mais sobre como escalar, consulte [Escalar um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-scale.md).

Além disso, você pode optar por executar o aplicativo em mais de uma instância. Escalar horizontalmente não apenas fornece mais capacidade de processamento, como também oferece alguma quantidade de tolerância a falhas. Se o processo falhar em uma instância, as outras instâncias ainda continuarão atendendo a solicitações.

Você pode definir a escala para ser Manual ou Automática.

#### <a name="use-autoheal"></a>Usar AutoHeal
O AutoHeal recicla o processo de trabalho para seu aplicativo com base nas configurações que você escolher (como alterações de configuração, solicitações, limites baseados na memória ou o tempo necessário para executar uma solicitação). Na maioria das vezes, reciclar o processo é a maneira mais rápida de resolver um problema. Embora você possa sempre reiniciar o aplicativo Web diretamente no Portal do Azure, o AutoHeal faz isso automaticamente. Tudo que você precisa fazer é adicionar alguns gatilhos na raiz web.config de seu aplicativo Web. Essas configurações devem funcionar da mesma forma ainda que seu aplicativo não seja um .Net.

Para saber mais, consulte [AutoHeal em sites do Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Reiniciar o aplicativo Web
Reiniciar é geralmente a maneira mais simples de se recuperar de problemas de uso únicos. No [Portal do Azure](https://portal.azure.com/), na folha de seu aplicativo Web, existem as opções para parar ou reiniciar o aplicativo.

 ![reinicie o aplicativo Web para solucionar problemas de desempenho](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Você também pode gerenciar seu aplicativo Web usando o Azure Powershell. Para obter mais informações, consulte [Usando o PowerShell do Azure com o Azure Resource Manager](../powershell-azure-resource-manager.md).
