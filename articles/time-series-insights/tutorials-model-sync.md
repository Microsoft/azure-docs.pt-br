---
title: Sincronização de modelo entre gêmeos e Time Series Insights digitais do Azure | Microsoft Docs
description: Práticas recomendadas e ferramentas usadas para converter o modelo de ativo em ADT para o modelo de ativo no Azure TSI
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/19/2021
ms.custom: dpalled
ms.openlocfilehash: 5e8e5367daa532fa0cc36a18cd28b382e7cd8f78
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682895"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Sincronização de modelo entre os Gêmeos Digitais do Azure e o Time Series Insights Gen2

Este artigo explica as práticas recomendadas e as ferramentas usadas para converter o modelo de ativo no gêmeos digital do Azure (ADT) para o modelo de ativo em Azure Time Series Insights (TSI).  Este artigo é a segunda parte de uma série de tutoriais de duas partes explicando a integração do Azure digital gêmeos com o Azure Time Series Insights. A integração do Azure digital gêmeos com o Time Series Insights permite arquivamento e acompanhamento do histórico de telemetrias e propriedades calculadas de gêmeos digital. Esta série de tutoriais destina-se a desenvolvedores que trabalham para integrar o Time Series Insights com o Azure digital gêmeos. A parte 1 explica  [como estabelecer o pipeline de dados que traz os dados reais de série temporal do Azure digital gêmeos para Time Series insights](../digital-twins/how-to-integrate-time-series-insights.md) e esta, segunda parte da série de tutoriais explica a sincronização do modelo de ativo entre o gêmeos e o time Series insights digital do Azure. Este tutorial explica as práticas recomendadas para escolher e estabelecer a Convenção de nomenclatura para ID de série temporal (TS ID) e estabelecer hierarquias manualmente no TSM (modelo de série temporal).

## <a name="choosing-a-time-series-id"></a>Escolhendo uma ID de série temporal

ID de série temporal é um identificador exclusivo usado para identificar ativos em Time Series Insights. Os dados de série temporal (telemetrias do campo, que são pares de valor de tempo) são representados usando variáveis listadas em TSID. No Azure digital gêmeos, as propriedades de propriedade e telemetrias são usadas para representar o estado de um entrelaçamento e medidas produzidas por o '/', respectivamente. A partir do design atual do TSM, o TSIDs precisa ser exclusivo. O uso de IDs de gêmeos no gêmeos digital do Azure ou combinado com o nome da propriedade ou da telemetria sempre fará uma ID exclusiva de TS no TSM. Em um caso típico, o **_`<Twin ID>`_** será o TSID, e os nomes de propriedade e telemetria serão as variáveis no TSM. No entanto, há casos de uso em que é preferível que as hierarquias de ativos em Time Series Insights sejam achatadas usando o formato de chaves compostas, como **_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_** . Vamos usar um exemplo para explicar o caso mais tarde. Considere uma sala em uma construção modelada como um entrelaçamento e tenha a ID de Room22. Sua propriedade de configuração de temperatura deve ser convertida como **_TSID Room22_TempSetting_** e a medição de temperatura a ser convertida em **_Room22_TempMea_** no TSM.

[![Escolhendo uma ID de série temporal](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>Contextualização de série temporal

A concontexto de dados (principalmente a espacial por natureza) em Time Series Insights é obtida por meio de hierarquias de ativos e o mesmo é usado para facilitar a navegação de dados por meio de uma exibição de árvore no Gerenciador de Time Series Insights. Os tipos de série temporal e hierarquias são definidos usando o modelo de série temporal (TSM) no Time Series Insights. Os tipos na ajuda do TSM para definir variáveis, enquanto os níveis de hierarquia e os valores de campo de instância são usados para construir o modo de exibição de árvore no Gerenciador de Time Series Insights. Para obter mais informações sobre o TSM, consulte a [documentação online do time Series insights](./concepts-model-overview.md).

No Azure digital gêmeos, a conexão entre os ativos é expressa usando relações de conexão. As relações de entrelaçamento são simplesmente um grafo de ativos conectados. No entanto, no time Series Insight, as relações entre os ativos são hierárquicas por natureza. Ou seja, os ativos compartilham uma relação pai-filho tipo OD e são representados usando uma estrutura de árvore. Para converter informações de relacionamento do Azure digital gêmeos em hierarquias Time Series Insights, precisamos escolher relações hierárquicas relevantes do gêmeos digital do Azure. O Azure digital gêmeos usa uma linguagem de modelagem padrão aberta chamada DTDL (digital mydefinition Language). Nos modelos DTDL são descritos usando uma variante do JSON chamada JSON-LD. Consulte a [documentação do DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) para obter detalhes completos sobre a especificação.

[![Conexão entre ativos](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>Convertendo a representação do grafo no Azure digital gêmeos para a estrutura de árvore no Time Series Insights

As seções a seguir do tutorial capturam alguns cenários principais da tradução manual da estrutura do grafo no Azure digital gêmeos para a estrutura de árvore no Time Series Insights.

Sistema de exemplo: Este tutorial usa o exemplo a seguir para explicar os conceitos discutidos abaixo. É um sistema de gerenciamento de compilação simples e fictício com um andar e duas salas. Ele tem três termostatos conectados, um em cada uma das salas e outra comum ao andar. Além disso, ele também tem um medidor de fluxo de água medindo o fluxo de água de Room21 para Room22 por meio de uma conexão de pipe entre as salas. Observando a relação espacial entre gêmeos, ele tem os dois tipos de relações.

1. Relação mais comum e hierárquica. Por exemplo, Building40-> Floor01-> FloorTS *-> temperatura
1. Relação não tão comum, circular. Por exemplo, a partir de Building40, o FlowMtr pode ser rastreado por meio de dois caminhos diferentes.

   1. Building40-> Floor01-> Room21-> FlowMtr *-> Flow
   1. Building40-> Floor01-> Room22-> FlowMtr *-> Flow  
      Onde "Flow" é a telemetria real que mede o fluxo de água entre Room21 e Room22

> [!Note]
>
> `*` FloorTS significa FloorThermoStat e FlowMtr significa medidor de fluxo e normalmente escolhido como TSID. A temperatura e o fluxo são a telemetria bruta referenciada como variáveis no Time Series Insights.

Dada a limitação atual no Time Series Insights que um ativo não pode ser representado em várias ramificações, as seções a seguir explicam a modelagem de relações hierárquicas e circulares no Time Series Insights.

## <a name="case-1-hierarchical-parent-child-relationship"></a>Caso 1: relação hierárquica (pai-filho)

É o tipo mais comum de relação entre o gêmeos. a relação pai-filho de modelagem puro é explicada na ilustração a seguir. Os campos de instância e TSID são derivados de IDs de cópia, conforme mostrado abaixo. Embora os campos de instância possam ser atualizados manualmente usando o Time Series Insights Explorer, a seção abaixo denominada "Atualizando campos de instância usando APIs" explica a escuta de alterações de modelo no gêmeos digital do Azure e a atualização de campos de instância no Time Series Insights usando o Azure functions.

[![Mapeando IDs de entrelaçamento](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![Mapeando IDs de entrelaçamento 2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>Caso 2: relação circular

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Relação circular no Azure digital gêmeos para a relação de hierarquia única no Time Series Insights

Considerando que o TSID deve ser exclusivo e só pode ser representado em uma hierarquia, esse caso representa o _' FlowMtr '_ com uma telemetria chamada _' Flow '_ apenas sob o _' Room21 '_. No futuro, quando Time Series Insights puder dar suporte à representação múltipla de séries temporais no TSM, a telemetria _' Flow '_ seria representada na _' sala 21 '_ e na _' sala 22 '_

A captura de tela a seguir mostra o mapeamento manual de IDs de cópia no Azure digital gêmeos para o campo de instância no TSM e a hierarquia resultante em Time Series Insights.

[![Mapeando IDs de entrelaçamento no Azure digital gêmeos](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>Relação circular no Azure digital gêmeos para várias hierarquias no Time Series Insights, usando duplicatas

A parte 1 do tutorial explica como um programa cliente (uma função do Azure) ajuda a transferir dados de telemetria do Hub IoT ou outras fontes de eventos para o Azure digital gêmeos. Essa abordagem sugere o uso do mesmo programa cliente para fazer atualizações nas propriedades relevantes do gêmeos pai. No exemplo fornecido, ao ler a telemetria FlowMtr do Hub IoT e atualizar a propriedade "Flow" em FlowMtr, o programa também pode atualizar as propriedades correspondentes em todos os gêmeos pai possíveis da origem. Em nosso exemplo, seria "outflowmea" (identificado usando a propriedade "saída") de Room21 e a propriedade "inflowmea" de Room22.  A captura de tela abaixo mostra a experiência final do usuário no Time Series Insights Explorer. Deve-se observar que temos duplicatas de dados adotando essa abordagem.

[![Time Series Insights Explorer](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

O trecho de código abaixo mostra como o aplicativo cliente foi capaz de navegar na relação de entrelaçamento usando as APIs do Azure digital gêmeos.

> [!Note]
>
> Este exemplo de trecho de código pressupõe que os leitores estejam familiarizados com a [parte 01](../digital-twins/tutorial-end-to-end.md#set-up-the-sample-function-app) do tutorial e que essa alteração de código tenha sido feita dentro da função "ProcessHubToDTEvents".

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>Atualizando campos de instância usando APIs

Esta seção do tutorial explica a escuta de alterações de modelo no Azure digital gêmeos, como criação, exclusão de gêmeos ou alteração de relações entre gêmeos e atualização de campos de instância e hierarquias programaticamente usando Time Series Insights APIs de modelo. Esse método de atualização do modelo de Time Series Insights geralmente é obtido por meio do Azure functions. No gêmeos digital do Azure, as notificações de eventos, como adição ou exclusões de ou mais, podem ser roteadas para serviços downstream, como hubs de eventos que, por sua vez, podem ser alimentadas no Azure functions. Mais detalhes sobre roteamento e filtragem de eventos são explicados [aqui](../digital-twins/how-to-manage-routes-portal.md).  Restante desta seção explica como usar Time Series Insights APIs de modelo no Azure Functions para atualizar o modelo de Time Series Insights em resposta à adição de conjunto de alterações (um tipo de alteração de modelo) no Azure digital gêmeos.

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>Recebendo e identificando a notificação de eventos de adição de entrelaçamento

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>Criando Time Series Insights cliente e adicionando detalhes da instância

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>Aplicando informações de hierarquia à instância

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>Próximas etapas

A terceira parte da série de tutoriais é mostrar como consultar dados históricos do Azure digital gêmeos usando APIs de Time Series Insights. É um trabalho em andamento e a seção será atualizada quando estiver pronta. Enquanto isso, os leitores são incentivados a consultar a [documentação da API de consulta de dados Time Series insights](./concepts-query-overview.md).
