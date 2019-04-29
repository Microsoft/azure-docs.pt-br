---
title: Como criar o modelo do Azure Resource Manager
description: Descreve o processo de criação de um modelo do Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2018
ms.author: tomfitz
ms.openlocfilehash: b198f860c10bd207a191057e21a8a159c5ebde26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550959"
---
# <a name="create-azure-resource-manager-template"></a>Criar um modelo do Azure Resource Manager

Este artigo descreve o processo e as decisões que você toma ao criar um modelo do Azure Resource Manager. Ele fornece uma visão geral dos exemplos e recursos que podem ajudá-lo na criação de seu modelo. O artigo supõe que você está implantando recursos em um grupo de recursos. Se você precisar implantar recursos em sua assinatura do Azure, como criar Políticas do Azure ou atribuições de controle de acesso baseado em função, consulte [Criar grupos de recursos e recursos para uma assinatura do Azure](deploy-to-subscription.md).

## <a name="select-json-editor"></a>Selecionar editor de JSON

O modelo do Resource Manager é um arquivo JSON. Você precisa de uma boa ferramenta de criação para trabalhar no arquivo JSON. Você tem muitas opções, mas se ainda não tiver um editor de preferência, instale o [VS Code (Visual Studio Code)](https://code.visualstudio.com/). 

Depois de instalar o VS Code, adicione a [extensão Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Essa extensão adiciona muitos recursos que simplificam a criação do modelo.

![Modelo do Visual Studio Code](./media/how-to-create-template/template-visual-studio-code.png)

A captura de tela mostra um modelo do Resource Manager aberto no Visual Studio Code. 

Para obter um tutorial de instalação da extensão de ferramentas do Resource Manager e saber como usar o VS Code, consulte [Início Rápido: Criar modelos do Azure Resource Manager usando o Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md).

## <a name="understand-the-template-structure"></a>Reconhecer a estrutura do modelo

Vamos examinar as partes do modelo para entender como funciona o modelo. O modelo pode não ter todas as seções. As seções que você quer enfocar são:

* A seção [parâmetros](resource-group-authoring-templates.md#parameters), que mostra os valores que você pode especificar durante a implantação para personalizar a infraestrutura implantada. 

* A seção [variáveis](resource-group-authoring-templates.md#variables), que mostra os valores usados em todo o modelo.

* A seção [funções](resource-group-authoring-templates.md#functions), que mostra expressões de modelo personalizadas usadas no modelo.

* A seção [recursos](resource-group-authoring-templates.md#resources), que mostra os recursos do Azure que são implantados em sua assinatura.

* A seção [saídas](resource-group-authoring-templates.md#outputs), que mostra os valores retornados após a conclusão da implantação.

## <a name="look-for-similar-templates"></a>Buscar modelos similares

Normalmente, é possível encontrar um modelo existente que implanta uma solução semelhante à que você precisa. Os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/) têm centenas de modelos de colaboradores da comunidade.

![Repositório de Início Rápido de Modelos](./media/how-to-create-template/template-quickstart-repo.png)

Pesquise nesse repositório um modelo que é semelhante ao que você precisa. Está tudo certo se o modelo fizer exatamente o que você precisa, pois você pode personalizá-lo.

Depois de localizar um modelo, selecione **Procurar no Github** e, em seguida, copie o arquivo **azuredeploy.json** do repositório. Em VS Code, crie um novo arquivo nomeado **azuredeploy.json** e adicione o conteúdo do arquivo de modelo que você copiou do repositório de Início Rápido.

## <a name="add-resources"></a>Adicionar recursos

Você provavelmente desejará personalizar o modelo para garantir que ele faça exatamente o que você quer. Primeiro, examine os recursos que estão implantados. Talvez seja necessário adicionar, remover ou alterar recursos no modelo. Para obter descrições e sintaxe dos recursos, consulte [Referência de modelo do Azure Resource Manager](/azure/templates/).

![Referência de modelo](./media/how-to-create-template/template-reference.png)

Depois de revisar essas propriedades, faça todas as alterações necessárias. Para obter recomendações sobre como definir recursos, consulte [recursos - práticas recomendadas](template-best-practices.md#resources).

## <a name="add-or-remove-parameters"></a>Adicionar ou remover parâmetros

Talvez ainda seja necessário ajustar os parâmetros para o modelo. É possível adicionar ou remover parâmetros com base na quantidade de personalização que deseja habilitar durante a implantação. Para obter recomendações sobre como usar parâmetros, consulte [parâmetros - práticas recomendadas](template-best-practices.md#parameters).

## <a name="add-tags"></a>Adicionar marcas

É possível adicionar marcas aos seus recursos para organizar logicamente por categorias e dividir os custos de cobrança. É fácil adicionar marcas, aplicando-as no JSON para o recurso. Por exemplo, a conta de armazenamento a seguir tem duas marcas:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
        "apiVersion": "2016-01-01",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[concat('storage', uniqueString(resourceGroup().id))]",
        "location": "[resourceGroup().location]",
        "tags": {
          "Dept": "Finance",
          "Environment": "Production"
        },
        "sku": {
          "name": "Standard_LRS"
        },
        "kind": "Storage",
        "properties": { }
      }
    ]
}
```

Também é possível aplicar marcas dinamicamente a partir de parâmetros. Para obter mais informações, consulte [marcas no modelo](resource-group-using-tags.md#templates).

## <a name="review-template-functions"></a>Examinar as funções do modelo

É possível observar expressões no modelo que estão entre colchetes, como `"[some-expression]"`. Essas expressões usam funções de modelo para construir dinamicamente valores durante a implantação.

Por exemplo, você geralmente verá uma expressão como:

```json
"name": "[parameters('siteName')]"
```

Essa expressão obtém o valor de um parâmetro. O valor é atribuído à propriedade name.

Ou, será possível ver uma expressão mais complexa que usa várias funções, como:

```json
"[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Essa expressão obtém um objeto com as propriedades de uma conta de armazenamento.

Para entender o que as funções fazem, examine a documentação [referência de função de modelo](resource-group-template-functions.md).

## <a name="create-more-than-one-instance"></a>Criar mais de uma instância

Às vezes você quer criar mais de uma instância de um recurso. Por exemplo, talvez sejam necessárias diversas contas de armazenamento. Em vez de repetir o recurso por meio do modelo, é possível usar a sintaxe `copy` para especificar mais de uma instância.

O exemplo a seguir cria três contas de armazenamento:

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

Também é possível especificar o número de instâncias dinamicamente a partir de um parâmetro. Para obter mais informações, consulte [Implantar mais de uma instância de um recurso ou propriedade em modelos do Azure Resource Manager](resource-group-create-multiple.md).

## <a name="conditionally-deploy-a-resource"></a>Implantar condicionalmente um recurso

Às vezes, é necessário especificar durante a implantação se um recurso no modelo é implantado. Por exemplo, você pode querer a flexibilidade para implantar um novo recurso ou usar um recurso existente. O elemento `condition` proporciona a capacidade de ativar ou desativar a implantação de um recurso. Quando a expressão no elemento de condição for true, o recurso será implantado. Quando for false, o recurso será ignorado durante a implantação.

O exemplo a seguir implanta condicionalmente uma conta de armazenamento:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Para obter mais informações, consulte o [elemento condicional](resource-group-authoring-templates.md#condition).

## <a name="review-dependencies"></a>Examinar dependências

Alguns recursos no modelo devem ser implantados antes de outros recursos. Por exemplo, o SQL server deve existir antes que o banco de dados SQL seja criado. O Resource Manager implicitamente determina a ordem de implantação para recursos quando a [função de referência](resource-group-template-functions-resource.md#reference) é usada. No entanto, em alguns casos, será necessário definir explicitamente as dependências usando o elemento `dependsOn`. Examine o modelo para ver se todas as dependências precisam ser adicionadas. Tenha cuidado para não adicionar dependências desnecessárias, pois eles podem reduzir a velocidade de implantação ou criar referências circulares.

A imagem a seguir mostra a ordem de dependência para vários recursos do Serviço de Aplicativo:

![Dependências do aplicativo Web](./media/how-to-create-template/web-dependencies.png)

O exemplo a seguir mostra parte de um modelo que define dependências.

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Para obter mais informações, consulte [Definir a ordem de implantação dos recursos em modelos do Azure Resource Manager](resource-group-define-dependencies.md).

## <a name="review-recommended-practices"></a>Examinar as práticas recomendadas

Antes de implantar seu modelo, examine as [Melhores práticas de modelo do Azure Resource Manager](template-best-practices.md) para ver se há alguma abordagem recomendada que você queira implementar no modelo.

Se você precisar usar o modelo em ambientes de nuvem do Azure diferente, consulte [Desenvolver modelos do Azure Resource Manager para consistência da nuvem](templates-cloud-consistency.md).

## <a name="next-steps"></a>Próximas etapas

* Para implantar um modelo, consulte [Implantar com a CLI do Azure](resource-group-template-deploy-cli.md) ou [Implantar com o PowerShell](resource-group-template-deploy.md).
* Para ver um início rápido passo a passo sobre como criar um modelo, consulte [Criar modelos do Azure Resource Manager usando o Visual Studio Code](resource-manager-quickstart-create-templates-use-visual-studio-code.md).
* Para obter uma lista das funções disponíveis em um modelo, consulte [Funções de modelo](resource-group-template-functions.md).
