---
title: Exemplo de execução de contêiner do comando de execução do Docker
titleSuffix: Azure Cognitive Services
description: Comando de execução do Docker para Análise de Sentimento contêiner
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 96b41c27b282bdc05dafa8c69398a11e8dbf3b1b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91779835"
---
Para executar o contêiner *análise de sentimento v3* , execute o comando a seguir `docker run` . Substitua os espaços reservados abaixo pelos seus próprios valores:

| Espaço reservado | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave para o recurso de Análise de Texto. Você pode encontrá-lo na página de **ponto de extremidade e chave** do recurso, na portal do Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | O ponto de extremidade para acessar o API de Análise de Texto. Você pode encontrá-lo na página de **ponto de extremidade e chave** do recurso, na portal do Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Esse comando:

* Executa um contêiner de *análise de sentimento* a partir da imagem de contêiner
* Aloca um núcleo de CPU e 8 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.