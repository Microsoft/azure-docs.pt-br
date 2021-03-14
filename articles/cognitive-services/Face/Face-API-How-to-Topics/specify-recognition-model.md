---
title: Como especificar um modelo de reconhecimento-face
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como escolher qual modelo de reconhecimento usar com seu aplicativo de face do Azure.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: longl
ms.custom: devx-track-csharp
ms.openlocfilehash: cd4ed992557a3c333919b63cc36757beb2f70454
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433549"
---
# <a name="specify-a-face-recognition-model"></a>Especificar um modelo de reconhecimento facial

Este guia mostra como especificar um modelo de reconhecimento facial para detecção de face, identificação e pesquisa de similaridade usando o serviço de face do Azure.

O serviço de face usa modelos de aprendizado de máquina para executar operações em faces humanas em imagens. Continuamos a melhorar a precisão de nossos modelos com base nos comentários e nos avanços dos clientes em pesquisa, e fornecemos esses aprimoramentos como atualizações de modelo. Os desenvolvedores têm a opção de especificar qual versão do modelo de reconhecimento facial gostaria de usar; Eles podem escolher o modelo que melhor se adapta ao seu caso de uso.

O serviço de face do Azure tem quatro modelos de reconhecimento disponíveis. Os modelos _recognition_01_ (publicados 2017), _recognition_02_ (publicado 2019) e _recognition_03_ (publicado na 2020) têm suporte contínuo para garantir a compatibilidade com versões anteriores para clientes que usam FaceLists ou o **Person** s criado com esses modelos. Uma **facelist** ou **Person** sempre usará o modelo de reconhecimento com o qual foi criado e novas faces serão associadas a esse modelo quando forem adicionadas. Isso não pode ser alterado após a criação e os clientes precisarão usar o modelo de reconhecimento correspondente com a **facelist** ou o **Person** correspondente.

Você pode migrar para modelos de reconhecimento posteriores por sua própria conveniência; no entanto, você precisará criar um novo FaceLists e PersonGroups com o modelo de reconhecimento de sua escolha.

O modelo de _recognition_04_ (publicado 2021) é o modelo mais preciso disponível no momento. Se você for um novo cliente, recomendamos o uso desse modelo. _Recognition_04_ fornecerá precisão aprimorada para comparações de similaridade e comparações de correspondência de pessoas. _Recognition_04_ melhora o reconhecimento de usuários registrados com coberturas de face (máscaras de Surgical, máscaras de N95, máscaras de tecido). Agora você pode criar experiências de usuário seguras e diretas que usam o modelo de _detection_03_ mais recente para detectar se um usuário registrado está aproveitando uma cobertura facial e, em seguida, reconhecer quem está com o modelo de _recognition_04_ mais recente. Observe que cada modelo opera independentemente dos outros, e um limite de confiança definido para um modelo não deve ser comparado entre os outros modelos de reconhecimento.

Continue lendo para saber como especificar um modelo selecionado em diferentes operações de face enquanto evita conflitos de modelo. Se você for um usuário avançado e quiser determinar se deve mudar para o modelo mais recente, pule para a seção [avaliar modelos diferentes](#evaluate-different-models) para avaliar o novo modelo e comparar os resultados usando seu conjunto de dados atual.


## <a name="prerequisites"></a>Pré-requisitos

Você deve estar familiarizado com os conceitos de detecção e identificação de ia face. Se não estiver, consulte estes guias primeiro:

* [Conceitos de detecção facial](../concepts/face-detection.md)
* [Conceitos de reconhecimento facial](../concepts/face-recognition.md)
* [Como detectar rostos em uma imagem](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detectar rostos com o modelo especificado

A detecção facial identifica os pontos de referência visuais de faces humanas e localiza seus locais de caixa delimitadora. Ele também extrai os recursos da face e os armazena para uso na identificação. Todas essas informações formam a representação de uma face.

O modelo de reconhecimento é usado quando os recursos de face são extraídos, para que você possa especificar uma versão de modelo ao executar a operação de detecção.

Ao usar a API de [detecção de face] , atribua a versão do modelo com o `recognitionModel` parâmetro. Os valores disponíveis são:
* recognition_01
* recognition_02
* recognition_03
* recognition_04


Opcionalmente, você pode especificar o parâmetro _returnRecognitionModel_ (padrão **false**) para indicar se _recognitionModel_ deve ser retornado em resposta. Portanto, uma URL de solicitação para a API REST de [detecção facial] terá a seguinte aparência:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Se você estiver usando a biblioteca de cliente, poderá atribuir o valor para `recognitionModel` passando uma cadeia de caracteres que representa a versão. Se você deixá-lo sem atribuição, uma versão de modelo padrão do `recognition_01` será usada. Consulte o exemplo de código a seguir para a biblioteca de cliente .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_01", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identificar faces com o modelo especificado

O serviço de face pode extrair dados de face de uma imagem e associá-los a um objeto **Person** (por meio da chamada [Add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API, por exemplo), e vários objetos **Person** podem ser armazenados juntos em um **Person**. Em seguida, uma nova face pode ser comparada com um grupo de **pessoas** (com a chamada de [identificação facial] ) e a pessoa correspondente dentro dele pode ser identificada.

Um grupo de **pessoas** deve ter um modelo de reconhecimento exclusivo para todas as **pessoas** s e você pode especificar isso usando o `recognitionModel` parâmetro ao criar o grupo ([Person-Create] ou [LargePersonGroup-Create]). Se você não especificar esse parâmetro, o modelo original `recognition_01` será usado. Um grupo sempre usará o modelo de reconhecimento com o qual foi criado, e novas faces serão associadas a esse modelo quando forem adicionadas a ele; Isso não pode ser alterado após a criação de um grupo. Para ver a qual modelo um grupo de **pessoas** está configurado, use a API [Person-Get] com o parâmetro _returnRecognitionModel_ definido como **true**.

Consulte o exemplo de código a seguir para a biblioteca de cliente .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

Nesse código, um grupo de **pessoas** com ID `mypersongroupid` é criado e é configurado para usar o modelo de _recognition_02_ para extrair os recursos de face.

De forma correspondente, você precisa especificar qual modelo usar ao detectar faces para comparar com esse **Person** (por meio da API de [detecção facial] ). O modelo usado deve sempre ser consistente com a configuração do **usuário**; caso contrário, a operação falhará devido a modelos incompatíveis.

Não há nenhuma alteração na API de [identificação facial] ; Você só precisa especificar a versão do modelo na detecção.

## <a name="find-similar-faces-with-specified-model"></a>Localizar faces semelhantes com o modelo especificado

Você também pode especificar um modelo de reconhecimento para pesquisa de similaridade. Você pode atribuir a versão do modelo `recognitionModel` ao ao criar a lista de rosto com a [facelist – Create] API ou [LargeFaceList-Create]. Se você não especificar esse parâmetro, o `recognition_01` modelo será usado por padrão. Uma lista de rosto sempre usará o modelo de reconhecimento com o qual foi criado e novas faces serão associadas a esse modelo quando forem adicionadas à lista; Você não pode alterar isso após a criação. Para ver a qual modelo uma lista de face está configurada, use a API [de obtenção de facelist] com o parâmetro _returnRecognitionModel_ definido como **true**.

Consulte o exemplo de código a seguir para a biblioteca de cliente .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_04");
```

Esse código cria uma lista de face chamada `My face collection` , usando o modelo de _recognition_04_ para extração de recursos. Quando você pesquisa esta lista de rosto para rostos semelhantes a uma nova face detectada, essa face deve ter sido detectada ([detecção de face]) usando o modelo de _recognition_04_ . Como na seção anterior, o modelo precisa ser consistente.

Não há nenhuma alteração na API de [face de busca semelhante] ; Você especifica apenas a versão do modelo na detecção.

## <a name="verify-faces-with-specified-model"></a>Verificar rostos com o modelo especificado

A API de [verificação de face] verifica se duas faces pertencem à mesma pessoa. Não há nenhuma alteração na API de verificação com relação aos modelos de reconhecimento, mas você só pode comparar faces que foram detectadas com o mesmo modelo.

## <a name="evaluate-different-models"></a>Avaliar modelos diferentes

Se você quiser comparar o desempenho de diferentes modelos de reconhecimento em seus próprios dados, será necessário:
1. Crie quatro PersonGroups usando _recognition_01_, _recognition_02_, _recognition_03_ e _recognition_04_ , respectivamente.
1. Use seus dados de imagem para detectar rostos e registrá-los para a **pessoa** s dentro dessas quatro **pessoas**. 
1. Treine seu PersonGroups usando a API Person-Train.
1. Teste com a identificação de face em todas as quatro **pessoas** do s e compare os resultados.


Se você normalmente especificar um limite de confiança (um valor entre zero e outro que determina o quão confiante o modelo deve ser para identificar uma face), talvez seja necessário usar limites diferentes para modelos diferentes. Um limite para um modelo não deve ser compartilhado para outro e não vai necessariamente produzir os mesmos resultados.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a especificar o modelo de reconhecimento a ser usado com diferentes APIs de serviço de face. Em seguida, siga um guia de início rápido para começar a usar a detecção facial.

* [SDK do .NET facial](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [SDK facial do Python](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [SDK do facial go](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[Face – Detectar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face-localizar semelhante]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face – Identificar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Verificação de face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[GrupoDePessoas - Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[Pessoa-obter]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[GrandeGrupoDePessoas - Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[Facelist-criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[Facelist-obter]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList-criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc