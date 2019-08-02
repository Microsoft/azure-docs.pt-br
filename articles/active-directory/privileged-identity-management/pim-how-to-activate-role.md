---
title: Ativar minhas funções do Azure AD no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como ativar funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7b3e02222580da67a4eeb4159e4fc5dc3b9a98f
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501680"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Ativar minhas funções do Azure AD no PIM

O Azure AD (Azure Active Directory) PIM (Privileged Identity Management) simplifica a forma como as empresas gerenciam o acesso privilegiado a recursos no Azure AD e em outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.  

Se você tiver se tornado elegível para uma função administrativa, isso significa que você poderá ativar essa função quando precisar executar ações que demandam privilégios. Por exemplo, se você ocasionalmente gerencia recursos do Office 365, administradores de função com privilégios de sua organização podem não o tornar um Administrador Global permanente, pois essa função também afeta outros serviços. Em vez disso, eles o tornam qualificado para funções do Azure AD, como Administrador do Exchange Online. Você pode solicitar a ativação da função quando precisar de seus privilégios e terá controle de administrador por um período predeterminado.

Este artigo destina-se a administradores que precisam ativar sua função do Azure AD no PIM.

## <a name="activate-a-role"></a>Ativar uma função

Quando você precisar assumir uma função do Azure AD, você poderá solicitar a ativação usando o **minhas funções** opção de navegação no PIM.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Abra o **Azure AD Privileged Identity Management**. Para obter informações sobre como adicionar o bloco do PIM ao painel, consulte [Começar a usar o PIM](pim-getting-started.md).

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **minhas funções** para ver uma lista de seus qualificados para funções do Azure AD.

    ![Funções do Azure AD - minhas funções mostrando a lista de funções qualificadas ou Active Directory](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Localize uma função que você deseja ativar.

    ![Ativar funções do Azure AD - minha lista de funções qualificadas mostrando o link](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Clique em **Ativar** para abrir o painel Detalhes de ativação de função.

1. Se sua função exigir a MFA (autenticação multifator), clique em **Verificar sua identidade antes de prosseguir**. Você só precisa se autenticar uma vez por sessão.

    ![Verifique se o meu painel de identidade com MFA antes da ativação de função](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Clique em **Verificar minha identidade** e siga as instruções para fornecer a verificação de segurança adicional.

    ![Página de verificação de segurança adicionais, perguntando como entrar em contato com você](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Clique em **Ativar** para abrir o painel Ativação.

    ![Painel de ativação para especificar a hora de início, duração, tíquete e motivo](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Se necessário, especifique uma hora de início de ativação personalizada.

1. Especifique a duração da ativação.

1. Na caixa **Motivo da ativação**, insira o motivo para a solicitação de ativação. Algumas funções exigem que você forneça um número de tíquete de problema.

    ![Painel de ativação concluída com uma hora de início personalizados, duração, tíquete e motivo](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Clique em **Ativar**.

    Se a função não exigir aprovação, uma **status de ativação** painel será exibido que exibe o status de ativação.

    ![Página de status de ativação que mostra três estágios de ativação](./media/pim-how-to-activate-role/activation-status.png)

    Depois que todas as etapas forem concluídas, clique no **sair** link para sair do portal do Azure. Quando você entrar no portal, agora você pode usar a função.

    Se a [função exigir aprovação](./azure-ad-pim-approval-workflow.md) para ser ativada, uma notificação será exibida no canto superior direito do seu navegador informando que a solicitação está com a aprovação pendente.

    ![Solicitação de ativação é a notificação de aprovação pendente](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Exibir o status de suas solicitações

Você pode exibir o status das suas solicitações pendentes a serem ativadas.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Minhas solicitações** para ver uma lista das suas solicitações.

    ![Funções do Azure AD - minha lista de solicitações](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Desativar uma função

Após uma função ter sido ativada, ela será desativada automaticamente quando limite de tempo (duração qualificada) for atingido.

Se concluir suas tarefas de administrador antes, você também poderá desativar uma função manualmente no Azure AD Privileged Identity Management.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Minhas funções**.

1. Clique em **Funções ativas** para ver a lista de funções ativas.

1. Localize a função que você terminou de usar e clique em **Destivar**.

## <a name="cancel-a-pending-request"></a>Cancelar uma solicitação pendente

Caso não precise da ativação de uma função que requer aprovação, você pode cancelar uma solicitação pendente a qualquer momento.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Minhas solicitações**.

1. Para a função que você deseja cancelar, clique no botão **Cancelar**.

    Quando você clicar em Cancelar, a solicitação será cancelada. Para ativar a função novamente, você precisará enviar uma nova solicitação de ativação.

   ![Minha lista de solicitações com o botão Cancelar realçado](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Solucionar problemas

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Permissões não são concedidas após a ativação de uma função

Quando você ativa uma função no PIM, a ativação não pode propagar instantaneamente para todos os portais que exigem a função com privilégios. Às vezes, mesmo se a alteração é propagada, cache da web em um portal pode resultar na alteração não entrarem em vigor imediatamente. Se a ativação está atrasada, aqui está o que você deve fazer.

1. Saia do portal do Azure e entre novamente.

    Quando você ativa uma função do AD do Azure, você verá os estágios de sua ativação. Depois que todas as etapas forem concluídas, você verá uma **sair** link. Você pode usar este link para sair. Isso resolverá a maioria dos casos para o atraso de ativação.

1. No PIM, verifique se que você está listado como o membro da função.

## <a name="next-steps"></a>Próximas etapas

- [Ativar minhas funções de recurso do Azure no PIM](pim-resource-roles-activate-your-roles.md)
