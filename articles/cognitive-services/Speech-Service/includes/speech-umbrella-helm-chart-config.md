---
title: Instalar contêineres de fala
titleSuffix: Azure Cognitive Services
description: Detalha as opções de configuração do gráfico de Helm de fala.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "67717225"
---
### <a name="speech-umbrella-chart"></a>Fala (gráfico de abrangência)

Os valores no gráfico "abrangência" de nível superior substituem os valores de Subgráficos correspondentes. Portanto, todos os valores personalizados locais devem ser adicionados aqui.

|Parâmetro|Descrição|Padrão|
| -- | -- | -- | -- |
| `speechToText.enabled` | Se o serviço de **fala a texto** está habilitado. | `true` |
| `speechToText.verification.enabled` | Se a `helm test` capacidade de serviço de **fala a texto** está habilitada. | `true` |
| `speechToText.verification.image.registry` | O repositório de imagens do Docker que `helm test` o usa para testar o serviço de **fala em texto** . O Helm cria um pod separado dentro do cluster para testar e efetua pull da imagem de *uso de teste* desse registro. | `docker.io` |
| `speechToText.verification.image.repository` | O repositório de imagens do Docker que `helm test` o usa para testar o serviço de **fala em texto** . O pod de teste do Helm usa esse repositório para efetuar pull da imagem *de uso de teste* . | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | A marca de imagem do Docker `helm test` usada com para o serviço de **fala a texto** . O pod de teste Helm usa essa marca para efetuar pull da imagem *de uso de teste* . | `latest` |
| `speechToText.verification.image.pullByHash` | Se a imagem do Docker de *teste de uso* é retirada por hash. Se `true` ,`speechToText.verification.image.hash` deve ser adicionado, com um valor de hash de imagem válido. | `false` |
| `speechToText.verification.image.arguments` | Os argumentos usados para executar a imagem do Docker de *uso de teste* . O pod de teste Helm passa esses argumentos para o contêiner `helm test`durante a execução. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Se o serviço de **conversão de texto em fala** está habilitado. | `true` |
| `textToSpeech.verification.enabled` | Se a `helm test` capacidade de serviço de **fala a texto** está habilitada. | `true` |
| `textToSpeech.verification.image.registry` | O repositório de imagens do Docker que `helm test` o usa para testar o serviço de **fala em texto** . O Helm cria um pod separado dentro do cluster para testar e efetua pull da imagem de *uso de teste* desse registro. | `docker.io` |
| `textToSpeech.verification.image.repository` | O repositório de imagens do Docker que `helm test` o usa para testar o serviço de **fala em texto** . O pod de teste do Helm usa esse repositório para efetuar pull da imagem *de uso de teste* . | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | A marca de imagem do Docker `helm test` usada com para o serviço de **fala a texto** . O pod de teste Helm usa essa marca para efetuar pull da imagem *de uso de teste* . | `latest` |
| `textToSpeech.verification.image.pullByHash` | Se a imagem do Docker de *teste de uso* é retirada por hash. Se `true` ,`textToSpeech.verification.image.hash` deve ser adicionado, com um valor de hash de imagem válido. | `false` |
| `textToSpeech.verification.image.arguments` | Os argumentos a serem executados com a imagem do Docker de *uso de teste* . O pod de teste Helm passa esses argumentos para o contêiner `helm test`durante a execução. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |