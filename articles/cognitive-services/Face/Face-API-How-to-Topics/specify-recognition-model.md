---
title: Como especificar um modelo de reconhecimento-API de Detecção Facial
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como escolher o modelo de reconhecimento a ser usado com o aplicativo API de Detecção Facial do Azure.
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: fd60923351970dfe5aa5705a0508dbd39941ef58
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68254354"
---
# <a name="specify-a-face-recognition-model"></a>Especificar um modelo de reconhecimento facial

Este guia mostra como especificar um modelo de reconhecimento facial para detecção de face, identificação e pesquisa de similaridade usando o API de Detecção Facial do Azure.

O API de Detecção Facial usa modelos de aprendizado de máquina para executar operações em faces humanas em imagens. Continuamos a melhorar a precisão de nossos modelos com base nos comentários e nos avanços dos clientes em pesquisa, e fornecemos esses aprimoramentos como atualizações de modelo. Os desenvolvedores têm a opção de especificar qual versão do modelo de reconhecimento facial gostaria de usar; Eles podem escolher o modelo que melhor se adapta ao seu caso de uso.

Se você for um novo usuário, recomendamos o uso do modelo mais recente. Continue lendo para saber como especificá-lo em diferentes operações de face enquanto evita conflitos de modelo. Se você for um usuário avançado e não tiver certeza se deve mudar para o modelo mais recente, pule para a seção [avaliar modelos diferentes](#evaluate-different-models) para avaliar o novo modelo e comparar os resultados usando seu conjunto de dados atual.

## <a name="prerequisites"></a>Pré-requisitos

Você deve estar familiarizado com os conceitos de detecção e identificação de ia face. Se não estiver, consulte estes guias de instruções primeiro:

* [Como detectar rostos em uma imagem](HowtoDetectFacesinImage.md)
* [Como identificar rostos em uma imagem](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detectar rostos com o modelo especificado

A detecção facial identifica os pontos de referência visuais de faces humanas e localiza seus locais de caixa delimitadora. Ele também extrai os recursos da face e os armazena para uso na identificação. Todas essas informações formam a representação de uma face.

O modelo de reconhecimento é usado quando os recursos de face são extraídos, para que você possa especificar uma versão de modelo ao executar a operação de detecção.

Ao usar a API de [Detecção facial] , atribua a versão do modelo `recognitionModel` com o parâmetro. Os valores disponíveis são:

* `recognition_01`
* `recognition_02`

Opcionalmente, você pode especificar o parâmetro _returnRecognitionModel_ (padrão **false**) para indicar se _recognitionModel_ deve ser retornado em resposta. Portanto, uma URL de solicitação para a API REST de [detecção facial] terá a seguinte aparência:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Se você estiver usando a biblioteca de cliente, poderá atribuir o valor para `recognitionModel` passando uma cadeia de caracteres que representa a versão.
Se você deixá-lo sem atribuição, a versão padrão do modelo (_recognition_01_) será usada. Consulte o exemplo de código a seguir para a biblioteca de cliente .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identificar faces com o modelo especificado

O API de Detecção Facial pode extrair dados de face de uma imagem e associá-los a um objeto **Person** (por meio da chamada [Add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API, por exemplo), e vários objetos **Person** podem ser armazenados juntos em um **Person**. Em seguida, uma nova face pode ser comparada com um grupo de **pessoas** (com a chamada de [Face – Identificar] ) e a pessoa correspondente dentro dele pode ser identificada.

Um grupo de **pessoas** deve ter um modelo de reconhecimento exclusivo para todas as **pessoas**s e você pode especificar isso usando o `recognitionModel` parâmetro ao criar o grupo ([GrupoDePessoas - Criar] ou [GrandeGrupoDePessoas - Criar]). Se você não especificar esse parâmetro, o modelo original `recognition_01` será usado. Um grupo sempre usará o modelo de reconhecimento com o qual foi criado, e novas faces serão associadas a esse modelo quando forem adicionadas a ele; Isso não pode ser alterado após a criação de um grupo. Para ver a qual modelo um grupo de **pessoas** está configurado, use a API [Pessoa-obter] com o parâmetro _returnRecognitionModel_ definido como **true**.

Consulte o exemplo de código a seguir para a biblioteca de cliente .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

Nesse código, um grupo de **pessoas** com ID `mypersongroupid` é criado e é configurado para usar o modelo _recognition_02_ para extrair recursos de face.

De forma correspondente, você precisa especificar qual modelo usar ao detectar faces para comparar com esse **Person** (por meio da API de [detecção facial] ). O modelo usado deve sempre ser consistente com a configuração do **usuário**; caso contrário, a operação falhará devido a modelos incompatíveis.

Não há nenhuma alteração na API de [Face – Identificar] ; Você só precisa especificar a versão do modelo na detecção.

## <a name="find-similar-faces-with-specified-model"></a>Localizar faces semelhantes com o modelo especificado

Você também pode especificar um modelo de reconhecimento para pesquisa de similaridade. Você pode atribuir a versão `recognitionModel` do modelo ao ao criar a lista de rosto com a facelist [Facelist-criar] API ou [LargeFaceList-criar]. Se você não especificar esse parâmetro, o modelo original `recognition_01` será usado. Uma lista facial sempre usará o modelo de reconhecimento com o qual foi criado, e novas faces serão associadas a esse modelo quando forem adicionadas a ele; Isso não pode ser alterado após a criação. Para ver a qual modelo uma lista de face está configurada, use a API [Facelist-obter] de facelist com o parâmetro _returnRecognitionModel_ definido como **true**.

Consulte o exemplo de código a seguir para a biblioteca de cliente .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Esse código cria uma lista de face `My face collection`chamada, usando o modelo _recognition_02_ para extração de recursos. Quando você pesquisa esta lista de rosto para rostos semelhantes a uma nova face detectada, essa face deve ter sido detectada ([Detecção facial]) usando o modelo _recognition_02_ . Como na seção anterior, o modelo precisa ser consistente.

Não há nenhuma alteração na API de [Face-localizar semelhante] ; Você especifica apenas a versão do modelo na detecção.

## <a name="verify-faces-with-specified-model"></a>Verificar rostos com o modelo especificado

A API de [verificação de face] verifica se duas faces pertencem à mesma pessoa. Não há nenhuma alteração na API de verificação com relação aos modelos de reconhecimento, mas você só pode comparar faces que foram detectadas com o mesmo modelo. Portanto, as duas faces precisarão ter sido detectadas usando `recognition_01` ou. `recognition_02`

## <a name="evaluate-different-models"></a>Avaliar modelos diferentes

Se você quiser comparar o desempenho dos modelos _recognition_01_ e _recognition_02_ em seus dados, será necessário:

1. Crie dois **Person**s com _recognition_01_ e _recognition_02_ , respectivamente.
1. Use seus dados de imagem para detectar rostos e registrá-los para a **pessoa**s para esses dois **Person**s e disparar o processo de treinamento com a API de treinamento de [GrupoDePessoas - Treinar] .
1. Teste com a [Face – Identificar] em ambos os s **Person**e compare os resultados.

Se você normalmente especificar um limite de confiança (um valor entre zero e outro que determina o quão confiante o modelo deve ser para identificar uma face), talvez seja necessário usar limites diferentes para modelos diferentes. Um limite para um modelo não deve ser compartilhado para outro e não vai necessariamente produzir os mesmos resultados.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a especificar o modelo de reconhecimento a ser usado com diferentes APIs de serviço de face. Em seguida, siga um guia de início rápido para começar a usar a detecção facial.

* [Detectar faces em uma imagem](../quickstarts/csharp-detect-sdk.md)

[Detecção facial]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face-localizar semelhante]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face – Identificar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Verificação de face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[GrupoDePessoas - Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[Pessoa-obter]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[GrupoDePessoas - Treinar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[GrandeGrupoDePessoas - Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[Facelist-criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[Facelist-obter]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList-criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
