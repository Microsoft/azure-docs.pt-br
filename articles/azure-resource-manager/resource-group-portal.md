---
title: Usar o Portal do Azure para gerenciar os recursos do Azure | Microsoft Docs
description: "Use o portal do Azure e o Azure Resource Manager para gerenciar seus recursos. Mostra como trabalhar com painéis para monitorar recursos."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0725bbf2-5913-4c07-af6e-24e11d957fbc
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: tomfitz
ms.openlocfilehash: 7a94fd5065de93384460e851627a9813d439956b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-resources-through-portal"></a>Gerenciar recursos do Azure por meio do portal
> [!div class="op_single_selector"]
> * [PowerShell do Azure](powershell-azure-resource-manager.md)
> * [CLI do Azure](xplat-cli-azure-resource-manager.md)
> * [Portal](resource-group-portal.md) 
> * [API REST](resource-manager-rest-api.md)
> 
> 

Este tópico mostra como usar o [Portal do Azure](https://portal.azure.com) com o [Azure Resource Manager](resource-group-overview.md) para gerenciar seus recursos do Azure. Para saber mais sobre a implantação de recursos por meio do portal, confira [Implantar recursos com modelos do Resource Manager e o Portal do Azure](resource-group-template-deploy-portal.md).

Atualmente, nem todo serviço dá suporte ao portal ou ao Gerenciador de Recursos. Para esses serviços, você precisa usar o [Portal Clássico](https://manage.windowsazure.com). Para obter o status de cada serviço, confira o [Gráfico de disponibilidade do Portal do Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## <a name="manage-resource-groups"></a>Gerenciar grupos de recursos

Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que você deseja gerenciar como um grupo. Você decide como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização. Em geral, adicione recursos que compartilham o mesmo ciclo de vida no mesmo grupo de recursos, para que você possa implantar, atualizar e excluí-los como um grupo facilmente. 

O grupo de recursos armazena metadados sobre os recursos. Portanto, quando você especifica um local para o grupo de recursos, especifica onde os metadados são armazenados. Por motivos de conformidade, você precisa fazer com que os dados sejam armazenados em determinada região.

1. Para ver todos os grupos de recursos em sua assinatura, selecione **Grupos de recursos**.
   
    ![procurar grupos de recursos](./media/resource-group-portal/browse-groups.png)
2. Para criar um grupo de recursos vazio, escolha **Adicionar**.
   
    ![adicionar grupo de recursos](./media/resource-group-portal/add-resource-group.png)
3. Forneça um nome e local para o novo grupo de recursos. Selecione **Criar**.
   
    ![criar grupo de recursos](./media/resource-group-portal/create-empty-group.png)
4. Talvez seja necessário selecionar **Atualizar** para ver o grupo de recursos recém-criado.
   
    ![atualizar grupo de recursos](./media/resource-group-portal/refresh-resource-groups.png)
5. Para personalizar as informações exibidas para os grupos de recursos, escolha **Colunas**.
   
    ![personalizar colunas](./media/resource-group-portal/select-columns.png)
6. Selecione as colunas para adicionar e, em seguida, selecione **Atualizar**.
   
    ![adicionar colunas](./media/resource-group-portal/add-columns.png)
7. Para saber mais sobre a implantação de recursos em seu novo grupo de recursos, confira [Implantar recursos com modelos do Resource Manager e o Portal do Azure](resource-group-template-deploy-portal.md).
8. Para obter acesso rápido a um grupo de recursos, você pode fixar a folha em seu painel.
   
    ![fixar grupo de recursos](./media/resource-group-portal/pin-group.png)
9. Este painel exibe o grupo de recursos e seus recursos. Você pode selecionar os grupos de recursos, ou qualquer um dos seus recursos, para navegar até o item.
   
    ![fixar grupo de recursos](./media/resource-group-portal/show-resource-group-dashboard.png)

## <a name="tag-resources"></a>Recursos de marca
Você pode aplicar marcas a recursos e grupos de recursos para organizar seus ativos de modo lógico. Para obter informações sobre como trabalhar com marcas, confira [Usando marcas para organizar os recursos do Azure](resource-group-using-tags.md).

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="monitor-resources"></a>Monitorar recursos
Quando você seleciona um recurso, a folha do recurso apresenta gráficos e tabelas padrão para esse tipo de recurso de monitoramento.

1. Selecione um recurso e observe a seção **Monitoramento** . Ela inclui gráficos que são relevantes para o tipo de recurso. A imagem a seguir mostra os dados de monitoramento padrão de uma conta de armazenamento.
   
    ![mostrar o monitoramento](./media/resource-group-portal/show-monitoring.png)
2. Você pode fixar uma seção da folha no painel selecionando as reticências (...) acima da seção. Você também pode personalizar o tamanho da seção na folha ou removê-los completamente. A imagem a seguir mostra como fixar, personalizar ou remover a seção CPU e Memória.
   
    ![fixar seção](./media/resource-group-portal/pin-cpu-section.png)
3. Depois de fixar a seção no painel, você verá o resumo nele. E selecioná-lo imediatamente conduz você a mais detalhes sobre os dados.
   
    ![exibir painel](./media/resource-group-portal/view-startboard.png)
4. Para personalizar completamente os dados que você monitora por meio do portal, navegue até o painel padrão e selecione **Novo painel**.
   
    ![painel Transações da Web](./media/resource-group-portal/dashboard.png)
5. Nomeie o novo painel e arraste os blocos para ele. Os blocos são filtrados por opções diferentes.
   
    ![painel Transações da Web](./media/resource-group-portal/create-dashboard.png)
   
     Para aprender a trabalhar com painéis, confira [Criar compartilhar painéis personalizados no Portal do Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-resources"></a>Gerenciar recursos
Na folha de um recurso, você vê as opções para gerenciá-lo. O portal apresenta as opções de gerenciamento para este tipo de recurso específico. Você pode ver os comandos de gerenciamento na parte superior da folha de recursos e à esquerda.

![Gerenciar recursos](./media/resource-group-portal/manage-resources.png)

Dessas opções, você pode executar operações como iniciar e parar uma máquina virtual ou reconfigurar suas propriedades.

## <a name="move-resources"></a>Mover recursos
Se você precisar mover um recurso para outro grupo de recursos ou outra assinatura, confira [Mover recursos para um novo grupo de recursos ou uma nova assinatura](resource-group-move-resources.md).

## <a name="lock-resources"></a>Bloquear recursos
Você pode bloquear uma assinatura, um recurso ou um grupo de recursos para impedir que outros usuários em sua organização excluam ou modifiquem recursos críticos acidentalmente. Para saber mais, confira [Bloquear recursos com o Gerenciador de Recursos do Azure](resource-group-lock-resources.md).

[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="view-your-subscription-and-costs"></a>Exibir sua assinatura e os custos
Você pode exibir as informações sobre sua assinatura e os custos acumulados para todos os seus recursos. Selecione **Assinaturas** e a assinatura que você deseja ver. Talvez você só tenha uma assinatura para selecionar.

![subscription](./media/resource-group-portal/select-subscription.png)

Na folha da assinatura, você verá uma taxa de gravação.

![taxa de gravação](./media/resource-group-portal/burn-rate.png)

Haverá também uma divisão de custos por tipo de recurso.

![custo de recurso](./media/resource-group-portal/cost-by-resource.png)

## <a name="export-template"></a>Exportar modelo
Depois de configurar o grupo de recursos, convém exibir o modelo do Resource Manager para o grupo de recursos. A exportação do modelo oferece dois benefícios:

1. Você pode automatizar com facilidade as implantações futuras da solução, pois o modelo contém a infraestrutura completa.
2. Você pode se familiarizar com a sintaxe do modelo analisando o JSON (JavaScript Object Notation) que representa sua solução.

Para obter as diretrizes passo a passo, confira [Exportar um modelo do Azure Resource Manager a partir dos recursos existentes](resource-manager-export-template.md).

## <a name="delete-resource-group-or-resources"></a>Excluir grupo de recursos ou recursos
Excluir um grupo de recursos exclui todos os recursos contidos nele. Você também pode excluir recursos individuais de um grupo de recursos. Tenha cuidado ao excluir um grupo de recursos, pois pode haver recursos em outros grupos de recursos vinculados a ele. O Resource Manager não exclui os recursos vinculados, mas talvez eles não funcionem corretamente sem os recursos esperados.

![excluir grupo](./media/resource-group-portal/delete-group.png)

## <a name="next-steps"></a>Próximas etapas
* Para exibir os logs de atividade, consulte [Operações de auditoria com o Resource Manager](resource-group-audit.md).
* Para exibir detalhes sobre uma implantação, confira [Exibir operações de implantação](resource-manager-deployment-operations.md).
* Para implantar recursos por meio do portal, confira [Implantar recursos com modelos do Resource Manager e o Portal do Azure](resource-group-template-deploy-portal.md).
* Para gerenciar o acesso aos recursos, confira [Usar as atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](../active-directory/role-based-access-control-configure.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).

