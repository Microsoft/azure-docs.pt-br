---
title: Tutorial – Monitorar APIs publicadas no Gerenciamento de API do Azure | Microsoft Docs
description: Siga as etapas deste tutorial para aprender a usar métricas, alertas, logs de atividades e logs de recursos para monitorar APIs no Gerenciamento de API do Azure.
services: api-management
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 1cb902c4b59193c46dbeca47bb355f0695a0f2c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572803"
---
# <a name="tutorial-monitor-published-apis"></a>Tutorial: Monitorar APIs publicadas

Com o Azure Monitor, é possível visualizar, consultar, rotear, arquivar e realizar ações nas métricas ou nos logs do serviço de Gerenciamento de API do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Exibir métricas da API 
> * Configurar uma regra de alerta 
> * Exibir logs de atividade
> * Habilitar e ver logs de recursos

Use também a [análise](howto-use-analytics.md) interna do API Management para monitorar o uso e o desempenho das suas APIs.

## <a name="prerequisites"></a>Pré-requisitos

+ Conheça a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md).
+ Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Exibir métricas das APIs

O Gerenciamento de API emite [métricas](../azure-monitor/essentials/data-platform-metrics.md) a cada minuto. Assim você vê quase em tempo real o estado e a integridade das suas APIs. Veja abaixo as duas métricas usadas com mais frequência. Para ver uma lista de todas as métricas disponíveis, confira [Métricas compatíveis](../azure-monitor/essentials/metrics-supported.md#microsoftapimanagementservice).

* **Capacidade:** ajuda a tomar decisões relacionadas ao upgrade/downgrade de seus serviços do APIM. A métrica é emitida por minuto e reflete a capacidade do gateway no momento da emissão dos relatórios. A métrica varia de 0 a 100 e é calculada com base nos recursos corretos do gateway, como a utilização da CPU e a memória.
* **Solicitações:** ajuda você a analisar o tráfego de API que passa pelos serviços do Gerenciamento de API. A métrica é emitida por minuto e relata o número de solicitações de gateway com dimensões, incluindo códigos de resposta, localização, nome de host e erros. 

> [!IMPORTANT]
> As seguintes métricas foram preteridas em maio de 2019 e serão desativadas em agosto de 2023: Total de solicitações de gateway, solicitações de gateway bem-sucedidas, solicitações de gateway não autorizadas, solicitações de gateway com falha, outras solicitações de gateway. Migre para a métrica de solicitações, que fornece funcionalidade equivalente.

:::image type="content" source="media/api-management-howto-use-azure-monitor/apim-monitor-metrics.png" alt-text="Captura de tela de &quot;Métricas&quot; na visão geral do Gerenciamento de API":::

Para acessar as métricas:

1. No [portal do Azure](https://portal.azure.com), navegue até a instância do Gerenciamento de API. Na página de **visão geral**, analise as principais métricas das suas APIs.
1. Para analisar as métricas detalhadamente, selecione **Métricas** no menu na parte inferior da página.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-metrics-blade.png" alt-text="Captura de tela do item &quot;Métricas&quot; no menu &quot;Monitoramento&quot;":::

1. Na lista suspensa, selecione as métricas em que você está interessado. Por exemplo, **Solicitações**. 
1. O gráfico mostra o número total de chamadas à API.
1. O gráfico pode ser filtrado usando as dimensões da métrica **Solicitações**. Por exemplo, selecione **Adicionar filtro**, **Categoria de Código de Resposta do Back-end** e insira 500 como valor. Agora, o gráfico mostra o número de solicitações que falharam no back-end da API.   

## <a name="set-up-an-alert-rule"></a>Configurar uma regra de alerta 

É possível receber [alertas](../azure-monitor/alerts/alerts-metric-overview.md) com base em métricas e logs de atividades. O Azure Monitor permite [configurar um alerta](../azure-monitor/alerts/alerts-metric.md) para que ele faça o seguinte quando for acionado:

* Enviar uma notificação por email
* Chamar um webhook
* Invocar um aplicativo lógico do Azure

Para configurar um exemplo de regra de alerta com base em uma métrica de solicitação:

1. No [portal do Azure](https://portal.azure.com), navegue até a instância do Gerenciamento de API.
1. Selecione **Alertas** na barra de menus na parte inferior da página.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/alert-menu-item.png" alt-text="Captura de tela da opção &quot;Alertas&quot; no menu &quot;Monitoramento&quot;":::

1. Selecione **+ Nova regra de alerta**.
1. Na janela **Criar regra de alerta**, clique em **Selecionar condição**.
1. Na janela **Configurar lógica de sinal**:
    1. Em **Tipo de sinal**, selecione **Métricas**.
    1. Em **Nome do sinal**, selecione **Solicitações**.
    1. Em **Dividir por dimensões**, em **Nome da dimensão**, selecione **Categoria de Código de Resposta do Gateway**.
    1. Em **Valores de dimensão**, selecione **4xx** para erros de cliente, como solicitações não autorizadas ou inválidas.
    1. Em **Lógica de alerta**, especifique um limite após o qual o alerta deve ser disparado e clique em **Concluído**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/threshold.png" alt-text="Captura de tela das janelas &quot;Configurar Lógica de Sinal&quot;":::

1. Selecione um grupo de ações existente ou crie um. Um grupo de ações é criado no exemplo a seguir. Um email de notificação será enviado a admin@contoso.com. 

    :::image type="content" source="media/api-management-howto-use-azure-monitor/action-details.png" alt-text="Captura de tela das notificações para o novo grupo de ações":::

1. Insira um nome e uma descrição da regra de alerta e selecione o nível de gravidade. 
1. Selecione **Criar regra de alerta**.
1. Agora, teste a regra de alerta chamando a API de Conferência sem uma chave de API. Por exemplo:

    ```bash
    curl GET https://apim-hello-world.azure-api.net/conference/speakers HTTP/1.1 
    ```

    Um alerta será disparado com base no período de avaliação e o email será enviado para admin@contoso.com. 

    Os alertas também aparecem na página **Alertas** da instância do Gerenciamento de API.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/portal-alerts.png" alt-text="Captura de tela dos alertas no portal":::

## <a name="activity-logs"></a>Logs de atividades

Os logs de atividades fornecem informações sobre as operações que foram realizadas em seus serviços de Gerenciamento de API. Usando logs de atividades, é possível determinar “o que, quem e quando” para quaisquer operações de gravação (PUT, POST, DELETE) realizadas em seus serviços de Gerenciamento de API.

> [!NOTE]
> Os logs de atividades não incluem operações de leitura (GET) ou operações realizadas no portal do Azure ou usando as APIs de gerenciamento original.

É possível acessar os logs de atividades em seu serviço de Gerenciamento de API ou acessar logs de todos os seus recursos do Azure no Azure Monitor. 

:::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs.png" alt-text="Captura de tela do log de atividades no portal":::

Para ver o log de atividades:

1. No [portal do Azure](https://portal.azure.com), navegue até a instância do Gerenciamento de API.

1. Selecione **Log de atividades**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs-blade.png" alt-text="Captura de tela do item &quot;Log de atividades&quot; no menu &quot;Monitoramento&quot;":::
1. Selecione o escopo de filtragem desejado e clique em **Aplicar**.

## <a name="resource-logs"></a>Logs de recursos

Os logs de recursos fornecem informações avançadas sobre operações e erros importantes para auditoria, bem como para fins de solução de problemas. Os logs de recursos são diferentes dos logs de atividades. O log de atividades mostra insights sobre as operações realizadas em seus recursos do Azure. Os logs de recursos fornecem informações sobre operações que o recurso realizou.

Para configurar os logs de recursos:

1. No [portal do Azure](https://portal.azure.com), navegue até a instância do Gerenciamento de API.
2. Selecione **Configurações de diagnóstico**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-diagnostic-logs-blade.png" alt-text="Captura de tela do item &quot;Configurações de diagnóstico&quot; no menu &quot;Monitoramento&quot;":::

1. Selecione **+ Adicionar configuração de diagnóstico**.
1. Selecione os logs ou as métricas que você deseja coletar.

   É possível arquivar os logs de recursos junto com as métricas em uma conta de armazenamento, transmiti-los para um Hub de Eventos ou enviá-los para o workspace do Log Analytics. 

Para saber mais, confira [Criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes](../azure-monitor/essentials/diagnostic-settings.md).

## <a name="view-diagnostic-data-in-azure-monitor"></a>Ver dados de diagnóstico no Azure Monitor

Se você habilitar a coleta de GatewayLogs ou métricas em um workspace do Log Analytics, poderá levar alguns minutos para que os dados apareçam no Azure Monitor. Para ver os dados:

1. No [portal do Azure](https://portal.azure.com), navegue até a instância do Gerenciamento de API.
1. Selecione **Logs** no menu na parte inferior da página.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/logs-menu-item.png" alt-text="Captura de tela do item &quot;Logs&quot; no menu &quot;Monitoramento&quot;":::

Execute consultas para ver os dados. Existem várias [consultas de exemplo](../azure-monitor/logs/example-queries.md). Você também pode executar as suas. Por exemplo, a seguinte consulta recupera as 24 horas mais recentes de dados da tabela GatewayLogs:

```kusto
ApiManagementGatewayLogs
| where TimeGenerated > ago(1d) 
```

Para saber mais sobre como usar logs de recursos do Gerenciamento de API, confira:

* [Introdução ao Log Analytics do Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md) ou experimente o [Ambiente de Demonstração do Log Analytics](https://portal.loganalytics.io/demo).

* [Visão geral sobre consultas de log no Azure Monitor](../azure-monitor/logs/log-query-overview.md).

O JSON a seguir indica uma entrada de exemplo em GatewayLogs para uma solicitação de API bem-sucedida. Para saber detalhes, confira a [referência de esquema](gateway-log-schema-reference.md). 

```json
{
    "Level": 4,
    "isRequestSuccess": true,
    "time": "2020-10-14T17:xx:xx.xx",
    "operationName": "Microsoft.ApiManagement/GatewayLogs",
    "category": "GatewayLogs",
    "durationMs": 152,
    "callerIpAddress": "xx.xx.xxx.xx",
    "correlationId": "3f06647e-xxxx-xxxx-xxxx-530eb9f15261",
    "location": "East US",
    "properties": {
        "method": "GET",
        "url": "https://apim-hello-world.azure-api.net/conference/speakers",
        "backendResponseCode": 200,
        "responseCode": 200,
        "responseSize": 41583,
        "cache": "none",
        "backendTime": 87,
        "requestSize": 526,
        "apiId": "demo-conference-api",
        "operationId": "GetSpeakers",
        "apimSubscriptionId": "master",
        "clientTime": 65,
        "clientProtocol": "HTTP/1.1",
        "backendProtocol": "HTTP/1.1",
        "apiRevision": "1",
        "clientTlsVersion": "1.2",
        "backendMethod": "GET",
        "backendUrl": "https://conferenceapi.azurewebsites.net/speakers"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/APIM-HELLO-WORLD"
}
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Exibir métricas da API
> * Configurar uma regra de alerta 
> * Exibir logs de atividade
> * Habilitar e ver logs de recursos


Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Rastrear chamadas](api-management-howto-api-inspector.md)