---
title: Configurar a tradução
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como configurar as várias opções de tradução.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 0a6ed6ecea216a36cfc9da4ef36a2bddc69cc6a8
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92633137"
---
# <a name="how-to-configure-translation"></a>Como configurar a tradução

Este artigo demonstra como configurar as várias opções de tradução no leitor de imersão.

## <a name="configure-translation-language"></a>Configurar idioma de tradução

O `options` parâmetro contém todos os sinalizadores que podem ser usados para configurar a tradução. Defina o `language` parâmetro para o idioma para o qual você deseja traduzir. Consulte o [suporte ao idioma](./language-support.md) para obter a lista completa de idiomas com suporte.

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>Traduzir automaticamente o documento na carga

Defina `autoEnableDocumentTranslation` como `true` para habilitar a tradução automática de todo o documento quando o leitor de imersão for carregado.

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>Habilitar automaticamente a tradução automática

Defina `autoEnableWordTranslation` como `true` para habilitar a tradução de uma única palavra.

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>Próximas etapas

* Explorar a [Referência do SDK da Leitura Avançada](./reference.md)