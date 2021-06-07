---
title: Registrar aplicativos móveis que chamam APIs da Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo móvel que chama APIs Web (registro de aplicativo)
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
ms.openlocfilehash: 6385f03556d155941139b77333d6f4a25081fe67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103151"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Registrar aplicativos móveis que chamam APIs da Web

Este artigo contém instruções para registrar um aplicativo móvel que você está criando.

## <a name="supported-account-types"></a>Tipos de conta com suporte

Os tipos de conta compatíveis com seus aplicativos móveis dependem da experiência que você deseja habilitar e dos fluxos que você deseja usar.

### <a name="audience-for-interactive-token-acquisition"></a>Público-alvo para aquisição de token interativo

A maioria dos aplicativos móveis usa a autenticação interativa. Quando o aplicativo usa essa forma de autenticação, os usuários podem entrar usando qualquer [tipo de conta](quickstart-register-app.md).

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Público-alvo para autenticação integrada do Windows, nome de usuário/senha e B2C

Com um aplicativo da Plataforma Universal do Windows (UWP), é possível usar a autenticação integrada do Windows para a entrada de usuários. Para usar a autenticação integrada do Windows ou a autenticação de nome de usuário e senha, o aplicativo precisa conectar usuários em um locatário próprio de desenvolvedor de linha de negócios (LOB). Em um cenário de ISV (fornecedor independente de software), o aplicativo pode conectar usuários em organizações do Azure Active Directory. Esses fluxos de autenticação não são compatíveis com contas pessoais da Microsoft.

Você também pode conectar usuários usando identidades sociais aprovadas por uma autoridade de B2C e uma política. Para usar esse método, você pode usar somente a autenticação interativa e a autenticação de nome de usuário e senha. Atualmente, a autenticação com nome de usuário e senha atualmente tem suporte apenas em Xamarin.iOS, Xamarin.Android e UWP.

Veja mais informações em [Cenários e fluxos de autenticação com suporte](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) e [Cenários e plataformas e idiomas com suporte](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages).

## <a name="platform-configuration-and-redirect-uris"></a>Configuração da plataforma e URIs de redirecionamento

### <a name="interactive-authentication"></a>Autenticação interativa

Quando você cria um aplicativo móvel que usa a autenticação interativa, a etapa de registro mais crítica é o URI de redirecionamento. Você pode definir a autenticação interativa por meio da [configuração de plataforma **na folha** Autenticação](https://aka.ms/MobileAppReg).

Essa experiência permitirá que o aplicativo obtenha o SSO (logon único) por meio do Microsoft Authenticator (e do Portal da Empresa do Intune no Android). Ele também dará suporte para políticas de gerenciamento de dispositivos.

O portal de registro de aplicativo oferece uma experiência de visualização para calcular o URI de resposta agenciado de aplicativos iOS e Android:

1. No portal de registro de aplicativo, selecione **Autenticação** > **Experimente agora a nova experiência**.

   ![A folha Autenticação, em que você escolhe uma nova experiência](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Selecione **Adicionar uma plataforma**.

   ![Adicionar uma plataforma](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Quando houver suporte para a lista de plataformas, selecione **iOS**.

   ![Escolher um aplicativo móvel](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Insira a ID do pacote e, em seguida, selecione **Registrar**.

   ![Insira a ID do pacote](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Quando você executa as etapas, o URI de redirecionamento é calculado, como na imagem a seguir.

![O URI de redirecionamento resultante](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Se preferir configurar manualmente o URI de redirecionamento, você poderá fazer isso por meio do manifesto do aplicativo. Este é o formato recomendado para o manifesto:

- **iOS**: `msauth.<BUNDLE_ID>://auth`
  - Por exemplo, digite `msauth.com.yourcompany.appName://auth`
- **Android**: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Para gerar o hash de assinatura do Android, use a chave de versão ou a chave de depuração por meio do comando KeyTool.

### <a name="username-password-authentication"></a>Autenticação de nome do usuário e senha

Quando o aplicativo usa apenas a autenticação de nome de usuário e senha, não é preciso registrar um URI de redirecionamento para ele. Esse fluxo faz uma viagem de ida e volta para a plataforma de identidade da Microsoft. O aplicativo não será chamado de volta em nenhum URI específico.

No entanto, identifique o aplicativo como um aplicativo cliente público. Para fazer isso:

1. Ainda no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>, selecione o aplicativo em **Registros de aplicativo** e, em seguida, selecione **Autenticação**.
1. Em **Configurações avançadas** > **Permitir fluxos de cliente públicos** > **Habilitar os seguintes fluxos móveis e de desktop:** , selecione **Sim**.

   :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Habilitar a configuração de cliente público no painel de autenticação no portal do Azure":::

## <a name="api-permissions"></a>Permissões de API

Os aplicativos móveis chamam APIs Web em nome do usuário conectado. Seu aplicativo precisa solicitar permissões delegadas. Essas permissões também são chamadas de escopos. Dependendo da experiência desejada, você pode solicitar permissões delegadas estaticamente por meio do portal do Azure. Ou você pode solicitá-las dinamicamente no runtime.

Ao registrar estaticamente as permissões, você permite que os administradores aprovem facilmente seu aplicativo. O registro estático é recomendado.

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [configuração de código do aplicativo](scenario-mobile-app-configuration.md).
