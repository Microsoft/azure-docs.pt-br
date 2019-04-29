---
title: Atributos da entidade Série de Conferências – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Série de Conferências.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 38b4aa4c899668a68041f042ce6981ddd8c58219
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61340189"
---
# <a name="conference-series-entity"></a>Entidade Série de Conferências

<sub> *Os atributos a seguir são específicos da entidade da série de conferências. (Ty = '3') </sub>

NOME    |DESCRIÇÃO                            |Type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID da Entidade                              |Int64      |É igual a
CN      |Nome normalizado da série de conferências      |Cadeia de caracteres     |É igual a
DCN     |Nome de exibição da série de conferências         |Cadeia de caracteres     |Nenhum
CC      |Contagem total de citações da série de conferências         |Int32      |Nenhum  
ECC     |Contagem total estimada de citações da série de conferências   |Int32      |Nenhum
F.FId   |ID da entidade do Campo de Estudo associado à série de conferências |Int64  | É igual a
F.FN    |Nome do Campo de Estudo associado à série de conferências  | Igual a,<br/>StartsWith
