---
title: Pull do Docker para o contêiner de Análise de Sentimento
titleSuffix: Azure Cognitive Services
description: Comando de pull do Docker para Análise de Sentimento contêiner
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 549ecf940c53267db6b3caa576c4c24db414337a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90906017"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Pull do Docker para o contêiner Análise de Sentimento v3

O contêiner de contêiner de análise de opiniões v3 está disponível em vários idiomas. Para baixar o contêiner para o contêiner em inglês, use o comando a seguir. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Para baixar o contêiner para outro idioma, substitua `en` por um dos códigos de idioma abaixo. 

| Contêiner de Análise de Texto | Código de idioma |
|--|--|
| Inglês | `en` |
| Espanhol | `es` |
| Francês | `fr` |
| Italiano | `it` |
| Alemão | `de` |
| Chinês-simplificado | `zh` |
| Chinês-tradicional | `zht` |
| Japonês | `ja` |
| Português | `pt` |
| Holandês | `nl` |

Para obter uma descrição completa das marcas disponíveis para os contêineres de Análise de Texto, consulte [Hub do Docker](https://go.microsoft.com/fwlink/?linkid=2018654).