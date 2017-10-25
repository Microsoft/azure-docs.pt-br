---
title: "Fluxos de trabalho de aprovação do Azure Privileged Identity Management | Microsoft Docs"
description: "Saiba mais sobre os fluxos de trabalho de aprovação do PIM (Privileged Identity Management)"
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: cf6a9213fa0a1cba8725aabb42abe51b805ece7a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="approvals-preview"></a>Aprovações (Visualização)

## <a name="overview"></a>Visão geral

Com Aprovações para o Privileged Identity Management, você pode configurar funções para solicitar aprovação para ativação e escolher um ou vários usuários ou grupos como aprovadores delegados. Continue lendo para saber como configurar funções e selecionar aprovadores.

>[!NOTE]
Tenha em mente que esse recurso ainda está em desenvolvimento e você poderá encontrar bugs. A funcionalidade, incluindo o texto e as convenções de nomenclatura, está sujeita a alterações e não deverá ser considerada definitiva.


## <a name="key-terminology"></a>Terminologia principal

*Usuário de Função Qualificada* – um usuário de função qualificada é um usuário de sua organização que foi atribuído a uma função do Azure AD como qualificada (a função exige a ativação).

*Aprovador Delegado* – um aprovador delegado é um ou vários indivíduos ou grupos do Azure AD responsáveis por aprovar solicitações de ativação de função.

## <a name="scenarios"></a>Cenários

A visualização particular dá suporte aos seguintes cenários:

**Como um PRA (Administrador de Função com Privilégios), você pode:**

-   [habilitar a aprovação para funções específicas](#enable-approval-for-specific-roles)

-   [especificar usuários e/ou grupos aprovadores para aprovar solicitações](#specify-approver-users-and/or-groups-to-approve-requests)

-   [exibir o histórico de solicitações e aprovações de todas as funções com privilégios](#view-request-and-approval-history-for-all-privileged-roles)

**Como um aprovador designado, você pode:**

-   [exibir as aprovações pendentes (solicitações)](#view-pending-approvals-requests)

-   [aprovar ou rejeitar solicitações de elevação de função (única e/ou em massa)](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [fornecer uma justificativa para minha aprovação/rejeição](#provide-justification-for-my-approval/rejection) 

**Como um Usuário de Função Qualificada, você pode:**

-   [solicitar a ativação de uma função que exige aprovação](#request-activation-of-a-role-that-requires-approval)

-   [exibir o status de sua solicitação a ser ativada](#view-the-status-of-your-request-to-activate)

-   [concluir a tarefa no Azure AD caso a ativação tenha sido aprovada](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>Navegação

Atualizamos a navegação para dar suporte a aprovações

![](media/azure-ad-pim-approval-workflow/image001.png)

A página de aterrissagem padrão fornece acesso conveniente às informações sobre o PIM e à nova documentação de aprovações.

![](media/azure-ad-pim-approval-workflow/image002.png)

Também adicionamos uma nova seção para todos os usuários do PIM, “Meu Histórico de Auditoria”. Aqui você pode encontrar todas as informações relevantes à sua identidade. Isso inclui todas as suas solicitações pendentes e concluídas, decisões feitas sobre as solicitações resolvidas e todas as ativações de função anteriores em um único local conveniente.

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>Habilitar a aprovação para funções específicas

Para habilitar a aprovação para uma função específica, primeiro selecione Funções de Diretório na barra de navegação à esquerda.

![](media/azure-ad-pim-approval-workflow/image004.png)

Localizar e selecionar as configurações na barra de navegação à esquerda de Funções de Diretório

![](media/azure-ad-pim-approval-workflow/image006.png)

Selecionar Funções com privilégios:

![](media/azure-ad-pim-approval-workflow/image009.png)

Selecione “Habilitar” na seção Exigir aprovação:

![](media/azure-ad-pim-approval-workflow/image011.png)

Depois de habilitada, a folha será expandida para mostrar os seguintes detalhes:

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
Se você NÃO especificar nenhum aprovador, os PRAs se tornarão os aprovadores padrão. Os PRAs precisarão aprovar TODAS as solicitações de ativação dessa função.

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>Especificar usuários e/ou grupos aprovadores para aprovar solicitações

Para delegar a aprovação, clique na opção para “Selecionar aprovadores”:

![](media/azure-ad-pim-approval-workflow/image015.png)

Quando a folha Selecionar aprovadores for carregada, você poderá pesquisar um usuário ou grupo específico usando a barra de pesquisa na parte superior ou selecionando na lista pré-populada e, em seguida, clicar em “Selecionar” quando terminar:

![](media/azure-ad-pim-approval-workflow/image017.png)

Observação: é possível selecionar vários usuários ou grupos por vez.

Sua seleção será exibida na lista de aprovadores selecionados, conforme visto abaixo:

![](media/azure-ad-pim-approval-workflow/image019.png)

Para remover um aprovador, basta clicar no botão Remover ao lado do nome.

Para adicionar outros aprovadores, repita o processo.

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>Exibir o histórico de solicitações e aprovações de todas as funções com privilégios

Para exibir o histórico de solicitações e aprovações de todas as funções com privilégios, selecione Histórico de Auditoria do painel:

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
É possível classificar os dados por Ação e procurar “Ativação Aprovada”

### <a name="view-pending-approvals-requests"></a>Exibir as aprovações pendentes (solicitações)

Como aprovador delegado, você receberá notificações por email quando uma solicitação estiver aguardando sua aprovação. Para exibir essas solicitações no portal do PIM, no painel (na nova barra de navegação), selecione a guia “Solicitações com Aprovação Pendente” na barra de navegação à esquerda.

![](media/azure-ad-pim-approval-workflow/image023.png)

Aqui, você verá uma lista de solicitações com aprovação pendente:

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>Aprovar ou rejeitar solicitações de elevação de função (única e/ou em massa)

Selecione as solicitações que você deseja aprovar ou negar e clique no botão na barra de ação que corresponde à sua decisão:

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>Fornecer uma justificativa para minha aprovação/rejeição

Isso abrirá uma nova folha para aprovar ou negar várias solicitações ao mesmo tempo. Insira uma justificativa para sua decisão e clique em Aprovar (ou Negar) na parte inferior ou na folha:

![](media/azure-ad-pim-approval-workflow/image029.png)

Quando o processo de solicitação for concluído, o símbolo de status refletirá a decisão tomada (neste exemplo, a decisão é aprovar):

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>Solicitar a ativação de uma função que exige aprovação

A solicitação da ativação de uma função que exige aprovação pode ser iniciada na barra de navegação antiga do PIM ou na nova barra de navegação, pois o processo de ativação de função permanece o mesmo. Basta selecionar uma função na lista de funções a ser ativada:

![](media/azure-ad-pim-approval-workflow/image033.png)

Se uma função com privilégios exigir a Autenticação Multifator, você deverá concluir essa tarefa primeiro:

![](media/azure-ad-pim-approval-workflow/image035.png)

Depois de concluída, clique em Ativar e forneça uma justificativa (se necessário):

![](media/azure-ad-pim-approval-workflow/image037.png)

O solicitante verá uma notificação indicando que a solicitação está com aprovação pendente:

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>Exibir o status de sua solicitação a ser ativada

A exibição do status de uma solicitação pendente a ser ativada deve ser acessada na nova barra de navegação. Na barra de navegação à esquerda, selecione a guia “Minhas Solicitações”:

![](media/azure-ad-pim-approval-workflow/image041.png)

O estado de solicitação usa “Pendente” como padrão, mas é possível ativar/desativar para ver todas as solicitações ou as solicitações negadas.

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>Concluir a tarefa no Azure AD caso a ativação tenha sido aprovada

Depois que a solicitação for aprovada, a função ficará ativa e você poderá continuar com qualquer trabalho que exige essa função.

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>Próximas etapas

Seu comentário é valioso para nós. Fique à vontade compartilhar seus comentários conosco aqui!
