---
title: Guia de início rápido para monitorar sites com o Application Insights do Azure Monitor | Microsoft Docs
description: Fornece instruções para configurar rapidamente o monitoramento do site do lado do cliente/navegador com o Application Insights do Azure Monitor
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/15/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 9845329d30da40243751c00f7c0fa62fdcfe5a0d
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67990087"
---
# <a name="start-monitoring-your-website"></a>Iniciar o monitoramento do site

Com o Application Insights do Azure Monitor, você pode monitorar facilmente o site quanto à sua disponibilidade, desempenho e uso. Você também pode identificar e diagnosticar erros rapidamente em seu aplicativo sem esperar que um usuário os relate. O Application Insights fornece monitoramento do lado do servidor, assim como capacidades de monitoramento do lado do cliente/navegador.

Este início rápido mostrará como adicionar o [SDK de JavaScript do Application Insights de software livre](https://github.com/Microsoft/ApplicationInsights-JS), que permite entender a experiência do lado do cliente/navegador para os visitantes de seu site.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

- É necessária uma assinatura do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Habilitar o Application Insights

O Application Insights pode coletar dados de telemetria de qualquer aplicativo conectado à Internet, sendo executado localmente ou na nuvem. Use as etapas a seguir para começar a exibir esses dados.

1. Selecione **Criar um recurso** > **Ferramentas de Gerenciamento** > **Application Insights**.

   > [!NOTE]
   >Se esta for a primeira vez que você cria um recurso do Application Insights, poderá aprender mais no artigo [Criar um recurso do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

   Uma caixa de configuração é exibida. Use a tabela a seguir para preencher os campos de entrada.

    | Configurações        | Valor           | DESCRIÇÃO  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Globalmente Exclusivo | Nome que identifica o aplicativo que você está monitorando |
   | **Grupo de recursos**     | myResourceGroup      | Nome para o novo grupo de recursos no qual hospedar dados do Application Insights |
   | **Localidade** | Leste dos EUA | Escolher uma localização perto de você ou perto onde seu aplicativo está hospedado |

2. Clique em **Criar**.

## <a name="create-an-html-file"></a>Criar um arquivo HTML

1. No computador local, crie um arquivo chamado ``hello_world.html``. Neste exemplo, o arquivo será colocado na raiz da unidade C: em ``C:\hello_world.html``.
2. Copie o script abaixo em ``hello_world.html``:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-app-insights-sdk"></a>Configurar o SDK do Application Insights

1. Selecione **Visão Geral** > **Essentials** > Copiar a **Chave de Instrumentação** do aplicativo.

   ![Formulário de recursos do Application Insights novo](media/website-monitoring/instrumentation-key-001.png)

2. Adicione o seguinte script ao seu ``hello_world.html`` antes da marca ``</head>`` de fechamento:

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
         function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
      }({
         instrumentationKey:"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
      });

      window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

3. Edite ``hello_world.html`` e adicione a chave de instrumentação.

4. Abra ``hello_world.html`` em uma sessão do navegador local. Isso criará uma única exibição de página. Você pode atualizar seu navegador para gerar vários modos de exibição de página de teste.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar o monitoramento no Portal do Azure

1. Agora você pode reabrir a página **Visão Geral** do Application Insights no Portal do Azure, na qual você recuperou sua chave de instrumentação, para exibir detalhes sobre o seu aplicativo em execução atualmente. Os quatro gráficos padrão da página Visão geral limitam-se aos dados do aplicativo do lado do servidor. Uma vez que estamos instrumentando as interações do lado do cliente/navegador com o SDK do JavaScript este modo de exibição específico não se aplica, a menos que também tenhamos um SDK do lado do servidor instalado.

2. Clique no ![ícone Mapa do Aplicativo](media/website-monitoring/006.png) **Analytics**.  Isso abre o **Analytics**, que fornece uma linguagem de consulta avançada para analisar todos os dados coletados pelo Application Insights. Para exibir dados relacionados às solicitações de navegador do lado do cliente, execute a seguinte consulta:

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![Grafo de análise de solicitações de usuário durante um período de tempo](./media/website-monitoring/analytics-query.png)

3. Volte para a página **Visão geral**. Clique em **Navegador** no cabeçalho **Investigar**, em seguida, selecione **desempenho** Aqui você encontra métricas relacionadas ao desempenho do seu site. Também há um modo de exibição correspondente para a análise de falhas e exceções em seu site. Você pode clicar em **Exemplos** para analisar os detalhes de cada transação individual. A partir daqui, você pode acessar a experiência dos [detalhes da transação de ponta a ponta](../../azure-monitor/app/transaction-diagnostics.md).

   ![Grafo de métricas do servidor](./media/website-monitoring/browser-performance.png)

4. Para começar a explorar as [ferramentas de análise de comportamento do usuário](../../azure-monitor/app/usage-overview.md), no menu principal do Application Insights, selecione [**Usuários**](../../azure-monitor/app/usage-segmentation.md) no cabeçalho **Usuários**. Uma vez que estamos testando de um único computador, veremos dados apenas para um usuário. Para um site ao vivo, a distribuição de usuários será semelhante ao seguinte:

     ![Gráfico de usuário](./media/website-monitoring/usage-users.png)

5. Se tivéssemos instrumentado um site mais complexo com várias páginas, outra ferramenta útil é [**Fluxos dos Usuários**](../../azure-monitor/app/usage-flows.md). Com **Fluxos dos Usuários** você pode rastrear a caminho que os visitantes percorrem dentro das várias partes do seu site.

   ![Visualização de Fluxos dos Usuários](./media/website-monitoring/user-flows.png)

Para saber sobre configurações mais avançadas para o monitoramento de sites, confira a [referência da API do SDK de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar trabalhando com inícios rápidos subsequentes ou com os tutoriais, não limpe os recursos criados nesse início rápido. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse guia de início rápido no portal do Azure.

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e clique em **myResourceGroup**.
2. Em sua página de grupo de recursos, clique em **Excluir**, digite **myResourceGroup** na caixa de texto e clique **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Localizar e diagnosticar problemas de desempenho](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
