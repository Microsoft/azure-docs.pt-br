---
title: 'Tutorial: Registrar um aplicativo de página única'
titleSuffix: Azure AD B2C
description: Siga este tutorial para saber como registrar um SPA (aplicativo de página única) no Azure Active Directory B2C usando o portal do Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d2eaf1dce432821dcfc693dc69dcf975a3d8be8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92503854"
---
# <a name="tutorial-register-a-single-page-application-spa-in-azure-active-directory-b2c"></a>Tutorial: Registrar um SPA (aplicativo de página única) no Azure Active Directory B2C

Antes que os [aplicativos](application-types.md) possam interagir com o Azure Active Directory B2C (Azure AD B2C), eles deverão ser registrados em um locatário gerenciado por você. Este tutorial mostra como registrar um "SPA" (aplicativo de página única) usando o portal do Azure.

## <a name="overview-of-authentication-options"></a>Visão geral das opções de autenticação

Muitos aplicativos Web modernos são criados como "SPAs" (aplicativos de página única). Os desenvolvedores os escrevem usando JavaScript ou uma estrutura SPA, como Angular, Vue e React. Esses aplicativos são executados em um navegador da Web e têm características de autenticação diferentes dos aplicativos Web do lado do servidor tradicionais.

O Azure AD B2C fornece **duas** opções para permitir que aplicativos de página única conectem usuários e obtenham tokens para acessar serviços de back-end ou APIs Web:

### <a name="authorization-code-flow-with-pkce"></a>Fluxo do código de autorização (com PKCE)
- [Fluxo do código de autorização do OAuth 2.0 (com PKCE)](./authorization-code-flow.md). O fluxo de código de autorização permite que o aplicativo troque um código de autorização para tokens de **ID** representarem o usuário autenticado e tokens de **Acesso** necessários para chamar APIs protegidas. Além disso, ele retorna tokens de **Atualização** que fornecem acesso de longo prazo a recursos em nome de usuários sem exigir interação com esses usuários. 

Essa é a abordagem **recomendada**. Ter tokens de atualização com tempo de vida limitado ajuda o aplicativo a se adaptar às [limitações de privacidade de cookie dos navegadores modernos](../active-directory/develop/reference-third-party-cookies-spas.md), como o Safari ITP.

Para aproveitar esse fluxo, seu aplicativo pode usar uma biblioteca de autenticação que dá suporte a ela, como [MSAL.js 2.x](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa). 

![Aplicativos de página única – autenticação](./media/tutorial-single-page-app/spa-app-auth.svg)

### <a name="implicit-grant-flow"></a>Fluxo de concessão implícita
- [Fluxo implícito do OAuth 2.0](implicit-flow-single-page-application.md). Algumas estruturas, como [MSAL.js 1.x](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp), dão suporte apenas ao fluxo de concessão implícita. O fluxo de concessão implícita permite que o aplicativo obtenha os tokens de **ID** e **Acesso**. Diferentemente do fluxo de código de autorização, o fluxo de concessão implícita não retorna um **token de Atualização**. 

![Aplicativo de página única – implícito](./media/tutorial-single-page-app/spa-app.svg)

Esse fluxo de autenticação não inclui cenários de aplicativos que usam estruturas JavaScript de multiplataforma, como o Electron e React-Native. Esses cenários exigem mais funcionalidades para interação com as plataformas nativas.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se você ainda não criou seu próprio [locatário do Azure AD B2C](tutorial-create-tenant.md), crie um agora. É possível usar um locatário existente do Azure AD B2C.

## <a name="register-the-spa-application"></a>Registrar o aplicativo SPA

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de aplicativo** e **Novo registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *spaapp1*.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer provedor de identidade ou diretório organizacional (para autenticar usuários com fluxos dos usuários)**
1. Em **URI de Redirecionamento**, selecione **SPA (Aplicativo de página única)** e insira `https://jwt.ms` na caixa de texto da URL.

    O URI de redirecionamento é o ponto de extremidade para o qual o usuário é enviado pelo servidor de autorização (Azure AD B2C, nesse caso) depois de concluir sua interação com o usuário e para o qual um token de acesso ou código de autorização é enviado após uma autorização bem-sucedida. Em um aplicativo de produção, normalmente é um ponto de extremidade de acesso público no qual o aplicativo está em execução, como `https://contoso.com/auth-response`. Para fins de teste, como este tutorial, você pode defini-lo como `https://jwt.ms`, um aplicativo da Web de propriedade da Microsoft que exibe o conteúdo decodificado de um token (o conteúdo do token nunca deixa o navegador). Durante o desenvolvimento do aplicativo, é possível adicionar o ponto de extremidade onde o aplicativo escuta localmente, como `http://localhost:5000`. Adicione e modifique URIs de redirecionamento nos aplicativos registrados a qualquer momento.

    As restrições a seguir se aplicam a URLs de redirecionamento:

    * A URL de resposta precisa começar com o esquema `https`, a menos que esteja usando `localhost`.
    * A URL de resposta diferencia maiúsculas de minúsculas. As letras maiúsculas e minúsculas devem corresponder às letras maiúsculas e minúsculas do caminho da URL do aplicativo em execução. Por exemplo, se o aplicativo incluir `.../abc/response-oidc` como parte de seu caminho, não especifique `.../ABC/response-oidc` na URL de resposta. Como o navegador da Web trata os caminhos diferenciando maiúsculas de minúsculas, os cookies associados a `.../abc/response-oidc` podem ser excluídos se forem redirecionados para a URL de `.../ABC/response-oidc` com maiúsculas e minúsculas não correspondentes.

1. Em **Permissões**, marque a caixa de seleção *Dar consentimento do administrador às permissões OpenID e offline_access*.
1. Selecione **Registrar**.


## <a name="enable-the-implicit-flow"></a>Habilitar o fluxo implícito
Se estiver usando o fluxo implícito, você precisará habilitar o fluxo de concessão implícita no registro do aplicativo.

1. No menu à esquerda, em **Gerenciar**, selecione **Autenticação**.
1. Em **Concessão implícita**, selecione ambas as caixas de seleção **Tokens de acesso** e **Tokens de ID**.
1. Clique em **Salvar**.

## <a name="migrate-from-the-implicit-flow"></a>Fazer a migração do fluxo implícito

Se você tem um aplicativo existente que usa o fluxo implícito, recomendamos migrar o usando o fluxo de código de autorização usando uma estrutura que dê suporte a ele, como [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

Quando todos os aplicativos de página única de produção representados por um registro de aplicativo estiverem usando o fluxo de código de autorização, desabilite as configurações de fluxo de concessão implícita. 

1. No menu à esquerda, em **Gerenciar**, selecione **Autenticação**.
1. Em **Concessão implícita**, cancele a seleção de ambas as caixas de seleção **Tokens de acesso** e **Tokens de ID**.
1. Clique em **Salvar**.

Os aplicativos que usam o fluxo implícito continuarão funcionando se você deixar o fluxo implícito habilitado (marcado).

* * *

## <a name="next-steps"></a>Próximas etapas

A seguir você aprenderá como criar fluxos de usuário para permitir que os usuários se inscrevam, acessem e gerenciem perfis.

> [!div class="nextstepaction"]
> [Criação de fluxos de usuário no Azure Active Directory B2C >](tutorial-create-user-flows.md)
