---
title: Variáveis em modelos
description: Descreve como definir variáveis em um modelo de Azure Resource Manager (modelo ARM).
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: feecc4b5df77e6a3bf51294cb12aabf44899dde5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874427"
---
# <a name="variables-in-arm-template"></a>Variáveis no modelo ARM

Este artigo descreve como definir e usar variáveis em seu modelo de Azure Resource Manager (modelo ARM). Você usa variáveis para simplificar seu modelo. Em vez de repetir expressões complicadas em todo o modelo, você define uma variável que contém a expressão complicada. Em seguida, você faz referência a essa variável conforme necessário em todo o modelo.

O Gerenciador de recursos resolve as variáveis antes de iniciar as operações de implantação. Sempre que a variável é usada no modelo, o Resource Manager a substitui pelo valor resolvido.

## <a name="define-variable"></a>Definir variável

Ao definir uma variável, forneça uma expressão de valor ou modelo que seja resolvida para um [tipo de dados](template-syntax.md#data-types). Você pode usar o valor de um parâmetro ou outra variável ao construir a variável.

Você pode usar [funções de modelo](template-functions.md) na declaração de variável, mas não pode usar a função de [referência](template-functions-resource.md#reference) ou qualquer uma das funções de [lista](template-functions-resource.md#list) . Essas funções obtêm o estado de tempo de execução de um recurso e não podem ser executadas antes da implantação quando as variáveis são resolvidas.

O seguinte exemplo mostra uma definição de variável. Ele cria um valor de cadeia de caracteres para um nome de conta de armazenamento. Ele usa várias funções de modelo para obter um valor de parâmetro e concatena-o a uma cadeia de caracteres exclusiva.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

## <a name="use-variable"></a>Usar uma variável

No modelo, você faz referência ao valor do parâmetro usando a função [Variables](template-functions-deployment.md#variables) . O exemplo a seguir mostra como usar a variável para uma propriedade de recurso.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="example-template"></a>Modelo de exemplo

O modelo a seguir não implanta nenhum recurso. Ele apenas mostra algumas maneiras de declarar variáveis.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>Variáveis de configuração

Você pode definir variáveis que mantêm valores relacionados para configurar um ambiente. Você define a variável como um objeto com os valores. O exemplo a seguir mostra um objeto que contém valores para dois ambientes – **Test** e **prod**. Você passa um desses valores durante a implantação.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as propriedades disponíveis para variáveis, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
* Para obter recomendações sobre como criar variáveis, consulte [Best Practices-Variables](template-best-practices.md#variables).
* Para obter um modelo de exemplo que atribui regras de segurança a um grupo de segurança de rede, consulte [regras de segurança de rede](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e arquivo de [parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
