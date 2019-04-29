---
title: Atributos da entidade Campo de Estudo ‒ API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Campo de Estudo na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: e9d6badf76efd03c0520a728af7b3e47b25f200a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61339578"
---
# <a name="field-of-study-entity"></a>Entidade Campo de Estudo

<sub> *Os atributos a seguir são específicos para a entidade do campo de estudo. (Ty = '6') </sub>

NOME    |DESCRIÇÃO                            |Type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID da Entidade                              |Int64      |É igual a
FN      |Nome normalizado do campo de estudo         |Cadeia de caracteres     |É igual a
DFN     |Nome de exibição do campo de estudo            |Cadeia de caracteres     |Nenhum
CC      |Contagem total de citações do campo de estudo    |Int32      |Nenhum  
ECC     |Campo da contagem total estimada de citações|Int32      |Nenhum
FL      |Nível nos campos de hierarquia de estudo     |Int32      |Igual a, <br/>IsBetween
FP.FN   |Nome do campo de estudo pai             |Cadeia de caracteres     |É igual a
FP.FId  |ID do campo de estudo pai               |Int64      |É igual a
FC.FN   |Nome do campo de estudo filho              |Cadeia de caracteres     |É igual a
FC.FId  |ID do campo de estudo filho                |Int64      |É igual a
