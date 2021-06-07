---
title: Guia de início rápido do Assistente de Métricas para Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: b6bea3fdc47bfbb74c18161a400d59453540b14d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081147"
---
[Documentação de referência](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/README.md) | [Pacote (PiPy)](https://pypi.org/project/azure-ai-metricsadvisor/) | [Exemplos](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples/README.md)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Depois que tiver sua assinatura do Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Criar um recurso do Assistente de Métricas"  target="_blank"> crie um recurso do Assistente de Métricas </a> no portal do Azure para implantar sua instância do Assistente de Métricas.  
* Seu Banco de Dados SQL com alguns dados de série temporal.
  
> [!TIP]
> * Encontre exemplos do Assistente de Métricas para Python no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples).
> * Poderá levar de 10 a 30 minutos para que o recurso do Assistente de Métricas implante uma instância de serviço para seu uso. Clique em **Ir para o recurso** quando ele for implantado com êxito. Após a implantação, você pode começar a usar sua instância do Assistente de Métricas com o portal da Web e com a API REST.
> * Você pode encontrar a URL para a API REST no portal do Azure, na seção **Visão geral** do recurso. Ele terá a seguinte aparência:
>    * `https://<instance-name>.cognitiveservices.azure.com/` 
 
## <a name="setting-up"></a>Configurando

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Depois de instalar o Python, você pode instalar a biblioteca de clientes com:

```console
pip install azure-ai-metricsadvisor --pre
```

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Crie um arquivo do Python e importe as bibliotecas a seguir.

```python
import os
import datetime
```

Crie variáveis para a chave e o ponto de extremidade do Azure do recurso.

> [!IMPORTANT]
> Acesse o portal do Azure. Se o recurso do Assistente de Métricas que você criou na seção **Pré-requisitos** tiver sido implantado com êxito, clique no botão **Ir para o Recurso** em **Próximas Etapas**. Encontre as chaves de assinatura e o ponto de extremidade na página **Chave e Ponto de Extremidade** do recurso, em **Gerenciamento de Recursos**. <br><br>Para recuperar sua chave de API, acesse [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net). Selecione as opções apropriadas: **Diretório**, **Assinaturas** e **Workspace** para seu recurso e escolha **Introdução**. Depois, você poderá recuperar suas chaves de API em [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key).   
>
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Confira o artigo [segurança](../../../cognitive-services-security.md) de Serviços Cognitivos para obter mais informações.

```python
subscription_key = "<paste-your-metrics-advisor-subscription-key-here>"
api_key = "<paste-your-metrics-advisor-api-key-here>"
service_endpoint = "<paste-your-metrics-advisor-endpoint-here>"
```

## <a name="object-model"></a>Modelo de objeto

As classes a seguir lidam com alguns dos principais recursos do SDK do Assistente de Métricas para Python.

|Nome|Descrição|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient) | **Usada para**: <br> – Listar incidentes <br> – Listar a causa raiz de incidentes <br> – Recuperar dados de série temporal originais e dados de série temporal enriquecidos pelo serviço. <br> – Listar alertas <br> – Adicionar comentários para ajustar seu modelo |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorAdministrationClient) | **Permite a você:** <br> – Gerenciar feeds de dados <br> – Criar, configurar, recuperar, listar e excluir configurações de detecção de anomalias <br> – Criar, configurar, recuperar, listar e excluir configurações de alerta de anomalias <br> – Gerenciar ganchos  |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeed#azure.ai.metricsadvisor.models.DataFeed)| **O que o Assistente de Métricas ingere da fonte de dados. Um `DataFeed` contém linhas de:** <br> – Carimbos de data/hora <br> – Zero ou mais dimensões <br> – Uma ou mais medidas  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeedmetric#azure.ai.metricsadvisor.models.DataFeedMetric) | Uma `DataFeedMetric` é uma medida quantificável usada para monitorar e avaliar o status de um processo empresarial específico. Pode ser uma combinação de vários valores de série temporal divididos em dimensões. Por exemplo, uma métrica de integridade da Web pode conter dimensões para contagem de usuários e mercado en-us. |

## <a name="code-examples"></a>Exemplos de código

Esses snippets de códigos mostram como fazer o seguinte com a biblioteca de clientes do Assistente de Métricas para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Adicionar um feed de dados](#add-a-data-feed)
* [Verificar o status da ingestão](#check-the-ingestion-status)
* [Definir a configuração de detecção e de alerta](#configure-anomaly-detection)
* [Criar uma configuração de alerta](#create-an-alert-configuration)
* [Consultar os resultados da detecção de anomalias](#query-the-alert)

## <a name="authenticate-the-client"></a>Autenticar o cliente

O cliente deste exemplo é um objeto `MetricsAdvisorAdministrationClient` que usa o ponto de extremidade e um objeto `MetricsAdvisorKeyCredential` que contém suas chaves. Você não precisa copiar este exemplo de código. Os métodos que você criar posteriormente criarão uma instância do cliente. O cliente alternativo é chamado `MetricsAdvisorClient`. Encontre mais informações sobre ele na [documentação de referência](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient).

```python
client = MetricsAdvisorAdministrationClient(service_endpoint,
                                MetricsAdvisorKeyCredential(subscription_key, api_key))
```

## <a name="add-a-data-feed"></a>Adicionar um feed de dados

Em um novo método, crie instruções de importação como o exemplo abaixo. Substitua `sql_server_connection_string` pela sua cadeia de conexão do SQL Server e substitua `query` por uma consulta que retorne seus dados em um só carimbo de data/hora. Você também precisará ajustar os valores de `DataFeedmetric` e `DataFeedDimension` com base nos seus dados personalizados.

> [!IMPORTANT]
> A consulta deve retornar no máximo um registro para cada combinação de dimensão, em cada carimbo de data/hora. E todos os registros retornados pela consulta precisam ter os mesmos carimbos de data/hora. O Assistente de Métricas executará essa consulta para cada carimbo de data/hora para ingerir seus dados. Confira a seção [Perguntas frequentes sobre consultas](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) para ver mais informações e exemplos. 

Crie um cliente com as chaves e o ponto de extremidade e use `client.create_data_feed()` para configurar o nome, a origem, a granularidade e o esquema. Você também pode definir o tempo de ingestão, as configurações de rollup, entre outros.


```python
def sample_create_data_feed():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        SQLServerDataFeed,
        DataFeedSchema,
        DataFeedMetric,
        DataFeedDimension,
        DataFeedOptions,
        DataFeedRollupSettings
    )
    sql_server_connection_string = "<replace-with-your-sql-server-connection-string>"
    query = "<replace-with-metrics-advisor-sql-server-query>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    data_feed = client.create_data_feed(
    name="My data feed",
    source=SQLServerDataFeed(
        connection_string=sql_server_connection_string,
        query=query,
    ),
    granularity="Daily",
    schema=DataFeedSchema(
        metrics=[
            DataFeedMetric(name="cost", display_name="Cost"),
            DataFeedMetric(name="revenue", display_name="Revenue")
        ],
        dimensions=[
            DataFeedDimension(name="category", display_name="Category"),
            DataFeedDimension(name="city", display_name="City")
        ],
        timestamp_column="Timestamp"
    ),
    ingestion_settings=datetime.datetime(2019, 10, 1),
    options=DataFeedOptions(
        data_feed_description="cost/revenue data feed",
        rollup_settings=DataFeedRollupSettings(
            rollup_type="AutoRollup",
            rollup_method="Sum",
            rollup_identification_value="__CUSTOM_SUM__"
        ),
        missing_data_point_fill_settings=DataFeedMissingDataPointFillSettings(
            fill_type="SmartFilling"
        ),
        access_mode="Private"
    )
)

return data_feed
sample_create_data_feed()
```

## <a name="check-the-ingestion-status"></a>Verificar o status da ingestão

Em um novo método, crie uma instrução de importação como o exemplo abaixo. Substitua `data_feed_id` pela ID do feed de dados que você criou. Crie um cliente com as chaves e o ponto de extremidade e use `client.list_data_feed_ingestion_status()` para obter o progresso da ingestão. Imprima os detalhes, como os últimos carimbos de data/hora ativos e com êxito.


```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient

    data_feed_id = "<replace-with-your-metrics-advisor-data-feed-id>"

   client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

ingestion_status = client.list_data_feed_ingestion_status(
    data_feed_id,
    datetime.datetime(2020, 9, 20),
    datetime.datetime(2020, 9, 25)
)
for status in ingestion_status:
    print("Timestamp: {}".format(status.timestamp))
    print("Status: {}".format(status.status))
    print("Message: {}\n".format(status.message))

```

## <a name="configure-anomaly-detection"></a>Configurar a detecção de anomalias

Em um novo método, crie instruções de importação como o exemplo abaixo. Substitua `metric_id` pela ID da métrica que deseja configurar. Crie um cliente com as chaves e o ponto de extremidade e use `client.create_detection_configuration` para criar uma configuração de detecção. As condições de limite especificam os parâmetros da detecção de anomalias.

```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        ChangeThresholdCondition,
        HardThresholdCondition,
        SmartDetectionCondition,
        SuppressCondition,
        MetricDetectionCondition,
    )
    metric_id = "replace-with-your-metric-id"

    
client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

change_threshold_condition = ChangeThresholdCondition(
    anomaly_detector_direction="Both",
    change_percentage=20,
    shift_point=10,
    within_range=True,
    suppress_condition=SuppressCondition(
        min_number=5,
        min_ratio=2
    )
)
hard_threshold_condition = HardThresholdCondition(
    anomaly_detector_direction="Up",
    upper_bound=100,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)
smart_detection_condition = SmartDetectionCondition(
    anomaly_detector_direction="Up",
    sensitivity=10,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)

detection_config = client.create_detection_configuration(
    name="my_detection_config",
    metric_id=metric_id,
    description="anomaly detection config for metric",
    whole_series_detection_condition=MetricDetectionCondition(
        cross_conditions_operator="OR",
        change_threshold_condition=change_threshold_condition,
        hard_threshold_condition=hard_threshold_condition,
        smart_detection_condition=smart_detection_condition
    )
)
return detection_config
```

## <a name="create-a-hook"></a>Criar um gancho

Em um novo método, crie instruções de importação como o exemplo abaixo. Crie um cliente com as chaves e o ponto de extremidade e use `client.create_hook()` para criar um gancho. Insira uma descrição, uma lista de emails para os quais o alerta será enviado e um link externo para receber o alerta.  

```python
def sample_create_hook():

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import EmailNotificationHook

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key))

hook = client.create_hook(
    hook=EmailNotificationHook(
        name="email hook",
        description="my email hook",
        emails_to_alert=["alertme@alertme.com"],
        external_link="https://example.com/handleAlerts", # you must enter a valid webhook url to post the alert payload
    )
)
```

##  <a name="create-an-alert-configuration"></a>Criar uma configuração de alerta

Em um novo método, crie instruções de importação como o exemplo abaixo. Substitua `detection_configuration_id` pela ID da configuração de detecção de anomalias e substitua `hook_id` pelo gancho criado anteriormente. Crie um cliente com as chaves e o ponto de extremidade e use `client.create_alert_configuration()` para criar uma configuração de alerta. 

```python
def sample_create_alert_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        MetricAlertConfiguration,
        MetricAnomalyAlertScope,
        TopNGroupScope,
        MetricAnomalyAlertConditions,
        SeverityCondition,
        MetricBoundaryCondition,
        MetricAnomalyAlertSnoozeCondition
    )
    anomaly_detection_configuration_id = "<replace-with-your-detection-configuration-id"
    hook_id = "<replace-with-your-hook-id>"

    client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

alert_config = client.create_alert_configuration(
        name="my alert config",
        description="alert config description",
        cross_metrics_operator="AND",
        metric_alert_configurations=[
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="WholeSeries"
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    severity_condition=SeverityCondition(
                        min_alert_severity="Low",
                        max_alert_severity="High"
                    )
                )
            ),
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="TopN",
                    top_n_group_in_scope=TopNGroupScope(
                        top=10,
                        period=5,
                        min_top_count=5
                    )
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    metric_boundary_condition=MetricBoundaryCondition(
                        direction="Up",
                        upper=50
                    )
                ),
                alert_snooze_condition=MetricAnomalyAlertSnoozeCondition(
                    auto_snooze=2,
                    snooze_scope="Metric",
                    only_for_successive=True
                )
            ),
        ],
        hook_ids=[hook_id]
    )

    return alert_config
```

### <a name="query-the-alert"></a>Consultar o alerta

Em um novo método, crie uma instrução de importação como o exemplo abaixo. Substitua `alert_id` pela ID do alerta e substitua `alert_config_id` pela ID de configuração do alerta. Crie um cliente com suas chaves e seu ponto de extremidade e use `client.list_anomalies` para listar as anomalias para um alerta. 

```python
from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorClient
    
alert_id = "<replace-with-your-alert-id>"
alert_config_id = "<replace-with-your-alert-configuration-id"

client = MetricsAdvisorClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

results = client.list_alerts(
    alert_configuration_id=alert_config_id,
    start_time=datetime.datetime(2020, 1, 1),
    end_time=datetime.datetime(2020, 9, 9),
    time_mode="AnomalyTime",
)
for result in results:
    print("Alert id: {}".format(result.id))
    print("Create on: {}".format(result.created_on))

results = client.list_anomalies(
    alert_configuration_id=alert_config_id,
    alert_id=alert_id,
)
for result in results:
    print("Create on: {}".format(result.created_on))
    print("Severity: {}".format(result.severity))
    print("Status: {}".format(result.status))
```

### <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `python` no seu arquivo de início rápido.

```console
python quickstart-file.py
```
