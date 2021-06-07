---
title: Exibir relatório do log de auditoria para funções do Azure AD no Azure AD PIM | Microsoft Docs
description: Saiba como exibir o histórico do log de auditoria para funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8481c562ecbab1f26e877e55a5a9454695ddf4c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370824"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Exibir histórico de auditoria para funções do Azure AD no Privileged Identity Management

Você pode usar o histórico de auditoria de Privileged Identity Management (PIM) para ver todas as atribuições de função e ativações nos últimos 30 dias para todas as funções privilegiadas. Se você quiser ver o histórico de auditoria completo de atividade na organização do Azure Active Directory (Azure AD), incluindo o administrador, o usuário final e a atividade de sincronização, poderá usar os [relatórios de segurança e atividade do Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>Determinar sua versão do PIM

A partir de novembro de 2019, a parte das funções do Azure AD da Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências de funções de recurso do Azure. Isso cria recursos adicionais, bem como [as alterações na API existente](azure-ad-roles-features.md#api-changes). Enquanto a nova versão está sendo distribuída, os procedimentos que você seguir neste artigo dependem da versão do Privileged Identity Management que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Privileged Identity Management você tem. Depois de saber sua versão do Privileged Identity Management, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que esteja na função de [administrador de função com privilégios](../roles/permissions-reference.md#privileged-role-administrator) .
1. Abra **Azure ad Privileged Identity Management**. Se você tiver uma faixa na parte superior da página Visão geral, siga as instruções na **nova guia versão** deste artigo. Caso contrário, siga as instruções na guia **versão anterior** .

    [![Captura de tela que mostra a página "funções do Azure AD – histórico de auditoria de funções de diretório".](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Selecione a guia para sua versão")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[Nova versão](#tab/new)

Siga estas etapas para exibir o histórico de auditoria para funções do Azure AD.

## <a name="view-resource-audit-history"></a>Exibir o histórico de auditoria de recursos

A auditoria de recursos fornece uma exibição de todas as atividades associadas às suas funções do Azure AD.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **auditoria de recurso**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria de recursos com filtros](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Exibir minha auditoria

A opção Minha auditoria permite que você exiba sua atividade de função pessoal.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione o recurso para o qual você deseja exibir o histórico de auditoria.

1. Selecione **minha auditoria**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria para o usuário atual](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="view-audit-history"></a>Exibir histórico de auditoria

Siga estas etapas para exibir o histórico de auditoria para funções do Azure AD.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função [Administrador de Funções com Privilégios](../roles/permissions-reference.md#privileged-role-administrator).

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione o **histórico de auditoria de funções de diretório**.

    Dependendo do histórico de auditoria, um gráfico de colunas é exibido junto com o total de ativações, Max ativações por dia e média de ativações por dia.

    [![Nova versão das funções do Azure AD](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Exibir histórico de auditoria de funções de diretório")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Na parte inferior da página, é exibida uma tabela com informações sobre cada ação no histórico de auditoria disponível. As colunas têm os seguintes significados:

    | Coluna | Descrição |
    | --- | --- |
    | Hora | Quando a ação ocorreu. |
    | Solicitante | Usuário que solicitou a ativação ou alteração da função. Se o valor for **sistema do Azure**, verifique o histórico de auditoria do Azure para obter mais informações. |
    | Ação | Ações tomadas pelo solicitante. As ações podem incluir atribuir, Cancelar atribuição, ativar, desativar ou AddedOutsidePIM. |
    | Membro | Usuário que está ativando ou atribuído a uma função. |
    | Função | Função atribuída ou ativada pelo usuário. |
    | Justificativa | Texto inserido no campo motivo durante a ativação. |
    | Expiração | Quando uma função ativada expira. Aplica-se somente a atribuições de função qualificadas. |

1. Para classificar o histórico de auditoria, clique nos botões de **hora**, **ação** e **função** .

## <a name="filter-audit-history"></a>Filtrar histórico de auditoria

1. Na parte superior da página Histórico de auditoria, clique no botão **Filtrar** .

    O painel **atualizar parâmetros do gráfico** é exibido.

1. Em **intervalo de tempo**, selecione um intervalo de tempo.

1. Em **funções**, marque as caixas de seleção para indicar as funções que você deseja exibir.

    ![Atualizar painel de parâmetros do gráfico](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Selecione **concluído** para exibir o histórico de auditoria filtrado.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Obter motivo, Aprovador e número do tíquete para eventos de aprovação

1. Entre no [portal do Azure](https://aad.portal.azure.com) com permissões de função de administrador de função com privilégios e abra o Azure AD.
1. Selecione **Logs de Auditoria**.
1. Use o filtro de **serviço** para exibir apenas eventos de auditoria para o serviço Privileged Identity Management. Na página **logs de auditoria** , você pode:

    - Consulte o motivo de um evento de auditoria na coluna **razão do status** .
    - Consulte o aprovador na coluna **iniciado por (ator)** do evento "Adicionar membro à solicitação de função aprovada".

    [![Captura de tela que mostra a página "logs de auditoria" com o menu "iniciado por (ator) aberto e" PIM "selecionados.](media/pim-how-to-use-audit-log/filter-audit-logs.png "Filtrar o log de auditoria para o serviço PIM")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. Selecione um evento de log de auditoria para ver o número do tíquete na guia **atividade** do painel de **detalhes** .
  
    [![Captura de tela que mostra a página "logs de auditoria" com o número do tíquete realçado no painel "detalhes".](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "Verificar o número do tíquete do evento de auditoria")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. Você pode exibir o solicitante (pessoa ativando a função) na guia **destinos** do painel de **detalhes** para um evento de auditoria. Há dois tipos de destino para as funções do Azure AD:

    - A função (**Type** = Role)
    - O solicitante (**tipo** = usuário)

Normalmente, o evento log de auditoria imediatamente acima do evento de aprovação é um evento para "Adicionar membro à função concluído" **, onde o iniciado por (ator)** é o solicitante. Na maioria dos casos, você não precisará localizar o solicitante na solicitação de aprovação de uma perspectiva de auditoria.

---

## <a name="next-steps"></a>Próximas etapas

- [Exibir a atividade e o histórico de auditoria das funções de recurso do Azure no Privileged Identity Management](azure-pim-resource-rbac.md)
