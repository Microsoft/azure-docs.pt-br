---
title: Gerenciar grupos de recursos-portal do Azure
description: Use portal do Azure para gerenciar seus grupos de recursos por meio de Azure Resource Manager. Mostra como criar, listar e excluir grupos de recursos.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 6086dffaefba003461a6edd8177afab05377103d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371245"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Gerenciar Azure Resource Manager grupos de recursos usando o portal do Azure

Saiba como usar o [portal do Azure](https://portal.azure.com) com [Azure Resource Manager](overview.md) para gerenciar seus grupos de recursos do Azure. Para gerenciar recursos do Azure, consulte [gerenciar recursos do Azure usando o portal do Azure](manage-resources-portal.md).

Outros artigos sobre como gerenciar grupos de recursos:

- [Gerenciar grupos de recursos do Azure usando o CLI do Azure](manage-resources-cli.md)
- [Gerenciar grupos de recursos do Azure usando o Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que você deseja gerenciar como um grupo. Você decide como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização. Em geral, adicione recursos que compartilham o mesmo ciclo de vida no mesmo grupo de recursos, para que você possa implantar, atualizar e excluí-los como um grupo facilmente.

O grupo de recursos armazena metadados sobre os recursos. Portanto, quando você especifica um local para o grupo de recursos, especifica onde os metadados são armazenados. Por motivos de conformidade, você precisa fazer com que os dados sejam armazenados em determinada região.


## <a name="create-resource-groups"></a>Criar grupos de recursos

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecionar **grupos de recursos**

    ![adicionar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Selecione **Adicionar**.
4. Insira os valores a seguir:

   - **Assinatura**: Selecione sua assinatura do Azure. 
   - **Grupo de recursos**: insira um nome para o novo grupo de recursos. 
   - **Região**: selecione um local do Azure, como **EUA Central**.

     ![Criar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Selecione **Examinar + Criar**
6. Selecione **Criar**. Leva alguns segundos para criar um grupo de recursos.
7. Selecione **Atualizar** no menu superior para atualizar a lista grupo de recursos e, em seguida, selecione o grupo de recursos recém-criado para abri-lo. Ou selecione **notificação**(o ícone de sino) na parte superior e, em seguida, selecione **ir para o grupo de recursos** para abrir o grupo de recursos recém-criado

    ![ir para o grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Listar os grupos de recursos

1. Entre no [portal do Azure](https://portal.azure.com).
2. Para listar os grupos de recursos, selecione **grupos de recursos**

    ![procurar grupos de recursos](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Para personalizar as informações exibidas para os grupos de recursos, selecione **Editar colunas**. A captura de tela a seguir mostra as colunas de adição que você pode adicionar à exibição:

## <a name="open-resource-groups"></a>Abrir grupos de recursos

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **grupos de recursos**.
3. Selecione o grupo de recursos que você deseja abrir.

## <a name="delete-resource-groups"></a>Excluir grupos de recursos

1. Abra o grupo de recursos que você deseja excluir.  Consulte [abrir grupos de recursos](#open-resource-groups).
2. Selecione **Excluir grupo de recursos**.

    ![excluir grupo de recursos do Azure](./media/manage-resource-groups-portal/delete-group.png)

Para obter mais informações sobre como Azure Resource Manager ordena a exclusão de recursos, consulte [Azure Resource Manager exclusão de grupo de recursos](delete-resource-group.md).

## <a name="deploy-resources-to-a-resource-group"></a>Implantar recursos em um grupo de recursos

Depois de criar um modelo do Resource Manager, você pode usar o portal do Azure para implantar os recursos do Azure. Para criar um modelo, consulte [início rápido: criar e implantar modelos de Azure Resource Manager usando o portal do Azure](../templates/quickstart-create-templates-use-the-portal.md). Para implantar um modelo usando o portal, consulte [implantar recursos com modelos do Resource Manager e portal do Azure](../templates/deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Mover para outro grupo de recursos ou assinatura

Você pode mover os recursos do grupo para outro grupo de recursos. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Bloquear grupos de recursos

O bloqueio impede que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos, como assinatura do Azure, grupo de recursos ou recurso. 

1. Abra o grupo de recursos que você deseja bloquear.  Consulte [abrir grupos de recursos](#open-resource-groups).
2. No painel esquerdo, selecione **bloqueios**.
3. Para adicionar um bloqueio ao grupo de recursos, selecione **Adicionar**.
4. Insira o **nome do bloqueio**, o tipo de **bloqueio**e as **observações**. Os tipos de bloqueio incluem **somente leitura**e **excluir**.

    ![bloquear grupo de recursos do Azure](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Para obter mais informações, consulte [Bloquear recursos para evitar alterações inesperadas](lock-resources.md).

## <a name="tag-resource-groups"></a>Marcar grupos de recursos

Você pode aplicar marcas a recursos e grupos de recursos para organizar seus ativos de modo lógico. Para obter informações, consulte [usando marcas para organizar os recursos do Azure](tag-resources.md#portal).

## <a name="export-resource-groups-to-templates"></a>Exportar grupos de recursos para modelos

Para obter informações sobre como exportar modelos, consulte [exportação única e de vários recursos para o portal de modelos](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerenciar o acesso a grupos de recursos

O Azure [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md) do Azure é a maneira como você gerencia o acesso a recursos no Azure. Para obter mais informações, confira [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber Azure Resource Manager, consulte [Azure Resource Manager visão geral](overview.md).
- Para saber mais sobre a sintaxe do modelo do Resource Manager, consulte [entender a estrutura e a sintaxe dos modelos de Azure Resource Manager](../templates/template-syntax.md).
- Para saber como desenvolver modelos, consulte os tutoriais passo a [passo](../index.yml).
- Para exibir os esquemas de modelo de Azure Resource Manager, consulte [referência de modelo](/azure/templates/).
