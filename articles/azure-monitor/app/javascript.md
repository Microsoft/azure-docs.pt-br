---
title: Aplicativo Azure insights para aplicativos Web JavaScript
description: Obter a exibição de página e contagens de sessão, dados de cliente Web, SPA (aplicativos de página única) e rastrear padrões de uso. Detecte exceções e problemas de desempenho em páginas da Web do JavaScript.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js
ms.openlocfilehash: 04cda044b002e226c49f8647d4705d7c0f2a514e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565258"
---
# <a name="application-insights-for-web-pages"></a>Application Insights para páginas da Web

Saiba mais sobre o desempenho e o uso de sua página da Web ou aplicativo. Se você adicionar [Application insights](app-insights-overview.md) ao script de página, obterá intervalos de carregamentos de página e chamadas AJAX, contagens e detalhes de exceções de navegador e falhas de AJAX, bem como contagens de sessão e usuários. Todos esses itens podem ser segmentados por página, sistema operacional cliente e versão do navegador, localização geográfica e outras dimensões. Você pode definir alertas para contagens de falhas ou carregamento de páginas lento. E inserindo chamadas de rastreamento em seu código JavaScript, você pode controlar como os diferentes recursos do seu aplicativo de página da Web são usados.

O Application Insights pode ser usado com todas as páginas da Web: basta adicionar um breve trecho de JavaScript. Se o seu serviço Web for [Java](java-get-started.md) ou [ASP.net](asp-net.md), você poderá usar os SDKs do lado do servidor em conjunto com o SDK do JavaScript do lado do cliente para obter uma compreensão de ponta a ponta do desempenho do aplicativo.

## <a name="adding-the-javascript-sdk"></a>Adicionando o SDK do JavaScript

> [!IMPORTANT]
> Novas regiões do Azure **exigem** o uso de cadeias de conexão em vez de chaves de instrumentação. A [cadeia de conexão](./sdk-connection-string.md?tabs=js) identifica o recurso ao qual você deseja associar os dados de telemetria. Ele também permite que você modifique os pontos de extremidade que o recurso usará como um destino para a telemetria. Você precisará copiar a cadeia de conexão e adicioná-la ao código do aplicativo ou a uma variável de ambiente.

1. Primeiro, você precisa de um recurso Application Insights. Se você ainda não tiver uma chave de recurso e de instrumentação, siga as [instruções criar um novo recurso](create-new-resource.md).
2. Copie a _chave de instrumentação_ (também conhecida como "iKey") ou a [cadeia de conexão](#connection-string-setup) para o recurso em que você deseja que sua telemetria JavaScript seja enviada (da etapa 1.) Você irá adicioná-lo à `instrumentationKey` `connectionString` configuração ou do SDK Application insights JavaScript.
3. Adicione o SDK Application Insights JavaScript à sua página da Web ou aplicativo por meio de uma das duas opções a seguir:
    * [Configuração do NPM](#npm-based-setup)
    * [Trecho de código JavaScript](#snippet-based-setup)

> [!IMPORTANT]
> Use apenas um método para adicionar o SDK do JavaScript ao seu aplicativo. Se você usar a configuração do NPM, não use o trecho e vice-versa.

> [!NOTE]
> A instalação do NPM instala o SDK do JavaScript como uma dependência do seu projeto, habilitando o IntelliSense, enquanto o trecho de código busca o SDK em tempo de execução. Ambos oferecem suporte aos mesmos recursos. No entanto, os desenvolvedores que desejam mais eventos e configurações personalizados geralmente optam pela configuração do NPM, enquanto os usuários buscam a habilitação rápida da aceitação da Web Analytics pronta para o trecho.

### <a name="npm-based-setup"></a>configuração baseada em NPM

Instale por meio do NPM.

```sh
npm i --save @microsoft/applicationinsights-web
```

> [!Note]
> As **digitações são incluídas neste pacote**, portanto, **não** é necessário instalar um pacote de digitações separado.
    
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Configuração baseada em trecho

Se seu aplicativo não usar o NPM, você poderá instrumentar diretamente suas páginas da Web com Application Insights colando esse trecho de código na parte superior de cada página. Preferivelmente, ele deve ser o primeiro script em sua `<head>` seção para que ele possa monitorar quaisquer possíveis problemas com todas as suas dependências e, opcionalmente, quaisquer erros de JavaScript. Se você estiver usando um aplicativo de servidor mais incrivelmente, adicione o trecho de código na parte superior do arquivo `_Host.cshtml` na `<head>` seção.

Para auxiliar no acompanhamento de qual versão do trecho de código seu aplicativo está usando, a partir da versão 2.5.5, o evento de exibição de página incluirá uma nova marca "ai. Internal. snippet" que conterá a versão do trecho identificado.

O trecho atual (listado abaixo) é a versão "5", a versão é codificada no trecho de código como VA: "#" e a [versão atual também está disponível no GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

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
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    /* ...Other Configuration Options... */
}});
</script>
```

> [!NOTE]
> Para facilitar a leitura e reduzir possíveis erros de JavaScript, todas as opções de configuração possíveis são listadas em uma nova linha no código de trecho acima, se você não quiser alterar o valor de uma linha comentada, ela poderá ser removida.


#### <a name="reporting-script-load-failures"></a>Relatando falhas de carregamento de script

Essa versão do trecho detecta e relata falhas ao carregar o SDK da CDN como uma exceção para o portal de Azure Monitor (no navegador de exceções de falhas &gt; &gt; ), essa exceção fornece visibilidade de falhas desse tipo para que você saiba que seu aplicativo não está relatando telemetria (ou outras exceções) conforme o esperado. Esse sinal é uma medida importante na compreensão de que você perdeu a telemetria porque o SDK não carregou ou inicializou, o que pode levar a:
- Relato de como os usuários estão usando (ou tentando usar) seu site;
- Falta de telemetria em como os usuários finais estão usando seu site;
- Erros de JavaScript ausentes que podem estar bloqueando os usuários finais de usar o site com êxito.

Para obter detalhes sobre essa exceção, consulte a página de solução de problemas de [falha de carregamento do SDK](javascript-sdk-load-failure.md) .

O relatório dessa falha como uma exceção ao portal não usa a opção de configuração ```disableExceptionTracking``` da configuração do Application insights e, portanto, se essa falha ocorrer, ela será sempre relatada pelo trecho de código, mesmo quando o suporte a Window. OnError estiver desabilitado.

Não há suporte especificamente para relatórios de falhas de carregamento do SDK no IE 8 (ou menos). Isso ajuda a reduzir o tamanho de reduzidos do trecho supondo que a maioria dos ambientes não seja exclusivamente IE 8 ou menos. Se você tiver esse requisito e desejar receber essas exceções, você precisará incluir uma versão fetchy ou criar seu próprio trecho que usa ```XDomainRequest``` , em vez de ```XMLHttpRequest``` , é recomendável que você use o [código-fonte do trecho fornecido](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js) como um ponto de partida.

> [!NOTE]
> Se você estiver usando uma versão anterior do trecho de código, é altamente recomendável que você atualize para a versão mais recente para que você receba esses problemas anteriormente não relatados.

#### <a name="snippet-configuration-options"></a>Opções de configuração de trecho

Todas as opções de configuração agora foram movidas para o final do script para ajudar a evitar a introdução acidental de erros de JavaScript que não apenas fariam com que o SDK falhasse no carregamento, mas também desativariam o relatório da falha.

Cada opção de configuração é mostrada acima em uma nova linha, se você não quiser substituir o valor padrão de um item listado como [opcional], poderá remover essa linha para minimizar o tamanho resultante da página retornada.

As opções de configuração disponíveis são
 
| Nome | Tipo | Description
|------|------|----------------
| src | Cadeia de caracteres **[obrigatório]** | A URL completa para onde carregar o SDK. Esse valor é usado para o atributo "src" de um &lt; script/marca dinamicamente adicionado &gt; . Você pode usar o local da CDN pública ou sua própria hospedada de forma privada.
| name | Cadeia de caracteres *[opcional]* | O nome global do SDK inicializado, por padrão, é `appInsights` . Portanto, ```window.appInsights``` será uma referência à instância inicializada. Observação: se você fornecer um valor de nome ou uma instância anterior parece ser atribuída (por meio do nome global appInsightsSDK), esse valor de nome também será definido no namespace global como ```window.appInsightsSDK=<name value>``` , isso é exigido pelo código de inicialização do SDK para garantir que ele esteja inicializando e atualizando o esqueleto de trecho e os métodos de proxy corretos.
| 2 | número em MS *[opcional]* | Define o atraso de carga a aguardar antes de tentar carregar o SDK. O valor padrão é 0ms e qualquer valor negativo adicionará imediatamente uma marca de script &lt; à &gt; região de cabeçalho da página, que bloqueará o evento de carregamento de página até que o script seja carregado (ou falhe).
| useXhr | booliano *[opcional]* | Essa configuração é usada apenas para falhas de carregamento do SDK de relatório. A geração de relatórios tentará primeiro usar Fetch () se disponível e, em seguida, fallback para XHR, definir esse valor como true Apenas ignora a verificação de busca. O uso desse valor só será necessário se o aplicativo estiver sendo usado em um ambiente em que a busca falha ao enviar os eventos de falha.
| crossOrigin | Cadeia de caracteres *[opcional]* | Ao incluir essa configuração, a marca de script adicionada para baixar o SDK incluirá o atributo crossOrigin com esse valor de cadeia de caracteres. Quando não definido (o padrão) nenhum atributo crossOrigin é adicionado. Os valores recomendados não estão definidos (o padrão); ""; ou "Anonymous" (para todos os valores válidos, consulte [atributo `crossorigin` HTML:](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) documentação)
| cfg | objeto **[obrigatório]** | A configuração passou para o SDK do Application Insights durante a inicialização.

### <a name="connection-string-setup"></a>Configuração da cadeia de conexão

Para a configuração de NPM ou de trecho de código, você também pode configurar sua instância do Application Insights usando uma cadeia de conexão. Basta substituir o `instrumentationKey` campo pelo `connectionString` campo.
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'YOUR_CONNECTION_STRING_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Enviando telemetria para o portal do Azure

Por padrão, o SDK do JavaScript Application Insights coleta a um número de itens de telemetria que são úteis para determinar a integridade do seu aplicativo e a experiência do usuário subjacente. Elas incluem:

- **Exceções não capturadas** em seu aplicativo, incluindo informações sobre
    - Rastreamento de pilha
    - Detalhes da exceção e mensagem que acompanha o erro
    - Número de colunas de & de linha de erro
    - URL em que o erro foi gerado
- **Solicitações de dependência de rede** feitas por solicitações **XHR** de aplicativo e **busca** (coleta de busca são desabilitadas por padrão), incluem informações sobre
    - URL da fonte de dependência
    - Método de & de comando usado para solicitar a dependência
    - Duração da solicitação
    - Código de resultado e status de êxito da solicitação
    - ID (se houver) do usuário que faz a solicitação
    - Contexto de correlação (se houver) em que a solicitação é feita
- **Informações do usuário** (por exemplo, local, rede, IP)
- **Informações do dispositivo** (por exemplo, navegador, sistema operacional, versão, idioma, modelo)
- **Informações da sessão**

### <a name="telemetry-initializers"></a>Inicializadores de telemetria
Inicializadores de telemetria são usados para modificar o conteúdo da telemetria coletada antes de ser enviada do navegador do usuário. Eles também podem ser usados para impedir que determinada telemetria seja enviada, retornando `false` . Vários inicializadores de telemetria podem ser adicionados à sua instância de Application Insights e são executados para adicioná-los.

O argumento de entrada para `addTelemetryInitializer` é um retorno de chamada que usa um [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) como argumento e retorna um `boolean` ou `void` . Se retornar `false` , o item de telemetria não será enviado, caso contrário, ele prosseguirá para o próximo inicializador de telemetria, se houver, ou será enviado para o ponto de extremidade da coleção de telemetria.

Um exemplo de como usar inicializadores de telemetria:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>Configuração
A maioria dos campos de configuração são nomeados de modo que eles podem ser padronizados como false. Todos os campos são opcionais, exceto para `instrumentationKey` .

| Name | Descrição | Padrão |
|------|-------------|---------|
| instrumentationKey | **Necessário**<br>Chave de instrumentação que você obteve do portal do Azure. | string<br/>nulo |
| accountId | Uma ID de conta opcional, se seu aplicativo agrupar usuários em contas. Sem espaços, vírgulas, pontos-e-vírgulas, Equals ou barras verticais | string<br/>nulo |
| sessionRenewalMs | Uma sessão será registrada se o usuário estiver inativo por esse período de tempo em milissegundos. | numeric<br/>1,8 milhões<br/>(30 min) |
| sessionExpirationMs | Uma sessão será registrada se continuar por esse período de tempo em milissegundos. | numeric<br/>86,4 milhões<br/>(24 horas) |
| maxBatchSizeInBytes | Tamanho máximo do lote de telemetria. Se um lote exceder esse limite, ele será imediatamente enviado e um novo lote será iniciado | numeric<br/>10000 |
| maxBatchInterval | Quanto tempo para a telemetria do lote antes de enviar (milissegundos) | numeric<br/>15000 |
| desabilitar&#8203;ExceptionTracking | Se for true, as exceções não serão coletadas. | booleano<br/> false |
| disableTelemetry | Se for true, a telemetria não será coletada ou enviada. | booleano<br/>false |
| enableDebug | Se for true, os dados de depuração **internos** serão lançados como uma exceção **em vez** de serem registrados, independentemente das configurações de log do SDK. O padrão é false. <br>**_Observação:_** A habilitação dessa configuração resultará em uma telemetria descartada sempre que ocorrer um erro interno. Isso pode ser útil para identificar rapidamente problemas com sua configuração ou uso do SDK. Se você não quiser perder a telemetria durante a depuração, considere usar `consoleLoggingLevel` ou `telemetryLoggingLevel` em vez de `enableDebug` . | booleano<br/>false |
| loggingLevelConsole | Registra erros **internos** de Application insights no console do. <br>0: desativado, <br>1: somente erros críticos, <br>2: tudo (erros & avisos) | numeric<br/> 0 |
| loggingLevelTelemetry | Envia erros **internos** de Application insights como telemetria. <br>0: desativado, <br>1: somente erros críticos, <br>2: tudo (erros & avisos) | numeric<br/> 1 |
| diagnosticLogInterval | interno Intervalo de sondagem (em MS) para fila de log interno | numeric<br/> 10000 |
| samplingPercentage | Porcentagem de eventos que serão enviados. O padrão é 100, o que significa que todos os eventos são enviados. Defina isso se desejar preservar o limite de dados para aplicativos de grande escala. | numeric<br/>100 |
| autoTrackPageVisitTime | Se for true, em um Pageview, o tempo de exibição da página instrumentada anterior será rastreado e enviado como telemetria e um novo temporizador será iniciado para o Pageview atual. | booleano<br/>false |
| disableAjaxTracking | Se for true, as chamadas AJAX não serão coletadas. | booleano<br/> false |
| disableFetchTracking | Se for true, as solicitações de busca não serão coletadas.|booleano<br/>true |
| overridePageViewDuration | Se for true, o comportamento padrão de trackPageView será alterado para registrar o final do intervalo de duração da exibição de página quando trackPageView for chamado. Se for false e nenhuma duração personalizada for fornecida para trackPageView, o desempenho de exibição de página será calculado usando a API de tempo de navegação. |booleano<br/>
| maxAjaxCallsPerView | Padrão 500-controla quantas chamadas AJAX serão monitoradas por exibição de página. Defina como-1 para monitorar todas as chamadas AJAX (ilimitadas) na página. | numeric<br/> 500 |
| disableDataLossAnalysis | Se for falso, os buffers do remetente da telemetria interna serão verificados na inicialização para os itens ainda não enviados. | booleano<br/> true |
| desabilitar&#8203;CorrelationHeaders | Se for false, o SDK adicionará dois cabeçalhos (' solicitação-ID ' e ' solicitação-contexto ') a todas as solicitações de dependência para correlacioná-los com as solicitações correspondentes no lado do servidor. | booleano<br/> false |
| correlationHeader&#8203;ExcludedDomains | Desabilitar cabeçalhos de correlação para domínios específicos | string[]<br/>não definido |
| correlationHeader&#8203;ExcludePatterns | Desabilitar cabeçalhos de correlação usando expressões regulares | Regex []<br/>não definido |
| correlationHeader domínios de&#8203; | Habilitar cabeçalhos de correlação para domínios específicos | string[]<br/>não definido |
| disableFlush&#8203;OnBeforeUnload | Se for true, o método Flush não será chamado quando o evento onBeforeUnload for disparado | booleano<br/> false |
| enableSessionStorageBuffer | Se for true, o buffer com todas as telemetrias não enviadas será armazenado no armazenamento de sessão. O buffer é restaurado no carregamento da página | booleano<br />true |
| cookieCfg | O padrão é o uso do cookie habilitado consulte Configurações de [ICookieCfgConfig](#icookiemgrconfig) para padrões completos. | [ICookieCfgConfig](#icookiemgrconfig)<br>(Desde 2.6.0)<br/>não definido |
| ~~isCookieUseDisabled~~<br>disableCookiesUsage | Se for true, o SDK não armazenará nem lerá nenhum dado de cookies. Observe que isso desabilita os cookies de usuário e de sessão e renderiza as folhas de uso e as experiências inúteis. isCookieUseDisable é preterido em favor de disableCookiesUsage, quando ambos são fornecidos disableCookiesUsage têm precedência.<br>(Desde v 2.6.0) E se `cookieCfg.enabled` também for definido, terá precedência sobre esses valores, o uso de cookies poderá ser reabilitado após a inicialização por meio do Core. getCookieMgr (). SetEnabled (true). | alias para [`cookieCfg.enabled`](#icookiemgrconfig)<br>false |
| cookieDomain | Domínio de cookie personalizado. Isso será útil se você quiser compartilhar Application Insights cookies entre subdomínios.<br>(Desde v 2.6.0) Se `cookieCfg.domain` for definido, terá precedência sobre esse valor. | alias para [`cookieCfg.domain`](#icookiemgrconfig)<br>nulo |
| cookiePath | Caminho do cookie personalizado. Isso será útil se você quiser compartilhar Application Insights cookies por trás de um gateway de aplicativo.<br>Se `cookieCfg.path` for definido, terá precedência sobre esse valor. | alias para [`cookieCfg.path`](#icookiemgrconfig)<br>(Desde 2.6.0)<br/>nulo |
| isRetryDisabled | Se for false, tente novamente 206 (êxito parcial), 408 (timeout), 429 (número excessivo de solicitações), 500 (erro interno do servidor), 503 (Serviço indisponível) e 0 (offline, somente se detectado) | booleano<br/>false |
| isStorageUseDisabled | Se for true, o SDK não armazenará nem lerá nenhum dado do armazenamento local e de sessão. | booleano<br/> false |
| isBeaconApiDisabled | Se for false, o SDK enviará toda a telemetria usando a [API de Beacon](https://www.w3.org/TR/beacon) | booleano<br/>true |
| onunloadDisableBeacon | Quando a guia for fechada, o SDK enviará toda a telemetria restante usando a [API de Beacon](https://www.w3.org/TR/beacon) | booleano<br/> false |
| sdkExtension | Define o nome da extensão do SDK. Somente caracteres alfabéticos são permitidos. O nome da extensão é adicionado como um prefixo à marca ' ia. Internal. sdkVersion ' (por exemplo, ' ext_javascript: 2.0.0 '). | string<br/> nulo |
| isBrowserLink&#8203;TrackingEnabled | Se for true, o SDK rastreará todas as solicitações de [link do navegador](/aspnet/core/client-side/using-browserlink) . | booleano<br/>false |
| appId | AppId é usado para a correlação entre dependências AJAX acontecendo no lado do cliente com as solicitações do lado do servidor. Quando a API de Beacon está habilitada, ela não pode ser usada automaticamente, mas pode ser definida manualmente na configuração. |string<br/> nulo |
| Habilitar&#8203;CorsCorrelation | Se for true, o SDK adicionará dois cabeçalhos (' solicitação-ID ' e ' solicitação-contexto ') a todas as solicitações de CORS para correlacionar as dependências do AJAX de saída com as solicitações correspondentes no lado do servidor. | booleano<br/>false |
| namePrefix | Um valor opcional que será usado como sufixo de nome para localStorage e o nome do cookie. | string<br/>não definido |
| Habilitar rastreamento de&#8203;de&#8203;AutoRoute | Rastreie automaticamente as alterações de rota em SPA (aplicativos de página única). Se for true, cada alteração de rota enviará um novo Pageview para Application Insights. As alterações de rota de hash ( `example.com/foo#bar` ) também são registradas como novas exibições de página.| booleano<br/>false |
| enableRequest&#8203;HeaderTracking | Se verdadeiro, o AJAX & cabeçalhos de solicitação de busca é acompanhado. | booleano<br/> false |
| enableResponse&#8203;HeaderTracking | Se for verdadeiro, os cabeçalhos de resposta da solicitação de busca do AJAX & serão controlados. | booleano<br/> false |
| distributedTracingMode | Define o modo de rastreamento distribuído. Se o modo de AI_AND_W3C ou o modo W3C for definido, os cabeçalhos de contexto de rastreamento do W3C (traceparent/tracestate) serão gerados e incluídos em todas as solicitações de saída. AI_AND_W3C é fornecida para compatibilidade com os serviços instrumentados Application Insightss herdados. Consulte o exemplo [aqui](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps).| `DistributedTracingModes`ou<br/>numeric<br/>(Desde v 2.6.0) `DistributedTracingModes.AI_AND_W3C`<br />(v 2.5.11 ou anterior) `DistributedTracingModes.AI` |
| Habilitar&#8203;AjaxErrorStatusText | Se for true, inclua o texto de dados de erro de resposta no evento de dependência em solicitações AJAX com falha. | booleano<br/> false |
| Habilitar&#8203;AjaxPerfTracking |Sinalizador para habilitar a pesquisa e a inclusão de janela adicional do navegador. os tempos de desempenho nas métricas relatadas `ajax` (XHR e FETCH) relatadas. | booleano<br/> false |
| maxAjaxPerf&#8203;LookupAttempts | O número máximo de vezes para procurar a janela. os tempos de desempenho (se disponíveis), isso é necessário, pois nem todos os navegadores preenchem a janela. o desempenho antes de relatar o final da solicitação XHR e, para buscar solicitações, isso é adicionado após sua conclusão.| numeric<br/> 3 |
| ajaxPerfLookupDelay | A quantidade de tempo de espera antes de tentar novamente localizar os intervalos do Windows. performance para uma `ajax` solicitação, o tempo é em milissegundos e é passado diretamente para setTimeout (). | numeric<br/> 25 MS |
| Acompanhamento do enableUnhandled&#8203;PromiseRejection&#8203; | Se verdadeiro, as rejeições de promessa sem tratamento serão coletadas e relatadas como um erro de JavaScript. Quando disableExceptionTracking for true (não rastrear exceções), o valor de configuração será ignorado e as rejeições de promessa sem tratamento não serão relatadas. | booleano<br/> false |
| desabilitar&#8203;validação de&#8203;InstrumentationKey | Se for true, a verificação de validação da chave de instrumentação será ignorada. | booleano<br/>false |
| enablePerfMgr | Quando habilitado (true), isso criará perfEvents local para o código que foi instrumentado para emitir perfEvents (por meio do auxiliar doperf ()). Isso pode ser usado para identificar problemas de desempenho no SDK com base no seu uso ou, opcionalmente, em seu próprio código instrumentado. [Mais detalhes estão disponíveis na documentação básica](https://github.com/microsoft/ApplicationInsights-JS/blob/master/docs/PerformanceMonitoring.md). Desde v 2.5.7 | booleano<br/>false |
| perfEvtsSendAll | Quando _enablePerfMgr_ está habilitado e o [IPerfManager](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/IPerfManager.ts) aciona um [INotificationManager](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/INotificationManager.ts). perfEvent (), esse sinalizador determina se um evento é disparado (e enviado a todos os ouvintes) para todos os eventos (true) ou apenas para eventos ' Parent ' (falso &lt; padrão &gt; ).<br />Um [IPerfEvent](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/IPerfEvent.ts) pai é um evento em que nenhum outro IPerfEvent ainda está em execução no ponto desse evento que está sendo criado e sua propriedade _pai_ não é nula ou indefinida. Desde v 2.5.7 |  booleano<br />false |
| idLength | Identifica o comprimento padrão usado para gerar novos valores de sessão aleatória e de ID de usuário. O padrão é 22, o valor padrão anterior era 5 (v 2.5.8 ou menos), se você precisar manter o comprimento máximo anterior, deverá definir esse valor como 5. |  numeric<br />22 |

## <a name="cookie-handling"></a>Manipulação de cookies

Da versão 2.6.0, o gerenciamento de cookies agora está disponível diretamente da instância do e pode ser desabilitado e habilitado novamente após a inicialização.

Se desabilitado durante a inicialização por meio das `disableCookiesUsage` configurações do ou `cookieCfg.enabled` , agora você poderá reabilitar por meio da função [ICookieMgr](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts) `setEnabled` .

O gerenciamento de cookies baseado em instâncias também substitui as funções globais CoreUtils anteriores do `disableCookies()` , do `setCookie(...)` e do `getCookie(...)` `deleteCookie(...)` . E para se beneficiar dos aprimoramentos de inversão em árvore também introduzidos como parte da 2.6.0 de versões, você não deve mais usar as funções globais.

### <a name="icookiemgrconfig"></a>ICookieMgrConfig

Configuração de cookie para gerenciamento de cookie baseado em instância adicionado na versão 2.6.0.

| Name | Descrição | Tipo e padrão |
|------|-------------|------------------|
| Habilitado | Um booliano que indica se o uso de cookies pelo SDK está habilitado pela instância atual. Se for false, a instância do SDK inicializada por essa configuração não armazenará nem lerá nenhum dado de cookies | booleano<br/> true |
| domínio | Domínio de cookie personalizado. Isso será útil se você quiser compartilhar Application Insights cookies entre subdomínios. Se não fornecido, usa o valor do `cookieDomain` valor raiz. | string<br/>nulo |
| caminho | Especifica o caminho a ser usado para o cookie, caso não tenha sido fornecido, ele usará qualquer valor do `cookiePath` valor raiz. | string <br/> / |
| GetCookie | Função para buscar o valor de Cookie nomeado, caso não tenha sido fornecido, ele usará a análise/cache do cookie interno. | `(name: string) => string` <br/> nulo |
| SetCookie | Função para definir o Cookie nomeado com o valor especificado, somente chamado ao adicionar ou atualizar um cookie. | `(name: string, value: string) => void` <br/> nulo |
| delCookie | Função para excluir o Cookie nomeado com o valor especificado, separado do SetCookie, para evitar a necessidade de analisar o valor para determinar se o cookie está sendo adicionado ou removido. Se não for fornecido, ele usará a análise/cache do cookie interno. | `(name: string, value: string) => void` <br/> nulo |

### <a name="simplified-usage-of-new-instance-cookie-manager"></a>Uso simplificado do novo Gerenciador de cookie de instância

- appInsights. [getCookieMgr ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). sethabilitad (true/false);
- appInsights. [getCookieMgr ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). Set ("myCookie", "o% 20encoded% 20value");
- appInsights. [getCookieMgr ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). Get ("myCookie");
- appInsights. [getCookieMgr ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). del ("myCookie");

## <a name="enable-time-on-page-tracking"></a>Habilitar rastreamento de tempo na página

Por configuração `autoTrackPageVisitTime: true` , o tempo que um usuário gasta em cada página é acompanhado. Em cada novo PageView, a duração que o usuário gastou na página *anterior* é enviada como uma [métrica personalizada](../essentials/metrics-custom-overview.md) chamada `PageVisitTime` . Essa métrica personalizada é visível na [Metrics Explorer](../essentials/metrics-getting-started.md) como uma "métrica baseada em log".

## <a name="enable-correlation"></a>Habilitar correlação

A correlação gera e envia dados que habilitam o rastreamento distribuído e alimentam o [mapa do aplicativo](../app/app-map.md), a exibição de [transação de ponta a ponta](../app/app-map.md#go-to-details)e outras ferramentas de diagnóstico.

O exemplo a seguir mostra todas as configurações possíveis necessárias para habilitar a correlação, com observações específicas do cenário abaixo:

```javascript
// excerpt of the config section of the JavaScript SDK snippet with correlation
// between client-side AJAX and server requests enabled.
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    disableFetchTracking: false,
    enableCorsCorrelation: true,
    enableRequestHeaderTracking: true,
    enableResponseHeaderTracking: true,
    correlationHeaderExcludedDomains: ['myapp.azurewebsites.net', '*.queue.core.windows.net']
    /* ...Other Configuration Options... */
}});
</script>

``` 

Se qualquer um dos servidores de terceiros aos quais o cliente se comunica não puder aceitar os `Request-Id` cabeçalhos e e `Request-Context` você não puder atualizar sua configuração, você precisará colocá-los em uma lista de exclusões por meio da `correlationHeaderExcludeDomains` propriedade de configuração. Esta propriedade dá suporte a curingas.

O lado do servidor precisa ser capaz de aceitar conexões com esses cabeçalhos presentes. Dependendo da `Access-Control-Allow-Headers` configuração no lado do servidor, muitas vezes é necessário estender a lista do lado do servidor adicionando `Request-Id` e `Request-Context` .

Acesso-controle-permitir-cabeçalhos: `Request-Id` , `Request-Context` , `<your header>`

> [!NOTE]
> Se você estiver usando o OpenTelemtry ou Application Insights SDKs lançados no 2020 ou posterior, é recomendável usar o [WC3 TraceContext](https://www.w3.org/TR/trace-context/). Consulte as diretrizes de configuração [aqui](../app/correlation.md#enable-w3c-distributed-tracing-support-for-web-apps).

## <a name="single-page-applications"></a>Aplicativos de página única

Por padrão, esse SDK **não** tratará da alteração de rota baseada em estado que ocorre em aplicativos de página única. Para habilitar o controle de alterações de rota automático para seu aplicativo de página única, você pode adicionar `enableAutoRouteTracking: true` à sua configuração de instalação.

Atualmente, oferecemos um [plug-in reajam](javascript-react-plugin.md)separado, que pode ser inicializado com esse SDK. Ele também realizará o controle de alterações de rota para você, além de coletar outra telemetria específica de reagir.
> [!NOTE]
> Use `enableAutoRouteTracking: true` somente se você **não** estiver usando o plug-in reagir. Ambos são capazes de enviar novos PageViews quando a rota é alterada. Se ambos estiverem habilitados, PageViews duplicados poderão ser enviados.

## <a name="extensions"></a>Extensões

| Extensões |
|---------------|
| [React](javascript-react-plugin.md)|
| [React Native](javascript-react-native-plugin.md)|
| [Angular](javascript-angular-plugin.md)|
| [Clique em coleção de análise automática](javascript-click-analytics-plugin.md)|

## <a name="explore-browserclient-side-data"></a>Explorar dados do navegador/cliente

Os dados do navegador/cliente podem ser exibidos acessando **métricas** e adicionando métricas individuais nas quais você está interessado:

![Captura de tela da página métricas em Application Insights mostrando exibições gráficas de dados de métricas para um aplicativo Web.](./media/javascript/page-view-load-time.png)

Você também pode exibir seus dados do SDK do JavaScript por meio da experiência do navegador no Portal.

Selecione **navegador** e, em seguida, escolha **falhas** ou **desempenho**.

![Captura de tela da página do navegador no Application Insights mostrando como adicionar falhas do navegador ou o desempenho do navegador às métricas que você pode exibir para seu aplicativo Web.](./media/javascript/browser.png)

### <a name="performance"></a>Desempenho

![Captura de tela da página desempenho em Application Insights mostrando exibições gráficas de métricas de operações para um aplicativo Web.](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Dependências

![Captura de tela da página desempenho em Application Insights mostrando exibições gráficas de métricas de dependência para um aplicativo Web.](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Análise

Para consultar sua telemetria coletada pelo SDK do JavaScript, selecione o botão **Exibir em logs (análise)** . Ao adicionar uma `where` instrução do `client_Type == "Browser"` , você verá apenas os dados do SDK do JavaScript e qualquer telemetria do lado do servidor coletada por outros SDKs será excluída.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Suporte ao mapa de origem

A pilha de chamadas reduzidos da sua telemetria de exceção pode ser unminified no portal do Azure. Todas as integrações existentes no painel detalhes da exceção funcionarão com o unminified pilha de chamadas recentemente.

#### <a name="link-to-blob-storage-account"></a>Vincular à conta de armazenamento de BLOBs

Você pode vincular seu recurso de Application Insights ao seu próprio contêiner de armazenamento de BLOBs do Azure para unminify automaticamente as pilhas de chamadas. Para começar, consulte [suporte ao mapa de origem automático](./source-map-support.md).

### <a name="drag-and-drop"></a>Arrastar e soltar

1. Selecione um item de telemetria de exceção no portal do Azure para exibir seus "detalhes de transação de ponta a ponta"
2. Identifique quais mapas de origem correspondem a esta pilha de chamadas. O mapa de origem deve corresponder ao arquivo de origem de um quadro de pilha, mas com sufixo `.map`
3. Arraste e solte os mapas de origem na pilha de chamadas no portal do Azure ![ uma imagem animada mostrando como arrastar e soltar arquivos de mapa de origem de uma pasta de compilação para a janela pilha de chamadas no portal do Azure.](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

Para uma experiência leve, você pode instalar a versão básica do Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Essa versão vem com o número mínimo de recursos e funcionalidades e se baseia em você para compilá-lo como desejar. Por exemplo, ele não executa a autocoleção (exceções não capturadas, AJAX, etc.). As APIs para enviar determinados tipos de telemetria, como `trackTrace` , `trackException` etc., não estão incluídas nesta versão, portanto, você precisará fornecer seu próprio wrapper. A única API que está disponível é `track` . Um [exemplo](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) está localizado aqui.

## <a name="examples"></a>Exemplos

Para obter exemplos de executáveis, consulte [Application insights exemplos de SDK do JavaScript](https://github.com/Azure-Samples?q=applicationinsights-js-demo).

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Atualizando da versão antiga do Application Insights

Alterações recentes na versão do SDK v2:
- Para permitir melhores assinaturas de API, algumas das chamadas à API, como trackPageView e trackexception, foram atualizadas. Não há suporte para a execução no Internet Explorer 8 e em versões anteriores do navegador.
- O envelope de telemetria tem alterações de estrutura e nome de campo devido a atualizações de esquema de dados.
- Movido `context.operation` para `context.telemetryTrace` . Alguns campos também foram alterados ( `operation.id`  -->  `telemetryTrace.traceID` ).
  - Para atualizar manualmente a ID de Pageview atual (por exemplo, em aplicativos de SPA), use `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()` .
    > [!NOTE]
    > Para manter a ID de rastreamento exclusiva, onde você usou anteriormente `Util.newId()` , agora use `Util.generateW3CId()` . Os dois, finalmente, acabam sendo a ID da operação.

Se você estiver usando o 1.0.20 (SDK de produção do Application insights) atual e quiser ver se o novo SDK funciona em tempo de execução, atualize a URL dependendo do seu cenário de carregamento do SDK atual.

- Baixar por meio do cenário da CDN: Atualize o trecho de código que você usa atualmente para apontar para a seguinte URL:
   ```
   "https://js.monitor.azure.com/scripts/b/ai.2.min.js"
   ```

- cenário de NPM: chame `downloadAndSetup` para baixar o script ApplicationInsights completo da CDN e inicialize-o com a chave de instrumentação:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://js.monitor.azure.com/scripts/b/ai.2.min.jss"
     });
   ```

Teste no ambiente interno para verificar se a telemetria de monitoramento está funcionando conforme o esperado. Se tudo funcionar, atualize suas assinaturas de API adequadamente para a versão do SDK V2 e implante em seus ambientes de produção.

## <a name="sdk-performanceoverhead"></a>Desempenho/sobrecarga do SDK

Em apenas 36 KB gzip e levando apenas ~ 15 ms para inicializar, Application Insights adiciona uma quantidade insignificante de loadtime ao seu site. Usando o trecho de código, os componentes mínimos da biblioteca são carregados rapidamente. Enquanto isso, o script completo é baixado em segundo plano.

Enquanto o script está sendo baixado da CDN, todo o acompanhamento de sua página é enfileirado. Depois que o script baixado terminar a inicialização assíncrona, todos os eventos que foram enfileirados serão rastreados. Como resultado, você não perderá nenhuma telemetria durante todo o ciclo de vida de sua página. Esse processo de instalação fornece à sua página um sistema de análise contínuo, invisível para os usuários.

> Resumo:
> - ![versão do NPM](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![Tamanho compactado gzip](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - tempo de inicialização geral de **15 ms**
> - Controle **zero** perdido durante o ciclo de vida da página

## <a name="browser-support"></a>Suporte ao navegador

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
✔ Mais recente do Chrome |  ✔ Mais recente do Firefox | ✔ De borda do IE 9 + &<br>Compatível com IE 8 | ✔ Mais recentes do Operations | ✔ Mais recente do Safari |

## <a name="es3ie8-compatibility"></a>Compatibilidade do ES3/IE8

Como um SDK, há inúmeros usuários que não podem controlar os navegadores que seus clientes usam. Como tal, precisamos garantir que esse SDK continue a "trabalhar" e não interrompa a execução do JS quando for carregado por um navegador mais antigo. Embora seja ideal para não oferecer suporte a navegadores do IE8 e de geração mais antiga (ES3), há vários clientes/usuários grandes que continuam a exigir que as páginas "funcionem" e, como observado, podem ou não controlar qual navegador os usuários finais optam por usar.

Isso não significa que ofereceremos suporte apenas ao conjunto comum mais baixo de recursos, apenas que precisamos manter a compatibilidade de código ES3 e ao adicionar novos recursos que precisarão ser adicionados de uma maneira que não interrompa a análise de JavaScript ES3 e adicionamos como um recurso opcional.

[Consulte o GitHub para obter detalhes completos sobre o suporte do IE8](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>SDK do código-fonte aberto

A Application Insights SDK do JavaScript é de software livre para exibir o código-fonte ou para contribuir com o projeto, visite o [repositório oficial do GitHub](https://github.com/Microsoft/ApplicationInsights-JS). 

Para obter as atualizações mais recentes e as correções de bugs, [consulte as notas de versão](./release-notes.md).

## <a name="next-steps"></a><a name="next"></a> Próximas etapas
* [Acompanhar uso](usage-overview.md)
* [Eventos e métricas personalizados](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)
* [Solucionar problemas de falha de carregamento do SDK](javascript-sdk-load-failure.md)
