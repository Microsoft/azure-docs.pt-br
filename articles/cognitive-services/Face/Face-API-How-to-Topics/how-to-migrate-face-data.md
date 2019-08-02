---
title: Como migrar seus dados de detecção facial entre assinaturas — API de Detecção Facial
titleSuffix: Azure Cognitive Services
description: Este guia mostra como migrar seus dados de detecção facial armazenados de uma assinatura de API de Detecção Facial para outra.
services: cognitive-services
author: lewlu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: lewlu
ms.openlocfilehash: 886e0ff353ab270bb823629d2068508531c14fc2
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516854"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrar seus dados de detecção facial para uma assinatura diferente de Detecção Facial

Este guia mostra como mover dados de face, como um objeto de um usuário salvo com faces, para um serviço de cognitiva do Azure diferente API de Detecção Facial assinatura. Para mover os dados, use o recurso de instantâneo. Dessa forma, você evita ter que criar e treinar repetidamente um objeto Person ou Facelist ao mover ou expandir suas operações. Por exemplo, talvez você tenha criado um objeto Person usando uma assinatura de avaliação gratuita e agora deseja migrá-lo para sua assinatura paga. Ou talvez seja necessário sincronizar dados de face entre regiões para uma grande operação empresarial.

Essa mesma estratégia de migração também se aplica aos objetos LargePersonGroup e LargeFaceList. Se você não estiver familiarizado com os conceitos deste guia, consulte suas definições no guia de [conceitos de reconhecimento facial](../concepts/face-recognition.md) . Este guia usa a biblioteca cliente .NET da API de Detecção Facial com C#.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes itens:

- Duas chaves de assinatura API de Detecção Facial, uma com os dados existentes e uma para migrar para o. Para assinar o serviço de API de Detecção Facial e obter sua chave, siga as instruções em [criar uma conta de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- A cadeia de caracteres de ID de assinatura API de Detecção Facial que corresponde à assinatura de destino. Para encontrá-lo, selecione **visão geral** na portal do Azure. 
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

Este guia usa um aplicativo de console simples para executar a migração de dados de face. Para obter uma implementação completa, consulte o [exemplo de instantâneo API de detecção facial](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) no github.

1. No Visual Studio, crie um novo aplicativo de console .NET Framework projeto. Nomeie-o como **FaceApiSnapshotSample**.
1. Obtenha os pacotes necessários do NuGet. Clique com o botão direito do mouse no seu projeto no Gerenciador de Soluções e selecione **gerenciar pacotes NuGet**. Selecione a guia **procurar** e selecione **incluir pré-lançamento**. Localize e instale o seguinte pacote:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Criar clientes de detecção facial

No método **principal** no *Program.cs*, crie duas instâncias [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) para suas assinaturas de origem e de destino. Este exemplo usa uma assinatura facial na região de Ásia Oriental como a origem e uma assinatura oeste dos EUA como o destino. Este exemplo demonstra como migrar dados de uma região do Azure para outra. Se suas assinaturas estiverem em regiões diferentes, altere as cadeias `Endpoint` de caracteres.

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Preencha os valores de chave de assinatura e as URLs de ponto de extremidade para suas assinaturas de origem e de destino.


## <a name="prepare-a-persongroup-for-migration"></a>Preparar um PersonGroup para migração

Você precisa da ID da pessoa em sua assinatura de origem para migrá-la para a assinatura de destino. Use o método [PersonGroupOperationsExtensions. ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) para recuperar uma lista de seus objetos Person. Em seguida, obtenha a propriedade [Person. PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) . Esse processo é diferente com base em quais objetos do Person você tem. Neste guia, a ID da pessoa de origem é armazenada `personGroupId`em.

> [!NOTE]
> O [código de exemplo](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) cria e treina um novo Person para migrar. Na maioria dos casos, você já deve ter um Person para usar.

## <a name="take-a-snapshot-of-a-persongroup"></a>Tirar um instantâneo de um Person

Um instantâneo é um armazenamento remoto temporário para determinados tipos de dados de face. Ele funciona como um tipo de área de transferência para copiar dados de uma assinatura para outra. Primeiro, você tira um instantâneo dos dados na assinatura de origem. Em seguida, aplique-o a um novo objeto de dados na assinatura de destino.

Use a instância FaceClient da assinatura de origem para tirar um instantâneo da pessoa. Use [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) com a ID de Person e a ID da assinatura de destino. Se você tiver várias assinaturas de destino, adicione-as como entradas de matriz no terceiro parâmetro.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> O processo de fazer e aplicar instantâneos não interrompe nenhuma chamada regular para a origem ou o destino PersonGroups ou FaceLists. Não faça chamadas simultâneas que alterem o objeto de origem, como [chamadas de gerenciamento](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) de facelist ou a chamada de [treinamento de pessoa](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) , por exemplo. A operação de instantâneo pode ser executada antes ou depois dessas operações ou pode encontrar erros.

## <a name="retrieve-the-snapshot-id"></a>Recuperar a ID do instantâneo

O método usado para tirar instantâneos é assíncrono, portanto, você deve aguardar sua conclusão. As operações de instantâneo não podem ser canceladas. Nesse código, o `WaitForOperation` método monitora a chamada assíncrona. Ele verifica o status a cada 100 ms. Após a conclusão da operação, recupere uma ID de operação analisando `OperationLocation` o campo. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Um valor `OperationLocation` típico tem esta aparência:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

O método auxiliar `WaitForOperation` está aqui:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

Depois que o status da `Succeeded`operação for exibido, obtenha a ID do instantâneo `ResourceLocation` analisando o campo da instância OperationStatus retornada.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Um valor `resourceLocation` típico tem esta aparência:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Aplicar um instantâneo a uma assinatura de destino

Em seguida, crie o novo Person na assinatura de destino usando uma ID gerada aleatoriamente. Em seguida, use a instância FaceClient da assinatura de destino para aplicar o instantâneo a esse Person. Transmita a ID do instantâneo e a nova ID de Person.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Um objeto de instantâneo é válido por apenas 48 horas. Apenas faça um instantâneo se você pretende usá-lo para a migração de dados logo após.

Uma solicitação de aplicação de instantâneo retorna outra ID de operação. Para obter essa ID, analise o `OperationLocation` campo da instância applySnapshotResult retornada. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

O processo do aplicativo de instantâneo também é assíncrono, portanto `WaitForOperation` , novamente use para aguardar sua conclusão.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testar a migração de dados

Depois de aplicar o instantâneo, o novo usuário na assinatura de destino é preenchido com os dados de face originais. Por padrão, os resultados de treinamento também são copiados. O novo Person está pronto para chamadas de identificação facial sem precisar de novo treinamento.

Para testar a migração de dados, execute as seguintes operações e compare os resultados que eles imprimem no console:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Use os seguintes métodos auxiliares:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

Agora você pode usar o novo usuário na assinatura de destino. 

Para atualizar o usuário de destino novamente no futuro, crie um novo Person para receber o instantâneo. Para fazer isso, siga as etapas neste guia. Um único objeto Person pode ter um instantâneo aplicado a ele apenas uma vez.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir a migração de dados de face, exclua manualmente o objeto de instantâneo.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Próximas etapas

Em seguida, consulte a documentação de referência de API relevante, explore um aplicativo de exemplo que usa o recurso de instantâneo ou siga um guia de instruções para começar a usar as outras operações de API mencionadas aqui:

- [Documentação de referência do instantâneo (SDK do .NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Exemplo de instantâneo API de Detecção Facial](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Adicionar faces](how-to-add-faces.md)
- [Detectar faces em uma imagem](HowtoDetectFacesinImage.md)
- [Identificar faces em imagens](HowtoIdentifyFacesinImage.md)
