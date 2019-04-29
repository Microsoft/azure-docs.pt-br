---
title: Atributos de entidade de autor ‒ API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Autor na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: d4b33c06ab023023aadf403cf0ef0b08c2bafc5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609755"
---
# <a name="author-entity"></a>Entidade Autor
<sub> *Os atributos a seguir são específicos da entidade de autor. (Ty = '1') </sub>

NOME    |DESCRIÇÃO                            |Type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID da Entidade                              |Int64      |É igual a
AuN     |Nome normalizado do autor                 |Cadeia de caracteres     |É igual a
DAuN    |Nome de exibição do autor                    |Cadeia de caracteres     |Nenhum
CC      |Contagem total de citações do autor            |Int32      |Nenhum  
ECC     |Contagem total estimada de citações do autor  |Int32      |Nenhum
E       |Metadados estendidos (consulte a tabela "Atributos Meta Estendidos")  |Cadeia de caracteres     |Nenhum  


## <a name="extended-metadata-attributes"></a>Atributos de metadados estendidos ##

NOME    | DESCRIÇÃO               
--------|---------------------------    
LKA.Afn     | nome de exibição de afiliação associado ao autor  
LKA.AfId        | ID da entidade de afiliação associada ao autor
