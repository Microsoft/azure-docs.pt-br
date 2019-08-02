---
title: Como especificar um modelo de detecção – API de detecção facial
titleSuffix: Azure Cognitive Services
description: Este artigo mostra como escolher qual modelo de detecção de face para usar com o aplicativo de API de detecção facial do Azure.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 26ab3cb247309aa21791ca5a984f39ef40ce9a78
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249628"
---
# <a name="specify-a-face-detection-model"></a>Especificar um modelo de detecção facial

Este guia mostra como especificar um modelo de detecção de face para a API de detecção facial do Azure.

API de detecção facial usa modelos de aprendizado de máquina para executar operações em rostos humanos em imagens. Continuamos a melhorar a precisão dos nossos modelos com base nos comentários dos clientes e avanços em pesquisas e fornecemos essas melhorias, como atualizações de modelo. Os desenvolvedores têm a opção de especificar qual versão do modelo de detecção facial eles gostariam de usar; eles podem escolher o modelo que melhor se adapta a seu caso de uso.

Continue lendo para saber como especificar o modelo de detecção de face em determinadas operações de face. API de detecção facial usa detecção facial, sempre que ele converte uma imagem de uma face em alguma outra forma de dados.

Se você não tiver certeza se você deve usar o modelo mais recente, vá para o [avaliar modelos diferentes](#evaluate-different-models) seção para avaliar o novo modelo e comparar os resultados usando o conjunto de dados atual.

## <a name="prerequisites"></a>Pré-requisitos

Você deve estar familiarizado com o conceito de detecção de face de inteligência Artificial. Se você não for, consulte o guia conceitual de detecção de face ou o guia de instruções:

* [Conceitos de detecção facial](../concepts/face-detection.md)
* [Como detectar faces em uma imagem](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detecte rostos com o modelo especificado

Detecção facial localiza os locais de caixa delimitadora de rostos humanos e identifica seus pontos de referência do visual. Ele extrai os recursos da superfície e armazena-os para uso posterior no [reconhecimento](../concepts/face-recognition.md) operações.

Quando você usa o [enfrentam - detectar] API, você pode atribuir a versão do modelo com o `detectionModel` parâmetro. Os valores disponíveis são:

* `detection_01`
* `detection_02`

Uma URL de solicitação para o [enfrentam - detectar] REST API terá esta aparência:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Se você estiver usando a biblioteca de cliente, você pode atribuir o valor para `detectionModel` , passando uma cadeia de caracteres apropriada. Se você deixar não atribuídos, a API usará a versão do modelo padrão (`detection_01`). Consulte o seguinte exemplo de código para a biblioteca de cliente .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Adicionar face a pessoa com o modelo especificado

API de detecção facial pode extrair dados de face de uma imagem e associá-la com um **pessoa** por meio do objeto de [PersonGroup pessoa - adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API. Nessa chamada de API, você pode especificar o modelo de detecção da mesma forma como em [enfrentam - detectar].

Consulte o seguinte exemplo de código para a biblioteca de cliente .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Esse código cria uma **PersonGroup** com a ID `mypersongroupid` e adiciona uma **pessoa** a ele. Em seguida, ele adiciona uma Face a este **pessoa** usando o `detection_02` modelo. Se você não especificar o *detectionModel* parâmetro, a API usará o modelo padrão, `detection_01`.

> [!NOTE]
> Você não precisa usar o mesmo modelo de detecção para todas as faces em uma **pessoa** objeto e você não precisa usar o mesmo modelo de detecção ao detectar novas faces a ser comparado com um **pessoa** objeto (em que o [Face – Identificar] API, por exemplo).

## <a name="add-face-to-facelist-with-specified-model"></a>Adicionar face a FaceList com o modelo especificado

Você também pode especificar um modelo de detecção de quando você adiciona uma face de um existente **FaceList** objeto. Consulte o seguinte exemplo de código para a biblioteca de cliente .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Esse código cria uma **FaceList** chamado `My face collection` e adiciona uma Face a ele com o `detection_02` modelo. Se você não especificar o *detectionModel* parâmetro, a API usará o modelo padrão, `detection_01`.

> [!NOTE]
> Você não precisa usar o mesmo modelo de detecção para todas as faces em uma **FaceList** objeto e você não precisa usar o mesmo modelo de detecção ao detectar novas faces a ser comparado com um **FaceList** objeto.

## <a name="evaluate-different-models"></a>Avaliar modelos diferentes

Os modelos de detecção facial diferentes são otimizados para tarefas diferentes. Consulte a tabela a seguir para obter uma visão geral das diferenças.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Opção padrão para todas as operações de detecção de face. | Lançado em maio de 2019 e disponível, opcionalmente, em todas as operações de detecção de face.
|Não é otimizado para pequena, do lado do modo de exibição ou desfocadas faces.  | Precisão aprimorada no pequeno, do lado do modo de exibição e desfocadas faces. |
|Retorna enfrenta atributos (pose principal, idade, emoções e assim por diante) se eles são especificados na chamada de detectar. |  Não retorna atributos faciais.     |
|Retorna enfrenta marcos se eles são especificados na chamada de detectar.   | Não retorna marcos faciais.  |

A melhor maneira de comparar o desempenho do `detection_01` e `detection_02` modelos é usá-las em um conjunto de dados de exemplo. Recomendamos chamar o [enfrentam - detectar] API em uma variedade de imagens, especialmente imagens de muitas faces ou de faces que são difíceis de ver, usando cada modelo de detecção. Preste atenção ao número de faces que retorna cada modelo.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como especificar o modelo de detecção para usar com diferentes APIs de detecção facial. Em seguida, siga um início rápido para começar a usar a detecção facial.

* [Detecte rostos em uma imagem (SDK do .NET)](../quickstarts/csharp-detect-sdk.md)

[Enfrentam - detectar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
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
