---
title: Monitorar aplicativos Python com o Azure Monitor | Microsoft Docs
description: Fornece instruções para conectar o Python OpenCensus com o Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
ms.openlocfilehash: 1e6376cd8389a4f1f0defebce0a2c7b6d0f9deed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323258"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Configurar Azure Monitor para seu aplicativo Python

O Azure Monitor permite o rastreamento distribuído, a coleta de métricas e o log de aplicativos Python por meio da integração com o [OpenCensus](https://opencensus.io). Este artigo orienta você pelo processo de configuração do OpenCensus para Python e do envio dos dados de monitoramento para Azure Monitor.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Instalação do Python. Este artigo usa o [Python 3.7.0](https://www.python.org/downloads/release/python-370/), embora outras versões provavelmente funcionem com pequenas alterações. O SDK só dá suporte a Python v 2.7 e v 3.4-v 3.7.
- Crie um [recurso](./create-new-resource.md) Application Insights. Você receberá sua própria chave de instrumentação (iKey) para seu recurso.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumento com SDK de Python OpenCensus para o Azure Monitor

Instale exportadores OpenCensus Azure Monitor:

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> O comando `python -m pip install opencensus-ext-azure` pressupõe que você tenha uma variável de ambiente `PATH` definida para sua instalação do Python. Se você não tiver configurado essa variável, precisará fornecer o caminho completo do diretório para o local em que o executável do Python está localizado. Execute um comando como este: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

O SDK usa três Azure Monitor exportadores para enviar tipos diferentes de telemetria para Azure Monitor. Eles são rastreamento, métricas e logs. Para obter mais informações sobre esses tipos de telemetria, consulte [Visão geral da plataforma de dados](../platform/data-platform.md). Use as instruções a seguir para enviar esses tipos de telemetria por meio dos três exportadores.

## <a name="telemetry-type-mappings"></a>Mapeamentos do tipo de telemetria

Aqui estão os exportadores que o OpenCensus fornece mapeado para os tipos de telemetria que você vê no Azure Monitor.

| Pilar da observação | Tipo de telemetria no Azure Monitor    | Explicação                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| Logs                    | Rastreamentos, exceções, customEvents   | Telemetria de log, telemetria de exceção, telemetria de eventos |
| Métricas                 | customMetrics, performanceCounters | Contadores de desempenho de métricas personalizadas                |
| Rastreamento                 | Dependências de solicitações             | Solicitações de entrada, solicitações de saída                |

### <a name="logs"></a>Logs

1. Primeiro vamos gerar alguns dados de log locais.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. O código solicita continuamente um valor a ser inserido. Uma entrada de log é emitida para cada valor inserido.

    ```output
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. Embora a inserção de valores seja útil para fins de demonstração, na realidade, desejamos emitir os dados de log para o Azure Monitor. Passe a cadeia de conexão diretamente no exportador. Ou você pode especificá-lo em uma variável de ambiente, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Modifique seu código da etapa anterior com base no seguinte exemplo de código:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)

    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. O exportador envia dados de log para Azure Monitor. Você pode encontrar os dados em `traces`. 

    > [!NOTE]
    > Nesse contexto, `traces` não é o mesmo que `tracing` . Aqui, `traces` refere-se ao tipo de telemetria que você verá em Azure monitor quando utilizar `AzureLogHandler` . Mas `tracing` refere-se a um conceito em OpenCensus e está relacionado ao [rastreamento distribuído](./distributed-tracing.md).

    > [!NOTE]
    > O agente raiz é configurado com o nível de aviso. Isso significa que todos os logs que você enviar com menos de uma severidade serão ignorados e, por sua vez, não serão enviados para Azure Monitor. Para obter mais informações, consulte a [documentação](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel).

1. Você também pode adicionar propriedades personalizadas às mensagens de log no argumento de palavra-chave *extra* usando o campo custom_dimensions. Essas propriedades aparecem como pares de chave-valor no `customDimensions` Azure monitor.
    > [!NOTE]
    > Para que esse recurso funcione, você precisa passar um dicionário para o campo custom_dimensions. Se você passar argumentos de qualquer outro tipo, o agente irá ignorá-los.

    ```python
    import logging

    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="configure-logging-for-django-applications"></a>Configurar o log para aplicativos Django

Você pode configurar o registro em log explicitamente no código do aplicativo, como acima, para seus aplicativos Django, ou pode especificá-lo na configuração de log do Django. Esse código pode entrar em qualquer arquivo que você usar para a configuração de configurações do Django. Para saber como definir as configurações de Django, consulte [configurações de Django](https://docs.djangoproject.com/en/3.0/topics/settings/). Para obter mais informações sobre como configurar o log, consulte [registro em log do Django](https://docs.djangoproject.com/en/3.0/topics/logging/).

```json
LOGGING = {
    "handlers": {
        "azure": {
            "level": "DEBUG",
        "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
            "instrumentation_key": "<your-ikey-here>",
         },
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "stream": sys.stdout,
         },
      },
    "loggers": {
        "logger_name": {"handlers": ["azure", "console"]},
    },
}
```

Certifique-se de usar o agente de log com o mesmo nome que o especificado em sua configuração.

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>Enviar exceções

O OpenCensus Python não rastreia e envia automaticamente a `exception` telemetria. Eles são enviados `AzureLogHandler` usando exceções por meio da biblioteca de registro em log do Python. Você pode adicionar propriedades personalizadas, assim como com o log normal.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
Como você deve registrar exceções explicitamente, cabe ao usuário como eles desejam registrar exceções sem tratamento. OpenCensus não coloca restrições sobre como um usuário deseja fazer isso, desde que registre explicitamente uma telemetria de exceção.

#### <a name="send-events"></a>Enviar eventos

Você pode enviar `customEvent` a telemetria exatamente da mesma maneira que envia a `trace` telemetria, exceto usando o `AzureEventHandler` em vez disso.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>amostragem

Para obter informações sobre amostragem no OpenCensus, dê uma olhada em [amostragem no OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Correlação de logs

Para obter detalhes sobre como enriquecer seus logs com dados de contexto de rastreamento, consulte a [integração de logs](./correlation.md#log-correlation) do OpenCensus Python.

#### <a name="modify-telemetry"></a>Modificar telemetria

Para obter detalhes sobre como modificar a telemetria acompanhada antes de ser enviada para Azure Monitor, consulte [processadores de telemetria](./api-filtering-sampling.md#opencensus-python-telemetry-processors)do OpenCensus Python.


### <a name="metrics"></a>Métricas

1. Primeiro vamos gerar alguns dados métricos locais. Criaremos uma métrica simples para acompanhar o número de vezes que o usuário seleciona a tecla **Enter** .

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
1. Executar o código repetidamente solicita que você selecione **Enter**. Uma métrica é criada para acompanhar o número de vezes que a **entrada** é selecionada. Com cada entrada, o valor é incrementado e as informações de métrica são exibidas no console. As informações incluem o valor atual e o carimbo de data/hora atual quando a métrica foi atualizada.

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. Embora a inserção de valores seja útil para fins de demonstração, na realidade, desejamos emitir os dados de métrica para o Azure Monitor. Passe a cadeia de conexão diretamente no exportador. Ou você pode especificá-lo em uma variável de ambiente, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Modifique seu código da etapa anterior com base no seguinte exemplo de código:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

1. O exportador envia dados de métrica para Azure Monitor em um intervalo fixo. O padrão é a cada 15 segundos. Estamos acompanhando uma única métrica, portanto, esses dados de métrica, com qualquer valor e carimbo de data/hora que ele contém, são enviados a cada intervalo. Você pode encontrar os dados em `customMetrics`.

#### <a name="performance-counters"></a>Contadores de desempenho

Por padrão, o exportador de métricas envia um conjunto de contadores de desempenho para Azure Monitor. Você pode desabilitar isso definindo o sinalizador `enable_standard_metrics` como `False` no construtor do exportador de métricas.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

Esses contadores de desempenho são enviados no momento:

- Memória disponível (bytes)
- Tempo do processador da CPU (percentual)
- Taxa de solicitação de entrada (por segundo)
- Tempo médio de execução da solicitação de entrada (milissegundos)
- Uso da CPU do processo (percentual)
- Bytes privados do processo (bytes)

Você deve ser capaz de ver essas métricas em `performanceCounters`. Para obter mais informações, consulte [Contadores de desempenho](./performance-counters.md).

#### <a name="modify-telemetry"></a>Modificar telemetria

Para obter informações sobre como modificar a telemetria acompanhada antes de ser enviada para Azure Monitor, consulte [processadores de telemetria](./api-filtering-sampling.md#opencensus-python-telemetry-processors)do OpenCensus Python.

### <a name="tracing"></a>Rastreamento

> [!NOTE]
> No OpenCensus, `tracing` refere-se ao [rastreamento distribuído](./distributed-tracing.md). O `AzureExporter` envia a telemetria `requests` e `dependency` para o Azure Monitor.

1. Primeiro vamos gerar alguns dados de rastreamento localmente. No Python IDLE, ou seu editor preferencial, digite o seguinte código:

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Executar o código repetidamente solicita que você insira um valor. Com cada entrada, o valor é impresso no Shell. O módulo OpenCensus Python gera uma parte correspondente do `SpanData` . O projeto OpenCensus define um [rastreamento como uma árvore de spans](https://opencensus.io/core-concepts/tracing/).
    
    ```output
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

1. Embora a inserção de valores seja útil para fins de demonstração, por fim, queremos emitir `SpanData` para Azure monitor. Passe a cadeia de conexão diretamente no exportador. Ou você pode especificá-lo em uma variável de ambiente, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Modifique seu código da etapa anterior com base no seguinte exemplo de código:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Agora, quando você executa o script Python, ainda deve ser solicitado a inserir valores, mas apenas o valor que está sendo impresso no shell. O criado `SpanData` é enviado para Azure monitor. Você pode encontrar os dados de span emitidos em `dependencies`. Para obter mais informações sobre solicitações de saída, consulte [dependências](./opencensus-python-dependency.md)do Python OpenCensus.
Para obter mais informações sobre solicitações de entrada, consulte OpenCensus Python [requests](./opencensus-python-request.md).

#### <a name="sampling"></a>amostragem

Para obter informações sobre amostragem no OpenCensus, dê uma olhada em [amostragem no OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Correlação de rastreamento

Para obter mais informações sobre a correlação de telemetria em seus dados de rastreamento, dê uma olhada na [correlação de telemetria](./correlation.md#telemetry-correlation-in-opencensus-python)do OpenCensus Python.

#### <a name="modify-telemetry"></a>Modificar telemetria

Para obter mais informações sobre como modificar a telemetria acompanhada antes de ser enviada para Azure Monitor, consulte [processadores de telemetria](./api-filtering-sampling.md#opencensus-python-telemetry-processors)do OpenCensus Python.

## <a name="configure-azure-monitor-exporters"></a>Configurar exportadores de Azure Monitor

Como mostrado, há três exportadores de Azure Monitor diferentes que dão suporte a OpenCensus. Cada uma envia tipos diferentes de telemetria para Azure Monitor. Para ver quais tipos de telemetria cada exportador envia, consulte a lista a seguir.

Cada exportador aceita os mesmos argumentos para configuração, passados pelos construtores. Você pode ver detalhes sobre cada um aqui:

- `connection_string`: A cadeia de conexão usada para se conectar ao recurso de Azure Monitor. Tem prioridade `instrumentation_key` .
- `enable_standard_metrics`: Usado para `AzureMetricsExporter` . Sinaliza o exportador para enviar as métricas do [contador de desempenho](../platform/app-insights-metrics.md#performance-counters) automaticamente para Azure monitor. Assume o padrão de `True`.
- `export_interval`: Usado para especificar a frequência em segundos de exportação.
- `instrumentation_key`: A chave de instrumentação usada para se conectar ao recurso de Azure Monitor.
- `logging_sampling_rate`: Usado para `AzureLogHandler` . Fornece uma taxa de amostragem [0, 1,0] para exportar logs. O padrão é 1,0.
- `max_batch_size`: Especifica o tamanho máximo da telemetria que é exportada de uma vez.
- `proxies`: Especifica uma sequência de proxies a serem usados para enviar dados para Azure Monitor. Para obter mais informações, consulte [proxies](https://requests.readthedocs.io/en/master/user/advanced/#proxies).
- `storage_path`: Um caminho para onde a pasta de armazenamento local existe (telemetria não enviada). A partir de `opencensus-ext-azure` v 1.0.3, o caminho padrão é o diretório Temp do sistema operacional + `opencensus-python`  +  `your-ikey` . Antes do v 1.0.3, o caminho padrão é $USER + `.opencensus`  +  `.azure`  +  `python-file-name` .

## <a name="view-your-data-with-queries"></a>Visualização dos seus dados com consultas

Você pode exibir os dados de telemetria que foram enviados do seu aplicativo por meio da guia **Logs (Análise)** .

![Captura de tela do painel Visão geral com "Logs (Análise)" selecionado em uma caixa vermelha](./media/opencensus-python/0010-logs-query.png)

Na lista em **Ativo**:

- Para a telemetria enviada com o exportador de rastreamento do Azure Monitor, as solicitações de entrada aparecem em `requests`. As solicitações de saída ou em andamento aparecem em `dependencies`.
- Para a telemetria enviada com o exportador de métricas do Azure Monitor, as métricas enviadas aparecem em `customMetrics`.
- Para a telemetria enviada com o exportador de logs do Azure Monitor, os logs aparecem em `traces`. As exceções aparecem em `exceptions`.

Para obter informações mais detalhadas sobre como usar consultas e logs, consulte [Logs no Azure Monitor](../platform/data-platform-logs.md).

## <a name="learn-more-about-opencensus-for-python"></a>Saiba mais sobre o OpenCensus para Python

* [OpenCensus Python no GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Personalização](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Exportadores Azure Monitor no GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus Integrations](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Aplicativos de exemplo do Azure Monitor](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Próximas etapas

* [Acompanhamento de solicitações de entrada](./opencensus-python-dependency.md)
* [Acompanhamento de solicitações em andamento](./opencensus-python-request.md)
* [Mapa do aplicativo](./app-map.md)
* [Monitoramento de desempenho de ponta a ponta](../learn/tutorial-performance.md)

### <a name="alerts"></a>Alertas

* [Testes de disponibilidade](./monitor-web-app-availability.md): Crie testes para garantir que o site esteja visível na Web.
* [Diagnóstico inteligente](./proactive-diagnostics.md): Esses testes são executados automaticamente, portanto, nenhuma configuração adicional será necessária. Eles informam se o aplicativo tem uma taxa incomum de solicitações com falha.
* [Alertas de métrica](../platform/alerts-log.md): Definir alertas para avisar se uma métrica ultrapassar um limite. Você pode defini-los em métricas personalizadas que você codifica em seu aplicativo.

