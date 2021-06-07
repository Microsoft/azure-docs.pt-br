---
title: 'Início Rápido: Monitorar sites com o Application Insights do Azure Monitor'
description: Neste início rápido, saiba como configurar o monitoramento do site do lado do cliente/navegador com o Application Insights do Azure Monitor.
ms.topic: quickstart
ms.date: 03/19/2021
ms.custom: mvc
ms.openlocfilehash: 0e10db39c8dbbf81087d696cfbb5b2ded1ae79ac
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654879"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Início Rápido: Iniciar o monitoramento do seu site com o Application Insights do Azure Monitor

Neste início rápido, você aprenderá a adicionar o SDK do JavaScript do Application Insights de software livre ao seu site. Você também aprenderá a entender melhor a experiência do lado do cliente/navegador para os visitantes do seu site.

Com o Application Insights do Azure Monitor, você pode monitorar facilmente o site quanto à sua disponibilidade, desempenho e uso. Você também pode identificar e diagnosticar erros rapidamente em seu aplicativo sem esperar que um usuário os relate. O Application Insights fornece monitoramento do lado do servidor e funcionalidades de monitoramento do lado do cliente/navegador.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Um site ao qual você pode adicionar o SDK do JavaScript do Application Insights.

## <a name="enable-application-insights"></a>Habilitar o Application Insights

O Application Insights pode coletar dados de telemetria de qualquer aplicativo conectado à Internet, executado localmente ou na nuvem. Execute as seguinte etapas para ver esses dados:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Selecione **Criar um recurso** > **Ferramentas de Gerenciamento** > **Application Insights**.

   > [!NOTE]
   >Se esta for a primeira vez que você cria um recurso do Application Insights, confira [Criar um recurso do Application Insights](./create-new-resource.md).
1. Quando a caixa de configuração for exibida, use a seguinte tabela para preencher os campos de entrada:

    | Configurações        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Globalmente Exclusivo | O nome que identifica o aplicativo que você está monitorando. |
   | **Grupo de recursos**     | myResourceGroup      | O nome do novo grupo de recursos no qual hospedar dados do Application Insights. é possível criar um grupo de recursos ou usar um grupo existente. |
   | **Localidade** | Leste dos EUA | Escolha uma localização perto de você ou de onde seu aplicativo está hospedado. |
1. Selecione **Criar**.

## <a name="create-an-html-file"></a>Criar um arquivo HTML

1. No computador local, crie um arquivo chamado ``hello_world.html``. Para este exemplo, crie o arquivo na raiz da unidade C para que fique semelhante a ``C:\hello_world.html``.
1. Copie e cole o seguinte script em ``hello_world.html``:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Configurar o SDK do Application Insights

1. Selecione **Visão geral** e copie a **Cadeia de Conexão** do aplicativo. Para este exemplo, precisamos apenas da parte da chave de instrumentação da cadeia de conexão `InstrumentationKey=00000000-0000-0000-0000-000000000000;`.

    :::image type="content" source="media/website-monitoring/keys.png" alt-text="Captura de tela da página de visão geral com a chave de instrumentação e a cadeia de conexão.":::

1. Adicione o seguinte script ao arquivo ``hello_world.html`` antes da marca de fechamento ``</head>``:

    ```html
    <script type="text/javascript">
    !function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
    src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
    // name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
    // ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
    // useXhr: 1, // Use XHR instead of fetch to report failures (if available),
    crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
    // onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
    cfg: { // Application Insights Configuration
        connectionString:"InstrumentationKey=YOUR_INSTRUMENTATION_KEY_GOES_HERE;" 
        /* ...Other Configuration Options... */
    }});
    </script>
    ```

    > [!NOTE]
    > O trecho atual (listado acima) é a versão "5", a versão é codificada no trecho como sv:"#" e a [versão atual e os detalhes de configuração estão disponíveis no GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

1. Edite ``hello_world.html`` e adicione a chave de instrumentação.

1. Abra ``hello_world.html`` em uma sessão do navegador local. Essa ação cria uma exibição de página única. Você pode atualizar o navegador para gerar várias exibições de página de teste.

## <a name="monitor-your-website-in-the-azure-portal"></a>Monitorar o site no portal do Azure

1. Reabra a página **Visão Geral** do Application Insights no portal do Azure para ver detalhes sobre o aplicativo em execução no momento. A página **Visão geral** é o local em que você recuperou sua chave de instrumentação.

   Os quatro gráficos padrão da página Visão geral limitam-se aos dados do aplicativo do lado do servidor. Como estamos instrumentando as interações do lado do cliente/navegador com o SDK do JavaScript, esta exibição específica não se aplica, a menos que também tenhamos um SDK do lado do servidor instalado.

1. Selecione **Logs**.  Essa ação abre **Logs**, que fornece uma linguagem de consulta avançada para analisar todos os dados coletados pelo Application Insights. Para ver dados relacionados às solicitações de navegador do lado do cliente, execute a seguinte consulta:

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

   :::image type="content" source="media/website-monitoring/log-query.png" alt-text="Captura de tela do grafo de análise de logs das solicitações de usuário durante um período.":::

1. Volte para a página **Visão geral**. No cabeçalho **Investigar**, selecione **Desempenho** e escolha a guia **Navegador**.  Métricas relacionadas ao desempenho do site serão exibidas. Há uma exibição correspondente para a análise de falhas e exceções em seu site. Você pode selecionar **Amostras** para acessar os [detalhes da transação de ponta a ponta](./transaction-diagnostics.md).

     :::image type="content" source="media/website-monitoring/performance.png" alt-text="Captura de tela da guia Desempenho com o grafo de métricas do navegador.":::

1. No menu principal do Application Insights, sob o cabeçalho **Uso**, selecione [**Usuários**](./usage-segmentation.md) para começar a explorar as [ferramentas de análise de comportamento do usuário](./usage-overview.md). Como estamos testando em um só computador, veremos dados de apenas um usuário.

1. Para um site mais complexo com várias páginas, use a ferramenta [**Fluxos dos Usuários**](./usage-flows.md) para acompanhar o caminho que os visitantes percorrem nas várias partes do site.

Para conhecer configurações mais avançadas do monitoramento de sites, confira a [referência da API do SDK de JavaScript](./javascript.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Se planejar continuar trabalhando em outros inícios rápidos ou tutoriais, não limpe os recursos criados neste início rápido. Caso contrário, use as etapas a seguir para excluir todos os recursos criados por esse início rápido no Portal do Azure.

> [!NOTE]
> Se você tiver usado um grupo de recursos existente, as instruções a seguir não funcionarão. Em vez disso, apenas exclua o recurso individual do Application Insights. Tenha em mente que quando você exclui um grupo de recursos, todos os recursos subjacentes que são membros desse grupo também são excluídos.

1. No menu à esquerda no portal do Azure, selecione **Grupos de recursos** e, em seguida, escolha **myResourceGroup** ou o nome de seu grupo de recursos temporário.
1. Na página do grupo de recursos, selecione **Excluir**, insira **myResourceGroup** na caixa de texto e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Localizar e diagnosticar problemas de desempenho](../logs/log-query-overview.md)

