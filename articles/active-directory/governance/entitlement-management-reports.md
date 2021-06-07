---
title: Exibir relatórios & logs no gerenciamento de direitos-Azure AD
description: Saiba como exibir o relatório de atribuições de usuário e os logs de auditoria em Azure Active Directory gerenciamento de direitos.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6eb44c1efd683b6febe9a355ef72c80cc6f2e40d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97746619"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Exibir relatórios e logs no gerenciamento de direitos do Azure AD

Os relatórios de gerenciamento de direitos do Azure AD e o log de auditoria do Azure AD fornecem detalhes adicionais sobre quais recursos os usuários têm acesso. Como administrador, você pode exibir os pacotes de acesso e as atribuições de recursos para um usuário e exibir logs de solicitação para fins de auditoria ou para determinar o status da solicitação de um usuário. Este artigo descreve como usar os relatórios de gerenciamento de direitos e os logs de auditoria do Azure AD.

Assista ao vídeo a seguir para saber como exibir a quais recursos os usuários têm acesso no gerenciamento de direitos:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Exibir pacotes de acesso para um usuário

Este relatório permite que você liste todos os pacotes de acesso que um usuário pode solicitar e os pacotes de acesso atribuídos ao usuário no momento.

**Função de pré-requisito:** Administrador global ou administrador de usuário

1. Clique em **Azure Active Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu à esquerda, clique em **relatórios**.

1. Clique em **pacotes de acesso para um usuário**.

1. Clique em **Selecionar usuários** para abrir o painel Selecionar usuários.

1. Localize o usuário na lista e, em seguida, clique em **selecionar**.

    A guia **pode solicitar** exibe uma lista dos pacotes de acesso que o usuário pode solicitar. Essa lista é determinada pelas [políticas de solicitação](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) definidas para os pacotes de acesso. 

    ![Pacotes de acesso para um usuário](./media/entitlement-management-reports/access-packages-report.png)

1. Se houver mais de uma função ou políticas de recurso para um pacote do Access, clique na entrada funções de recurso ou políticas para ver os detalhes da seleção.

1. Clique na guia **atribuído** para ver uma lista dos pacotes de acesso atribuídos no momento ao usuário. Quando um pacote do Access é atribuído a um usuário, isso significa que o usuário tem acesso a todas as funções de recurso no pacote do Access.

## <a name="view-resource-assignments-for-a-user"></a>Exibir atribuições de recursos para um usuário

Este relatório permite que você liste os recursos atualmente atribuídos a um usuário no gerenciamento de direitos. Observe que esse relatório é para recursos gerenciados com gerenciamento de direitos. O usuário pode ter acesso a outros recursos em seu diretório fora do gerenciamento de direitos.

**Função de pré-requisito:** Administrador global ou administrador de usuário

1. Clique em **Azure Active Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu à esquerda, clique em **relatórios**.

1. Clique em **atribuições de recurso para um usuário**.

1. Clique em **Selecionar usuários** para abrir o painel Selecionar usuários.

1. Localize o usuário na lista e, em seguida, clique em **selecionar**.

    Uma lista dos recursos atribuídos ao usuário no momento é exibida. A lista também mostra o pacote de acesso e a política da qual ele obteve a função de recurso, juntamente com a data de início e de término do acesso.
    
    Se um usuário tem acesso ao mesmo recurso em dois ou mais pacotes, você pode clicar em uma seta para ver cada pacote e política.

    ![Atribuições de recursos para um usuário](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Determinar o status da solicitação de um usuário

Para obter detalhes adicionais sobre como um usuário solicitou e recebeu acesso a um pacote do Access, você pode usar o log de auditoria do Azure AD. Em particular, você pode usar os registros de log nas `EntitlementManagement` `UserManagement` categorias e para obter detalhes adicionais sobre as etapas de processamento para cada solicitação.  

1. Clique em **Azure Active Directory** e em **logs de auditoria**.

1. Na parte superior, altere a **categoria** para `EntitlementManagement` ou `UserManagement` , dependendo do registro de auditoria que você está procurando.  

1. Clique em **Aplicar**.

1. Para baixar os logs, clique em **baixar**.

Quando o Azure AD recebe uma nova solicitação, ele grava um registro de auditoria, no qual a **categoria** é `EntitlementManagement` e a **atividade** normalmente é `User requests access package assignment` .  No caso de uma atribuição direta criada no portal do Azure, o campo **atividade** do registro de auditoria é `Administrator directly assigns user to access package` e o usuário que executa a atribuição é identificado pelo **ActorUserPrincipalName**.

O AD do Azure gravará registros de auditoria adicionais enquanto a solicitação estiver em andamento, incluindo:

| Categoria | Atividade | Status da solicitação |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | A solicitação não requer aprovação |
| `UserManagement` | `Create request approval` | A solicitação requer aprovação |
| `UserManagement` | `Add approver to request approval` | A solicitação requer aprovação |
| `EntitlementManagement` | `Approve access package assignment request` | Solicitação aprovada |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Solicitação aprovada ou não requer aprovação |

Quando um usuário recebe acesso, o Azure AD grava um registro de auditoria para a `EntitlementManagement` categoria com **atividade** `Fulfill access package assignment` .  O usuário que recebeu o acesso é identificado pelo campo **ActorUserPrincipalName** .

Se o acesso não tiver sido atribuído, o Azure AD gravará um registro de auditoria para a `EntitlementManagement` categoria com **atividade** `Deny access package assignment request` , se a solicitação tiver sido negada por um aprovador, ou `Access package assignment request timed out (no approver action taken)` se a solicitação tiver expirado antes que um Aprovador pudesse aprovar.

Quando a atribuição de pacote de acesso do usuário expira, é cancelada pelo usuário ou removida por um administrador, o Azure AD grava um registro de auditoria para a `EntitlementManagement` categoria com a **atividade** de `Remove access package assignment` .

## <a name="next-steps"></a>Próximas etapas

- [Arquivar relatórios e logs](entitlement-management-logs-and-reporting.md)
- [Solucionar problemas de gerenciamento de direitos do Azure AD](entitlement-management-troubleshoot.md)
- [Cenários comuns](entitlement-management-scenarios.md)
