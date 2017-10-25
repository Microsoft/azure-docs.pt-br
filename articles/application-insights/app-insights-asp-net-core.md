---
title: Azure Application Insights para ASP.NET Core | Microsoft Docs
description: Monitorar aplicativos web de disponibilidade, desempenho e uso.
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: d86495eea467977f6c079de72e2b49a2a1da2b60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights para ASP.NET Core
O [Application Insights](app-insights-overview.md) permite que você monitore seu aplicativo Web quanto à disponibilidade, desempenho e uso. Com os comentários que você obtiver sobre o desempenho e a eficiência de seu aplicativo em uso, você pode fazer escolhas informadas sobre a direção do projeto em cada ciclo de vida de desenvolvimento.

![Exemplo](./media/app-insights-asp-net-core/sample.png)

Precisa de uma assinatura do [Microsoft Azure](http://azure.com). Entre com uma conta da Microsoft, que você pode ter para o Windows, XBox Live ou outros serviços de nuvem da Microsoft. Sua equipe pode ter uma assinatura organizacional do Azure: peça ao proprietário que adicione você a ela usando sua conta da Microsoft.

## <a name="getting-started"></a>Introdução

* No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto e selecione **Configurar Application Insights** ou **Adicionar > Application Insights**. [Saiba mais](app-insights-asp-net.md).
* Se esses comandos de menu não forem exibidos, siga o [manual Guia de Introdução](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started). Talvez seja necessário fazer isso se o projeto foi criado com uma versão do Visual Studio anterior a 2017.

## <a name="using-application-insights"></a>Usando o Application Insights
Entre no [portal do Microsoft Azure](https://portal.azure.com), selecione **Todos os Recursos** ou **Application Insights** e, em seguida, selecione o recurso criado para monitorar o aplicativo.

Em uma janela separada do navegador, use seu aplicativo por algum tempo. Você verá os dados exibidos nos gráficos do Application Insights. (Talvez você precise clicar em Atualizar.) Haverá uma pequena quantidade de dados enquanto você estiver desenvolvendo, mas estes gráficos realmente ganharão vida quando você publicar seu aplicativo e tiver muitos usuários. 

A página de visão geral mostra gráficos de desempenho chave: tempo de resposta do servidor, tempo de carregamento de página e contagens de solicitações com falha. Clique em qualquer gráfico para ver mais gráficos e dados.

As exibições no portal se enquadram em três categorias principais:

* O [Metrics Explorer](app-insights-metrics-explorer.md) mostra gráficos e tabelas de métricas e contagens, como tempos de resposta, taxas de falha ou métricas que você cria com a [API](app-insights-api-custom-events-metrics.md). Filtre e segmente os dados por valores de propriedade para obter uma compreensão melhor do seu aplicativo e seus usuários.
* O [Search Explorer](app-insights-diagnostic-search.md) lista eventos individuais, como solicitações específicas, exceções, rastreamentos de log ou eventos que você criou com a [API](app-insights-api-custom-events-metrics.md). Filtre e pesquise nos eventos e navegue entre os eventos relacionados para investigar problemas.
* [Análise](app-insights-analytics.md) permite que você execute consultas SQL em sua telemetria e é uma poderosa ferramenta de análise e de diagnóstico.

## <a name="alerts"></a>Alertas
* Você obtém automaticamente [alertas proativos de diagnóstico](app-insights-proactive-diagnostics.md) que informam sobre alterações anômalas em taxas de falha e outras métricas.
* Configure [testes de disponibilidade](app-insights-monitor-web-app-availability.md) para testar seu site continuamente em locais em todo o mundo e receber emails assim que qualquer teste falhar.
* Configure [alertas de métrica](app-insights-monitor-web-app-availability.md) para saber se métricas, como tempos de resposta ou taxas de exceção, saem dos limites aceitáveis.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="open-source"></a>Código-fonte aberto
[Ler e contribuir para o código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)


## <a name="next-steps"></a>Próximas etapas
* [Adicione telemetria às suas páginas da Web](app-insights-javascript.md) para monitorar o uso e o desempenho de páginas.
* [Monitore dependências](app-insights-asp-net-dependencies.md) para ver se REST, SQL ou outros recursos externos estão causando lentidão.
* [Use a API](app-insights-api-custom-events-metrics.md) para enviar seus próprios eventos e métricas para uma exibição mais detalhada do desempenho e do uso do aplicativo.
* [Testes de disponibilidade](app-insights-monitor-web-app-availability.md) verificam seu aplicativo constantemente em todo o mundo. 

