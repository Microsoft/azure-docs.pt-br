---
title: Implantar várias instâncias dos recursos do Azure | Microsoft Docs
description: Use a operação de cópia e matrizes em um modelo do Gerenciador de Recursos do Azure para iterar várias vezes durante a implantação de recursos.
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: tomfitz
ms.openlocfilehash: dbacec6e8f91480996150e73f2a81dbcde67550b
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494797"
---
# <a name="resource-property-or-variable-iteration-in-azure-resource-manager-templates"></a>Recurso, propriedade ou iteração de variável em modelos de Azure Resource Manager

Este artigo mostra como criar mais de uma instância de um recurso, variável ou propriedade em seu modelo de Azure Resource Manager. Para criar várias instâncias, adicione o `copy` objeto ao seu modelo.

Quando usado com um recurso, o objeto de cópia tem o seguinte formato:

```json
"copy": {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "mode": "serial" <or> "parallel",
    "batchSize": <number-to-deploy-serially>
}
```

Quando usado com uma variável ou propriedade, o objeto de cópia tem o seguinte formato:

```json
"copy": [
  {
      "name": "<name-of-loop>",
      "count": <number-of-iterations>,
      "input": <values-for-the-property-or-variable>
  }
]
```

Ambos os usos são descritos mais detalhadamente neste artigo. Para um tutorial, consulte [Tutorial: crie várias instâncias de recursos usando modelos do Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

Caso precise especificar se um recurso é ou não implantado, confira [Elemento condition](resource-group-authoring-templates.md#condition).

## <a name="copy-limits"></a>Limites de cópia

Para especificar o número de iterações, você fornece um valor para a propriedade Count. A contagem não pode exceder 800.

A contagem não pode ser um número negativo. Se você implantar um modelo com a API REST versão **2019-05-10** ou posterior, poderá definir Count como zero. As versões anteriores da API REST não dão suporte a zero para contagem. Atualmente, CLI do Azure ou PowerShell não dão suporte a zero para contagem, mas esse suporte será adicionado em uma versão futura.

Tenha cuidado ao usar a [implantação do modo completo](deployment-modes.md) com a cópia. Se você reimplantar com o modo completo em um grupo de recursos, todos os recursos que não forem especificados no modelo após a resolução do loop de cópia serão excluídos.

Os limites para a contagem são os mesmos, se usados com um recurso, uma variável ou uma propriedade.

## <a name="resource-iteration"></a>Iteração de recurso

Quando você deve decidir durante a implantação entre criar uma ou mais instâncias de um recurso, adicione um elemento `copy` ao tipo de recurso. No elemento copiar, especifique o número de iterações e um nome para esse loop.

O recurso para criar várias vezes leva o seguinte formato:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
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

Para deslocar o valor do índice, você pode passar um valor na função copyIndex(). O número de iterações ainda é especificado no elemento Copy, mas o valor de copyIndex é offset pelo valor especificado. Assim, o seguinte exemplo:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Cria estes nomes:

* storage1
* storage2
* storage3

A operação de cópia é útil ao trabalhar com matrizes porque você pode percorrer cada elemento da matriz. Use a função `length` na matriz para especificar a contagem de iterações e `copyIndex` para recuperar o índice atual na matriz. Assim, o seguinte exemplo:

```json
"parameters": { 
  "org": { 
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
    "name": "[concat('storage', parameters('org')[copyIndex()])]", 
    "copy": { 
      "name": "storagecopy", 
      "count": "[length(parameters('org'))]" 
    }, 
    ...
  } 
]
```

Cria estes nomes:

* storagecontoso
* storagefabrikam
* storagecoho

Por padrão, o Gerenciador de Recursos cria os recursos em paralelo. Ele não aplica nenhum limite ao número de recursos implantados em paralelo, além do limite total de 800 recursos no modelo. A ordem em que eles são criados não é garantida.

No entanto, convém especificar que os recursos são implantados em sequência. Por exemplo, ao atualizar um ambiente de produção, convém balancear as atualizações para que apenas um determinado número seja atualizado por vez. Para implantar em série mais de uma instância de um recurso, defina `mode` para **serial** e `batchSize` para o número de instâncias a serem implantadas de cada vez. Com o modo serial, o Resource Manager cria uma dependência em instâncias anteriores no loop de modo que não inicie um lote até que o lote anterior esteja concluído.

Por exemplo, para implantar em série duas contas de armazenamento por vez, use:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      }
    }
  ],
  "outputs": {}
}
```

A propriedade de modo também aceita **paralelo**, que é o valor padrão.

Para obter informações sobre como usar a cópia com modelos aninhados, consulte [usando copiar](resource-group-linked-templates.md#using-copy).

## <a name="property-iteration"></a>Iteração de propriedade

Para criar mais de um valor para uma propriedade em um recurso, inclua uma matriz `copy` no elemento properties. Essa matriz contém objetos, e cada objeto tem as seguintes propriedades:

* nome - o nome da propriedade para criar vários valores para
* Contagem – o número de valores para criar.
* entrada - um objeto que contém os valores para atribuir à propriedade  

O exemplo a seguir mostra como aplicar `copy` para a propriedade dataDisks em uma máquina virtual:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
        "name": "dataDisks",
        "count": 3,
        "input": {
          "lun": "[copyIndex('dataDisks')]",
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      }],
      ...
```

Observe que ao usar `copyIndex` dentro de uma iteração de propriedade, você deve fornecer o nome da iteração. Você não precisa fornecer o nome quando usado com a iteração de recurso.

Gerenciador de recursos expande a matriz `copy` durante a implantação. O nome da matriz se torna o nome da propriedade. Os valores de entrada se tornam as propriedades do objeto. O modelo implantado se torna:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      ],
      ...
```

O elemento de cópia está em uma matriz, assim você pode especificar mais de uma propriedade para o recurso. Adicione um objeto para cada propriedade para criar.

```json
{
  "name": "string",
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

Você pode usar iteração de recurso e propriedade juntos. Referência a iteração de propriedade por nome.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "apiVersion": "2018-04-01",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="variable-iteration"></a>Iteração de variável

Para criar várias instâncias de uma variável, use a propriedade `copy` na seção de variáveis. Crie uma matriz de elementos construídos a partir do valor na propriedade `input`. Você pode usar a propriedade `copy` dentro de uma variável ou no nível superior da seção de variáveis. Ao usar `copyIndex` dentro de uma iteração de variável, você deve fornecer o nome da iteração.

Para obter um exemplo simples de criação de uma matriz de valores de cadeia de caracteres, consulte [copiar modelo de matriz](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json).

O exemplo a seguir mostra várias maneiras diferentes de criar variáveis de matriz com elementos construídos dinamicamente. Ele mostra como usar a cópia dentro de uma variável para criar matrizes de objetos e cadeias de caracteres. Ele também mostra como usar a cópia no nível superior para criar matrizes de objetos, cadeias de caracteres e inteiros.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

O tipo de variável que é criado depende do objeto de entrada. Por exemplo, a variável chamada **Top-Level-Object-array** no exemplo anterior retorna:

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

E, a variável chamada de **nível superior-String-array** retorna:

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>Depender dos recursos em um loop

Você especifica que um recurso é implantado após outro recurso usando o elemento `dependsOn`. Para implantar um recurso que depende da coleção de recursos em um loop, forneça o nome do loop de cópia no elemento dependsOn. O exemplo a seguir mostra como implantar três contas de armazenamento antes de implantar a máquina Virtual. A definição completa da máquina Virtual não é exibida. Observe que o elemento de cópia tem o nome definido como `storagecopy` e o elemento dependsOn para as Máquinas Virtuais também é definido como `storagecopy`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    },
    {
      "apiVersion": "2015-06-15", 
      "type": "Microsoft.Compute/virtualMachines", 
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

<a id="looping-on-a-nested-resource" />

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
|[Implantação da VM com um número variável de discos de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Implanta vários discos de dados com uma máquina virtual. |
|[Variáveis de cópia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Demonstra as diferentes maneiras de iteração em variáveis. |
|[Várias regras de segurança](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Implanta várias regras de segurança em um grupo de segurança de rede. Cria as regras de segurança a partir de um parâmetro. Para o parâmetro, consulte [vários arquivos de parâmetro NSG](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Próximas etapas

* Para passar por um tutorial, consulte [Tutorial: crie várias instâncias de recursos usando os modelos do Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

* Para saber mais sobre as seções de um modelo, veja [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para saber mais sobre como implantar o modelo, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).

