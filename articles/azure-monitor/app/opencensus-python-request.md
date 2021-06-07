---
title: Acompanhamento de solicitação de entrada no Aplicativo Azure insights com Python OpenCensus | Microsoft Docs
description: Monitore chamadas de solicitação para seus aplicativos Python via OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: 3b029a9cb14a81c80072847dc17d6b71f480743f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585669"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Acompanhar solicitações de entrada com Python OpenCensus

Os dados de solicitação de entrada são coletados usando Python OpenCensus e suas várias integrações. Acompanhe os dados de solicitação de entrada enviados aos seus aplicativos Web criados com base nas estruturas populares da Web `django` `flask` e no `pyramid` . Os dados são enviados para Application Insights em Azure Monitor como `requests` telemetria.

Primeiro, instrumentar seu aplicativo Python com o [SDK do Python OpenCensus](./opencensus-python.md)mais recente.

## <a name="tracking-django-applications"></a>Acompanhamento de aplicativos Django

1. Baixe e instale `opencensus-ext-django` do [PyPI](https://pypi.org/project/opencensus-ext-django/) e Instrumente seu aplicativo com o `django` middleware. As solicitações de entrada enviadas ao seu `django` aplicativo serão rastreadas.

2. Inclua `opencensus.ext.django.middleware.OpencensusMiddleware` em seu `settings.py` arquivo em `MIDDLEWARE` .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Verifique se o AzureExporter está configurado corretamente no seu `settings.py` em `OPENCENSUS` . Para solicitações de URLs que você não deseja controlar, adicione-as ao `EXCLUDELIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Acompanhamento de aplicativos Flask

1. Baixe e instale `opencensus-ext-flask` do [PyPI](https://pypi.org/project/opencensus-ext-flask/) e Instrumente seu aplicativo com o `flask` middleware. As solicitações de entrada enviadas ao seu `flask` aplicativo serão rastreadas.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. Você também pode configurar seu `flask` aplicativo por meio do `app.config` . Para solicitações de URLs que você não deseja controlar, adicione-as ao `EXCLUDELIST_PATHS` .

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Rastrear aplicativos de pirâmide

1. Baixe e instale `opencensus-ext-django` do [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) e Instrumente seu aplicativo com a `pyramid` interpolação. As solicitações de entrada enviadas ao seu `pyramid` aplicativo serão rastreadas.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Você pode configurar sua `pyramid` interpolação diretamente no código. Para solicitações de URLs que você não deseja controlar, adicione-as ao `EXCLUDELIST_PATHS` .

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="tracking-fastapi-applications"></a>Acompanhamento de aplicativos FastAPI

OpenCensus não tem uma extensão para FastAPI. Para escrever seu próprio middleware FastAPI, conclua as seguintes etapas:

1. As seguintes dependências são necessárias: 
    - [fastapi](https://pypi.org/project/fastapi/)
    - [uvicorn](https://pypi.org/project/uvicorn/)

2. Adicione o [middleware FastAPI](https://fastapi.tiangolo.com/tutorial/middleware/). Certifique-se de definir o tipo de span Server: `span.span_kind = SpanKind.SERVER` .

3. Execute seu aplicativo. As chamadas feitas ao aplicativo FastAPI devem ser rastreadas automaticamente e a telemetria deve ser registrada diretamente em Azure Monitor.

    ```python 
    # Opencensus imports
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    from opencensus.trace.span import SpanKind
    from opencensus.trace.attributes_helper import COMMON_ATTRIBUTES
    # FastAPI imports
    from fastapi import FastAPI, Request
    # uvicorn
    import uvicorn

    app = FastAPI()

    HTTP_URL = COMMON_ATTRIBUTES['HTTP_URL']
    HTTP_STATUS_CODE = COMMON_ATTRIBUTES['HTTP_STATUS_CODE']

    # fastapi middleware for opencensus
    @app.middleware("http")
    async def middlewareOpencensus(request: Request, call_next):
        tracer = Tracer(exporter=AzureExporter(connection_string=f'InstrumentationKey={APPINSIGHTS_INSTRUMENTATIONKEY}'),sampler=ProbabilitySampler(1.0))
        with tracer.span("main") as span:
            span.span_kind = SpanKind.SERVER

            response = await call_next(request)

            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_STATUS_CODE,
                attribute_value=response.status_code)
            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_URL,
                attribute_value=str(request.url))

        return response

    @app.get("/")
    async def root():
        return "Hello World!"

    if __name__ == '__main__':
        uvicorn.run("example:app", host="127.0.0.1", port=5000, log_level="info")
    ```

## <a name="next-steps"></a>Próximas etapas

* [Mapa do aplicativo](./app-map.md)
* [Disponibilidade](./monitor-web-app-availability.md)
* [Pesquisar](./diagnostic-search.md)
* [Consulta de log (Analytics)](../logs/log-query-overview.md)
* [Diagnóstico da transação](./transaction-diagnostics.md)

