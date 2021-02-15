---
title: Compartilhar portal do Azure dashboards usando o controle de acesso baseado em função do Azure
description: Este artigo explica como compartilhar um painel no portal do Azure usando o controle de acesso baseado em função do Azure.
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: de2efd75d07c3dc7df771aad1bd9c73453dad212
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745971"
---
# <a name="share-azure-dashboards-by-using-azure-role-based-access-control"></a>Compartilhar painéis do Azure usando o controle de acesso baseado em função do Azure

Depois de configurar um painel, você pode publicá-lo e compartilhá-lo com outros usuários na sua organização. Você permite que outras pessoas exibam seu painel usando o [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/role-assignments-portal.md). Atribuir um usuário ou grupo de usuários a uma função. Essa função define se esses usuários podem exibir ou modificar o painel publicado.

Todos os painéis publicados são implementados como recursos do Azure. Elas existem como itens gerenciáveis em sua assinatura e estão contidas em um grupo de recursos. Do ponto de vista do controle de acesso, os painéis não são diferentes de outros recursos, como uma máquina virtual ou uma conta de armazenamento.

> [!TIP]
> Blocos individuais no painel impõem seus próprios requisitos de controle de acesso com base nos recursos que exibem. Você pode compartilhar um Dashboard em larga escala ao proteger os dados em blocos individuais.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Noções básicas de controle de acesso de painéis

Com o Azure RBAC (controle de acesso baseado em função), você pode atribuir usuários a funções em três níveis diferentes de escopo:

* subscription
* grupo de recursos
* recurso

As permissões que você atribui herdam da assinatura para o recurso. O painel publicado é um recurso. Talvez você já tenha usuários atribuídos a funções para a assinatura que se aplicam ao painel publicado.

Digamos que você tenha uma assinatura do Azure e vários membros de sua equipe receberam as funções de *proprietário*, *colaborador* ou *leitor* da assinatura. Os usuários que são proprietários ou colaboradores podem listar, exibir, criar, modificar ou excluir painéis dentro da assinatura. Os usuários que são leitores podem listar e exibir painéis, mas não podem modificá-los ou excluí-los. Os usuários com acesso de leitor podem fazer edições locais em um painel publicado, como ao solucionar um problema, mas não podem publicar essas alterações de volta no servidor. Eles podem fazer uma cópia privada do painel para si mesmos.

Você também pode atribuir permissões ao grupo de recursos que contém vários dashboards ou a um Dashboard individual. Por exemplo, você pode decidir que um grupo de usuários deve ter permissões limitadas na assinatura, mas um maior acesso a um painel específico. Atribua esses usuários a uma função para esse painel.

## <a name="publish-dashboard"></a>Publicar painel

Vamos supor que você configure um dashboard que deseja compartilhar com um grupo de usuários em sua assinatura. As etapas a seguir mostram como compartilhar um painel para um grupo chamado gerenciadores de armazenamento. Você pode nomear o grupo como desejar. Para obter mais informações, consulte [Managing groups in Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Antes de atribuir acesso, você deve publicar o painel.

1. No painel, selecione **Compartilhar**.

    ![Selecione compartilhar para seu painel](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. Em **compartilhamento + controle de acesso**, selecione **publicar**.

    ![publicar seu painel](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Por padrão, o compartilhamento publica o painel em um grupo de recursos denominado **dashboards**. Para selecionar um grupo de recursos diferente, desmarque a caixa de seleção.

Seu painel agora foi publicado. Se as permissões herdadas da assinatura forem adequadas, você não precisará fazer mais nada. Outros usuários em sua organização podem acessar e modificar o Dashboard com base em sua função de nível de assinatura.

## <a name="assign-access-to-a-dashboard"></a>Atribuir acesso a um painel

Você pode atribuir um grupo de usuários a uma função para esse painel.

1. Depois de publicar o painel, selecione a opção **compartilhar** ou **descompartilhar** para acessar o **compartilhamento + controle de acesso**.

1. Em **compartilhamento + controle de acesso**, selecione **gerenciar usuários**.

    ![gerenciar usuários para um painel](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. Selecione **atribuições de função** para ver os usuários existentes que já atribuiram uma função para esse painel.

1. Para adicionar um novo usuário ou grupo, selecione **Adicionar** e **Adicionar atribuição de função**.

    ![Adicionar um usuário para acesso ao painel](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Selecione a função que representa as permissões a serem concedidas. Neste exemplo, selecione **Colaborador**.

1. Selecione o usuário ou grupo para atribuir à função. Se você não vir o usuário ou grupo que está procurando na lista, use a caixa de pesquisa. A lista de grupos disponíveis depende dos grupos que você criou no Active Directory.

1. Quando terminar de adicionar usuários ou grupos, selecione **salvar**.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma lista de funções, consulte [funções internas do Azure](../role-based-access-control/built-in-roles.md).
* Para saber mais sobre como gerenciar recursos, consulte [gerenciar recursos do Azure usando o portal do Azure](../azure-resource-manager/management/manage-resources-portal.md).