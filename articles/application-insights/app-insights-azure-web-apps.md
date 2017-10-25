---
title: Monitorar desempenho do aplicativo Web do Azure | Microsoft Docs
description: "Monitoramento do desempenho do aplicativo de para aplicativos Web do Azure. Tempo de resposta e de carga, informações de dependência e alertas definidos sobre o desempenho do gráfico."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/05/2017
ms.author: bwren
ms.openlocfilehash: dd6c2a1acdcd3b933bed536fd2bb1d83e1e593c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-azure-web-app-performance"></a>Monitorar o desempenho do aplicativo Web do Azure
No [Portal do Azure](https://portal.azure.com), você pode configurar o monitoramento de desempenho de aplicativo dos seus [aplicativos Web do Azure](../app-service/app-service-web-overview.md). O [Azure Application Insights](app-insights-overview.md) equipa seu aplicativo para enviar a telemetria sobre suas atividades para o serviço Application Insights, onde ela é armazenada e analisada. Lá, os gráficos de métricas e as ferramentas de pesquisa podem ser usados para ajudar a diagnosticar problemas, melhorar o desempenho e avaliar o uso.

## <a name="run-time-or-build-time"></a>Tempo de execução ou tempo de compilação
Você pode configurar o monitoramento por meio da instrumentação do aplicativo de duas maneiras:

* **Tempo de execução** - você pode selecionar um desempenho extensão de monitoramento quando seu aplicativo Web já está ativo. Não é necessário recompilar ou reinstalar o aplicativo. Obtenha um conjunto padrão de pacotes que monitoram os tempos de resposta, taxas de êxito, exceções, dependências e assim por diante. 
* **Tempo de compilação** - você pode instalar um pacote em seu aplicativo em desenvolvimento. Essa opção é mais versátil. Além dos mesmos pacotes padrão, você pode escrever código para personalizar a telemetria ou para enviar sua própria telemetria. Você pode registrar eventos de registro de acordo com a semântica do seu domínio de aplicativo ou de atividades específicas. 

## <a name="run-time-instrumentation-with-application-insights"></a>Execute a instrumentação de tempo com o Application Insights
Se você já estiver executando um aplicativo Web no Azure, então já está sendo monitorado: taxas de erro e de solicitação. Adicione o Application Insights para obter mais, como tempos de resposta, monitoramento de chamadas para dependências, detecção inteligente e a linguagem avançada de consulta do Log Analytics. 

1. **Selecione o Application Insights** no painel de controle do Azure para seu aplicativo Web.
   
    ![Em Monitoramento, selecione Application Insights](./media/app-insights-azure-web-apps/05-extend.png)
   
   * Opte por criar um novo recurso, a menos que já tenha configurado um recurso do Application Insights para esse aplicativo por outra rota.
2. **Instrumente seu aplicativo Web** após a instalação do Application Insights. 
   
    ![Instrumentar seu aplicativo Web](./media/app-insights-azure-web-apps/restart-web-app-for-insights.png)

   **Habilite o monitoramento do lado do cliente** para telemetria de usuário e exibição de página.

   * Selecione Configurações > Configurações do Aplicativo
   * Em configurações do aplicativo, adicione um novo par de chave/valor: 
   
    Chave: `APPINSIGHTS_JAVASCRIPT_ENABLED` 
    
    Valor: `true`
   * **Salve** as configurações e **Reinicie** seu aplicativo.
3. **Monitore o seu aplicativo**.  [Explorar os dados](#explore-the-data).

Posteriormente, você poderá compilar o aplicativo com o Application Insights, se desejar.

*Como remover o Application Insights ou alternar para envio para outro recurso?*

* No Azure, abra a folha de controle de aplicativo Web e, em Ferramentas de Desenvolvimento, abra **Extensões**. Excluir a extensão do Application Insights. Em seguida, em Monitoramento, selecione Application Insights e crie ou selecione o recurso desejado.

## <a name="build-the-app-with-application-insights"></a>Compilar seu aplicativo com o Application Insights
O Application Insights pode fornecer dados de telemetria mais detalhados instalando um SDK em seu aplicativo. Em particular, você pode coletar logs de rastreamento, [escrever telemetria personalizada](app-insights-api-custom-events-metrics.md) e obter relatórios de exceção mais detalhados.

1. **No Visual Studio** (2013 atualização 2 ou posterior), adicione o Application Insights ao seu projeto.

    Clique com botão direito no projeto Web e selecione **Adicionar > Application Insights** ou **Configurar Application Insights**.
   
    ![Clique com o botão direito do mouse no projeto da Web e escolha Adicionar ou Configurar o Application Insights](./media/app-insights-azure-web-apps/03-add.png)
   
    Se receber uma solicitação para se conectar, use as credenciais da conta do Azure.
   
    A operação tem dois efeitos:
   
   1. Cria um recurso do Application Insights no Azure, onde a telemetria é armazenada, analisada e exibida.
   2. Adiciona o pacote NuGet do Application Insights ao seu código (se ainda não estiver lá) e o configura para enviar telemetria ao recurso do Azure.
2. **Teste a telemetria** executando o aplicativo no computador de desenvolvimento (F5).
3. **Publique o aplicativo** no Azure como de costume. 

*Como eu mudo para enviar dados para um recurso diferente do Application Insights?*

* No Visual Studio, clique com o botão direito do mouse no projeto, escolha **Configurar o Application Insights** e escolha o recurso desejado. Você obtém a opção para criar um novo recurso. Recompilar e reimplantar.

## <a name="explore-the-data"></a>Explorar os dados
1. Na folha do Application Insights do seu painel de controle do aplicativo Web, você vê o Live Metrics, que mostra solicitações e falhas em um segundo ou dois após a ocorrência delas. É muito útil exibir quando você estiver republicando seu aplicativo – você poderá ver todos os problemas imediatamente.
2. Clique para o recurso Application Insights completo.

    ![Clique para o](./media/app-insights-azure-web-apps/view-in-application-insights.png)

    Você também pode ir lá diretamente da navegação de recursos do Azure.

1. Clique em qualquer lugar do gráfico para ver mais detalhes:
   
    ![Na folha de visão geral do Application Insights, clique em um gráfico](./media/app-insights-azure-web-apps/07-dependency.png)
   
    Você pode [personalizar folhas de métricas](app-insights-metrics-explorer.md).
2. Clique para ver os eventos individuais e suas propriedades:
   
    ![Clique em um tipo de evento para abrir uma pesquisa filtrada nesse tipo](./media/app-insights-azure-web-apps/08-requests.png)
   
    Observe o link "…", que abre todas as propriedades.
   
    Você pode [personalizar pesquisas](app-insights-diagnostic-search.md).

Para ter pesquisas mais eficientes em sua telemetria, use a [linguagem de consulta do Log Analytics](app-insights-analytics-tour.md).

## <a name="more-telemetry"></a>Mais telemetria

* [Carregar dados da página da Web](app-insights-javascript.md)
* [Telemetria personalizada](app-insights-api-custom-events-metrics.md)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Próximas etapas
* [Executar o criador de perfil em seu aplicativo ativo](app-insights-profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – monitorar o Azure Functions com o Application Insights
* [Permita que o diagnóstico do Azure](app-insights-azure-diagnostics.md) seja enviado ao Application Insights.
* [Monitore as métricas de integridade do serviço](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para se certificar de que o serviço esteja disponível e responsivo.
* [Receba notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) sempre que ocorrerem eventos operacionais ou métricas ultrapassarem um limite.
* Use o [Application Insights para aplicativos JavaScript e páginas Web](app-insights-javascript.md) para obter a telemetria do cliente dos navegadores que visitam uma página da Web.
* [Configure testes de disponibilidade da Web](app-insights-monitor-web-app-availability.md) para ser alertado se o seu site for desativado.

