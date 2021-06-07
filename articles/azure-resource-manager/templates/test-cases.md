---
title: Casos de teste para o kit de ferramentas de teste
description: Descreve os testes que são executados pelo kit de ferramentas de teste do modelo ARM.
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 451323058ad743d6e26fc8bcea27d1b44c76f543
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97674035"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>Casos de teste padrão para o kit de ferramentas de teste do modelo ARM

Este artigo descreve os testes padrão que são executados com o [modelo do kit de ferramentas de teste](test-toolkit.md). Ele fornece exemplos que passam ou reprovam o teste. Ele inclui o nome de cada teste.

## <a name="use-correct-schema"></a>Usar esquema correto

Nome do teste: o **esquema deploymenttemplate está correto**

Em seu modelo, você deve especificar um valor de esquema válido.

O exemplo a seguir **passa** esse teste.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

A propriedade de esquema no modelo deve ser definida como um dos seguintes esquemas:

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>Os parâmetros devem existir

Nome do teste: a **propriedade de parâmetros deve existir**

Seu modelo deve ter um elemento Parameters. Os parâmetros são essenciais para tornar seus modelos reutilizáveis em ambientes diferentes. Adicione parâmetros ao modelo para obter valores que mudam ao implantar em ambientes diferentes.

O exemplo a seguir **passa** este teste:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>Parâmetros declarados devem ser usados

Nome do teste: os **parâmetros devem ser referenciados**

Para reduzir a confusão em seu modelo, exclua todos os parâmetros definidos, mas não usados. Esse teste encontra todos os parâmetros que não são usados em nenhum lugar no modelo. A eliminação de parâmetros não utilizados também facilita a implantação do modelo, pois você não precisa fornecer valores desnecessários.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>Os parâmetros seguros não podem ter o padrão codificado

Nome do teste: **os parâmetros de cadeia de caracteres segura não podem ter o padrão**

Não forneça um valor padrão embutido em código para um parâmetro seguro em seu modelo. Uma cadeia de caracteres vazia é suficiente para o valor padrão.

Você usa os tipos **SecureString** ou **secureobject** em parâmetros que contêm valores confidenciais, como senhas. Quando um parâmetro usa um tipo seguro, o valor do parâmetro não é registrado ou armazenado no histórico de implantação. Essa ação impede que um usuário mal-intencionado descubra o valor confidencial.

No entanto, quando você fornece um valor padrão, esse valor é detectável por qualquer pessoa que possa acessar o modelo ou o histórico de implantação.

O exemplo a seguir **falha** neste teste:

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

O exemplo a seguir **passa** este teste:

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>As URLs de ambiente não podem ser codificadas

Nome do teste: **deploymenttemplate não deve conter URI codificado**

Não codifique as URLs de ambiente em seu modelo. Em vez disso, use a [função de ambiente](template-functions-deployment.md#environment) para obter dinamicamente essas URLs durante a implantação. Para obter uma lista dos hosts de URL que estão bloqueados, consulte o [caso de teste](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1).

O exemplo a seguir **falha** nesse teste porque a URL está codificada.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

O teste também **falha** quando usado com [concat](template-functions-string.md#concat) ou [URI](template-functions-string.md#uri).

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

O exemplo a seguir **passa** esse teste.

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>O local usa o parâmetro

Nome do teste: o **local não deve ser codificado**

Seus modelos devem ter um parâmetro chamado local. Use esse parâmetro para definir o local dos recursos em seu modelo. No modelo principal (chamado azuredeploy.jsno ou mainTemplate.jsem), esse parâmetro pode padrão para o local do grupo de recursos. Em modelos vinculados ou aninhados, o parâmetro de local não deve ter um local padrão.

Os usuários do seu modelo podem ter regiões limitadas disponíveis para eles. Quando você embuti código o local do recurso, os usuários podem ser impedidos de criar um recurso nessa região. Os usuários podem ser bloqueados mesmo se você definir o local do recurso como `"[resourceGroup().location]"` . O grupo de recursos pode ter sido criado em uma região que os outros usuários não podem acessar. Esses usuários estão impedidos de usar o modelo.

Ao fornecer um parâmetro de local que usa como padrão o local do grupo de recursos, os usuários podem usar o valor padrão quando conveniente, mas também especificar um local diferente.

O exemplo a seguir **falha** nesse teste porque o local no recurso está definido como `resourceGroup().location` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

O exemplo a seguir usa um parâmetro Location, mas **falha** esse teste porque o parâmetro Location assume como padrão um local codificado.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Em vez disso, crie um parâmetro que usa como padrão o local do grupo de recursos, mas permite que os usuários forneçam um valor diferente. O exemplo a seguir **passa** esse teste quando o modelo é usado como o modelo principal.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

No entanto, se o exemplo anterior for usado como um modelo vinculado, o teste **falhará**. Quando usado como um modelo vinculado, remova o valor padrão.

## <a name="resources-should-have-location"></a>Os recursos devem ter o local

Nome do teste: os **recursos devem ter o local**

O local de um recurso deve ser definido como uma [expressão de modelo](template-expressions.md) ou `global` . A expressão de modelo normalmente usaria o parâmetro Location descrito no teste anterior.

O exemplo a seguir **falha** nesse teste porque o local não é uma expressão ou `global` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

O exemplo a seguir **passa** esse teste.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

O exemplo a seguir também **passa** esse teste.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>O tamanho da VM usa o parâmetro

Nome do teste: **o tamanho da VM deve ser um parâmetro**

Não Codifique o tamanho da máquina virtual. Forneça um parâmetro para que os usuários do seu modelo possam modificar o tamanho da máquina virtual implantada.

O exemplo a seguir **falha** nesse teste.

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

Em vez disso, forneça um parâmetro.

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

Em seguida, defina o tamanho da VM para esse parâmetro.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>Os valores min e Max são números

Nome do teste: o **valor mínimo e o máximo são números**

Se você definir valores mínimos e máximos para um parâmetro, especifique-os como números.

O exemplo a seguir **falha** neste teste:

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Em vez disso, forneça os valores como números. O exemplo a seguir **passa** este teste:

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

Você também receberá esse aviso se fornecer um valor mínimo ou máximo, mas não o outro.

## <a name="artifacts-parameter-defined-correctly"></a>Parâmetro de artefatos definido corretamente

Nome do teste: **parâmetro de artefatos**

Quando você inclui parâmetros para `_artifactsLocation` e `_artifactsLocationSasToken` , use os padrões e tipos corretos. As condições a seguir devem ser atendidas para passar este teste:

* Se você fornecer um parâmetro, deverá fornecer o outro
* `_artifactsLocation` deve ser uma **cadeia de caracteres**
* `_artifactsLocation` deve ter um valor padrão no modelo principal
* `_artifactsLocation` Não é possível ter um valor padrão em um modelo aninhado 
* `_artifactsLocation` deve ter uma `"[deployment().properties.templateLink.uri]"` ou a URL do repositório bruto para seu valor padrão
* `_artifactsLocationSasToken` deve ser uma **secureString**
* `_artifactsLocationSasToken` Só pode ter uma cadeia de caracteres vazia para seu valor padrão
* `_artifactsLocationSasToken` Não é possível ter um valor padrão em um modelo aninhado 

## <a name="declared-variables-must-be-used"></a>Variáveis declaradas devem ser usadas

Nome do teste: as **variáveis devem ser referenciadas**

Para reduzir a confusão em seu modelo, exclua todas as variáveis que estão definidas, mas não usadas. Esse teste localiza todas as variáveis que não são usadas em qualquer lugar no modelo.

## <a name="dynamic-variable-should-not-use-concat"></a>A variável dinâmica não deve usar Concat

Nome do teste: **referências de variáveis dinâmicas não devem usar Concat**

Às vezes, você precisa construir dinamicamente uma variável com base no valor de outra variável ou parâmetro. Não use a função [concat](template-functions-string.md#concat) ao definir o valor. Em vez disso, use um objeto que inclua as opções disponíveis e obtenha dinamicamente uma das propriedades do objeto durante a implantação.

O exemplo a seguir **passa** esse teste. A variável **currentImage** é definida dinamicamente durante a implantação.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>Usar versão da API recente

Nome do teste: **ApiVersions deve ser recente**

A versão de API para cada recurso deve usar uma versão recente. O teste avalia a versão usada em relação às versões disponíveis para esse tipo de recurso.

## <a name="use-hardcoded-api-version"></a>Usar versão de API codificada

Nome do teste: **provedores ApiVersions não são permitidos**

A versão de API para um tipo de recurso determina quais propriedades estão disponíveis. Forneça uma versão de API embutida em código em seu modelo. Não recupere uma versão de API que seja determinada durante a implantação. Você não saberá quais propriedades estão disponíveis.

O exemplo a seguir **falha** nesse teste.

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

O exemplo a seguir **passa** esse teste.

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>As propriedades não podem estar vazias

Nome do teste: o **modelo não deve conter espaços em branco**

Não codifique as propriedades para um valor vazio. Os valores vazios incluem cadeias de caracteres, objetos ou matrizes nulas e vazias. Se você definiu uma propriedade como um valor vazio, remova essa propriedade do modelo. No entanto, não há problema em definir uma propriedade como um valor vazio durante a implantação, como por meio de um parâmetro.

## <a name="use-resource-id-functions"></a>Usar funções de ID de recurso

Nome do teste: as **IDs devem ser derivadas de ResourceId**

Ao especificar uma ID de recurso, use uma das funções de ID de recurso. As funções permitidas são:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

Não use a função concat para criar uma ID de recurso. O exemplo a seguir **falha** nesse teste.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

O exemplo a seguir **passa** esse teste.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>A função ResourceId tem parâmetros corretos

Nome do teste: **ResourceId não deve conter**

Ao gerar IDs de recurso, não use funções desnecessárias para parâmetros opcionais. Por padrão, a função [ResourceId](template-functions-resource.md#resourceid) usa a assinatura e o grupo de recursos atuais. Você não precisa fornecer esses valores.  

O exemplo a seguir **falha** nesse teste, porque você não precisa fornecer a ID da assinatura e o nome do grupo de recursos atuais.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

O exemplo a seguir **passa** esse teste.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Este teste se aplica a:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [reference](template-functions-resource.md#reference)
* [list*](template-functions-resource.md#list)

Para `reference` `list*` o e o, o teste **falha** quando você usa `concat` o para construir a ID do recurso.

## <a name="dependson-best-practices"></a>depende das práticas recomendadas

Nome do teste: **as práticas recomendadas dependentes**

Ao definir as dependências de implantação, não use a função [If](template-functions-logical.md#if) para testar uma condição. Se um recurso depender de um recurso que é [implantado condicionalmente](conditional-resource-deployment.md), defina a dependência como você faria com qualquer recurso. Quando um recurso condicional não é implantado, Azure Resource Manager o remove automaticamente das dependências necessárias.

O exemplo a seguir **falha** nesse teste.

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

O exemplo a seguir **passa** esse teste.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>Implantações aninhadas ou vinculadas não podem usar debug

Nome do teste: os **recursos de implantação não devem ser depurados**

Ao definir um [modelo aninhado ou vinculado](linked-templates.md) com o tipo de recurso **Microsoft. Resources/Implantations** , você pode habilitar a depuração para esse modelo. A depuração é bem quando você precisa testar esse modelo, mas deve ser ativada quando estiver pronto para usar o modelo em produção.

## <a name="admin-user-names-cant-be-literal-value"></a>Nomes de usuário de administrador não podem ser valores literais

Nome do teste: **AdminUsername não deve ser um literal**

Ao definir um nome de usuário administrador, não use um valor literal.

O exemplo a seguir **falha** neste teste:

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Em vez disso, use um parâmetro. O exemplo a seguir **passa** este teste:

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Usar imagem de VM mais recente

Nome do teste: as **imagens de VM devem usar a versão mais recente**

Se o modelo incluir uma máquina virtual com uma imagem, verifique se ela está usando a versão mais recente da imagem.

## <a name="use-stable-vm-images"></a>Usar imagens de VM estáveis

Nome do teste: as **máquinas virtuais não devem ser visualizadas**

As máquinas virtuais não devem usar imagens de visualização.

O exemplo a seguir **falha** nesse teste.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

O exemplo a seguir **passa** esse teste.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Não usar a extensão ManagedIdentity

Nome do teste: **ManagedIdentityExtension não deve ser usado**

Não aplique a extensão ManagedIdentity a uma máquina virtual. Para obter mais informações, consulte [como parar de usar a extensão de identidades gerenciadas da máquina virtual e começar a usar o serviço de metadados de instância do Azure](../../active-directory/managed-identities-azure-resources/howto-migrate-vm-extension.md).

## <a name="outputs-cant-include-secrets"></a>As saídas não podem incluir segredos

Nome do teste: **saídas não devem conter segredos**

Não inclua nenhum valor na seção de saídas que exponha segredos potencialmente. A saída de um modelo é armazenada no histórico de implantação, de modo que um usuário mal-intencionado possa encontrar essas informações.

O exemplo a seguir **falha** no teste porque ele inclui um parâmetro seguro em um valor de saída.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

O exemplo a seguir **falha** porque ele usa uma função [list *](template-functions-resource.md#list) nas saídas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como executar o kit de ferramentas de teste, consulte [usar o kit de ferramentas do ARM template](test-toolkit.md).
- Para um módulo Microsoft Learn que abrange o uso do kit de ferramentas de teste, consulte [Visualizar alterações e validar recursos do Azure usando o What-If e o ARM template Test Toolkit](/learn/modules/arm-template-test/).