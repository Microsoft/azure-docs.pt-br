---
title: Gerenciar grupos do Azure Resource Manager usando o portal do Azure | Microsoft Docs
description: Use o portal do Azure para gerenciar seus grupos do Azure Resource Manager.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: bc3c1a05c64edea260bd177dd7eaefc003db5310
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296300"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Gerenciar grupos de recursos do Azure Resource Manager usando o portal do Azure

Saiba como usar o [portal do Azure](https://portal.azure.com) com [do Azure Resource Manager](resource-group-overview.md) para gerenciar os grupos de recursos do Azure. Para gerenciar recursos do Azure, confira [gerenciar recursos do Azure usando o portal do Azure](./manage-resources-portal.md).

Outros artigos sobre como gerenciar grupos de recursos:

- [Gerenciar grupos de recursos do Azure, usando a CLI do Azure](./manage-resources-cli.md)
- [Gerenciar grupos de recursos do Azure usando o PowerShell do Azure](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que você deseja gerenciar como um grupo. Você decide como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização. Em geral, adicione recursos que compartilham o mesmo ciclo de vida no mesmo grupo de recursos, para que você possa implantar, atualizar e excluí-los como um grupo facilmente.

O grupo de recursos armazena metadados sobre os recursos. Portanto, quando você especifica um local para o grupo de recursos, especifica onde os metadados são armazenados. Por motivos de conformidade, você precisa fazer com que os dados sejam armazenados em determinada região.

O grupo de recursos armazena metadados sobre os recursos. Quando você especificar um local para o grupo de recursos, você está especificando onde os metadados são armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **grupos de recursos**

    ![adicionar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Selecione **Adicionar**.
4. Insira os valores a seguir:

   - **Assinatura**: Selecione sua assinatura do Azure. 
   - **Grupo de recursos**: Insira um novo nome de grupo de recursos. 
   - **Região**: Selecione um local do Azure, como **EUA**.

     ![Criar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Selecione **revisar + criar**
6. Selecione **Criar**. Demora alguns segundos para criar um grupo de recursos.
7. Selecione **Refresh** no menu superior para atualizar a lista de grupos de recursos e, em seguida, selecione o grupo de recursos criado recentemente para abri-lo. Ou selecione **notificação**(o ícone de sino) na parte superior e, em seguida, selecione **ir para o grupo de recursos** para abrir o grupo de recursos criado recentemente

    ![Vá para o grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Listar os grupos de recursos

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Para listar os grupos de recursos, selecione **grupos de recursos**

    ![procurar grupos de recursos](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Para personalizar as informações exibidas para os grupos de recursos, selecione **Editar colunas**. Captura de tela a seguir mostra as colunas de adição, que você pode adicionar à exibição:

## <a name="open-resource-groups"></a>Grupos de recursos aberto

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Grupos de recursos**.
3. Selecione o grupo de recursos que você deseja abrir.

## <a name="delete-resource-groups"></a>Excluir grupos de recursos

1. Abra o grupo de recursos que você deseja excluir.  Ver [grupos de recursos de abrir](#open-resource-groups).
2. Selecione **Excluir grupo de recursos**.

    ![Excluir grupo de recursos do azure](./media/manage-resource-groups-portal/delete-group.png)

Para obter mais informações sobre como o Azure Resource Manager ordena a exclusão de recursos, consulte [exclusão do grupo de recursos do Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-a-resource-group"></a>Implantar recursos em um grupo de recursos

Depois de criar um modelo do Resource Manager, você pode usar o portal do Azure para implantar seus recursos do Azure. Para criar um modelo, consulte [guia de início rápido: Crie e implante modelos do Azure Resource Manager usando o portal do Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Para implantar um modelo usando o portal, consulte [implantar recursos com modelos do Resource Manager e o portal do Azure](resource-group-template-deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Mover para outro grupo de recursos ou assinatura

Você pode mover os recursos no grupo para outro grupo de recursos. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Grupos de recursos de bloqueio

O bloqueio impede que outros usuários na sua organização acidentalmente excluam ou modifiquem recursos críticos, como a assinatura do Azure, grupo de recursos ou recurso. 

1. Abra o grupo de recursos que você deseja excluir.  Ver [grupos de recursos de abrir](#open-resource-groups).
2. No painel esquerdo, selecione **bloqueia**.
3. Para adicionar um bloqueio para o grupo de recursos, selecione **adicionar**.
4. Insira **nome do bloqueio**, **tipo de bloqueio**, e **notas**. Os tipos de bloqueio incluem **somente leitura**, e **excluir**.

    ![grupo de recursos do azure de bloqueio](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Para obter mais informações, consulte [bloquear recursos para evitar alterações inesperadas](./resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Marcar grupos de recursos

Você pode aplicar marcas a recursos e grupos de recursos para organizar seus ativos de modo lógico. Para obter informações, consulte [usando marcas para organizar seus recursos do Azure](./resource-group-using-tags.md#portal).

## <a name="export-resource-groups-to-templates"></a>Exportar grupos de recursos para modelos

Para obter informações sobre como exportar modelos, consulte [exportação única e vários recurso para o modelo - Portal](export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerenciar o acesso a grupos de recursos

O [Controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md) é a maneira de gerenciar o acesso aos recursos no Azure. Para obter mais informações, confira [gerenciar o acesso usando o portal do Azure e o RBAC](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber o Azure Resource Manager, consulte [visão geral do Azure Resource Manager](./resource-group-overview.md).
- Para obter a sintaxe do modelo do Resource Manager, consulte [entender a estrutura e sintaxe dos modelos do Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, consulte o [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para exibir os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).