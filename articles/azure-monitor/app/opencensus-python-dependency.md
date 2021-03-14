---
title: Acompanhamento de dependência no Aplicativo Azure insights com Python OpenCensus | Microsoft Docs
description: Monitore chamadas de dependência para seus aplicativos Python via OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: a8f673295236d60ec6681bbfaee1201a4659674b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585710"
---
# <a name="track-dependencies-with-opencensus-python"></a>Acompanhar dependências com Python OpenCensus

Uma dependência é um componente externo que é chamado por seu aplicativo. Os dados de dependência são coletados usando o OpenCensus Python e suas várias integrações. Os dados são enviados para Application Insights em Azure Monitor como `dependencies` telemetria.

Primeiro, instrumentar seu aplicativo Python com o [SDK do Python OpenCensus](./opencensus-python.md)mais recente.

## <a name="in-process-dependencies"></a>Dependências em processo

O SDK do OpenCensus Python para Azure Monitor permite que você envie telemetria de dependências "em processo" (informações e lógica que ocorre dentro de seu aplicativo). As dependências em processo terão o `type` campo como `INPROC` na análise.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Dependências com a integração de "solicitações"

Acompanhe suas solicitações de saída com a integração do OpenCensus `requests` .

Baixe e instale `opencensus-ext-requests` do [PyPI](https://pypi.org/project/opencensus-ext-requests/) e adicione-o às integrações de rastreamento. As solicitações enviadas usando a biblioteca de [solicitações](https://pypi.org/project/requests/) do Python serão acompanhadas.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>Dependências com a integração "httplib"

Acompanhe suas solicitações de saída com a `httplib` integração do OpenCensus.

Baixe e instale `opencensus-ext-httplib` do [PyPI](https://pypi.org/project/opencensus-ext-httplib/) e adicione-o às integrações de rastreamento. As solicitações enviadas usando [http. Client](https://docs.python.org/3.7/library/http.client.html) para Python3 ou [httplib](https://docs.python.org/2/library/httplib.html) para Python2 serão rastreadas.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>Dependências com a integração "Django"

Acompanhe suas solicitações de Django de saída com a `django` integração do OpenCensus.

> [!NOTE]
> As únicas solicitações de Django de saída que são rastreadas são chamadas feitas a um banco de dados. Para solicitações feitas ao aplicativo Django, consulte [solicitações de entrada](./opencensus-python-request.md#tracking-django-applications).

Baixe e instale `opencensus-ext-django` do [PyPI](https://pypi.org/project/opencensus-ext-django/) e adicione a seguinte linha à `MIDDLEWARE` seção no `settings.py` arquivo Django.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

É possível fornecer configuração adicional, ler [personalizações](https://github.com/census-instrumentation/opencensus-python#customization) para uma referência completa.

```python
OPENCENSUS = {
    'TRACE': {
        'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
        'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
            connection_string="InstrumentationKey=<your-ikey-here>"
        )''',
    }
}
```

## <a name="dependencies-with-mysql-integration"></a>Dependências com a integração "MySQL"

Acompanhe as dependências do MYSQL com a integração do OpenCensus `mysql` . Essa integração dá suporte à biblioteca [MySQL-Connector](https://pypi.org/project/mysql-connector-python/) .

Baixe e instale `opencensus-ext-mysql` do [PyPI](https://pypi.org/project/opencensus-ext-mysql/) e adicione as linhas a seguir ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Dependências com a integração "pymysql"

Acompanhe suas dependências do PyMySQL com a integração do OpenCensus `pymysql` .

Baixe e instale `opencensus-ext-pymysql` do [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) e adicione as linhas a seguir ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Dependências com a integração "PostgreSQL"

Acompanhe suas dependências do PostgreSQL com a integração do OpenCensus `postgresql` . Essa integração dá suporte à biblioteca [psycopg2](https://pypi.org/project/psycopg2/) .

Baixe e instale `opencensus-ext-postgresql` do [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) e adicione as linhas a seguir ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Dependências com a integração "pymongo"

Acompanhe suas dependências do MongoDB com a integração do OpenCensus `pymongo` . Essa integração dá suporte à biblioteca [pymongo](https://pypi.org/project/pymongo/) .

Baixe e instale `opencensus-ext-pymongo` do [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) e adicione as linhas a seguir ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Dependências com a integração "SQLAlchemy"

Acompanhe suas dependências usando o SQLAlchemy usando a integração do OpenCensus `sqlalchemy` . Essa integração acompanha o uso do pacote [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) , independentemente do banco de dados subjacente.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Próximas etapas

* [Mapa do aplicativo](./app-map.md)
* [Disponibilidade](./monitor-web-app-availability.md)
* [Pesquisa](./diagnostic-search.md)
* [Consulta de log (Analytics)](../logs/log-query-overview.md)
* [Diagnóstico da transação](./transaction-diagnostics.md)

