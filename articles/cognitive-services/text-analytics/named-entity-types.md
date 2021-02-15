---
title: Categorias com suporte para reconhecimento de entidade nomeada
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre as categorias de entidade com suporte no API de Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/22/2021
ms.author: aahi
ms.openlocfilehash: 883c5a20612f4dab44c0d06776ee287b27174ab9
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097247"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Categorias de entidade com suporte no API de Análise de Texto v3

Use este artigo para localizar as categorias de entidade que podem ser retornadas pelo [reconhecimento de entidade nomeada](how-tos/text-analytics-how-to-entity-linking.md) (Ner). NER executa um modelo de previsão para identificar e categorizar entidades nomeadas de um documento de entrada.

Uma visualização do NER v 3.1 também está disponível, o que inclui a capacidade de detectar informações pessoais ( `PII` ) e de integridade ( `PHI` ). Além disso, clique na guia **integridade** para ver uma lista de categorias com suporte no análise de texto para fins de integridade. 

Você pode encontrar uma lista de tipos retornados pela versão 2,1 no [Guia de migração](migration-guide.md?tabs=named-entity-recognition)

## <a name="entity-categories"></a>Categorias de entidade

#### <a name="general"></a>[Geral](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[PII](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Saúde](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

**_

## <a name="next-steps"></a>Próximas etapas

_ [Como usar o reconhecimento de entidade nomeada no análise de texto](how-tos/text-analytics-how-to-entity-linking.md)
