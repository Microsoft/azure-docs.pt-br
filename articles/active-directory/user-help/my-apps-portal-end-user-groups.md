---
title: Atualize suas informações de grupos no portal meus aplicativos-Azure Active Directory | Microsoft Docs
description: Saiba como exibir e atualizar suas informações relacionadas a grupos, incluindo a exibição dos grupos de sua propriedade, a criação de novos grupos, a exibição dos grupos aos quais você já é membro e a União de todos os grupos dos quais você já não faz parte.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a11b43d040dec838de350c23614ae42b6756ec6e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383173"
---
# <a name="update-your-groups-info-from-the-my-apps-portal"></a>Atualizar suas informações de grupos no portal meus aplicativos

É possível usar sua conta corporativa ou de estudante com o portal **Meus Aplicativos** baseado na Web para exibir e iniciar muitos aplicativos baseados em nuvem da sua organização, para atualizar algumas informações do perfil e da conta, para ver suas informações de **Grupos** e para realizar **revisões de acesso** para seus aplicativos e grupos. Se você não tiver acesso ao portal **Meus Aplicativos**, será necessário contatar a assistência técnica para obter permissão.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Este conteúdo destina-se aos usuários. Se você for administrador, poderá encontrar mais informações sobre como configurar e gerenciar seus aplicativos baseados em nuvem na [Documentação de Gerenciamento do Aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="view-your-groups-information"></a>Exibir as informações de seus grupos

Se o administrador tiver concedido permissão para exibir o bloco **grupos** , você poderá:

- **Como um membro do grupo.** Exiba detalhes ou deixe qualquer grupo.

- **Como um proprietário de grupo.** Exiba detalhes, crie um novo grupo, adicione ou remova Membros ou exclua seu grupo.

### <a name="to-view-your-groups-information"></a>Para exibir as informações de seus grupos

1. Entre sua conta corporativa ou de estudante.

2. Abra o navegador da Web e vá https://myapps.microsoft.com para ou use o link fornecido pela sua organização. Por exemplo, você pode ser direcionado para uma página personalizada para sua organização, como https://myapps.microsoft.com/contoso.com.

    A página **aplicativos** é exibida, mostrando todos os aplicativos baseados em nuvem pertencentes à sua organização e disponíveis para uso.

    ![Página de aplicativos no portal meus aplicativos](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. Selecione o bloco **grupos** para ver as informações relacionadas ao grupo.

    ![Página grupos com grupos proprietários e membros](media/my-apps-portal/my-apps-portal-groups-page.png)

4. Com base em suas permissões, você pode usar a página **grupos** para:

    - **Examine os grupos que você possui.** Exiba informações sobre os grupos que você possui em sua organização por meio dos **grupos que** possuo na área. A seleção de um nome de grupo específico fornece mais detalhes sobre o grupo, incluindo o tipo de grupo, o número de membros, a política de junção e a lista de membros ativos.

    - **Crie um novo grupo.** Crie um novo grupo com você como o proprietário da área **grupos que possuo** . Para obter etapas específicas, consulte a seção [criar um novo grupo](#create-a-new-group) deste artigo.

    - **Editar um grupo existente.** Edite os detalhes de qualquer um dos seus próprios grupos. Para obter etapas específicas, consulte a seção [Editar um grupo existente](#edit-an-existing-group) deste artigo.

    - **Adicionar ou remover membros.** Adicionar ou remover membros de grupos que você possui. Para obter etapas específicas, consulte a seção [Adicionar ou remover um membro](#add-or-remove-a-member) deste artigo.

    - **Renove um grupo do Office 365.** Se sua organização permitir, você poderá renovar seus grupos do Office 365. Para obter etapas específicas, consulte a seção renovar [um grupo do Office 365](#renew-an-office-365-group) deste artigo. 

    - **Excluir um grupo.** Exclua todos os grupos que você possui. Para obter etapas específicas, consulte a seção [excluir um grupo](#delete-a-group) deste artigo.

    - **Examine os grupos dos quais você faz parte.** Exiba os nomes de todos os grupos aos quais você é membro dos **grupos que estou na** área. A seleção de um nome de grupo específico fornece mais detalhes sobre o grupo, incluindo o tipo de grupo, o número de membros, a política de junção e a lista de membros ativos.

    - **Ingresse em um grupo.** Junte-se a um grupo existente, para o qual você ainda não é membro, dos **grupos que estou na** área. Para obter etapas específicas, consulte [ingressar em um grupo existente](#join-an-existing-group).

## <a name="create-a-new-group"></a>Criar um novo grupo

1. Na página **grupos** , selecione **criar um grupo** na área **grupos de sua propriedade** .

    A caixa **Criar grupo** é exibida.

    ![Criar caixa de grupo](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Insira as informações necessárias:

    - **Tipo de grupo:**

        - **Segurança.** Usado para gerenciar o acesso de membros e computadores a recursos compartilhados para um grupo de usuários. Por exemplo, você pode criar um grupo de segurança para uma política de segurança específica. Com isso, você pode dar um conjunto de permissões a todos os membros ao mesmo tempo, em vez de precisar adicionar permissões a cada membro individualmente.

        - **Office 365.** Fornece oportunidades de colaboração, oferecendo acesso de membros a uma caixa de correio compartilhada, calendário, arquivos, site do SharePoint e muito mais. Essa opção também permite dar acesso ao grupo a pessoas fora da sua organização.

    - **Nome do grupo.** Adicione um nome para o grupo, algo fácil de lembrar e que faça sentido.

    - **Descrição do grupo (opcional).** Adicione uma descrição opcional para seu grupo.

    - **Política de grupo.** Escolha uma opção para permitir que todos ingressem no grupo ou para permitir que somente o proprietário do grupo adicione membros.

3. Selecione **Criar**.

    O novo grupo é criado com você como o proprietário e aparece na lista **grupos que** possuo. Como você é o proprietário, esse grupo também aparece na lista **grupos que estou em** .

## <a name="edit-an-existing-group"></a>Editar um grupo existente

Depois de criar um grupo, você pode editar seus detalhes, incluindo a atualização de qualquer uma das informações existentes.

### <a name="to-edit-your-details"></a>Para editar seus detalhes

1. Selecione o grupo que você deseja editar na página **grupos** e, em seguida, selecione **Editar detalhes** na  *&lt;página&gt; group_name* .

    A caixa **Editar detalhes** é exibida e você pode atualizar as informações que adicionou quando criou inicialmente o grupo.

2. Faça todas as alterações e, em seguida, selecione **Atualizar**.

## <a name="add-or-remove-a-member"></a>Adicionar ou remover um membro

Você pode adicionar ou remover membros de qualquer um dos seus próprios grupos.

### <a name="to-add-or-remove-a-member"></a>Para adicionar ou remover um membro

1. Selecione o grupo ao qual você deseja adicionar membros e, em seguida **+** , selecione  *&lt;na&gt; página group_name* .

    ![Adicionar um membro do grupo, com + sinal realçado](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Procure o membro que você deseja adicionar, na caixa **adicionar membros** e, em seguida, selecione **Adicionar**.

    ![Caixa adicionar membros, com o novo membro a adicionar](media/my-apps-portal/my-apps-portal-add-member-page.png)

    Um convite é enviado para o novo membro, para começar a acessar os aplicativos da organização.

3. Se você adicionou um membro por engano ou se um membro saiu da sua organização, você pode remover o membro selecionando **Remover membro** ao lado do nome do membro na *&lt;página group_name&gt;* .

    ![Remover um membro, com o link de remoção realçado](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Renovar um grupo do Office 365

Se sua organização permitir, você poderá renovar um grupo do Office 365, estendendo sua data de validade.

### <a name="to-renew-a-group"></a>Para renovar um grupo

1. Selecione o grupo do Office 365 que você deseja renovar e, em seguida, selecione renovar **grupo**.

    ![Renovar um grupo do Office 365, estendendo a data de validade](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. Clique em **OK** para fechar a mensagem de confirmação.

    Depois de atualizar a página, você verá suas datas de **última renovação** e de expiração de **grupo** atualizadas.

## <a name="delete-a-group"></a>Excluir um grupo

Você pode excluir qualquer um de seus próprios grupos a qualquer momento. No entanto, se você excluir um grupo por engano, precisará criá-lo e adicionar membros novamente.

### <a name="to-delete-the-group"></a>Para excluir o grupo

1. Selecione o grupo que você deseja excluir permanentemente e, em seguida, selecione **excluir grupo** na *&lt;página group_name&gt;* .

    ![< página de > do group_name com o link excluir grupo realçado](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Selecione **Sim** na mensagem de confirmação.

    O grupo é excluído permanentemente.

## <a name="join-an-existing-group"></a>Ingressar em um grupo existente

Você pode ingressar um grupo já existente na página **grupos** .

### <a name="to-join-or-leave-a-group"></a>Para ingressar ou sair de um grupo

1. Na página **grupos** , selecione **grupo de junção** nos **grupos que estou na** área.

    A página **grupos de junção** é exibida.

    ![Página de grupos de junção, com o botão unir grupo realçado](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Na página **grupos de junção** , selecione o nome do grupo que você deseja unir, exiba os detalhes do grupo associado e, se o grupo estiver disponível, selecione **ingressar**no grupo.

    Se o grupo exigir que o proprietário do grupo aprove a associação, você será solicitado a inserir uma justificativa comercial para o porquê de precisar ingressar no grupo e, em seguida, selecionar **solicitação**. Se o grupo não exigir aprovação, você será adicionado imediatamente como um membro e o grupo aparecerá em seus **grupos em que estou na** lista.

3. Se você tiver ingressado em um grupo por engano ou se não precisar mais fazer parte dele, poderá selecionar o nome do grupo na página **grupos de junção** e, em seguida, selecionar **sair do grupo**.

    ![Página grupos de junção, com o botão sair do grupo realçado](media/my-apps-portal/my-apps-portal-leave-group-link.png)    

## <a name="next-steps"></a>Próximas etapas

- [Acesse e use aplicativos no portal meus aplicativos](my-apps-portal-end-user-access.md).

- [Altere as informações do seu perfil](my-apps-portal-end-user-update-profile.md).

- [Execute suas próprias revisões de acesso](my-apps-portal-end-user-access-reviews.md).
