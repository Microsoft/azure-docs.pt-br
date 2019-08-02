---
title: O que é um híbrido do Azure AD associado um dispositivo?
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
ms.openlocfilehash: 5c57180ba10322cb790c05b3f8f48043ca08b545
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462740"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos adicionados ao Azure AD híbrido

Por mais de uma década, muitas organizações têm usado o ingresso no domínio para o respectivo Active Directory local a fim de permitir que:

- Os departamentos de TI gerenciem dispositivos pertencentes à empresa de um local central.
- Os usuários entrem em seus dispositivos com as respectivas contas corporativas ou de estudante do Active Directory.

Normalmente, as organizações com um espaço local dependem de métodos de geração de imagens para provisionar dispositivos e, muitas vezes, usam o **SCCM (System Center Configuration Manager)** ou a **GP (Política de Grupo)** para gerenciá-los.

Se seu ambiente tiver um espaço local do AD e você também quiser se beneficiar dos recursos fornecidos pelo Azure Active Directory, será possível implementar dispositivos adicionados ao Azure AD híbrido. Esses dispositivos são dispositivos associados ao Active Directory local e registrados com o Azure Active Directory.

|   | Ingresso no Azure AD híbrido |
| --- | --- |
| **Definição** | Ingressado no local AD e o Azure AD que exigem a conta organizacional para entrar no dispositivo |
| **Público-alvo principal** | Adequado para organizações híbridas com existente local infraestrutura do AD |
|   | Aplicável a todos os usuários em uma organização |
| **Propriedade do dispositivo** | Organização |
| **Sistemas operacionais** | Windows 10, 8.1 e 7 |
|   | Windows Server 2008 R2, 2012/R2, 2016 e 2019 |
| **Provisionamento** | Windows 10, Windows Server 2016/2019 |
|   | Ingresso no domínio pelo IT e AutoAssociação via Azure config AD Connect ou AD FS |
|   | Ingresso no domínio do Windows Autopilot e AutoAssociação por meio da configuração do Azure AD Connect ou AD FS |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2 - exigem MSI |
| **Entrada de dispositivo nas opções** | Contas organizacionais usando: |
|   | Senha |
|   | Windows Hello for Business para Win10 |
| **Gerenciamento de dispositivos** | Política de Grupo |
|   | System Center Configuration Manager autônomo ou o cogerenciamento com o Microsoft Intune |
| **Principais recursos** | SSO para recursos de nuvem e locais |
|   | Acesso condicional por meio do ingresso no domínio ou por meio do Intune se cogerenciado |
|   | Autoatendimento de redefinição de senha e o Windows Hello PIN redefinido em tela de bloqueio |
|   | Enterprise State Roaming entre dispositivos |

![Dispositivos adicionados ao Azure AD híbrido](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Cenários

Uso do Azure AD dispositivos híbridos adicionados ao se:

- Você tem aplicativos Win32 implantados nesses dispositivos que utilizam a autenticação de computador do Active Directory.
- Você deseja continuar a usar a diretiva de grupo para gerenciar a configuração do dispositivo.
- Você deseja continuar a usar soluções de geração de imagens existentes para implantar e configurar os dispositivos.
- Você deve dar suporte a 8.1 dispositivos, além do Windows 10 e 7 do Windows de nível inferior

## <a name="next-steps"></a>Próximas etapas

- [Planejar sua implementação de ingresso no Azure AD híbrido](hybrid-azuread-join-plan.md)
- [Gerenciar identidades de dispositivo usando o portal do Azure](device-management-azure-portal.md)
- [Gerenciar dispositivos obsoletos no Azure AD](manage-stale-devices.md)
