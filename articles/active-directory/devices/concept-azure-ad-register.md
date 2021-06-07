---
title: O que são dispositivos registrados no Azure AD?
description: Saiba como os dispositivos registrados no Azure AD fornecem aos usuários suporte para o BYOD (Traga seu próprio dispositivo) ou cenários de dispositivo móvel.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c40b7962090492f8b4b85e555a947130c3628d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91256431"
---
# <a name="azure-ad-registered-devices"></a>Dispositivos registrados no Azure AD

A meta dos dispositivos registrados no Azure AD é fornecer aos usuários suporte para o BYOD (Traga seu próprio dispositivo) ou cenários de dispositivo móvel. Nesses cenários, um usuário pode acessar os Azure Active Directory recursos controlados da sua organização usando um dispositivo pessoal.

| Azure AD registrado | Descrição |
| --- | --- |
| **Definição** | Registrado no Azure AD sem exigir que a conta organizacional entre no dispositivo |
| **Público principal** | Aplicável a todos os usuários com os seguintes critérios: |
|   | BYOD (Traga seu próprio dispositivo) |
|   | Dispositivos móveis |
| **Propriedade do dispositivo** | Usuário ou organização |
| **Sistemas Operacionais** | Windows 10, iOS, Android e MacOS |
| **Provisionamento** | Windows 10 – configurações |
|   | iOS/Android – Portal da Empresa ou aplicativo Microsoft Authenticator |
|   | MacOS – Portal da Empresa |
| **Opções de entrada do dispositivo** | Credenciais locais do usuário final |
|   | Senha |
|   | Windows Hello |
|   | PIN |
|   | Biometria ou padrão para outros dispositivos |
| **Gerenciamento de dispositivos** | Gerenciamento de dispositivo móvel (exemplo: Microsoft Intune) |
|   | gerenciamento de aplicativos móveis |
| **Principais funcionalidades** | SSO para recursos de nuvem |
|   | Acesso condicional quando registrado no Intune |
|   | Acesso condicional via política de proteção de aplicativo |
|   | Habilita a entrada pelo telefone com o aplicativo Microsoft Authenticator |

![Dispositivos registrados no Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Os dispositivos registrados no Azure AD são conectados ao usando uma conta local como uma conta Microsoft em um dispositivo Windows 10, mas também têm uma conta do Azure AD anexada para acesso aos recursos organizacionais. O acesso a recursos na organização pode ser limitado com base nessa conta do Azure AD e nas políticas de acesso condicional aplicadas à identidade do dispositivo.

Os administradores podem proteger e controlar ainda mais esses dispositivos registrados no Azure AD usando ferramentas de MDM (gerenciamento de dispositivo móvel), como Microsoft Intune. O MDM fornece um meio para impor configurações necessárias à organização, como exigir que o armazenamento seja criptografado, a complexidade da senha e o software de segurança mantidos atualizados. 

O registro do Azure AD pode ser realizado ao acessar um aplicativo de trabalho pela primeira vez ou manualmente usando o menu de configurações do Windows 10. 

## <a name="scenarios"></a>Cenários

Um usuário em sua organização deseja acessar as ferramentas de email, o tempo de relatório e o registro de benefícios de seu PC doméstico. Sua organização tem essas ferramentas por trás de uma política de acesso condicional que requer acesso de um dispositivo compatível com o Intune. O usuário adiciona a conta da organização e registra seu PC doméstico com o Azure AD e as políticas necessárias do Intune são impostas, permitindo que o usuário acesse seus recursos.

Outro usuário deseja acessar seu email organizacional em seu telefone Android pessoal que tenha sido com raiz. Sua empresa requer um dispositivo em conformidade e criou uma política de conformidade do Intune para bloquear qualquer dispositivo com raiz. O funcionário está parado de acessar recursos organizacionais neste dispositivo.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar identidades de dispositivo usando o portal do Azure](device-management-azure-portal.md)
- [Gerenciar dispositivos obsoletos no Azure AD](manage-stale-devices.md)
