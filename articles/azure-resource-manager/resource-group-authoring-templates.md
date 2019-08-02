---
title: Estrutura e sintaxe do modelo do Azure Resource Manager | Microsoft Docs
description: Descreve a estrutura e as propriedades dos modelos do Azure Resource Manager usando a sintaxe JSON declarativa.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tomfitz
ms.openlocfilehash: ab8e4f5f6506f80b62c112298f73f95bc7fedeaf
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204354"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager

Este artigo descreve a estrutura de um modelo do Azure Resource Manager. Ele apresenta as diferentes seções de um modelo e as propriedades que estão disponíveis nessas seções. O modelo consiste em JSON e expressões que podem ser usados na criação de valores para sua implantação.

Este artigo destina-se para os usuários que têm alguma familiaridade com modelos do Resource Manager. Ele fornece informações detalhadas sobre a estrutura e sintaxe do modelo. Se você quiser uma introdução à criação de um modelo, consulte [criar seu primeiro modelo do Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Formato de modelo

Em sua estrutura mais simples, um modelo tem os seguintes elementos:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Nome do elemento | Obrigatório | DESCRIÇÃO |
|:--- |:--- |:--- |
| $schema |Sim |Local do arquivo de esquema JSON que descreve a versão da linguagem do modelo.<br><br> Para implantações de grupo de recursos, use: `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Para implantações de assinatura, use: `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Sim |Versão do modelo (como 1.0.0.0). Você pode fornecer qualquer valor para esse elemento. Use esse valor para documentar alterações significativas em seu modelo. Ao implantar recursos com o modelo, esse valor pode ser usado para garantir que o modelo certo esteja sendo usado. |
| apiProfile |Não | Uma versão de API que serve como uma coleção de versões de API para tipos de recursos. Use esse valor para evitar ter que especificar as versões de API para cada recurso no modelo. Quando você especificar uma versão de perfil de API e não especifica uma versão de API para o tipo de recurso, o Gerenciador de recursos usa a versão da API para esse tipo de recurso que é definido no perfil.<br><br>A propriedade de perfil de API é especialmente útil ao implantar um modelo em ambientes diferentes, como o Azure Stack e o Azure global. Use a versão de perfil de API para verificar se o modelo automaticamente usa as versões que têm suporte em ambos os ambientes. Para obter uma lista de versões atuais do perfil de API e os recursos definidos no perfil de versões de API, consulte [perfil de API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Para obter mais informações, consulte [controlar versões usando perfis de API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Não |Valores que são fornecidos quando a implantação é executada para personalizar a implantação dos recursos. |
| [variables](#variables) |Não |Valores que são usados como fragmentos JSON no modelo para simplificar expressões de linguagem do modelo. |
| [functions](#functions) |Não |Funções definidas pelo usuário que estão disponíveis no modelo. |
| [resources](#resources) |Sim |Tipos de recursos que são implantados ou atualizados em um grupo de recursos ou assinatura. |
| [outputs](#outputs) |Não |Valores que são retornados após a implantação. |

Cada elemento tem propriedades que você pode definir. Esse artigo descreve as seções do modelo com mais detalhes.

## <a name="syntax"></a>Sintaxe

A sintaxe básica do modelo é JSON. No entanto, você pode usar expressões para estender os valores JSON disponíveis dentro do modelo.  As expressões começam e terminam com colchetes: `[` e `]`, respectivamente. O valor da expressão é avaliado quando o modelo é implantado. Uma expressão pode retornar uma cadeia de caracteres, inteiro, booliano, matriz ou objeto. O exemplo a seguir mostra uma expressão no valor padrão de um parâmetro:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Dentro da expressão, a sintaxe `resourceGroup()` chama uma das funções que o Gerenciador de recursos fornece para uso dentro de um modelo. Assim como no JavaScript, as chamadas de função são formatadas como `functionName(arg1,arg2,arg3)`. A sintaxe `.location` recupera uma propriedade do objeto retornado por essa função.

As funções do modelo e seus parâmetros não diferenciam maiúsculas de minúsculas. Por exemplo, o Resource Manager resolve **variables('var1')** e **VARIABLES('VAR1')** da mesma forma. Quando avaliada, a função preservará as maiúsculas e minúsculas, a menos que a função modifique-as expressamente (como toUpper ou toLower). Determinados tipos de recursos podem ter requisitos de maiúsculas e minúsculas independentemente de como as funções são avaliadas.

Para ter uma cadeia de caracteres literal a iniciar com um colchete esquerdo `[` e terminando com um colchete direito `]`, mas não é interpretada como uma expressão, adicione um colchete extra para iniciar a cadeia de caracteres com `[[`. Por exemplo, a variável:

```json
"demoVar1": "[[test value]"
```

Resolve para `[test value]`.

No entanto, se a cadeia de caracteres literal não termina com um colchete, não escapa primeiro colchete. Por exemplo, a variável:

```json
"demoVar2": "[test] value"
```

Resolve para `[test] value`.

Para passar um valor de cadeia de caracteres como um parâmetro para uma função, use aspas simples.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

Para sair de aspas duplas em uma expressão, como a adição de um objeto JSON no modelo, use a barra invertida.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Uma expressão de modelo não pode exceder 24.576 caracteres.

Para obter a lista completa das funções de modelo, veja [Funções de modelo do Gerenciador de Recursos do Azure](resource-group-template-functions.md). 

## <a name="parameters"></a>parâmetros

Na seção de parâmetros do modelo, você deve especificar os valores que você pode inserir ao implantar os recursos. Esses valores de parâmetro permitem personalizar a implantação fornecendo valores que são personalizados para um determinado ambiente (como desenvolvimento, teste e produção). Você não precisa fornecer parâmetros em seu modelo, mas sem parâmetros o modelo sempre implantaria os mesmos recursos com o mesmo nomes, locais e propriedades.

Você está limitado a 256 parâmetros em um modelo. Você pode reduzir o número de parâmetros por meio de objetos que contenham várias propriedades, como é mostrado neste artigo.

### <a name="available-properties"></a>Propriedades disponíveis

As propriedades disponíveis para um parâmetro são:

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

| Nome do elemento | Obrigatório | DESCRIÇÃO |
|:--- |:--- |:--- |
| parameterName |Sim |Nome do parâmetro. Deve ser um identificador JavaScript válido. |
| type |Sim |Tipo do valor do parâmetro. Os valores e tipos permitidos são **cadeia de caracteres**, **securestring**, **int**, **bool**, **objeto**, **secureObject**, e **matriz**. |
| defaultValue |Não |Valor padrão do parâmetro, se nenhum valor for fornecido para o parâmetro. |
| allowedValues |Não |Matriz de valores permitidos para o parâmetro para garantir que o valor correto seja fornecido. |
| minValue |Não |O valor mínimo para parâmetros de tipo int, esse valor é inclusivo. |
| maxValue |Não |O valor máximo para parâmetros de tipo int, esse valor é inclusivo. |
| minLength |Não |O tamanho mínimo para parâmetros de tipo de matriz, cadeia de caracteres segura e cadeia de caracteres, esse valor é inclusivo. |
| maxLength |Não |O tamanho máximo para parâmetros de tipo de matriz, cadeia de caracteres segura e cadeia de caracteres, esse valor é inclusivo. |
| description |Não |Descrição do parâmetro exibido aos usuários pelo portal. Para obter mais informações, confira [Comentários em modelos](#comments). |

### <a name="define-and-use-a-parameter"></a>Definir e usar um parâmetro

O exemplo a seguir mostra uma definição de parâmetro simples. Ele define o nome do parâmetro e especifica que leva um valor de cadeia de caracteres. O parâmetro aceita apenas valores que fazem sentido para o uso pretendido. Especifica um valor padrão quando nenhum valor é fornecido durante a implantação. Por fim, o parâmetro inclui uma descrição de seu uso.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

No modelo, você referencia o valor para o parâmetro com a seguinte sintaxe:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

### <a name="template-functions-with-parameters"></a>Funções com parâmetros de modelo

Ao especificar o valor padrão para um parâmetro, você pode usar a maioria das funções de modelo. Você pode usar outro valor de parâmetro para criar um valor padrão. O modelo a seguir demonstra o uso de funções no valor padrão:

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Não é possível usar a função `reference` na seção de parâmetros. Os parâmetros são avaliados antes da implantação, portanto, a função `reference` não pode obter o estado de tempo de execução de um recurso. 

### <a name="objects-as-parameters"></a>Objetos como parâmetros

Pode ser mais fácil organizar valores relacionados, passando-os como um objeto. Essa abordagem também reduz o número de parâmetros no modelo.

Defina o parâmetro no modelo e especifique um objeto JSON em vez de um único valor durante a implantação. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Em seguida, faça referência às subpropriedades do parâmetro usando o operador ponto.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

### <a name="parameter-example-templates"></a>Modelos de exemplo de parâmetro

Esses modelos de exemplo demonstram alguns cenários de uso de parâmetros. Implante-os para testar como os parâmetros são tratados em cenários diferentes.

|Modelo  |DESCRIÇÃO  |
|---------|---------|
|[parâmetros com funções de valores padrão](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Demonstra como usar funções de modelo ao definir valores padrão para parâmetros. O modelo não implanta todos os recursos. Ele cria valores de parâmetro e retorna os valores. |
|[objeto de parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Demonstra como usar um objeto para um parâmetro. O modelo não implanta todos os recursos. Ele cria valores de parâmetro e retorna os valores. |

## <a name="variables"></a>variáveis

Na seção de variáveis, você constrói valores que podem ser usados em todo o seu modelo. Você não precisa definir variáveis, mas normalmente elas simplificam seu modelo reduzindo expressões complexas.

### <a name="available-definitions"></a>Definições disponíveis

O exemplo a seguir mostra as opções disponíveis para definir uma variável:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": { 
    <variable-complex-type-value> 
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

Para obter informações sobre como usar `copy` para criar vários valores para uma variável, consulte [iteração de variável](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Definir e usar uma variável

O seguinte exemplo mostra uma definição de variável. Ele cria um valor de cadeia de caracteres para um nome de conta de armazenamento. Ele usa várias funções de modelo para obter um valor de parâmetro e concatena-o para uma cadeia de caracteres exclusiva.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Você usa a variável ao definir o recurso.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>Variáveis de configuração

Você pode usar tipos JSON complexos a fim de definir valores relacionados para um ambiente.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

Em parâmetros, você cria um valor que indica quais valores de configuração devem ser usados.

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
```

Você recupera as configurações atuais com:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>Modelos de exemplo da variável

Esses modelos de exemplo demonstram alguns cenários de uso de variáveis. Implante-os para testar como as variáveis são tratadas em cenários diferentes. 

|Modelo  |DESCRIÇÃO  |
|---------|---------|
| [definições disponíveis](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Demonstra os diferentes tipos de variáveis. O modelo não implanta todos os recursos. Ele cria valores de variável e retorna os valores. |
| [variável de configuração](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Demonstra o uso de uma variável que define os valores de configuração. O modelo não implanta todos os recursos. Ele cria valores de variável e retorna os valores. |
| [regras de segurança de rede](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e [arquivo de parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Cria uma matriz no formato correto para a atribuição de regras de segurança a um grupo de segurança de rede. |


## <a name="functions"></a>Funções

Dentro de seu modelo, você pode criar suas próprias funções. Essas funções estão disponíveis para uso em seu modelo. Normalmente, você define a expressão complexa que não deseja repetir em todo o seu modelo. Você cria as funções definidas pelo usuário de expressões e [funções](resource-group-template-functions.md) que têm suporte em modelos.

Ao definir uma função de usuário, há algumas restrições:

* A função não pode acessar variáveis.
* A função só pode usar os parâmetros que são definidos na função. Quando você usa o [função parâmetros](resource-group-template-functions-deployment.md#parameters) dentro de uma função definida pelo usuário, você está restrito aos parâmetros para essa função.
* A função não pode chamar outras funções definidas pelo usuário.
* A função não pode usar a [função de referência](resource-group-template-functions-resource.md#reference).
* Os parâmetros para a função não podem ter valores padrão.

As suas funções exigem um valor de namespace para evitar conflitos de nomenclatura com funções de modelo. O exemplo a seguir mostra uma função que retorna um nome de conta de armazenamento:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

Chame a função com:

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>Recursos
Na seção de recursos, você define os recursos que são implantados ou atualizados.

### <a name="available-properties"></a>Propriedades disponíveis

Você define recursos com a seguinte estrutura:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
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
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
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
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nome do elemento | Obrigatório | DESCRIÇÃO |
|:--- |:--- |:--- |
| condition | Não | Valor booliano que indica se o recurso será provisionado durante esta implantação. Quando for `true`, o recurso será criado durante a implantação. Quando `false`, o recurso será ignorado para essa implantação. Ver [condição](#condition). |
| apiVersion |Sim |Versão da API REST a ser usada para criar o recurso. Para determinar os valores disponíveis, consulte [referência de modelo](/azure/templates/). |
| type |Sim |Tipo do recurso. Esse valor é uma combinação do namespace do provedor de recursos e do tipo de recurso (como **Microsoft.Storage/storageAccounts**). Para determinar os valores disponíveis, consulte [referência de modelo](/azure/templates/). Para um recurso filho, o formato do tipo depende de se ele tem aninhados no recurso pai ou definido fora do recurso pai. Ver [recursos filho](#child-resources). |
| name |Sim |Nome do recurso. O nome deve seguir as restrições de componente URI definidas em RFC3986. Além disso, os serviços do Azure que expõem o nome do recurso a terceiros validam o nome para garantir que ele não é uma tentativa de falsificar outra identidade. Para um recurso filho, o formato do nome depende se ele tem aninhados no recurso pai ou definido fora do recurso pai. Ver [recursos filho](#child-resources). |
| location |Varia |Locais geográficos com suporte do recurso fornecido. Você pode selecionar qualquer uma das localizações disponíveis, mas geralmente faz sentido escolher um que esteja perto de seus usuários. Normalmente, também faz sentido colocar recursos que interagem entre si na mesma região. A maioria dos tipos de recurso exige um local, ao contrário de alguns deles (como uma atribuição de função). |
| marcas |Não |Marcas que são associadas ao recurso. Aplique marcas para organizar recursos logicamente em toda a sua assinatura. |
| comentários |Não |Suas anotações para documentar os recursos no modelo. Para obter mais informações, confira [Comentários em modelos](resource-group-authoring-templates.md#comments). |
| cópia |Não |Se mais de uma instância for necessária, o número de recursos a serem criados. O modo padrão é paralelo. Especifica o modo serial quando você não deseja que todos os recursos sejam implantados ao mesmo tempo. Para obter mais informações, consulte [Criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Não |Recursos que devem ser implantados antes deste recurso. O Gerenciador de Recursos avalia as dependências entre os recursos e os implanta na ordem correta. Quando os recursos não dependem uns dos outros, eles são implantados em paralelo. O valor pode ser uma lista separada por vírgulas de nomes de recursos ou identificadores exclusivos de recursos. Somente lista recursos que são implantados neste modelo. Os recursos que não são definidos neste modelo já devem existir. Evite adicionar dependências desnecessárias, pois elas podem reduzir sua implantação e criar dependências circulares. Para obter orientação sobre como configurar as dependências, confira [Definir as dependências nos modelos do Azure Resource Manager](resource-group-define-dependencies.md). |
| propriedades |Não |Definições de configuração específicas do recurso. Os valores para as propriedades são iguais aos valores que você fornece no corpo da solicitação para a operação da API REST (método PUT) para criar o recurso. Especifique também uma matriz de cópia para criar várias instâncias de uma propriedade. Para determinar os valores disponíveis, consulte [referência de modelo](/azure/templates/). |
| sku | Não | Alguns recursos permitem que os valores definam a SKU para implantar. Por exemplo, você pode especificar o tipo de redundância para uma conta de armazenamento. |
| kind | Não | Alguns recursos permitem que um valor defina o tipo de recurso que você implantar. Por exemplo, você pode especificar o tipo de Cosmos DB para criar. |
| plan | Não | Alguns recursos permitem que um valor defina o plano para implantar. Por exemplo, você pode especificar a imagem do marketplace para uma máquina virtual. | 
| recursos |Não |Recursos filho que dependem do recurso que está sendo definido. Forneça apenas os tipos de recurso permitidos pelo esquema do recurso pai. A dependência do recurso pai não é implícita. Você deve definir explicitamente essa dependência. Ver [recursos filho](#child-resources). |

### <a name="condition"></a>Condição

Quando você deve decidir durante a implantação se é necessário criar um recurso, use o `condition` elemento. O valor desse elemento é resolvido como verdadeiro ou falso. Quando o valor for true, o recurso será criado. Quando o valor for false, o recurso não será criado. O valor só pode ser aplicado para o recurso inteiro.

Normalmente, você usa esse valor quando deseja criar um novo recurso ou usar um existente. Por exemplo, para especificar se uma nova conta de armazenamento é implantada ou uma conta de armazenamento existente é usada, use:

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

Para obter um modelo de exemplo completo que usa o elemento `condition`, confira [VM com uma Rede Virtual nova ou existente, um Armazenamento e um IP público](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

Se você usar um [referência](resource-group-template-functions-resource.md#reference) ou [lista](resource-group-template-functions-resource.md#list) função com um recurso que condicionalmente é implantado, a função é avaliada, mesmo se o recurso não está implantado. Você obterá um erro se a função se refere a um recurso que não existe. Use o [se](resource-group-template-functions-logical.md#if) função para garantir que a função é avaliada apenas para condições quando o recurso é implantado. Consulte a [se função](resource-group-template-functions-logical.md#if) para um modelo de exemplo que usa se e uma referência com um recurso condicionalmente implantado.

### <a name="resource-names"></a>Nomes de recurso

Normalmente, você trabalha com três tipos de nome de recurso no Resource Manager:

* Os nomes de recurso devem ser exclusivos.
* Nomes de recursos que não precisam ser exclusivos, mas você opta por fornecer um nome que pode ajudá-lo a identificar o recurso.
* Nomes de recursos que podem ser genéricos.

Fornecer um **nome de recurso exclusivo** para qualquer tipo de recurso que tem um ponto de extremidade de acesso de dados. Entre os tipos comuns de recurso que exigem um nome exclusivo estão:

* Armazenamento do Azure<sup>1</sup> 
* Recurso de Aplicativos Web do Serviço de Aplicativo do Azure
* SQL Server
* Cofre da Chave do Azure
* Cache Redis do Azure
* Lote do Azure
* Gerenciador de Tráfego do Azure
* Azure Search
* Azure HDInsight

<sup>1</sup> Os nomes de conta de armazenamento devem estar em letras minúsculas, ter até 24 caracteres e não incluir hifens.

Ao configurar o nome, é possível criar manualmente um nome exclusivo ou usar a função [uniqueString()](resource-group-template-functions-string.md#uniquestring) para gerar um nome. Também convém adicionar um prefixo ou sufixo ao resultado **uniqueString**. Modificar o nome exclusivo pode ajudar você a identificar mais facilmente o tipo de recurso pelo nome. Por exemplo, você pode gerar um nome exclusivo para uma conta de armazenamento usando a seguinte variável:

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

Para alguns tipos de recurso, você talvez queira fornecer uma **nome para identificação**, mas o nome não precisa ser exclusivo. Para esses tipos de recursos, forneça um nome que o descreve o uso ou características.

```json
"parameters": {
  "vmName": { 
    "type": "string",
    "defaultValue": "demoLinuxVM",
    "metadata": {
      "description": "The name of the VM to create."
    }
  }
}
```

Para tipos de recursos que você principalmente o acesso por meio de um recurso diferente, você pode usar um **nome genérico** que é embutido no modelo. Por exemplo, você pode definir um nome genérico padrão para regras de firewall em um servidor SQL:

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

### <a name="resource-location"></a>Local do recurso

Ao implantar um modelo, é necessário fornecer um local para cada recurso. Tipos de recursos diferentes têm suporte em locais diferentes. Para obter os locais com suporte para um tipo de recurso, consulte [provedores de recursos e tipos do Azure](resource-manager-supported-services.md).

Use um parâmetro para especificar o local de recursos e defina o valor padrão para `resourceGroup().location`.

O exemplo a seguir mostra uma conta de armazenamento implantada a um local especificado como um parâmetro:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

### <a name="child-resources"></a>Recursos filho

Em alguns tipos de recursos, também é possível definir uma matriz de recursos filho. Os recursos filho são recursos que somente existem no contexto de outro recurso. Por exemplo, um banco de dados SQL não pode existir sem um servidor SQL para que o banco de dados seja filho do servidor. É possível definir o banco de dados dentro da definição para o servidor.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "exampleserver",
  ...
  "resources": [
    {
      "apiVersion": "2017-10-01-preview",
      "type": "databases",
      "name": "exampledatabase",
      ...
    }
  ]
}
```

Porém, não é necessário definir o banco de dados dentro do servidor. Você pode definir o recurso filho no nível superior. Você pode usar essa abordagem se o recurso pai não estiver implantado no mesmo modelo ou se desejar usar `copy` para criar mais de um recurso filho. Com essa abordagem, forneça o tipo de recurso completo e inclua o nome do recurso pai no nome do recurso filho.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "exampleserver",
  "resources": [ 
  ],
  ...
},
{
  "apiVersion": "2017-10-01-preview",
  "type": "Microsoft.Sql/servers/databases",
  "name": "exampleserver/exampledatabase",
  ...
}
```

Os valores que você fornecer para o nome e tipo variam com base em se o recurso filho é definido no recurso pai ou fora do recurso pai.

Quando aninhados no recurso pai, use:

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

Quando definidos fora o recurso pai, use:

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

Quando aninhados, o tipo é definido como `databases` , mas seu tipo de recurso completo ainda é `Microsoft.Sql/servers/databases`. `Microsoft.Sql/servers/` não é fornecido porque ele é presumido do tipo de recurso pai. O nome do recurso filho é definido como `exampledatabase` , mas o nome completo inclui o nome do pai. `exampleserver` não é fornecido porque ele é presumido do recurso pai.

Ao construir uma referência totalmente qualificada a um recurso, a ordem para combinação dos segmentos do tipo e do nome não é simplesmente uma concatenação dos dois. Em vez disso, após o namespace, use uma sequência de pares *tipo/nome* do menos específico para o mais específico:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Por exemplo:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`está correto, `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` não está correto

## <a name="outputs"></a>outputs

Na seção de saídas, você especifica valores que são retornados da implantação. Normalmente, você retorna valores de recursos que foram implantados.

### <a name="available-properties"></a>Propriedades disponíveis

O exemplo a seguir mostra a estrutura de uma definição de saída:

```json
"outputs": {
  "<outputName>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Nome do elemento | Obrigatório | DESCRIÇÃO |
|:--- |:--- |:--- |
| outputName |Sim |Nome do valor de saída. Deve ser um identificador JavaScript válido. |
| condition |Não | Valor booliano que indica se esse valor de saída é retornado. Quando `true`, o valor é incluído na saída para a implantação. Quando `false`, o valor de saída é ignorado para esta implantação. Quando não especificado, o valor padrão é `true`. |
| type |Sim |Tipo do valor de saída. Valores de saída oferecem suporte aos mesmos tipos que os parâmetros de entrada do modelo. Se você especificar **securestring** para o tipo de saída, o valor não for exibido no histórico de implantação e não podem ser recuperado de outro modelo. Para usar um valor do segredo em mais de um modelo, armazenar o segredo em um cofre de chaves e referenciar o segredo no arquivo de parâmetros. Para obter mais informações, consulte [usar o Azure Key Vault para passar o valor de parâmetro seguro durante a implantação](resource-manager-keyvault-parameter.md). |
| value |Sim |Expressão de linguagem do modelo avaliada e retornada como valor de saída. |

### <a name="define-and-use-output-values"></a>Definir e usar valores de saída

O exemplo a seguir mostra como retornar a ID de recurso para um endereço IP público:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

O próximo exemplo mostra como retornar condicionalmente a ID de recurso para um endereço IP público com base no fato de um novo ter sido implantado:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Para obter um exemplo simples de saída condicional, consulte [modelo de saída condicional](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

Após a implantação, você pode recuperar o valor com script. Para o PowerShell, use:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Para a CLI do Azure, use:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Você pode recuperar o valor de saída de um modelo vinculado usando a função [reference](resource-group-template-functions-resource.md#reference). Para obter um valor de saída de um modelo vinculado, recupere o valor da propriedade com uma sintaxe semelhante a: `"[reference('deploymentName').outputs.propertyName.value]"`.

Ao obter uma propriedade de saída de um modelo vinculado, o nome da propriedade não pode incluir um traço.

O exemplo a seguir mostra como definir o endereço IP de um balanceador de carga recuperando um valor de um modelo vinculado.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Não é possível usar a `reference` função na seção de saídas de um [modelo aninhado](resource-group-linked-templates.md#link-or-nest-a-template). Para retornar os valores de um recurso implantado em um modelo aninhado, converta seu modelo aninhado em um modelo vinculado.

### <a name="output-example-templates"></a>Modelos de exemplo de saída

|Modelo  |DESCRIÇÃO  |
|---------|---------|
|[Variáveis de cópia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Cria variáveis complexas e gera os valores. Ele não implanta nenhum recurso. |
|[Endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Cria um endereço IP público e gera a ID de recurso. |
|[Balanceador de carga](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Links para o modelo anterior. Usa a ID do recurso na saída ao criar o balanceador de carga. |


<a id="comments" />

## <a name="comments-and-metadata"></a>Comentários e metadados

Você tem algumas opções para adicionar comentários e metadados ao seu modelo.

Você pode adicionar um objeto `metadata` praticamente em qualquer lugar em seu modelo. O Resource Manager ignora o objeto, mas seu editor de JSON pode avisar você de que a propriedade não é válida. No objeto, defina as propriedades necessárias.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Para **Parâmetros**, adicione um objeto `metadata` com uma propriedade `description`.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Ao implantar o modelo por meio do portal, o texto que você fornece na descrição é usado automaticamente como uma dica para esse parâmetro.

![Mostrar dica de parâmetro](./media/resource-group-authoring-templates/show-parameter-tip.png)

Para **recursos**, adicione um elemento `comments` ou um objeto de metadados. O exemplo a seguir mostra um elemento de comentários e um objeto de metadados.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

Para **saídas**, adicione um objeto de metadados ao valor de saída.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

Você não pode adicionar um objeto de metadados a funções definidas pelo usuário.

Para comentários embutidos, você pode usar `//`, mas essa sintaxe não funciona com todas as ferramentas. Você não pode usar a CLI do Azure para implantar o modelo com comentários embutidos. E você não pode usar o editor de modelo do portal para trabalhar em modelos com comentários embutidos. Se você adicionar esse estilo de comentário, verifique se as ferramentas que você usa apoiam comentários JSON embutidos.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

No VS Code, você pode definir o modo de linguagem para JSON com comentários. Os comentários embutidos não são mais marcados como inválidos. Para alterar o modo:

1. Abra a seleção do modo de linguagem (Ctrl+K M)

1. Selecione **JSON com Comentários**.

   ![Selecione o modo de idioma](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Próximas etapas
* Para exibir modelos completos para muitos tipos diferentes de soluções, consulte os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que podem ser usadas em um modelo, consulte [Funções do Modelo do Azure Resource Manager](resource-group-template-functions.md).
* Para combinar vários modelos durante a implantação, confira [Como usar modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para obter recomendações sobre a criação de modelos, consulte[Melhores práticas para modelo do Azure Resource Manager](template-best-practices.md).
* Para obter recomendações sobre a criação de modelos do Resource Managers que podem ser usados em todos os ambientes do Azure e do Azure Stack, veja [Desenvolver modelos do Azure Resource Manager para consistência de nuvem](templates-cloud-consistency.md).
