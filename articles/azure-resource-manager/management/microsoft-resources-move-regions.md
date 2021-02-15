---
title: Mover regiões para recursos no Microsoft. Resources
description: Mostre como mover recursos que estão no namespace Microsoft. Resources para novas regiões.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 098e5c36969da12966d793b6e1eddd4f79701ead
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88951048"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Mover recursos do Microsoft. Resources para uma nova região

Talvez seja necessário mover um recurso existente para uma nova região. Este artigo mostra como mover dois tipos de recursos-templateSpecs e deploymentScripts-que estão no namespace Microsoft. Resources.

## <a name="move-template-specs-to-new-region"></a>Mover as especificações do modelo para uma nova região

Se você tiver uma [especificação de modelo](../templates/template-specs.md) em uma região e quiser movê-la para uma nova região, poderá exportar a especificação do modelo e reimplantá-la.

1. Use o comando para exportar uma especificação de modelo existente. Para os valores de parâmetro, forneça os valores que correspondem à especificação do modelo que você deseja exportar.

   Para o Azure PowerShell, use:

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   Para a CLI do Azure, use:

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. Use a especificação do modelo exportado para criar uma nova especificação de modelo. Os exemplos a seguir mostram `westus` para a nova região, mas você pode fornecer a região desejada.

   Para o Azure PowerShell, use:

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   Para a CLI do Azure, use:

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>Mover scripts de implantação para uma nova região

1. Selecione o grupo de recursos que contém o script de implantação que você deseja mover para uma nova região.

1. [Exporte o modelo](../templates/export-template-portal.md). Ao exportar, selecione o script de implantação e todos os outros recursos necessários.

1. No modelo exportado, exclua as seguintes propriedades:

   * tenantId
   * principalId
   * clientId

1. O modelo exportado tem um valor codificado para a região do script de implantação.

   ```json
   "location": "westus2",
   ```

   Altere o modelo para permitir um parâmetro para definir o local. Para obter mais informações, consulte [definir local do recurso no modelo ARM](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [Implante o modelo exportado](../templates/deploy-powershell.md) e especifique uma nova região para o script de implantação.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como mover recursos para um novo grupo de recursos ou assinatura, confira [mover recursos para um novo grupo de recursos ou assinatura](move-resource-group-and-subscription.md).
* Para saber mais sobre como mover recursos para uma nova região, consulte [movendo recursos do Azure entre regiões](move-region.md).
