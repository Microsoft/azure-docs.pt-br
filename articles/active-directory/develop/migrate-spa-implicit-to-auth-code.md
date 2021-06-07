---
title: Migrar aplicativo de página única do JavaScript da concessão implícita para o fluxo do código de autorização | Azure
titleSuffix: Microsoft identity platform
description: Como atualizar um SPA JavaScript usando MSAL.js 1.x e o fluxo de concessão implícita para MSAL.js 2.x e o fluxo do código de autorização com suporte para PKCE e CORS.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 3c11334fe1b4d77be6e64febfc1d3de6efa302c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100365933"
---
# <a name="migrate-a-javascript-single-page-app-from-implicit-grant-to-auth-code-flow"></a>Migrar um aplicativo de página única do JavaScript da concessão implícita para o fluxo do código de autorização

A Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) v2.0 dá suporte para o fluxo do código de autorização com PKCE e CORS para aplicativos de página única na plataforma de identidade da Microsoft. Siga as etapas das seções abaixo para migrar seu aplicativo MSAL.js 1.x usando a concessão implícita para MSAL.js 2.0+ (doravante *2. x*) e o fluxo do código de autorização.

A MSAL.js 2.x tem aprimoramentos em comparação com a MSAL.js 1.x, dando suporte ao fluxo do código de autorização no navegador em vez do fluxo de concessão implícita. A MSAL.js 2.x **NÃO** dá suporte ao fluxo implícito.

## <a name="migration-steps"></a>Etapas da migração

Para atualizar seu aplicativo para MSAL.js 2.x e o fluxo do código de autorização, há três etapas principais:

1. Mude seu URI de redirecionamento de [registro de aplicativo](#switch-redirect-uris-to-spa-platform) da plataforma **Web** para a plataforma de **aplicativo de página única**.
1. Atualize seu [código](#switch-redirect-uris-to-spa-platform) de MSAL.js 1.x para **2.x**.
1. Desabilite a [concessão implícita](#disable-implicit-grant-settings) no registro do aplicativo quando todos os aplicativos que compartilham o registro foram atualizados para MSAL.js 2.x e o fluxo do código de autorização.

As seções a seguir descrevem cada etapa com detalhes adicionais.

## <a name="switch-redirect-uris-to-spa-platform"></a>Mude os URIs de redirecionamento para a plataforma SPA

Se você quiser continuar usando o registro de aplicativo existente para seus aplicativos, use o portal do Azure para atualizar os URIs de redirecionamento do registro para a plataforma SPA. Isso habilita o fluxo do código de autorização com suporte a PKCE e CORS para aplicativos que usam o registro (você ainda precisa atualizar o código do aplicativo para MSAL.js v2.x).

Siga estas etapas para os registros de aplicativo que estão configurados atualmente com URIs de redirecionamento da plataforma **Web**:

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a> e selecione o locatário do **Azure Active Directory**.
1. Em **Registros de aplicativo**, selecione seu aplicativo e, em seguida, **Autenticação**.
1. No bloco da plataforma **Web**, em **URIs de redirecionamento**, selecione a faixa de aviso indicando que você deve migrar seus URIs.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-01-implicit-warning-banner.png" alt-text="Faixa de aviso de fluxo implícito no bloco do aplicativo Web no portal do Azure":::
1. Selecione *apenas* os URIs de redirecionamento cujos aplicativos usarão MSAL.js 2.x e, em seguida, selecione **Configurar**.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-02-select-redirect-uri.png" alt-text="Selecione o painel de URI de redirecionamento no painel do SPA do portal do Azure":::

Esses URIs de redirecionamento agora devem aparecer no bloco da plataforma **aplicativo de página única**, mostrando que o suporte a CORS com o fluxo do código de autorização e o PKCE está habilitado para esses URIs.

:::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-03-spa-redirect-uri-tile.png" alt-text="Bloco de aplicativo de página única no registro de aplicativos do portal do Azure":::

Você também pode [criar um registro de aplicativo](scenario-spa-app-registration.md) em vez de atualizar os URIs de redirecionamento em seu registro existente.

## <a name="update-your-code-to-msaljs-2x"></a>Atualize seu código para MSAL.js 2.x

No MSAL 1.x, você criou uma instância do aplicativo inicializando um [UserAgentApplication][msal-js-useragentapplication] da seguinte maneira:

```javascript
// MSAL 1.x
import * as msal from "msal";

const msalInstance = new msal.UserAgentApplication(config);
```

No MSAL 2.x, em vez disso, inicialize um [PublicClientApplication][msal-js-publicclientapplication]:

```javascript
// MSAL 2.x
import * as msal from "@azure/msal-browser";

const msalInstance = new msal.PublicClientApplication(config);
```

Para obter um passo a passo completo de como adicionar MSAL 2.x ao seu aplicativo, confira o [Tutorial: Conectar usuários e chamar a API do Microsoft Graph de um SPA (aplicativo de página única) do JavaScript usando o fluxo do código de autorização](tutorial-v2-javascript-auth-code.md).

Para obter alterações adicionais que talvez você precise fazer em seu código, consulte o [guia de migração](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/v1-migration.md) do GitHub.

## <a name="disable-implicit-grant-settings"></a>Desabilitar as configurações de concessão implícita

Depois de atualizar todos os aplicativos de produção que usam esse registro de aplicativo e sua ID de cliente para o MSAL 2.x e o fluxo do código de autorização, você deve desmarcar as configurações de concessão implícita no registro do aplicativo.

Quando você desmarca as configurações de concessão implícita no registro do aplicativo, o fluxo implícito é desabilitado para todos os aplicativos que usam o registro e sua ID do cliente.

**Não** desabilite o fluxo de concessão implícita antes de atualizar todos os aplicativos para MSAL.js 2.x e o [PublicClientApplication][msal-js-publicclientapplication].

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o fluxo de código de autorização, incluindo as diferenças entre os fluxos de código implícito e de autenticação, confira [Plataforma de identidade da Microsoft e o fluxo de código de autorização OAuth 2.0](v2-oauth2-auth-code-flow.md).

Se quiser se aprofundar no desenvolvimento de aplicativos de página única JavaScript na plataforma de identidade da Microsoft, a série de artigos [Cenário: aplicativo de página única](scenario-spa-overview.md) em várias partes pode ajudar você a começar.

<!-- LINKS - external -->
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html
