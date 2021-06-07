---
title: Como migrar os dados de detecção facial entre assinaturas – Detecção Facial
titleSuffix: Azure Cognitive Services
description: Este guia mostra como migrar os dados de detecção facial armazenados de uma assinatura de Detecção Facial para outra.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: nitinme
ms.custom: devx-track-csharp
ms.openlocfilehash: ea2a127ffbf966b024322ab5a908466dec1cc8a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935627"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrar seus dados de detecção facial para uma assinatura diferente de Detecção Facial

Este guia mostra como mover dados de detecção facial, como um objeto PersonGroup salvo com rostos, para uma assinatura distinta de Detecção Facial dos Serviços Cognitivos do Azure. Para mover os dados, use o recurso de Instantâneo. Dessa forma, você evita ter que criar e treinar repetidamente um objeto PersonGroup ou FaceList ao mover ou expandir as operações. Por exemplo, talvez você tenha criado um objeto PersonGroup com uma assinatura gratuita e agora deseja migrá-lo para a assinatura paga. Ou talvez seja necessário sincronizar os dados de detecção facial entre assinaturas em regiões distintas para uma grande operação empresarial.

Essa mesma estratégia de migração também se aplica aos objetos LargePersonGroup e LargeFaceList. Se você não tiver familiaridade com os conceitos deste guia, confira as definições no guia [Conceitos de reconhecimento facial](../concepts/face-recognition.md). Esse guia usa a biblioteca de cliente .NET da Detecção Facial com C#.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará dos seguintes itens:

- Duas chaves de assinatura de Detecção Facial, uma com os dados existentes e uma para a qual migrar. Para assinar o serviço de Detecção Facial e obter a chave, siga as instruções em [Criar uma conta de Serviços Cognitivos](../../cognitive-services-apis-create-account.md).
- A cadeia de ID da assinatura de Detecção Facial que corresponde à assinatura de destino. Para encontrá-la, selecione **Visão Geral** no portal do Azure. 
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

Este guia usa um aplicativo de console simples para executar a migração de dados de detecção facial. Para uma implementação completa, confira [Exemplo de instantâneo de Detecção Facial](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) no GitHub.

1. No Visual Studio, crie um projeto de aplicativo de Console .NET Framework. Nomeie-o como **FaceApiSnapshotSample**.
1. Obtenha os pacotes necessários do NuGet. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet**. Selecione a guia **Navegar** e, em seguida, selecione **Incluir pré-lançamento**. Localize e instale o seguinte pacote:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Criar clientes de detecção facial

No método **Main** em *Program.cs*, crie duas instâncias [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) para suas assinaturas de origem e de destino. Este exemplo usa uma assinatura de Detecção Facial na região do Leste da Ásia como a origem e uma assinatura do Oeste dos EUA como o destino. Ele demonstra como migrar dados de uma região do Azure para outra. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

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

Preencha os valores da chave de assinatura e as URLs do ponto de extremidade para as assinaturas de origem e de destino.


## <a name="prepare-a-persongroup-for-migration"></a>Preparar um PersonGroup para migração

Você precisa da ID do PersonGroup na sua assinatura de origem para migrá-la para a assinatura de destino. Use o método [PersonGroupOperationsExtensions.ListAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync) para recuperar uma lista dos objetos PersonGroup. Em seguida, obtenha a propriedade [PersonGroup.PersonGroupId](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId). Este processo é diferente com base nos objetos PersonGroup que você tem. Neste guia, a ID PersonGroup de origem é armazenada em `personGroupId`.

> [!NOTE]
> O [código de exemplo](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) cria e treina um novo PersonGroup para a migração. Na maioria dos casos, você já deve ter um PersonGroup para usar.

## <a name="take-a-snapshot-of-a-persongroup"></a>Tirar um instantâneo de um PersonGroup

Um instantâneo é um armazenamento remoto temporário para determinados tipos de dados de Detecção Facial. Ele funciona como um tipo de área de transferência para copiar dados de uma assinatura para outra. Primeiro, você tira um instantâneo dos dados na assinatura de origem. Em seguida, aplique-o a um novo objeto de dados na assinatura de destino.

Use a instância FaceClient da assinatura de origem para tirar um instantâneo do PersonGroup. Use [TakeAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync) com a ID do PersonGroup e a ID da assinatura de destino. Se você tiver várias assinaturas de destino, adicione-as como entradas matriz no terceiro parâmetro.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> O processo de obter e aplicar instantâneos não interrompe chamadas regulares para PersonGroups ou FaceLists de origem ou de destino. Não faça chamadas simultâneas que alterem o objeto de origem, como [chamadas de gerenciamento de FaceList](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations) ou a chamada de [Treinamento do PersonGroup](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations), por exemplo. A operação de instantâneo poderá ser executada antes ou depois dessas operações ou poderá encontrar erros.

## <a name="retrieve-the-snapshot-id"></a>Recuperar a ID do instantâneo

O método usado para tirar instantâneos é assíncrono. Portanto, você deve aguardar a conclusão. As operações de instantâneo não podem ser canceladas. Nesse código, o método `WaitForOperation` monitora a chamada assíncrona. Ele verifica o status a cada 100 ms. Após a conclusão da operação, recupere uma ID de operação analisando o campo `OperationLocation`. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Um valor `OperationLocation` típico se parece com este:

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

Depois que o status da operação mostrar `Succeeded`, obtenha a ID do instantâneo analisando o campo `ResourceLocation` da instância de OperationStatus retornada.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Um valor `resourceLocation` típico se parece com este:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Aplicar um instantâneo em uma assinatura de destino

Em seguida, crie o PersonGroup na assinatura de destino, usando uma ID gerada aleatoriamente. Depois, use a instância FaceClient da assinatura de destino para aplicar o instantâneo a este PersonGroup. Forneça a ID do instantâneo e a nova ID de PersonGroup.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Um objeto de instantâneo é válido apenas por 48 horas. Tire um instantâneo somente se você pretende usá-lo para a migração de dados logo em seguida.

Uma solicitação de aplicação de instantâneo retorna outra ID de operação. Para obter essa ID, analise o campo `OperationLocation` da instância retornada applySnapshotResult. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

O processo do aplicativo de instantâneo também é assíncrono. Portanto, use o `WaitForOperation` novamente para aguardar a conclusão.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testar a migração de dados

Depois de aplicar o instantâneo, o novo PersonGroup na assinatura de destino é preenchido com os dados de detecção facial originais. Por padrão, os resultados de treinamento também são copiados. O novo PersonGroup está pronto para chamadas de identificação facial sem precisar de novo treinamento.

Para testar a migração de dados, você pode executar as seguintes operações e comparar os resultados que elas imprimem ao console:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the PersonGroup before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Use os seguintes métodos auxiliares:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("PersonGroup:");
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

Agora você pode usar o novo PersonGroup na assinatura de destino. 

Para atualizar o PersonGroup de destino novamente no futuro, crie um PersonGroup para receber o instantâneo. Para isso, siga as etapas deste guia. Um único objeto PersonGroup só pode ter um instantâneo aplicado a ele uma vez.

## <a name="clean-up-resources"></a>Limpar os recursos

Após terminar de migrar os dados de detecção facial, exclua manualmente o objeto de instantâneo.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Próximas etapas

Em seguida, consulte a documentação de referência de API relevante, explore um aplicativo de exemplo que usa o recurso de Instantâneo ou siga um guia de instruções para começar a usar as outras operações de API mencionadas aqui:

- [Documentação de referência do instantâneo (SDK do .NET)](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations)
- [Exemplo de instantâneo de detecção facial](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Adicionar faces](how-to-add-faces.md)
- [Detectar faces em uma imagem](HowtoDetectFacesinImage.md)
