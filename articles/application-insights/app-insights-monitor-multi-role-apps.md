---
title: "Suporte do Application Insights do Azure para vários componentes, microsserviços e contêineres | Microsoft Docs"
description: "Monitoramento de aplicativos que consistem em vários componentes ou funções para desempenho e uso."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/17/2017
ms.author: bwren
ms.openlocfilehash: ca1bb8ee886c4b4e69be9dd653d6a52b874e1f5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Monitore aplicativos de vários componentes com o Application Insights (visualização)

Você pode monitorar aplicativos que consistem em vários componentes, funções ou serviços de servidor com [Application Insights do Azure](app-insights-overview.md). A integridade dos componentes e as relações entre eles são exibidas em um mapa de aplicativo único. Você pode rastrear a operações individuais por meio de vários componentes com correlação automática de HTTP. Os diagnósticos do contêiner podem ser integrados e correlacionados à telemetria de aplicativos. Use um único recurso do Application Insights para todos os componentes do aplicativo. 

![Mapa de aplicativos de vários componentes](./media/app-insights-monitor-multi-role-apps/app-map.png)

Usamos 'componente' aqui para indicar qualquer parte funcional de um aplicativo grande. Por exemplo, um aplicativo de negócios típico pode consistir em código do cliente em execução em navegadores da Web, conversando com um ou mais serviços de aplicativos Web, que, por sua vez, usam serviços de back-end. Os componentes de servidor podem ser hospedados no local ou na nuvem, podem ser funções da Web e de trabalho do Azure ou podem ser executados em contêineres como Docker ou Service Fabric. 

### <a name="sharing-a-single-application-insights-resource"></a>Compartilhamento um único recurso do Application Insights 

A principal técnica aqui é enviar telemetria de cada componente em seu aplicativo para o mesmo recurso do Application Insights, mas usar a propriedade `cloud_RoleName` para distinguir os componentes quando necessário. O SDK do Application Insights adiciona a propriedade `cloud_RoleName` para a emissão dos componentes de telemetria. Por exemplo, o SDK adicionará um nome do site ou nome de função de serviço para a propriedade `cloud_RoleName`. Você pode substituir esse valor com um telemetryinitializer. O mapa de aplicativo usa a propriedade `cloud_RoleName` para identificar os componentes no mapa.

Para obter mais informações sobre como substituir a propriedade `cloud_RoleName` consulte [Adicionar propriedades: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer).  

Em alguns casos, isso pode não ser apropriado e talvez você prefira usar recursos separados para diferentes grupos de componentes. Por exemplo, talvez seja necessário usar recursos diferentes para fins de cobranças ou gerenciamento. O uso de recursos separados significa que você não vê todos os componentes exibidos em um mapa de aplicativo único e não pode consultar componentes na [Análise](app-insights-analytics.md). Você também precisa configurar os recursos separados.

Com essa limitação, vamos supor no restante desse documento que você quer enviar dados de vários componentes para um recurso do Application Insights.

## <a name="configure-multi-component-applications"></a>Configurar aplicativos de vários componentes

Para obter um mapa de aplicativo de vários componentes, você cumprir os seguintes requisitos:

* **Instalar a versão de pré-lançamento mais recente** do pacote Application Insights em cada componente do aplicativo. 
* **Compartilhar um único recurso do Application Insights** para todas as funções do aplicativo.
* **Habilitar o mapa de aplicativo de várias funções** na folha de visualizações.

Configurar cada componente do aplicativo usando o método apropriado para seu tipo. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), [JavaScript](app-insights-javascript.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. Instalação do pacote de pré-lançamento mais recente

Como atualizar ou instalar os pacotes do Application Insights no projeto para cada componente de servidor. Se você estiver usando o Visual Studio:

1. Clique o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet**. 
2. Selecione **Incluir pré-lançamento**.
3. Se pacotes do Application Insights aparecerem nas atualizações, selecione-os. 

    Caso contrário, procure e instale o pacote apropriado:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric – para componentes em execução como executáveis de convidado e executando um aplicativo do Service Fabric em contêineres do Docker
    * Microsoft.ApplicationInsights.ServiceFabric.Native - para serviços confiáveis em aplicativos no Service Fabric
    * Microsoft.ApplicationInsights.Kubernetes para componentes em execução no Docker e nos Kubernetes

### <a name="2-share-a-single-application-insights-resource"></a>2. Compartilhamento de um único recurso do Application Insights

* No Visual Studio, clique o botão direito do mouse em um projeto e selecione **Configurar o Application Insights** ou **Application Insights > Configuração**. Para o primeiro projeto, use o assistente para criar um recurso do Application Insights. Para projetos subsequentes, selecione o mesmo recurso.
* Se não houver nenhum menu do Application Insights, configure manualmente:

   1. No [Portal do Azure](https://portal,azure.com), abra o recurso do Application Insights já criado para outro componente.
   2. Na folha de Visão geral, abra a guia suspensa Essentials e copie a **Chave de instrumentação.**
   3. No seu projeto, abra ApplicationInsights.config e insira: `<InstrumentationKey>your copied key</InstrumentationKey>`

![Copie a chave de instrumentação para o arquivo .config](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-multi-role-application-map"></a>3. Habilitação do mapa de aplicativo de várias funções

No portal do Azure, abra o recurso do seu aplicativo. Na folha de visualizações, habilite o *Mapa de Aplicativo de Várias Funções*.

### <a name="4-enable-docker-metrics-optional"></a>4. Habilitação das métricas de Docker (opcional) 

Se um componente é executado no Docker hospedado em uma VM Windows do Azure, você pode coletar métricas adicionais do contêiner. Insira isso no seu arquivo de configuração de [diagnóstico do Azure](../monitoring-and-diagnostics/azure-diagnostics.md):

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## <a name="use-cloudrolename-to-separate-components"></a>Usar cloud_RoleName para separar componentes

A propriedade `cloud_RoleName` está conectada a todas as telemetrias. Identifica o componente (a função ou o serviço) que origina a telemetria. (Não é igual a cloud_RoleInstance, que separa funções idênticas executadas em paralelo em vários processos do servidor ou computadores.)

No portal, você pode filtrar ou segmentar a sua telemetria usando essa propriedade. Neste exemplo, a folha de falhas é filtrada para mostrar apenas as informações do serviço Web do front-end, a filtragem de falhas de back-end da API do CRM:

![Gráfico de métricas segmentado por nome de função de nuvem](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>Rastrear operações entre componentes

Você pode rastrear, de um componente para outro, as chamadas feitas durante o processamento de uma operação individual.


![Mostrar telemetria para operação](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Clique em uma lista correlacionada de telemetria para esta operação entre o servidor web de front-end e a API de back-end:

![Pesquisar entre os componentes](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Próximas etapas

* [Separação da telemetria de desenvolvimento, teste e produção](app-insights-separate-resources.md)
