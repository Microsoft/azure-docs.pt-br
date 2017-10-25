---
title: Estrutura e sintaxe do modelo do Azure Resource Manager | Microsoft Docs
description: Descreve a estrutura e as propriedades dos modelos do Azure Resource Manager usando a sintaxe JSON declarativa.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2017
ms.author: tomfitz
ms.openlocfilehash: dc9b64062d7f68c83aa090eec96744819a5ca423
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager
Este tópico descreve a estrutura de um modelo do Azure Resource Manager. Ele apresenta as diferentes seções de um modelo e as propriedades que estão disponíveis nessas seções. O modelo consiste em JSON e expressões que podem ser usados na criação de valores para sua implantação. Para ver um tutorial passo a passo sobre como criar um modelo, confira [Criar seu primeiro modelo do Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Formato de modelo
Em sua estrutura mais simples, um modelo contém os seguintes elementos:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| Nome do elemento | Obrigatório | Descrição |
|:--- |:--- |:--- |
| $schema |Sim |Local do arquivo de esquema JSON que descreve a versão da linguagem do modelo. Use a URL mostrada no exemplo anterior. |
| contentVersion |Sim |Versão do modelo (como 1.0.0.0). Você pode fornecer qualquer valor para esse elemento. Ao implantar recursos com o modelo, esse valor pode ser usado para garantir que o modelo certo esteja sendo usado. |
| parameters |Não |Valores que são fornecidos quando a implantação é executada para personalizar a implantação dos recursos. |
| variáveis |Não |Valores que são usados como fragmentos JSON no modelo para simplificar expressões de linguagem do modelo. |
| recursos |Sim |Tipos de recursos que são implantados ou atualizados em um grupo de recursos. |
| outputs |Não |Valores que são retornados após a implantação. |

Cada elemento contém propriedades que você pode definir. O seguinte exemplo contém a sintaxe completa de um modelo:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  
        "<parameter-name>" : {
            "type" : "<type-of-parameter-value>",
            "defaultValue": "<default-value-of-parameter>",
            "allowedValues": [ "<array-of-allowed-values>" ],
            "minValue": <minimum-value-for-int>,
            "maxValue": <maximum-value-for-int>,
            "minLength": <minimum-length-for-string-or-array>,
            "maxLength": <maximum-length-for-string-or-array-parameters>,
            "metadata": {
                "description": "<description-of-the parameter>" 
            }
        }
    },
    "variables": {  
        "<variable-name>": "<variable-value>",
        "<variable-name>": { 
            <variable-complex-type-value> 
        }
    },
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": {
              "<tag-name1>": "<tag-value1>",
              "<tag-name2>": "<tag-value2>"
          },
          "comments": "<your-reference-notes>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
          },
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": {
              "<settings-for-the-resource>",
              "copy": [
                  {
                      "name": ,
                      "count": ,
                      "input": {}
                  }
              ]
          },
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

Examinaremos as seções do modelo em detalhes mais adiante neste tópico.

## <a name="expressions-and-functions"></a>Expressões e funções
A sintaxe básica do modelo é JSON. No entanto, as expressões e as funções estendem os valores JSON disponíveis no modelo.  As expressões são escritas em literais de cadeia de caracteres JSON cujo primeiro e último caracteres são os colchetes: `[` e `]`, respectivamente. O valor da expressão é avaliado quando o modelo é implantado. Embora gravado como um literal de cadeia de caracteres, o resultado da avaliação da expressão pode ser de um tipo JSON diferente, como uma matriz ou um inteiro, dependendo da expressão real.  Para ter uma cadeia de caracteres literal que começa com um colchete `[`, mas que não é interpretada como uma expressão, adicione um colchete extra para iniciar a cadeia de caracteres com `[[`.

Normalmente, você usa expressões com funções para executar operações e configurar a implantação. Assim como no JavaScript, as chamadas de função são formatadas como `functionName(arg1,arg2,arg3)`. Você faz referência às propriedades usando os operadores dot e [index].

O seguinte exemplo mostra como usar várias das funções ao construir valores:

```json
"variables": {
    "location": "[resourceGroup().location]",
    "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
    "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

Para obter a lista completa das funções de modelo, veja [Funções de modelo do Gerenciador de Recursos do Azure](resource-group-template-functions.md). 

## <a name="parameters"></a>parameters
Na seção de parâmetros do modelo, você deve especificar os valores que você pode inserir ao implantar os recursos. Esses valores de parâmetro permitem personalizar a implantação fornecendo valores que são personalizados para um determinado ambiente (como desenvolvimento, teste e produção). Você não precisa fornecer parâmetros em seu modelo, mas sem parâmetros o modelo sempre implantaria os mesmos recursos com o mesmo nomes, locais e propriedades.

Você define parâmetros com a seguinte estrutura:

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": "<default-value-of-parameter>",
        "allowedValues": [ "<array-of-allowed-values>" ],
        "minValue": <minimum-value-for-int>,
        "maxValue": <maximum-value-for-int>,
        "minLength": <minimum-length-for-string-or-array>,
        "maxLength": <maximum-length-for-string-or-array-parameters>,
        "metadata": {
            "description": "<description-of-the parameter>" 
        }
    }
}
```

| Nome do elemento | Obrigatório | Descrição |
|:--- |:--- |:--- |
| parameterName |Sim |Nome do parâmetro. Deve ser um identificador JavaScript válido. |
| type |Sim |Tipo do valor do parâmetro. Consulte a lista de tipos permitidos após esta tabela. |
| defaultValue |Não |Valor padrão do parâmetro, se nenhum valor for fornecido para o parâmetro. |
| allowedValues |Não |Matriz de valores permitidos para o parâmetro para garantir que o valor correto seja fornecido. |
| minValue |Não |O valor mínimo para parâmetros de tipo int, esse valor é inclusivo. |
| maxValue |Não |O valor máximo para parâmetros de tipo int, esse valor é inclusivo. |
| minLength |Não |O comprimento mínimo para cadeia, secureString e parâmetros do tipo de matriz, esse valor é inclusivo. |
| maxLength |Não |O comprimento máximo para cadeia, secureString e parâmetros do tipo de matriz, esse valor é inclusivo. |
| Descrição |Não |Descrição do parâmetro exibido aos usuários pelo portal. |

Os valores e tipos permitidos são:

* **string**
* **secureString**
* **int**
* **bool**
* **object** 
* **secureObject**
* **array**

Para especificar um parâmetro como opcional, forneça um defaultValue (pode ser uma cadeia de caracteres vazia). 

Se você especificar um nome de parâmetro em seu modelo que corresponda a um parâmetro no comando de implantação do modelo, haverá uma possível ambiguidade nos valores fornecidos. O Resource Manager resolve essa confusão adicionando o sufixo **FromTemplate** ao parâmetro do modelo. Por exemplo, se você incluir um parâmetro chamado **ResourceGroupName** em seu modelo, ele entrará em conflito com o parâmetro **ResourceGroupName** no cmdlet [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment). Durante a implantação, você recebe uma solicitação para fornecer um valor para **ResourceGroupNameFromTemplate**. Em geral, você deve evitar essa confusão não dando aos parâmetros o mesmo nome dos parâmetros usados para operações de implantação.

> [!NOTE]
> Todas as senhas, chaves e outros segredos devem usar o tipo **secureString** . Se você passar dados confidenciais em um objeto JSON, use o tipo **secureObject**. Os parâmetros de modelo com o tipo secureString ou secureObject não podem ser lidos após a implantação de recursos. 
> 
> Por exemplo, a seguinte entrada no histórico de implantação mostra o valor para uma cadeia de caracteres e objeto, mas não para secureString e secureObject.
>
> ![mostrar valores de implantação](./media/resource-group-authoring-templates/show-parameters.png)  
>

O seguinte exemplo mostra como definir parâmetros:

```json
"parameters": {
    "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
    },
    "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
    },
    "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
    },
    "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
    }
}
```

Para saber como inserir os valores do parâmetro durante a implantação, consulte [Implantar um aplicativo com o modelo do Azure Resource Manager](resource-group-template-deploy.md). 

## <a name="variables"></a>variáveis
Na seção de variáveis, você constrói valores que podem ser usados em todo o seu modelo. Você não precisa definir variáveis, mas normalmente elas simplificam seu modelo reduzindo expressões complexas.

Você define variáveis com a seguinte estrutura:

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    }
}
```

O seguinte exemplo mostra como definir uma variável que é construída com base em dois valores de parâmetro:

```json
"variables": {
    "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
}
```

O próximo exemplo mostra uma variável que é um tipo JSON complexo e variáveis que são construídas com base em outras variáveis:

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
"variables": {
    "environmentSettings": {
        "test": {
            "instancesSize": "Small",
            "instancesCount": 1
        },
        "prod": {
            "instancesSize": "Large",
            "instancesCount": 4
        }
    },
    "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
    "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
    "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
}
```

## <a name="resources"></a>recursos
Na seção de recursos, você define os recursos que são implantados ou atualizados. Essa seção pode ficar mais complicada, porque você precisa entender os tipos que está implantando para fornecer os valores corretos. Para obter os valores específicos de recurso (apiVersion, tipo e propriedades) que é preciso definir, confira [Define resources in Azure Resource Manager templates](/azure/templates/) (Definir recursos nos modelos do Azure Resource Manager). 

Você define recursos com a seguinte estrutura:

```json
"resources": [
  {
      "condition": "<boolean-value-whether-to-deploy>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": "<number-of-iterations>",
          "mode": "<serial-or-parallel>",
          "batchSize": "<number-to-deploy-serially>"
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nome do elemento | Obrigatório | Descrição |
|:--- |:--- |:--- |
| condition | Não | Valor booliano que indica se o recurso está implantado. |
| apiVersion |Sim |Versão da API REST a ser usada para criar o recurso. |
| type |Sim |Tipo do recurso. Esse valor é uma combinação do namespace do provedor de recursos e do tipo de recurso (como **Microsoft.Storage/storageAccounts**). |
| name |Sim |Nome do recurso. O nome deve seguir as restrições de componente URI definidas em RFC3986. Além disso, os serviços do Azure que expõem o nome do recurso a terceiros validam o nome para garantir que ele não é uma tentativa de falsificar outra identidade. |
| location |Varia |Locais geográficos com suporte do recurso fornecido. Você pode selecionar qualquer uma das localizações disponíveis, mas geralmente faz sentido escolher um que esteja perto de seus usuários. Normalmente, também faz sentido colocar recursos que interagem entre si na mesma região. A maioria dos tipos de recursos exige um local, mas alguns deles (como uma atribuição de função) não. Confira [Configure os locais dos recursos de modelos do Azure Resource Manager](resource-manager-template-location.md). |
| marcas |Não |Marcas que são associadas ao recurso. Confira [Marque recursos em modelos do Azure Resource Manager](resource-manager-template-tags.md). |
| comentários |Não |Suas anotações para documentar os recursos em seu modelo |
| cópia |Não |Se mais de uma instância for necessária, o número de recursos a serem criados. O modo padrão é paralelo. Especifica o modo serial, quando você não deseja que sejam todos ou os recursos para implantação ao mesmo tempo. Para obter mais informações, consulte [Criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Não |Recursos que devem ser implantados antes deste recurso. O Gerenciador de Recursos avalia as dependências entre os recursos e os implanta na ordem correta. Quando os recursos não dependem uns dos outros, são implantados paralelamente. O valor pode ser uma lista separada por vírgulas de nomes de recursos ou identificadores exclusivos de recursos. Somente lista recursos que são implantados neste modelo. Recursos que não são definidos neste modelo já devem existir. Evite adicionar dependências desnecessárias, pois elas podem reduzir sua implantação e criar dependências circulares. Para obter orientação sobre como configurar as dependências, confira [Definir as dependências nos modelos do Azure Resource Manager](resource-group-define-dependencies.md). |
| propriedades |Não |Definições de configuração específicas do recurso. Os valores para as propriedades são iguais aos valores que você fornece no corpo da solicitação para a operação da API REST (método PUT) para criar o recurso. Você também pode especificar uma matriz de cópia para criar várias instâncias de uma propriedade. Para obter mais informações, consulte [Criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md). |
| recursos |Não |Recursos filho que dependem do recurso que está sendo definido. Forneça apenas os tipos de recurso permitidos pelo esquema do recurso pai. O tipo totalmente qualificado do recurso filho inclui o tipo de recurso pai, como **Microsoft.Web/sites/extensions**. A dependência do recurso pai não é implícita. Você deve definir explicitamente essa dependência. |

A seção de recursos contém uma matriz dos recursos a serem implantados. Em cada recurso, você também pode definir uma matriz de recursos filhos. Portanto, a seção de recursos pode ter uma estrutura como:

```json
"resources": [
  {
      "name": "resourceA",
  },
  {
      "name": "resourceB",
      "resources": [
        {
            "name": "firstChildResourceB",
        },
        {   
            "name": "secondChildResourceB",
        }
      ]
  },
  {
      "name": "resourceC",
  }
]
```      

Para saber mais sobre como definir recursos filho, confira [Definir o nome e o tipo do recurso filho no modelo do Resource Manager](resource-manager-template-child-resource.md).

O elemento **condição** especifica se o recurso foi implantado. O valor desse elemento é resolvido como verdadeiro ou falso. Por exemplo, para especificar se uma nova conta de armazenamento é implantada, use:

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

Para obter um exemplo do uso de um recurso novo ou existente, consulte [Modelo de condição novo ou existente](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResources.NewOrExisting.json).

Para especificar se uma máquina virtual foi implantada com uma senha ou chave SSH, definir duas versões da máquina virtual em seu modelo e usar **condição** para diferenciar o uso. Passe um parâmetro que especifica qual cenário de implantação.

```json
{
    "condition": "[equals(parameters('passwordOrSshKey'),'password')]",
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('vmName'),'password')]",
    "properties": {
        "osProfile": {
            "computerName": "[variables('vmName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
        },
        ...
    },
    ...
},
{
    "condition": "[equals(parameters('passwordOrSshKey'),'sshKey')]",
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('vmName'),'ssh')]",
    "properties": {
        "osProfile": {
            "linuxConfiguration": {
                "disablePasswordAuthentication": "true",
                "ssh": {
                    "publicKeys": [
                        {
                            "path": "[variables('sshKeyPath')]",
                            "keyData": "[parameters('adminSshKey')]"
                        }
                    ]
                }
            }
        },
        ...
    },
    ...
}
``` 

Para obter um exemplo do uso de uma senha ou chave SSH para implantar a máquina virtual, consulte [Modelo de condição de nome de usuário ou o SSH](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResourcesUsernameOrSsh.json).

## <a name="outputs"></a>Saídas
Na seção de saídas, você especifica valores que são retornados da implantação. Por exemplo, é possível retornar o URI para acessar um recurso implantado.

O exemplo a seguir mostra a estrutura de uma definição de saída:

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Nome do elemento | Obrigatório | Descrição |
|:--- |:--- |:--- |
| outputName |Sim |Nome do valor de saída. Deve ser um identificador JavaScript válido. |
| type |Sim |Tipo do valor de saída. Valores de saída oferecem suporte aos mesmos tipos que os parâmetros de entrada do modelo. |
| value |Sim |Expressão de linguagem do modelo avaliada e retornada como valor de saída. |

O exemplo a seguir mostra um valor que é retornado na seção de saídas.

```json
"outputs": {
    "siteUri" : {
        "type" : "string",
        "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
    }
}
```

Para obter mais informações sobre como trabalhar com a saída, consulte [Compartilhando o estado nos modelos do Azure Resource Manager](best-practices-resource-manager-state.md).

## <a name="template-limits"></a>Limites de modelo

Limite o tamanho de seu modelo em 1 MB e cada arquivo de parâmetro em 64 KB. O limite de 1 MB se aplica para o estado final do modelo depois que ele foi expandido com definições de recurso iterativo e valores para variáveis e parâmetros. 

Você também está limitado a:

* 256 parâmetros
* 256 variáveis
* 800 recursos (incluindo a contagem de cópias)
* 64 valores de saída
* 24.576 caracteres em uma expressão de modelo

Você pode exceder alguns limites de modelo usando um modelo aninhado. Para saber mais, confira [Uso de modelos vinculados ao implantar recursos do Azure](resource-group-linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, você pode combinar vários valores em um objeto. Para saber mais, veja [Objetos como parâmetros](resource-manager-objects-as-parameters.md).

## <a name="next-steps"></a>Próximas etapas
* Para exibir modelos completos para muitos tipos diferentes de soluções, consulte os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que podem ser usadas em um modelo, consulte [Funções do Modelo do Azure Resource Manager](resource-group-template-functions.md).
* Para combinar vários modelos durante a implantação, consulte [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
* Talvez seja necessário usar recursos que existam em um grupo de recursos diferente. Essa situação é comum ao trabalhar com contas de armazenamento ou redes virtuais compartilhadas com vários grupos de recursos. Para obter mais informações, consulte a [função resourceId](resource-group-template-functions-resource.md#resourceid).
