---
title: Vulnerabilidades detectadas pelo Azure Active Directory Identity Protection
description: Visão geral das vulnerabilidades detectadas pelo Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 446e2ef33b714afe06f24f3dbfc46bc6a42790b8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335157"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnerabilidades detectadas pelo Azure Active Directory Identity Protection

As vulnerabilidades são pontos fracos em um ambiente que podem ser explorados por um invasor. Recomendamos que os administradores resolvam essas vulnerabilidades para melhorar a postura de segurança de sua organização.

![Vulnerabilidades relatadas pela proteção de identidade](./media/vulnerabilities/identity-protection-vulnerabilities.png)

As seções a seguir fornecem uma visão geral das vulnerabilidades relatadas pelo Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registro de autenticação multifator não configurado

Essa vulnerabilidade ajuda a avaliar a implantação da autenticação multifator do Azure em sua organização.

A autenticação multifator do Azure fornece uma segunda camada de segurança para a autenticação do usuário. Ela ajuda a proteger o acesso a dados e aplicativos ao mesmo tempo que atende à demanda dos usuários para um processo de entrada simples. A autenticação multifator do Azure fornece opções de verificação fáceis de usar, como:

* Telefonema
* Mensagem de texto
* Notificação do aplicativo móvel
* Código de verificação de OTP

Recomendamos exigir a Autenticação Multifator do Azure para entradas de usuário. A autenticação multifator desempenha um papel fundamental em políticas de acesso condicional com base em risco disponíveis por meio da proteção de identidade.

Para obter mais informações, consulte [O que é Autenticação Multifator do Microsoft Azure?](../authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Aplicativos de nuvem não gerenciados

Essa vulnerabilidade ajuda a identificar aplicativos de nuvem não gerenciados na sua organização.

A equipe de ti geralmente não está ciente de todos os aplicativos de nuvem em sua organização. É fácil ver por que os administradores teriam preocupações sobre acesso não autorizado a dados corporativos, perda de dados e outros riscos de segurança.

É recomendável implantar Cloud Discovery para descobrir aplicativos de nuvem não gerenciados e gerenciar esses aplicativos usando Azure Active Directory.

Para obter mais informações, confira [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Alertas de Segurança do Privileged Identity Management

Essa vulnerabilidade ajuda você a descobrir e resolver alertas sobre identidades com privilégios na sua organização.  

Para permitir que os usuários executem operações com privilégios, as organizações precisam oferecer aos usuários acesso privilegiado temporário ou permanente ao Azure AD, a recursos do Azure ou do Office 365 ou a outros aplicativos SaaS. Cada um desses usuários privilegiados aumenta a superfície de ataque da sua organização. Essa vulnerabilidade ajuda você a identificar os usuários com acesso privilegiado desnecessário e tomar as devidas providências para reduzir ou eliminar o risco que eles representam.

Recomendamos que as organizações usem Azure AD Privileged Identity Management para gerenciar, controlar e monitorar identidades privilegiadas no Azure AD, bem como outras serviços online da Microsoft, como o Office 365 ou Microsoft Intune.

Para obter mais informações, confira [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md).

## <a name="see-also"></a>Consulte também

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
