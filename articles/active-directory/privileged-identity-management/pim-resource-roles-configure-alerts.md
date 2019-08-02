---
title: Configurar alertas de segurança para funções de recurso do Azure no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como configurar alertas de segurança para funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7ce8b79644a9ffc9481ba825ec5623a9268983
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476324"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Configurar alertas de segurança para funções de recurso do Azure no PIM
Azure Active Directory (Azure AD) PIM Privileged Identity Management () gera alertas quando há atividade suspeita ou não segura em seu ambiente. Quando um alerta é disparado, ele aparece na página Alertas. 

![Recursos do Azure - listagem de alerta, o nível de risco e a contagem de página de alertas](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Revisar alertas
Selecione um alerta para ver um relatório que lista os usuários ou as funções que dispararam o alerta, com o conselho de correção.

![Verificação de relatório de alerta que mostra a última hora, descrição, etapas de mitigação, tipo, severidade, impacto de segurança e como evitar da próxima vez](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alertas
| Alerta | severity | Gatilho | Recomendações |
| --- | --- | --- | --- |
| **Muitos proprietários atribuídos a um recurso** |Média |Muitos usuários têm a função de proprietário. |Examine os usuários na lista e reatribua alguns a funções menos privilegiadas. |
| **Muitos proprietários permanentes atribuídos a um recurso** |Média |Muitos usuários são permanentemente atribuídos a uma função. |Revise os usuários na lista e reatribua alguns para exigir ativação para o uso da função. |
| **Duplicar função criada** |Média |Várias funções têm os mesmos critérios. |Use apenas uma dessas funções. |


### <a name="severity"></a>severity
* **Alta**: Exige ação imediata devido a uma violação da política. 
* **Média**: Não exige ação imediata, mas sinaliza uma possível violação da política.
* **Baixa**: Não exige ação imediata, mas sugere uma alteração preferencial da política.

## <a name="configure-security-alert-settings"></a>Definir configurações de alerta de segurança
Na página Alertas, vá para **Configurações**.

![Página de alertas com as configurações realçadas](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Personalize configurações nos diferentes alertas para trabalhar com seu ambiente e as metas de segurança.

![Definir página de um alerta habilitar e definir as configurações](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Próximas etapas

- [Definir configurações de função de recurso do Azure no PIM](pim-resource-roles-configure-role-settings.md)
