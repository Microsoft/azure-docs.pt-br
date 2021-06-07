---
title: O que é um dispositivo adicionado ao Azure AD?
description: Saiba mais sobre os dispositivos ingressados no Azure AD e como o gerenciamento de identidade do dispositivo pode ajudá-lo a gerenciar dispositivos que estão acessando recursos em seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b7cc0535549f2dbcdef4ab043ee506527fdbc5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90601445"
---
# <a name="azure-ad-joined-devices"></a>Dispositivos adicionados ao Azure AD

A adição ao Azure AD destina-se a organizações que desejam estar somente em nuvem ou priorizam a nuvem. Qualquer organização pode implantar dispositivos adicionados ao Azure AD, independentemente do tamanho ou do setor. A adição ao Azure AD funciona bem até mesmo em um ambiente híbrido, permitindo acesso aos recursos e aplicativos locais e de nuvem.

| Ingresso no AD do Azure | Descrição |
| --- | --- |
| **Definição** | Adicionado somente ao Azure AD, exigindo que a conta da organização se conecte ao dispositivo |
| **Público principal** | Adequado para organizações híbridas e somente de nuvem. |
|   | Aplicável a todos os usuários em uma organização |
| **Propriedade do dispositivo** | Organização |
| **Sistemas operacionais** | Todos os dispositivos Windows 10, exceto Windows 10 Home |
|   | [Máquinas Virtuais do Windows Server 2019 em execução no Azure](howto-vm-sign-in-azure-ad-windows.md) (não há suporte para Server Core) |
| **Provisionamento** | Autoatendimento: Configurações ou OOBE do Windows |
|   | Registro em massa |
|   | Windows Autopilot |
| **Opções de entrada do dispositivo** | Contas organizacionais que usam: |
|   | Senha |
|   | Windows Hello for Business |
|   | Chaves de segurança do FIDO 2.0 (versão prévia) |
| **Gerenciamento de dispositivos** | Gerenciamento de dispositivo móvel (exemplo: Microsoft Intune) |
|   | Gerenciamento em conjunto com o Microsoft Intune e o Microsoft Endpoint Configuration Manager |
| **Principais funcionalidades** | SSO para recursos de nuvem e locais |
|   | Acesso condicional por meio de registro do MDM e avaliação de conformidade do MDM |
|   | Redefinição de senha por autoatendimento e redefinição de PIN do Windows Hello na tela de bloqueio |
|   | Enterprise State Roaming entre dispositivos |

Os dispositivos adicionados ao Azure AD são conectados usando uma conta organizacional do Azure AD. O acesso a recursos na organização pode ser limitado com base nessa conta do Azure AD e nas [políticas de acesso condicional](../conditional-access/howto-conditional-access-policy-compliant-device.md) aplicadas à identidade do dispositivo.

Os administradores podem proteger e controlar os dispositivos adicionados ao Azure AD usando ferramentas de MDM (gerenciamento de dispositivo móvel), como Microsoft Intune, ou em cenários de gerenciamento em conjunto usando o Microsoft Endpoint Configuration Manager. Essas ferramentas fornecem um meio de impor configurações necessárias à organização, como exigir que o armazenamento seja criptografado, complexidade de senha, instalações e atualizações de software. Os administradores podem disponibilizar os aplicativos da organização para os dispositivos adicionados ao Azure AD usando o Configuration Manager para [Gerenciar aplicativos do Microsoft Store para Negócios e Educação](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

A adição ao Azure AD pode ser realizada usando opções de autoatendimento, como a OOBE (Experiência pronta para uso), o registro em massa ou o [Windows AutoPilot](/intune/enrollment-autopilot).

Os dispositivos adicionados ao Azure AD ainda podem manter o acesso de logon único a recursos locais quando estão na rede da organização. Os dispositivos adicionados ao Azure AD ainda podem ser autenticados em servidores locais como arquivos, impressão e outros aplicativos.

## <a name="scenarios"></a>Cenários

Embora a adição ao Azure AD se destine basicamente às organizações que não têm uma infraestrutura do Active Directory local para Windows Server, você certamente pode usá-la em cenários em que:

- Você deseja fazer a transição para infraestrutura baseada em nuvem usando o Azure AD e um MDM como o Intune.
- Não é possível usar um ingresso no domínio local, por exemplo, caso seja necessário colocar dispositivos móveis, como tablets e telefones, sob controle.
- Os usuários precisam principalmente acessar Microsoft 365 ou outros aplicativos SaaS integrados ao Azure AD.
- Você quer gerenciar um grupo de usuários no Azure AD, e não no Active Directory. Esse cenário pode se aplicar, por exemplo, a funcionários temporários, prestadores de serviços ou alunos.
- Você deseja fornecer recursos de ingresso para trabalhadores em escritórios remotos com infraestrutura local limitada.

Você pode configurar dispositivos ingressados no Azure AD para todos os dispositivos Windows 10 com exceção do Windows 10 Home.

A meta dos dispositivos adicionados ao Azure AD é simplificar:

- As implantações de dispositivos Windows que pertencem à organização
- O acesso a aplicativos e recursos organizacionais de qualquer dispositivo Windows
- Gerenciamento baseado em nuvem de dispositivos empresariais
- Os usuários entram em seus dispositivos com as respectivas contas corporativas ou de aluno do Azure AD ou do Active Directory.

![Dispositivos adicionados ao Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

O Ingresso no Azure AD pode ser implantado usando um destes métodos:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Implantação em massa](/intune/windows-bulk-enroll)
- [Experiência de autoatendimento](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Próximas etapas

- [Planejar sua implementação de ingresso no Azure AD](azureadjoin-plan.md)
- [Como gerenciar o grupo de administradores locais nos dispositivos adicionados ao Azure AD](assign-local-admin.md)
- [Gerenciar identidades de dispositivo usando o portal do Azure](device-management-azure-portal.md)
- [Gerenciar dispositivos obsoletos no Azure AD](manage-stale-devices.md)
