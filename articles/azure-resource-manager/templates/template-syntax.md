---
title: Estrutura e sintaxe do modelo
description: Descreve a estrutura e as propriedades de modelos de Azure Resource Manager (modelos ARM) usando a sintaxe JSON declarativa.
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 698309c5aa0817c4b758ec81133d4c98061aa355
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653122"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>Noções básicas de estrutura e sintaxe dos modelos ARM

Este artigo descreve a estrutura de um modelo de Azure Resource Manager (modelo ARM). Ele apresenta as diferentes seções de um modelo e as propriedades que estão disponíveis nessas seções.

Este artigo destina-se a usuários que têm alguma familiaridade com modelos ARM. Ele fornece informações detalhadas sobre a estrutura do modelo. Para obter um tutorial passo a passo que orienta você durante o processo de criação de um modelo, confira [Tutorial: criar e implantar seu primeiro modelo do ARM](template-tutorial-create-first-template.md). Para saber mais sobre os modelos do ARM por meio de um conjunto guiado de módulos no Microsoft Learn, consulte [implantar e gerenciar recursos no Azure usando modelos do ARM](/learn/paths/deploy-manage-resource-manager-templates/).

## <a name="template-format"></a>Formato de modelo

Em sua estrutura mais simples, um modelo tem os seguintes elementos:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Nome do elemento | Obrigatório | Descrição |
|:--- |:--- |:--- |
| $schema |Yes |Local do arquivo de esquema JSON que descreve a versão da linguagem do modelo. O número de versão usada depende do escopo da implantação e do editor de JSON.<br><br>Se você estiver usando [vs Code com a extensão de ferramentas de Azure Resource Manager](quickstart-create-templates-use-visual-studio-code.md), use a versão mais recente para implantações de grupo de recursos:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Outros editores (incluindo o Visual Studio) podem não ser capazes de processar esse esquema. Para esses editores, use:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Para implantações de assinatura, use:<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>Para implantações de grupo de gerenciamento, use:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>Para implantações de locatário, use:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| contentVersion |Yes |Versão do modelo (como 1.0.0.0). Você pode fornecer qualquer valor para esse elemento. Use esse valor para documentar alterações significativas em seu modelo. Ao implantar recursos com o modelo, esse valor pode ser usado para garantir que o modelo certo esteja sendo usado. |
| apiProfile |No | Uma versão de API que serve como uma coleção de versões de API para tipos de recursos. Use esse valor para evitar a especificação de versões de API para cada recurso no modelo. Quando você especifica uma versão de perfil de API e não especifica uma versão de API para o tipo de recurso, o Resource Manager usa a versão de API para esse tipo de recurso que é definido no perfil.<br><br>A propriedade de perfil de API é especialmente útil ao implantar um modelo em ambientes diferentes, como Azure Stack e o Azure global. Use a versão do perfil de API para certificar-se de que o modelo usa automaticamente as versões com suporte em ambos os ambientes. Para obter uma lista das versões de perfil de API atuais e as versões de API de recursos definidas no perfil, consulte [perfil de API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Para obter mais informações, consulte [rastrear versões usando perfis de API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |No |Valores que são fornecidos quando a implantação é executada para personalizar a implantação dos recursos. |
| [variáveis](#variables) |No |Valores que são usados como fragmentos JSON no modelo para simplificar expressões de linguagem do modelo. |
| [funções](#functions) |No |Funções definidas pelo usuário que estão disponíveis no modelo. |
| [os](#resources) |Yes |Tipos de recursos que são implantados ou atualizados em um grupo de recursos ou assinatura. |
| [outputs](#outputs) |No |Valores que são retornados após a implantação. |

Cada elemento tem propriedades que você pode definir. Esse artigo descreve as seções do modelo com mais detalhes.

## <a name="data-types"></a>Tipos de dados

Em um modelo do ARM, você pode usar esses tipos de dados:

* string
* secureString
* INT
* bool
* object
* secureObject
* array

O modelo a seguir mostra o formato dos tipos de dados. Cada tipo tem um valor padrão no formato correto.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringParameter": {
      "type": "string",
      "defaultValue": "option 1"
    },
    "intParameter": {
      "type": "int",
      "defaultValue": 1
    },
    "boolParameter": {
        "type": "bool",
        "defaultValue": true
    },
    "objectParameter": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b"
      }
    },
    "arrayParameter": {
      "type": "array",
      "defaultValue": [ 1, 2, 3 ]
    }
  },
  "resources": [],
  "outputs": {}
}
```

A cadeia de caracteres segura usa o mesmo formato que a cadeia de caracteres, e o objeto seguro usa o mesmo formato que o objeto. Quando você define um parâmetro como uma cadeia de caracteres segura ou um objeto seguro, o valor do parâmetro não é salvo no histórico de implantação e não é registrado. No entanto, se você definir esse valor seguro para uma propriedade que não está esperando um valor seguro, o valor não será protegido. Por exemplo, se você definir uma cadeia de caracteres segura para uma marca, esse valor será armazenado como texto sem formatação. Use cadeias de caracteres seguras para senhas e segredos.

Para números inteiros passados como parâmetros embutidos, o intervalo de valores pode ser limitado pelo SDK ou pela ferramenta de linha de comando usada para implantação. Por exemplo, ao usar o PowerShell para implantar um modelo, os tipos de inteiros podem variar de-2147483648 a 2147483647. Para evitar essa limitação, especifique valores inteiros grandes em um [arquivo de parâmetro](parameter-files.md). Os tipos de recurso aplicam seus próprios limites para propriedades de inteiros.

Ao especificar valores Boolianos e inteiros em seu modelo, não coloque o valor entre aspas. Comece e termine valores de cadeia de caracteres com aspas duplas.

Os objetos começam com uma chave esquerda e terminam com uma chave direita. As matrizes começam com um colchete esquerdo e terminam com um colchete direito.

## <a name="parameters"></a>Parâmetros

Na seção de parâmetros do modelo, você especifica quais valores você pode inserir ao implantar os recursos. O limite de parâmetros em um modelo é 256. Você pode reduzir o número de parâmetros usando objetos que contêm várias propriedades.

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

| Nome do elemento | Obrigatório | Descrição |
|:--- |:--- |:--- |
| nome do parâmetro |Yes |Nome do parâmetro. Deve ser um identificador JavaScript válido. |
| tipo |Yes |Tipo do valor do parâmetro. Os valores e tipos permitidos são **cadeia de caracteres**, **securestring**, **int**, **bool**, **objeto**, **secureObject**, e **matriz**. Consulte [tipos de dados](#data-types). |
| defaultValue |No |Valor padrão do parâmetro, se nenhum valor for fornecido para o parâmetro. |
| allowedValues |No |Matriz de valores permitidos para o parâmetro para garantir que o valor correto seja fornecido. |
| minValue |No |O valor mínimo para parâmetros de tipo int, esse valor é inclusivo. |
| maxValue |No |O valor máximo para parâmetros de tipo int, esse valor é inclusivo. |
| minLength |No |O tamanho mínimo para parâmetros de tipo de matriz, cadeia de caracteres segura e cadeia de caracteres, esse valor é inclusivo. |
| maxLength |No |O tamanho máximo para parâmetros de tipo de matriz, cadeia de caracteres segura e cadeia de caracteres, esse valor é inclusivo. |
| descrição |No |Descrição do parâmetro exibido aos usuários pelo portal. Para obter mais informações, confira [Comentários em modelos](#comments). |

Para obter exemplos de como usar parâmetros, consulte [parâmetros em modelos ARM](template-parameters.md).

## <a name="variables"></a>Variáveis

Na seção de variáveis, você constrói valores que podem ser usados em todo o seu modelo. Você não precisa definir variáveis, mas normalmente elas simplificam seu modelo reduzindo expressões complexas. O formato de cada variável corresponde a um dos [tipos de dados](#data-types).

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

Para obter informações sobre como usar `copy` o para criar vários valores para uma variável, consulte [Iteration Variable](copy-variables.md).

Para obter exemplos de como usar variáveis, consulte [variáveis no modelo ARM](template-variables.md).

## <a name="functions"></a>Funções

Dentro de seu modelo, você pode criar suas próprias funções. Essas funções estão disponíveis para uso em seu modelo. Normalmente, você define expressões complicadas que não deseja repetir em todo o modelo. Você cria as funções definidas pelo usuário de expressões e [funções](template-functions.md) que têm suporte em modelos.

Ao definir uma função de usuário, há algumas restrições:

* A função não pode acessar variáveis.
* A função só pode usar os parâmetros que são definidos na função. Quando você usa a [função Parameters](template-functions-deployment.md#parameters) em uma função definida pelo usuário, você está restrito aos parâmetros para essa função.
* A função não pode chamar outras funções definidas pelo usuário.
* A função não pode usar a [função de referência](template-functions-resource.md#reference).
* Os parâmetros para a função não podem ter valores padrão.

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| Nome do elemento | Obrigatório | Descrição |
|:--- |:--- |:--- |
| namespace |Yes |Namespace para as funções personalizadas. Use para evitar conflitos de nomenclatura com funções de modelo. |
| nome da função |Yes |Nome da função personalizada. Ao chamar a função, combine o nome da função com o namespace. Por exemplo, para chamar uma função chamada uniqueName no namespace contoso, use `"[contoso.uniqueName()]"` . |
| nome do parâmetro |No |Nome do parâmetro a ser usado na função personalizada. |
| valor do parâmetro |No |Tipo do valor do parâmetro. Os valores e tipos permitidos são **cadeia de caracteres**, **securestring**, **int**, **bool**, **objeto**, **secureObject**, e **matriz**. |
| tipo de saída |Yes |Tipo do valor de saída. Os valores de saída dão suporte aos mesmos tipos de parâmetros de entrada de função. |
| saída-valor |Yes |Expressão de linguagem do modelo que é avaliada e retornada da função. |

Para obter exemplos de como usar funções personalizadas, consulte [funções definidas pelo usuário no modelo ARM](template-user-defined-functions.md).

## <a name="resources"></a>Recursos

Na seção de recursos, você define os recursos que são implantados ou atualizados.

Você define recursos com a seguinte estrutura:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
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
| condition | No | Valor booliano que indica se o recurso será provisionado durante esta implantação. Quando for `true`, o recurso será criado durante a implantação. Quando `false`, o recurso será ignorado para essa implantação. Consulte a [condição](conditional-resource-deployment.md). |
| tipo |Yes |Tipo do recurso. Esse valor é uma combinação do namespace do provedor de recursos e do tipo de recurso (como **Microsoft. Storage/storageAccounts**). Para determinar os valores disponíveis, consulte [referência de modelo](/azure/templates/). Para um recurso filho, o formato do tipo depende de se ele está aninhado dentro do recurso pai ou definido fora do recurso pai. Confira [Definir o nome e o tipo de recursos filho](child-resource-name-type.md). |
| apiVersion |Sim |Versão da API REST a ser usada para criar o recurso. Ao criar um novo modelo, defina esse valor para a versão mais recente do recurso que você está implantando. Desde que o modelo funcione conforme necessário, continue usando a mesma versão de API. Ao continuar a usar a mesma versão de API, você minimiza o risco de uma nova versão de API alterar a forma como o modelo funciona. Considere atualizar a versão da API somente quando desejar usar um novo recurso que é introduzido em uma versão posterior. Para determinar os valores disponíveis, consulte [referência de modelo](/azure/templates/). |
| name |Sim |Nome do recurso. O nome deve seguir as restrições de componente URI definidas em RFC3986. Os serviços do Azure que expõem o nome do recurso a partes externas validam o nome para verificar se não há uma tentativa de falsificar outra identidade. Para um recurso filho, o formato do nome depende se ele está aninhado dentro do recurso pai ou definido fora do recurso pai. Confira [Definir o nome e o tipo de recursos filho](child-resource-name-type.md). |
| comments |No |Suas anotações para documentar os recursos no modelo. Para obter mais informações, confira [Comentários em modelos](template-syntax.md#comments). |
| local |Varia |Locais geográficos com suporte do recurso fornecido. Você pode selecionar qualquer uma das localizações disponíveis, mas geralmente faz sentido escolher um que esteja perto de seus usuários. Normalmente, também faz sentido colocar recursos que interagem entre si na mesma região. A maioria dos tipos de recurso exige um local, ao contrário de alguns deles (como uma atribuição de função). Consulte [definir local do recurso](resource-location.md). |
| dependsOn |No |Recursos que devem ser implantados antes deste recurso. O Gerenciador de Recursos avalia as dependências entre os recursos e os implanta na ordem correta. Quando os recursos não dependem uns dos outros, eles são implantados em paralelo. O valor pode ser uma lista separada por vírgulas de nomes de recursos ou identificadores exclusivos de recursos. Somente lista recursos que são implantados neste modelo. Os recursos que não são definidos neste modelo já devem existir. Evite adicionar dependências desnecessárias, pois elas podem reduzir sua implantação e criar dependências circulares. Para obter orientação sobre como definir dependências, consulte [definir a ordem de implantação de recursos em modelos ARM](define-resource-dependency.md). |
| marcas |Não |Marcas que são associadas ao recurso. Aplique marcas para organizar recursos logicamente em toda a sua assinatura. |
| sku | Não | Alguns recursos permitem que os valores definam a SKU para implantar. Por exemplo, você pode especificar o tipo de redundância para uma conta de armazenamento. |
| kind | No | Alguns recursos permitem que um valor defina o tipo de recurso que você implantar. Por exemplo, você pode especificar o tipo de Cosmos DB para criar. |
| copy |No |Se mais de uma instância for necessária, o número de recursos a serem criados. O modo padrão é paralelo. Especifica o modo serial quando você não deseja que todos os recursos sejam implantados ao mesmo tempo. Para obter mais informações, consulte [Criar várias instâncias de recursos no Azure Resource Manager](copy-resources.md). |
| plan | No | Alguns recursos permitem que um valor defina o plano para implantar. Por exemplo, você pode especificar a imagem do marketplace para uma máquina virtual. |
| properties |No |Definições de configuração específicas do recurso. Os valores para as propriedades são iguais aos valores que você fornece no corpo da solicitação para a operação da API REST (método PUT) para criar o recurso. Especifique também uma matriz de cópia para criar várias instâncias de uma propriedade. Para determinar os valores disponíveis, consulte [referência de modelo](/azure/templates/). |
| recursos |Não |Recursos filho que dependem do recurso que está sendo definido. Forneça apenas os tipos de recurso permitidos pelo esquema do recurso pai. A dependência do recurso pai não é implícita. Você deve definir explicitamente essa dependência. Confira [Definir o nome e o tipo de recursos filho](child-resource-name-type.md). |

## <a name="outputs"></a>Saídas

Na seção de saídas, você especifica valores que são retornados da implantação. Normalmente, você retorna valores de recursos que foram implantados.

O exemplo a seguir mostra a estrutura de uma definição de saída:

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| Nome do elemento | Obrigatório | Descrição |
|:--- |:--- |:--- |
| nome de saída |Yes |Nome do valor de saída. Deve ser um identificador JavaScript válido. |
| condition |No | Valor booliano que indica se esse valor de saída é retornado. Quando `true`, o valor é incluído na saída para a implantação. Quando `false`, o valor de saída é ignorado para esta implantação. Quando não especificado, o valor padrão é `true`. |
| tipo |Yes |Tipo do valor de saída. Valores de saída oferecem suporte aos mesmos tipos que os parâmetros de entrada do modelo. Se você especificar **SecureString** para o tipo de saída, o valor não será exibido no histórico de implantação e não poderá ser recuperado de outro modelo. Para usar um valor secreto em mais de um modelo, armazene o segredo em um Key Vault e referencie o segredo no arquivo de parâmetro. Para saber mais, confira [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](key-vault-parameter.md). |
| valor |No |Expressão de linguagem do modelo avaliada e retornada como valor de saída. Especifique o **valor** ou a **cópia**. |
| copy |No | Usado para retornar mais de um valor para uma saída. Especifique o **valor** ou a **cópia**. Para obter mais informações, consulte [Iteration de saída em modelos ARM](copy-outputs.md). |

Para obter exemplos de como usar saídas, consulte [saídas no modelo ARM](template-outputs.md).

<a id="comments"></a>

## <a name="comments-and-metadata"></a>Comentários e metadados

Você tem algumas opções para adicionar comentários e metadados ao seu modelo.

### <a name="comments"></a>Comentários

Para comentários embutidos, você pode usar o `//` ou o `/* ... */` , mas essa sintaxe não funciona com todas as ferramentas. Se você adicionar esse estilo de comentário, verifique se as ferramentas que você usa apoiam comentários JSON embutidos.

> [!NOTE]
> Para implantar modelos com comentários usando CLI do Azure com a versão 2.3.0 ou mais antiga, você deve usar a `--handle-extended-json-format` opção.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

No Visual Studio Code, a [extensão de ferramentas de Azure Resource Manager](quickstart-create-templates-use-visual-studio-code.md) pode detectar automaticamente um modelo de ARM e alterar o modo de linguagem. Se você vir **Azure Resource Manager modelo** no canto inferior direito de vs Code, poderá usar os comentários embutidos. Os comentários embutidos não são mais marcados como inválidos.

![Modo de modelo de Azure Resource Manager Visual Studio Code](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metadados

Você pode adicionar um objeto `metadata` praticamente em qualquer lugar em seu modelo. O Resource Manager ignora o objeto, mas seu editor de JSON pode avisar você de que a propriedade não é válida. No objeto, defina as propriedades necessárias.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

![Mostrar dica de parâmetro](./media/template-syntax/show-parameter-tip.png)

Para **recursos**, adicione um elemento `comments` ou um objeto de metadados. O exemplo a seguir mostra um elemento de comentários e um objeto de metadados.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
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

## <a name="multi-line-strings"></a>Cadeias de caracteres de várias linhas

Você pode dividir uma cadeia de caracteres em várias linhas. Por exemplo, consulte a propriedade Location e um dos comentários no exemplo de JSON a seguir.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Para implantar modelos com cadeias de caracteres de várias linhas usando CLI do Azure com a versão 2.3.0 ou mais antiga, você deve usar a `--handle-extended-json-format` opção.

## <a name="next-steps"></a>Próximas etapas

* Para exibir modelos completos para muitos tipos diferentes de soluções, consulte os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que você pode usar de dentro de um modelo, consulte [funções de modelo do ARM](template-functions.md).
* Para combinar vários modelos durante a implantação, consulte [usando modelos vinculados e aninhados ao implantar recursos do Azure](linked-templates.md).
* Para obter recomendações sobre como criar modelos, consulte [práticas recomendadas do modelo ARM](template-best-practices.md).
* Para obter respostas a perguntas comuns, consulte perguntas frequentes [sobre modelos de ARM](frequently-asked-questions.md).
