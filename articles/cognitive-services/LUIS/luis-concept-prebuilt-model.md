---
title: Modelos predefinidos – LUIS
titleSuffix: Azure Cognitive Services
description: Modelos predefinidos fornecem domínios, intenções, enunciados e entidades. Você pode iniciar o aplicativo com um domínio predefinido ou, posteriormente adicionar um domínio relevante ao aplicativo.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 6642e59c2957b298d54bc587853752b9fce74686
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019136"
---
# <a name="prebuilt-models"></a>Modelos predefinidos

Modelos predefinidos fornecem domínios, intenções, enunciados e entidades. Você pode iniciar seu aplicativo com um modelo predefinido ou adicionar um modelo relevante ao seu aplicativo posteriormente. 

## <a name="types-of-prebuilt-models"></a>Tipos de modelos predefinidos

O LUIS fornece três tipos de modelos pré-criados. Cada modelo poderá ser adicionado ao aplicativo a qualquer momento. 

|Tipo de modelo|Includes|
|--|--|
|[Domínio](luis-reference-prebuilt-domains.md)|Intenções, enunciados, entidades|
|Intenções|Intenções, enunciados|
|[Entidades](luis-reference-prebuilt-entities.md)|Apenas entidades| 

## <a name="prebuilt-domains"></a>Domínios predefinidos

O Reconhecimento vocal (LUIS) fornece *domínios* pré-criados, que são modelos pré-instalados de [intenções](luis-how-to-add-intents.md) e [entidades](luis-concept-entity-types.md) que funcionam em conjunto para domínios ou categorias comuns de aplicativos cliente. 

Os domínios predefinidos são treinados e prontos para serem adicionados ao aplicativo LUIS. As intenções e entidades de um domínio predefinido são totalmente personalizáveis depois de serem adicionadas ao seu aplicativo. 

> [!TIP]
> As intenções e as entidades em um domínio predefinido funcionam melhor juntas. É melhor combinar intenções e entidades do mesmo domínio, quando for possível.
> O domínio predefinido de Utilitários tem intenções que você pode personalizar para uso em qualquer domínio. Por exemplo, você pode adicionar `Utilities.Repeat` ao seu aplicativo e treiná-lo para reconhecer qualquer ação que o usuário quiser repetir em seu aplicativo. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Alterando o comportamento de uma intenção de domínio predefinido

Você pode achar que um domínio predefinido contém uma intenção que é semelhante a uma que você deseja ter em seu aplicativo do LUIS mas quer que tenha um comportamento diferente. Por exemplo, o domínio predefinido **coloca** uma `MakeReservation` intenção de fazer uma reserva de restaurante, mas você deseja que seu aplicativo use essa intenção para fazer reservas de Hotel. Nesse caso, você pode modificar o comportamento dessa intenção adicionando o exemplo declarações à intenção de fazer reservas de Hotel e, em seguida, treinar novamente o aplicativo. 

Você pode encontrar uma lista completa dos domínios predefinidos na [Referência de domínios predefinidos](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Intenções predefinidas

O LUIS fornece tentativas predefinidas e suas declarações para cada um de seus domínios predefinidos. As intenções podem ser adicionadas sem adicionar todo o domínio. Adicionar uma intenção é o processo de adicionar uma intenção e seu declarações ao seu aplicativo. Tanto o nome da intenção como a lista de enunciados podem ser modificados.  

## <a name="prebuilt-entities"></a>Entidades predefinidas

O LUIS inclui um conjunto de entidades predefinidas para reconhecer tipos comuns de informações como datas, horas, números, medidas e moeda. O suporte a entidade predefinida varia conforme a cultura do seu aplicativo de LUIS. Para obter uma lista completa de entidades predefinidas compatíveis com LUIS, incluindo suporte por cultura, veja a [referência de entidade predefinida](./luis-reference-prebuilt-entities.md).

Quando uma entidade predefinida é incluída em seu aplicativo, suas previsões são incluídas em seu aplicativo publicado. O comportamento das entidades predefinidas é previamente treinado e **não pode** ser modificado. 

> [!NOTE]
> **builtin.datetime** foi preterido. Ele foi substituído por [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), que fornece o reconhecimento de intervalos de data e hora, bem como reconhecimento aprimorado de datas e horas ambíguas.

## <a name="next-steps"></a>Próximas etapas

Saiba como [adicionar entidades predefinidas](./howto-add-prebuilt-models.md) ao aplicativo.