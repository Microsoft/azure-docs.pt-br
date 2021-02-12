---
title: Registrar aplicativos móveis que chamam APIs da Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo móvel que chama APIs da Web (registro do aplicativo)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 902348063a24b50d1de0a821d9d6e67931cc53cd
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582529"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Registrar aplicativos móveis que chamam APIs da Web

Este artigo contém instruções para ajudá-lo a registrar um aplicativo móvel que você está criando.

## <a name="supported-account-types"></a>Tipos de conta com suporte

Os tipos de conta aos quais seus aplicativos móveis dão suporte dependem da experiência que você deseja habilitar e dos fluxos que você deseja usar.

### <a name="audience-for-interactive-token-acquisition"></a>Público-alvo para aquisição de token interativo

A maioria dos aplicativos móveis usa a autenticação interativa. Se seu aplicativo usar essa forma de autenticação, você poderá conectar usuários de qualquer [tipo de conta](quickstart-register-app.md).

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Público-alvo para autenticação integrada do Windows, nome de usuário-senha e B2C

Se você tiver um aplicativo Plataforma Universal do Windows (UWP), poderá usar a autenticação integrada do Windows para conectar usuários. Para usar a autenticação integrada do Windows ou a autenticação de senha de nome de usuário, seu aplicativo precisa conectar usuários em seu próprio locatário de desenvolvedor de linha de negócios (LOB). Em um cenário ISV (fornecedor independente de software), seu aplicativo pode conectar usuários em Azure Active Directory organizações. Esses fluxos de autenticação não têm suporte para contas pessoais da Microsoft.

Você também pode conectar usuários usando identidades sociais que passam uma política e autoridade B2C. Para usar esse método, você pode usar somente a autenticação interativa e a autenticação de senha de nome de usuário. Nome de usuário-a autenticação de senha atualmente tem suporte apenas em Xamarin. iOS, Xamarin. Android e UWP.

Para obter mais informações, consulte [cenários e fluxos de autenticação com suporte](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) e [cenários e plataformas e linguagens com suporte](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages).

## <a name="platform-configuration-and-redirect-uris"></a>Configuração de plataforma e URIs de redirecionamento

### <a name="interactive-authentication"></a>Autenticação interativa

Quando você cria um aplicativo móvel que usa a autenticação interativa, a etapa de registro mais crítica é o URI de redirecionamento. Você pode definir a autenticação interativa por meio da [configuração de plataforma na folha **autenticação**](https://aka.ms/MobileAppReg).

Essa experiência permitirá que seu aplicativo obtenha SSO (logon único) por meio de Microsoft Authenticator (e Portal da Empresa do Intune no Android). Ele também dará suporte a políticas de gerenciamento de dispositivos.

O portal de registro de aplicativo fornece uma experiência de visualização para ajudá-lo a calcular o URI de resposta orientado para aplicativos iOS e Android:

1. No portal de registro de aplicativo, selecione **autenticação**  >  **Experimente a nova experiência**.

   ![A folha de autenticação, onde você escolhe uma nova experiência](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Selecione **Adicionar uma plataforma**.

   ![Adicionar uma plataforma](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Quando houver suporte para a lista de plataformas, selecione **Ios**.

   ![Escolher um aplicativo móvel](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Insira a ID do pacote e, em seguida, selecione **registrar**.

   ![Insira a ID do pacote](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Quando você concluir as etapas, o URI de redirecionamento será computado para você, como na imagem a seguir.

![O URI de redirecionamento resultante](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Se preferir configurar manualmente o URI de redirecionamento, você poderá fazer isso por meio do manifesto do aplicativo. Este é o formato recomendado para o manifesto:

- **Ios**: `msauth.<BUNDLE_ID>://auth`
  - Por exemplo, digite `msauth.com.yourcompany.appName://auth`
- **Android**: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Você pode gerar o hash de assinatura do Android usando a chave de versão ou a chave de depuração por meio do comando keytool.

### <a name="username-password-authentication"></a>Nome de usuário-autenticação de senha

Se seu aplicativo usar apenas a autenticação de nome de usuário-senha, você não precisará registrar um URI de redirecionamento para seu aplicativo. Esse fluxo faz uma viagem de ida e volta para a plataforma de identidade da Microsoft. Seu aplicativo não será chamado de volta em nenhum URI específico.

No entanto, identifique seu aplicativo como um aplicativo cliente público. Para fazer isso:

1. Ainda no <a href="https://portal.azure.com/" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> portal do Azure</a>, selecione seu aplicativo no **registros de aplicativo** e, em seguida, selecione **autenticação**.
1. Em **Configurações avançadas**,  >  **permitir fluxos de cliente público**  >  **habilite os seguintes fluxos móveis e de área de trabalho:**, selecione **Sim**.

   :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Habilitar a configuração de cliente público no painel de autenticação no portal do Azure":::

## <a name="api-permissions"></a>Permissões de API

Os aplicativos móveis chamam APIs em nome do usuário conectado. Seu aplicativo precisa solicitar permissões delegadas. Essas permissões também são chamadas de escopos. Dependendo da experiência desejada, você pode solicitar permissões delegadas estaticamente por meio do portal do Azure. Ou você pode solicitá-los dinamicamente em tempo de execução.

Ao registrar estaticamente as permissões, você permite que os administradores aprovem facilmente seu aplicativo. O registro estático é recomendado.

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, configuração de [código do aplicativo](scenario-mobile-app-configuration.md).
