---
title: Definir política de cookie de leitor de imersão
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como definir a política de cookie para o leitor de imersão.
services: cognitive-services
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 02901e6b4a75257b2cda8dee84dbe3438dc15c18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332365"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Como definir a política de cookie para o leitor de imersão

O leitor de imersão desabilitará o uso do cookie por padrão. Se você habilitar o uso do cookie, o leitor de imersão poderá usar cookies para manter as preferências do usuário e controlar o uso do recurso. Se você habilitar o uso de cookies no leitor de imersão, considere os requisitos da política de conformidade do cookie da UE. É responsabilidade do aplicativo host obter qualquer consentimento do usuário necessário de acordo com a política de conformidade do cookie da UE.

A política de cookie pode ser definida por meio das [Opções](../reference.md#options)de leitura imersiva.

## <a name="enable-cookie-usage"></a>Habilitar uso de cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Desabilitar o uso do cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Próximas etapas

* Confira o [Início rápido do Node.js](../quickstarts/client-libraries.md?pivots=programming-language-nodejs) para ver o que mais você pode fazer com o SDK de Leitura Avançada usando Node.js
* Veja o [tutorial do Android](../tutorial-android.md) para saber o que mais você pode fazer com o SDK de Leitura Avançada usando o Java ou o Kotlin para Android
* Veja o [tutorial do iOS](../tutorial-ios.md) para saber o que mais você pode fazer com o SDK de Leitura Avançada usando o Swift para iOS
* Confira o [tutorial do Python](../tutorial-python.md) para ver o que mais você pode fazer com o SDK de Leitura Avançada usando Python
* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [Referência de SDK da Leitura Avançada](../reference.md)