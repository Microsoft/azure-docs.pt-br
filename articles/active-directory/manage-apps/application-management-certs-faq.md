---
title: Perguntas frequentes sobre certificados de gerenciamento de aplicativos Azure Active Directory
description: Aprenda as respostas para perguntas frequentes sobre o gerenciamento de certificados para aplicativos usando Azure Active Directory como um IdP (provedor de identidade).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: kenwith
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 928bf02e2d628379738483b40631e36f0f929176
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803628"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Perguntas frequentes sobre os certificados de gerenciamento de aplicativos do Azure Active Directory (Azure AD)

Esta página responde às perguntas frequentes sobre o gerenciamento de certificados para aplicativos usando o Azure Active Directory (AD do Azure) como um provedor de identidade (IdP).

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Há uma maneira de gerar uma lista de certificados de autenticação SAML expirados?

Você pode exportar todos os registros de aplicativo com segredos de expiração, certificados e seus proprietários para os aplicativos especificados de seu diretório em um arquivo CSV por meio de [scripts do PowerShell](app-management-powershell-samples.md). 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Onde posso encontrar as informações sobre as etapas de renovação de certificados em breve?

Você pode encontrar as etapas [aqui](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire).

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Como posso personalizar a data de validade dos certificados emitidos pelo AD do Azure?

Por padrão, o Azure AD configura um certificado para expirar após três anos quando ele é criado automaticamente durante a configuração de logon único do SAML. Como não é possível alterar a data de um certificado depois de salvá-lo, você precisa criar um novo certificado. Para obter as etapas sobre como fazer isso, consulte [Personalizar a data de validade do seu certificado de Federação e distribuí-lo para um novo certificado](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate).

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Como automatizar as notificações de expiração de certificados?

O Azure AD enviará uma notificação por email 60, 30 e 7 dias antes que o certificado SAML expire. Você pode adicionar mais de um endereço de email para receber notificações. 

> [!NOTE]
> Você pode adicionar até 5 endereços de email à lista de notificação (incluindo o endereço de email do administrador que adicionou o aplicativo). Se precisar de mais pessoas para ser notificado, use os emails da lista de distribuição. 

Para especificar os emails para os quais você deseja que as notificações sejam enviadas, consulte [adicionar endereços de notificação por email para expiração do certificado](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration).

Não há nenhuma opção para editar ou personalizar essas notificações de email recebidas do `aadnotification@microsoft.com` . No entanto, você pode exportar registros de aplicativo com validade e certificados expirados por meio de [scripts do PowerShell](app-management-powershell-samples.md).

## <a name="who-can-update-the-certificates"></a>Quem pode atualizar os certificados?

O proprietário do aplicativo ou administrador global ou administrador de aplicativos pode atualizar os certificados por meio de portal do Azure interface do usuário, PowerShell ou Microsoft Graph.

## <a name="i-need-more-details-about-certificate-signing-options"></a>Preciso de mais detalhes sobre as opções de assinatura de certificado.

No Azure AD, você pode configurar opções de assinatura de certificado e o algoritmo de assinatura de certificado. Para saber mais, consulte [Opções avançadas de assinatura de certificado de token SAML para aplicativos do Azure ad](certificate-signing-options.md).

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Preciso substituir o certificado para os aplicativos de Proxy de Aplicativo do AD do Azure e precisa de mais instruções.

Para substituir certificados para aplicativos de Proxy de Aplicativo do AD do Azure, consulte [exemplo do PowerShell – substituir certificado em aplicativos de proxy de aplicativo](scripts/powershell-get-custom-domain-replace-cert.md).

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Como fazer gerenciar certificados para domínios personalizados no Proxy de Aplicativo do AD do Azure?

Para configurar um aplicativo local para usar um domínio personalizado, você precisa de um domínio personalizado verificado do Azure Active Directory, um certificado PFX para o domínio personalizado e um aplicativo local para configurar. Para saber mais, confira [domínios personalizados no Azure proxy de aplicativo do AD](application-proxy-configure-custom-domain.md). 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>Preciso atualizar o certificado de autenticação de tokens no lado do aplicativo. Onde posso obtê-lo no lado do Azure AD?

Você pode renovar um certificado SAML X. 509, consulte [certificado de autenticação SAML](configure-saml-single-sign-on.md#saml-signing-certificate).

## <a name="what-is-azure-ad-signing-key-rollover"></a>O que é substituição de chave de assinatura do Azure AD?

Encontre mais detalhes [aqui](../develop/active-directory-signing-key-rollover.md). 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Como fazer renovar certificado de criptografia de token de aplicativo?

Para renovar um certificado de criptografia de token de aplicativo, consulte [como renovar um certificado de criptografia de token para um aplicativo empresarial](howto-saml-token-encryption.md). 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Como fazer renovar certificado de assinatura de token de aplicativo?

Para renovar um certificado de assinatura de token de aplicativo, consulte [como renovar um certificado de autenticação de token para um aplicativo empresarial](manage-certificates-for-federated-single-sign-on.md).

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Como fazer atualizar o Azure AD depois de alterar meus certificados de Federação?

Para atualizar o Azure AD depois de alterar os certificados de Federação, consulte [renovar certificados de Federação para Microsoft 365 e Azure Active Directory](../hybrid/how-to-connect-fed-o365-certs.md).
