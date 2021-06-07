---
title: Registrar aplicativos de página única (SPA) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo de página única (registro de aplicativo)
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: 9366bb5b2bb5820245ec1b699bbf2ddda0dd9f9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103168"
---
# <a name="single-page-application-app-registration"></a>Aplicativo de página única: Registro do aplicativo

Para registrar um aplicativo de página única (SPA) na plataforma de identidade da Microsoft, conclua as etapas a seguir. As etapas de registro diferem entre MSAL.js 1,0, que é compatível com o fluxo de concessão implícita, e MSAL.js 2,0, que é compatível com o fluxo de código de autorização com PKCE.

## <a name="create-the-app-registration"></a>Criar o registro do aplicativo

Para aplicativos baseados em MSAL.js 1.0 e 2.0, comece concluindo as etapas a seguir para criar o registro inicial do aplicativo.

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
1. Insira um **Nome** para seu aplicativo. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
1. Selecione os **Tipos de conta compatíveis** para o aplicativo. **NÃO** insira **URI de redirecionamento**. Veja a descrição dos diferentes tipos de conta em [Registrar um aplicativo](quickstart-register-app.md).
1. Selecione **Registrar** para criar o registro do aplicativo.

Em seguida, configure o registro do aplicativo com um **URI de redirecionamento** para especificar para onde a plataforma de identidade da Microsoft deve redirecionar o cliente juntamente com os tokens de segurança. Use as etapas apropriadas para a versão do MSAL.js que você está usando em seu aplicativo:

- [MSAL.js 2.0 com fluxo de código de autorização](#redirect-uri-msaljs-20-with-auth-code-flow) (recomendado)
- [MSAL.js 1.0 com fluxo implícito](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>URI de redirecionamento: [MSAL.js 2.0 com fluxo de código de autorização](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

Siga estas etapas para adicionar um URI de redirecionamento para um aplicativo que usa MSAL.js 2.0 ou posterior. O MSAL.js 2.0+ é compatível com o fluxo de código de autorização com PKCE e CORS em resposta às [restrições de cookie de terceiros do navegador](reference-third-party-cookies-spas.md). O fluxo de concessão implícita não é compatível com o MSAL.js 2.0+.

1. No portal do Azure, selecione o registro de aplicativo que você criou anteriormente em [Criar o registro do aplicativo](#create-the-app-registration).
1. Em **Gerenciar**, escolha **Autenticação** > **Adicionar uma plataforma**.
1. Em **Aplicativos Web**, selecione o bloco **Aplicativo de página única**.
1. Em **URIs de redirecionamento**, insira um [URI de redirecionamento](reply-url.md). **NÃO** marque as caixas de seleção em **Concessão implícita e fluxos híbridos**.
1. Selecione **Configurar** para concluir a adição do URI de redirecionamento.

Você concluiu o registro do seu aplicativo de página única (SPA) e configurou um URI de redirecionamento para o qual o cliente será redirecionado e todos os tokens de segurança serão enviados. Ao configurar o URI de redirecionamento usando o bloco **Aplicativo de página única** no painel **Adicionar uma plataforma**, o registro do aplicativo é configurado para compatibilidade com o fluxo de código de autorização com PKCE e CORS.

Siga o [tutorial](tutorial-v2-javascript-auth-code.md) para maiores orientações.

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>URI de redirecionamento: [MSAL.js 1.0 com fluxo implícito](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)

Siga estas etapas para adicionar um URI de redirecionamento para um aplicativo de página única que usa MSAL.js 1.3 ou anterior e o fluxo de concessão implícita. Os aplicativos que usam MSAL.js 1.3 ou anterior não são compatíveis com o fluxo de código de autorização.

1. No portal do Azure, selecione o registro de aplicativo que você criou anteriormente em [Criar o registro do aplicativo](#create-the-app-registration).
1. Em **Gerenciar**, escolha **Autenticação** > **Adicionar uma plataforma**.
1. Em **Aplicativos Web**, selecione o bloco **Aplicativo de página única**.
1. Em **URIs de redirecionamento**, insira um [URI de redirecionamento](reply-url.md).
1. Habilite **Concessão implícita e fluxos híbridos**:
    - Se o seu aplicativo conectar usuários, selecione **Tokens de ID**.
    - Se o seu aplicativo também precisar chamar uma API Web protegida, selecione **Tokens de acesso**. Para obter mais informações sobre esses tipos de token, confira [Tokens de ID](id-tokens.md) e [Tokens de acesso](access-tokens.md).
1. Selecione **Configurar** para concluir a adição do URI de redirecionamento.

Você concluiu o registro do seu aplicativo de página única (SPA) e configurou um URI de redirecionamento para o qual o cliente será redirecionado e todos os tokens de segurança serão enviados. Quando selecionou **Tokens de ID**, **Tokens de acesso** ou as duas opções, você habilitou o fluxo de concessão implícita.

Siga o [tutorial](tutorial-v2-javascript-spa.md) para maiores orientações.

## <a name="note-about-authorization-flows"></a>Observação sobre fluxos de autorização

Por padrão, um registro de aplicativo criado usando a configuração de plataforma de aplicativo de página única habilita o fluxo de código de autorização. Para aproveitar esse fluxo, seu aplicativo deve usar o MSAL.js 2.0 ou posterior.

Como mencionado anteriormente, aplicativos de página única usando MSAL.js 1.3 são restritos ao fluxo de concessão implícita. As atuais [melhores práticas de OAuth 2.0](v2-oauth2-auth-code-flow.md) recomendam o uso do fluxo de código de autorização no lugar do fluxo implícito para SPAs. Ter tokens de atualização com tempo de vida limitado também ajuda o aplicativo a se adaptar às [limitações de privacidade de cookie dos navegadores modernos](reference-third-party-cookies-spas.md), como o Safari ITP.

Se todos os aplicativos de uma página de produção representados por um registro de aplicativo estiverem usando o MSAL.js 2.0 e o fluxo de código de autorização, desmarque as configurações de concessão implícita no painel **Autenticação** do registro do aplicativo, no portal do Azure. Os aplicativos que usam MSAL.js 1.x e o fluxo implícito continuarão a funcionar se você deixar o fluxo implícito habilitado (marcado).

## <a name="next-steps"></a>Próximas etapas

Configure o código do aplicativo para usar o registro do aplicativo criado nas etapas anteriores: [configuração do código do aplicativo](scenario-spa-app-configuration.md).
