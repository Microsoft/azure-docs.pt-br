---
title: Introdução à API de Reconhecimento de Fala do Bing em JavaScript | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use a API de Reconhecimento de Fala do Bing em Serviços Cognitivos para desenvolver aplicativos que convertam continuamente áudio de fala em texto.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17901ad40a48e9ee8d1a8b872b04ad52b75b3a52
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515204"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Introdução à API de Reconhecimento de Fala em JavaScript

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

É possível desenvolver aplicativos que convertem áudio de fala em texto usando a API de Reconhecimento de Fala. A biblioteca de clientes JavaScript usa o [protocolo WebSocket do Serviço de Fala](../API-Reference-REST/websocketprotocol.md), que permite falar e receber texto transcrito simultaneamente. Este artigo ajuda-o a começar a usar a API de Reconhecimento de Fala em JavaScript.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Inscreva-se na API de Reconhecimento de Fala e receba uma chave de assinatura de avaliação gratuita

O Speech API é parte dos Serviços Cognitivos. É possível obter chaves de assinatura de avaliação gratuita na página de [assinatura dos Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/). Após selecionar o Speech API, selecione **Obter chave de API** para obter a chave. Isso retorna uma chave primária e secundária. Ambas as chaves estão vinculadas à mesma cota, portanto, é possível usar uma das chaves.

> [!IMPORTANT]
> Obtenha uma chave de assinatura. Antes de poder usar as bibliotecas de clientes do Speech, será necessário ter uma [chave de assinatura](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Introdução

Nesta seção, mostraremos as etapas necessárias para carregar uma página HTML de exemplo. O exemplo está localizado em nosso [repositório do GitHub](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). É possível **abrir o exemplo diretamente** do repositório ou **abrir o exemplo de uma cópia local** do repositório.

> [!NOTE]
> Alguns navegadores bloqueiam o acesso ao microfone em uma origem não segura. Portanto, é recomendável hospedar 'exemplo'/'seu aplicativo' no https para que ele funcione em todos os navegadores com suporte.

### <a name="open-the-sample-directly"></a>Abra o exemplo diretamente

Adquira uma chave de assinatura, conforme descrito acima. Em seguida, abra o [link para o exemplo](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Isso carregará a página no navegador padrão (Renderizado usando [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Abra o exemplo de uma cópia local

Para experimentar o exemplo localmente, clone esse repositório:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

compile as fontes TypeScript e agrupe-as em um arquivo JavaScript único ([npm](https://www.npmjs.com/) precisa ser instalado no computador). Altere para a raiz do repositório clonado e execute os comandos:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Abra `samples\browser\Sample.html` no seu navegador favorito.

## <a name="next-steps"></a>Próximas etapas

Mais informações sobre como incluir o SDK na sua própria página da Web estão disponíveis [aqui](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Comentários

- A API de Reconhecimento de Fala dá suporte a três [modos de reconhecimento](../concepts.md#recognition-modes). É possível alternar o modo, atualizando a função **Setup()** localizada no arquivo Sample.html. O exemplo define o modo para `Interactive` por padrão. Para alterar o modo, atualize o parâmetro `SR.RecognitionMode.Interactive` para outro modo. Por exemplo, altere o parâmetro para `SR.RecognitionMode.Conversation`.
- Para obter uma lista completa de linguagens com suporte, consulte [Linguagens com suporte](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Tópicos relacionados

- [Repositório de exemplo da API de Reconhecimento de Dala do JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Introdução à API REST](GetStartedREST.md)
