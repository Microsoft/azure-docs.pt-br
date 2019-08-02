---
title: Usar o Portal do Azure para implantar recursos do Azure | Microsoft Docs
description: Use o portal do Azure e o Azure Resource Manager para implantar seus recursos.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: tomfitz
ms.openlocfilehash: a171d9b4f054c942eebb08e7e11dd1164545f408
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460444"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Implantar recursos com modelos do Resource Manager e o portal do Azure

Saiba como usar o [portal do Azure](https://portal.azure.com) com [do Azure Resource Manager](resource-group-overview.md) para implantar seus recursos do Azure. Para saber mais sobre como gerenciar seus recursos, consulte [gerenciar recursos do Azure usando o portal do Azure](manage-resources-portal.md).

Implantar recursos do Azure usando o portal do Azure geralmente envolve duas etapas:

- Crie um grupo de recursos.
- Implante recursos no grupo de recursos.

Além disso, você também pode implantar um modelo do Azure Resource Manager para criar recursos do Azure.

Este artigo mostra os dois métodos.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Para criar um novo grupo de recursos, selecione **grupos de recursos** da [portal do Azure](https://portal.azure.com).

   ![Selecionar grupos de recursos](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Em Grupo de Recursos, selecione **Adicionar**.

   ![Adicionar grupo de recursos](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Selecione ou insira os seguintes valores de propriedade:

    - **Assinatura**: Selecione uma assinatura do Azure.
    - **Grupo de recursos**: Nomeie o grupo de recursos.
    - **Região**: Especifique um local do Azure. Isso é onde o grupo de recursos armazena metadados sobre os recursos. Por motivos de conformidade, talvez você queira especificar onde os metadados são armazenados. Em geral, é recomendável que você especifique um local em que a maioria de seus recursos residirá. Usar o mesmo local pode simplificar seu modelo.

   ![Definir valores de grupo](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. Selecione **Examinar + criar**.
1. Examine os valores e, em seguida, selecione **criar**.
1. Selecione **Refresh** antes de poder ver o novo grupo de recursos na lista.

## <a name="deploy-resources-to-a-resource-group"></a>Implantar recursos em um grupo de recursos

Depois de criar um grupo de recursos, você pode implantar os recursos para o grupo do Marketplace. O Marketplace oferece soluções predefinidas para cenários comuns.

1. Para iniciar uma implantação, selecione **criar um recurso** da [portal do Azure](https://portal.azure.com).

   ![Novo recurso](./media/resource-group-template-deploy-portal/new-resources.png)

1. Localize o tipo de recurso que você deseja implantar. Os recursos são organizados em categorias. Se você não vir a solução específica que deseja implantar, você poderá pesquisá-lo no Marketplace. Captura de tela a seguir mostra que o servidor do Ubuntu é selecionada.

   ![Selecionar tipo de recurso](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Dependendo do tipo de recurso selecionado, você terá uma coleção de propriedades significativas para definir antes da implantação. Para todos os tipos, você deve selecionar um grupo de recursos de destino. A imagem a seguir mostra como criar uma máquina virtual Linux e implantá-lo ao grupo de recursos que você criou.

   ![Criar grupo de recursos](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Como alternativa, você pode optar por criar um grupo de recursos ao implantar seus recursos. Selecione **Criar novo** e dê um nome ao grupo de recursos.

1. Sua implantação será iniciada. A implantação pode levar vários minutos. Alguns recursos demorar mais do que outros recursos. Quando a implantação for concluída, você verá uma notificação. Selecione **ir para o recurso** para abrir

   ![Exibir notificação](./media/resource-group-template-deploy-portal/view-notification.png)

1. Após implantar seus recursos, adicione mais deles ao grupo de recursos selecionando **Adicionar**.

   ![Adicionar recurso](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implantar recursos de um modelo personalizado

Se quiser executar uma implantação, mas não usar nenhum dos modelos no Marketplace, você poderá criar um modelo personalizado que define a infraestrutura para sua solução. Para saber mais sobre a criação de modelos, confira [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](resource-group-authoring-templates.md).

> [!NOTE]
> A interface do portal não oferece suporte ao referenciar um [segredo de um Key Vault](resource-manager-keyvault-parameter.md). Em vez disso, use [PowerShell](resource-group-template-deploy.md) ou [CLI do Azure](resource-group-template-deploy-cli.md) para implantar o modelo localmente ou em um URI externo.

1. Para implantar um modelo personalizado por meio do portal, selecione **criar um recurso**, pesquise **modelo**. e, em seguida, selecione **implantação de modelo**.

   ![Pesquisar implantação de modelo](./media/resource-group-template-deploy-portal/search-template.png)

1. Selecione **Criar**.
1. Você verá várias opções para criar um modelo:

    - **Criar seu próprio modelo no editor**: criar um modelo usando o modelo do portal de editor.  O editor é capaz de adicionar um esquema de modelo de recursos.
    - **Modelos comuns**: Há quatro templatess comuns para a criação de uma máquina virtual Linux, máquina de virtual do Windows, um aplicativo web e um banco de dados SQL do Azure.
    - **Carregar um modelo de início rápido do GitHub**: use um existente [modelos de início rápido](https://azure.microsoft.com/resources/templates/).

   ![Opções de exibição](./media/resource-group-template-deploy-portal/see-options.png)

    Este tutorial fornece as instruções para carregar um modelo de início rápido.

1. Sob **carregar um modelo de início rápido do GitHub**, digite ou selecione **101-storage-account-create**.

    Você tem duas opções:

    - **Selecionar modelo**: implantar o modelo.
    - **Editar modelo**: editar o modelo de início rápido antes de implantá-lo.

1. Selecione **Editar modelo** para explorar o editor de modelo do portal. O modelo é carregado no editor. Observe que há dois parâmetros: **storageAccountType** e **local**.

   ![Criar modelo](./media/resource-group-template-deploy-portal/show-json.png)

1. Fazer uma pequena alteração no modelo. Por exemplo, atualize o **storageAccountName** variável para:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Clique em **Salvar**. Agora você verá a interface de implantação de modelo do portal. Observe os dois parâmetros que você definiu no modelo.
1. Insira ou selecione os valores de propriedade:

    - **Assinatura**: Selecione uma assinatura do Azure.
    - **Grupo de recursos**: Selecione **criar novo** e dê um nome.
    - **Localização**: Selecione um local do Azure.
    - **Tipo de conta de armazenamento**: Use o valor padrão.
    - **Localização**: Use o valor padrão.
    - **Concordo com os termos e as condições declarados acima**: (selecionar)

1. Selecione **Comprar**.

## <a name="next-steps"></a>Próximas etapas

- Para exibir os logs de auditoria, confira [Operações de auditoria com o Gerenciador de Recursos](./resource-group-audit.md).
- Para solucionar erros de implantação, confira [View deployment operations](./resource-manager-deployment-operations.md) (Exibir operações de implantação).
- Para exportar um modelo de implantação ou grupo de recursos, consulte [exportação do Azure Resource Manager templates](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Para implementar com segurança seu serviço em várias regiões, consulte [Gerenciador de implantação do Azure](./deployment-manager-overview.md).
