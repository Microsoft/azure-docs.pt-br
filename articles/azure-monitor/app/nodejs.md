---
title: Como monitorar os serviços Node.js com o Application Insights do Azure | Microsoft Docs
description: Monitore o desempenho e diagnostique problemas em serviços do Node.js com o Application Insights.
services: application-insights
documentationcenter: nodejs
author: mrbullwinkle
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: 13379111706eaa816a8fa16cfe72711b7bf4d739
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576437"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Como monitorar seus serviços do Node.js e aplicativos com o Application Insights

O [Application Insights do Azure](../../azure-monitor/app/app-insights-overview.md) monitora seus componentes e serviços de back-end depois da implantação para ajudá-lo a [detectar e diagnosticar rapidamente problemas de desempenho, entre outros](../../azure-monitor/app/detect-triage-diagnose.md). Você pode usar o Application Insights para serviços Node.js hospedados em seu data center, em aplicativos Web e VMs do Azure e até mesmo em outras nuvens públicas.

Para receber, armazenar e explorar os dados de monitoramento, incluir o SDK em seu código e, em seguida, configurar um recurso do Application Insights correspondente no Azure. O SDK envia dados a esse recurso para análise e exploração.

O SDK do Node.js pode monitorar automaticamente as solicitações HTTP de entrada e saída, exceções e algumas métricas de sistema. Começando na versão 0.20, o SDK também pode monitorar alguns pacotes de terceiros comuns, como MongoDB, MySQL e Redis. Todos os eventos relacionados a uma solicitação HTTP de entrada são correlacionados para solução de problemas mais rápida.

Você pode usar a API TelemetryClient para instrumentar e monitorar manualmente outros aspectos do aplicativo e do sistema. Descrevemos a API TelemetryClient em mais detalhes mais adiante neste artigo.

## <a name="get-started"></a>Introdução

Conclua as seguintes tarefas para configurar o monitoramento em um aplicativo ou serviço.

### <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar, verifique se você tem uma assinatura do Azure ou [obtenha uma gratuitamente][azure-free-offer]. Se sua organização já tiver uma assinatura do Azure, um administrador pode seguir [estas instruções][add-aad-user] para adicioná-lo a ela.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: https://docs.microsoft.com/azure/active-directory/active-directory-users-create-azure-portal


### <a name="resource"></a> Configurar um recurso do Application Insights


1. Entre no [Portal do Azure][portal].
2. Selecione **Criar um recurso** > **Ferramentas de desenvolvedor** > **Application Insights**. O recurso inclui um ponto de extremidade para receber dados de telemetria, armazenamento para esses dados, painéis e relatórios salvos, configurações de alerta e regra e muito mais.

3. Na página de criação de recursos, na caixa **Tipo de Aplicativo**, selecione **Aplicativo Node.js**. O tipo de aplicativo determina os painéis e relatórios padrão a serem criados. (Qualquer recurso do Application Insights pode coletar dados de qualquer idioma e plataforma.)

### <a name="sdk"></a>Configurar o SDK do Node.js

Inclua o SDK em seu aplicativo, para que ele possa coletar dados. 

1. Copie a Chave de Instrumentação do recurso (também chamada de *ikey*) no portal do Azure. O Application Insights usa a ikey para mapear os dados para o recurso do Azure. Antes de o SDK poder usar a ikey, você deverá especificar a ikey em uma variável de ambiente ou em seu código.  

   ![Copie a chave de instrumentação](./media/nodejs/instrumentation-key-001.png)

2. Adicione a biblioteca de SDK do Node.js para as dependências do seu aplicativo por meio do package.json. Na pasta raiz do seu aplicativo, execute:

   ```bash
   npm install applicationinsights --save
   ```

3. Carregue a biblioteca em seu código explicitamente. Como o SDK injeta instrumentação em muitas outras bibliotecas, carregue a biblioteca o mais cedo possível, mesmo antes de outras instruções `require`. 

   Na parte superior do seu primeiro arquivo .js, adicione o código a seguir. O método `setup` configura a chave (e, portanto, os recursos do Azure) a ser usada por padrão para todos os itens rastreados.

   ```javascript
   const appInsights = require("applicationinsights");
   appInsights.setup("<instrumentation_key>");
   appInsights.start();
   ```
   
   Você também pode fornecer um ikey por meio da variável de ambiente APPINSIGHTS\_INSTRUMENTATIONKEY em vez de transmiti-lo manualmente ao `setup()` ou `new appInsights.TelemetryClient()`. Essa prática permite manter ikeys fora do código-fonte comprometido e também especificar ikeys diferentes para ambientes diferentes.

   Para outras opções de configuração, confira as seções a seguir.

   Você pode experimentar o SDK sem enviar telemetria definindo `appInsights.defaultClient.config.disableAppInsights = true`.

### <a name="monitor"></a>Monitore o seu aplicativo

O SDK reúne automaticamente a telemetria sobre o tempo de execução do Node.js e sobre alguns módulos de terceiros comuns. Use o aplicativo para gerar alguns desses dados.

Em seguida, no [portal do Azure][portal], vá para o recurso do Application Insights criado anteriormente. Na **Linha do tempo de visão geral**, procure seus primeiros pontos de dados. Para obter dados mais detalhadas, selecione componentes diferentes nos gráficos.

Para exibir a topologia que foi descoberta para seu aplicativo, selecione o botão **Mapa de aplicativo**. Selecione os componentes no mapa para ver mais detalhes.

![Mapa de aplicativo simples](./media/nodejs/application-map-002.png)

Para saber mais sobre o aplicativo e solucionar problemas, selecione os outros modos de exibição disponíveis na seção **INVESTIGAR**.

![Seção Investigar](./media/nodejs/007-investigate-pane.png)

#### <a name="no-data"></a>Não há dados?

Como o SDK envia lotes de dados, pode haver um atraso antes de os itens serem exibidos no portal. Se você não visualizar os dados em seu recurso, experimente algumas das seguintes correções:

* Continue a usar o aplicativo. Execute mais ações para gerar mais telemetria.
* Clique em **Atualizar** no modo de exibição de recursos do portal. Os gráficos se atualizam sozinhos de tempos em tempos, mas a atualização manual força a atualização imediatamente.
* Verifique se [as portas de saída obrigatórias](../../azure-monitor/app/ip-addresses.md) estão abertas.
* Use [Pesquisar](../../azure-monitor/app/diagnostic-search.md) para procurar eventos específicos.
* Confira as [Perguntas Frequentes][FAQ].


## <a name="sdk-configuration"></a>Configuração do SDK

Os métodos de configuração do SDK e os valores padrão estão listados no exemplo de código a seguir.

Para correlacionar totalmente eventos em um serviço, você deve definir `.setAutoDependencyCorrelation(true)`. Com essa opção definida, o SDK pode acompanhar o contexto entre retornos de chamada assíncronos no Node.js.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .start();
```

## <a name="telemetryclient-api"></a>API TelemetryClient

Para obter uma descrição completa da API TelemetryClient, confira [API do Application Insights para métricas e eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md).

Você pode acompanhar qualquer solicitação, evento, métrica ou exceção usando o SDK do Node.js para Application Insights. O exemplo de código abaixo demonstra algumas das APIs que você pode usar:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey is in env var
let client = appInsights.defaultClient;

client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Acompanhamento das suas dependências

Use o código abaixo para acompanhar suas dependências:

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.defaultClient;

var success = false;
let startTime = Date.now();
// Execute dependency call here...
let duration = Date.now() - startTime;
success = true;

client.trackDependency({dependencyTypeName: "dependency name", name: "command name", duration: duration, success: success});
```

### <a name="add-a-custom-property-to-all-events"></a>Adição de uma propriedade personalizada para todos os eventos

Use o código abaixo para adicionar uma propriedade personalizada a todos os eventos:

```javascript
appInsights.defaultClient.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Acompanhamento das solicitações GET HTTP

Use o código abaixo para controlar as solicitações GET HTTP:

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.defaultClient.trackNodeHttpRequest({request: req, response: res});
    }
    // Other work here...
    res.end();
});
```

### <a name="track-server-startup-time"></a>Tempo de inicialização do servidor de acompanhamento

Use o código abaixo para acompanhar o tempo de inicialização do servidor:

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

## <a name="next-steps"></a>Próximas etapas

* [Monitorar sua telemetria no portal](../../azure-monitor/app/app-insights-dashboards.md)
* [Escrever consultas de análise sobre a telemetria](../../azure-monitor/log-query/get-started-portal.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../../azure-monitor/app/troubleshoot-faq.md

