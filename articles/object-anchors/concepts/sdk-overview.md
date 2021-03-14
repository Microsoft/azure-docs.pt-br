---
title: Visão geral do SDK do Runtime
description: Familiarize-se com o SDK de tempo de execução de âncoras de objeto.
author: craigktreasure
manager: vriveras
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: 74663f05c5ff995a090c7cd35e4edf46a754da17
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034601"
---
# <a name="runtime-sdk-overview"></a>Visão geral do SDK do Runtime

Esta seção fornece uma visão geral de alto nível do SDK de tempo de execução de âncoras de objeto, que é usado para detectar objetos usando um modelo de âncoras de objeto. Você obterá uma compreensão de como um objeto é representado e de quais são os diversos componentes usados.

Todos os tipos descritos abaixo podem ser encontrados no namespace **Microsoft. MixedReality. ancoras** .

## <a name="types"></a>Tipos

### <a name="objectmodel"></a>ObjectModel

Um [ObjectModel](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectmodel) representa a geometria de um objeto físico e codifica os parâmetros necessários para detecção e estimativa de pose. Ele deve ser criado usando o [serviço âncoras de objeto](../quickstarts/get-started-model-conversion.md). Em seguida, um aplicativo pode carregar o arquivo de modelo gerado usando a API de âncoras de objeto e consultar a malha inserida nesse modelo para visualização.

### <a name="objectsearcharea"></a>ObjectSearchArea

Um [ObjectSearchArea](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectsearcharea) especifica o espaço para procurar um ou vários objetos. Ele é definido por uma ID de nó de grafo espacial e limites espaciais no sistema de coordenadas representado pela ID do nó do grafo espacial. O SDK de tempo de execução de âncoras de objeto dá suporte a quatro tipos de limites, ou seja, **campo de exibição**, **caixa delimitadora**, **esfera** e um **local**.

### <a name="objectquery"></a>ObjectQuery

Um [ObjectQuery](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery) informa a um **objeto** que indica como localizar objetos de um determinado modelo. Ele fornece os seguintes parâmetros ajustáveis, cujos valores padrão podem ser recuperados de um modelo de objeto.

#### <a name="minsurfacecoverage"></a>MinSurfaceCoverage

A propriedade [MinSurfaceCoverage](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage) indica o valor a considerar uma instância como detectada.

Para cada candidato de objeto, um **observador** computa a proporção de superfícies sobrepostas entre o modelo de objeto transformado e a cena e, em seguida, ele relatará o candidato ao aplicativo somente quando a taxa de cobertura estiver acima de um determinado limite.

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

A propriedade [IsExpectedToBeStandingOnGroundPlane](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane) indica se espera-se que o objeto de destino fique no plano de chão.

Um plano de chão é o menor piso horizontal na área de pesquisa. Ele fornece uma boa restrição sobre as representações de objeto possíveis. Ativar esse sinalizador orientará o **observador** para estimar a pose em um espaço limitado e poderá melhorar a precisão. Esse parâmetro será ignorado se o modelo não for esperado para o plano de chão.

#### <a name="expectedmaxverticalorientationindegrees"></a>ExpectedMaxVerticalOrientationInDegrees

A propriedade [ExpectedMaxVerticalOrientationInDegrees](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees) indica o ângulo máximo esperado em graus entre a direção de cima de uma instância de objeto e gravidade.

Esse parâmetro fornece outra restrição na direção para cima de uma pose estimada. Por exemplo, se um objeto estiver correto, esse parâmetro poderá ser 0. As âncoras de objeto não devem detectar objetos que são diferentes do modelo. Se um modelo estiver ativo, então ele não detectará uma instância disposta lado à direita. Um novo modelo seria usado para layout de lado abaixo. A mesma regra se aplica a Articulation.

#### <a name="maxscalechange"></a>MaxScaleChange

A propriedade [MaxScaleChange](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange) indica a alteração máxima de escala do objeto (dentro de 0 ~ 1) em relação ao mapeamento espacial. A escala estimada é aplicada aos vértices de objetos transformados centralizados na origem e alinhada ao eixo. Escalas estimadas não podem ser a escala real entre um modelo CAD e sua representação física, mas alguns valores que permitem que o aplicativo processe um modelo de objeto próximo ao mapeamento espacial no objeto físico.

#### <a name="searchareas"></a>SearchAreas

A propriedade [SearchAreas](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas) indica uma matriz de limites espaciais onde encontrar objeto (s).

O **observador** procurará objetos no espaço de União de todas as áreas de pesquisa especificadas em uma consulta. Nesta versão, retornaremos no máximo um objeto com maior confiança para reduzir a latência.

### <a name="objectinstance"></a>ObjectInstance

Um [ObjectInstance](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectinstance) representa uma posição hipotética em que uma instância de um determinado modelo pode estar no sistema de coordenadas do HoloLens. Cada instância vem com uma `SurfaceCoverage` propriedade para indicar a qualidade da pose estimada.

Uma instância é criada por meio do `ObjectObserver.DetectAsync` método de chamada e, em seguida, atualizada automaticamente em segundo plano quando ativa. Um aplicativo pode escutar o evento de estado alterado em uma determinada instância ou alterar o modo de controle para pausar/retomar a atualização. Uma instância será automaticamente removida do **observador** quando o rastreamento for perdido.

### <a name="objectobserver"></a>Objectobservador

Um [Objectobservador](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectobserver) carrega modelos de objeto, detecta suas instâncias e relata DOF de cada instância no sistema de coordenadas do HoloLens.

Embora qualquer instância ou modelo de objeto seja criado a partir de um **observador**, seus tempos de vida são independentes. Um aplicativo pode descartar um observador e continuar a usar o modelo ou instância de objeto.

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

O [ObjectDiagnosticSession](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession) registra o diagnóstico e grava dados em um arquivo morto.

Um arquivo de diagnóstico inclui a nuvem do ponto de cena, o status do observador e as informações sobre os modelos. Essas informações são úteis para identificar possíveis problemas de tempo de execução. Consulte mais informações em [Perguntas Frequentes](../faq.md).

## <a name="runtime-sdk-usage-and-details"></a>Uso e detalhes do SDK de tempo de execução

Esta seção deve fornecer as noções básicas de como usar o SDK de tempo de execução. Ele deve fornecer contexto suficiente para navegar pelos aplicativos de exemplo para ver como as âncoras de objeto são usadas de forma holística.

### <a name="initialization"></a>Inicialização

Os aplicativos precisam chamar a `ObjectObserver.IsSupported()` API para determinar se há suporte para âncoras de objeto no dispositivo antes de usá-lo. Se a `ObjectObserver.IsSupported()` API retornar `false` , verifique se o aplicativo habilitou o recurso **spatialPerception** e/ou a atualização para o so de HoloLens mais recente.

```cs
if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
var status = await ObjectObserver.RequestAccessAsync();
if(status != ObjectObserverStatus.Allowed)
{
    // Handle the error
}
```

Em seguida, o aplicativo cria um observador de objeto e carrega os modelos necessários gerados pelo [serviço de conversão de modelo de âncoras de objeto](../quickstarts/get-started-model-conversion.md).

```cs
var observer = new ObjectObserver();

byte[] modelAsBytes; // Load a model into a byte array. Model could be a file, an embedded resource, or a network stream.
var model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed into a centered coordinate system for the 
// ease of computing the object pose. The rigid transform can be retrieved through the `OriginToCenterTransform` property.
```

O aplicativo cria uma consulta para detectar instâncias desse modelo dentro de um espaço.

```cs
var coordinateSystem = default(SpatialGraphCoordinateSystem);
var searchAreaAsBox = ObjectSearchArea.FromOrientedBox(coordinateSystem, default(SpatialOrientedBox));

// Optionally change the parameters, otherwise use the default values embedded in the model.
var query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchAreaAsBox);

// Detection could take long, run in a background thread.
var detectedObjects = await observer.DetectAsync(query);
```

Por padrão, cada instância detectada será rastreada automaticamente pelo **observador**. Opcionalmente, podemos manipular essas instâncias alterando seu modo de controle ou ouvindo seu evento de alteração de estado.

```cs
foreach (var instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition" - consumes less CPU cycles thus fast to update the state.
    // "HighLatencyAccuratePosition" - (not yet implemented) consumes more CPU cycles thus potentially taking longer time to update the state.
    // "Paused" - stops to update the state until mode changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    //instance.Dispose();
}
```

No evento de estado alterado, podemos consultar o estado mais recente ou descartar uma instância se ela perdeu o rastreamento.

```cs
var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    var state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in the same order
        // to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance and dispose it.
        sender.Dispose();
    }
});
```

Além disso, um aplicativo pode, opcionalmente, gravar uma ou várias sessões de diagnóstico para depuração offline.

```cs
string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
var diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

Por fim, liberamos os recursos descartando todos os objetos.

```cs
foreach(var instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```
