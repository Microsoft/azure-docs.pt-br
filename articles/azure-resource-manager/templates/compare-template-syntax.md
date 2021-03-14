---
title: Comparar sintaxe para modelos de Azure Resource Manager em JSON e bicep
description: Compara modelos de Azure Resource Manager desenvolvidos com JSON e bicep e mostra como converter entre os idiomas.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 85f85e66e69eede68bab847e4bc68514e65115eb
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418038"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Comparando JSON e bicep para modelos

Este artigo compara a sintaxe bicep com a sintaxe JSON para modelos de Azure Resource Manager (modelos ARM). Na maioria dos casos, o bicep fornece sintaxe que é menos detalhada do que o equivalente em JSON.

## <a name="syntax-equivalents"></a>Equivalentes de sintaxe

Se você estiver familiarizado com o uso do JSON para desenvolver modelos de ARM, use a tabela a seguir para saber mais sobre a sintaxe equivalente para bicep.

| Cenário | Bicep | JSON |
| -------- | ------------ | ----- |
| Criar uma expressão | `func()` | `"[func()]"` |
| Obter valor do parâmetro | `exampleParameter` | `[parameters('exampleParameter'))]` |
| Obter valor da variável | `exampleVar` | `[variables('exampleVar'))]` |
| Concatenar cadeias de caracteres | `'${namePrefix}-vm'` | `[concat(parameters('namePrefix'), '-vm')]` |
| Definir propriedade de recurso | `sku: '2016-Datacenter'` | `"sku": "2016-Datacenter",` |
| Retornar o AND lógico | `isMonday && isNovember` | `[and(parameter('isMonday'), parameter('isNovember'))]` |
| Obter a ID de recurso do recurso no modelo | `nic1.id` | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` |
| Obter Propriedade do recurso no modelo | `diagsAccount.properties.primaryEndpoints.blob` | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` |
| Definir condicionalmente um valor | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` |
| Separar uma solução em vários arquivos | Usar módulos | Usar modelos vinculados |
| Definir o escopo de destino da implantação | `targetScope = 'subscription'` | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` |
| Definir dependência | Conte com a detecção automática de dependências ou defina manualmente a dependência com `dependsOn: [ stg ]` | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` |
| Declaração de recurso | `resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {...}` | `"resources": [ { "type": "Microsoft.Compute/virtualMachines", "apiVersion": "2020-06-01", ... } ]` |

## <a name="recommendations"></a>Recomendações

* Quando possível, evite usar as funções [Reference](template-functions-resource.md#reference) e [ResourceId](template-functions-resource.md#resourceid) em seu arquivo bicep. Ao fazer referência a um recurso na mesma implantação do bicep, use o identificador de recurso. Por exemplo, se você implantou um recurso em seu arquivo bicep com `stg` como o identificador de recurso, use sintaxe como `stg.id` ou `stg.properties.primaryEndpoints.blob` para obter valores de propriedade. Usando o identificador de recurso, você cria uma dependência implícita entre os recursos. Você não precisa definir explicitamente a dependência com a propriedade depending.
* Use maiúsculas e minúsculas para identificadores. Se não tiver certeza de qual tipo de capitalização usar, experimente o camel case. Por exemplo, `param myCamelCasedParameter string`.
* Adicione uma descrição a um parâmetro somente quando a descrição fornecer informações essenciais aos usuários. Você pode usar `//` comentários para algumas informações.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre o bicep, consulte o [tutorial do bicep](./bicep-tutorial-create-first-bicep.md).
* Para saber mais sobre como converter modelos entre os idiomas, consulte [convertendo modelos de ARM entre JSON e bicep](bicep-decompile.md).
