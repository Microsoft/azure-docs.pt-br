---
title: "Como gerenciar configurações de ativação de função | Microsoft Docs"
description: "Aprenda a alterar as configurações padrão para identidades com privilégios com a extensão Privileged Identity Management do Azure Active Directory."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: f6cbcb6a-8a89-4077-afd8-06c94a64f4aa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 23605e89cd1846d2e06e48cb5d3e0191cb9e9b4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-role-activation-settings-in-azure-ad-privileged-identity-management"></a>Como gerenciar as configurações de ativação de função no Privileged Identity Management do Azure AD
Um administrador de função com privilégios pode personalizar o Azure AD PIM (Privileged Identity Management) em sua organização, incluindo alterar a experiência de um usuário que está ativando uma atribuição de função elegível.

## <a name="manage-the-role-activation-settings"></a>Gerenciar as configurações de ativação de função
1. Acesse o [portal do Azure](https://portal.azure.com) e selecione o aplicativo **Azure AD Privileged Identity Management** do painel.
2. Selecione **Gerenciar funções privilegiadas** > **Configurações** > **Funções Privilegiadas**.
3. Escolha a função cujas configurações você deseja gerenciar.

Na página de configurações para cada função, há uma série de configurações que você pode realizar. Essas configurações afetam apenas os usuários que são administradores elegíveis, mas não os administradores permanentes.

**Ativações**: o tempo, em horas, pelo qual uma função fica ativa antes de expirar. Isso pode ser entre 1 e 72 horas.

**Notificações**: você pode escolher se o sistema envia ou não emails para administradores confirmando que eles ativaram uma função. Isso pode ser útil para detectar ativações não autorizadas ou ilegítimas.

**Tíquete de Solicitação/Incidente**: você pode escolher se exigirá ou não que administradores elegíveis incluam um número de tíquete ao ativar sua função. Isso pode ser útil ao realizar auditorias de acesso à função.

**Multi-Factor Authentication**: você pode escolher se deseja ou não exigir que os usuários verifiquem sua identidade com MFA antes que possam ativar suas funções. Os usuários só precisam verificar isso uma vez por sessão, não precisam fazê-lo toda vez que ativam uma função. Há duas dicas para ter em mente quando você habilita a MFA:

* Os usuários que têm contas da Microsoft para seus endereços de email (normalmente @outlook.com, mas nem sempre) não podem se registrar para MFA do Azure. Se quiser atribuir funções aos usuários com contas da Microsoft, você deverá torná-los administradores permanentes ou desabilitar o MFA para essa função.
* Você não pode desabilitar o MFA para funções com altos privilégios do Azure AD e do Office365. Esse é um recurso de segurança, porque estas funções devem ser protegidas com cuidado:  
  
  * Administrador de aplicativos
  * Administrador do servidor de Proxy de Aplicativo
  * Administrador de cobrança  
  * Administrador de conformidade  
  * Administrador de serviços do CRM
  * Aprovador de acesso do Sistema de Proteção de Dados do Cliente
  * Gravador de diretório  
  * Administrador do Exchange  
  * Administrador global
  * Administrador de serviço do Intune
  * Administrador de caixa de correio  
  * Suporte de camada 1 do parceiro  
  * Suporte de camada 2 do parceiro  
  * Administrador de função com privilégios   
  * Administrador de segurança  
  * Administrador do SharePoint  
  * Administrador do Skype for Business  
  * Administrador da conta de usuário  

Para obter mais informações sobre como usar MFA com PIM, consulte [Como Exigir MFA](active-directory-privileged-identity-management-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

