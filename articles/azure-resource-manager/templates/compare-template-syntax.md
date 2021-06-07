---
title: Comparar sintaxe para modelos de Azure Resource Manager em JSON e bicep
description: Compara modelos de Azure Resource Manager desenvolvidos com JSON e bicep e mostra como converter entre os idiomas.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 225e52e9534a77a01502b762f043a4f34df19caa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461787"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Comparando JSON e bicep para modelos

Este artigo compara a sintaxe bicep com a sintaxe JSON para modelos de Azure Resource Manager (modelos ARM). Na maioria dos casos, o bicep fornece sintaxe que é menos detalhada do que o equivalente em JSON.

Se você estiver familiarizado com o uso do JSON para desenvolver modelos de ARM, use os exemplos a seguir para saber mais sobre a sintaxe equivalente para bicep.

## <a name="expressions"></a>Expressões

Para criar uma expressão:

```bicep
func()
```

```json
"[func()]"
```

## <a name="parameters"></a>Parâmetros

Para declarar um parâmetro com um valor padrão:

```bicep
param demoParam string = 'Contoso'
```

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

Para obter um valor de parâmetro:

```bicep
demoParam
```

```json
[parameters('demoParam'))]
```

## <a name="variables"></a>Variáveis

Para declarar uma variável:

```bicep
var demoVar = 'example value'
```

```json
"variables": {
  "demoVar": "example value"
},
```

Para obter um valor de variável:

```bicep
demoVar
```

```json
[variables('demoVar'))]
```

## <a name="strings"></a>Cadeias de caracteres

Para concatenar cadeias de caracteres:

```bicep
'${namePrefix}-vm'
```

```json
[concat(parameters('namePrefix'), '-vm')]
```

## <a name="logical-operators"></a>Operadores lógicos

Para retornar o **and** lógico:

```bicep
isMonday && isNovember
```

```json
[and(parameter('isMonday'), parameter('isNovember'))]
```

Para definir condicionalmente um valor:

```bicep
isMonday ? 'valueIfTrue' : 'valueIfFalse'
```

```json
[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]
```

## <a name="deployment-scope"></a>Escopo da implantação

Para definir o escopo de destino da implantação:

```bicep
targetScope = 'subscription'
```

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"
```

## <a name="resources"></a>Recursos

Para declarar um recurso:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

```json
"resources": [ 
  { 
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Para implantar condicionalmente um recurso:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = if(deployVM) {
  ...
}
```

```json
"resources": [ 
  {
    "condition": "[parameters('deployVM')]",
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Para definir a propriedade de recurso:

```bicep
sku: '2016-Datacenter'
```

```json
"sku": "2016-Datacenter",
```

Para obter a ID de recurso do recurso no modelo:

```bicep
nic1.id
```

```json
[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]
```

## <a name="loops"></a>Loops

Para iterar sobre itens em uma matriz ou contagem:

```bicep
[for storageName in storageAccounts: {
  ...
}]
```

```json
"copy": {
  "name": "storagecopy",
  "count": "[length(parameters('storageAccounts'))]"
},
...
```

## <a name="resource-dependencies"></a>Dependências de recursos

Para definir a dependência entre os recursos:

Para bicep, conte com a detecção automática de dependências ou defina manualmente a dependência.

```bicep
dependsOn: [ stg ]
```

```json
"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]
```

## <a name="reference-resources"></a>Recursos de referência

Para obter uma propriedade de um recurso no modelo:

```bicep
diagsAccount.properties.primaryEndpoints.blob
```

```json
[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]
```

Para obter uma propriedade de um recurso existente que não está implantado no modelo:

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
stg.properties.primaryEndpoints.blob
```

```json
// required every time the property is needed
"[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
```

## <a name="outputs"></a>Saídas

Para gerar uma propriedade de um recurso no modelo:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

## <a name="code-reuse"></a>Reutilização de código

Para separar uma solução em vários arquivos:

* Para bicep, use [módulos](bicep-tutorial-add-modules.md).
* Para JSON, use [modelos vinculados](linked-templates.md).

## <a name="recommendations"></a>Recomendações

* Quando possível, evite usar as funções [Reference](template-functions-resource.md#reference) e [ResourceId](template-functions-resource.md#resourceid) em seu arquivo bicep. Ao fazer referência a um recurso na mesma implantação do bicep, use o identificador de recurso. Por exemplo, se você implantou um recurso em seu arquivo bicep com `stg` como o identificador de recurso, use sintaxe como `stg.id` ou `stg.properties.primaryEndpoints.blob` para obter valores de propriedade. Usando o identificador de recurso, você cria uma dependência implícita entre os recursos. Você não precisa definir explicitamente a dependência com a propriedade depending.
* Se o recurso não estiver implantado no arquivo bicep, você ainda poderá obter uma referência simbólica para o recurso usando a palavra-chave **existente** .
* Use maiúsculas e minúsculas para identificadores. Se não tiver certeza de qual tipo de capitalização usar, experimente o camel case. Por exemplo, `param myCamelCasedParameter string`.
* Adicione uma descrição a um parâmetro somente quando a descrição fornecer informações essenciais aos usuários. Você pode usar `//` comentários para algumas informações.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre o bicep, consulte o [tutorial do bicep](./bicep-tutorial-create-first-bicep.md).
* Para saber mais sobre como converter modelos entre os idiomas, consulte [convertendo modelos de ARM entre JSON e bicep](bicep-decompile.md).
