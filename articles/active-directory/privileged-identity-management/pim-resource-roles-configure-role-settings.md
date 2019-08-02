---
title: Definir configurações de função de recurso do Azure no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como definir configurações de função de recurso do Azure no Azure AD PIM (Privileged Identity Management).
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
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6da92ccd50707e676a1f5d583fe22ae8b3d8b73
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476310"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Definir configurações de função de recurso do Azure no PIM

Quando você configura as configurações de função de recurso do Azure, você pode definir as configurações padrão que são aplicadas para atribuições de função de recursos do Azure no Azure Active Directory (Azure AD) gerenciamento de PIM (Privileged Identity). Use os procedimentos a seguir para configurar o fluxo de trabalho de aprovação e especifique quem pode aprovar ou negar solicitações.

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função de recursos do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função [Administrador de Funções com Privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Recursos do Azure**.

1. Clique no recurso que você quer gerenciar, como uma assinatura ou um grupo de gerenciamento.

    ![Listagem de recursos que podem ser gerenciados de página de recursos do Azure](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Clique em **Configurações de função**.

    ![Página de configurações de função, listando as funções de recurso do Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Clique na função cujas configurações você deseja configurar.

    ![Página de detalhes de configuração de função listando várias configurações de atribuição e ativação](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Clique em **Editar** para abrir o painel Configurações de função.

    ![Editar página de configurações de função com opções de atualizar as configurações de atribuição e ativação](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    No painel Configurações de função de cada função, há várias configurações que podem ser configuradas.

## <a name="assignment-duration"></a>Duração dae atribuição

É possível escolher entre duas opções de duração de atribuição para cada tipo de atribuição (qualificada e ativa) ao definir as configurações de uma função. Essas opções passam a ter a duração máxima padrão quando um membro é atribuído à função no PIM.

É possível escolher uma destas opções de duração de atribuição **qualificada**:

| | |
| --- | --- |
| **Permitir atribuição qualificada permanente** | Administradores de recursos podem atribuir membros qualificados permanentes. |
| **Expirar atribuição qualificada após** | Os administradores de recursos podem exigir que todas as atribuições qualificadas tenham uma data de início e de término especificadas. |

E, você pode escolher uma destas opções de duração da atribuição **ativa**:

| | |
| --- | --- |
| **Permitir atribuição ativa permanente** | Administradores de recursos podem atribuir membros ativos permanentes. |
| **Expirar atribuição ativa após** | Os administradores de recursos podem exigir que todas as atribuições ativas tenham uma data de início e de término especificadas. |

> [!NOTE] 
> Todas as atribuições que têm uma data de término especificada poderão ser renovadas por administradores de recursos. Além disso, os membros podem iniciar solicitações de autoatendimento para [estender ou renovar atribuições de função](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Exigir autenticação multifator

O PIM fornece imposição opcional de MFA (Autenticação Multifator) para dois cenários distintos.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Exigir Autenticação Multifator na atribuição ativa

Em alguns casos, você pode querer atribuir um membro a uma função por um curto período (um dia, por exemplo). Nesse caso, eles não precisam que os membros designados solicitem ativação. Nesse cenário, o PIM não pode impor MFA quando o membro usa sua atribuição de função, pois já está ativo na função a partir do momento em que é atribuído.

Para garantir que o administrador de recursos cumprindo a atribuição é quem diz ser, é possível impor MFA na atribuição ativa, marcando a caixa **Exigir Autenticação Multifator na atribuição ativa**.

### <a name="require-multi-factor-authentication-on-activation"></a>Exigir a Autenticação Multifator na ativação

É possível exigir que membros qualificados de uma função executem o MFA antes que possam ser ativados. Esse processo garante que o usuário solicitando ativação seja quem diz ser com uma certeza razoável. A aplicação dessa opção protege recursos críticos em situações em que a conta do usuário pode ter sido comprometida.

Para solicitar que um membro qualificado execute a MFA antes da ativação, marque a caixa **Exigir Autenticação Multifator na ativação**.

Para obter mais informações, consulte [MFA (Autenticação Multifator) e PIM](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Duração máxima de ativação

Use o controle deslizante **Duração máxima da ativação** para definir o tempo máximo, em horas, que uma função permanecerá ativa antes de expirar. Esse valor pode estar entre 1 e 24 horas.

## <a name="require-justification"></a>Exigir justificativa

É possível exigir que os membros insiram uma justificativa na atribuição ativa ou quando são ativados. Para exigir justificativa, marque a caixa **Exigir justificativa na atribuição ativa**  ou a caixa **Exigir justificativa na ativação**.

## <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se você quiser exigir aprovação para ativar uma função, siga estas etapas.

1. Marque a caixa de seleção **Exige aprovação para ativar**.

1. Clique em **Selecionar aprovadores** para abrir o painel Selecionar um membro ou grupo.

    ![Selecione um painel de membro ou grupo a fim de selecionar aprovadores](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecione pelo menos um membro ou grupo e clique em **Selecionar**. É possível adicionar qualquer combinação de membros e grupos. É necessário selecionar pelo menos um aprovador. Não há nenhum aprovador padrão.

    Suas seleções serão exibidas na lista de aprovadores selecionados.

1. Após especificar todas as configurações de função, clique em **Atualizar** para salvar as alterações.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções de recurso do Azure no PIM](pim-resource-roles-assign-roles.md)
- [Configurar alertas de segurança para funções de recurso do Azure no PIM](pim-resource-roles-configure-alerts.md)
