---
title: Definir a ordem de implantação dos recursos do Azure | Microsoft Docs
description: Descreve como definir um recurso como dependente de outro recurso durante a implantação para garantir que os recursos sejam implantados na ordem correta.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: tomfitz
ms.openlocfilehash: 32b2b41e47fe089da70d82e6049d0139795df88a
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204226"
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Definir a ordem de implantação dos recursos em modelos do Azure Resource Manager
Para um determinado recurso, pode ser necessário que existam outros recursos antes que o recurso em questão seja implantado. Por exemplo, um SQL Server deve existir antes que você tente implantar um Banco de Dados SQL. Você define essa relação marcando um recurso como dependente do outro. Defina uma dependência com o elemento **dependsOn** ou usando a função **reference**. 

O Gerenciador de Recursos avalia as dependências entre os recursos e os implanta na ordem de dependência. Quando os recursos não dependem uns dos outros, o Gerenciador de Recursos os implanta paralelamente. Você só precisa definir as dependências para recursos que são implantados no mesmo modelo. 

Para um tutorial, consulte [Tutorial: crie modelos do Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md).

## <a name="dependson"></a>dependsOn
No seu modelo, o elemento dependsOn permite definir um recurso como um dependente em um ou mais recursos. Seu valor pode ser uma lista de nomes de recurso separados por vírgula. 

O exemplo a seguir mostra um conjunto de escala de máquina virtual que depende de um balanceador de carga, de uma rede virtual e de um loop que cria várias contas de armazenamento. Esses outros recursos não são mostrados no exemplo a seguir, mas precisam ser existe em outro lugar no modelo.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "apiVersion": "2016-03-30",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

No exemplo anterior, uma dependência foi incluída nos recursos criados por meio de um loop de cópia chamado **storageLoop**. Por exemplo, consulte [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md)

Ao definir dependências, você pode incluir o namespace do provedor de recurso e o tipo de recurso para evitar ambiguidade. Por exemplo, para esclarecer um balanceador de carga e uma rede virtual que possam ter os mesmos nomes que outros recursos, use o seguinte formato:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
``` 

Embora você talvez queira usar o dependsOn para mapear as relações entre os seus recursos, é importante entender por que você está fazendo isso. Por exemplo, para documentar a maneira como os recursos são interconectados, dependsOn não é a abordagem certa. Não é possível consultar os recursos que foram definidos no elemento dependsOn após a implantação. Usando o dependsOn, potencialmente afetar o tempo de implantação porque o Resource Manager - Gerenciador de recursos não implantar em paralelos dois recursos que têm uma dependência. 

## <a name="child-resources"></a>Recursos filho
A propriedade resources permite especificar os recursos filho relacionados ao recurso que está sendo definido. Os recursos filho só podem ser definidos em cinco níveis de profundidade. É importante observar que uma dependência implícita de implantação não é criada entre um recurso filho e o recurso pai. Se precisar que o recurso filho seja implantado após o recurso pai, você deve declarar explicitamente essa dependência com a propriedade dependsOn. 

Cada recurso pai aceita somente determinados tipos de recurso como recursos filho. Os tipos de recurso aceitos são especificados no [esquema do modelo](https://github.com/Azure/azure-resource-manager-schemas) do recurso pai. O nome do tipo de recurso de filho inclui o nome do tipo de recurso pai, assim como **Microsoft.Web/sites/config** e **Microsoft.Web/sites/extensions** são ambos recursos filho do **Microsoft.Web/sites**.

O exemplo a seguir mostra um SQL Server e um Banco de Dados SQL. Observe que uma dependência explícita é definida entre o Banco de Dados SQL e o SQL Server, ainda que o banco de dados seja um filho do servidor.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "apiVersion": "2014-04-01-preview",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "Database"
        },
        "apiVersion": "2014-04-01-preview",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>funções de referência e lista
A [função de referência](resource-group-template-functions-resource.md#reference) permite que uma expressão derive seu valor de outro nome JSON e de pares de valor ou de recursos de tempo de execução. O [lista * funções](resource-group-template-functions-resource.md#list) retornar valores para um recurso de uma operação de lista.  Expressões de referência e lista implicitamente declarar que um recurso depende de outro, quando o recurso referenciado é implantado no mesmo modelo e referenciado por seu nome (não a ID de recurso). Se você passar a ID de recurso para as funções de referência ou lista, não será criada uma referência implícita.

O formato geral da função de referência é:

```json
reference('resourceName').propertyPath
```

O formato geral de função do listKeys é:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

No exemplo a seguir, um ponto de extremidade CDN depende explicitamente do perfil CDN e implicitamente de um aplicativo Web.

```json
{
    "name": "[variables('endpointName')]",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "apiVersion": "2016-04-02",
    "dependsOn": [
            "[variables('profileName')]"
    ],
    "properties": {
        "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
        ...
    }
```

Você pode usar esse elemento ou o elemento dependsOn para especificar dependências, mas não é necessário usar ambos para o mesmo recurso dependente. Sempre que possível, use uma referência implícita para evitar adicionar uma dependência desnecessária.

Para saber mais, consulte [Função de referência](resource-group-template-functions-resource.md#reference).

## <a name="circular-dependencies"></a>Dependências circulares

O Resource Manager identifica dependências circulares durante a validação do modelo. Se você receber um erro indicando que existe uma dependência circular, avalie o modelo para ver se qualquer dependência não é necessários e pode ser removida. Se remover dependências não funcionar, você pode evitar dependências circulares ao mover algumas operações de implantação em recursos filho que são implantados após os recursos que possuem a dependência circular. Por exemplo, suponha que você estiver implantando duas máquinas virtuais, mas você deve definir propriedades em cada um deles que se referem a outro. Você pode implantá-los na seguinte ordem:

1. vm1
2. vm2
3. Extensão na vm1 depende vm1 e vm2. A extensão define valores na vm1 que ele obtém da vm2.
4. Extensão da vm2 depende vm1 e vm2. A extensão define valores de vm2 obtido do vm1.

Para obter informações sobre como avaliar a ordem de implantação e resolver erros de dependência, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>Próximas etapas

* Para passar por um tutorial, consulte [Tutorial: crie modelos do Azure Resource Manager com recursos dependentes ](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
* Para ver recomendações sobre de configuração de dependências, confira [Melhores práticas para modelos do Azure Resource Manager](template-best-practices.md).
* Para saber mais sobre a solução de problemas de dependência durante a implantação, confira [Solucionar erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para saber mais sobre a criação de modelos do Gerenciador de Recursos do Azure, consulte [Criando modelos](resource-group-authoring-templates.md). 
* Para obter uma lista das funções disponíveis em um modelo, consulte [Funções de modelo](resource-group-template-functions.md).

