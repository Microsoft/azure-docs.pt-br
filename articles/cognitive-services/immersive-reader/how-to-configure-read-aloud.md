---
title: Configurar Ler em Voz Alta
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como configurar as várias opções para leitura em voz alta.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 8b45fe07b4bd42059199197bc5b99f20f6b2a8cf
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608709"
---
# <a name="how-to-configure-read-aloud"></a>Como configurar a leitura em voz alta

Este artigo demonstra como configurar as várias opções para leitura em voz alta no leitor de imersão.

## <a name="automatically-start-read-aloud"></a>Iniciar leitura em voz alta automaticamente

O `options` parâmetro contém todos os sinalizadores que podem ser usados para configurar a leitura em voz alta. Defina `autoplay` como `true` para habilitar o início automático de leitura em voz depois de iniciar o leitor de imersão.

```typescript
const options = {
    readAloudOptions: {
        autoplay: true
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

> [!NOTE]
> Devido a limitações do navegador, a reprodução automática não tem suporte no Safari.

## <a name="configure-the-voice"></a>Configurar a voz

Defina `voice` como `male` ou `female` . Nem todos os idiomas dão suporte a ambas as vozes. Para obter mais informações, consulte a página [suporte a idiomas](./language-support.md) .

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>Configurar velocidade de reprodução

Definir `speed` como um número entre `0.5` (50%) e `2.5` (250%) exaustiva. Os valores fora desse intervalo obterão clamped para 0,5 ou 2,5.

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>Próximas etapas

* Explorar a [Referência do SDK da Leitura Avançada](./reference.md)