---
title: Como usar Application Insights agente de In-Process Java no Azure Spring Cloud
description: Como monitorar aplicativos e microserviços usando Application Insights agente de In-Process Java no Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: f6f1ed1a3e09397a720dfd4d842b79cd88aa738d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877804"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Application Insights o agente de In-Process Java no Azure Spring Cloud (versão prévia)

Este documento explica como monitorar aplicativos e microserviços usando o Application Insights agente Java no Azure Spring Cloud. 

Com esse recurso, você pode:

* Pesquisar dados de rastreamento com diferentes filtros.
* Exibir mapa de dependências de microserviços.
* Verifique o desempenho da solicitação.
* Monitorar métricas dinâmicas em tempo real.
* Verificar falhas de solicitação.
* Verifique as métricas do aplicativo.

Application Insights fornecer muitas perspectivas observáveis, incluindo:

* Mapa do aplicativo
* Desempenho
* Falhas
* Métricas
* Live Metrics    
* Disponibilidade

## <a name="enable-java-in-process-agent-for-application-insights"></a>Habilitar o agente de In-Process Java para Application Insights

Habilite o recurso de visualização do agente In-Process do Java usando o procedimento a seguir.

1. Vá para a página Visão geral do serviço da sua instância de serviço.
2. Clique em **Application insights** entrada na folha monitoramento.
3. Clique no botão **habilitar Application insights** para habilitar a integração de **Application insights** .
4. Selecione uma instância existente do Application Insights ou crie uma nova.
5. Chick **habilitar o agente em processo Java** para habilitar o recurso de agente em processo do Java de visualização. Aqui você também pode personalizar a taxa de amostragem de 0 a 100.
6.  Clique em **Salvar** para salvar a alteração.

## <a name="portal"></a>Portal

1. Ir para o **serviço | Página de visão geral** e selecione **Application insights** na seção **monitoramento** . 
2. Clique em **habilitar Application insights** para habilitar o Application insights no Azure Spring Cloud.
3. Clique em **habilitar agente em processo Java** para habilitar o recurso Java IPA Preview. Quando um recurso de visualização do IPA está habilitado, você pode configurar uma taxa de amostragem opcional (padrão 10,0%).

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Usando o recurso Application Insights

Quando o recurso de **Application insights** está habilitado, você pode:

No painel de navegação esquerdo, clique em **Application insights** para ir para a página **visão geral** do Application insights. 

* Clique em **mapa do aplicativo** para ver o status de chamadas entre aplicativos.

  [![Ipa 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Clique no link entre Customers-Service e `petclinic` para ver mais detalhes, como uma consulta do SQL.

* No painel de navegação esquerdo, clique em **desempenho** para ver os dados de desempenho de todas as operações de aplicativos, bem como dependências e funções.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* No painel de navegação esquerdo, clique em **falhas** para ver se algo é inesperado em seus aplicativos.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* No painel de navegação esquerdo, clique em **métricas** e selecione o namespace, você verá as métricas do Spring boot e as métricas personalizadas, se houver.

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* No painel de navegação esquerdo, clique em **métricas ao vivo** para ver as métricas em tempo real para dimensões diferentes.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* No painel de navegação esquerdo, clique em **disponibilidade** para monitorar a disponibilidade e a capacidade de resposta dos aplicativos Web Criando [testes de disponibilidade no Application insights](../azure-monitor/app/monitor-web-app-availability.md).

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>Modelo do ARM
Para usar o modelo de Azure Resource Manager, copie o conteúdo a seguir para `azuredeploy.json` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

## <a name="cli"></a>CLI
Aplique o modelo ARM com o comando da CLI:

* Para uma instância existente do Azure Spring Cloud:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" –name "assignedName" –resource-group "resourceGroupName"
```
* Para uma instância recém-criada do Azure Spring Cloud:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" –resource-group "resourceGroupName"
```
* Para desabilitar o app-Insight:

```azurecli
az spring-cloud app-insights update --disable –name "assignedName" –resource-group "resourceGroupName"

```

## <a name="see-also"></a>Confira também
* [Usar o rastreamento distribuído com o Azure Spring Cloud](spring-cloud-howto-distributed-tracing.md)
* [Analisar logs e métricas](diagnostic-services.md)
* [Transmitir logs em tempo real](spring-cloud-howto-log-streaming.md)