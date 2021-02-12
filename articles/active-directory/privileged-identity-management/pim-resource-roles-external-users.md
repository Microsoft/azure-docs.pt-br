---
title: Atribuir funções de recurso do Azure a convidados no PIM – Azure AD | Microsoft Docs
description: Saiba como convidar usuários convidados externos e atribuir funções de recursos do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4e2e18f3bb9d1c972d805a60493897d605921e4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010632"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Convide usuários convidados e atribua funções de recursos do Azure no Privileged Identity Management

Os usuários convidados do Azure Active Directory (Azure AD) fazem parte dos recursos de colaboração B2B (entre empresas) no Azure AD para que você possa gerenciar usuários e fornecedores externos convidados como convidados no Azure AD. Ao combinar a colaboração B2B com o Azure AD Privileged Identity Management (PIM), você pode estender seus requisitos de conformidade e governança para convidados. Por exemplo, você pode usar esses recursos de Privileged Identity Management para tarefas de identidade do Azure com convidados:

- Atribuir acesso a recursos específicos do Azure
- Habilitar acesso Just-In-Time
- Especifique a data de duração e de término da atribuição
- Exigir autenticação multifator na atribuição ou ativação ativa
- Realizar as revisões de acesso
- Usar alertas e logs de auditoria

Este artigo descreve como convidar um convidado para sua organização e gerenciar o acesso aos recursos do Azure usando Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Quando você convidar convidados?

Aqui estão alguns exemplos de quando você pode convidar convidados para sua organização:

- Permitir que um fornecedor externo autônomo tenha apenas uma conta de email para acessar os recursos do Azure para um projeto.
- Permitir que um parceiro externo em uma grande organização que usa Serviços de Federação do Active Directory (AD FS) local para acessar o aplicativo de despesas.
- Permitir que os engenheiros de suporte que não são da sua organização (como o suporte da Microsoft) acessem temporariamente o recurso do Azure para solucionar problemas.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Como funciona a colaboração usando os convidados B2B?

Ao usar a colaboração B2B, você pode convidar um usuário externo para sua organização como um convidado. O convidado pode ser gerenciado como um usuário em sua organização, mas um convidado precisa ser autenticado em sua organização inicial e não em sua organização do Azure AD. Isso significa que, se o convidado não tiver mais acesso à sua organização inicial, eles também perderão o acesso à sua organização. Por exemplo, se o convidado deixar sua organização, ele perderá automaticamente o acesso a todos os recursos que você compartilhou com eles no Azure AD sem precisar fazer nada. Para obter mais informações sobre a colaboração B2B, consulte [o que é acesso de usuário convidado em Azure Active Directory B2B?](../external-identities/what-is-b2b.md).

![Diagrama mostrando como um usuário convidado é autenticado em seu diretório base](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Verificar as configurações de colaboração do convidado

Para garantir que você possa convidar convidados para sua organização, verifique suas configurações de colaboração de convidado.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Selecione **Azure Active Directory**  >  **configurações do usuário**.

1. Selecione **gerenciar configurações de colaboração externas**.

    ![Página Configurações de colaboração externa mostrando permissão, convite e configurações de restrição de colaboração](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Verifique se os **Administradores e usuários na função de convite do emissor de convidado podem convidar** o comutador está definido como **Sim**.

## <a name="invite-a-guest-and-assign-a-role"></a>Convidar um convidado e atribuir uma função

Usando Privileged Identity Management, você pode convidar um convidado e torná-lo qualificado para uma função de recurso do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função Administrador de [função privilegiada](../roles/permissions-reference.md#privileged-role-administrator) ou [administrador de usuário](../roles/permissions-reference.md#user-administrator) .

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **recursos do Azure**.

1. Use o **Filtro de recurso** para filtrar a lista de recursos gerenciados.

1. Selecione o recurso que você deseja gerenciar, como um recurso, grupo de recursos, assinatura ou grupo de gerenciamento.

    Você deve definir o escopo para apenas o que o convidado precisa.

1. Em gerenciar, selecione **funções** para ver a lista de funções para recursos do Azure.

    ![Lista de funções de recursos do Azure mostrando o número de usuários que estão ativos e qualificados](./media/pim-resource-roles-external-users/resources-roles.png)

1. Selecione a função mínima que será necessária para o usuário.

    ![Página de função selecionada listando os membros atuais dessa função](./media/pim-resource-roles-external-users/selected-role.png)

1. Na página função, selecione **Adicionar membro** para abrir o painel nova atribuição.

1. Clique em **Selecionar um membro ou grupo**.

    ![Nova atribuição – selecione um painel membro ou grupo listando usuários e grupos junto com uma opção de convite](./media/pim-resource-roles-external-users/select-member-group.png)

1. Para convidar um convidado, clique em **convidar**.

    ![Convide uma página de convidado com caixas para inserir um endereço de email e especificar uma mensagem pessoal](./media/pim-resource-roles-external-users/invite-guest.png)

1. Depois de selecionar um convidado, clique em **convidar**.

    O convidado deve ser adicionado como um membro selecionado.

1. No painel **selecionar um membro ou grupo** , clique em **selecionar**.

1. No painel **configurações de associação** , selecione o tipo de atribuição e a duração.

    ![Nova atribuição – página Configurações de associação com opções para especificar tipo de atribuição, data de início e data de término](./media/pim-resource-roles-external-users/membership-settings.png)

1. Para concluir a atribuição, selecione **concluído** e, em seguida, **Adicionar**.

    A atribuição de função de convidado aparecerá na sua lista de funções.

    ![Página de função que lista o convidado como qualificado](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Ativar a função como um convidado

Se você for um usuário externo, deverá aceitar o convite para ser convidado na organização do Azure AD e possivelmente ativar sua atribuição de função.

1. Abra o email com seu convite. O email se parecerá com o seguinte.

    ![Convite por email com nome de diretório, mensagem pessoal e um link de introdução](./media/pim-resource-roles-external-users/email-invite.png)

1. Selecione o **link de introdução no** email.

1. Depois de revisar as permissões, clique em **Aceitar**.

    ![Página examinar permissões em um navegador com uma lista de permissões que a organização gostaria de examinar](./media/pim-resource-roles-external-users/invite-accept.png)

1. Você pode ser solicitado a aceitar os termos de uso e especificar se deseja permanecer conectado. No portal do Azure, se você estiver *qualificado* para uma função, ainda não terá acesso aos recursos.

1. Para ativar a atribuição de função, abra o email com o link ativar função. O email se parecerá com o seguinte.

    ![Email indicando que você está qualificado para uma função com um link de função de ativação](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Selecione **Ativar função** para abrir suas funções qualificadas no Privileged Identity Management.

    ![Página minhas funções no Privileged Identity Management listando suas funções qualificadas](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Em ação, selecione o link **Ativar** .

    Dependendo das configurações de função, você precisará especificar algumas informações para ativar a função.

1. Depois de especificar as configurações para a função, clique em **Ativar** para ativar a função.

    ![Ativar o escopo e as opções de listagem de páginas para especificar a hora de início, a duração e o motivo](./media/pim-resource-roles-external-users/activate-role.png)

    A menos que o administrador seja necessário para aprovar sua solicitação, você deve ter acesso aos recursos especificados.

## <a name="view-activity-for-a-guest"></a>Exibir atividade para um convidado

Você pode exibir os logs de auditoria para controlar o que os convidados estão fazendo.

1. Como administrador, abra Privileged Identity Management e selecione o recurso que foi compartilhado.

1. Selecione **auditoria de recurso** para exibir a atividade para esse recurso. O exemplo a seguir mostra um exemplo da atividade para um grupo de recursos.

    ![Recursos do Azure – página de auditoria de recursos listando a hora, o solicitante e a ação](./media/pim-resource-roles-external-users/audit-resource.png)

1. Para exibir a atividade para o convidado, selecione **Azure Active Directory**  >  nome de convidado **dos usuários**  >  *guest name*.

1. Selecione **logs de auditoria** para ver os logs de auditoria da organização. Se necessário, você pode especificar os filtros.

    ![Logs de auditoria de diretório listando data, destino, iniciado por e atividade](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções de administrador do Azure AD no Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [O que é o acesso de usuário convidado na colaboração B2B do Azure AD?](../external-identities/what-is-b2b.md)