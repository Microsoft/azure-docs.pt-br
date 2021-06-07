---
title: Parâmetros da cadeia de consulta da API v3
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 14380dd781fc1eebfe7edb0a816ff8af0f2f17b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91309414"
---
Os parâmetros da cadeia de consulta da API v3 incluem:

|Parâmetro de consulta|Nome do portal do LUIS|Type|Versão|Padrão|Finalidade|
|--|--|--|--|--|--|
|`log`|Salvar logs|boolean|V2 & V3|false|Armazenar consulta no arquivo de log. O valor padrão é false.|
|`query`|-|string|Somente V3|Nenhum padrão-ele é necessário na solicitação GET|**Em v2**, o expressão a ser previsto está no `q` parâmetro. <br><br>**No v3**, a funcionalidade é passada no `query` parâmetro.|
|`show-all-intents`|Incluir pontuações para todas as intenções|boolean|Somente V3|false|Retorne todas as intenções com a pontuação correspondente no objeto **preditiva. retenções** . As intenções são retornadas como objetos em um `intents` objeto pai. Isso permite o acesso programático sem a necessidade de encontrar a intenção em uma matriz: `prediction.intents.give` . Em v2, elas foram retornadas em uma matriz. |
|`verbose`|Incluir mais detalhes de entidades|boolean|V2 & V3|false|**Em v2**, quando definido como true, todas as intenções previstas foram retornadas. Se você precisar de todas as intenções previstas, use o parâmetro v3 de `show-all-intents` .<br><br>**No v3**, esse parâmetro fornece apenas detalhes de metadados de entidade de previsão de entidade.  |
|`timezoneOffset`|-|string|V2|-|Fuso horário aplicado às entidades datetimeV2.|
|`datetimeReference`|-|string|V3|-|[Fuso horário](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) aplicado às entidades datetimeV2. Substitui `timezoneOffset` de v2.|