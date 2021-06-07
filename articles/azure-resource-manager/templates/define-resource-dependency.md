---
title: Definir ordem de implantação para recursos
description: Descreve como definir um recurso do Azure como dependente de outro recurso durante a implantação. As dependências garantem que os recursos sejam implantados na ordem correta.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: f6b63b066da06a17c3a2e51ab0f3ab9bf521a144
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934740"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Definir a ordem de implantação de recursos em modelos do ARM

Ao implantar recursos, talvez seja necessário verificar se existem alguns recursos antes de outros recursos. Por exemplo, você precisa de um SQL Server lógico antes de implantar um banco de dados. Você estabelece essa relação marcando um recurso como dependente do outro recurso. Use o `dependsOn` elemento para definir uma dependência explícita. Use as funções de **referência** ou de **lista** para definir uma dependência implícita.

Azure Resource Manager avalia as dependências entre os recursos e os implanta em sua ordem dependente. Quando os recursos não dependem uns dos outros, o Gerenciador de Recursos os implanta paralelamente. Você só precisa definir as dependências para recursos que são implantados no mesmo modelo.

## <a name="dependson"></a>dependsOn

Dentro de seu modelo de Azure Resource Manager (modelo ARM), o `dependsOn` elemento permite que você defina um recurso como dependente de um ou mais recursos. Seu valor é uma matriz JavaScript Object Notation (JSON) de cadeias de caracteres, cada uma delas é um nome de recurso ou uma ID. A matriz pode incluir recursos que são [implantados condicionalmente](conditional-resource-deployment.md). Quando um recurso condicional não é implantado, Azure Resource Manager o remove automaticamente das dependências necessárias.

O exemplo a seguir mostra uma interface de rede que depende de uma rede virtual, grupo de segurança de rede e endereço IP público. Para obter o modelo completo, consulte [o modelo de início rápido para uma VM do Linux](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-simple-linux/azuredeploy.json).

```json
{
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-06-01",
    "name": "[variables('networkInterfaceName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
      "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
      "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
      "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
    ],
    ...
}
```

Embora você possa ser inclinado a usar `dependsOn` para mapear relações entre seus recursos, é importante entender por que está fazendo isso. Por exemplo, para documentar como os recursos são interconectados, `dependsOn` não é a abordagem certa. Não é possível consultar quais recursos foram definidos no `dependsOn` elemento após a implantação. A definição de dependências desnecessárias reduz o tempo de implantação porque o Gerenciador de recursos não pode implantar esses recursos em paralelo.

## <a name="child-resources"></a>Recursos filho

Uma dependência de implantação implícita não é criada automaticamente entre um [recurso filho](child-resource-name-type.md) e o recurso pai. Se você precisar implantar o recurso filho após o recurso pai, defina a `dependsOn` propriedade.

O exemplo a seguir mostra um SQL Server e um banco de dados lógicos. Observe que uma dependência explícita é definida entre o banco de dados e o servidor, embora o banco de dados seja um filho do servidor.

```json
"resources": [
  {
    "type": "Microsoft.Sql/servers",
    "apiVersion": "2020-02-02-preview",
    "name": "[parameters('serverName')]",
    "location": "[parameters('location')]",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "type": "databases",
        "apiVersion": "2020-08-01-preview",
        "name": "[parameters('sqlDBName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
          },
        "dependsOn": [
          "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
        ]
      }
    ]
  }
]
```

Para o modelo completo, consulte [modelo de início rápido para o banco de dados SQL do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-sql-database/azuredeploy.json).

## <a name="reference-and-list-functions"></a>funções de referência e lista

A [função de referência](template-functions-resource.md#reference) permite que uma expressão derive seu valor de outro nome JSON e de pares de valor ou de recursos de runtime. O [lista * funções](template-functions-resource.md#list) retornar valores para um recurso de uma operação de lista.

Expressões de lista e referência implicitamente declaram que um recurso depende de outro. Sempre que possível, use uma referência implícita para evitar adicionar uma dependência desnecessária.

Para impor uma dependência implícita, consulte o recurso por nome, não ID do recurso. Se você passar a ID de recurso para as funções de referência ou lista, não será criada uma referência implícita.

O formato geral da `reference` função é:

```json
reference('resourceName').propertyPath
```

O formato geral da `listKeys` função é:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

No exemplo a seguir, um ponto de extremidade CDN depende explicitamente do perfil CDN e implicitamente de um aplicativo Web.

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

Para saber mais, consulte [Função de referência](template-functions-resource.md#reference).

## <a name="depend-on-resources-in-a-loop"></a>Depender dos recursos em um loop

Para implantar recursos que dependem de recursos em um [loop de cópia](copy-resources.md), você tem duas opções. Você pode definir uma dependência em recursos individuais no loop ou em todo o loop.

> [!NOTE]
> Para a maioria dos cenários, você deve definir a dependência em recursos individuais dentro do loop de cópia. Só dependa do loop inteiro quando precisar que todos os recursos no loop existam antes de criar o próximo recurso. Definir a dependência em todo o loop faz com que o grafo de dependências se expanda significativamente, especialmente se esses recursos de loops dependerem de outros recursos. As dependências expandidas dificultam a conclusão eficiente da implantação.

O exemplo a seguir mostra como implantar várias máquinas virtuais. O modelo cria o mesmo número de interfaces de rede. Cada máquina virtual depende de uma interface de rede, em vez de todo o loop.

```json
{
  "type": "Microsoft.Network/networkInterfaces",
  "apiVersion": "2020-05-01",
  "name": "[concat(variables('nicPrefix'),'-',copyIndex())]",
  "location": "[parameters('location')]",
  "copy": {
    "name": "nicCopy",
    "count": "[parameters('vmCount')]"
  },
  ...
},
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "name": "[concat(variables('vmPrefix'),copyIndex())]",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]"
  ],
  "copy": {
    "name": "vmCopy",
    "count": "[parameters('vmCount')]"
  },
  "properties": {
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]",
          "properties": {
            "primary": "true"
          }
        }
      ]
    },
    ...
  }
}
```

O exemplo a seguir mostra como implantar três contas de armazenamento antes de implantar a máquina virtual. Observe que o `copy` elemento foi `name` definido como `storagecopy` e o `dependsOn` elemento da máquina virtual também é definido como `storagecopy` .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
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
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="circular-dependencies"></a>Dependências circulares

O Resource Manager identifica dependências circulares durante a validação do modelo. Se você receber um erro para uma dependência circular, avalie o modelo para ver se alguma dependência pode ser removida. Se a remoção de dependências não funcionar, você poderá evitar dependências circulares movendo algumas operações de implantação para recursos filho. Implante os recursos filho após os recursos que têm a dependência circular. Por exemplo, suponha que você estiver implantando duas máquinas virtuais, mas você deve definir propriedades em cada um deles que se referem a outro. Você pode implantá-los na seguinte ordem:

1. vm1
2. vm2
3. Extensão na vm1 depende vm1 e vm2. A extensão define valores na vm1 que ele obtém da vm2.
4. Extensão da vm2 depende vm1 e vm2. A extensão define valores de vm2 obtido do vm1.

Para obter informações sobre como avaliar a ordem de implantação e resolver erros de dependência, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](common-deployment-errors.md).

## <a name="next-steps"></a>Próximas etapas

* Para percorrer um tutorial, consulte [tutorial: criar modelos de ARM com recursos dependentes](template-tutorial-create-templates-with-dependent-resources.md).
* Para obter um módulo do Microsoft Learn que aborda as dependências de recursos, confira [Gerenciar implantações de nuvem complexas usando recursos avançados do modelo do ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Para obter recomendações ao definir dependências, consulte [práticas recomendadas do modelo ARM](template-best-practices.md).
* Para saber mais sobre a solução de problemas de dependência durante a implantação, confira [Solucionar erros comuns de implantação do Azure com o Azure Resource Manager](common-deployment-errors.md).
* Para saber mais sobre como criar modelos de Azure Resource Manager, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
* Para obter uma lista das funções disponíveis em um modelo, consulte [funções de modelo do ARM](template-functions.md).
