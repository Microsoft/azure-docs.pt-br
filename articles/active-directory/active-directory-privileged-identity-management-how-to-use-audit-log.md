---
title: Como usar o log de auditoria no Azure AD Privileged Identity Management | Microsoft Docs
description: "Saiba como usar o log de auditoria na extensão do Privileged Identity Management do Azure."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 5d13a6dd-1fcb-4e76-82fb-cb2f4f0e4357
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 7d9a5255a64d46c1388d328a606b3f297d61262b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-audit-log-in-pim"></a>Usar o log de auditoria no PIM
Você pode usar o log de auditoria do PIM (Privileged Identity Management) para ver todas as ativações e atribuições de usuário dentro de um determinado período de tempo. Se você quiser ver o histórico completo de auditoria da atividade em seu locatário, incluindo o administrador, usuário final e atividade de sincronização, use os [Relatórios de acesso e uso do Azure Active Directory.](active-directory-view-access-usage-reports.md)

## <a name="navigate-to-the-audit-log"></a>Navegar até o log de auditoria
No painel do [portal do Azure](https://portal.azure.com) , selecione o aplicativo **Azure AD Privileged Identity Management** . Aqui, acesse o log de auditoria clicando em **Gerenciar funções privilegiadas** > **Histórico de auditoria** no painel do PIM.

## <a name="the-audit-log-graph"></a>O gráfico do log de auditoria
Você pode usar o log de auditoria para exibir, em um gráfico de linha, o total de ativações, o número máximo de ativações diárias e a média diária de ativações.  Você também pode filtrar os dados por função se houver mais de uma função no histórico de auditoria.

Use os botões de **tempo**, **ação** e **função** para classificar o log.

## <a name="the-audit-log-list"></a>A lista de logs de auditoria
As colunas na lista de logs de auditoria são:

* **Solicitante** : o usuário que solicitou a ativação ou alteração da função.  Se o valor for "Sistema Azure", verifique o log de auditoria do Azure para obter mais informações.
* **Usuário** : o usuário que está ativando ou está atribuído a uma função.
* **Função** : a função atribuída ou ativada pelo usuário.
* **Ação** : as ações tomadas pelo solicitante. Isso pode incluir a atribuição, o cancelamento da atribuição, a ativação ou a desativação.
* **Tempo** - quando a ação aconteceu.
* **Raciocínio** -se qualquer texto foi digitado no campo de motivo durante a ativação, ele será mostrado aqui.
* **Expiração** : relevante apenas para a ativação de funções.

## <a name="filter-the-audit-log"></a>Filtrar o log de auditoria
Você pode filtrar as informações que aparecem no log de auditoria clicando no botão **Filtrar** .  A **folha Atualizar parâmetros do gráfico** será exibida.

Depois de definir os filtros, clique em **Atualizar** para filtrar os dados no log.  Se os dados não aparecerem imediatamente, atualize a página.

### <a name="change-the-date-range"></a>Alterar o intervalo de data
Use os botões **Hoje**, **Última semana**, **Mês Passado** ou **Personalizar** para alterar o intervalo de tempo do log de auditoria.

Quando você escolher o botão **Personalizar**, terá um campo de data **De** e um campo de data **Até** para especificar um intervalo de datas para o log.  Você pode digitar as datas no formato DD/MM/AAAA, ou clique no ícone **calendário** e escolha a data em um calendário.

### <a name="change-the-roles-included-in-the-log"></a>Alterar as funções incluídas no log
Marque ou desmarque a caixa de seleção **Função** ao lado de cada função para incluí-la ou excluí-la do log.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

