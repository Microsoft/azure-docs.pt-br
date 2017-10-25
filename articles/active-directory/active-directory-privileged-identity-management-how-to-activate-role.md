---
title: "Como ativar ou desativar uma função | Microsoft Docs"
description: "Aprenda a ativar funções para identidades com privilégios com o aplicativo Azure Privileged Identity Management."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 1ce9e2e7-452b-4f66-9588-0d9cd2539e45
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: a143fd78eae52fda0cbadb7e74fd8209f24629a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Como ativar ou desativar funções no Azure AD Privileged Identity Management
O Azure Active Directory (AD) Privileged Identity Management simplifica a forma como as empresas gerenciam o acesso privilegiado a recursos no Azure AD e em outros Microsoft Online Services, como o Office 365 ou o Microsoft Intune.  

Se você tiver se tornado elegível para uma função administrativa, isso significa que você poderá ativar essa função quando precisar executar ações que demandam privilégios. Por exemplo, se você ocasionalmente gerencia recursos do Office 365, administradores de função com privilégios de sua organização podem não o tornar um Administrador Global permanente, pois essa função também afeta outros serviços. Em vez disso, eles o tornam qualificado para funções do Azure AD, como Administrador do Exchange Online. Você poderá solicitar a ativação da função quando precisar de seus privilégios e terá controle administrativo por um período de tempo predeterminado.

Este artigo destina-se a administradores que precisam ativar sua função no Azure AD PIM (Privileged Identity Management). Ele o orienta pelas etapas para ativar uma função quando você precisar das permissões e desativar a função quando terminar. Além disso, os administradores com função com privilégios podem exigir aprovação para ativar uma função (Versão prévia). Saiba mais sobre os [Fluxos de trabalho de aprovação de PIM](./privileged-identity-management/azure-ad-pim-approval-workflow.md) aqui.

## <a name="add-the-privileged-identity-management-application"></a>Adicionar o aplicativo Privileged Identity Management
Use o aplicativo Azure AD Privileged Identity Management no [Portal do Azure](https://portal.azure.com/) para solicitar uma ativação de função mesmo se você for operar em outro portal ou por meio do PowerShell. Se você não tiver o aplicativo Azure AD Privileged Identity Management em seu portal do Azure, siga estas etapas para começar.

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Selecione seu nome de usuário no canto superior direito do portal do Azure e selecione o diretório em que você vai operar.
3. Selecione **Mais serviços** e use a caixa de texto Filtrar para procurar **Azure AD Privileged Identity Management**.
4. Marque **Fixar no painel** e então clique em **Criar**. O aplicativo Privileged Identity Management é aberto.

## <a name="activate-a-role"></a>Ativar uma função
Quando precisar assumir uma função, você poderá solicitar a ativação selecionando a opção de navegação **Minhas Funções** na coluna de navegação esquerda do aplicativo Azure AD Privileged Identity Management.

1. Entre no [portal do Azure](https://portal.azure.com/) e selecione o bloco Azure AD Privileged Identity Management.
2. Selecione **Minhas Funções**. Uma lista de suas funções atribuídas qualificadas aparece no agrupamento na parte superior da página.
3. Selecione uma função para ativar.
4. Selecione **Ativar**. A folha **Solicitação de ativação de função** é exibida.
5. Algumas funções exigem MFA (Autenticação Multifator) antes que você possa ativar a função. Você só precisa se autenticar uma vez por sessão.
   
    ![Verifique com o MFA antes da ativação de função - captura de tela][2]
6. Insira o motivo para a solicitação de ativação no campo de texto.  Algumas funções exigem que você forneça um número de tíquete de problema.
7. Selecione **OK**.  Se a função não exigir aprovação, ela já estará ativada e a função será exibida na lista de funções ativas (logo abaixo da lista de atribuições de função qualificadas). Se a [função exigir aprovação](./privileged-identity-management/azure-ad-pim-approval-workflow.md) para ser ativada, uma notificação do sistema será exibida brevemente no canto superior direito do seu navegador informando que a solicitação está com a aprovação pendente.

    ![Notificação de solicitação pendente – captura de tela][3]

## <a name="deactivate-a-role"></a>Desativar uma função
Após uma função ter sido ativada, ela será desativada automaticamente quando limite de tempo (duração qualificada) for atingido.

Se concluir suas tarefas de administração antes, você também poderá desativar uma função manualmente no aplicativo Azure AD Privileged Identity Management.  Selecione **Minhas Funções**, escolha a função que terminou de usar no agrupamento **Atribuições de função ativas** e selecione **Desativar**.  

## <a name="cancel-a-pending-request"></a>Cancelar uma solicitação pendente
Caso não precise da ativação de uma função que requer aprovação, você pode cancelar uma solicitação pendente a qualquer momento. Basta selecionar a opção de navegação **Minhas Funções** na coluna de navegação esquerda do aplicativo Azure AD Privileged Identity Management.

1. Entre no [portal do Azure](https://portal.azure.com/) e selecione o bloco Azure AD Privileged Identity Management.
2. Selecione **Minhas Funções**. Uma lista de suas funções atribuídas qualificadas aparece no agrupamento na parte superior da página.
3. Selecione uma função.
4. Selecione a faixa **A ativação está com aprovação pendente** na folha de detalhes de ativação de função.
5. Selecione **Cancelar** na parte superior da folha **Aprovação pendente**.

   ![Cancelar solicitação pendente – captura de tela][4]

## <a name="next-steps"></a>Próximas etapas
Caso você esteja interessado em aprender mais sobre o Azure AD Privileged Identity Management, os links a seguir têm mais informações.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png
[3]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Toast2.png
[4]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png
