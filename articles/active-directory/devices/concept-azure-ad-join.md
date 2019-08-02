---
title: O que é um Azure AD associado um dispositivo?
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
ms.openlocfilehash: 4af3aea7218ea8792bb66188e8df7baf9f460b0b
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462805"
---
# <a name="azure-ad-joined-devices"></a>Dispositivos adicionados ao Azure AD

Ingresso no Azure AD destina-se a organizações que desejam ser primeiro na nuvem ou somente na nuvem. Qualquer organização pode implantar dispositivos adicionados ao Azure AD não importa o tamanho ou o setor. Ingresso no Azure AD funciona até mesmo em um ambiente híbrido, habilitando o acesso aos recursos e aplicativos de nuvem e locais.

|   | Ingresso no AD do Azure |
| --- | --- |
| **Definição** | Associados apenas ao Azure AD que exigem a conta organizacional para entrar no dispositivo |
| **Público-alvo principal** | Adequado para ambos somente na nuvem e as organizações híbridas. |
|   | Aplicável a todos os usuários em uma organização |
| **Propriedade do dispositivo** | Organização |
| **Sistemas operacionais** | Todos os dispositivos Windows 10 |
| **Provisionamento** | Autoatendimento: OOBE do Windows ou as configurações |
|   | Registro em massa |
|   | Windows Autopilot |
| **Entrada de dispositivo nas opções** | Contas organizacionais usando: |
|   | Senha |
|   | Windows Hello for Business |
|   | Chaves de segurança FIDO2.0 (visualização) |
| **Gerenciamento de dispositivos** | Gerenciamento de dispositivo móvel (exemplo: Microsoft Intune) |
|   | Cogerenciamento com o Microsoft Intune e o System Center Configuration Manager |
| **Principais recursos** | SSO para recursos de nuvem e locais |
|   | Acesso condicional por meio do registro do MDM e avaliação de conformidade do MDM |
|   | Autoatendimento de redefinição de senha e o Windows Hello PIN redefinido em tela de bloqueio |
|   | Enterprise State Roaming entre dispositivos |

Dispositivos adicionados ao AD do Azure se conectaram usando uma unidade organizacional conta do Azure AD. Acesso aos recursos na organização pode ser ainda mais limitado com base nessa conta do AD do Azure e [políticas de acesso condicional](../conditional-access/overview.md) aplicada à identidade do dispositivo.

Os administradores podem proteger e ainda mais o controle do Azure AD ingressado em dispositivos usando as ferramentas de gerenciamento de dispositivo móvel (MDM) como o Microsoft Intune ou em cenários de cogerenciamento usando o System Center Configuration Manager. Essas ferramentas fornecem um meio para impor as configurações necessárias de organização, como exigir armazenamento a ser criptografado, a complexidade de senha, as instalações de software e atualizações de software. Os administradores podem disponibilizar aplicativos da organização para dispositivos adicionados ao Azure AD usando o [System Center Configuration Manager e o Microsoft Store para empresas](https://docs.microsoft.com/sccm/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Ingresso no Azure AD pode ser feito usando as opções de autoatendimento, como o de OOBE (configuração), o registro em massa, ou [Windows Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot).

Dispositivos adicionados ao AD do Azure ainda podem manter o acesso de logon único para recursos locais quando estão na rede da organização. Dispositivos que são adicionados ao Azure AD ainda possam autenticar a servidores locais, como arquivos, impressão e outros aplicativos.

## <a name="scenarios"></a>Cenários

Embora a adição ao Azure AD se destine basicamente às organizações que não têm uma infraestrutura do Active Directory local para Windows Server, você certamente pode usá-la em cenários em que:

- Você deseja fazer a transição para infraestrutura baseada em nuvem usando o Azure AD e um MDM como o Intune.
- Não é possível usar um ingresso no domínio local, por exemplo, caso seja necessário colocar dispositivos móveis, como tablets e telefones, sob controle.
- Os usuários precisam basicamente acessar o Office 365 ou outros aplicativos SaaS integrados ao Azure AD.
- Você quer gerenciar um grupo de usuários no Azure AD, e não no Active Directory. Esse cenário pode se aplicar, por exemplo, para funcionários temporários, prestadores de serviços ou alunos.
- Você deseja fornecer recursos de ingresso para trabalhadores em escritórios remotos com infraestrutura local limitada.

Você pode configurar dispositivos adicionados ao Azure AD para dispositivos com Windows 10.

A meta dos dispositivos adicionados ao Azure AD é simplificar:

- As implantações de dispositivos Windows que pertencem à organização
- O acesso a aplicativos e recursos organizacionais de qualquer dispositivo Windows
- Gerenciamento baseado em nuvem de dispositivos empresariais
- Os usuários façam logon seus dispositivos com o seu Azure AD ou sincronizadas do Active Directory ou de estudante contas.

![Dispositivos adicionados ao Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

O Ingresso no Azure AD pode ser implantado usando um destes métodos:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Implantação em massa](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [Experiência de autoatendimento](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Próximas etapas

- [Planejar sua implementação de junção do Azure AD](azureadjoin-plan.md)
- [Como gerenciar o grupo de administradores locais no Azure AD associado a dispositivos](assign-local-admin.md)
- [Gerenciar identidades de dispositivo usando o portal do Azure](device-management-azure-portal.md)
- [Gerenciar dispositivos obsoletos no Azure AD](manage-stale-devices.md)
