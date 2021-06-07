---
title: Ferramenta de diagnóstico e solução
description: Saiba como você pode solucionar problemas com seu aplicativo no serviço Azure App com a ferramenta de diagnóstico e solução no portal do Azure.
keywords: serviço de aplicativo, serviço de aplicativo do azure, diagnósticos, suporte, aplicativo web, solução de problemas, autoajuda
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: d58341979b0bbe0699a5ca293b20394c43cde1d1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88962801"
---
# <a name="azure-app-service-diagnostics-overview"></a>Visão geral de diagnóstico do Serviço de Aplicativo do Azure

Quando você estiver executando um aplicativo Web, você deseja estar preparado para os problemas que podem surgir, desde os erros 500 até os usuários informando que seu site está inoperante. O diagnóstico do serviço de aplicativo é uma experiência inteligente e interativa para ajudá-lo a solucionar problemas de seu aplicativo sem nenhuma configuração necessária. Quando você tiver problemas com seu aplicativo, o diagnóstico do serviço de aplicativo indicará o que há de errado para orientá-lo sobre as informações corretas para solucionar problemas e resolver o problema com mais facilidade e rapidez.

Embora essa experiência seja mais útil quando você estiver tendo problemas com seu aplicativo nas últimas 24 horas, todos os gráficos de diagnóstico estarão sempre disponíveis para você analisar.

O diagnóstico do Serviço de Aplicativo funciona não para apenas o aplicativo no Windows, mas também aplicativos em [Linux/contêineres](./overview.md#app-service-on-linux), [Ambiente do Serviço de Aplicativo](./environment/intro.md) e [Azure Functions](../azure-functions/functions-overview.md).

## <a name="open-app-service-diagnostics"></a>Abra o diagnóstico do Serviço de Aplicativo

Para acessar o diagnóstico do serviço de aplicativo, navegue até o aplicativo Web do serviço de aplicativo ou Ambiente do Serviço de Aplicativo na [portal do Azure](https://portal.azure.com). No painel de navegação esquerdo, clique em **Diagnosticar e resolver problemas**.

Para Azure Functions, navegue até seu aplicativo de funções e, na barra de navegação superior, clique em **recursos da plataforma** e selecione **diagnosticar e resolver problemas** na seção **Gerenciamento de recursos** .

Na home page do diagnóstico do serviço de aplicativo, você pode escolher a categoria que melhor descreve o problema com seu aplicativo usando as palavras-chave em cada bloco de página inicial. Além disso, essa página é onde você pode encontrar **ferramentas de diagnóstico** para aplicativos do Windows. Consulte [ferramentas de diagnóstico (somente para o aplicativo do Windows)](#diagnostic-tools-only-for-windows-app).

![Home page](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> Se seu aplicativo estiver inativo ou executando lentidão, você poderá [coletar um rastreamento de criação de perfil](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html) para identificar a causa raiz do problema. A criação de perfil é leve e é projetada para cenários de produção.
>

## <a name="interactive-interface"></a>Interface interativa

Depois de selecionar uma categoria de Home Page que melhor se alinha com o problema do aplicativo, a interface interativa do diagnóstico do serviço de aplicativo, gênio, pode orientá-lo durante o diagnóstico e a resolução de problemas com seu aplicativo. Você pode usar os atalhos de bloco fornecidos pelo gênio para exibir o relatório de diagnóstico completo da categoria do problema que você está interessado. Os atalhos de bloco fornecem uma forma direta de acessar suas métricas de diagnóstico.

![Atalhos de bloco](./media/app-service-diagnostics/tile-shortcuts-2.png)

Depois de clicar nesses blocos, você poderá ver uma lista de tópicos relacionados ao problema descrito no bloco. Esses tópicos fornecem trechos de informações importantes do relatório completo. Você pode clicar em qualquer um desses tópicos para investigar mais os problemas. Além disso, você pode clicar em **Exibir relatório completo** para explorar todos os tópicos em uma única página.

![Tópicos](./media/app-service-diagnostics/application-logs-insights-3.png)

![Exibir relatório completo](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Relatório de diagnóstico

Depois de optar por investigar o problema mais tarde clicando em um tópico, você pode exibir mais detalhes sobre o tópico muitas vezes complementado com grafos e reexplicações. O relatório de diagnóstico pode ser uma ferramenta poderosa para identificar o problema com seu aplicativo.

![Relatório de diagnóstico](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Verificação de integridade

Se você não sabe o que há de errado com seu aplicativo ou não sabe por onde começar a solucionar seus problemas, a verificação de integridade é um bom lugar para começar. A verificação de integridade analisa seus aplicativos para fornecer uma visão geral rápida e interativa que indica o que está íntegro e o que está errado, informando a você onde procurar investigar o problema. Sua interface inteligente e interativa fornece orientações durante o processo de solução de problemas. A verificação de integridade é integrada ao relatório de diagnóstico da experiência gênio para aplicativos do Windows e aplicativo Web inoperante para aplicativos Linux.

### <a name="health-checkup-graphs"></a>Grafos da verificação de integridade

Há quatro grafos diferentes na verificação de integridade.

- **solicitações e erros:** Um grafo que mostra o número de solicitações feitas nas últimas 24 horas, juntamente com erros do servidor HTTP.
- **desempenho do aplicativo:** Um grafo que mostra o tempo de resposta nas últimas 24 horas para vários grupos de percentil.
- **Uso da CPU:** Um grafo que mostra o percentual de uso de CPU geral por instância nas últimas 24 horas.  
- **uso de memória:** Um grafo que mostra o percentual geral de uso de memória física por instância nas últimas 24 horas.

![Verificação de integridade](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Investigar problemas de código do aplicativo (somente para o aplicativo do Windows)

Como muitos problemas de aplicativo estão relacionados a problemas no código do aplicativo, o diagnóstico do Serviço de Aplicativo integra-se ao [Application Insights](../azure-monitor/app/app-insights-overview.md) para realçar exceções e problemas de dependência para correlacionar com o tempo de inatividade selecionado. O Application Insights precisa ser habilitado separadamente.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Para exibir Application Insights exceções e dependências, selecione o **aplicativo Web inoperante** ou atalhos de bloco **lentos do aplicativo Web** .

### <a name="troubleshooting-steps-only-for-windows-app"></a>Etapas de solução de problemas (somente para o aplicativo do Windows)

Se for detectado um problema com uma categoria de problema específica nas últimas 24 horas, você poderá exibir o relatório de diagnóstico completo e o diagnóstico do serviço de aplicativo poderá solicitar que você veja mais conselhos de solução de problemas e as próximas etapas para uma experiência mais guiada.

![Application Insights e solução de problemas e próximas etapas](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Ferramentas de diagnóstico (somente para o aplicativo do Windows)

As ferramentas de diagnóstico incluem ferramentas de diagnóstico mais avançadas que ajudam você a investigar problemas de código do aplicativo, lentidão, cadeias de conexão e muito mais. e ferramentas proativas que ajudam a atenuar problemas com uso de CPU, solicitações e memória.

### <a name="proactive-cpu-monitoring"></a>Monitoramento proativo de CPU

O monitoramento proativo de CPU fornece uma maneira fácil e proativa de tomar uma ação quando seu aplicativo ou processo filho para seu aplicativo está consumindo altos recursos de CPU. Você pode definir suas próprias regras de limite de CPU para atenuar temporariamente um problema de alta utilização da CPU até que a causa real do problema inesperado seja encontrada. Para obter mais informações, consulte [atenuar os problemas de CPU antes que eles ocorram](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html).

![Monitoramento proativo de CPU](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Recuperação automática e auto-recuperação proativa

A recuperação automática é uma ação de mitigação que você pode tomar quando seu aplicativo estiver tendo um comportamento inesperado. Você pode definir suas próprias regras com base na contagem de solicitações, solicitação lenta, limite de memória e código de status HTTP para disparar ações de mitigação. Use a ferramenta para mitigar temporariamente um comportamento inesperado até encontrar a causa raiz. Para obter mais informações, consulte [anunciando a nova experiência de recuperação automática no diagnóstico do serviço de aplicativo](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Recuperação automática](./media/app-service-diagnostics/auto-healing-10.png)

Como o monitoramento proativo de CPU, a auto-recuperação proativa é uma solução de chave para reduzir o comportamento inesperado de seu aplicativo. A recuperação automática proativa reinicia seu aplicativo quando o serviço de aplicativo determina que seu aplicativo está em um estado irrecuperável. Para obter mais informações, consulte [introdução à reparo automático proativo](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html).

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Navegador e análise de alterações (somente para aplicativo do Windows)

Em uma equipe grande com integração contínua e em que seu aplicativo tem muitas dependências, pode ser difícil identificar a alteração específica que causa um comportamento não íntegro. O navegador ajuda a obter visibilidade sobre a topologia do aplicativo ao renderizar automaticamente um mapa de dependências de seu aplicativo e todos os recursos na mesma assinatura. O Navigator permite que você exiba uma lista consolidada de alterações feitas pelo seu aplicativo e suas dependências e restrinja uma alteração que causa um comportamento não íntegro. Ele pode ser acessado por meio do **navegador** de bloco da Home Page e precisa ser habilitado antes de ser usado pela primeira vez. Para obter mais informações, consulte [obter visibilidade das dependências do seu aplicativo com o navegador](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html).

![Página padrão do navegador](./media/app-service-diagnostics/navigator-default-page-11.png)

![Exibição de comparação](./media/app-service-diagnostics/diff-view-12.png)

A análise de alteração para alterações de aplicativo pode ser acessada por meio de atalhos de bloco, **alterações de aplicativo** e **falhas de aplicativo** em **disponibilidade e desempenho** para que você possa usá-lo simultaneamente com outras métricas. Antes de usar o recurso, você deve primeiro habilitá-lo. Para obter mais informações, consulte [anunciando a nova experiência de análise de alterações no diagnóstico do serviço de aplicativo](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

Poste suas perguntas ou comentários no [UserVoice](https://feedback.azure.com/forums/169385-web-apps) adicionando "[diag]" no título.