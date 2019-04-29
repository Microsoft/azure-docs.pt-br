---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
origin.date: 09/13/2018
ms.date: 11/05/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: b8f961ad3fe4550b915253746d0f4f677c593a8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300540"
---
## <a name="add-the-applications-registration-to-your-code"></a>Adicionar o registro do aplicativo ao código

Nesta etapa, você precisa adicionar a ID do Cliente/Aplicativo ao projeto.

1. Abra `MainActivity` (em `app` > `java` > *`{host}.{namespace}`*)
2. Substitua a linha que começa com `final static String CLIENT_ID` por:
    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
3. Abra: `app` > `manifests` > `AndroidManifest.xml`
4. Adicione a seguinte atividade para `manifest\application`. A atividade`BrowserTabActivity` permite que a Microsoft faça um retorno de chamada ao aplicativo após concluir a autenticação:

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after Sign In-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />

            <!--Add in your scheme/host from registered redirect URI-->
            <!--By default, the scheme should be similar to 'msal[appId]' -->
            <data android:scheme="msal[Enter the application Id here]"
                android:host="auth" />
        </intent-filter>
    </activity>
    ```

<!-- ms.date: 11/05/2018 -->
