---
title: Convidar pessoas e atribuir funções de recurso do Azure no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como convidar usuários externos e atribuir funções de recurso do Azure no Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 07476c9f5db64a5d107a493022fa3548fe0dae4c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476350"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>Convidar usuários e atribuir funções de recurso do Azure no PIM

Azure Active Directory (Azure AD) business-to-business (B2B) é um conjunto de recursos dentro do Azure AD que permite às organizações colaborar com fornecedores usando qualquer conta e usuários convidados externo (convidados). Quando você combina B2B com Azure AD Privileged Identity Management (PIM), você pode continuar a aplicar seus requisitos de governança e conformidade aos convidados. Por exemplo, você pode usar esses recursos PIM para tarefas de identidade do Azure com convidados:

- Atribuir acesso a recursos específicos do Azure
- Habilitar acesso just-in-time
- Especifique a data de duração e de término da atribuição
- Exigir MFA na atribuição ativa ou a ativação
- Realizar as revisões de acesso
- Usar alertas e logs de auditoria

Este artigo descreve como convidar uma pessoa à sua organização e gerenciar o acesso a recursos do Azure usando o Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Ao convidar convidados?

Aqui estão alguns cenários de exemplo, quando você pode convidar pessoas para sua organização:

- Permitir que um fornecedor externo autônomo tenha apenas uma conta de email para acessar os recursos do Azure para um projeto.
- Permitir que um parceiro externo em uma grande organização que usa Serviços de Federação do Active Directory (AD FS) local para acessar o aplicativo de despesas.
- Permitir que os engenheiros de suporte que não são da sua organização (como o suporte da Microsoft) acessem temporariamente o recurso do Azure para solucionar problemas.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Como funciona a colaboração usar B2B convidados trabalho?

Quando você usa a colaboração B2B, você pode convidar um usuário externo à sua organização como convidado. O convidado parece estar em sua organização, mas o convidado não tem nenhuma credencial associada a ele. Sempre que um convidado precisa ser autenticada, eles devem ser autenticados na organização inicial e não em sua organização. Isso significa que se o convidado não tiver acesso à sua organização inicial, eles também percam o acesso à sua organização. Por exemplo, se o convidado deixa sua organização, eles automaticamente perdem o acesso a recursos compartilhados com eles no Azure AD sem ter que fazer nada. Para obter mais informações sobre B2B, consulte [O que é acesso de usuário convidado no Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![Diagrama que mostra como um usuário convidado é exibido em seu diretório, mas é autenticado em seu diretório base](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Verifique as configurações de colaboração de convidado

Para certificar-se de que você pode convidar pessoas em sua organização, você deve verificar as configurações de colaboração de convidado.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Selecione **Azure Active Directory** > **Configurações de usuário**.

1. Clique em **Gerenciar configurações de colaboração externa**.

    ![Página de configurações de colaboração externa, mostrando as configurações de restrição de permissão, convite e colaboração](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Certifique-se de que os **Administradores e usuários na função de emissor do convite ao convidado** a alternância é definida como **Sim**.

## <a name="invite-a-guest-and-assign-a-role"></a>Convidar uma pessoa e atribuir uma função

Usar o PIM, você pode convidar uma pessoa e torná-los elegível para uma função de recurso do Azure como um usuário do membro.

1. Entrar no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro do [administrador com privilégios de função](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) ou [usuário administrador](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) função.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Recursos do Azure**.

1. Use o **Filtro de recurso** para filtrar a lista de recursos gerenciados.

1. Clique no recurso que você quer gerenciar, como recurso, grupo de recursos, assinatura ou grupo de gerenciamento.

    Você deve definir o escopo para precisa apenas de que o convidado.

1. Em Gerenciar, clique em **Funções** para ver a lista de funções dos recursos do Azure.

    ![Listam de funções de recursos do Azure mostrando o número de usuários que estão ativas e qualificadas](./media/pim-resource-roles-external-users/resources-roles.png)

1. Clique na função mínima que o usuário precisará.

    ![Selecionado listando os membros atuais dessa função de página de função](./media/pim-resource-roles-external-users/selected-role.png)

1. Clique em **Adicionar membro** para abrir o novo painel de atribuição.

1. Clique em **Selecionar um membro ou grupo**.

    ![Nova atribuição - selecione um membro ou grupo listando usuários e grupos, juntamente com uma opção de convite de painel](./media/pim-resource-roles-external-users/select-member-group.png)

1. Para convidar um convidado, clique em **convidar**.

    ![Convidar uma página de convidado com as caixas para inserir um endereço de email e especificar uma mensagem pessoal](./media/pim-resource-roles-external-users/invite-guest.png)

1. Depois que você tiver selecionado um convidado, clique em **convidar**.

    O convidado deve ser adicionado como um membro selecionado.

1. No **selecione um membro ou grupo** painel, clique em **selecione**.

1. No **configurações de associação** painel, selecione o tipo de atribuição e a duração.

    ![Nova atribuição - página de configurações de associação com opções para especificar o tipo de atribuição, data de início e data de término](./media/pim-resource-roles-external-users/membership-settings.png)

1. Para concluir a atribuição, clique em **Concluído** e, em seguida **Adicionar**.

    A atribuição de função convidado aparecerá na sua lista de função.

    ![Página de função listando o convidado como qualificada](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Ativar a função como um convidado

Como um usuário externo, primeiro você precisa aceitar o convite para sua organização do AD do Azure e, possivelmente, ativar sua função.

1. Abra o email com o seu convite. O email se parecerá com o seguinte.

    ![Convite por email com o nome do diretório, a mensagem pessoal e um link de Introdução](./media/pim-resource-roles-external-users/email-invite.png)

1. Clique no link **Começar** link no email.

1. Depois de revisar as permissões, clique em **Aceitar**.

    ![Examine a página de permissões em um navegador com uma lista de permissões que a organização que você examine](./media/pim-resource-roles-external-users/invite-accept.png)

1. Você pode ser solicitado a aceitar os termos de uso e especificar se deseja permanecer conectado.

    O portal do Azure abre. Se você estiver apenas qualificado para uma função, não terá acesso aos recursos.

1. Para ativar sua função, abra o email com o link ativar função. O email se parecerá com o seguinte.

    ![Mensagem de email de PIM indicando qualificados para uma função com um link de função ativar](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Clique em **Ativar a função** para abrir suas funções qualificadas no PIM.

    ![Minha página de funções no PIM listando suas funções qualificadas](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Em Ação, clique no link **Ativar**.

    Dependendo das configurações de função, você precisará especificar algumas informações para ativar a função.

1. Depois de especificar as configurações para a função, clique em **Ativar** para ativar a função.

    ![Ativar escopo de listagem de página e as opções para especificar a hora de início, duração e motivo](./media/pim-resource-roles-external-users/activate-role.png)

    A menos que o administrador seja necessário para aprovar sua solicitação, você deve ter acesso aos recursos especificados.

## <a name="view-activity-for-a-guest"></a>Exibir atividade de um convidado

Assim como um usuário membro, você pode exibir os logs de auditoria para controlar o que os convidados estão fazendo.

1. Como administrador, abra o PIM e selecione o recurso que foi compartilhado.

1. Clique em **Auditoria de recurso** para exibir a atividade para esse recurso. O exemplo a seguir mostra um exemplo da atividade para um grupo de recursos.

    ![Página de listagem o tempo, o solicitante e a ação de auditoria de recursos do Azure - recursos](./media/pim-resource-roles-external-users/audit-resource.png)

1. Para exibir a atividade para o convidado, clique em **Azure Active Directory** > **usuários** > nome do convidado.

1. Clique em **logs de auditoria** para ver os logs de auditoria para a organização. Se necessário, você pode especificar os filtros.

    ![Data de listagem, destino, iniciada por e a atividade das trilhas de auditoria de diretório](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções de administrador do AD do Azure no PIM](pim-how-to-add-role-to-user.md)
- [O que é o acesso de usuários convidados no B2B do Azure Active Directory?](../b2b/what-is-b2b.md)
