---
title: "Corrigir os erros 502 Gateway Incorreto e 503 Serviço Indisponíveis | Microsoft Docs"
description: "Solucionar problemas dos erros \"502 Gateway Incorreto\" e \"503 Serviço Indisponível\" no seu aplicativo Web hospedado no Serviço de Aplicativo do Azure."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: top-support-issue
keywords: "502 Gateway Incorreto, 503 Serviço Indisponível, erro 503, erro 502"
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 397a6aaf7dc27adfa0fc0e722b8a2be5cc1d75f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Solucionar problemas de erros HTTP de "502 Gateway Incorreto" e "503 Serviço Indisponível" em seus Aplicativos Web do Azure
"502 Gateway Incorreto" e "503 Serviço Indisponível" são os erros comuns em seu aplicativo Web hospedado no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Este artigo ajuda você a solucionar esses erros.

Se você precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure MSDN e Excedente de Pilha](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter Suporte**.

## <a name="symptom"></a>Sintoma
Quando você navega até o aplicativo Web, ele retorna um erro de HTTP “502 Gateway Incorreto” ou um “503 Serviço Indisponível”.

## <a name="cause"></a>Causa
Esse problema geralmente é causado por questões no nível de aplicativo, como:

* solicitações demorando muito
* aplicativo usando muita memória/CPU
* aplicativo falhando devido a uma exceção.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Etapas de solução de problemas para resolver erros de "502 Gateway Incorreto" e "503 Serviço Indisponível"
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
Essa opção permite que você descubra se seu aplicativo está com problemas. Na folha de seu aplicativo Web, clique no bloco **Solicitações e erros** . A folha **Métrica** mostrará todas as métricas que você pode adicionar.

Algumas das métricas que deseja monitorar para seu aplicativo Web são

* Conjunto de trabalho de memória média
* Tempo médio de resposta
* Tempo de CPU
* Conjunto de trabalho de memória
* Solicitações

![monitorar aplicativo Web para solucionar problemas de erros HTTP de 502 Gateway Incorreto e 503 Serviço Indisponível](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Para obter mais informações, confira:

* [Monitorar aplicativos Web no Serviço de Aplicativo do Azure](web-sites-monitor.md)
* [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Coletar dados
#### <a name="use-the-azure-app-service-support-portal"></a>Usar o Portal de Suporte do Serviço de Aplicativo do Azure
Os Aplicativos Web fornecem a capacidade de solucionar problemas relacionados ao seu aplicativo Web examinando logs de HTTP, logs de eventos, despejos de processo e muito mais. Você pode acessar todas essas informações usando nosso portal de Suporte em **http://&lt;nome do aplicativo>.scm.azurewebsites.net/Support**

O portal de Suporte do Serviço de Aplicativo do Azure fornece três guias separadas para dar suporte às três etapas de um cenário de solução de problemas comum:

1. Observar o comportamento atual
2. Analisar por meio da coleta das informações de diagnóstico e da execução dos analisadores internos
3. Atenuar

Se o problema estiver ocorrendo no momento, clique em **Analisar** > **Diagnóstico** > **Diagnosticar Agora** para criar uma sessão de diagnóstico para você, que coletará logs de HTTP, logs do visualizador de eventos, despejos de memória, logs de erros de PHP e relatórios de processo de PHP.

Depois que os dados são coletados, ele também executará uma análise dos dados e fornecerá um relatório HTML.

Caso queira baixar os dados, por padrão, estariam armazenados na pasta D:\home\data\DaaS.

Para saber mais sobre o portal de Suporte do Serviço de Aplicativo do Azure, consulte [Novas atualizações para suporte de extensão de sites para sites do Azure](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Usar o Console de Depuração Kudu
Aplicativos Web vêm com um console de depuração que você pode usar para depuração, exploração, carregamento de arquivos, bem como para pontos de extremidade JSON para obter informações sobre seu ambiente. Chama-se *Console Kudu* ou *Painel SCM* de seu aplicativo Web.

Você pode acessar este painel acessando o link **https://&lt;Nome do aplicativo>.scm.azurewebsites.net/**.

Estas são algumas das coisas que o Kudu fornece:

* configurações de ambiente para seu aplicativo
* fluxo de logs
* despejos de diagnóstico
* console de depuração no qual você pode executar os cmdlets do Powershell e os comandos básicos de DOS.

Outro recurso útil do Kudu é que, caso seu aplicativo esteja lançando exceções de primeira chance, você pode usar o Kudu e o despejo de processo da ferramenta SysInternals para criar despejos de memória. Esses despejos de memória são instantâneos do processo e podem frequentemente ajudá-lo a solucionar problemas mais complexos com seu aplicativo Web.

Para saber mais sobre recursos disponíveis no Kudu, consulte [Ferramentas online de Sites do Azure que você deve conhecer](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Atenuar o problema
#### <a name="scale-the-web-app"></a>Escalar o aplicativo Web
No Serviço de Aplicativo do Azure, para um melhor desempenho e taxa de transferência, você pode ajustar a escala na qual você está executando seu aplicativo. Escalar verticalmente aplicativos Web envolve duas ações relacionadas: alterar seu plano do Serviço de Aplicativo para um tipo de preço mais alto e configurar determinadas configurações depois de ter mudado para o tipo de preço mais alto.

Para saber mais sobre como escalar, consulte [Escalar um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-scale.md).

Além disso, você pode optar por executar o aplicativo em mais de uma instância. Isso não apenas fornece mais capacidade de processamento, como também oferece alguma quantidade de tolerância a falhas. Se o processo falhar em uma instância, a outra instância ainda continuará atendendo a solicitações.

Você pode definir a escala para ser Manual ou Automática.

#### <a name="use-autoheal"></a>Usar AutoHeal
O AutoHeal recicla o processo de trabalho para seu aplicativo com base nas configurações que você escolher (como alterações de configuração, solicitações, limites baseados na memória ou o tempo necessário para executar uma solicitação). Na maioria das vezes, reciclar o processo é a maneira mais rápida de resolver um problema. Embora você possa sempre reiniciar o aplicativo Web diretamente no Portal do Azure, o AutoHeal fará isso automaticamente. Tudo que você precisa fazer é adicionar alguns gatilhos na raiz web.config de seu aplicativo Web. Observe que essas configurações devem funcionar da mesma forma ainda que seu aplicativo não seja um .Net.

Para saber mais, consulte [AutoHeal em sites do Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Reiniciar o aplicativo Web
Esta é geralmente a maneira mais simples de se recuperar de problemas de uso únicos. No [Portal do Azure](https://portal.azure.com/), na folha de seu aplicativo Web, existem as opções para parar ou reiniciar o aplicativo.

 ![reiniciar o aplicativo para solucionar os erros HTTP de 502 Gateway Incorreto e 503 Serviço Indisponível](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Você também pode gerenciar seu aplicativo Web usando o Azure Powershell. Para obter mais informações, consulte [Usando o PowerShell do Azure com o Azure Resource Manager](../powershell-azure-resource-manager.md).

