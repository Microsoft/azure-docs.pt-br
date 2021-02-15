---
title: Implantar recursos com portal do Azure
description: Use portal do Azure e o gerenciamento de recursos do Azure para implantar seus recursos em um grupo de recursos em sua assinatura.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: d8467bb4e51fc4e6ba89a84f1260a8d2743758d2
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028668"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>Implantar recursos com modelos ARM e portal do Azure

Saiba como usar o [portal do Azure](https://portal.azure.com) com [modelos de Azure Resource Manager (modelos ARM)](overview.md) para implantar os recursos do Azure. Para saber mais sobre como gerenciar seus recursos, consulte [gerenciar recursos do Azure usando o portal do Azure](../management/manage-resources-portal.md).

A implantação de recursos do Azure usando o portal do Azure geralmente envolve duas etapas:

- Crie um grupos de recursos.
- Implante recursos no grupo de recursos.

Além disso, você pode criar um modelo de ARM personalizado para implantar recursos do Azure.

Este artigo mostra os dois métodos.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Para criar um novo grupo de recursos, selecione **grupos de recursos** no [portal do Azure](https://portal.azure.com).

   ![Selecionar grupos de recursos](./media/deploy-portal/select-resource-groups.png)

1. Em grupos de recursos, selecione **Adicionar**.

   ![Adicionar grupo de recursos](./media/deploy-portal/add-resource-group.png)

1. Selecione ou insira os seguintes valores de propriedade:

    - **Assinatura**: Selecione uma assinatura do Azure.
    - **Grupo de recursos**: dê um nome ao grupo de recursos.
    - **Região**: especifique um local do Azure. Esse local é onde o grupo de recursos armazena metadados sobre os recursos. Por motivos de conformidade, talvez você queira especificar onde os metadados são armazenados. Em geral, recomendamos que você especifique um local onde a maioria dos seus recursos será. Usar o mesmo local pode simplificar seu modelo.

   ![Definir valores de grupo](./media/deploy-portal/set-group-properties.png)

1. Selecione **Examinar + criar**.
1. Examine os valores e, em seguida, selecione **criar**.
1. Selecione **Atualizar** antes de poder ver o novo grupo de recursos na lista.

## <a name="deploy-resources-to-a-resource-group"></a>Implantar recursos em um grupo de recursos

Depois de criar um grupo de recursos, você pode implantar recursos no grupo por meio do Marketplace. O Marketplace oferece soluções predefinidas para cenários comuns.

1. Para iniciar uma implantação, selecione **criar um recurso** no [portal do Azure](https://portal.azure.com).

   ![Novo recurso](./media/deploy-portal/new-resources.png)

1. Localize o tipo de recurso que você deseja implantar. Os recursos são organizados em categorias. Se você não vir a solução específica que deseja implantar, você poderá pesquisá-lo no Marketplace. A captura de tela a seguir mostra que o servidor Ubuntu está selecionado.

   ![Selecionar tipo de recurso](./media/deploy-portal/select-resource-type.png)

1. Dependendo do tipo de recurso selecionado, você terá uma coleção de propriedades significativas para definir antes da implantação. Para todos os tipos, você deve selecionar um grupo de recursos de destino. A imagem a seguir mostra como criar uma máquina virtual do Linux e implantá-la no grupo de recursos que você criou.

   ![Criar grupo de recursos](./media/deploy-portal/select-existing-group.png)

   Você pode optar por criar um grupo de recursos ao implantar seus recursos. Selecione **Criar novo** e dê um nome ao grupo de recursos.

1. Sua implantação será iniciada. A implantação pode levar vários minutos. Alguns recursos levam mais tempo do que outros recursos. Quando a implantação for concluída, você verá uma notificação. Selecione **ir para o recurso** para abrir

   ![Exibir notificação](./media/deploy-portal/view-notification.png)

1. Após implantar seus recursos, adicione mais deles ao grupo de recursos selecionando **Adicionar**.

   ![Adicionar recurso](./media/deploy-portal/add-resource.png)

Embora você não o tenha visto, o portal usava um modelo ARM para implantar os recursos selecionados. Você pode encontrar o modelo do histórico de implantação. Para obter mais informações, consulte [Exportar modelo após a implantação](export-template-portal.md#export-template-after-deployment).

## <a name="deploy-resources-from-custom-template"></a>Implantar recursos de um modelo personalizado

Se quiser executar uma implantação, mas não usar nenhum dos modelos no Marketplace, você poderá criar um modelo personalizado que define a infraestrutura para sua solução. Para saber mais sobre a criação de modelos, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).

> [!NOTE]
> A interface do portal não oferece suporte ao referenciar um [segredo de um Key Vault](key-vault-parameter.md). Em vez disso, use [PowerShell](deploy-powershell.md) ou [CLI do Azure](deploy-cli.md) para implantar o modelo localmente ou em um URI externo.

1. Para implantar um modelo personalizado por meio do portal, selecione **criar um recurso**, Pesquisar **modelo**. e, em seguida, selecione **implantação de modelo**.

   ![Pesquisar implantação de modelo](./media/deploy-portal/search-template.png)

1. Selecione **Criar**.
1. Você verá várias opções para criar um modelo:

    - **Crie seu próprio modelo no editor**: Crie seu próprio modelo no editor de modelos de Portal.
    - **Modelos comuns**: Selecione entre soluções comuns.
    - **Carregar um modelo de início rápido do GitHub**: selecione em [modelos de início rápido](https://azure.microsoft.com/resources/templates/).

   ![Opções de exibição](./media/deploy-portal/see-options.png)

    Este tutorial fornece a instrução para carregar um modelo de início rápido.

1. Em **carregar um modelo de início rápido do GitHub**, digite ou selecione **101-armazenamento-conta-criar**.

    Você tem duas opções:

    - **Selecionar modelo**: implantar o modelo.
    - **Editar modelo**: Edite o modelo de início rápido antes de implantá-lo.

1. Selecione **Editar modelo** para explorar o editor de modelo de Portal. O modelo é carregado no editor. Observe que há dois parâmetros: `storageAccountType` e `location` .

   ![Criar modelo](./media/deploy-portal/show-json.png)

1. Faça uma alteração secundária no modelo. Por exemplo, atualize a `storageAccountName` variável para:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Selecione **Salvar**. Agora você vê a interface de implantação de modelo do Portal. Observe os dois parâmetros que você definiu no modelo.
1. Insira ou selecione os valores de propriedade:

    - **Assinatura**: Selecione uma assinatura do Azure.
    - **Grupo de recursos**: selecione **criar novo** e dê um nome.
    - **Local**: selecione um local do Azure.
    - **Tipo de conta de armazenamento**: Use o valor padrão.
    - **Local**: Use o valor padrão.
    - **Eu concordo com os termos e condições declarados acima**: (SELECT)

1. Selecione **Comprar**.

## <a name="next-steps"></a>Próximas etapas

- Para exibir os logs de auditoria, confira [Operações de auditoria com o Gerenciador de Recursos](../management/view-activity-logs.md).
- Para solucionar erros de implantação, confira [View deployment operations](deployment-history.md) (Exibir operações de implantação).
- Para exportar um modelo de uma implantação ou grupo de recursos, consulte [exportar modelos do ARM](export-template-portal.md).
- Para distribuir com segurança seu serviço em várias regiões, consulte [Deployment Manager do Azure](deployment-manager-overview.md).
