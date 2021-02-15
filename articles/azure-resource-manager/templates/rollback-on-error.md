---
title: Reverter em caso de erro para a implantação bem-sucedida
description: Especifique que uma implantação com falha deve ser revertida para uma implantação bem-sucedida.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 742a8f16a2dce3204b48085759091540586a4522
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492205"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Reverter em caso de erro para implantação bem-sucedida

Quando uma implantação falha, é possível reimplantar automaticamente uma implantação anterior bem-sucedida com base em seu histórico de implantações. Essa funcionalidade será útil se você tiver um estado válido conhecido para sua implantação de infraestrutura e quiser reverter para esse estado. Você pode especificar uma determinada implantação anterior ou a última implantação bem-sucedida.

> [!IMPORTANT]
> Esse recurso reverte uma implantação com falha reimplantando uma implantação anterior. Esse resultado pode ser diferente do que você esperaria de desfazer a implantação com falha. Certifique-se de entender como a implantação anterior é reimplantada.

## <a name="considerations-for-redeploying"></a>Considerações para reimplantação

Antes de usar esse recurso, considere estes detalhes sobre como a reimplantação é tratada:

- A implantação anterior é executada usando o [modo completo](./deployment-modes.md#complete-mode), mesmo que você tenha usado o [modo incremental](./deployment-modes.md#incremental-mode) durante a implantação anterior. A reimplantação no modo completo pode produzir resultados inesperados quando a implantação anterior era incremental. O modo completo significa que todos os recursos não incluídos na implantação anterior serão excluídos. Especifique uma implantação anterior que represente todos os recursos e seus Estados que você deseja que existam no grupo de recursos. Para obter mais informações, consulte [modos de implantação](./deployment-modes.md).
- A reimplantação é executada exatamente como foi executada anteriormente com os mesmos parâmetros. Você não pode alterar os parâmetros.
- A reimplantação afeta apenas os recursos, as alterações de dados não são afetadas.
- Você pode usar esse recurso somente com implantações de grupo de recursos. Ele não dá suporte a assinaturas, grupos de gerenciamento ou implantações em nível de locatário. Para obter mais informações sobre a implantação em nível de assinatura, consulte [criar grupos de recursos e recursos no nível da assinatura](./deploy-to-subscription.md).
- Você só pode usar essa opção com implantações de nível raiz. Implantações de um modelo aninhado não estão disponíveis para reimplantação.

Para usar essa opção, suas implantações devem ter nomes exclusivos no histórico de implantação. É apenas com nomes exclusivos que uma implantação específica pode ser identificada. Se você não tiver nomes exclusivos, uma implantação com falha poderá substituir uma implantação bem-sucedida no histórico.

Se você especificar uma implantação anterior que não existe no histórico de implantação, a reversão retornará um erro.

## <a name="powershell"></a>PowerShell

Para reimplementar a última implantação bem-sucedida, adicione o parâmetro `-RollbackToLastDeployment` como um sinalizador.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Para reimplantar uma implantação específica, use o `-RollBackDeploymentName` parâmetro e forneça o nome da implantação. A implantação especificada deve ter êxito.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>CLI do Azure

Para reimplementar a última implantação bem-sucedida, adicione o parâmetro `--rollback-on-error` como um sinalizador.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Para reimplantar uma implantação específica, use o `--rollback-on-error` parâmetro e forneça o nome da implantação. A implantação especificada deve ter êxito.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>API REST

Para reimplantar a última implantação bem-sucedida se a implantação atual falhar, use:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Para reimplantar uma implantação específica se a implantação atual falhar, use:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

A implantação especificada deve ter êxito.

## <a name="next-steps"></a>Próximas etapas

- Para entender os modos completo e incremental, consulte [Azure Resource Manager modos de implantação](deployment-modes.md).
- Para entender como definir parâmetros em seu modelo, confira [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](template-syntax.md).
