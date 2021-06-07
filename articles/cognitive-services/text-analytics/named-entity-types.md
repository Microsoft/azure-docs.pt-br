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
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 8b596a5e54c0b59c4c0b49aa5cdc4fd6477d46dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599285"
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

***

## <a name="next-steps"></a>Próximas etapas

* [Como usar o reconhecimento de entidade nomeada no Análise de Texto](how-tos/text-analytics-how-to-entity-linking.md)
