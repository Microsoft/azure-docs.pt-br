---
title: Quais são o Azure AD, dispositivos registrados?
description: Saiba como o gerenciamento de identidade do dispositivo pode ajudar você a gerenciar dispositivos que estão acessando os recursos em seu ambiente.
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
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462753"
---
# <a name="azure-ad-registered-devices"></a>Dispositivos registrados no Azure AD

A meta dos dispositivos registrados no Azure AD é fornecer aos usuários com suporte para o BYOD traga seu próprio dispositivo () ou cenários de dispositivo móvel. Nesses cenários, um usuário pode acessar os recursos da organização do Active Directory do Azure controlado usando um dispositivo pessoal.

|   | Azure AD registrado |
| --- | --- |
| **Definição** | Registrado com o Azure AD sem a necessidade de uma conta organizacional para entrar no dispositivo |
| **Público-alvo principal** | Aplicável a todos os usuários com os seguintes critérios: |
|   | Traga seu próprio dispositivo (BYOD) |
|   | Dispositivos móveis |
| **Propriedade do dispositivo** | Usuário ou a organização |
| **Sistemas operacionais** | Windows 10, iOS, Android e MacOS |
| **Provisionamento** | Windows 10 – configurações |
|   | iOS/Android – o aplicativo Microsoft Authenticator ou do Portal da empresa |
|   | Portal da empresa MacOS |
| **Entrada de dispositivo nas opções** | Credenciais locais do usuário final |
|   | Senha |
|   | Windows Hello |
|   | PIN |
|   | Biometria ou padrão para outros dispositivos |
| **Gerenciamento de dispositivos** | Gerenciamento de dispositivo móvel (exemplo: Microsoft Intune) |
|   | gerenciamento de aplicativos móveis |
| **Principais recursos** | SSO aos recursos de nuvem |
|   | Acesso condicional, quando registrado no Intune |
|   | Acesso condicional por meio da diretiva de proteção de aplicativo |
|   | Permite a entrada de telefone com o aplicativo Microsoft Authenticator |

![Dispositivos registrados no Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Dispositivos registrados no AD do Azure se conectaram usando uma conta local, como uma conta da Microsoft em um dispositivo Windows 10, mas Além disso, ter uma conta do AD do Azure anexada para o acesso aos recursos organizacionais. Acesso aos recursos na organização pode ser ainda mais limitado com base nessa conta do Azure AD e as políticas de acesso condicional aplicadas à identidade do dispositivo.

Os administradores podem proteger e controlar ainda mais esses dispositivos registrados no Azure AD usando as ferramentas de gerenciamento de dispositivo móvel (MDM) como o Microsoft Intune. MDM fornece um meio para impor as configurações necessárias de organização, como exigir armazenamento sejam criptografados, a complexidade de senha e mantido atualizado do software de segurança. 

Registro do Azure AD pode ser feito ao acessar um aplicativo de trabalho pela primeira vez ou manualmente usando o menu de configurações do Windows 10. 

## <a name="scenarios"></a>Cenários

Um usuário em sua organização quer acessar ferramentas para email, o relatório de licença e registro de benefícios de seus PCs domésticos. Sua organização tem essas ferramentas por trás de uma política de acesso condicional que exige acesso a partir de um dispositivo em conformidade do Intune. O usuário adiciona a conta da organização e registra seus PCs domésticos com o Azure AD e políticas do Intune necessárias são impostas, dando ao usuário acesso aos seus recursos.

Outro usuário deseja acessar o email organizacional em seus telefones pessoais Android que foi modificado. Sua empresa exige um dispositivo em conformidade e criou uma política de conformidade do Intune para bloquear todos os dispositivos com raiz. O funcionário está parado de acessar recursos organizacionais este dispositivo.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar identidades de dispositivo usando o portal do Azure](device-management-azure-portal.md)
- [Gerenciar dispositivos obsoletos no Azure AD](manage-stale-devices.md)
