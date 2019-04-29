---
title: Atributos da entidade do Academic Graph ‒ API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos da entidade que você pode usar com o Grafo Acadêmico na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 485775660ecfdf2291365ab98c9188295ea2cbde
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61340172"
---
# <a name="entity-attributes"></a>Atributos da entidade

O grafo acadêmico é composto de 7 tipos de entidades. Todas as entidades terão uma ID de entidade e um tipo de entidade.

## <a name="common-entity-attributes"></a>Atributos de entidades comuns
NOME    |DESCRIÇÃO                |Type       | Operações
------- | ------------------------- | --------- | ----------------------------
ID      |ID da Entidade                  |Int64      |É igual a
Ty      |Tipo de entidade                |enum   |É igual a

## <a name="entity-type-enum"></a>Enumeração de tipo de entidade
NOME                                                            |value
----------------------------------------------------------------|-----
[Papel](PaperEntityAttributes.md)                               |0
[Autor](AuthorEntityAttributes.md)                             |1
[Diário](JournalEntityAttributes.md)                           |2
[Série de conferência](JournalEntityAttributes.md)                 |3
[Instância de conferência](ConferenceInstanceEntityAttributes.md)    |4
[Afiliação](AffiliationEntityAttributes.md)                   |5
[Campo de estudo](FieldsOfStudyEntityAttributes.md)                      |6

