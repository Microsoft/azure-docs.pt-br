---
title: correlação de telemetria no Azure Application Insights | Microsoft Docs
description: Correlação no Application Insights Telemetry
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: beaeb0131a2c9b326d663f6fcbb8273a9b52b412
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100960"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlação de telemetria no Application Insights

No mundo dos microsserviços, cada operação lógica requer que o trabalho seja feito em vários componentes do serviço. Você pode monitorar cada um desses componentes separadamente usando [Application insights](../../azure-monitor/app/app-insights-overview.md). O Application Insights é compatível com a correlação de telemetria distribuída, que você pode usar para detectar qual componente é responsável por falhas ou degradação do desempenho.

Este artigo explica o modelo de dados usado pelo Application Insights para correlacionar a telemetria enviada por vários componentes. Ele aborda protocolos e técnicas de propagação de contexto. Ele também aborda a implementação de táticas de correlação em diferentes linguagens e plataformas.

## <a name="data-model-for-telemetry-correlation"></a>Modelo de dados para correlação de telemetria

O Application Insights define um [modelo de dados](../../azure-monitor/app/data-model.md) para correlação de telemetria distribuída. Para associar a telemetria a uma operação lógica, cada item de telemetria tem um campo de contexto chamado `operation_Id` . Esse identificador é compartilhado por todos os itens de telemetria no rastreamento distribuído. Portanto, mesmo se você perder a telemetria de uma única camada, ainda poderá associar a telemetria relatada por outros componentes.

Uma operação lógica distribuída normalmente consiste em um conjunto de operações menores que são solicitações processadas por um dos componentes. Essas operações são definidas por [telemetria de solicitação](../../azure-monitor/app/data-model-request-telemetry.md). Cada item de telemetria de solicitação tem seu próprio `id` que o identifica de forma exclusiva e global. E todos os itens de telemetria (como rastreamentos e exceções) associados à solicitação devem definir o `operation_parentId` como o valor da solicitação `id` .

Cada operação de saída, como uma chamada HTTP para outro componente, é representada pela [telemetria de dependência](../../azure-monitor/app/data-model-dependency-telemetry.md). A telemetria de dependência também define seu próprio `id` que é globalmente exclusivo. A telemetria de solicitação, iniciada por essa chamada de dependência, usa este `id` como `operation_parentId`.

Você pode compilar um modo de exibição de operação lógica distribuída usando `operation_Id`, `operation_parentId` e `request.id` com `dependency.id`. Esses campos também definem a ordem de causalidade das chamadas de telemetria.

Em um ambiente de microsserviços, os rastreamentos de componentes podem ir para itens de armazenamento diferentes. Todo componente pode ter sua própria chave de instrumentação no Application Insights. Para obter a telemetria para a operação lógica, o Application Insights consulta dados de cada item de armazenamento. Quando o número de itens de armazenamento for grande, você precisará de uma dica sobre onde procurar em seguida. O modelo de dados do Application Insights define dois campos para resolver esse problema: `request.source` e `dependency.target`. O primeiro campo identifica o componente que iniciou a solicitação de dependência. O segundo campo identifica qual componente retornou a resposta da chamada de dependência.

## <a name="example"></a>Exemplo

Vejamos um exemplo. Um aplicativo chamado preços de ações mostra o preço atual do mercado de um estoque usando uma API externa chamada stock. O aplicativo de preços de ações tem uma página chamada Stock Page que o navegador da Web do cliente abre usando o `GET /Home/Stock` . O aplicativo consulta a API de estoque usando a chamada HTTP `GET /api/stock/value` .

Você pode analisar a telemetria resultante executando uma consulta:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nos resultados, observe que todos os itens de telemetria compartilham a raiz `operation_Id`. Quando uma chamada AJAX é feita da página, uma nova ID exclusiva ( `qJSXU` ) é atribuída à telemetria de dependência e a ID de pageView é usada como `operation_ParentId` . A solicitação do servidor, em seguida, usa a ID do Ajax como `operation_ParentId`.

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Página de ações                | STYz         |                    | STYz         |
| dependência | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| solicitação    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependência | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Quando a chamada `GET /api/stock/value` é feita a um serviço externo, você precisa saber a identidade desse servidor para poder definir o `dependency.target` campo adequadamente. Quando o serviço externo não oferece suporte a monitoramento, `target` é definido como o nome do host do serviço (por exemplo, `stock-prices-api.com`). Mas se o serviço se identificar retornando um cabeçalho HTTP predefinido, `target` contém a identidade do serviço que permite ao Application insights criar um rastreamento distribuído consultando a telemetria desse serviço.

## <a name="correlation-headers-using-w3c-tracecontext"></a>Cabeçalhos de correlação usando o W3C TraceContext

Application Insights está fazendo a transição para o [contexto de rastreamento W3C](https://w3c.github.io/trace-context/), que define:

- `traceparent`: Transporta a ID da operação globalmente exclusiva e o identificador exclusivo da chamada.
- `tracestate`: Transporta o contexto de rastreamento específico do sistema.

A versão mais recente do SDK do Application Insights dá suporte ao protocolo Trace-Context, mas talvez seja necessário aceitá-lo. (A compatibilidade com versões anteriores com o protocolo de correlação anterior com suporte pelo SDK do Application Insights será mantida.)

O [protocolo http de correlação, também chamado Request-ID](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), está sendo preterido. Esse protocolo define dois cabeçalhos:

- `Request-Id`: Transporta a ID global exclusiva da chamada.
- `Correlation-Context`: Transporta a coleção de pares nome-valor das propriedades de rastreamento distribuído.

Application Insights também define a [extensão](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) para o protocolo http de correlação. Ele usa pares nome-valor de `Request-Context` para propagar a coleção de propriedades usadas pelo computador chamado ou chamador imediato. O SDK do Application Insights usa esse cabeçalho para definir `dependency.target` os `request.source` campos e.

Os modelos de dados do [W3C Trace-Context](https://w3c.github.io/trace-context/) e Application insights data são mapeados da seguinte maneira:

| Application Insights                   | TraceContext do W3C                                      |
|------------------------------------    |-------------------------------------------------|
| `Id` de `Request` e `Dependency`     | [ID-pai](https://w3c.github.io/trace-context/#parent-id)                                     |
| `Operation_Id`                         | [ID do rastreamento](https://w3c.github.io/trace-context/#trace-id)                                           |
| `Operation_ParentId`                   | [ID pai](https://w3c.github.io/trace-context/#parent-id) da extensão pai deste span. Se esse for um Span raiz, esse campo deverá estar vazio.     |

Para obter mais informações, consulte [Application insights modelo de dados de telemetria](../../azure-monitor/app/data-model.md).

### <a name="enable-w3c-distributed-tracing-support-for-net-apps"></a>Habilitar o suporte ao rastreamento distribuído W3C para aplicativos .NET

O rastreamento distribuído baseado em W3C de TraceContext é habilitado por padrão em todos os SDKs recentes de .NET Framework/. NET Core, juntamente com a compatibilidade com versões anteriores com o protocolo Request-Id herdado.

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Habilitar suporte a rastreamento distribuído do W3C para aplicativos Java

#### <a name="java-30-agent"></a>Agente do Java 3,0

  O agente do Java 3,0 dá suporte ao W3C pronto para uso e nenhuma configuração adicional é necessária. 

#### <a name="java-sdk"></a>Java SDK
- **Configuração de entrada**

  - Para aplicativos Java EE, adicione o seguinte à `<TelemetryModules>` marca em ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```

  - Para aplicativos Spring boot, adicione estas propriedades:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Configuração de saída**

  Adicione o seguinte ao AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > o modo de compatibilidade com versões anteriores está habilitado por padrão e o parâmetro `enableW3CBackCompat` é opcional. Use-o somente quando quiser desativar a compatibilidade com versões anteriores.
  >
  > O ideal seria desativá-lo quando todos os seus serviços forem atualizados para versões mais recentes dos SDKs compatíveis com o protocolo W3C. É altamente recomendável que você mude para os SDKs mais novos assim que possível.

> [!IMPORTANT]
> Verifique se as configurações de entrada e saída são exatamente iguais.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Habilitar o suporte ao rastreamento distribuído W3C para aplicativos Web

Este recurso está em `Microsoft.ApplicationInsights.JavaScript` . Isso está desabilitado por padrão. Para habilitá-lo, use `distributedTracingMode` config. AI_AND_W3C é fornecida para compatibilidade com versões anteriores com qualquer serviço herdado instrumentado por Application Insights.

- **[configuração baseada em NPM](./javascript.md#npm-based-setup)**

Adicione a seguinte configuração:
  ```JavaScript
    distributedTracingMode: DistributedTracingModes.W3C
  ```

- **[Configuração baseada em trecho](./javascript.md#snippet-based-setup)**

Adicione a seguinte configuração:
  ```
      distributedTracingMode: 2 // DistributedTracingModes.W3C
  ```
> [!IMPORTANT] 
> Para ver todas as configurações necessárias para habilitar a correlação, consulte a [documentação de correlação do JavaScript](./javascript.md#enable-correlation).

## <a name="telemetry-correlation-in-opencensus-python"></a>Correlação de telemetria no OpenCensus Python

O Python OpenCensus dá suporte ao [contexto de rastreamento W3C](https://w3c.github.io/trace-context/) sem a necessidade de configuração adicional.

Como referência, o modelo de dados OpenCensus pode ser encontrado [aqui](https://github.com/census-instrumentation/opencensus-specs/tree/master/trace).

### <a name="incoming-request-correlation"></a>Correlação de solicitação de entrada

OpenCensus Python correlaciona cabeçalhos W3C Trace-Context de solicitações de entrada para as extensões que são geradas a partir das próprias solicitações. O OpenCensus fará isso automaticamente com integrações para essas estruturas de aplicativos Web populares: Flask, Django e pirâmide. Você só precisa popular os cabeçalhos W3C Trace-Context com o [formato correto](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) e enviá-los com a solicitação. Veja um exemplo de aplicativo Flask que demonstra isso:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Esse código executa um aplicativo Flask de exemplo em seu computador local, ouvindo a porta `8080` . Para correlacionar o contexto de rastreamento, você envia uma solicitação ao ponto de extremidade. Neste exemplo, você pode usar um `curl` comando:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Ao examinar o [formato de cabeçalho do contexto de rastreamento](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), você pode derivar as seguintes informações:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Se você examinar a entrada de solicitação que foi enviada para Azure Monitor, poderá ver os campos preenchidos com as informações do cabeçalho de rastreamento. Você pode encontrar esses dados em logs (análise) no recurso de Application Insights de Azure Monitor.

![Solicitar telemetria em logs (análise)](./media/opencensus-python/0011-correlation.png)

O `id` campo está no formato `<trace-id>.<span-id>` , em que o `trace-id` é tirado do cabeçalho de rastreamento que foi passado na solicitação e o `span-id` é uma matriz de 8 bytes gerada para esse intervalo.

O `operation_ParentId` campo está no formato `<trace-id>.<parent-id>` , onde `trace-id` e e `parent-id` são tirados do cabeçalho de rastreamento que foi passado na solicitação.

### <a name="log-correlation"></a>Correlação de logs

O OpenCensus Python permite que você correlacione os logs adicionando uma ID de rastreamento, uma ID de extensão e um sinalizador de amostragem aos registros de log. Você adiciona esses atributos instalando a [integração de log](https://pypi.org/project/opencensus-ext-logging/)do OpenCensus. Os seguintes atributos serão adicionados aos objetos Python `LogRecord` : `traceId` , `spanId` e `traceSampled` . Observe que isso só entra em vigor para agentes que são criados após a integração.

Aqui está um aplicativo de exemplo que demonstra isso:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Quando esse código é executado, as seguintes impressões são impressas no console do:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Observe que há um `spanId` presente para a mensagem de log que está dentro do intervalo. Isso é o mesmo `spanId` que pertence ao trecho nomeado `hello` .

Você pode exportar os dados de log usando o `AzureLogHandler` . Para obter mais informações, consulte [este artigo](./opencensus-python.md#logs).

Também podemos passar informações de rastreamento de um componente para outro para uma correlação adequada. Por exemplo, considere um cenário em que há dois componentes `module1` e `module2` . O Module1 chama funções no Module2 e para obter logs de `module1` e para `module2` um único rastreamento, podemos usar a seguinte abordagem:

```python
# module1.py
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer
from module2 import function_1

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
   logger.warning('In the span')
   function_1(tracer)
logger.warning('After the span')


# module2.py

import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

def function_1(parent_tracer=None):
    if parent_tracer is not None:
        tracer = Tracer(
                    span_context=parent_tracer.span_context,
                    sampler=AlwaysOnSampler(),
                )
    else:
        tracer = Tracer(sampler=AlwaysOnSampler())

    with tracer.span("function_1"):
        logger.info("In function_1")
```

## <a name="telemetry-correlation-in-net"></a>Correlação de telemetria em .NET

O tempo de execução do .NET oferece suporte distribuído com a ajuda da [atividade](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) e do [diagnóstico](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)

O SDK do .NET Application Insights usa `DiagnosticSource` e `Activity` para coletar e correlacionar a telemetria.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Correlação de telemetria em Java

O [agente Java](./java-in-process-agent.md) , bem como [Java SDK](../../azure-monitor/app/java-get-started.md) versão 2.0.0 ou posterior, dá suporte à correlação automática de telemetria. Ele preenche automaticamente `operation_id` todas as telemetrias (como rastreamentos, exceções e eventos personalizados) emitidos dentro do escopo de uma solicitação. Ele também propaga os cabeçalhos de correlação (descritos anteriormente) para chamadas de serviço a serviço via HTTP, se o [agente do SDK do Java](../../azure-monitor/app/java-agent.md) estiver configurado.

> [!NOTE]
> Application Insights o agente Java coleta automaticamente solicitações e dependências para JMS, Kafka, Subnety/webfluxo e muito mais. Para o SDK do Java, somente as chamadas feitas por meio do Apache HttpClient têm suporte para o recurso de correlação. A propagação automática de contexto entre tecnologias de mensagens (como Kafka, RabbitMQ e barramento de serviço do Azure) não tem suporte no SDK. 

> [!NOTE]
> Para coletar telemetria personalizada, você precisa instrumentar o aplicativo com o SDK do Java 2,6. 

### <a name="role-names"></a>Nomes de função

Talvez você queira personalizar a maneira como os nomes de componentes são exibidos no [mapa do aplicativo](../../azure-monitor/app/app-map.md). Para fazer isso, você pode definir manualmente o `cloud_RoleName` executando uma das seguintes ações:

- Para Application Insights o agente Java 3,0, defina o nome da função de nuvem da seguinte maneira:

    ```json
    {
      "role": {
        "name": "my cloud role name"
      }
    }
    ```
    Você também pode definir o nome da função de nuvem usando a variável de ambiente `APPLICATIONINSIGHTS_ROLE_NAME` .

- Com Application Insights SDK do Java 2.5.0 e posterior, você pode especificar o `cloud_RoleName` adicionando `<RoleName>` ao arquivo de ApplicationInsights.xml:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Se você usar o Spring boot com o iniciador do Spring boot Application Insights, precisará apenas definir seu nome personalizado para o aplicativo no arquivo Application. Properties:

  `spring.application.name=<name-of-app>`

  O iniciador do Spring boot atribui automaticamente `cloudRoleName` ao valor inserido para a `spring.application.name` propriedade.

## <a name="next-steps"></a>Próximas etapas

- Gravar [telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md).
- Para cenários de correlação avançada em ASP.NET Core e ASP.NET, consulte [rastrear operações personalizadas](custom-operations-tracking.md).
- Saiba mais sobre como [configurar cloud_RoleName](./app-map.md#set-or-override-cloud-role-name) para outros SDKs.
- Integrar todos os componentes do seu microsserviço ao Application Insights. Consultar as [plataformas compatíveis](./platforms.md).
- Consultar o [modelo de dados](./data-model.md) para os tipos do Application Insights.
- Saiba como [estender e filtrar a telemetria](./api-filtering-sampling.md).
- Revisar a [Referência de configuração do Application Insights](configuration-with-applicationinsights-config.md).