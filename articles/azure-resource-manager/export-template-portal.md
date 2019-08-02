---
title: Exportar modelo do Resource Manager usando o portal do Azure
description: Use o portal do Azure para exportar um modelo do Azure Resource Manager de recursos em sua assinatura.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: tomfitz
ms.openlocfilehash: e482bf99013b9bec9dfbf64c4e8ad5a8a43ff540
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296315"
---
# <a name="single-and-multi-resource-export-to-template-in-azure-portal"></a>Exportação de única e vários recurso para o modelo no portal do Azure

Para ajudar com a criação de modelos do Azure Resource Manager, você pode exportar um modelo de recursos existentes. O modelo exportado o ajuda a entender a sintaxe JSON e propriedades que implantar seus recursos. Para automatizar as implantações futuras, comece com o modelo exportado e modificá-lo para seu cenário.

O Gerenciador de recursos permite que você selecione um ou mais recursos para a exportação para um modelo. Você pode se concentrar em exatamente os recursos que necessários no modelo.

Este artigo mostra como exportar modelos por meio do portal. Você também pode usar [CLI do Azure](manage-resource-groups-cli.md#export-resource-groups-to-templates), [do Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates), ou [API REST](/rest/api/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Escolha a opção de exportação à direita

Há duas maneiras de exportar um modelo:

* **Exportar do grupo de recursos ou recurso**. Essa opção gera um novo modelo de recursos existentes. O modelo exportado é um "instantâneo" do estado atual do grupo de recursos. Você pode exportar um grupo de recursos inteiro ou recursos específicos dentro desse grupo de recursos.

* **Exportar antes da implantação ou do histórico de**. Esta opção recupera uma cópia exata de um modelo usado para a implantação.

Dependendo da opção escolhida, os modelos exportados tem qualidades diferentes.

| Do grupo de recursos ou recurso | Antes da implantação ou do histórico |
| --------------------- | ----------------- |
| Modelo seja o instantâneo do estado atual dos recursos. Ele inclui todas as alterações manuais feitas depois da implantação. | Modelo mostra apenas o estado dos recursos no momento da implantação. As alterações manuais feitas após a implantação não são incluídas. |
| Você pode selecionar quais recursos de um grupo de recursos para exportar. | Todos os recursos para uma implantação específica são incluídos. Você não pode escolher um subconjunto desses recursos ou adicionar recursos que foram adicionados em um momento diferente. |
| Modelo inclui todas as propriedades para os recursos, incluindo algumas propriedades que você normalmente não seria definido durante a implantação. Você talvez queira remover ou limpar essas propriedades antes de reutilizar o modelo. | Modelo inclui apenas as propriedades necessárias para a implantação. O modelo está pronto para uso. |
| Modelo provavelmente não inclui todos os parâmetros que você precisa para reutilização. A maioria dos valores de propriedade são embutidos no modelo. Para reimplantar o modelo em outros ambientes, você precisará adicionar parâmetros que aumentam a capacidade de configurar os recursos. | Modelo inclui os parâmetros que tornam fácil reimplantar em ambientes diferentes. |

Exportar o modelo de um grupo de recursos ou recurso, quando:

* Você precisa capturar alterações para os recursos que foram feitas após a implantação original.
* Você deve selecionar a quais recursos são exportados.

Exportar o modelo antes da implantação ou do histórico, quando:

* Você deseja um modelo fácil reutilização.
* Você não precisa incluir as alterações feitas após a implantação original.

## <a name="export-template-from-resource-group"></a>Exportar modelo do grupo de recursos

Para exportar um ou mais recursos de um grupo de recursos:

1. Selecione o grupo de recursos que contém os recursos que você deseja exportar.

1. Para exportar todos os recursos no grupo de recursos, selecionar tudo e, em seguida **exportar modelo**. O **exportar modelo** opção só será habilitada depois que você selecionou pelo menos um recurso.

   ![Exportar todos os recursos](./media/export-template-portal/select-all-resources.png)

1. Para escolher os recursos específicos para exportação, marque as caixas de seleção ao lado desses recursos. Em seguida, selecione **exportar modelo**.

   ![Selecione os recursos a serem exportados](./media/export-template-portal/select-resources.png)

1. O modelo exportado é exibido e está disponível para download.

   ![Mostrar modelo](./media/export-template-portal/show-template.png)

## <a name="export-template-from-resource"></a>Exportar modelo de recurso

Para exportar um recurso:

1. Selecione o grupo de recursos que contém o recurso que você deseja exportar.

1. Selecione o recurso Exportar.

   ![Selecionar recurso](./media/export-template-portal/select-link-resource.png)

1. Para esse recurso, selecione **exportar modelo** no painel esquerdo.

   ![Exportar recurso](./media/export-template-portal/export-single-resource.png)

1. O modelo exportado é exibido e está disponível para download. O modelo contém apenas o único recurso.

## <a name="export-template-before-deployment"></a>Exportar modelo antes da implantação

1. Selecione o serviço do Azure que você deseja implantar.

1. Preencha os valores para o novo serviço.

1. Depois de passar a validação, mas antes de iniciar a implantação, selecione **baixar um modelo para a automação**.

   ![Baixar o modelo](./media/export-template-portal/download-before-deployment.png)

1. O modelo é exibido e está disponível para download.

   ![Mostrar modelo](./media/export-template-portal/show-template-before-deployment.png)

## <a name="export-template-after-deployment"></a>Exportar modelo após a implantação

Você pode exportar o modelo que foi usado para implantar os recursos existentes. O modelo que você obtém é exatamente aquele que foi usado para a implantação.

1. Selecione o grupo de recursos que você deseja exportar.

1. Selecione o link sob **implantações**.

   ![Selecione o histórico de implantação](./media/export-template-portal/select-deployment-history.png)

1. Selecione uma das implantações do histórico de implantação.

   ![Selecione a implantação](./media/export-template-portal/select-details.png)

1. Selecione **modelo**. O modelo usado para essa implantação é exibido e está disponível para download.

   ![Selecionar modelo](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como exportar modelos com [CLI do Azure](manage-resource-groups-cli.md#export-resource-groups-to-templates), [do Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates), ou [API REST](/rest/api/resources/resourcegroups/exporttemplate).
- Para obter a sintaxe do modelo do Resource Manager, consulte [entender a estrutura e sintaxe dos modelos do Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, consulte o [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para exibir os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).