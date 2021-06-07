---
title: Configurar a tradução
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como configurar as várias opções da tradução.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: bb90cb3a86acb6a94fa92c33d78ec596659e105f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608692"
---
# <a name="how-to-configure-translation"></a>Como configurar a tradução

Este artigo demonstra como configurar as várias opções da tradução na Leitura Avançada.

## <a name="configure-translation-language"></a>Configurar o idioma da tradução

O parâmetro `options` contém todos os sinalizadores que podem ser usados para configurar a tradução. Defina o parâmetro `language` como o idioma para o qual você quer traduzir. Veja a lista completa de idiomas com suporte em [Suporte ao idioma](./language-support.md).

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>Traduzir automaticamente o documento ao carregar

Defina `autoEnableDocumentTranslation` como `true` para habilitar a tradução automática de todo o documento quando a Leitura Avançada é carregada.

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>Habilitar automaticamente a tradução de palavra

Defina `autoEnableWordTranslation` como `true` para habilitar a tradução de uma palavra.

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>Próximas etapas

* Explorar a [Referência do SDK da Leitura Avançada](./reference.md)