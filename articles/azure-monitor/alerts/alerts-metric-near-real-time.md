---
title: Recursos com suporte para alertas de métrica no Azure Monitor
description: Referência sobre métricas e logs de suporte para alertas de métrica no Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 02/10/2021
ms.subservice: alerts
ms.openlocfilehash: 7b4cdd37cefb628c03129a990d52ed348acd3ac1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100605169"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Recursos com suporte para alertas de métrica no Azure Monitor

Agora, o Azure Monitor dá suporte a um [novo tipo de alerta de métrica](../platform/alerts-overview.md), que tem vantagens significativas sobre os [alertas de métrica clássicos](./alerts-classic.overview.md) mais antigos. As métricas estão disponíveis para uma [ampla lista de serviços do Azure](../platform/metrics-supported.md). Os alertas mais recentes dão suporte a um subconjunto (cada vez maior) de tipos de recurso. Este artigo lista esse subconjunto.

Você também pode usar alertas de métrica mais recentes em dados de log populares armazenados em um espaço de trabalho Log Analytics extraído como métricas. Para saber mais, exiba [Alertas de Métrica para Logs](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLI, suporte REST
No momento, você pode criar alertas de métrica mais recentes somente nos modelos portal do Azure, [API REST](/rest/api/monitor/metricalerts/)ou [Gerenciador de recursos](./alerts-metric-create-templates.md). Suporte para configurar os alertas mais recentes usando o PowerShell e a CLI do Azure versões 2.0 e superiores em breve.

## <a name="metrics-and-dimensions-supported"></a>Medidas e dimensões com suporte
Os alertas de métrica mais recentes fornecem suporte a alertas para métricas que usam dimensões. É possível usar dimensões para filtrar sua métrica para o nível certo. Todas as métricas compatíveis, juntamente com as dimensões aplicáveis, podem ser exploradas e visualizadas no [Azure Monitor – Metrics Explorer](../essentials/metrics-charts.md).

Aqui está a lista completa de Azure Monitor fontes métricas com suporte dos alertas mais recentes:

|Tipo de recurso  |Dimensões compatíveis |Alertas de vários recursos| Métricas disponíveis|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Sim | Não | |
|Microsoft.ApiManagement/service | Sim | Não | [Gerenciamento da API](../platform/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft.AppConfiguration/configurationStores |Sim | Não | [Configuração de Aplicativos](../platform/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft.AppPlatform/Spring | Sim | Não | [Azure Spring Cloud](../platform/metrics-supported.md#microsoftappplatformspring) |
|Microsoft.Automation/automationAccounts | Sim| Não | [Contas de Automação](../platform/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/privateClouds | Não | Não | [Solução VMware no Azure](../platform/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.Batch/batchAccounts | Sim | Não | [Contas de Lote](../platform/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft.Cache/Redis | Sim | Sim | [Cache Redis do Azure](../platform/metrics-supported.md#microsoftcacheredis) |
|Microsoft.ClassicCompute/domainNames/slots/roles | Não | Não | [Serviços de nuvem clássico](../platform/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft.ClassicCompute/virtualMachines | Não | Não | [Máquinas virtuais clássicas](../platform/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft.ClassicStorage/storageAccounts | Sim | Não | [Contas de armazenamento (clássicas)](../platform/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft.ClassicStorage/storageAccounts/blobServices | Sim | Não | [Contas de armazenamento (clássicas) – BLOBs](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft.ClassicStorage/storageAccounts/fileServices | Sim | Não | [Contas de armazenamento (clássicas)-arquivos](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft.ClassicStorage/storageAccounts/queueServices | Sim | Não | [Contas de armazenamento (clássicas)-filas](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft.ClassicStorage/storageAccounts/tableServices | Sim | Não | [Contas de armazenamento (clássicas)-tabelas](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft.CognitiveServices/accounts | Sim | Não | [Serviços Cognitivos](../platform/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Sim | Sim<sup>1</sup> | [Máquinas virtuais](../platform/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Sim | Não |[Conjuntos de dimensionamento de máquinas virtuais](../platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft.ContainerInstance/containerGroups | Sim| Não | [Grupos de contêineres](../platform/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft.ContainerRegistry/registries | Não | Não | [Registros de contêiner](../platform/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft.ContainerService/managedClusters | Sim | Não | [Clusters gerenciados](../platform/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Sim | Sim | [Data Box](../platform/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft.DataFactory/datafactories| Sim| Não | [Data Factories V1](../platform/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft.DataFactory/factories |Sim | Não | [Data Factories V2](../platform/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/accounts | Sim | Não | [Compartilhamentos de dados](../platform/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft.DBforMariaDB/servers | Não | Não | [DB para MariaDB](../platform/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servers | Não | Não |[Banco de dados para MySQL](../platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers | Não | Não | [Banco de dados para PostgreSQL](../platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.DBforPostgreSQL/serversv2 | Não | Não | [BD para PostgreSQL v2](../platform/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft.DBforPostgreSQL/flexibleServers | Sim | Não | [BD para PostgreSQL (servidores flexíveis)](../platform/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft.Devices/IotHubs | Sim | Não |[Hub IoT](../platform/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft.Devices/provisioningServices| Sim | Não | [Serviços de provisionamento de dispositivos](../platform/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Sim | Não | [Gêmeos Digitais](../platform/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Sim | Não | [Cosmos DB](../platform/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft.EventGrid/domains | Sim | Não | [Domínios de Grade de Eventos](../platform/metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Sim | Não | [Tópicos do sistema de grade de eventos](../platform/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft.EventGrid/topics |Sim | Não | [Tópicos de Grade de Eventos](../platform/metrics-supported.md#microsofteventgridtopics) |
|Microsoft.EventHub/clusters |Sim| Não | [Clusters de hubs de eventos](../platform/metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/namespaces |Sim| Não | [Hubs de Evento](../platform/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft.HDInsight/clusters | Sim | Não | [Clusters HDInsight](../platform/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft.Insights/Components | Sim | Não | [Application Insights](../platform/metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/vaults | Sim |Sim |[Cofres](../platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Kusto/Clusters | Sim |Não |[Clusters Data Explorer](../platform/metrics-supported.md#microsoftkustoclusters)|
|Microsoft.Logic/integrationServiceEnvironments | Sim | Não |[Ambientes de serviço de integração](../platform/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft.Logic/workflows | Não | Não |[Aplicativos Lógicos](../platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces | Sim | Não | [Machine Learning](../platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Maps/accounts | Sim | Não | [Contas de mapas](../platform/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft.Media/mediaservices | Não | Não | [Serviços de Mídia](../platform/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft.Media/mediaservices/streamingEndpoints | Sim | Não | [Pontos de extremidade de streaming dos serviços de mídia](../platform/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft.NetApp/netAppAccounts/capacityPools | Sim | Sim | [Pools de capacidade do Azure NetApp](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes | Sim | Sim | [Volumes do Azure NetApp](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways | Sim | Não | [Gateways de aplicativo](../platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/azurefirewalls | Sim | Não | [Firewalls](../platform/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | Não | Não | [Zonas DNS](../platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Sim | Não |[Circuitos do ExpressRoute](../platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/expressRoutePorts | Sim | Não |[ExpressRoute Direct](../platform/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft.Network/loadBalancers (apenas para SKUs Standard)| Sim| Não | [Balanceadores de carga](../platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| Não | Não | [Gateways NAT](../platform/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft. Network/privateEndpoints| Não | Não | [Pontos de extremidade privados](../platform/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft.Network/privateLinkServices| Não | Não | [Serviços de vínculo privado](../platform/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft.Network/publicipaddresses | Não | Não | [Endereços IP Públicos](../platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Sim | Não | [Perfis do Gerenciador de Tráfego](../platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Sim | Não | [Workspaces do Log Analytics](../platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. emparelhamento/emparelhamentos | Sim | Não | [Emparelhamentos](../platform/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. emparelhamento/peeringServices | Sim | Não | [Serviços de Emparelhamento](../platform/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft.PowerBIDedicated/capacities | Não | Não | [Capacidades](../platform/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft.Relay/namespaces | Sim | Não | [Retransmissão](../platform/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft.Search/searchServices | Não | Não | [Serviços Search](../platform/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | Sim | Não | [Barramento de Serviço](../platform/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft.Sql/managedInstances | Não | Sim | [Instâncias gerenciadas do SQL](../platform/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servers/databases | Não | Sim | [Bancos de dados SQL](../platform/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft.Sql/servers/elasticPools | Não | Sim | [Pools elásticos do SQL](../platform/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAccounts |Sim | Não | [Contas de Armazenamento](../platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/blobServices | Sim| Não | [Contas de armazenamento-BLOBs](../platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft.Storage/storageAccounts/fileServices | Sim| Não | [Contas de armazenamento-arquivos](../platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft.Storage/storageAccounts/queueServices | Sim| Não | [Contas de armazenamento-filas](../platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft.Storage/storageAccounts/tableServices | Sim| Não | [Contas de armazenamento – tabelas](../platform/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft.StorageCache/caches | Sim | Não | [Caches HPC](../platform/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft. StorageSync/storageSyncServices | Sim | Não | [Serviços de sincronização de armazenamento](../platform/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft.StreamAnalytics/streamingjobs | Sim | Não | [Stream Analytics](../platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft.Synapse/workspaces | Sim | Não | [Synapse Analytics](../platform/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft. Synapse/Workspaces/bigDataPools | Sim | Não | [Pools de Apache Spark do Synapse Analytics](../platform/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft. Synapse/espaços de trabalho/sqlpools | Sim | Não | [Pools de SQL do Synapse Analytics](../platform/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft. VMWareCloudSimple/virtualMachines | Sim | Não | [Máquinas Virtuais de CloudSimple](../platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft.Web/hostingEnvironments/multiRolePools | Sim | Não | [Ambiente do Serviço de Aplicativo pools de várias funções](../platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Sim | Não | [Ambiente do Serviço de Aplicativo pools de trabalho](../platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Sim | Não | [Planos do serviço de aplicativo](../platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Sim | Não | [Serviços de Aplicativos e Funções](../platform/metrics-supported.md#microsoftwebsites)|
|Microsoft.Web/sites/slots | Sim | Não | [Slots do Serviço de Aplicativo](../platform/metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> não há suporte para métricas de rede de máquina virtual (rede no total, total de saída de rede, fluxos de entrada, fluxos de saída, taxa de criação máxima de fluxos de entrada, taxa de criação máxima de fluxos de saída) e métricas personalizadas.

## <a name="payload-schema"></a>Esquema de conteúdo

> [!NOTE]
> Você também pode usar o [esquema de alerta comum](./alerts-common-schema.md), que fornece a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta no Azure monitor, para suas integrações de webhook. [Saiba mais sobre as definições de esquema de alerta comuns.](./alerts-common-schema-definitions.md)


A operação POST contém a carga JSON e esquema a seguir para todos os alertas de métrica mais recentes quando o [grupo de ações ](./action-groups.md) adequadamente configurado é usado:

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a nova [Experiência de alertas](../platform/alerts-overview.md).
* Saiba mais sobre os [alertas de log no Azure](./alerts-unified-log.md).
* Saiba mais sobre [alertas no Azure](../platform/alerts-overview.md).