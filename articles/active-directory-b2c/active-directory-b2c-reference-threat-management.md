---
title: "Azure Active Directory B2C: gerenciamento de ameaças| Microsoft Docs"
description: "Saiba mais sobre técnicas de detecção e mitigação de ataques de negação de serviço e ataques de senha no Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: Ajith Alexander
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2016
ms.author: 
ms.openlocfilehash: 9472cb01eb713e297053727b1a314293574bb657
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-threat-management"></a>Azure Active Directory B2C: gerenciamento de ameaças

O gerenciamento de ameaças inclui planejamento para proteção contra ataques ao sistema e às redes. Os ataques de negação de serviço podem tornar os recursos não disponíveis para usuários pretendidos. Os ataques de senha ocasionam o acesso não autorizado aos recursos. O Azure Active Directory B2C (Azure AD B2C) tem recursos internos que podem ajudá-lo a proteger seus dados contra essas ameaças de várias maneiras.

## <a name="denial-of-service-attacks"></a>Ataques de negação de serviço

O Azure AD B2C usa técnicas de detecção e de mitigação como cookies SYN e limites de taxa e de conexão para proteger os recursos subjacentes contra esses ataques de negação de serviço.

## <a name="password-attacks"></a>Ataques de senha

O Azure AD B2C também tem técnicas de mitigação prontas para ataques de senha. A mitigação inclui ataques de senhas de força bruta e ataques de senhas de dicionário. As senhas definidas pelos usuários devem ser de complexidade razoável. Ao usar vários sinais, o Azure AD B2C analisa a integridade das solicitações. O Azure AD B2C foi projetado para diferenciar, de forma inteligente, os usuários pretendidos de hackers e botnets. O Azure AD B2C oferece uma estratégia sofisticada para bloquear contas com base nas senhas digitadas, na probabilidade de um ataque.

Para obter mais informações, consulte o [Centro de Confiabilidade da Microsoft](https://www.microsoft.com/trustcenter/security/threatmanagement).
