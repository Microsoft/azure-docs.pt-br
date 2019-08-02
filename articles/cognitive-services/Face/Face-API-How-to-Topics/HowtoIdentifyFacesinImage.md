---
title: 'Exemplo: Identificar faces em imagens – API de Detecção Facial'
titleSuffix: Azure Cognitive Services
description: Use a API de Detecção Facial para identificar faces em imagens.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 5806c17b0532f4d18b7ac57fbf70c92ed9d47daa
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827507"
---
# <a name="example-identify-faces-in-images"></a>Exemplo: Identificar faces em imagens

Este guia demonstra como identificar faces desconhecidas, usando objetos PersonGroup que são criados com base em pessoas conhecidas anteriormente. Os exemplos são gravados em C#, usando a biblioteca de clientes da API de Detecção Facial dos Serviços Cognitivos do Azure.

## <a name="preparation"></a>Preparação

Este exemplo demonstra:

- Como criar um PersonGroup. Esse PersonGroup contém uma lista de pessoas conhecidas.
- Como atribuir faces a cada pessoa. Essas faces são usadas como uma linha de base para identificar pessoas. É recomendável que você use exibições frontais e claras das faces. Um exemplo seria uma ID de foto. Um bom conjunto de fotos inclui faces da mesma pessoa em poses, cores de roupa ou estilos de cabelo diferentes.

Para executar a demonstração deste exemplo, prepare:

- Algumas fotos com a face da pessoa. [Baixar fotos de exemplo](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) para Ana, Guilherme e Clara.
- Uma série de fotos de teste. As fotos podem ou não conter as faces de Ana, Guilherme ou Clara. Elas serão usadas para testar a identificação. Escolha também algumas imagens de exemplo do link anterior.

## <a name="step-1-authorize-the-api-call"></a>Etapa 1: Autorizar a chamada à API

Cada chamada à API de Detecção Facial exige uma chave de assinatura. Essa chave pode ser passada por um parâmetro de cadeia de caracteres de consulta ou especificada no cabeçalho da solicitação. Para passar a chave de assinatura por meio da cadeia de caracteres de consulta, confira a URL de solicitação para [Face – Detecção](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) como um exemplo:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Como alternativa, especifique a chave de assinatura no cabeçalho da solicitação HTTP **ocp-apim-subscription-key: &lt;Chave de Assinatura&gt;** .
Quando você usar uma biblioteca de clientes, a chave de assinatura será passada através do construtor da classe FaceClient. Por exemplo:
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
Para obter a chave de assinatura, acesse o Azure Marketplace no portal do Azure. Para saber mais, confira [Assinaturas](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Etapa 2: Criar o PersonGroup

Nesta etapa, um PersonGroup denominado "MyFriends" contém Ana, Guilherme e Clara. Cada pessoa tem várias faces registradas. As faces devem ser detectadas entre as imagens. Depois de todas essas etapas, você tem um PersonGroup como o da imagem a seguir:

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Etapa 2.1: Definir pessoas para o PersonGroup
Uma pessoa é uma unidade básica de identificação. Uma pessoa pode ter uma ou mais faces conhecidas registradas. Um PersonGroup é uma coleção de pessoas. Cada pessoa é definida dentro de um determinado PersonGroup. A identificação é feita com relação a um PersonGroup. A tarefa é criar um PersonGroup e, em seguida, criar as pessoas nele, como Ana, Guilherme e Clara.

Primeiro, crie um novo PersonGroup, usando a API [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244). A API da biblioteca de clientes correspondente é o método CreatePersonGroupAsync para a classe FaceClient. A ID do grupo que é especificada para criar o grupo é exclusiva para cada assinatura. Você também pode obter, atualizar ou excluir PersonGroups, usando outras APIs PersonGroup. 

Após um grupo ser definido, você poderá definir as pessoas dentro delem, usando a API [Pessoa do PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c). O método da biblioteca de clientes é CreatePersonAsync. Você pode adicionar uma face a cada pessoa depois que elas tiverem sido criadas.

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceClient.PersonGroupPerson.CreateAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a> Etapa 2.2: Detectar faces e registrá-las para a pessoa correta
A detecção é feita enviando uma solicitação da Web "POST" para A API [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) com o arquivo de imagem no corpo da solicitação HTTP. Quando você usar a biblioteca de clientes, a detecção facial será feita pelo método DetectAsync para a classe FaceClient.

Para cada face detectada, chame [Pessoa do PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) para adicioná-la à pessoa correta.

O código a seguir demonstra o processo de como detectar uma face de uma imagem e adicioná-la a uma pessoa:

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Se a imagem tiver mais de uma face, apenas a face maior será adicionada. Você pode adicionar outras faces à pessoa. Passar uma cadeia de caracteres no formato "targetFace = esquerda, superior, largura, altura" para o parâmetro de consulta da API [Pessoa do PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b). Você também pode usar o parâmetro opcional targetFace no método AddPersonFaceAsync para adicionar outras faces. Cada face adicionada à pessoa receberá uma ID facial persistente exclusiva. Você pode usar essa ID em [Pessoa do PersonGroup – Excluir Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) e [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Etapa 3: Treinar o PersonGroup

O PersonGroup deve ser treinado antes que se possa utilizá-lo para realizar a identificação. O PersonGroup deve ser treinado novamente após uma pessoa ser adicionada ou removida, ou se você editar a face de uma pessoa registrada. O treinamento é feito com A API [PersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Quando você usa a biblioteca de clientes, isso é uma chamada ao método TrainPersonGroupAsync:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
O treinamento é um processo assíncrono. Ele poderá não estar concluído mesmo depois que o método TrainPersonGroupAsync tiver retornado. Talvez seja necessário consultar o status do treinamento. Use a API [PersonGroup – Obter Status do Treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) ou o método GetPersonGroupTrainingStatusAsync da biblioteca de clientes. O código a seguir demonstra uma lógica simples de esperar o fim do treinamento do PersonGroup:
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != TrainingStatusType.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Etapa 4: Identificar um rosto com relação a um PersonGroup definido

Quando a API de Detecção Facial executa identificações, ela calcula a semelhança de uma face de teste entre todas as faces em um grupo. Ela retorna a pessoa mais comparável à face de teste. Esse processo é feito por meio da API [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) ou pelo método IdentifyAsync da biblioteca de clientes.

A face de teste deve ser detectada por meio das etapas anteriores. Em seguida, a ID facial é passada para a API de identificação como um segundo argumento. Várias IDs faciais podem ser identificadas ao mesmo tempo. O resultado contém todos os resultados identificados. Por padrão, o processo de identificação retornará somente uma pessoa que melhor corresponda à face de teste. Se preferir, especifique o parâmetro opcional maxNumOfCandidatesReturned para permitir que o processo de identificação retorne mais candidatos.

O código a seguir demonstra o processo de identificação:

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectAsync(s);
    var faceIds = faces.Select(face => face.FaceId).ToArray();
 
    var results = await faceClient.Face.IdentifyAsync(faceIds, personGroupId);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceClient.PersonGroupPerson.GetAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Depois de concluir as etapas, tente identificar faces diferentes. Veja se as faces de Ana, Guilherme ou Clara podem ser identificadas corretamente, de acordo com as imagens carregadas para detecção facial. Veja os exemplos a seguir:

![Identificar faces diferentes](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Etapa 5: Solicitar grande escala

Um PersonGroup pode conter até 10 mil pessoas com base na limitação de design anterior.
Para obter mais informações sobre cenários em escala de até milhões, veja [Como usar o recurso de larga escala](how-to-use-large-scale.md).

## <a name="summary"></a>Resumo

Neste guia, você aprendeu o processo de criar um PersonGroup e identificar uma pessoa. Os seguintes recursos foram explicados e demonstrados:

- Detecte faces usando a API [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d).
- Crie PersonGroups usando a API [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244).
- Crie pessoas usando a API [Pessoa do PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c).
- Treine um PersonGroup usando a API [PersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).
- Identifique faces desconhecidas no PersonGroup usando a API [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="related-topics"></a>Tópicos relacionados

- [Conceitos de reconhecimento facial](../concepts/face-recognition.md)
- [Detectar faces em uma imagem](HowtoDetectFacesinImage.md)
- [Adicionar faces](how-to-add-faces.md)
- [Usar o recurso de larga escala](how-to-use-large-scale.md)
