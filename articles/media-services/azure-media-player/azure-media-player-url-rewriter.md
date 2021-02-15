---
title: Regravador de URL do Player de Mídia do Azure
description: O Player de Mídia do Azure reescreverá uma determinada URL dos Serviços de Mídia do Azure para fornecer fluxos para SMOOTH, DASH, HLS v3 e HLS v4.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: b037eec13fda0b5ec16a4f2f53ad2a64fb5f8da1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91275550"
---
# <a name="url-rewriter"></a>Regravador de URL #

Por padrão, o Player de Mídia do Azure reescreverá uma determinada URL dos Serviços de Mídia do Azure para fornecer fluxos para SMOOTH, DASH, HLS v3 e HLS v4. Por exemplo, se a fonte for fornecida da seguinte maneira, o Player de Mídia do Azure garantidamente tentará reproduzir todos os protocolos acima:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

No entanto, se você não quiser usar o regravador de URL, poderá optar por isso adicionando a propriedade `disableUrlRewriter` ao parâmetro. Isso significa que todas as informações passadas para as fontes são passadas diretamente para o player, sem modificação.  Aqui está um exemplo de adição de duas fontes ao player, em um streaming DASH e em um SMOOTH.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

ou

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

Além disso, se desejar, você poderá usar o parâmetro `streamingFormats` para especificar os formatos de streaming específicos nos quais quiser que o Player de Mídia do Azure faça a regravação. As opções incluem `DASH`, `SMOOTH`, `HLSv3`, `HLSv4` e `HLS`. A diferença entre HLS e HLSv3 e v4 é que o formato HLS é compatível com a reprodução de conteúdo FairPlay. Os formatos v3 e v4 não são compatíveis com a FairPlay. Isso será útil se você não tiver uma política de entrega para um protocolo específico disponível.  Veja um exemplo de quando um protocolo DASH não está habilitado com seu ativo.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

ou

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

As duas opções acima podem ser usadas em combinação uma com a outra para várias circunstâncias, com base em seu ativo específico.

> [!NOTE]
> As informações de proteção do Widevine só persistem no protocolo DASH.

## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)