---
title: 'Início Rápido: Monitorar o Node.js com o Application Insights do Azure Monitor'
description: Fornece instruções para configurar rapidamente um aplicativo Web Node.js para monitoramento com o Application Insights do Azure Monitor
ms.topic: quickstart
author: lgayhardt
ms.author: lagayhar
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: 96a60c88997226516d451124280053d87065e5b6
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106381936"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Início Rápido: Começar a monitorar seu aplicativo Web Node.js com o Azure Application Insights

Neste início rápido, você adicionará a versão 0.22 do SDK do Application Insights para Node.js a um aplicativo Web do Node.js.

Com o Azure Application Insights, você pode monitorar facilmente o aplicativo Web quanto à sua disponibilidade, desempenho e uso. Você também pode identificar e diagnosticar erros rapidamente em seu aplicativo sem esperar que um usuário os relate. Com a versão 0.20 do SDK em diante você pode monitorar pacotes comuns de terceiros, incluindo MongoDB, MySQL e Redis.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Um aplicativo do Node.js em funcionamento.

## <a name="enable-application-insights"></a>Habilitar o Application Insights

O Application Insights pode coletar dados de telemetria de qualquer aplicativo conectado à Internet, independentemente de ele ter execução local ou na nuvem. Use as etapas a seguir para começar a exibir esses dados.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione **Criar um recurso** > **Ferramentas de desenvolvedor** > **Application Insights**.

   ![Adicionar um recurso do Azure Application Insights](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Se esta for a primeira vez criando um recurso do Application Insights, você pode aprender mais no documento [Criar um recurso do Application Insights](../app/create-new-resource.md).

   Uma página de configuração é exibida. Use a tabela a seguir para preencher os campos de entrada. 

    | Configurações        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Globalmente Exclusivo | Nome que identifica o aplicativo que você está monitorando |
   | **Grupo de recursos**     | myResourceGroup      | Nome do novo grupo de recursos no qual hospedar dados do AppInsights. é possível criar um grupo de recursos ou usar um grupo existente. |
   | **Localidade** | Leste dos EUA | Escolher uma localização perto de você ou perto onde seu aplicativo está hospedado |

3. Selecione **Criar**.

## <a name="configure-appinsights-sdk"></a>Configurar o SDK do AppInsights

1. Selecione **Visão Geral** e copie a **Chave de Instrumentação** do aplicativo.

   ![Exibir a chave de instrumentação do Application Insights](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Adicione o SDK do Application Insights para Node.js ao aplicativo. Da pasta raiz do seu aplicativo, execute:

   ```bash
   npm install applicationinsights --save
   ```

3. Edite o primeiro arquivo *.js* do seu aplicativo e adicione as duas linhas abaixo à parte superior do script. Se você estiver usando o [aplicativo de início rápido do Node.js](../../app-service/quickstart-nodejs.md), deverá modificar o arquivo *index.js*. Substitua `<instrumentation_key>` pela chave de instrumentação do Application Insights. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Reinicie o aplicativo.

> [!NOTE]
> Leva 3 a 5 minutos para que os dados comecem a aparecer no portal. Se esse aplicativo é um aplicativo de teste de baixo tráfego, tenha em mente que a maioria das métricas só são capturadas quando há operações ou solicitações ativas ocorrendo.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar o monitoramento no Portal do Azure

1. Agora você pode reabrir a página **Visão Geral** do Application Insights no Portal do Azure, na qual você recuperou sua chave de instrumentação, para exibir detalhes sobre o seu aplicativo em execução atualmente.

   ![Menu Visão Geral do Application Insights](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Selecione **Mapa do aplicativo** para obter um layout visual das relações de dependência entre os componentes do aplicativo. Cada componente mostra KPIs como alertas, desempenho, falhas e carregamento.

   ![Mapa do aplicativo do Application Insights](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Selecione o ícone **Análise do Aplicativo**![ícone do mapa do aplicativo](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) **Exibir no Analytics**.  Essa ação abre a **Análise do Application Insights**, que fornece uma linguagem de consulta avançada para analisar todos os dados coletados pelo Application Insights. Nesse caso, uma consulta que renderiza a contagem de solicitações como um gráfico é gerada para você. Você pode escrever suas próprias consultas para analisar outros dados.

   ![Grafos de análise do Application Insights](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Volte para a página **Visão geral** e examine os gráficos KPI.  Esse painel fornece estatísticas sobre a integridade do aplicativo, incluindo o número de solicitações de entrada, a duração dessas solicitações e as falhas que ocorrem.

   ![Grafos de linha do tempo da visão geral de integridade do Application Insights](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

   Para habilitar o gráfico **Tempo de Carregamento da Exibição de Página** para popular usando dados de **telemetria do lado do cliente**, adicione esse script a cada página que você desejar acompanhar:

   ```HTML
   <!-- 
   To collect user behavior analytics tools about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. À esquerda, selecione **Métricas**. Use o Metrics Explorer para investigar a integridade e o uso do recurso. Selecione **Adicionar novo gráfico** para criar exibições personalizadas adicionais ou **Editar** para modificar a altura, a paleta de cores, os agrupamentos, as métricas e os tipos de gráficos existentes. Por exemplo, crie um gráfico que exibe o tempo médio de carregamento de página do navegador selecionando "Tempo de carregamento de página do navegador" no menu suspenso das métricas e "Média" da agregação. Para saber mais sobre o Azure Metrics Explorer, acesse [Introdução ao Azure Metrics Explorer](../essentials/metrics-getting-started.md).

   ![Grafo de métricas do servidor do Application Insights](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Para saber mais sobre como monitorar o Node.js, confira a [documentação adicional do Node.js do AppInsights](../app/nodejs.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você terminar de testar, poderá excluir o grupo de recursos e todos os recursos relacionados. Para isso, siga as etapas abaixo.

> [!NOTE]
> Se você tiver usado um grupo de recursos existente, as instruções abaixo não funcionarão e você precisará apenas excluir o recurso individual do Application Insights. Saiba que sempre que você excluir um grupo de recursos, todos os recursos subjacentes que são membros desse grupo serão excluídos.

1. No menu à esquerda no portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione **myResourceGroup**.
2. Na página do grupo de recursos, selecione **Excluir**, insira **myResourceGroup** na caixa de texto e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Localizar e diagnosticar problemas de desempenho](../logs/log-query-overview.md)

