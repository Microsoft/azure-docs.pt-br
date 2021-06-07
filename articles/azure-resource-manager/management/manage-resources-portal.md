---
title: Gerenciar recursos-portal do Azure
description: Use o portal do Azure e Azure Resource Manager para gerenciar seus recursos. Mostra como implantar e excluir recursos.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 12d704b0a3d92aa0585fb120f969000def282396
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092204"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Gerenciar recursos do Azure usando o portal do Azure

Saiba como usar o [portal do Azure](https://portal.azure.com) com [Azure Resource Manager](overview.md) para gerenciar seus recursos do Azure. Para gerenciar grupos de recursos, consulte [gerenciar grupos de recursos do Azure usando o portal do Azure](manage-resource-groups-portal.md).

Outros artigos sobre o gerenciamento de recursos:

- [Gerenciar recursos do Azure usando o CLI do Azure](manage-resources-cli.md)
- [Gerenciar recursos do Azure usando o Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Implantar recursos em um grupo de recursos

Depois de criar um modelo do Resource Manager, você pode usar o portal do Azure para implantar os recursos do Azure. Para criar um modelo, consulte [início rápido: criar e implantar modelos de Azure Resource Manager usando o portal do Azure](../templates/quickstart-create-templates-use-the-portal.md). Para implantar um modelo usando o portal, consulte [implantar recursos com modelos do Resource Manager e portal do Azure](../templates/deploy-portal.md).

## <a name="open-resources"></a>Abrir recursos

Os recursos do Azure são organizados por serviços do Azure e por grupos de recursos. Os procedimentos a seguir mostram como abrir uma conta de armazenamento chamada **mystorage0207**. A máquina virtual reside em um grupo de recursos chamado **mystorage0207rg**.

Para abrir um recurso pelo tipo de serviço:

1. Entre no [portal do Azure](https://portal.azure.com).
2. No painel esquerdo, selecione o serviço do Azure. Nesse caso, **contas de armazenamento**.  Se você não vir o serviço listado, selecione **todos os serviços** e, em seguida, selecione o tipo de serviço.

    ![abrir recurso do Azure no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Selecione o recurso que você deseja abrir.

    ![Captura de tela que realça o recurso selecionado.](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Uma conta de armazenamento é semelhante A:

    ![Captura de tela que mostra a aparência de uma conta de armazenamento.](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Para abrir um recurso por grupo de recursos:

1. Entre no [portal do Azure](https://portal.azure.com).
2. No painel esquerdo, selecione **grupos de recursos** para listar o recurso dentro do grupo.
3. Selecione o recurso que você deseja abrir. 

## <a name="manage-resources"></a>Gerenciar recursos

Ao exibir um recurso no portal, são exibidas as opções para gerenciar esse recurso específico.

![gerenciar recursos do Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

A captura de tela mostra as opções de gerenciamento para uma máquina virtual do Azure. Você pode executar operações como iniciar, reiniciar e parar uma máquina virtual.

## <a name="delete-resources"></a>Excluir recursos

1. Abra o recurso no Portal. Para as etapas, consulte [recursos abertos](#open-resources).
2. Selecione **Excluir**. A captura de tela a seguir mostra as opções de gerenciamento para uma máquina virtual.

    ![excluir recurso do Azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Digite o nome do recurso para confirmar a exclusão e, em seguida, selecione **excluir**.

Para obter mais informações sobre como Azure Resource Manager ordena a exclusão de recursos, consulte [Azure Resource Manager exclusão de grupo de recursos](delete-resource-group.md).

## <a name="move-resources"></a>Mover recursos

1. Abra o recurso no Portal. Para as etapas, consulte [recursos abertos](#open-resources).
2. Selecione **Mover**. A captura de tela a seguir mostra as opções de gerenciamento para uma conta de armazenamento.

    ![mover recurso do Azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Selecione **mover para outro grupo de recursos** ou **mover para outra assinatura** , dependendo de suas necessidades.

Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Bloquear recursos

O bloqueio impede que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos, como assinatura do Azure, grupo de recursos ou recurso. 

1. Abra o recurso no Portal. Para as etapas, consulte [recursos abertos](#open-resources).
2. Selecione **bloqueios**. A captura de tela a seguir mostra as opções de gerenciamento para uma conta de armazenamento.

    ![bloquear recurso do Azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Selecione **Adicionar** e, em seguida, especifique as propriedades do bloqueio.

Para obter mais informações, consulte [Bloquear recursos com Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Recursos de marca

A marcação ajuda a organizar o grupo de recursos e os recursos logicamente. 

1. Abra o recurso no Portal. Para as etapas, consulte [recursos abertos](#open-resources).
2. Selecionar **Marcas**. A captura de tela a seguir mostra as opções de gerenciamento para uma conta de armazenamento.

    ![marcar recurso do Azure](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Especifique as propriedades da marca e, em seguida, selecione **salvar**.

Para obter informações, consulte [usando marcas para organizar os recursos do Azure](tag-resources.md#portal).

## <a name="monitor-resources"></a>Monitorar recursos

Quando você abre um recurso, o portal apresenta gráficos e tabelas padrão para monitorar esse tipo de recurso. A captura de tela a seguir mostra os grafos de uma máquina virtual:

![monitorar recursos do Azure](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Você pode selecionar o ícone de pino no canto superior direito dos grafos para fixar o grafo no painel. Para aprender a trabalhar com painéis, confira [Criar compartilhar painéis personalizados no Portal do Azure](../../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Gerenciar o acesso aos recursos

O Azure [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md) do Azure é a maneira como você gerencia o acesso a recursos no Azure. Para obter mais informações, confira [Atribuir funções do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber Azure Resource Manager, consulte [Azure Resource Manager visão geral](overview.md).
- Para saber mais sobre a sintaxe do modelo do Resource Manager, consulte [entender a estrutura e a sintaxe dos modelos de Azure Resource Manager](../templates/template-syntax.md).
- Para saber como desenvolver modelos, consulte os tutoriais passo a [passo](../index.yml).
- Para exibir os esquemas de modelo de Azure Resource Manager, consulte [referência de modelo](/azure/templates/).
