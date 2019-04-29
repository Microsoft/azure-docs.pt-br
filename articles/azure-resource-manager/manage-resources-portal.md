---
title: Gerenciar recursos do Azure usando o portal do Azure | Microsoft Docs
description: Use o portal do Azure e o Azure Resource Manager para gerenciar seus recursos.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 20bf38b87ce29f8506a5611ecd25cf38f6d4ed61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550599"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Gerenciar recursos do Azure usando o portal do Azure

Saiba como usar o [portal do Azure](https://portal.azure.com) com [do Azure Resource Manager](resource-group-overview.md) para gerenciar seus recursos do Azure. Para gerenciar grupos de recursos, consulte [grupos de recursos do Azure gerenciar usando o portal do Azure](./manage-resource-groups-portal.md).

Outros artigos sobre o gerenciamento de recursos:

- [Gerenciar recursos do Azure, usando a CLI do Azure](./manage-resources-cli.md)
- [Gerenciar recursos do Azure usando o PowerShell do Azure](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Implantar recursos em um grupo de recursos

Depois de criar um modelo do Resource Manager, você pode usar o portal do Azure para implantar seus recursos do Azure. Para criar um modelo, consulte [guia de início rápido: Crie e implante modelos do Azure Resource Manager usando o portal do Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Para implantar um modelo usando o portal, consulte [implantar recursos com modelos do Resource Manager e o portal do Azure](resource-group-template-deploy-portal.md).

## <a name="open-resources"></a>Recursos abertos

Recursos do Azure são organizados por serviços do Azure e por grupos de recursos. Os procedimentos a seguir mostra como abrir uma conta de armazenamento chamada **mystorage0207**. A máquina virtual reside em um grupo de recursos chamado **mystorage0207rg**.

Para abrir um recurso, o tipo de serviço:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No painel esquerdo, selecione o serviço do Azure. Nesse caso, **contas de armazenamento**.  Se você não vir o serviço listado, selecione **todos os serviços**e, em seguida, selecione o tipo de serviço.

    ![Abra o recurso do azure no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Selecione o recurso que você deseja abrir.

    ![Abra o recurso do azure no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Uma conta de armazenamento é semelhante a:

    ![Abra o recurso do azure no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Para abrir um grupo de recursos por recursos:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No painel esquerdo, selecione **grupos de recursos** para listar os recursos dentro do grupo.
3. Selecione o recurso que você deseja abrir. 

## <a name="manage-resources"></a>Gerenciar recursos

Ao exibir um recurso no portal, são exibidas as opções para gerenciar esse recurso específico.

![gerenciar recursos do Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

A captura de tela mostra as opções de gerenciamento para uma máquina virtual do Azure. Você pode executar operações como iniciar, reiniciar e parar uma máquina virtual.

## <a name="delete-resources"></a>Excluir recursos

1. Abra o recurso no portal. Para obter as etapas, consulte [abrir recursos](#open-resources).
2. Selecione **Excluir**. Captura de tela a seguir mostra as opções de gerenciamento para uma máquina virtual.

    ![excluir recursos do azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Digite o nome do recurso para confirmar a exclusão e, em seguida, selecione **excluir**.

Para obter mais informações sobre como o Azure Resource Manager ordena a exclusão de recursos, consulte [exclusão do grupo de recursos do Azure Resource Manager](./resource-group-delete.md).

## <a name="move-resources"></a>Mover recursos

1. Abra o recurso no portal. Para obter as etapas, consulte [abrir recursos](#open-resources).
2. Selecione **Mover**. Captura de tela a seguir mostra as opções de gerenciamento para uma conta de armazenamento.

    ![Mover recursos do azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Selecione **mover para outro grupo de recursos** ou **Moeve para outra assinatura** dependendo das suas necessidades.

Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](resource-group-move-resources.md).

## <a name="lock-resources"></a>Bloquear recursos

O bloqueio impede que outros usuários na sua organização acidentalmente excluam ou modifiquem recursos críticos, como a assinatura do Azure, grupo de recursos ou recurso. 

1. Abra o recurso no portal. Para obter as etapas, consulte [abrir recursos](#open-resources).
2. Selecione **bloqueia**. Captura de tela a seguir mostra as opções de gerenciamento para uma conta de armazenamento.

    ![recurso de bloqueio do azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Selecione **adicionar**e, em seguida, especifique as propriedades de bloqueio.

Para saber mais, confira [Bloquear recursos com o Gerenciador de Recursos do Azure](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Recursos de marca

Ajuda a organizar logicamente o seu grupo de recursos e recursos de marcação. 

1. Abra o recurso no portal. Para obter as etapas, consulte [abrir recursos](#open-resources).
2. Selecionar **Marcas**. Captura de tela a seguir mostra as opções de gerenciamento para uma conta de armazenamento.

    ![recursos de marca do azure](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Especifique as propriedades de marca e, em seguida, selecione **salvar**.

Para obter informações, consulte [usando marcas para organizar seus recursos do Azure](./resource-group-using-tags.md#portal).

## <a name="monitor-resources"></a>Monitorar recursos

Quando você abre um recurso, o portal apresenta gráficos e tabelas para esse tipo de recurso de monitoramento padrão. Captura de tela a seguir mostra os gráficos para uma máquina virtual:

![recurso de monitor do azure](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Você pode selecionar o ícone de pino no canto superior direito dos gráficos para fixar o gráfico no painel. Para aprender a trabalhar com painéis, confira [Criar compartilhar painéis personalizados no Portal do Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Gerenciar acesso aos recursos

O [Controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md) é a maneira de gerenciar o acesso aos recursos no Azure. Para obter mais informações, confira [gerenciar o acesso usando o portal do Azure e o RBAC](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber o Azure Resource Manager, consulte [visão geral do Azure Resource Manager](./resource-group-overview.md).
- Para obter a sintaxe do modelo do Resource Manager, consulte [entender a estrutura e sintaxe dos modelos do Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, consulte o [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para exibir os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).