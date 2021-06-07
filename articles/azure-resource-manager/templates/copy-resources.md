---
title: Implantar várias instâncias de recursos
description: Use a operação de cópia e as matrizes em um modelo de Azure Resource Manager (modelo ARM) para implantar o tipo de recurso muitas vezes.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: c9bcb22ec53129520fd9574d0eb58b1e5777531e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97724486"
---
# <a name="resource-iteration-in-arm-templates"></a>Iteração de recurso em modelos ARM

Este artigo mostra como criar mais de uma instância de um recurso em seu modelo de Azure Resource Manager (modelo ARM). Ao adicionar o `copy` elemento à seção de recursos do modelo, você pode definir dinamicamente o número de recursos a serem implantados. Você também evita a repetição da sintaxe do modelo.

Você também pode usar `copy` com [Propriedades](copy-properties.md), [variáveis](copy-variables.md)e [saídas](copy-outputs.md).

Caso precise especificar se um recurso é ou não implantado, confira [Elemento condition](conditional-resource-deployment.md).

## <a name="syntax"></a>Sintaxe

O `copy` elemento tem o seguinte formato geral:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

A `name` propriedade é qualquer valor que identifique o loop. A `count` propriedade especifica o número de iterações que você deseja para o tipo de recurso.

Use as `mode` `batchSize` Propriedades e para especificar se os recursos são implantados em paralelo ou em sequência. Essas propriedades são descritas em [serial ou paralela](#serial-or-parallel).

## <a name="copy-limits"></a>Limites de cópia

A contagem não pode exceder 800.

A contagem não pode ser um número negativo. Ele pode ser zero se você implantar o modelo com uma versão recente do CLI do Azure, do PowerShell ou da API REST. Especificamente, você deve usar:

* Azure PowerShell **2,6** ou posterior
* CLI do Azure **2.0.74** ou posterior
* API REST versão **2019-05-10** ou posterior
* [Implantações vinculadas](linked-templates.md) devem usar a versão de API **2019-05-10** ou posterior para o tipo de recurso de implantação

As versões anteriores do PowerShell, da CLI e da API REST não dão suporte a zero para contagem.

Tenha cuidado ao usar a [implantação do modo completo](deployment-modes.md) com a cópia. Se você reimplantar com o modo completo em um grupo de recursos, todos os recursos que não forem especificados no modelo após a resolução do loop de cópia serão excluídos.

## <a name="resource-iteration"></a>Iteração de recurso

O exemplo a seguir cria o número de contas de armazenamento especificadas no `storageCount` parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

Observe que o nome de cada recurso inclui a função `copyIndex()`, que retorna a iteração atual no loop. `copyIndex()`é baseado em zero. Assim, o seguinte exemplo:

```json
"name": "[concat('storage', copyIndex())]",
```

Cria estes nomes:

* storage0
* storage1
* storage2.

Para deslocar o valor do índice, você pode passar um valor na função `copyIndex()`. O número de iterações ainda é especificado no elemento Copy, mas o valor de `copyIndex` é offset pelo valor especificado. Assim, o seguinte exemplo:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Cria estes nomes:

* storage1
* storage2
* storage3

A operação de cópia é útil ao trabalhar com matrizes porque você pode percorrer cada elemento da matriz. Use a função `length` na matriz para especificar a contagem de iterações e `copyIndex` para recuperar o índice atual na matriz.

O exemplo a seguir cria uma conta de armazenamento para cada nome fornecido no parâmetro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

Se você quiser retornar valores dos recursos implantados, poderá usar [Copiar na seção de saídas](copy-outputs.md).

## <a name="serial-or-parallel"></a>Serial ou paralela

Por padrão, o Gerenciador de Recursos cria os recursos em paralelo. Ele não aplica nenhum limite ao número de recursos implantados em paralelo, além do limite total de 800 recursos no modelo. A ordem em que eles são criados não é garantida.

No entanto, convém especificar que os recursos são implantados em sequência. Por exemplo, ao atualizar um ambiente de produção, convém balancear as atualizações para que apenas um determinado número seja atualizado por vez. Para implantar em série mais de uma instância de um recurso, defina `mode` para **serial** e `batchSize` para o número de instâncias a serem implantadas de cada vez. Com o modo serial, o Resource Manager cria uma dependência em instâncias anteriores no loop de modo que não inicie um lote até que o lote anterior esteja concluído.

O valor de `batchSize` não pode exceder o valor de `count` no elemento de cópia.

Por exemplo, para implantar em série duas contas de armazenamento por vez, use:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

A `mode` propriedade também aceita **Parallel**, que é o valor padrão.

## <a name="iteration-for-a-child-resource"></a>Iteração para um recurso filho

Você não pode usar um loop de cópia para um recurso filho. Para criar mais de uma instância de um recurso que você normalmente define como aninhado em outro recurso, você deve criar esse recurso como um recurso de nível superior. Você define a relação com o recurso pai por meio das propriedades de tipo e nome.

Por exemplo, suponha que você normalmente defina um conjunto de dados como um recurso filho em uma data factory.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Para criar mais de um conjunto de dados, mova-o para fora do data factory. O conjunto de dados deve estar no mesmo nível que a data factory, mas ainda é um recurso filho da data factory. Você preserva a relação entre um conjunto de dados e o data factory por meio das propriedades de tipo e nome. Como o tipo não pode ser inferido de sua posição no modelo, você deve fornecer o tipo totalmente qualificado no formato: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Para estabelecer uma relação pai/filho com uma instância da data factory, forneça um nome para o conjunto de dados que inclui o nome do recurso pai. Use o formato: `{parent-resource-name}/{child-resource-name}`.

O exemplo a seguir mostra a implementação:

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos a seguir mostram cenários comuns para criar mais de uma instância de um recurso ou propriedade.

|Modelo  |Descrição  |
|---------|---------|
|[Armazenamento de cópia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Implanta a mais de uma conta de armazenamento com um número de índice no nome. |
|[Armazenamento de cópia serial](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Implanta várias contas de armazenamento ao tempo. O nome inclui o número de índice. |
|[Armazenamento de cópia com matriz](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Implanta várias contas de armazenamento. O nome inclui um valor de uma matriz. |

## <a name="next-steps"></a>Próximas etapas

* Para definir dependências em recursos que são criados em um loop de cópia, consulte [definir a ordem para implantar recursos em modelos ARM](define-resource-dependency.md).
* Para percorrer um tutorial, consulte [tutorial: criar várias instâncias de recurso com modelos ARM](template-tutorial-create-multiple-instances.md).
* Para obter um módulo do Microsoft Learn que aborda a cópia de recursos, confira [Gerenciar implantações de nuvem complexas usando recursos avançados do modelo do ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Para outros usos do elemento copiar, consulte:
  * [Iteração de propriedade em modelos ARM](copy-properties.md)
  * [Iteração variável em modelos ARM](copy-variables.md)
  * [Iteração de saída em modelos ARM](copy-outputs.md)
* Para obter informações sobre como usar a cópia com modelos aninhados, consulte [usando copiar](linked-templates.md#using-copy).
