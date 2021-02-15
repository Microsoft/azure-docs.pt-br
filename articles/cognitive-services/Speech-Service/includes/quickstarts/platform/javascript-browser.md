---
title: 'Início Rápido: Instalação da plataforma do SDK de Fala para JavaScript (navegador) – Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para configurar sua plataforma para usar JavaScript (navegador) com o SDK do serviço de Fala.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-js
ms.openlocfilehash: ec73394e184e3c16fbc4eebf2a1090c9e52ddee4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188143"
---
Este guia mostra como instalar o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para JavaScript para uso com uma página da Web.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>Criar uma nova pasta Site

Crie uma nova pasta vazia. Caso deseje hospedar o exemplo em um servidor Web, certifique-se de que ele possa acessar a pasta.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Descompactar o SDK de Fala para JavaScript nessa pasta

Baixe o SDK de Fala como um [pacote .zip](https://aka.ms/csspeech/jsbrowserpackage) e descompacte-o na pasta recém-criada. Isso resulta no desempacotamento de cinco arquivos:
* `microsoft.cognitiveservices.speech.sdk.bundle.js` Uma versão legível por humanos do SDK de Fala.
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map` Um arquivo de mapa usado para depurar o código do SDK.
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts` Definições de objeto para uso com TypeScript
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js` Uma versão reduzida do SDK de fala.
* `speech-processor.js` Código para aprimorar o desempenho em alguns navegadores.

## <a name="create-an-indexhtml-page"></a>Criar uma página index.html

Crie um novo arquivo na pasta, chamada `index.html`, e abra este arquivo com um editor de texto.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [windows](../quickstart-list.md)]