---
title: Começar a usar o PIM - Azure Active Directory | Microsoft Docs
description: Saiba como ativar e começar a usar o PIM (Gerenciamento de Identidades Privilegiadas) do Azure Active Directory Privileged Identity Management no portal do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 408991ffc3922986234f7d40e1cd589b1d126ba1
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476479"
---
# <a name="start-using-pim"></a>Comece a usar o PIM

Com o Azure AD (Azure Active Directory) PIM (Privileged Identity Management), você pode gerenciar, controlar e monitorar o acesso em sua organização. Esse escopo inclui o acesso a recursos do Azure, Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Este artigo descreve como habilitar e começar a usar o PIM.

## <a name="prerequisites"></a>Pré-requisitos

Para usar o PIM, você deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Para obter mais informações, veja [Requisitos de licença para usar o PIM](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>Primeira pessoa a usar o PIM

Se você for a primeira pessoa a usar o PIM em seu diretório, receberá automaticamente as funções [Administrador de Segurança](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) e [Administrador de Função Privilegiada](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) no diretório. Somente os administradores com privilégios de função podem gerenciar atribuições de função do AD do Azure de usuários. Além disso, você pode optar por executar o [assistente de segurança](pim-security-wizard.md) que o orientará na experiência inicial de descoberta e atribuição.

## <a name="enable-pim"></a>Habilita o PIM

Para começar a usar o PIM no seu diretório, você deve primeiro habilitar o PIM.

1. Faça login no [portal do Microsoft Azure](https://portal.azure.com/) como administrador global do seu diretório.

    Você deve ser um Administrador Global com uma conta organizacional (por exemplo, @yourdomain.com), não uma conta da Microsoft (por exemplo, @outlook.com), para habilitar o PIM para um diretório.

1. Clique em **Todos os serviços** e encontre o serviço **Azure AD Privileged Identity Management**.

    ![Azure AD Privileged Identity Management em Todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Clique para abrir o Início Rápido do PIM.

1. Na lista, clique em **Consentimento para o PIM**.

    ![Consentimento para o PIM para habilitar o PIM](./media/pim-getting-started/consent-pim.png)

1. Clique em **verificar minha identidade** para verificar sua identidade com o Azure MFA. Você será solicitado a escolher uma conta.

    ![Escolher uma janela de conta para verificar sua identidade](./media/pim-getting-started/pick-account.png)

1. Se mais informações forem necessárias para a verificação, você será guiado pelo processo. Para mais informações, consulte [Obter ajuda para a confirmação em dois passos](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![Mais informações necessárias a janela se sua organização precisa de mais informações](./media/pim-getting-started/more-information-required.png)

    Por exemplo, você pode ser solicitado a fornecer confirmação por telefone.

    ![Página de verificação de segurança adicionais, perguntando como entrar em contato com você](./media/pim-getting-started/additional-security-verification.png)

1. Depois de concluir o processo de verificação, clique no botão **consentimento**.

1. Na mensagem exibida, clique em **Sim** para consentir com o serviço PIM.

    ![Consentimento à mensagem PIM para concluir o processo de consentimento](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Inscreva-se o PIM para funções do Microsoft Azure Active Directory

Depois que você habilitou o PIM para seu diretório, você precisará inscrever-se de PIM para gerenciar as funções do Microsoft Azure Active Directory.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **funções do Microsoft Azure Active Directory**.

    ![Inscreva-se o PIM para funções do Microsoft Azure Active Directory](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Clique em **Inscreva-se**.

1. Na mensagem que aparece, clique em **Sim** para se inscrever o PIM para gerenciar as funções do Azure AD.

    ![Inscrever PIM para mensagem de funções do Microsoft Azure Active Directory](./media/pim-getting-started/sign-up-pim-message.png)

    Quando a inscrição for concluída, as opções do Microsoft Azure Active Directory serão ativadas. Talvez você precise atualizar o portal.

    Para obter informações sobre como descobrir e selecionar os recursos do Azure para proteger com o PIM, consulte [Recursos do Azure para gerenciar no PIM](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Navegue até as tarefas

Depois que o PIM é configurado, você pode executar suas tarefas de gerenciamento de identidade.

![Janela de navegação em mostrando PIM tarefas e gerenciar opções](./media/pim-getting-started/pim-quickstart-tasks.png)

| Tarefa + Gerenciar | DESCRIÇÃO |
| --- | --- |
| **Minhas Funções**  | Exibe uma lista de funções qualificadas e ativas atribuídas a você. É aqui que você pode ativar as funções qualificadas atribuídas. |
| **Minhas solicitações** | Exibe as solicitações pendentes para ativar atribuições de função qualificadas. |
| **Aprovar solicitações** | Exibe uma lista de solicitações de usuários para ativar funções qualificadas em seu diretório, que você pode aprovar. |
| **Análise de acesso** | Lista as revisões de acesso ativas atribuídas a você para completar, esteja você revisando o acesso para si mesmo ou para outra pessoa. |
| **Funções do Microsoft Azure Active Directory** | Exibe um painel de controle e configurações para os administradores de função com privilégios gerenciam atribuições de função do AD do Azure. Esse painel é desabilitado para todos que não forem administradores de função com privilégios. Esses usuários têm acesso a um painel especial denominado Minha exibição. O painel Minha exibição exibe somente informações sobre o usuário que acessa o painel, não o locatário inteiro. |
| **Recursos do Azure** | Exibe um painel e configurações para que administradores com função com privilégios gerenciem atribuições de função de recurso do Azure. Esse painel é desabilitado para todos que não forem administradores de função com privilégios. Esses usuários têm acesso a um painel especial denominado Minha exibição. O painel Minha exibição exibe somente informações sobre o usuário que acessa o painel, não o locatário inteiro. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Adicionar um bloco PIM ao painel

Para que seja mais fácil abrir o PIM, adicione um bloco do PIM ao painel do portal do Azure.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Clique em **Todos os serviços** e encontre o serviço **Azure AD Privileged Identity Management**.

    ![Azure AD Privileged Identity Management em Todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Clique para abrir o Início Rápido do PIM.

1. Marque **Fixar folha no painel** para fixar a folha do Início Rápido do PIM ao painel.

    ![Ícone de pino para fixar a folha PIM ao painel](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    No painel do Azure, você verá um bloco como este:

    ![Início rápido do PIM bloco no dashboard](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções do Azure AD no PIM](pim-how-to-add-role-to-user.md)
- [Descobrir os recursos do Azure para gerenciar no PIM](pim-resource-roles-discover-resources.md)
