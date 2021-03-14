---
title: Recursos com suporte para alertas de métrica no Azure Monitor
description: Referência sobre métricas e logs de suporte para alertas de métrica no Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: efc1438fdc539af278ebff1f292c5fa0a91b7b91
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016093"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Recursos com suporte para alertas de métrica no Azure Monitor

Agora, o Azure Monitor dá suporte a um [novo tipo de alerta de métrica](./alerts-overview.md), que tem vantagens significativas sobre os [alertas de métrica clássicos](./alerts-classic.overview.md) mais antigos. As métricas estão disponíveis para uma [ampla lista de serviços do Azure](../essentials/metrics-supported.md). Os alertas mais recentes dão suporte a um subconjunto (cada vez maior) de tipos de recurso. Este artigo lista esse subconjunto.

Você também pode usar alertas de métrica mais recentes em dados de log populares armazenados em um espaço de trabalho Log Analytics extraído como métricas. Para saber mais, exiba [Alertas de Métrica para Logs](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLI, suporte REST
No momento, você pode criar alertas de métrica mais recentes somente nos modelos portal do Azure, [API REST](/rest/api/monitor/metricalerts/)ou [Gerenciador de recursos](./alerts-metric-create-templates.md). Suporte para configurar os alertas mais recentes usando o PowerShell e a CLI do Azure versões 2.0 e superiores em breve.

## <a name="metrics-and-dimensions-supported"></a>Medidas e dimensões com suporte
Os alertas de métrica mais recentes fornecem suporte a alertas para métricas que usam dimensões. É possível usar dimensões para filtrar sua métrica para o nível certo. Todas as métricas compatíveis, juntamente com as dimensões aplicáveis, podem ser exploradas e visualizadas no [Azure Monitor – Metrics Explorer](../essentials/metrics-charts.md).

Aqui está a lista completa de Azure Monitor fontes métricas com suporte dos alertas mais recentes:

|Tipo de recurso  |Dimensões compatíveis |Alertas de vários recursos| Métricas disponíveis|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Sim | Não | |
|Microsoft.ApiManagement/service | Sim | Não | [Gerenciamento da API](../essentials/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft.AppConfiguration/configurationStores |Sim | Não | [Configuração de Aplicativos](../essentials/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft.AppPlatform/Spring | Sim | Não | [Azure Spring Cloud](../essentials/metrics-supported.md#microsoftappplatformspring) |
|Microsoft.Automation/automationAccounts | Sim| Não | [Contas de Automação](../essentials/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/privateClouds | Não | Não | [Solução VMware no Azure](../essentials/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.Batch/batchAccounts | Sim | Não | [Contas de Lote](../essentials/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft. BotService/botServices | Sim | Não | [Serviços de Bot](../essentials/metrics-supported.md#microsoftbotservicebotservices) |
|Microsoft.Cache/Redis | Sim | Sim | [Cache Redis do Azure](../essentials/metrics-supported.md#microsoftcacheredis) |
|Microsoft.ClassicCompute/domainNames/slots/roles | Não | Não | [Serviços de nuvem clássico](../essentials/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft.ClassicCompute/virtualMachines | Não | Não | [Máquinas virtuais clássicas](../essentials/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft.ClassicStorage/storageAccounts | Sim | Não | [Contas de armazenamento (clássicas)](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft.ClassicStorage/storageAccounts/blobServices | Sim | Não | [Contas de armazenamento (clássicas) – BLOBs](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft.ClassicStorage/storageAccounts/fileServices | Sim | Não | [Contas de armazenamento (clássicas)-arquivos](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft.ClassicStorage/storageAccounts/queueServices | Sim | Não | [Contas de armazenamento (clássicas)-filas](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft.ClassicStorage/storageAccounts/tableServices | Sim | Não | [Contas de armazenamento (clássicas)-tabelas](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft.CognitiveServices/accounts | Sim | Não | [Serviços Cognitivos](../essentials/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft. Compute/cloudservices | Sim | Não |  [Serviços de Nuvem](../essentials/metrics-supported.md#microsoftcomputecloudservices) |
|Microsoft. Compute/cloudservices/Roles | Sim | Não |  [Funções de serviço de nuvem](../essentials/metrics-supported.md#microsoftcomputecloudservicesroles) |
|Microsoft.Compute/virtualMachines | Sim | Sim<sup>1</sup> | [Máquinas virtuais](../essentials/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Sim | Não |[Conjuntos de dimensionamento de máquinas virtuais](../essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft.ContainerInstance/containerGroups | Sim| Não | [Grupos de contêineres](../essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft.ContainerRegistry/registries | Não | Não | [Registros de contêiner](../essentials/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft.ContainerService/managedClusters | Sim | Não | [Clusters gerenciados](../essentials/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Sim | Sim | [Data Box](../essentials/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft.DataFactory/datafactories| Sim| Não | [Data Factories V1](../essentials/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft.DataFactory/factories |Sim | Não | [Data Factories V2](../essentials/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/accounts | Sim | Não | [Compartilhamentos de dados](../essentials/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft.DBforMariaDB/servers | Não | Não | [DB para MariaDB](../essentials/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servers | Não | Não |[Banco de dados para MySQL](../essentials/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/flexibleServers | Sim | Não | [BD para PostgreSQL (servidores flexíveis)](../essentials/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft. DBforPostgreSQL/serverGroupsv2 | Sim | Não | BD para PostgreSQL (hiperescala) |
|Microsoft.DBforPostgreSQL/servers | Não | Não | [Banco de dados para PostgreSQL](../essentials/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.DBforPostgreSQL/serversv2 | Não | Não | [BD para PostgreSQL v2](../essentials/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft.Devices/IotHubs | Sim | Não |[Hub IoT](../essentials/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft.Devices/provisioningServices| Sim | Não | [Serviços de provisionamento de dispositivos](../essentials/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Sim | Não | [Gêmeos Digitais](../essentials/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Sim | Não | [Cosmos DB](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft.EventGrid/domains | Sim | Não | [Domínios de Grade de Eventos](../essentials/metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Sim | Não | [Tópicos do sistema de grade de eventos](../essentials/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft.EventGrid/topics |Sim | Não | [Tópicos de Grade de Eventos](../essentials/metrics-supported.md#microsofteventgridtopics) |
|Microsoft.EventHub/clusters |Sim| Não | [Clusters de hubs de eventos](../essentials/metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/namespaces |Sim| Não | [Hubs de Evento](../essentials/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft.HDInsight/clusters | Sim | Não | [Clusters HDInsight](../essentials/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft.Insights/Components | Sim | Não | [Application Insights](../essentials/metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/vaults | Sim |Sim |[Cofres](../essentials/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Kusto/Clusters | Sim |Não |[Clusters Data Explorer](../essentials/metrics-supported.md#microsoftkustoclusters)|
|Microsoft.Logic/integrationServiceEnvironments | Sim | Não |[Ambientes de serviço de integração](../essentials/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft.Logic/workflows | Não | Não |[Aplicativos Lógicos](../essentials/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces | Sim | Não | [Machine Learning](../essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft. MachineLearningServices/Workspaces/onlineEndpoints | Sim | Não | Machine Learning-pontos de extremidade |
|Microsoft. MachineLearningServices/Workspaces/onlineEndpoints/implantações | Sim | Não | Implantações de ponto de extremidade Machine Learning |
|Microsoft.Maps/accounts | Sim | Não | [Contas de mapas](../essentials/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft.Media/mediaservices | Não | Não | [Serviços de Mídia](../essentials/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft.Media/mediaservices/streamingEndpoints | Sim | Não | [Pontos de extremidade de streaming dos serviços de mídia](../essentials/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft.NetApp/netAppAccounts/capacityPools | Sim | Sim | [Pools de capacidade do Azure NetApp](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes | Sim | Sim | [Volumes do Azure NetApp](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways | Sim | Não | [Gateways de aplicativo](../essentials/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/azurefirewalls | Sim | Não | [Firewalls](../essentials/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | Não | Não | [Zonas DNS](../essentials/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Sim | Não |[Circuitos do ExpressRoute](../essentials/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/expressRoutePorts | Sim | Não |[ExpressRoute Direct](../essentials/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft.Network/loadBalancers (apenas para SKUs Standard)| Sim| Não | [Balanceadores de carga](../essentials/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| Não | Não | [Gateways NAT](../essentials/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft. Network/privateEndpoints| Não | Não | [Pontos de extremidade privados](../essentials/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft.Network/privateLinkServices| Não | Não | [Serviços de vínculo privado](../essentials/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft.Network/publicipaddresses | Não | Não | [Endereços IP Públicos](../essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Sim | Não | [Perfis do Gerenciador de Tráfego](../essentials/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Sim | Não | [Workspaces do Log Analytics](../essentials/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. emparelhamento/emparelhamentos | Sim | Não | [Emparelhamentos](../essentials/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. emparelhamento/peeringServices | Sim | Não | [Serviços de Emparelhamento](../essentials/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft.PowerBIDedicated/capacities | Não | Não | [Capacidades](../essentials/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft.Relay/namespaces | Sim | Não | [Retransmissão](../essentials/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft.Search/searchServices | Não | Não | [Serviços Search](../essentials/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | Sim | Não | [Barramento de Serviço](../essentials/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft.Sql/managedInstances | Não | Sim | [Instâncias gerenciadas do SQL](../essentials/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servers/databases | Não | Sim | [Bancos de dados SQL](../essentials/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft.Sql/servers/elasticPools | Não | Sim | [Pools elásticos do SQL](../essentials/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAccounts |Sim | Não | [Contas de Armazenamento](../essentials/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/blobServices | Sim| Não | [Contas de armazenamento-BLOBs](../essentials/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft.Storage/storageAccounts/fileServices | Sim| Não | [Contas de armazenamento-arquivos](../essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft.Storage/storageAccounts/queueServices | Sim| Não | [Contas de armazenamento-filas](../essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft.Storage/storageAccounts/tableServices | Sim| Não | [Contas de armazenamento – tabelas](../essentials/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft.StorageCache/caches | Sim | Não | [Caches HPC](../essentials/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft. StorageSync/storageSyncServices | Sim | Não | [Serviços de sincronização de armazenamento](../essentials/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft.StreamAnalytics/streamingjobs | Sim | Não | [Stream Analytics](../essentials/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft.Synapse/workspaces | Sim | Não | [Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft. Synapse/Workspaces/bigDataPools | Sim | Não | [Pools de Apache Spark do Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft. Synapse/espaços de trabalho/sqlpools | Sim | Não | [Pools de SQL do Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft. VMWareCloudSimple/virtualMachines | Sim | Não | [Máquinas Virtuais de CloudSimple](../essentials/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft.Web/hostingEnvironments/multiRolePools | Sim | Não | [Ambiente do Serviço de Aplicativo pools de várias funções](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Sim | Não | [Ambiente do Serviço de Aplicativo pools de trabalho](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Sim | Não | [Planos do serviço de aplicativo](../essentials/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Sim | Não | [Serviços de Aplicativos e Funções](../essentials/metrics-supported.md#microsoftwebsites)|
|Microsoft.Web/sites/slots | Sim | Não | [Slots do Serviço de Aplicativo](../essentials/metrics-supported.md#microsoftwebsitesslots)|

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

* Saiba mais sobre a nova [Experiência de alertas](./alerts-overview.md).
* Saiba mais sobre os [alertas de log no Azure](./alerts-unified-log.md).
* Saiba mais sobre [alertas no Azure](./alerts-overview.md).
