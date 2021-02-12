---
title: Como monitorar os serviços Node.js com o Application Insights do Azure | Microsoft Docs
description: Monitore o desempenho e diagnostique problemas em serviços do Node.js com o Application Insights.
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 0d414ce44a8d6ab308bd31f7372bb1c146fac9f5
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611008"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Como monitorar seus serviços do Node.js e aplicativos com o Application Insights

O [Application insights](./app-insights-overview.md) monitora seus serviços de back-end e componentes após a implantação, para ajudá-lo a descobrir e diagnosticar rapidamente o desempenho e outros problemas. Você pode usar Application Insights para Node.js serviços hospedados em seu datacenter, em VMs do Azure e em aplicativos Web e até mesmo em outras nuvens públicas.

Para receber, armazenar e explorar os dados de monitoramento, incluir o SDK em seu código e, em seguida, configurar um recurso do Application Insights correspondente no Azure. O SDK envia dados a esse recurso para análise e exploração.

O SDK do Node.js pode monitorar automaticamente as solicitações HTTP de entrada e saída, exceções e algumas métricas de sistema. A partir da versão 0,20, o SDK também pode monitorar alguns [pacotes de terceiros](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules)comuns, como MongoDB, MySQL e Redis. Todos os eventos relacionados a uma solicitação HTTP de entrada são correlacionados para solução de problemas mais rápida.

Você pode usar a API TelemetryClient para instrumentar e monitorar manualmente outros aspectos do aplicativo e do sistema. Descrevemos a API TelemetryClient em mais detalhes mais adiante neste artigo.

## <a name="get-started"></a>Introdução

Conclua as seguintes tarefas para configurar o monitoramento em um aplicativo ou serviço.

### <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar, verifique se você tem uma assinatura do Azure ou [obtenha uma gratuitamente][azure-free-offer]. Se sua organização já tiver uma assinatura do Azure, um administrador pode seguir [estas instruções][add-aad-user] para adicioná-lo a ela.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: ../../active-directory/fundamentals/add-users-azure-active-directory.md

### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a> Configurar um recurso de Application Insights

1. Entre no [portal do Azure][portal].
2. [Criar um recurso do Application Insights](create-new-resource.md)

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a>Configurar o SDK do Node.js

Inclua o SDK em seu aplicativo, para que ele possa coletar dados.

> [!IMPORTANT]
> Novas regiões do Azure **exigem** o uso de cadeias de conexão em vez de chaves de instrumentação. A [cadeia de conexão](./sdk-connection-string.md?tabs=nodejs) identifica o recurso ao qual você deseja associar os dados de telemetria. Ele também permite que você modifique os pontos de extremidade que o recurso usará como um destino para a telemetria. Você precisará copiar a cadeia de conexão e adicioná-la ao código do aplicativo ou a uma variável de ambiente.

1. Copie a chave de instrumentação do recurso (também chamada de *iKey*) de seu recurso recém-criado. O Application Insights usa a ikey para mapear os dados para o recurso do Azure. Antes de o SDK poder usar a ikey, você deverá especificar a ikey em uma variável de ambiente ou em seu código.  

   ![Copie a chave de instrumentação](./media/nodejs/instrumentation-key-001.png)

2. Adicione a biblioteca de SDK do Node.js para as dependências do seu aplicativo por meio do package.json. Na pasta raiz do seu aplicativo, execute:

   ```bash
   npm install applicationinsights --save
   ```

    > [!NOTE]
    > Se você estiver usando o TypeScript, não instale pacotes de "digitações" separados. Este pacote NPM contém digitações internas.

3. Carregue a biblioteca em seu código explicitamente. Como o SDK injeta instrumentação em muitas outras bibliotecas, carregue a biblioteca o mais cedo possível, mesmo antes de outras instruções `require`.

   ```javascript
   let appInsights = require('applicationinsights');
   ```
4.  Você também pode fornecer um iKey por meio da variável de ambiente `APPINSIGHTS_INSTRUMENTATIONKEY` , em vez de passá-lo manualmente para  `setup()` ou `new appInsights.TelemetryClient()` . Essa prática permite manter ikeys fora do código-fonte comprometido e também especificar ikeys diferentes para ambientes diferentes. Para configurar a chamada manualmente `appInsights.setup('[your ikey]');` .

    Para outras opções de configuração, confira as seções a seguir.

    Você pode experimentar o SDK sem enviar telemetria definindo `appInsights.defaultClient.config.disableAppInsights = true`.

5. Inicie a coleta e o envio de dados automaticamente chamando `appInsights.start();` .

### <a name="monitor-your-app"></a><a name="monitor"></a> Monitorar seu aplicativo

O SDK reúne automaticamente a telemetria sobre o tempo de execução de Node.js e alguns módulos de terceiros comuns. Use o aplicativo para gerar alguns desses dados.

Em seguida, no [portal do Azure][portal], vá para o recurso do Application Insights criado anteriormente. Na **Linha do tempo de visão geral**, procure seus primeiros pontos de dados. Para obter dados mais detalhadas, selecione componentes diferentes nos gráficos.

Para exibir a topologia descoberta para seu aplicativo, você pode usar o [mapa do aplicativo](app-map.md).

#### <a name="no-data"></a>Sem dados

Como o SDK envia lotes de dados, pode haver um atraso antes de os itens serem exibidos no portal. Se você não visualizar os dados em seu recurso, experimente algumas das seguintes correções:

* Continue a usar o aplicativo. Execute mais ações para gerar mais telemetria.
* Clique em **Atualizar** no modo de exibição de recursos do portal. Os gráficos se atualizam sozinhos de tempos em tempos, mas a atualização manual força a atualização imediatamente.
* Verifique se [as portas de saída obrigatórias](./ip-addresses.md) estão abertas.
* Use [Pesquisar](./diagnostic-search.md) para procurar eventos específicos.
* Verifique as [perguntas frequentes][FAQ].

## <a name="basic-usage"></a>Uso básico

Para uma coleção integrada de solicitações HTTP, eventos de biblioteca de terceiros populares, exceções sem tratamento e métricas do sistema:

```javascript

let appInsights = require("applicationinsights");
appInsights.setup("[your ikey]").start();

```

> [!NOTE]
> Se a chave de instrumentação for definida na variável de ambiente `APPINSIGHTS_INSTRUMENTATIONKEY` , o `.setup()` poderá ser chamado sem argumentos. Isso facilita o uso de diferentes ikeys para ambientes diferentes.

Carregue a biblioteca de Application Insights, o `require("applicationinsights")` mais cedo possível em seus scripts antes de carregar outros pacotes. Isso é necessário para que a biblioteca de Application Insights possa preparar pacotes posteriores para rastreamento. Se você encontrar conflitos com outras bibliotecas fazendo uma preparação semelhante, tente carregar a biblioteca de Application Insights depois delas.

Devido à maneira como o JavaScript manipula retornos de chamada, é necessário trabalho adicional para rastrear uma solicitação entre dependências externas e retornos de chamada posteriores. Por padrão, esse controle adicional é habilitado; Desabilite- `setAutoDependencyCorrelation(false)` a chamando conforme descrito na seção [configuração](#sdk-configuration) abaixo.

## <a name="migrating-from-versions-prior-to-022"></a>Migrando de versões anteriores a 0,22

Há alterações significativas entre as versões anteriores à versão 0,22 e posteriores. Essas alterações foram projetadas para trazer consistência com outros SDKs de Application Insights e permitir extensibilidade futura.

Em geral, você pode migrar com o seguinte:

- Substitua referências a `appInsights.client` com `appInsights.defaultClient` .
- Substituir referências a `appInsights.getClient()` por `new appInsights.TelemetryClient()`
- Substitua todos os argumentos para os métodos Client. Track * por um único objeto que contém propriedades nomeadas como argumentos. Consulte a dica de tipo interno do IDE ou [TelemetryTypes](https://github.com/Microsoft/ApplicationInsights-node.js/tree/develop/Declarations/Contracts/TelemetryTypes) para o objeto excetuado para cada tipo de telemetria.

Se você acessar as funções de configuração do SDK sem encadear-las ao `appInsights.setup()` , agora poderá encontrar essas funções em `appInsights.Configurations` (por exemplo, `appInsights.Configuration.setAutoCollectDependencies(true)` ). Examine as alterações na configuração padrão na próxima seção.

## <a name="sdk-configuration"></a>Configuração do SDK

O `appInsights` objeto fornece vários métodos de configuração. Eles são listados no trecho a seguir com seus valores padrão.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true, true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .setSendLiveMetrics(false)
    .setDistributedTracingMode(appInsights.DistributedTracingModes.AI)
    .start();
```

Para correlacionar totalmente eventos em um serviço, você deve definir `.setAutoDependencyCorrelation(true)`. Com essa opção definida, o SDK pode acompanhar o contexto entre retornos de chamada assíncronos no Node.js.

Examine suas descrições na dica de tipo interno do IDE ou [applicationinsights. TS](https://github.com/microsoft/ApplicationInsights-node.js/blob/develop/applicationinsights.ts) para obter informações detalhadas sobre o que esses controles e os argumentos secundários opcionais.

> [!NOTE]
>  Por padrão, o `setAutoCollectConsole` é configurado para *excluir* chamadas para `console.log` (e outros métodos de console). Somente chamadas para agentes de terceiros com suporte (por exemplo, Winston e Bunyan) serão coletadas. Você pode alterar esse comportamento para incluir chamadas para `console` métodos usando o `setAutoCollectConsole(true, true)` .

### <a name="sampling"></a>amostragem

Por padrão, o SDK enviará todos os dados coletados para o serviço de Application Insights. Se você coletar muitos dados, talvez queira habilitar a amostragem para reduzir a quantidade de dados enviados. Defina o `samplingPercentage` campo no `config` objeto de um cliente para fazer isso. Definir `samplingPercentage` como 100 (o padrão) significa que todos os dados serão enviados e 0 significa que nada será enviado.

Se você estiver usando correlação automática, todos os dados associados a uma única solicitação serão incluídos ou excluídos como uma unidade.

Adicione um código como o seguinte para habilitar a amostragem:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.config.samplingPercentage = 33; // 33% of all telemetry will be sent to Application Insights
appInsights.start();
```

### <a name="multiple-roles-for-multi-components-applications"></a>Várias funções para aplicativos de vários componentes

Se seu aplicativo consiste em vários componentes que você deseja instrumentar com a mesma chave de instrumentação e ainda vir esses componentes como unidades separadas no portal, como se estivessem usando chaves de instrumentação separadas (por exemplo, como nós separados no mapa do aplicativo), talvez seja necessário configurar manualmente o campo RoleName para distinguir a telemetria de um componente de outros componentes que enviam dados para Application Insights o recurso

Use o seguinte para definir o campo RoleName:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.context.tags[appInsights.defaultClient.context.keys.cloudRole] = "MyRoleName";
appInsights.start();
```

### <a name="automatic-third-party-instrumentation"></a>Instrumentação automática de terceiros

Para rastrear o contexto entre chamadas assíncronas, algumas alterações são necessárias em bibliotecas de terceiros, como MongoDB e Redis. Por padrão, Application Insights será usado [`diagnostic-channel-publishers`](https://github.com/Microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers) para aplicar patches a algumas dessas bibliotecas. Isso pode ser desabilitado definindo a `APPLICATION_INSIGHTS_NO_DIAGNOSTIC_CHANNEL` variável de ambiente.

> [!NOTE]
> Ao definir essa variável de ambiente, os eventos podem não estar mais corretamente associados à operação correta.

 Macaco individual-os patches podem ser desabilitados definindo a `APPLICATION_INSIGHTS_NO_PATCH_MODULES` variável de ambiente como uma lista separada por vírgulas de pacotes a serem desabilitados (por exemplo, `APPLICATION_INSIGHTS_NO_PATCH_MODULES=console,redis` ) para evitar a aplicação de patches nos `console` `redis` pacotes e.

Atualmente, há nove pacotes que são instrumentados:,,,,, `bunyan` `console` ,, `mongodb` `mongodb-core` `mysql` `redis` `winston` `pg` e `pg-pool` . Visite o [Leiame Diagnostic-Channel-Publishers](https://github.com/Microsoft/node-diagnostic-channel/blob/master/src/diagnostic-channel-publishers/README.md) para obter informações sobre exatamente quais versões desses pacotes são corrigidas.

Os `bunyan` `winston` `console` patches, e irão gerar Application insights eventos de rastreamento com base em se o `setAutoCollectConsole` está habilitado. O restante irá gerar Application Insights eventos de dependência com base no fato de `setAutoCollectDependencies` estar habilitado.

### <a name="live-metrics"></a>Live Metrics    

Para habilitar o envio de métricas ao vivo de seu aplicativo para o Azure, use `setSendLiveMetrics(true)` . Atualmente, não há suporte para a filtragem de métricas em tempo real no Portal.

### <a name="extended-metrics"></a>Métricas estendidas

> [!NOTE]
> A capacidade de enviar métricas nativas estendidas foi adicionada na versão 1.4.0

Para habilitar o envio de métricas nativas estendidas do seu aplicativo para o Azure, instale o pacote de métricas nativas separado. O SDK será carregado automaticamente quando for instalado e começará a coletar Node.js métricas nativas.

```bash
npm install applicationinsights-native-metrics
```

Atualmente, o pacote de métricas nativas executa a coleta autocoleção de tempo de CPU de coleta de lixo, tiques de loop de eventos e uso de heap:

- **Coleta de lixo**: a quantidade de tempo de CPU gasto em cada tipo de coleta de lixo e quantas ocorrências de cada tipo.
- **Loop de eventos**: quantas tiques ocorreram e quanto tempo de CPU foi gasto no total.
- **Heap vs não heap**: a quantidade de uso de memória do aplicativo está no heap ou não heap.

### <a name="distributed-tracing-modes"></a>Modos de rastreamento distribuídos

Por padrão, o SDK enviará cabeçalhos compreendidos por outros aplicativos/serviços instrumentados com um SDK Application Insights. Opcionalmente, você pode habilitar o envio/recebimento de cabeçalhos de [contexto de rastreamento W3C](https://github.com/w3c/trace-context) , além dos cabeçalhos de ia existentes, para que você não interrompa a correlação com nenhum dos seus serviços herdados existentes. Habilitar cabeçalhos W3C permitirá que seu aplicativo correlacione-se com outros serviços não instrumentados com Application Insights, mas adote esse padrão W3C.

```Javascript
const appInsights = require("applicationinsights");
appInsights
  .setup("<your ikey>")
  .setDistributedTracingMode(appInsights.DistributedTracingModes.AI_AND_W3C)
  .start()
```

## <a name="telemetryclient-api"></a>API TelemetryClient

Para obter uma descrição completa da API TelemetryClient, confira [API do Application Insights para métricas e eventos personalizados](./api-custom-events-metrics.md).

Você pode acompanhar qualquer solicitação, evento, métrica ou exceção usando o SDK do Node.js para Application Insights. O exemplo de código abaixo demonstra algumas das APIs que você pode usar:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var. start() can be omitted to disable any non-custom data
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
let client = new appInsights.TelemetryClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:duration, resultCode:0, success: true, dependencyTypeName: "ZSQL"});;
```

Um utilitário de exemplo `trackMetric` que usa para medir por quanto tempo o agendamento do loop de evento leva:  

```javascript
function startMeasuringEventLoop() {
  var startTime = process.hrtime();
  var sampleSum = 0;
  var sampleCount = 0;

  // Measure event loop scheduling delay
  setInterval(() => {
    var elapsed = process.hrtime(startTime);
    startTime = process.hrtime();
    sampleSum += elapsed[0] * 1e9 + elapsed[1];
    sampleCount++;
  }, 0);

  // Report custom metric every second
  setInterval(() => {
    var samples = sampleSum;
    var count = sampleCount;
    sampleSum = 0;
    sampleCount = 0;

    if (count > 0) {
      var avgNs = samples / count;
      var avgMs = Math.round(avgNs / 1e6);
      client.trackMetric({name: "Event Loop Delay", value: avgMs});
    }
  }, 1000);
}
```

### <a name="add-a-custom-property-to-all-events"></a>Adição de uma propriedade personalizada para todos os eventos

Use o código abaixo para adicionar uma propriedade personalizada a todos os eventos:

```javascript
appInsights.defaultClient.commonProperties = {
  environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Acompanhamento das solicitações GET HTTP

Use o código a seguir para rastrear manualmente as solicitações HTTP GET:

> [!NOTE]
> Todas as solicitações são rastreadas por padrão. Para desabilitar a coleta automática, chame. setAutoCollectRequests (false) antes de chamar Start ().

```javascript
appInsights.defaultClient.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});
```

Como alternativa, você pode controlar solicitações usando o `trackNodeHttpRequest` método:

```javascript
var server = http.createServer((req, res) => {
  if ( req.method === "GET" ) {
      appInsights.defaultClient.trackNodeHttpRequest({request:req, response:res});
  }
  // other work here....
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

### <a name="flush"></a>Liberar

Por padrão, a telemetria é armazenada em buffer por 15 segundos antes de ser enviada para o servidor de ingestão. Se seu aplicativo tiver um curto período de vida (por exemplo, uma ferramenta CLI), pode ser necessário liberar manualmente sua telemetria armazenada em buffer quando o aplicativo for encerrado, `appInsights.defaultClient.flush()` .

Se o SDK detectar que seu aplicativo está falhando, ele chamará flush para você, `appInsights.defaultClient.flush({ isAppCrashing: true })` . Com a opção flush `isAppCrashing` , supõe-se que seu aplicativo esteja em um estado anormal, não é adequado para o envio de telemetria. Em vez disso, o SDK salvará toda a telemetria armazenada em buffer no [armazenamento persistente](./data-retention-privacy.md#nodejs) e permitirá que o aplicativo seja encerrado. Quando o aplicativo é iniciado novamente, ele tenta enviar qualquer telemetria que foi salva no armazenamento persistente.

### <a name="preprocess-data-with-telemetry-processors"></a>Pré-processar dados com processadores de telemetria

Você pode processar e filtrar os dados coletados antes que eles sejam enviados para retenção usando *processadores de telemetria*. Os processadores de telemetria são chamados um por um na ordem em que foram adicionados antes que o item de telemetria seja enviado para a nuvem.

```javascript
public addTelemetryProcessor(telemetryProcessor: (envelope: Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean)
```

Se um processador de telemetria retornar false, esse item de telemetria não será enviado.

Todos os processadores de telemetria recebem os dados de telemetria e seu envelope para inspecionar e modificar. Eles também recebem um objeto de contexto. O conteúdo desse objeto é definido pelo `contextObjects` parâmetro ao chamar um método Track para telemetria rastreada manualmente. Para telemetria coletada automaticamente, esse objeto é preenchido com as informações de solicitação disponíveis e o conteúdo de solicitação persistente conforme fornecido pelo `appInsights.getCorrelationContext()` (se a correlação de dependência automática estiver habilitada).

O tipo TypeScript para um processador de telemetria é:

```javascript
telemetryProcessor: (envelope: ContractsModule.Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean;
```

Por exemplo, um processador que remove os dados de rastreamento de pilhas de exceções pode ser escrito e adicionado da seguinte maneira:

```javascript
function removeStackTraces ( envelope, context ) {
  if (envelope.data.baseType === "Microsoft.ApplicationInsights.ExceptionData") {
    var data = envelope.data.baseData;
    if (data.exceptions && data.exceptions.length > 0) {
      for (var i = 0; i < data.exceptions.length; i++) {
        var exception = data.exceptions[i];
        exception.parsedStack = null;
        exception.hasFullStack = false;
      }
    }
  }
  return true;
}

appInsights.defaultClient.addTelemetryProcessor(removeStackTraces);
```

## <a name="use-multiple-instrumentation-keys"></a>Usar várias chaves de instrumentação

Você pode criar vários recursos de Application Insights e enviar dados diferentes para cada usando suas respectivas chaves de instrumentação ("iKey").

 Por exemplo:

```javascript
let appInsights = require("applicationinsights");

// configure auto-collection under one ikey
appInsights.setup("_ikey-A_").start();

// track some events manually under another ikey
let otherClient = new appInsights.TelemetryClient("_ikey-B_");
otherClient.trackEvent({name: "my custom event"});
```

## <a name="advanced-configuration-options"></a>Opções de configuração avançada

O objeto Client contém uma `config` propriedade com muitas configurações opcionais para cenários avançados. Eles podem ser definidos da seguinte maneira:

```javascript
client.config.PROPERTYNAME = VALUE;
```

Essas propriedades são específicas do cliente, para que você possa configurar `appInsights.defaultClient` separadamente de clientes criados com o `new appInsights.TelemetryClient()` .

| Propriedade                        | Descrição                                                                                                |
| ------------------------------- |------------------------------------------------------------------------------------------------------------|
| instrumentationKey              | Um identificador para o recurso de Application Insights.                                                      |
| endpointUrl                     | O ponto de extremidade de ingestão para o qual enviar cargas de telemetria.                                                      |
| quickPulseHost                  | O host de Live Metrics Stream para o qual enviar a telemetria de métricas ao vivo.                                            |
| proxyHttpUrl                    | Um servidor proxy para tráfego HTTP do SDK (opcional, padrão extraído da `http_proxy` variável de ambiente).     |
| proxyHttpsUrl                   | Um servidor proxy para o tráfego HTTPS do SDK (opcional, padrão extraído da `https_proxy` variável de ambiente).   |
| httpAgent                       | Um http. Agente a ser usado para o tráfego HTTP do SDK (opcional, padrão indefinido).                                   |
| httpsAgent                      | Um https. Agente a ser usado para o tráfego HTTPS do SDK (opcional, padrão indefinido).                                 |
| maxBatchSize                    | O número máximo de itens de telemetria a serem incluídos em uma carga para o ponto de extremidade de ingestão (padrão `250` ).   |
| maxBatchIntervalMs              | A quantidade máxima de tempo de espera para que uma carga chegue a maxBatchSize (padrão `15000` ).               |
| disableAppInsights              | Um sinalizador que indica se a transmissão de telemetria está desabilitada (padrão `false` ).                                 |
| samplingPercentage              | A porcentagem de itens de telemetria rastreados que devem ser transmitidos (padrão `100` ).                      |
| correlationIdRetryIntervalMs    | O tempo de espera antes de tentar recuperar a ID da correlação entre componentes (padrão `30000` ).     |
| correlationHeaderExcludedDomains| Uma lista de domínios para excluir da injeção de cabeçalho de correlação entre componentes (padrão, consulte [config. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Config.ts)).|

## <a name="next-steps"></a>Próximas etapas

* [Monitorar sua telemetria no portal](./overview-dashboard.md)
* [Escrever consultas de análise sobre a telemetria](../log-query/log-analytics-tutorial.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../faq.md
