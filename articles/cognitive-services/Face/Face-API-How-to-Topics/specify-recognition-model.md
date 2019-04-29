---
title: Como especificar um modelo de reconhecimento – API de detecção facial
titleSuffix: Azure Cognitive Services
description: Este artigo mostra como escolher qual modelo de reconhecimento para usar com o aplicativo de API de detecção facial do Azure.
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: 33348e637143b923719425b9674f99a475d848d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815816"
---
# <a name="specify-a-face-recognition-model"></a>Especificar um modelo de reconhecimento facial

Este guia mostra como especificar um modelo de reconhecimento de face para detecção facial, a identificação e a pesquisa de similaridade usando a API de detecção facial do Azure.

API de detecção facial usa modelos de aprendizado de máquina para executar operações em rostos humanos em imagens. Continuamos a melhorar a precisão dos nossos modelos com base nos comentários dos clientes e avanços em pesquisas e fornecemos essas melhorias, como atualizações de modelo. Os desenvolvedores têm a opção de especificar qual versão do modelo de reconhecimento facial eles gostariam de usar; eles podem escolher o modelo que melhor se adapta a seu caso de uso.

Se você for um novo usuário, é recomendável que você usar o modelo mais recente. Continue lendo para saber como especificá-lo em operações diferentes de Face, evitando conflitos de modelo. Se você for um usuário avançado e não têm certeza se você deve alternar para o modelo mais recente, vá para o [avaliar modelos diferentes](#evaluate-different-models) seção para avaliar o novo modelo e comparar os resultados usando o conjunto de dados atual.

## <a name="prerequisites"></a>Pré-requisitos

Você deve estar familiarizado com os conceitos de identificação e detecção de face de inteligência Artificial. Se você não for, consulte esses guias de instruções primeiro:

* [Como detectar faces em uma imagem](HowtoDetectFacesinImage.md)
* [Como identificar rostos em uma imagem](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detecte rostos com o modelo especificado

Detecção facial identifica os pontos de referência visual de rostos humanos e localiza suas localizações de caixa delimitadora. Ele também extrai os recursos da face e armazena-os para uso em identificação. Todas essas informações de formulários a representação de uma face.

O modelo de reconhecimento é usado quando os recursos de detecção facial estão extraídos, para que você possa especificar uma versão de modelo ao executar a operação de detectar.

Ao usar o [enfrentam - detectar] API, atribuir a versão do modelo com o `recognitionModel` parâmetro. Os valores disponíveis são:

* `recognition_01`
* `recognition_02`

Opcionalmente, você pode especificar o _returnRecognitionModel_ parâmetro (padrão **falso**) para indicar se _recognitionModel_ deve ser retornado na resposta. Portanto, uma URL de solicitação para o [enfrentam - detectar] REST API terá esta aparência:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]
&subscription-key=<Subscription key>`

Se você estiver usando a biblioteca de cliente, você pode atribuir o valor para `recognitionModel` , passando uma cadeia de caracteres que representa a versão.
Se você deixá-lo não atribuído, a versão do modelo padrão (_recognition_01_) será usado. Consulte o seguinte exemplo de código para a biblioteca de cliente .NET.

```csharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identifique rostos com o modelo especificado

API de detecção facial pode extrair dados de face de uma imagem e associá-la com um **pessoa** objeto (por meio das [adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) chamada à API, por exemplo) e vários **pessoa** objetos podem ser armazenados juntos em uma **PersonGroup**. Em seguida, um novo rosto pode ser comparado com um **PersonGroup** (com o [Face – Identificar] chamar), e a pessoa correspondente dentro desse grupo pode ser identificada.

Um **PersonGroup** deve ter um modelo de reconhecimento exclusivo para todos o **pessoa**s e você pode especificar-isso usando o `recognitionModel` parâmetro quando você cria o grupo ([GrupoDePessoas - Criar] ou [GrandeGrupoDePessoas - Criar]). Se você não especificar esse parâmetro, o original `recognition_01` modelo é usado. Um grupo sempre usará o modelo de reconhecimento que ele foi criado com, e novas faces tornará associados esse modelo quando eles são adicionados a ele. Isso não pode ser alterado após a criação de um grupo. Para ver qual modelo de um **PersonGroup** é configurado com, use o [PersonGroup - Get] API com o _returnRecognitionModel_ parâmetro definido como **true**.

Consulte o seguinte exemplo de código para a biblioteca de cliente .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

Nesse código, uma **PersonGroup** com a ID `mypersongroupid` é criada, e ele é configurado para usar os _recognition_02_ modelo para extrair recursos de detecção facial.

Do mesmo modo, você precisa especificar qual modelo usar ao detectar faces para comparar em relação a este **PersonGroup** (por meio de [enfrentam - detectar] API). O modelo que você use sempre deve ser consistente com o **PersonGroup**da configuração; caso contrário, haverá falha na operação devido a modelos incompatíveis.

Não há nenhuma alteração na [Face – Identificar] API; você só precisa especificar a versão do modelo de detecção.

## <a name="find-similar-faces-with-specified-model"></a>Encontre rostos parecidos com o modelo especificado

Você também pode especificar um modelo de reconhecimento para pesquisa de similaridade. Você pode atribuir a versão do modelo com `recognitionModel` ao criar a lista de face com [FaceList - Create] API ou [LargeFaceList - Create]. Se você não especificar esse parâmetro, o original `recognition_01` modelo é usado. Uma lista de face sempre usará o modelo de reconhecimento que ele foi criado com, e novas faces tornará associados esse modelo quando eles são adicionados a ele. Isso não pode ser alterado após a criação. Para ver qual modelo de uma lista de detecção facial é configurada com, use o [FaceList - Get] API com o _returnRecognitionModel_ parâmetro definido como **verdadeiro**.

Consulte o seguinte exemplo de código para a biblioteca de cliente .NET.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Esse código cria uma lista de face chamada `My face collection`, usando o _recognition_02_ modelo de recurso para extração. Quando você pesquisa esta lista de face para faces semelhantes para um novo rosto detectado, que enfrentam deve foram detectada ([enfrentam - detectar]) usando o _recognition_02_ modelo. Como na seção anterior, o modelo precisa ser consistente.

Não há nenhuma alteração na [Detecção facial - localizar Similar] API; você especificar apenas a versão do modelo de detecção.

## <a name="verify-faces-with-specified-model"></a>Verifique rostos com o modelo especificado

O [Face: verificar] API verifica se dois rostos pertencem à mesma pessoa. Não há nenhuma alteração na API de verificar em relação a modelos de reconhecimento, mas só é possível comparar rostos que foram detectados com o mesmo modelo. Portanto, os dois rostos ambos precisará têm sido detectados usando `recognition_01` ou `recognition_02`.

## <a name="evaluate-different-models"></a>Avaliar modelos diferentes

Se você deseja comparar o desempenho do _recognition_01_ e _recognition_02_ modelos em seus dados, você precisará:

1. Criar duas **PersonGroup**s com _recognition_01_ e _recognition_02_ , respectivamente.
1. Use os dados de imagem para detectar faces e registrá-las para **pessoa**s nesses dois **PersonGroup**s e o treinamento de gatilho processam com [GrupoDePessoas - Treinar] API.
1. Testar com o [Face – Identificar] em ambos **PersonGroup**s e comparar os resultados.

Se você normalmente especifica um limite de confiança (um valor entre zero e um que determina o grau de confiança que o modelo deve ser identificar uma face), você talvez precise usar limites diferentes para modelos diferentes. Um limite para um modelo não deve ser compartilhado para outro e não necessariamente produzirá os mesmos resultados.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como especificar o modelo de reconhecimento para uso com serviço de detecção facial diferente APIs. Em seguida, siga um início rápido para começar a usar a detecção facial.

* [Detecte rostos em uma imagem](../quickstarts/csharp-detect-sdk.md)

[Enfrentam - detectar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Detecção facial - localizar Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face – Identificar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face: verificar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[GrupoDePessoas - Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[GrupoDePessoas - Treinar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[GrandeGrupoDePessoas - Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
