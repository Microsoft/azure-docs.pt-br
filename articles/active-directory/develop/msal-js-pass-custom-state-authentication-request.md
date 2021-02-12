---
title: Passar o estado personalizado em solicitações de autenticação (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como passar um valor de parâmetro de estado personalizado na solicitação de autenticação usando a biblioteca de autenticação da Microsoft para JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5c1fad96ea6e3b75b3afdfd4a4d3baac43308541
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063664"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Passar o estado personalizado em solicitações de autenticação usando MSAL.js

O parâmetro *State* , conforme definido pelo OAuth 2,0, está incluído em uma solicitação de autenticação e também é retornado na resposta do token para impedir ataques de solicitação entre sites forjado. Por padrão, a biblioteca de autenticação da Microsoft para JavaScript (MSAL.js) passa um valor de parâmetro de *estado* exclusivo gerado aleatoriamente nas solicitações de autenticação.

O parâmetro State também pode ser usado para codificar informações do estado do aplicativo antes do redirecionamento. Você pode passar o estado do usuário no aplicativo, como a página ou a exibição em que eles estavam, como entrada para esse parâmetro. A biblioteca de MSAL.js permite que você passe seu estado personalizado como parâmetro de estado no `Request` objeto:

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> Se você quiser ignorar um token em cache e ir para o servidor, passe o booliano para `forceRefresh` o objeto AuthenticationParameters usado para fazer uma solicitação de logon/token.
> `forceRefresh` Não deve ser usado por padrão, devido ao impacto no desempenho em seu aplicativo.
> Depender do cache dará aos seus usuários uma experiência melhor.
> Ignorar o cache só deve ser usado em cenários em que você saiba que os dados atualmente armazenados em cache não têm informações atualizadas.
> Como uma ferramenta de administração que adiciona funções a um usuário que precisa obter um novo token com funções atualizadas.

Por exemplo:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

O estado transmitido é acrescentado ao GUID exclusivo definido pelo MSAL.js ao enviar a solicitação. Quando a resposta é retornada, o MSAL.js verifica se há uma correspondência de estado e retorna o estado personalizado passado no `Response` objeto como `accountState` .

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

Para saber mais, leia sobre como [criar um aplicativo de página única (Spa)](scenario-spa-overview.md) usando MSAL.js.