---
title: Atributos de entidade de afiliação na API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Afiliação na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 82e6a5b66342e58e62da029d617cbd1d74c28149
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61340501"
---
# <a name="affiliation-entity"></a>Entidade Afiliação

<sub> *Os atributos a seguir são específicos da entidade de afiliação. (Ty = '5') </sub>

NOME    |DESCRIÇÃO                            |Type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID da Entidade                              |Int64      |É igual a
AfN     |Nome normalizado da afiliação        |Cadeia de caracteres     |É igual a
DAfN    |Nome de exibição da afiliação       |Cadeia de caracteres     |Nenhum
CC      |Contagem total de citações da afiliação           |Int32      |Nenhum  
ECC     |Contagem total estimada de citações da afiliação |Int32      |Nenhum

## <a name="extended-metadata-attributes"></a>Atributos de metadados estendidos ##

NOME    | DESCRIÇÃO               
--------|---------------------------    
Computador      |Contagem de artigos da afiliação
