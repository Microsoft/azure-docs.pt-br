---
title: Corrigir os erros HTTP 502 e HTTP 503
description: Solucionar problemas dos erros "502 Gateway Incorreto" e "503 Serviço Indisponível" no seu aplicativo hospedado no Serviço de Aplicativo do Azure.
tags: top-support-issue
keywords: 502 Gateway Incorreto, 503 Serviço Indisponível, erro 503, erro 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 801f4840d0d1447f839e5c2c4bc3168741b62b63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586247"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Solucionar problemas de erros HTTP de "502 Gateway Incorreto" e "503 Serviço Indisponível" em seu Serviço de Aplicativo do Azure
"502 Gateway Incorreto" e "503 Serviço Indisponível" são os erros comuns em seu aplicativo hospedado no [Serviço de Aplicativo do Azure](./overview.md). Este artigo ajuda você a solucionar esses erros.

Se você precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure MSDN e Excedente de Pilha](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter Suporte**.

## <a name="symptom"></a>Sintoma
Quando você navega até o aplicativo, ele retorna um erro de HTTP “502 Gateway Incorreto” ou um “503 Serviço Indisponível”.

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

O [Serviço de Aplicativo](overview.md) oferece diversas opções em cada etapa.

<a name="observe"></a>

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observar e monitorar o comportamento do aplicativo
#### <a name="track-service-health"></a>Controlar a integridade do serviço
O Microsoft Azure publica sempre que há uma degradação no desempenho ou interrupção do serviço. Você pode controlar a integridade do serviço no [Portal do Azure](https://portal.azure.com/). Para obter mais informações, confira [Controlar a integridade do serviço](../service-health/service-notifications.md).

#### <a name="monitor-your-app"></a>Monitorar o aplicativo
Essa opção permite que você descubra se seu aplicativo está com problemas. Na folha do seu aplicativo, clique no bloco **Solicitações e erros**. A folha **Métrica** mostrará todas as métricas que você pode adicionar.

Algumas das métricas que você talvez queira monitorar para o aplicativo são

* Conjunto de trabalho de memória média
* Tempo médio de resposta
* Tempo de CPU
* Conjunto de trabalho de memória
* Requests

![monitorar aplicativo para solucionar problemas de erros HTTP de 502 Gateway Incorreto e 503 Serviço Indisponível](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Para obter mais informações, consulte:

* [Monitorar aplicativos no Serviço de Aplicativo do Azure](web-sites-monitor.md)
* [Receber notificações de alerta](../azure-monitor/alerts/alerts-overview.md)

<a name="collect"></a>

### <a name="2-collect-data"></a>2. Coletar dados
#### <a name="use-the-diagnostics-tool"></a>Use a ferramenta de diagnóstico
O Serviço de Aplicativo fornece uma experiência inteligente e interativa para ajudar você a solucionar problemas do seu aplicativo, sem a necessidade de configuração. Quando tiver problemas com o aplicativo, a ferramenta de diagnóstico indicará o que está errado para direcioná-lo às informações certas para solucionar o problema com mais facilidade e rapidez.

Para acessar o diagnóstico do Serviço de Aplicativo, navegue até seu aplicativo Serviço de Aplicativo ou Ambiente do Serviço de Aplicativo no [Portal do Azure](https://portal.azure.com). No painel de navegação esquerdo, clique em **Diagnosticar e resolver problemas**.

#### <a name="use-the-kudu-debug-console"></a>Usar o Console de Depuração Kudu
O Serviço de Aplicativo vem com um console de depuração que você pode usar para depurar, explorar e carregar arquivos, incluindo pontos de extremidade JSON para obter informações sobre o ambiente. Chama-se *Console Kudu* ou *Painel SCM* de seu aplicativo.

Você pode acessar este painel acessando o link **https://&lt;Nome do aplicativo>.scm.azurewebsites.net/** .

Estas são algumas das coisas que o Kudu fornece:

* configurações de ambiente para seu aplicativo
* fluxo de logs
* despejos de diagnóstico
* console de depuração no qual você pode executar os cmdlets do Powershell e os comandos básicos de DOS.

Outro recurso útil do Kudu é que, caso seu aplicativo esteja lançando exceções de primeira chance, você pode usar o Kudu e o despejo de processo da ferramenta SysInternals para criar despejos de memória. Esses despejos de memória são instantâneos do processo e podem frequentemente ajudá-lo a solucionar problemas mais complexos com seu aplicativo.

Para saber mais sobre recursos disponíveis no Kudu, consulte [Ferramentas online de Sites do Azure que você deve conhecer](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate"></a>

### <a name="3-mitigate-the-issue"></a>3. Atenuar o problema
#### <a name="scale-the-app"></a>Escalar o aplicativo
No Serviço de Aplicativo do Azure, para um melhor desempenho e taxa de transferência, você pode ajustar a escala na qual você está executando seu aplicativo. Escalar verticalmente aplicativos envolve duas ações relacionadas: alterar seu plano do Serviço de Aplicativo para um tipo de preço mais alto e definir certas configurações depois de ter mudado para o tipo de preço mais alto.

Para saber mais sobre como dimensionar, consulte [Escalar um aplicativo no Serviço de Aplicativo do Azure](manage-scale-up.md).

Além disso, você pode optar por executar o aplicativo em mais de uma instância. Isso não apenas fornece mais capacidade de processamento, como também oferece alguma quantidade de tolerância a falhas. Se o processo falhar em uma instância, a outra instância ainda continuará atendendo a solicitações.

Você pode definir a escala para ser Manual ou Automática.

#### <a name="use-autoheal"></a>Usar AutoHeal
O AutoHeal recicla o processo de trabalho para seu aplicativo com base nas configurações que você escolher (como alterações de configuração, solicitações, limites baseados na memória ou o tempo necessário para executar uma solicitação). Na maioria das vezes, reciclar o processo é a maneira mais rápida de resolver um problema. Embora você possa sempre reiniciar o aplicativo diretamente no Portal do Azure, o AutoHeal fará isso automaticamente. Tudo que você precisa fazer é adicionar alguns gatilhos na raiz web.config do seu aplicativo. Observe que essas configurações devem funcionar da mesma forma mesmo que o seu aplicativo não seja um .NET.

Para saber mais, consulte [AutoHeal em sites do Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Reiniciar o aplicativo
Esta é geralmente a maneira mais simples de se recuperar de problemas de uso únicos. No [portal do Azure](https://portal.azure.com/), na folha do aplicativo, há opções para parar ou reiniciar o aplicativo.

 ![reiniciar o aplicativo para solucionar os erros HTTP de 502 Gateway Incorreto e 503 Serviço Indisponível](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Também é possível gerenciar o aplicativo usando o Azure Powershell. Para obter mais informações, consulte [Usando o PowerShell do Azure com o Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).