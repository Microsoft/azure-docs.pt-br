---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 594b4090fcdfa18432563269743b88cb9d2d723b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300541"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Adicionar as informações de registro do aplicativo ao aplicativo

Nesta etapa, você precisa adicionar a ID do Aplicativo ao projeto:

1. Em `ViewController.swift`, substitua a linha que começa com "`let kClientID`" por:

```swift
let kClientID = "[Enter the application Id here]"
```

<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Pressione CTRL e clique em <code>Info.plist</code> para abrir o menu contextual e, em seguida, clique em: <code>Open As</code> > <code>Source Code</code>
</li>
<li>
No nó raiz <code>dict</code>, adicione o seguinte:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Enter the application Id here]</string>
        </array>
    </dict>
</array>
```
