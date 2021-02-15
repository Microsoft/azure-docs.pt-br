---
title: Pull do Docker para o contêiner de Extração de Frases-chave
titleSuffix: Azure Cognitive Services
description: Comando de pull do Docker para Extração de Frases-chave Contêiner
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 02dde8a27b9687e58bf1a09c1a951f306937f0d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906060"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Pull do Docker para o contêiner de Extração de Frases-chave

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner do registro de contêiner da Microsoft.

Para obter uma descrição completa das marcas disponíveis para os contêineres de Análise de Texto, consulte o contêiner [extração de frases-chave](https://go.microsoft.com/fwlink/?linkid=2018757) no Hub do Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```
