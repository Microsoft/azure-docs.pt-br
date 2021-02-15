---
title: Variáveis em modelos
description: Descreve como definir variáveis em um modelo de Azure Resource Manager (modelo ARM) e arquivo bicep.
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: cafd42112e5d296cb73f88e292a66ca2203f3810
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364453"
---
# <a name="variables-in-arm-templates"></a>Variáveis em modelos ARM

Este artigo descreve como definir e usar variáveis em seu modelo de Azure Resource Manager (modelo ARM) ou arquivo bicep. Você usa variáveis para simplificar seu modelo. Em vez de repetir expressões complicadas em todo o modelo, você define uma variável que contém a expressão complicada. Em seguida, você usa essa variável conforme necessário em todo o modelo.

O Gerenciador de recursos resolve as variáveis antes de iniciar as operações de implantação. Sempre que a variável é usada no modelo, o Resource Manager a substitui pelo valor resolvido.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-variable"></a>Definir variável

Ao definir uma variável, você não especifica um [tipo de dados](template-syntax.md#data-types) para a variável. Em vez disso, forneça um valor ou uma expressão de modelo. O tipo de variável é inferido a partir do valor resolvido. O exemplo a seguir define uma variável para uma cadeia de caracteres.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "stringVar": "example value"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var stringVar = 'example value'
```

---

Você pode usar o valor de um parâmetro ou outra variável ao construir a variável.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputValue": {
    "defaultValue": "deployment parameter",
    "type": "string"
  }
},
"variables": {
  "stringVar": "myVariable",
  "concatToVar": "[concat(variables('stringVar'), '-addtovar') ]",
  "concatToParam": "[concat(parameters('inputValue'), '-addtoparam')]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputValue string = 'deployment parameter'

var stringVar = 'myVariable'
var concatToVar =  '${stringVar}-addtovar'
var concatToParam = '${inputValue}-addtoparam'
```

---

Você pode usar [funções de modelo](template-functions.md) para construir o valor da variável.

Em modelos JSON, você não pode usar a função de [referência](template-functions-resource.md#reference) ou qualquer uma das funções de [lista](template-functions-resource.md#list) na declaração de variável. Essas funções obtêm o estado de tempo de execução de um recurso e não podem ser executadas antes da implantação quando as variáveis são resolvidas.

As funções Reference e List são válidas ao declarar uma variável em um arquivo bicep.

O exemplo a seguir cria um valor de cadeia de caracteres para um nome de conta de armazenamento. Ele usa várias funções de modelo para obter um valor de parâmetro e concatena-o a uma cadeia de caracteres exclusiva.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

---

## <a name="use-variable"></a>Usar uma variável

O exemplo a seguir mostra como usar a variável para uma propriedade de recurso.

# <a name="json"></a>[JSON](#tab/json)

Em um modelo JSON, você faz referência ao valor da variável usando a função [Variables](template-functions-deployment.md#variables) .

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Em um arquivo bicep, você faz referência ao valor da variável fornecendo o nome da variável.

```bicep
resource demoAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
```

---

## <a name="example-template"></a>Modelo de exemplo

O modelo a seguir não implanta nenhum recurso. Ele mostra algumas maneiras de declarar variáveis de tipos diferentes.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Atualmente, o bicep não dá suporte a loops.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/variables.bicep":::

---

## <a name="configuration-variables"></a>Variáveis de configuração

Você pode definir variáveis que mantêm valores relacionados para configurar um ambiente. Você define a variável como um objeto com os valores. O exemplo a seguir mostra um objeto que contém valores para dois ambientes – **Test** e **prod**. Passe um desses valores durante a implantação.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.bicep":::

---

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as propriedades disponíveis para variáveis, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
* Para obter recomendações sobre como criar variáveis, consulte [Best Practices-Variables](template-best-practices.md#variables).
* Para obter um modelo de exemplo que atribui regras de segurança a um grupo de segurança de rede, consulte [regras de segurança de rede](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e arquivo de [parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
