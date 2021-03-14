---
title: Atualizar suas Informações de grupo no portal Meus Aplicativos – Azure AD
description: Saiba como exibir e atualizar as informações relacionadas ao grupo, incluindo a exibição dos grupos de sua propriedade, criação de grupos, exibição dos grupos dos quais você já é membro e o ingresso aos grupos dos quais você não faz parte.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: fc8d211160e757c78a8e2adafad703118628e6dc
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095037"
---
# <a name="update-your-groups-info-on-the-my-apps-portal"></a>Atualizar suas Informações de grupo no portal Meus Aplicativos

Você pode usar sua conta corporativa ou de estudante com o portal **meus aplicativos** baseado na Web para exibir e iniciar muitos dos aplicativos baseados em nuvem da sua organização, para atualizar algumas de suas informações de perfil e de conta, para ver suas informações de **grupos** e para realizar **revisões de acesso** para seus aplicativos e grupos. Se você não tiver acesso ao portal **Meus Aplicativos**, será necessário contatar a assistência técnica para obter permissão.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Este conteúdo destina-se aos usuários. Se você for administrador, poderá encontrar mais informações sobre como configurar e gerenciar seus aplicativos baseados em nuvem na [Documentação de Gerenciamento do Aplicativo](../manage-apps/index.yml).
>
> Se você vir um erro ao entrar com um conta Microsoft pessoal, ainda poderá entrar usando o nome de domínio da sua organização (como contoso.com) ou a **ID de locatário** da sua organização em uma das seguintes URLs:
>
>   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
>   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*

## <a name="view-your-groups-information"></a>Exibir suas Informações de grupos

Se seu administrador conceder uma permissão para exibir o bloco **Grupos**, você pode:

- **Como membro do grupo.** Exibir detalhes ou sair de qualquer grupo.

- **Como proprietário do grupo.** Exibir detalhes, criar um grupo, adicionar ou remover membros ou excluir seu grupo.

### <a name="to-view-your-groups-information"></a>Para exibir as informações de grupos

1. Entre sua conta corporativa ou de estudante.

2. Abra o navegador da Web e acesse https://myapps.microsoft.com ou use o link fornecido pela sua organização. Por exemplo, você pode ser direcionado a uma página personalizada para sua organização, como https://myapps.microsoft.com/contoso.com. Se você estiver entrando com um conta Microsoft pessoal, ainda poderá entrar usando o nome de domínio da sua organização (como contoso.com) ou a **ID de locatário** da sua organização em uma das seguintes URLs:

   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*


    A página **Aplicativos** é exibida, mostrando todos os aplicativos baseados em nuvem pertencentes à sua organização e disponíveis para uso.

    ![Página Aplicativos no portal Meus Aplicativos](media/my-apps-portal-end-user-groups/my-apps-home-large.png)

3. Puxe o menu **meus aplicativos** e selecione **meus grupos** para ver suas informações relacionadas ao grupo.

4. Com base em suas permissões, você pode usar a página **meus grupos** para:

    - **Examinar os grupos de sua propriedade.** Exiba informações sobre os grupos de sua propriedade em sua organização na área **Grupos que possuo**. A seleção de um nome de grupo específico fornece mais detalhes sobre o grupo, incluindo o tipo de grupo, o número de membros, a política de junção e a lista de membros ativos.

    - **Criar um grupo.** Crie um grupo com você como proprietário da área **Grupos que possuo**. Para etapas específicas, confira a seção [Criar um grupo](#create-a-new-group) deste artigo.

    - **Editar um grupo existente.** Edite os detalhes de qualquer um dos seus próprios grupos. Para etapas específicas, confira a seção [Editar um grupo existente](#edit-an-existing-group) deste artigo.

    - **Adicionar ou remover membros.** Adicione ou remova membros de grupos de sua propriedade. Para ver etapas específicas, confira a seção [Adicionar ou remover um membro](#add-or-remove-a-member) deste artigo.

    - **Renovar um grupo do Office 365.** Se sua organização permitir, você poderá renovar seus grupos do Office 365. Para ver etapas específicas, confira a seção [Renovar um grupo do Office 365](#renew-an-office-365-group) deste artigo. 

    - **Excluir um grupo.** Exclua todos os grupos de sua propriedade. Para ver etapas específicas, confira a seção [Excluir um grupo](#delete-a-group) deste artigo.

    - **Examinar os grupos dos quais você faz parte.** Exiba os nomes dos grupos dos quais você é membro na área **Grupos em que estou** área. A seleção de um nome de grupo específico fornece mais detalhes sobre o grupo, incluindo o tipo de grupo, o número de membros, a política de junção e a lista de membros ativos.

    - **Ingressar em um grupo.** Entre em um grupo existente, do qual você ainda não é membro, na área **Grupos em que estou**. Para ver etapas específicas, confira [Ingressar em um grupo existente](#join-an-existing-group).

## <a name="create-a-new-group"></a>Criar um novo grupo

1. Na página **Grupos**, selecione **Criar um grupo** na área **Grupos de minha propriedade**.

    A caixa **Criar grupo** é exibida.

    ![Caixa Criar grupo](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Insira as informações necessárias:

    - **Tipo de grupo:**

        - **Segurança.** Usado para gerenciar o acesso de membros e computadores a recursos compartilhados para um grupo de usuários. Por exemplo, você pode criar um grupo de segurança para uma política de segurança específica. Com isso, você pode dar um conjunto de permissões a todos os membros ao mesmo tempo, em vez de precisar adicionar permissões a cada membro individualmente.

        - **Office 365.** Fornece oportunidades de colaboração, oferecendo acesso de membros a uma caixa de correio compartilhada, calendário, arquivos, site do SharePoint e muito mais. Essa opção também permite dar acesso ao grupo a pessoas fora da sua organização.

    - **Nome do grupo.** Adicione um nome para o grupo, algo fácil de lembrar e que faça sentido.

    - **Descrição do grupo (opcional).** Adicione uma descrição opcional para seu grupo.

    - **Política de grupo.** Escolha permitir que todos entrem no grupo ou permitir que somente o proprietário do grupo adicione membros.

3. Selecione **Criar**.

    O grupo é criado com você como proprietário e ele é exibido na lista **Grupos de minha propriedade**. Como você é o proprietário, esse grupo também é exibido na lista **Grupos em que estou**.

## <a name="edit-an-existing-group"></a>Editar um grupo existente

Após criar um grupo, você pode editar os detalhes dele, incluindo atualizar as informações existentes.

1. Selecione o grupo que você deseja editar na página **Grupos** e selecione **Editar detalhes** na página *&lt;nome_do_grupo&gt;* .

    A caixa **Editar detalhes** é exibida e você pode atualizar as informações adicionadas quando criou inicialmente o grupo.

2. Faça todas as alterações e, em seguida, selecione **Atualizar**.

## <a name="add-or-remove-a-member"></a>Adicionar ou remover um membro

Você pode adicionar ou remover membros de grupos de sua propriedade.

1. Selecione o grupo ao qual você deseja adicionar membros e, em seguida, selecione **+** na página *&lt;nome_do_grupo&gt;* .

    ![Adicionar um membro do grupo, com o sinal de + realçado](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Pesquise o membro que você deseja adicionar, na caixa **Adicionar membros**, e selecione **Adicionar**.

    ![Caixa Adicionar membros, com o novo membro a ser adicionado](media/my-apps-portal/my-apps-portal-add-member-page.png)

    Um convite é enviado para o novo membro, para começar a acessar os aplicativos da organização.

3. Se você adicionou um membro por engano ou se um membro saiu da sua organização, você pode removê-lo selecionando **Remover membro** ao lado do nome do membro na página *&lt;nome_do_grupo&gt;* .

    ![Remover um membro, com o link de remoção realçado](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Renovar um grupo do Office 365

Se sua organização permitir, você poderá renovar um grupo do Office 365, estendendo sua data de validade.

1. Selecione o grupo do Office 365 que você deseja renovar e, em seguida, selecione **Renovar grupo**.

    ![Renovar um grupo do Office 365, estendendo a data de validade](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. Clique em **OK** para fechar a mensagem de confirmação.

    Depois de atualizar a página, você verá suas datas **Última renovação** e **Expiração de grupo** atualizadas.

## <a name="delete-a-group"></a>Excluir um grupo

Você pode excluir qualquer um dos seus grupos a qualquer momento. No entanto, se você excluir um grupo por engano, precisará criá-lo e adicionar membros novamente.

1. Selecione o grupo que você deseja excluir permanentemente e, em seguida, selecione **Excluir grupo** na página *&lt;nome_do_grupo&gt;* .

    ![Página <nome_do_grupo> com o link Excluir grupo realçado](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Selecione **Sim** na mensagem de confirmação.

    O grupo é excluído permanentemente.

## <a name="join-an-existing-group"></a>Ingressar em um grupo existente

Você pode ingressar em um grupo existente por meio da página **Grupos** ou sair dele.

1. Na página **Grupos**, selecione **Ingressar em um grupo** na área **Grupos em que estou**.

    A página **Ingressar em grupos** é exibida.

    ![Página Ingressar em grupos, com o botão Ingressar em grupo realçado](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Na página **Ingressar em grupos**, selecione o nome do grupo em que você deseja ingressar, exiba os detalhes do grupo associado e, se ele estiver disponível, selecione **Ingressar no grupo**.

    Se o grupo exigir que o proprietário aprove a associação, você deverá inserir uma justificativa comercial do porquê você precisa ingressar no grupo e, em seguida, selecionar **Solicitar**. Se o grupo não exigir aprovação, você será adicionado imediatamente como membro e ele será exibido na lista **Grupos em que estou**.

3. Se você ingressou em um grupo por engano ou se não precisar mais fazer parte dele, poderá selecionar o nome dele na página **Ingressar em grupos** e selecionar **Sair do grupo**.

    ![Página Ingressar em grupos, com o botão Sair do grupo realçado](media/my-apps-portal/my-apps-portal-leave-group-link.png)

## <a name="next-steps"></a>Próximas etapas

- [Acesse e use aplicativos no portal Meus Aplicativos](my-apps-portal-end-user-access.md).

- [Alterar as informações do seu perfil](./my-account-portal-settings.md).

- [Executar suas próprias revisões de acesso](my-apps-portal-end-user-access-reviews.md).