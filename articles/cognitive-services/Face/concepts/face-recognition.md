---
title: Conceitos de reconhecimento facial
titleSuffix: Azure Cognitive Services
description: Este artigo explica os conceitos de verificar, localizar semelhantes, agrupar e identificar as operações de reconhecimento facial e as estruturas de dados subjacentes.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 00dadf8a91b7ed01ab9f91933d296744305a95af
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518799"
---
# <a name="face-recognition-concepts"></a>Conceitos de reconhecimento facial

Este artigo explica os conceitos de verificar, localizar semelhantes, agrupar e identificar as operações de reconhecimento facial e as estruturas de dados subjacentes. Em grande parte, o reconhecimento descreve o trabalho de comparação de duas faces diferentes para determinar se elas são semelhantes ou pertencem à mesma pessoa.

## <a name="recognition-related-data-structures"></a>Estruturas de dados relacionadas a reconhecimento

As operações de reconhecimento usam principalmente as seguintes estruturas de dados. Esses objetos são armazenados na nuvem e podem ser referenciados por suas cadeias de caracteres de ID. As cadeias de caracteres de ID são sempre exclusivas em uma assinatura. Os campos de nome podem ser duplicados.

|Nome|Descrição|
|:--|:--|
|DetectedFace| Essa única representação facial é recuperada pela operação de [detecção facial](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) . Sua ID expira 24 horas após sua criação.|
|PersistedFace| Quando objetos DetectedFace são adicionados a um grupo, como Facelist ou Person, eles se tornam objetos PersistedFace. Eles podem ser [recuperados](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) a qualquer momento e não expiram.|
|[Facelist](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) ou [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Essa estrutura de dados é uma lista asclassificada de objetos PersistedFace. Uma Facelist tem uma ID exclusiva, uma cadeia de caracteres de nome e, opcionalmente, uma cadeia de caracteres de dados de usuário.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Essa estrutura de dados é uma lista de objetos PersistedFace que pertencem à mesma pessoa. Ele tem uma ID exclusiva, uma cadeia de caracteres de nome e, opcionalmente, uma cadeia de caracteres de dados de usuário.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) ou [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Essa estrutura de dados é uma lista asclassificada de objetos Person. Ele tem uma ID exclusiva, uma cadeia de caracteres de nome e, opcionalmente, uma cadeia de caracteres de dados de usuário. Um Person deve ser [treinado](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) antes que possa ser usado em operações de reconhecimento.|

## <a name="recognition-operations"></a>Operações de reconhecimento

Esta seção detalha como as quatro operações de reconhecimento usam as estruturas de dados descritas anteriormente. Para obter uma descrição ampla de cada operação de reconhecimento, consulte [visão geral](../Overview.md).

### <a name="verify"></a>Verificar

A operação de [verificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) usa uma ID de face de DetectedFace ou PersistedFace e outra ID de face ou um objeto Person e determina se elas pertencem à mesma pessoa. Se você passar um objeto Person, poderá, opcionalmente, passar um Person para o qual essa pessoa pertença para melhorar o desempenho.

### <a name="find-similar"></a>Localizar semelhante

A operação [Localizar semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) usa uma ID de face de DetectedFace ou PersistedFace e uma facelist ou uma matriz de outras IDs de face. Com uma Facelist, ele retorna uma face de rostos menor que é semelhante à face fornecida. Com uma matriz de IDs de face, ela retorna, de forma semelhante, uma matriz menor.

### <a name="group"></a>Grupo

A operação de [grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) usa uma matriz de IDs de face asclassificadas de DetectedFace ou PersistedFace e retorna as mesmas IDs agrupadas em várias matrizes menores. Cada matriz de "grupos" contém IDs de face que parecem semelhantes. Uma única matriz "bagunça" contém IDs de face para as quais nenhuma semelhança foi encontrada.

### <a name="identify"></a>Identificar

A operação de [identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) usa uma ou várias IDs de face de DetectedFace ou PersistedFace e um Person e retorna uma lista de objetos Person a que cada face pode pertencer. Os objetos Person retornados são encapsulados como objetos candidatos, que têm um valor de confiança de previsão.

## <a name="input-data"></a>Dados de entrada

Use as seguintes dicas para garantir que suas imagens de entrada forneçam os resultados de reconhecimento mais precisos:

* Os formatos de imagem de entrada com suporte são JPEG, PNG, GIF (o primeiro quadro), BMP.
* O tamanho do arquivo de imagem não deve ser maior que 6 MB.
* Quando você cria objetos Person, use fotos que apresentam diferentes tipos de ângulos e iluminação.
* Algumas faces podem não ser reconhecidas devido a desafios técnicos, como:
  * Imagens com iluminação extrema, por exemplo, baixa iluminação excessiva.
  * Obstruções que bloqueiam um ou ambos os olhos.
  * Diferenças no tipo de cabelo ou cabelo facial.
  * Alterações na aparência facial devido à idade.
  * Expressões de faciais Extreme.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com os conceitos de reconhecimento facial, escreva um script que identifique rostos em relação a um funcionário treinado.

* [Início rápido da biblioteca de clientes de Detecção Facial](../Quickstarts/client-libraries.md)