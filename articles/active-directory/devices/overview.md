---
title: O que é a identidade do dispositivo no Azure Active Directory?
description: Saiba como o gerenciamento de identidade de dispositivos pode ajudá-lo a gerenciar os dispositivos que estão acessando recursos em seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d38ca8bdf93ff201b3f5842f4cb0e8409dcd0c3
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481669"
---
# <a name="what-is-a-device-identity"></a>O que é uma identidade do dispositivo?

Devido à proliferação de dispositivos de todos os tipos e tamanhos e ao conceito de BYOD (Traga seu próprio dispositivo), os profissionais de TI vêm se deparando com duas metas ligeiramente opostas:

- Capacitar os usuários finais para serem produtivos em qualquer situação e em qualquer lugar
- Proteger os ativos da organização

Para proteger esses ativos, a equipe de TI precisa primeiro gerenciar as identidades dos dispositivos. Eles podem usar a identidade do dispositivo como base em ferramentas como o Microsoft Intune para garantir o atendimento aos padrões de segurança e conformidade. O Azure AD (Azure Active Directory) permite o logon único em dispositivos, aplicativos e serviços em qualquer lugar por meio desses dispositivos.

- Seus usuários podem obter acesso aos ativos da organização de que precisam. 
- A equipe de TI obtém os controles de que precisam para proteger sua organização.

O gerenciamento de identidade de dispositivos também é a base para o [acesso condicional com base em dispositivos](../conditional-access/require-managed-devices.md). Com as políticas de acesso condicional baseadas em dispositivos, é possível garantir que o acesso a recursos em seu ambiente seja feita apenas por dispositivos gerenciados.

## <a name="getting-devices-in-azure-ad"></a>Obter dispositivos no Azure AD

Há várias opções para colocar um dispositivo no Azure AD:

- **Registrado no Azure AD**
   - Os dispositivos que estão registrados no Azure AD normalmente são dispositivos móveis ou de propriedade pessoal e são conectados com uma conta Microsoft pessoal ou outra conta local.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Ingressado no Azure AD**
   - Os dispositivos que estão ingressados no Azure AD são de propriedade de uma organização e são conectados com uma conta do Azure AD que pertence a tal organização. Elas existem somente na nuvem.
      - Windows 10 
- **Ingressado no Azure AD híbrido**
   - Os dispositivos que estão ingressados no Azure AD híbrido são de propriedade de uma organização e são conectados a uma conta do Azure AD que pertence a tal organização. Eles existem na nuvem e 
      - no Windows 7, 8.1 ou 10
      - no Windows Server 2008 ou mais recente

![Dispositivos exibidos na folha Dispositivos do Azure AD](./media/overview/azure-ad-devices-all-devices-overview.png)

## <a name="device-management"></a>Gerenciamento de dispositivos

Os dispositivos no Azure AD podem ser gerenciados usando ferramentas de MDM (Gerenciamento de Dispositivo Móvel), como o Microsoft Intune, o System Center Configuration Manager ou a Política de Grupo (ingresso no Azure AD híbrido), ferramentas de MAM (Gerenciamento de Aplicativo Móvel) ou outras ferramentas de terceiros.

## <a name="resource-access"></a>Acesso a recursos

O registro e o ingresso oferecem aos usuários SSO (Logon Único) Contínuo a recursos da nuvem e aos administradores a capacidade de aplicar políticas de Acesso Condicional a esses recursos. 

Os dispositivos ingressados no Azure AD ou no Azure AD híbrido beneficiam-se do SSO nos recursos locais da sua organização e nos recursos de nuvem. Saiba mais no artigo [Como funciona o SSO em recursos locais para dispositivos ingressados no Azure AD](azuread-join-sso.md).

## <a name="device-security"></a>Segurança do dispositivo

- Os **dispositivos registrados no Azure AD** utilizam uma conta gerenciada pelo usuário final; essa conta é uma conta Microsoft ou outra credencial gerenciada localmente e protegida com uma ou mais das opções a seguir.
   - Senha
   - PIN
   - Padrão
   - Windows Hello
- Os **dispositivos ingressados no Azure AD ou no Azure AD híbrido** utilizam uma conta organizacional no Azure AD protegida com uma ou mais das opções a seguir.
   - Senha
   - Windows Hello for Business

## <a name="provisioning"></a>Provisionamento

A obtenção de dispositivos no Azure AD pode ser feita como autoatendimento ou por um processo de provisionamento controlado pelos administradores.

## <a name="summary"></a>Resumo

Com o gerenciamento de identidade do dispositivo no Azure AD, é possível:

- Simplificar o processo de colocar e gerenciar dispositivos no Azure AD
- Fornecer aos usuários um acesso fácil de usar a recursos baseados em nuvem da sua organização

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [dispositivos registrados no Azure AD](concept-azure-ad-register.md)
- Saiba mais sobre [dispositivos ingressados no Azure AD](concept-azure-ad-join.md)
- Saiba mais sobre [dispositivos ingressados no Azure AD híbrido](concept-azure-ad-join-hybrid.md)
- Confira uma visão geral de como gerenciar identidades de dispositivo no portal do Azure em [Gerenciar identidades de dispositivo usando o portal do Azure](device-management-azure-portal.md).
- Para saber mais sobre o acesso condicional baseado em dispositivo, confira [Configurar as políticas de acesso condicional com base em dispositivo do Azure Active Directory](../conditional-access/require-managed-devices.md).
