---
title: Como especificar um modelo de detecção-face
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como escolher qual modelo de detecção facial usar com seu aplicativo de face do Azure.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.custom: devx-track-csharp
ms.openlocfilehash: 5a70b10f7d22c9cc04427bdfbb44243fad457ba0
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913476"
---
# <a name="specify-a-face-detection-model"></a>Especificar um modelo de detecção facial

Este guia mostra como especificar um modelo de detecção facial para o serviço de face do Azure.

O serviço de face usa modelos de aprendizado de máquina para executar operações em faces humanas em imagens. Continuamos a melhorar a precisão de nossos modelos com base nos comentários e nos avanços dos clientes em pesquisa, e fornecemos esses aprimoramentos como atualizações de modelo. Os desenvolvedores têm a opção de especificar qual versão do modelo de detecção facial desejam usar; Eles podem escolher o modelo que melhor se adapta ao seu caso de uso.

Continue lendo para saber como especificar o modelo de detecção facial em determinadas operações de face. O serviço de face usa a detecção facial sempre que converte uma imagem de uma face em alguma outra forma de dados.

Se você não tiver certeza se deve usar o modelo mais recente, pule para a seção [avaliar modelos diferentes](#evaluate-different-models) para avaliar o novo modelo e comparar os resultados usando o conjunto de dados atual.

## <a name="prerequisites"></a>Pré-requisitos

Você deve estar familiarizado com o conceito de detecção de face de ia. Se não estiver, consulte o guia conceitual de detecção facial ou guia de instruções:

* [Conceitos de detecção facial](../concepts/face-detection.md)
* [Como detectar rostos em uma imagem](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detectar rostos com o modelo especificado

A detecção facial localiza os locais de caixa delimitadora de faces humanas e identifica seus pontos de referência visuais. Ele extrai os recursos da face e os armazena para uso posterior em operações de [reconhecimento](../concepts/face-recognition.md) .

Ao usar a API de [detecção de face] , você pode atribuir a versão do modelo com o `detectionModel` parâmetro. Os valores disponíveis são:

* `detection_01`
* `detection_02`

Uma URL de solicitação para a API REST de [detecção facial] terá a seguinte aparência:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Se você estiver usando a biblioteca de cliente, poderá atribuir o valor para `detectionModel` passando uma cadeia de caracteres apropriada. Se você deixá-lo sem atribuição, a API usará a versão de modelo padrão ( `detection_01` ). Consulte o exemplo de código a seguir para a biblioteca de cliente .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_03", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Adicionar face à pessoa com o modelo especificado

O serviço de face pode extrair dados de face de uma imagem e associá-los a um objeto **Person** por meio da API de [face da pessoa-adicionar facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) . Nesta chamada à API, você pode especificar o modelo de detecção da mesma maneira que na [detecção de face].

Consulte o exemplo de código a seguir para a biblioteca de cliente .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_03");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Esse código cria um **Person** com ID `mypersongroupid` e adiciona uma **pessoa** a ele. Em seguida, ele adiciona uma face a essa **pessoa** usando o `detection_02` modelo. Se você não especificar o parâmetro *detectionModel* , a API usará o modelo padrão, `detection_01` .

> [!NOTE]
> Você não precisa usar o mesmo modelo de detecção para todas as faces em um objeto **Person** e não precisa usar o mesmo modelo de detecção ao detectar novas faces para comparar com um objeto **Person** (na API de [identificação facial] , por exemplo).

## <a name="add-face-to-facelist-with-specified-model"></a>Adicionar face à Facelist com o modelo especificado

Você também pode especificar um modelo de detecção ao adicionar uma face a um objeto de **facelist** existente. Consulte o exemplo de código a seguir para a biblioteca de cliente .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_03");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Esse código cria uma **facelist** chamada `My face collection` e adiciona uma face a ela com o `detection_02` modelo. Se você não especificar o parâmetro *detectionModel* , a API usará o modelo padrão, `detection_01` .

> [!NOTE]
> Você não precisa usar o mesmo modelo de detecção para todas as faces em um objeto de **facelist** e não precisa usar o mesmo modelo de detecção ao detectar novas faces para comparar com um objeto de **facelist** .

## <a name="evaluate-different-models"></a>Avaliar modelos diferentes

Os diferentes modelos de detecção facial são otimizados para tarefas diferentes. Consulte a tabela a seguir para obter uma visão geral das diferenças.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Opção padrão para todas as operações de detecção de face. | Lançado em maio de 2019 e disponível opcionalmente em todas as operações de detecção de face.
|Não otimizado para faces pequenas, rápidas ou borradas.  | Precisão aprimorada em faces pequenas, de exibição lateral e borradas. |
|Retorna os atributos de face (pose de cabeçalho, idade, emoção e assim por diante) se eles forem especificados na chamada de detecção. |  Não retorna atributos de face.     |
|Retorna os pontos de referência de face se eles forem especificados na chamada de detecção.   | Não retorna pontos de referência de face.  |

A melhor maneira de comparar o desempenho dos `detection_01` modelos e `detection_02` é usá-los em um conjunto de exemplo. É recomendável chamar a API de [detecção facial] em uma variedade de imagens, especialmente imagens de muitas faces ou de faces que são difíceis de ver, usando cada modelo de detecção. Preste atenção ao número de faces que cada modelo retorna.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a especificar o modelo de detecção a ser usado com diferentes APIs de face. Em seguida, siga um guia de início rápido para começar a usar a detecção facial.

* [SDK do .NET facial](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [SDK facial do Python](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [SDK do facial go](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[Face – Detectar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face – Identificar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc