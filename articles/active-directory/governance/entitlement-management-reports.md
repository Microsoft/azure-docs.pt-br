---
title: Exibir relatórios e logs no gerenciamento de direitos do Azure AD (versão prévia)-Azure Active Directory
description: Saiba como exibir o relatório de atribuições de usuário e os logs de auditoria em Azure Active Directory gerenciamento de direitos (versão prévia).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: d33b4751b421f5af1536af9a88d15e060ab59bdb
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489070"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Exibir relatórios e logs no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> No momento, o gerenciamento de direitos do Azure AD (Azure Active Directory) está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="view-resources-a-user-has-access-to"></a>Exibir recursos aos quais um usuário tem acesso

1. Clique em **Azure Active Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu à esquerda, clique em relatório de atribuições de **usuário**.

1. Clique em **Selecionar usuários** para abrir o painel Selecionar usuários.

1. Localize o usuário na lista para o qual você deseja exibir os recursos aos quais eles têm acesso.

1. Clique no usuário e, em seguida, clique em **selecionar**.

    É exibida uma lista de recursos aos quais o usuário tem acesso. Ele inclui o pacote, a política e as datas do Access.

    ![Relatório de atribuições de usuário](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Determinar o status da solicitação de um usuário

Para obter detalhes adicionais sobre como um usuário solicitou e recebeu acesso a um pacote do Access, você pode usar o log de auditoria do Azure AD. Em particular, você pode usar os registros de log nas `EntitlementManagement` categorias `UserManagement` e para obter detalhes adicionais sobre as etapas de processamento para cada solicitação.  

1. Clique em **Azure Active Directory** e em **logs de auditoria**.

1. Na parte superior, altere a **categoria** para `EntitlementManagement` ou `UserManagement`, dependendo do registro de auditoria que você está procurando.  

1. Clique em **Aplicar**.

1. Para baixar os logs, clique em **baixar**.

Quando o Azure ad recebe uma nova solicitação, ele grava um registro de auditoria, no  qual a `EntitlementManagement` categoria é  e a atividade `User requests access package assignment`normalmente é.  No caso de uma atribuição direta criada no portal do Azure, o campo **atividade** do registro de auditoria é `Administrator directly assigns user to access package`e o usuário que executa a atribuição é identificado pelo **ActorUserPrincipalName**.

O AD do Azure gravará registros de auditoria adicionais enquanto a solicitação estiver em andamento, incluindo:

| Categoria | Atividade | Status da solicitação |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | A solicitação não requer aprovação |
| `UserManagement` | `Create request approval` | A solicitação requer aprovação |
| `UserManagement` | `Add approver to request approval` | A solicitação requer aprovação |
| `EntitlementManagement` | `Approve access package assignment request` | Solicitação aprovada |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Solicitação aprovada ou não requer aprovação |

Quando um usuário recebe acesso, o Azure ad grava um registro de auditoria para `EntitlementManagement` a categoria com **atividade** `Fulfill access package assignment`.  O usuário que recebeu o acesso é identificado pelo campo **ActorUserPrincipalName** .

Se o acesso não tiver sido atribuído, o Azure ad gravará um registro `EntitlementManagement` de auditoria para a `Deny access package assignment request`categoria com **atividade** , se a solicitação tiver sido negada por `Access package assignment request timed out (no approver action taken)`um aprovador, ou se a solicitação tiver expirado antes que um Aprovador pudesse aprovar.

Quando a atribuição de pacote de acesso do usuário expira, é cancelada pelo usuário ou removida por um administrador, o Azure ad grava um registro de `EntitlementManagement` auditoria para a categoria `Remove access package assignment`com a **atividade** de.

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas de gerenciamento de direitos do Azure AD](entitlement-management-troubleshoot.md)
- [Cenários comuns](entitlement-management-scenarios.md)
