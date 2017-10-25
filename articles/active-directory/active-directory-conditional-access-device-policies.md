---
title: "Políticas de dispositivo de acesso condicional do Azure Active Directory para serviços do Office 365 | Microsoft Docs"
description: "Saiba mais sobre como provisionar políticas de dispositivo de acesso condicional para ajudar a tornar os recursos corporativos mais seguros, ao mesmo tempo que mantém a conformidade do usuário e o acesso a serviços."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 10a4a23af08c31ea107e196ae441fefd39c7cabc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="active-directory-conditional-access-device-policies-for-office-365-services"></a>Políticas de dispositivo de acesso condicional do Active Directory para serviços do Office 365

O acesso condicional exige várias partes para funcionar. Ele envolve um usuário autenticado com multifator, um dispositivo autenticado e um dispositivo em conformidade, entre outros fatores. Neste artigo, nosso foco principal são as condições baseadas em dispositivo que sua organização pode usar para ajudá-lo a controlar o acesso aos serviços do Office 365. 

Os usuários corporativos desejam acessar serviços do Office 365 como o Exchange e SharePoint Online no trabalho ou na escola em seus dispositivos pessoais. Você deseja que o acesso seja seguro. É possível provisionar políticas de dispositivo de acesso condicional para ajudar a tornar os recursos corporativos mais seguros, ao mesmo tempo que concede acesso a serviços para os usuários que estão usando dispositivos em conformidade. Defina políticas de acesso condicional para o Office 365 no portal de acesso condicional do Microsoft Intune.

O Azure AD (Azure Active Directory) impõe políticas de acesso condicional para ajudar a proteger o acesso aos serviços do Office 365. É possível criar uma política de acesso condicional que impede um usuário que usa um dispositivo fora de conformidade de acessar um serviço do Office 365. O usuário deve estar em conformidade com às políticas de dispositivo da empresa antes que o acesso ao serviço seja concedido. Como alternativa, você pode criar uma política que exige que os usuários registrem seus dispositivos para obter acesso a um serviço do Office 365. As políticas podem ser aplicadas a todos os usuários de uma organização ou limitadas a alguns grupos de destino. É possível adicionar mais grupos de destino a uma política ao longo do tempo.

É um pré-requisito para a imposição de políticas de dispositivo que os usuários registrem seus dispositivos com o serviço de registro de dispositivo do Azure AD. Você pode optar por ativar a autenticação multifator em dispositivos que se registram com o serviço de registro de dispositivo do Azure AD. A autenticação multifator é recomendada para o serviço de registro de dispositivo do Azure Active Directory. Quando a autenticação multifator é ativada, os usuários que registram seus dispositivos com o serviço de registro de dispositivo do Azure AD recebem um desafio para a autenticação de dois fatores.

## <a name="how-does-a-conditional-access-policy-work"></a>Como funciona uma política de acesso condicional?

Quando um usuário solicita o acesso a um serviço do Office 365 em uma plataforma de dispositivo com suporte, o Azure AD autentica o usuário e o dispositivo. O Azure AD concede acesso ao serviço apenas se o usuário está em conformidade com a política definida para o serviço. Os usuários em dispositivos que não estão registrados recebem instruções sobre como registrar e entrar em conformidade para acessar serviços corporativos do Office 365. Os usuários em dispositivos iOS e Android devem registrar seus dispositivos usando o aplicativo do Portal da Empresa do Intune. Quando um usuário registra seu dispositivo, o dispositivo é registrado com o Azure AD e é registrado para a conformidade e o gerenciamento de dispositivos. Você deve usar o serviço de registro de dispositivo do Azure AD com o Microsoft Intune para o Gerenciamento de Dispositivo Móvel do Office 365. O registro de dispositivo é obrigatório para que os usuários acessem os serviços do Office 365 quando as políticas de dispositivo são impostas.

Quando um usuário registra um dispositivo com êxito, o dispositivo se torna confiável. O Azure AD fornece ao usuário autenticado o acesso de logon único aos aplicativos da empresa. O Azure AD impõe uma política de acesso condicional para conceder acesso a um serviço, não apenas na primeira vez que o usuário solicita acesso, mas sempre que ele renova uma solicitação de acesso. O usuário terá o acesso negado a serviços quando as credenciais de entrada forem alteradas, o dispositivo for perdido ou roubado ou as condições da política não forem atendidas no momento da solicitação de renovação.

## <a name="deployment-considerations"></a>Considerações de implantação

Você deve usar o serviço de registro de dispositivo do Azure AD para registrar dispositivos.

Quando os usuários locais estiverem prestes a serem autenticados, o AD FS (Serviços de Federação do Active Directory) (versão 1.0 e versões posteriores) será obrigatório. A autenticação multifator para o Workplace Join falhará quando o provedor de identidade não tiver a capacidade de fornecer a autenticação multifator. Por exemplo, não é possível usar a autenticação multifator com o AD FS 2.0. Verifique se o AD FS local funciona com a autenticação multifator e se um método de autenticação multifator válido está em vigor antes de ativar a autenticação multifator para o serviço de registro de dispositivo do Azure AD. Por exemplo, o AD FS no Windows Server 2012 R2 tem funcionalidades de autenticação multifator. Você também deve definir um método de autenticação adicional válido (autenticação multifator) no servidor do AD FS antes de ativar a autenticação multifator para o serviço de registro de dispositivo do Azure AD. Para obter mais informações sobre os métodos de autenticação multifator com suporte no AD FS, consulte [Configurar métodos de autenticação adicionais do AD FS](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs).

## <a name="next-steps"></a>Próximas etapas

*   Para obter respostas a perguntas comuns, consulte [Perguntas frequentes sobre o acesso condicional do Azure Active Directory](active-directory-conditional-faqs.md).
