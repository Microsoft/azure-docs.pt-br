---
title: Criar arquivo de parâmetros
description: Criar arquivo de parâmetros para passar valores durante a implantação de um modelo do Azure Resource Manager
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 2b6d942b21594fa608127bb8f403e72295671005
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89276636"
---
# <a name="create-resource-manager-parameter-file"></a>Criar um arquivo de parâmetro do Resource Manager

Em vez de passar parâmetros como valores embutidos no script, talvez seja mais fácil usar um arquivo JSON que contenha os valores de parâmetro. Este artigo mostra como criar o arquivo de parâmetro.

## <a name="parameter-file"></a>Arquivo de parâmetro.

O arquivo de parâmetro tem o seguinte formato:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Observe que os valores de parâmetro são armazenados como texto sem formatação no arquivo de parâmetros. Essa abordagem funciona para valores que não são confidenciais, como especificar a SKU para um recurso. Não funciona para valores confidenciais, como senhas. Se você precisar passar um valor confidencial como um parâmetro, armazene o valor em um cofre de chaves e faça referência ao cofre de chaves em seu arquivo de parâmetros. O valor confidencial é recuperado com segurança durante a implantação.

O arquivo de parâmetros a seguir inclui um valor de texto sem formatação e um valor que é armazenado em um cofre de chaves.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

Para saber mais sobre ambas como usar valores de um cofre de chaves, confira [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](key-vault-parameter.md).

## <a name="define-parameter-values"></a>Definir valores de parâmetro

Para descobrir como definir os valores de parâmetro, abra o modelo que você está implantando. Examine a seção de parâmetros do modelo. O seguinte exemplo mostra os parâmetros de um modelo.

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

O primeiro detalhe a ser observado é o nome de cada parâmetro. Os valores em seu arquivo de parâmetros devem corresponder aos nomes.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Observe o tipo do parâmetro. Os valores em seu arquivo de parâmetros devem ter os mesmos tipos. Para este modelo, você pode fornecer ambos os parâmetros como cadeias de caracteres.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Em seguida, procure um valor padrão. Se um parâmetro tiver um valor padrão, você poderá fornecer um valor, mas não precisará fazer isso.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Por fim, examine os valores permitidos e as restrições como comprimento máximo. Eles informam o intervalo de valores que você pode fornecer para o parâmetro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

O arquivo de parâmetro só pode conter valores para parâmetros definidos no modelo. Se o arquivo de parâmetro contiver parâmetros extras que não correspondem aos parâmetros no modelo, você receberá um erro.

## <a name="parameter-type-formats"></a>Formatos do tipo de parâmetro

O exemplo a seguir mostra os formatos de tipos de parâmetros diferentes.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
   }
}
```

## <a name="deploy-template-with-parameter-file"></a>Implantar modelo com arquivo de parâmetro

Para passar um arquivo de parâmetro local com CLI do Azure, use @ e o nome do arquivo de parâmetro.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Para obter mais informações, consulte [implantar recursos com modelos ARM e CLI do Azure](./deploy-cli.md#parameters).

Para passar um arquivo de parâmetro local com Azure PowerShell, use o `TemplateParameterFile` parâmetro.

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Para obter mais informações, consulte [implantar recursos com modelos ARM e Azure PowerShell](./deploy-powershell.md#pass-parameter-values)

> [!NOTE]
> Não é possível usar um arquivo de parâmetro com a folha modelo personalizado no Portal.

## <a name="file-name"></a>Nome do arquivo

A convenção geral para nomear o arquivo de parâmetros é adicionar **.parameters** ao nome do modelo. Por exemplo, se seu modelo for nomeado **azuredeploy.json**, o arquivo de parâmetros será nomeado **azuredeploy.parameters.json**. Essa convenção de nomenclatura ajuda a ver a conexão entre o modelo e os parâmetros.

Para implantar em ambientes diferentes, crie mais de um arquivo de parâmetros. Ao nomear o arquivo de parâmetros, adicione uma maneira de identificar seu uso. Por exemplo, use **azuredeploy.parameters-dev.json** e **azuredeploy.parameters-prod.json**

## <a name="parameter-precedence"></a>Precedência de parâmetro

Você pode usar parâmetros embutidos e um arquivo de parâmetro local na mesma operação de implantação. Por exemplo, você pode especificar alguns valores no arquivo de parâmetro local e adicionar outros valores embutidos durante a implantação. Se você fornecer valores para um parâmetro no arquivo de parâmetros local e embutido, o valor embutido terá precedência.

É possível usar um arquivo de parâmetros externo, fornecendo o URI para o arquivo. Quando você usa um arquivo de parâmetro externo, não pode passar outros valores embutidos ou de um arquivo local. Todos os parâmetros embutidos são ignorados. Forneça todos os valores de parâmetro no arquivo externo.

## <a name="parameter-name-conflicts"></a>Conflitos de nome de parâmetro

Se o modelo incluir um parâmetro com o mesmo nome que um dos parâmetros no comando do PowerShell, o PowerShell apresentará o parâmetro do modelo com o postfix **FromTemplate**. Por exemplo, um parâmetro chamado **ResourceGroupName** em seu modelo entra em conflito com o parâmetro **ResourceGroupName** no cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Você é solicitado a fornecer um valor para **ResourceGroupNameFromTemplate**. Você pode evitar essa confusão usando nomes de parâmetros que não são usados para comandos de implantação.


## <a name="next-steps"></a>Próximas etapas

- Para entender como definir parâmetros em seu modelo, confira [Parâmetros nos modelos do Azure Resource Manager](template-parameters.md).
- Para saber mais sobre ambas como usar valores de um cofre de chaves, confira [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](key-vault-parameter.md).
- Para obter mais informações sobre parâmetros, confira [Parâmetros nos modelos do Azure Resource Manager](template-parameters.md).
