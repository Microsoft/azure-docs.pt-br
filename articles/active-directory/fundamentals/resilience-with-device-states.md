---
title: Crie resiliência usando os Estados do dispositivo no Azure Active Directory
description: Um guia para arquitetos e administradores de ti para a criação de resiliência usando os Estados do dispositivo
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d476be7a417cfc31cca76d3409074aaaa281a56
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724603"
---
# <a name="build-resilience-with-device-states"></a>Crie resiliência com os Estados do dispositivo

Ao habilitar os [Estados do dispositivo](../devices/overview.md) com o Azure AD, os administradores podem criar políticas de [acesso condicional](../conditional-access/overview.md) que controlam o acesso a aplicativos com base no estado do dispositivo. O benefício adicional dos dispositivos é que ele atende aos requisitos de autenticação fortes para acesso aos recursos, reduzindo as solicitações de autenticação MFA adicionais e melhorando a resiliência. 

O fluxograma a seguir apresenta as diferentes maneiras de carregar dispositivos no Azure AD que habilitam os Estados do dispositivo. Você pode usar mais de um em sua organização.

![fluxograma para escolher os Estados do dispositivo](./media/resilience-with-device-states/admin-resilience-devices.png)

Quando você usa os [Estados do dispositivo](../devices/overview.md), os usuários na maioria dos casos experimentam o logon único para recursos por meio de um PRT (token de [atualização principal](../devices/concept-primary-refresh-token.md) ). O PRT contém declarações sobre o usuário e o dispositivo e pode ser usado para obter tokens de autenticação para acessar aplicativos do dispositivo. O PRT é válido por 14 dias e é renovado continuamente, contanto que o usuário use ativamente o dispositivo, proporcionando aos usuários uma experiência resiliente. Um PRT também pode obter uma declaração de autenticação multifator de várias maneiras. Para obter mais informações, consulte [quando um PRT Obtém uma declaração de MFA](../devices/concept-primary-refresh-token.md).

## <a name="how-do-device-states-help"></a>Como os Estados do dispositivo ajudam?

Quando um PRT é usado para solicitar acesso a um aplicativo, suas declarações de dispositivo, sessão e MFA são confiáveis pelo Azure AD. Quando os administradores criam políticas que exigem um controle baseado em dispositivo ou um controle de autenticação multifator, o requisito de política pode ser atendido por meio de seu estado do dispositivo sem tentar a autenticação multifator. Os usuários não verão prompts adicionais de autenticação multifator no mesmo dispositivo. Isso aumenta a resiliência para uma interrupção do serviço de MFA do Azure ou suas dependências como provedores locais de telecomunicações.

## <a name="how-do-i-implement-device-states"></a>Como fazer implementar Estados de dispositivo?

* Habilite o ingresso do [Azure ad híbrido](../devices/hybrid-azuread-join-plan.md) e o [ingresso no Azure ad](../devices/azureadjoin-plan.md) para dispositivos Windows de propriedade da empresa e exija que eles sejam associados, se possível. Se não for possível, exija que eles sejam registrados.

  Se houver versões mais antigas do Windows em sua organização, atualize esses dispositivos para usar o Windows 10.

* Padronize o acesso do navegador do usuário para usar o [Microsoft Edge](/deployedge/microsoft-edge-security-identity) ou o Google Chrome com [extensões](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) [com suporte](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) que habilitaram o SSO contínuo para aplicativos Web usando o PRT.

* Para dispositivos iOS e Android pessoais ou de propriedade da empresa, implante o [aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-overview.md). Além da autenticação multifator e recursos de entrada sem senha, o aplicativo Microsoft Authenticator habilitará o logon único entre aplicativos nativos por meio da [autenticação orientada](../develop/msal-android-single-sign-on.md) com menos prompts de autenticação para os usuários finais.

* Para dispositivos iOS e Android pessoais ou de propriedade da empresa, use o [Gerenciamento de aplicativos móveis](/mem/intune/apps/app-management) para acessar com segurança os recursos da empresa com menos solicitações de autenticação. 

* [Use o plug-in Microsoft Enterprise SSO para dispositivos Apple (versão prévia)](../develop/apple-sso-plugin.md). Isso registra o dispositivo e fornece SSO no navegador e em aplicativos nativos do Azure AD. 

## <a name="next-steps"></a>Próximas etapas
Recursos de resiliência para administradores e arquitetos
 
* [Crie resiliência com o gerenciamento de credenciais](resilience-in-credentials.md)

* [Criar resiliência usando a Continuous Access Evaluation (CAE)](resilience-with-continuous-access-evaluation.md)

* [Criar resiliência na autenticação de usuário externa](resilience-b2b-authentication.md)

* [Crie resiliência em sua autenticação híbrida](resilience-in-hybrid.md)

* [Crie resiliência no acesso do aplicativo com o proxy de aplicativo](resilience-on-premises-access.md)


Recursos de resiliência para desenvolvedores

* [Crie resiliência IAM em seus aplicativos](resilience-app-development-overview.md)

* [Crie resiliência em seus sistemas CIAM](resilience-b2c.md)