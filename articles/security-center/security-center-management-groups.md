---
title: Obtenha visibilidade de todo o locatário para o Centro de Segurança do Azure | Microsoft Docs
description: Este artigo explica como gerenciar sua postura de segurança em escala aplicando políticas a todas as assinaturas vinculadas ao seu locatário de Azure Active Directory.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 13cbc2e9451221fef951eb6fac4c6b2772275122
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556433"
---
# <a name="organize-management-groups-subscriptions-and-tenant-wide-visibility"></a>Organizar grupos de gerenciamento, assinaturas e visibilidade em todo o locatário

Este artigo explica como gerenciar a postura de segurança de sua organização em escala aplicando políticas de segurança a todas as assinaturas do Azure vinculadas ao seu locatário de Azure Active Directory.

Para obter visibilidade da postura de segurança de todas as assinaturas registradas no locatário do Azure AD, é necessário atribuir uma função do Azure com permissões de leitura suficientes no grupo de gerenciamento raiz.


## <a name="organize-your-subscriptions-into-management-groups"></a>Organizar suas assinaturas em grupos de gerenciamento

### <a name="introduction-to-management-groups"></a>Introdução aos grupos de gerenciamento

Os grupos de gerenciamento do Azure fornecem a capacidade de gerenciar com eficiência o acesso, as políticas e os relatórios sobre grupos de assinaturas, bem como gerenciar com eficácia toda a propriedade do Azure executando ações no grupo de gerenciamento raiz. Você organiza assinaturas em grupos de gerenciamento e aplica as políticas de governança aos grupos de gerenciamento. Todas as assinaturas dentro de um grupo de gerenciamento herdam automaticamente as políticas aplicadas ao grupo de gerenciamento. 

Cada locatário do Azure AD recebe um único grupo de gerenciamento de nível superior chamado **grupo de gerenciamento raiz**. Esse grupo de gerenciamento raiz é compilado na hierarquia para que todos os grupos de gerenciamento e assinaturas sejam dobrados nele. Esse grupo permite que políticas globais e atribuições de função do Azure sejam aplicadas no nível do diretório. 

O grupo de gerenciamento raiz é criado automaticamente quando você faz qualquer uma das seguintes ações: 
- Abra **grupos de gerenciamento** no [portal do Azure](https://portal.azure.com).
- Crie um grupo de gerenciamento com uma chamada à API.
- Cria um grupo de gerenciamento com PowerShell. Para obter instruções sobre o PowerShell, consulte [criar grupos de gerenciamento para gerenciamento de recursos e da organização](../governance/management-groups/create-management-group-portal.md).

Os grupos de gerenciamento não são necessários para carregar a central de segurança, mas recomendamos que você crie pelo menos um para que o grupo de gerenciamento raiz seja criado. Depois que o grupo é criado, todas as assinaturas sob o seu locatário Azure AD serão conectadas a ele. 


Para obter mais informações sobre grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md).

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Exibir e criar grupos de gerenciamento no portal do Azure

1. No [portal do Azure](https://portal.azure.com), use a caixa de pesquisa na barra superior para localizar e abrir **grupos de gerenciamento**.

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="Acessando seus grupos de gerenciamento":::

    A lista de seus grupos de gerenciamento é exibida.

1. Para criar um grupo de gerenciamento, selecione **Adicionar grupo de gerenciamento**, insira os detalhes relevantes e selecione **salvar**.

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Adicionando um grupo de gerenciamento ao Azure":::

    - **ID do Grupo de Gerenciamento** é o identificador exclusivo do diretório usado para enviar comandos nesse grupo de gerenciamento. Esse identificador não é editável após a criação, visto que é usado em todo o sistema do Azure para identificar esse grupo. 
    - O campo de nome de exibição é o nome exibido no portal do Azure. Um nome de exibição separado é um campo opcional ao criar o gerenciamento de grupo e pode ser alterado a qualquer momento.  


### <a name="add-subscriptions-to-a-management-group"></a>Adicionar assinaturas a um grupo de gerenciamento
Você consegue adicionar assinaturas a um grupo de gerenciamento que você criou.

1. Em **grupos de gerenciamento**, selecione o grupo de gerenciamento para sua assinatura.

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="Selecione um grupo de gerenciamento para sua assinatura":::

1. Quando a página do grupo for aberta, selecione **detalhes**

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="Abrindo a página de detalhes de um grupo de gerenciamento":::

1. Na página de detalhes do grupo, selecione **Adicionar assinatura**, em seguida, selecione suas assinaturas e selecione **salvar**. Repita até adicionar todas as assinaturas no escopo.

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="Adicionando uma assinatura a um grupo de gerenciamento":::
   > [!IMPORTANT]
   > Grupos de gerenciamento podem conter ambos assinaturas e grupos de gerenciamento criança. Quando você atribui um usuário a uma função do Azure ao grupo de gerenciamento pai, o acesso é herdado pelas assinaturas do grupo de gerenciamento filho. Conjunto de políticas no grupo de gerenciamento pai são também herdadas pela criança. 


## <a name="grant-tenant-wide-permissions-to-yourself"></a>Conceder permissões em todo o locatário para você mesmo

Um usuário com a função de Azure Active Directory (AD) do **administrador global** pode ter responsabilidades em todo o locatário, mas não tem as permissões do Azure para exibir as informações de toda a organização na central de segurança do Azure. A elevação de permissões é necessária porque as atribuições de função do Azure AD não concedem acesso aos recursos do Azure. 

> [!TIP]
> Saiba mais sobre a elevação de permissões para a função de administrador global em [elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure](../role-based-access-control/elevate-access-global-admin.md).

Para atribuir permissões em nível de locatário:

1. Se sua organização gerencia o acesso a recursos com [Azure ad Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)ou qualquer outra ferramenta PIM, a função de administrador global deve estar ativa para o usuário seguindo o procedimento abaixo.

1. Como um usuário administrador global sem uma atribuição no grupo de gerenciamento raiz do locatário, abra a página **visão geral** da central de segurança e selecione o link **visibilidade em todo o locatário** na faixa. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Habilitar permissões em nível de locatário na central de segurança do Azure":::

1. Selecione a nova função do Azure a ser atribuída. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Formulário para definir as permissões de nível de locatário a serem atribuídas ao usuário":::

    > [!TIP]
    > Geralmente, a função Admin de Segurança é necessária para aplicar políticas no nível raiz, enquanto Leitor de Segurança será suficiente para fornecer visibilidade no nível dos locatários. Para obter mais informações sobre as permissões concedidas por essas funções, consulte a [descrição da função interna Admin de Segurança](../role-based-access-control/built-in-roles.md#security-admin) ou a [descrição da função interna Leitor de Segurança](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Para obter as diferenças entre essas funções específicas à central de segurança, consulte a tabela em [funções e ações permitidas](security-center-permissions.md#roles-and-allowed-actions).

    A exibição de toda a organização é obtida com a concessão de funções no nível do grupo de gerenciamento raiz do locatário.  

1. Faça logoff do portal do Azure e, em seguida, faça logon novamente.

1. Após elevar o acesso, abra ou atualize a Central de Segurança do Azure para verificar se você tem visibilidade de todas as assinaturas do locatário do Azure AD. 


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Solicitar permissões em todo o locatário quando as suas não forem suficientes

Se você fizer logon na central de segurança e vir uma faixa informando que sua exibição é limitada, você pode clicar para enviar uma solicitação ao administrador global da sua organização. Na solicitação, você pode incluir a função que deseja atribuir e o administrador global tomará uma decisão sobre qual função conceder. 

É a decisão do administrador global se você deve aceitar ou rejeitar essas solicitações. 

> [!IMPORTANT]
> Você só pode enviar uma solicitação a cada sete dias.

Para solicitar permissões elevadas de seu administrador global:

1. No portal do Azure, abra a central de segurança do Azure.

1. Se você vir a faixa "você está vendo informações limitadas". Selecione-o.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Faixa informando a um usuário que ele pode solicitar permissões em todo o locatário.":::

1. No formulário de solicitação detalhado, selecione a função desejada e a justificativa de por que você precisa dessas permissões.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Página de detalhes para solicitar permissões em todo o locatário do administrador global do Azure":::

1. Selecione **Solicitar acesso**.

    Um email é enviado para o administrador global. O email contém um link para a central de segurança onde eles podem aprovar ou rejeitar a solicitação.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="Email para o administrador global para obter novas permissões":::

    Depois que o administrador global selecionar **examinar a solicitação** e concluir o processo, a decisão será enviada por email para o usuário solicitante. 

## <a name="assign-azure-roles-to-other-users"></a>Atribuir funções do Azure a outros usuários

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Atribua funções do Azure aos usuários por meio do portal do Azure: 
1. Entre no [portal do Azure](https://portal.azure.com). 
1. Para exibir grupos de gerenciamento, selecione **Todos os serviços** no menu principal do Azure e, em seguida, selecione **Grupos de Gerenciamento**.
1.  Selecione um grupo de gerenciamento e selecione **detalhes**.

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="Captura de tela dos detalhes dos Grupos de Gerenciamento":::

1. Selecione **controle de acesso (iam)** e **atribuições de função**.
1. Selecione **Adicionar atribuição de função**.
1. Selecione a função a ser atribuída e o usuário e, em seguida, selecione **salvar**.  
   
   ![Captura de tela da função Adicionar Leitor de Segurança](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>Atribuir funções do Azure a usuários com o PowerShell: 
1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps).
2. Execute os seguintes comandos: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Quando solicitado, entre com suas credenciais. 

    ![Logue em screenshot promt](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Conceda permissões de função de leitor executando o seguinte comando:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Para remover a função, use o seguinte comando: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>Remover acesso elevado 

Depois que as funções do Azure tiverem sido atribuídas aos usuários, o administrador de locatários deverá se remover da função de administrador de acesso do usuário.

1. Entre no [portal do Azure](https://portal.azure.com) ou no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com).

2. Na lista de navegação, selecione **Azure Active Directory** e, em seguida, selecione **Propriedades**.

3. Em **Gerenciamento de acesso para recursos do Azure**, defina a opção como **não**.

4. Para salvar sua configuração, selecione **salvar**.



## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como ganhar visibilidade a nível locatário para Centro de Segurança do Azure. Para obter informações relacionadas. consulte:

- [Permissões na Central de Segurança do Azure](security-center-permissions.md)