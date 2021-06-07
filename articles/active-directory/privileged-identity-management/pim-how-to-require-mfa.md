---
title: MFA ou 2FA e Privileged Identity Management-Azure AD | Microsoft Docs
description: Saiba como o PIM (Azure AD Privileged Identity Management) valida a MFA (autenticação multifator).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4344e769cc8466287dab1e98e95cc3fbe705ffbd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94834992"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Autenticação multifator e Privileged Identity Management

É recomendável que você exija MFA (autenticação multifator) a todos os administradores. Isso reduz o risco de um ataque devido a uma senha comprometida.

Você pode exigir que os usuários concluam um desafio de autenticação multifator ao entrarem. Você também pode exigir que os usuários concluam um desafio de autenticação multifator quando ativam uma função no Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Dessa forma, se o usuário não concluiu um desafio de autenticação multifator quando se conectasse, ele será solicitado a fazer isso por Privileged Identity Management.

> [!IMPORTANT]
> Agora, a autenticação multifator do Azure AD funciona apenas com contas corporativas ou de estudante, não com contas pessoais da Microsoft (geralmente uma conta pessoal que é usada para entrar nos serviços da Microsoft, como Skype, Xbox ou Outlook.com). Por isso, qualquer pessoa que usa uma conta pessoal não pode ser um administrador qualificado porque não pode usar a autenticação multifator para ativar suas funções. Se esses usuários precisarem continuar a gerenciar cargas de trabalho usando uma conta da Microsoft, eleve-os a administradores permanentes por enquanto.

## <a name="how-pim-validates-mfa"></a>Como o PIM valida MFA

Há duas opções para validar a autenticação multifator quando um usuário ativa uma função.

A opção mais simples é contar com a autenticação multifator do Azure AD para usuários que estão ativando uma função privilegiada. Para fazer isso, primeiro verifique se os usuários estão licenciados, se necessário, e se foram registrados para a autenticação multifator do Azure AD. Para obter mais informações sobre como implantar a autenticação multifator do Azure AD, consulte [implantar a autenticação multifator do Azure ad baseada em nuvem](../authentication/howto-mfa-getstarted.md). É recomendável, mas não obrigatório, que você configure o Azure AD para impor a autenticação multifator para esses usuários quando eles entram. Isso ocorre porque as verificações de autenticação multifator serão feitas Privileged Identity Management si mesma.

Como alternativa, se os usuários autenticarem no local, você poderá fazer com que seu provedor de identidade seja responsável pela autenticação multifator. Por exemplo, se você configurou os serviços de Federação do AD para exigir autenticação baseada em cartão inteligente antes de acessar o Azure AD, a [proteção de recursos de nuvem com a autenticação multifator do Azure AD e o AD FS](../authentication/howto-mfa-adfs.md) inclui instruções para configurar o AD FS para enviar declarações ao Azure AD. Quando um usuário tenta ativar uma função, Privileged Identity Management aceitará que a autenticação multifator já tenha sido validada para o usuário depois de receber as declarações apropriadas.

## <a name="next-steps"></a>Próximas etapas

- [Definir as configurações de função do Azure AD no Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Definir configurações de função de recurso do Azure no Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
