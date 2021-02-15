---
title: Suporte à autenticação sem senha com chaves FIDO2 em aplicativos que você desenvolve | Azure
titleSuffix: Microsoft identity platform
description: Este guia de implantação explica como dar suporte à autenticação sem senha com chaves de segurança FIDO2 nos aplicativos que você desenvolve
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 1/29/2021
ms.author: nichola
ms.custom: aaddev
ms.openlocfilehash: 5abece0e272d4b72ba6f787ad44b091df5d45226
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416926"
---
# <a name="support-passwordless-authentication-with-fido2-keys-in-apps-you-develop"></a>Dar suporte à autenticação sem senha com chaves FIDO2 em aplicativos que você desenvolve

Essas configurações e práticas recomendadas ajudarão você a evitar cenários comuns que impedem a disponibilidade de [autenticação FIDO2 com senha](../../active-directory/authentication/concept-authentication-passwordless.md) para usuários de seus aplicativos.

## <a name="general-best-practices"></a>Práticas recomendadas gerais

### <a name="domain-hints"></a>Dicas de domínio

Não use uma dica de domínio para ignorar a [descoberta de realm inicial](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md). Esse recurso destina-se a tornar as entradas mais simplificadas, mas o provedor de identidade federada pode não oferecer suporte à autenticação sem senha.

### <a name="requiring-specific-credentials"></a>Exigindo credenciais específicas

Se você estiver usando o SAML, não especifique que uma senha é necessária [usando o elemento RequestedAuthnContext](single-sign-on-saml-protocol.md#requestauthncontext).

O elemento RequestedAuthnContext é opcional, portanto, para resolver isso, você pode removê-lo de suas solicitações de autenticação SAML. Essa é uma prática recomendada geral, pois o uso desse elemento também pode impedir que outras opções de autenticação, como a autenticação multifator, funcionem corretamente.

### <a name="using-the-most-recently-used-authentication-method"></a>Usando o método de autenticação usado mais recentemente

O método de entrada que foi usado mais recentemente por um usuário será apresentado a eles primeiro. Isso pode causar confusão quando os usuários acreditam que devem usar a primeira opção apresentada. No entanto, eles podem escolher outra opção selecionando "outras maneiras de entrar", conforme mostrado abaixo.

:::image type="content" source="./media/support-fido2-authentication/most-recently-used-method.png" alt-text="Imagem da experiência de autenticação do usuário realçando o botão que permite ao usuário alterar o método de autenticação.":::

## <a name="platform-specific-best-practices"></a>Práticas recomendadas específicas da plataforma

### <a name="desktop"></a>Desktop

As opções recomendadas para implementar a autenticação são, em ordem:

- Os aplicativos de área de trabalho .NET que estão usando a MSAL (biblioteca de autenticação da Microsoft) devem usar o Gerenciador de autenticação do Windows (WAM). Essa integração e seus benefícios estão [documentados no GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/wam).
- Use [WebView2](https://docs.microsoft.com/microsoft-edge/webview2/) para dar suporte a FIDO2 em um navegador inserido.
- Use o navegador do sistema. As bibliotecas de MSAL para plataformas de desktop usam esse método por padrão. Você pode consultar nossa página sobre a compatibilidade do navegador FIDO2 para garantir que o navegador que você usa dê suporte à autenticação do FIDO2.

### <a name="mobile"></a>Dispositivos móveis

A partir de fevereiro de 2020, o FIDO2 não tem suporte atualmente para aplicativos iOS ou Android nativos, mas está em desenvolvimento.

Para preparar aplicativos para sua disponibilidade e, como prática recomendada geral, os aplicativos iOS e Android devem usar o MSAL com sua configuração padrão de usar o navegador da Web do sistema.

Se você não estiver usando o MSAL, você ainda deverá usar o navegador da Web do sistema para autenticação. Recursos como logon único e acesso condicional dependem de uma superfície da Web compartilhada fornecida pelo navegador da Web do sistema. Isso significa usar as [guias personalizadas do Chrome](https://developer.chrome.com/docs/multidevice/android/customtabs/) (Android) ou [autenticar um usuário por meio de um serviço Web | Documentação do desenvolvedor da Apple](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service) (Ios).

### <a name="web-and-single-page-apps"></a>Aplicativos Web e de página única

A disponibilidade da autenticação FIDO2 com senha para aplicativos que são executados em um navegador da Web dependerá da combinação de navegador e plataforma. Você pode consultar nossa [matriz de compatibilidade do FIDO2](../authentication/fido2-compatibility.md) para verificar se a combinação de seus usuários será suportada.

## <a name="next-steps"></a>Próximas etapas

[Opções de autenticação com senha para Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)
