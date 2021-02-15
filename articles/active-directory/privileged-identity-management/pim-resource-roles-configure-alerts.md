---
title: Configurar alertas de segurança para funções de recurso do Azure no Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Saiba como configurar alertas de segurança para funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bfca096eb49ee9f1807935de1dac49151cc8ac3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84743755"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Configurar alertas de segurança para funções de recurso do Azure no Privileged Identity Management

Privileged Identity Management (PIM) gera alertas quando há atividade suspeita ou não segura em sua organização do Azure Active Directory (AD do Azure). Quando um alerta é disparado, ele aparece na página Alertas.

![Recursos do Azure-alerta de listagem de página de alertas, nível de risco e contagem](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Revisar alertas

Selecione um alerta para ver um relatório que lista os usuários ou funções que dispararam o alerta, juntamente com diretrizes de correção.

![Relatório de alertas mostrando a hora da última verificação, descrição, etapas de mitigação, tipo, gravidade, impacto de segurança e como evitar a próxima vez](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alertas

| Alerta | Severity | Gatilho | Recomendação |
| --- | --- | --- | --- |
| **Muitos proprietários atribuídos a um recurso** |Médio |Muitos usuários têm a função de proprietário. |Examine os usuários na lista e reatribua alguns a funções menos privilegiadas. |
| **Muitos proprietários permanentes atribuídos a um recurso** |Médio |Muitos usuários são permanentemente atribuídos a uma função. |Revise os usuários na lista e reatribua alguns para exigir ativação para o uso da função. |
| **Duplicar função criada** |Médio |Várias funções têm os mesmos critérios. |Use apenas uma dessas funções. |

### <a name="severity"></a>Severity

- **Alta**: exige ação imediata devido a uma violação da política. 
- **Média**: não exige ação imediata, mas sinaliza uma possível violação da política.
- **Baixa**: não exige ação imediata, mas sugere uma alteração preferencial da política.

## <a name="configure-security-alert-settings"></a>Definir configurações de alerta de segurança

Na página Alertas, vá para **Configurações**.

![Página de alertas com configurações realçadas](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Personalize configurações nos diferentes alertas para trabalhar com seu ambiente e as metas de segurança.

![Página de configuração de um alerta para habilitar e definir configurações](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Próximas etapas

- [Definir configurações de função de recurso do Azure no Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
