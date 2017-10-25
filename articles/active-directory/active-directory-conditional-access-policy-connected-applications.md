---
title: "Configurar políticas de acesso condicional com base no dispositivo do Azure Active Directory | Microsoft Docs"
description: "Saiba como configurar políticas de acesso condicional com base no dispositivo do Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: a57edd30975ec0e943fd84b2c66137d328a89b8b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Configurar políticas de acesso condicional com base no dispositivo do Azure Active Directory

Com o [acesso condicional do Azure AD (Active Directory)](active-directory-conditional-access-azure-portal.md), você pode ajustar como usuários autorizados podem acessar seus recursos. Por exemplo, é possível limitar o acesso a dispositivos confiáveis para determinados recursos. Uma política de acesso condicional que exige um dispositivo confiável também é conhecida como política de acesso condicional com base no dispositivo.

Este tópico fornece informações sobre como configurar políticas de acesso condicional com base no dispositivo para aplicativos conectados pelo Azure AD. 


## <a name="before-you-begin"></a>Antes de começar

O acesso condicional com base no dispositivo associa o **acesso condicional do Azure AD** e o **gerenciamento de dispositivos do Azure AD**. Caso ainda não esteja familiarizado com uma dessas áreas, você deverá ler os seguintes tópicos primeiro:

- **[Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md)** - esse tópico fornece uma visão geral conceitual do acesso condicional e da terminologia relacionada.

- **[Introduction to device management in Azure Active Directory](device-management-introduction.md)** (Introdução ao gerenciamento de dispositivos no Azure Active Directory) - esse tópico fornece uma visão geral das várias opções para conectar dispositivos ao Azure AD. 


## <a name="trusted-devices"></a>Dispositivos confiáveis

Em um mundo móvel e em nuvem, o Azure Active Directory permite o logon único para dispositivos, aplicativos e serviços de qualquer lugar. Para determinados recursos no seu ambiente, conceder acesso aos usuários certos talvez não seja o suficiente. Além dos usuários certos, você também pode exigir que um dispositivo confiável seja usado para acessar um recurso. Em seu ambiente, é possível definir em que um dispositivo confiável se baseia nos seguintes componentes:

- As [plataformas de dispositivo](active-directory-conditional-access-azure-portal.md#device-platforms) em um dispositivo
- Se um dispositivo está em conformidade
- Se um dispositivo foi adicionado ao domínio 

As [plataformas de dispositivo](active-directory-conditional-access-azure-portal.md#device-platforms) são caracterizadas pelo sistema operacional que está em execução no seu dispositivo. Em sua política de acesso condicional com base no dispositivo, você pode limitar o acesso para determinados recursos a plataformas de dispositivo específicas.



Em uma política de acesso condicional com base no dispositivo, é possível exigir que dispositivos confiáveis sejam marcados como em conformidade.

![Aplicativos na nuvem](./media/active-directory-conditional-access-policy-connected-applications/24.png)

Os dispositivos podem ser marcados como em conformidade no diretório por um destes recursos:

- Intune 
- Um sistema gerenciado por dispositivo móvel de terceiros que gerencia os dispositivos Windows 10 por meio da integração do Azure AD 
 
  

Somente dispositivos que são conectados ao Azure AD podem ser marcados como em conformidade. Para conectar um dispositivo ao Azure Active Directory, você tem as seguintes opções: 

- Registrado no Azure AD
- Adicionado ao Azure AD
- Adicionado ao Azure AD híbrido

    ![Aplicativos na nuvem](./media/active-directory-conditional-access-policy-connected-applications/26.png)

Se você tiver um espaço físico do AD (Active Directory) local, é possível considerar dispositivos que não estão conectados ao Azure AD, mas adicionados ao seu AD para serem confiáveis.

![Aplicativos na nuvem](./media/active-directory-conditional-access-policy-connected-applications/25.png)


## <a name="next-steps"></a>Próximas etapas

Antes de configurar uma política de acesso condicional com base no dispositivo em seu ambiente, você deve dar uma olhada nas [melhores práticas para acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md).

