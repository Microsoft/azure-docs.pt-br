---
title: Detalhes da estrutura de definição de iniciativa
description: Descreve como as definições de iniciativa de política são usadas para agrupar definições de política para implantação em recursos do Azure em sua organização.
ms.date: 10/07/2020
ms.topic: conceptual
ms.openlocfilehash: 8f9c6146e1dde5b5a7f6595c61638319de60a82d
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876168"
---
# <a name="azure-policy-initiative-definition-structure"></a>Estrutura de definição da iniciativa Azure Policy

Iniciativas permitem que você agrupe várias definições de políticas relacionadas para simplificar atribuições e gerenciamento, pois você trabalha com um grupo como um único item. Por exemplo, você pode agrupar as definições de política de marcação relacionadas em uma única iniciativa. Em vez de atribuir cada política individualmente, você aplica a iniciativa.

Você usa JSON para criar uma definição de iniciativa de política. A definição da iniciativa de política contém elementos para:

- nome de exibição
- descrição
- metadata
- parameters
- definições de política
- grupos de políticas (essa propriedade faz parte do [recurso de conformidade regulatória (versão prévia)](./regulatory-compliance.md))

O exemplo a seguir ilustra como criar uma iniciativa para lidar com duas marcas: `costCenter` e `productName`. Ele usa duas políticas internas para aplicar o valor da marca padrão.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "metadata": {
            "version": "1.0.0",
            "category": "Tags"
        },
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

Azure Policys e padrões estão em exemplos de [Azure Policy](../samples/index.md).

## <a name="metadata"></a>Metadados

A `metadata` propriedade opcional armazena informações sobre a definição da iniciativa de política.
Os clientes podem definir quaisquer propriedades e valores úteis para sua organização no `metadata` . No entanto, há algumas propriedades _comuns_ usadas por Azure Policy e em interno.

### <a name="common-metadata-properties"></a>Propriedades de metadados comuns

- `version` (cadeia de caracteres): rastreia detalhes sobre a versão do conteúdo de uma definição de iniciativa de política.
- `category` (String): determina sob qual categoria em portal do Azure a definição de política é exibida.

  > [!NOTE]
  > Para uma iniciativa de [conformidade regulatória](./regulatory-compliance.md) , o `category` deve ser a **conformidade regulatória**.

- `preview` (booliano): sinalizador true ou false para se a definição da iniciativa de política for _Preview_.
- `deprecated` (booliano): sinalizador true ou false para se a definição da iniciativa de política tiver sido marcada como _preterida_.

> [!NOTE]
> O serviço Azure Policy usa as propriedades `version`, `preview` e `deprecated` para transmitir o nível de alteração para uma definição de política interna ou uma iniciativa e um estado. O formato de `version` é: `{Major}.{Minor}.{Patch}`. Estados específicos, como _preterido_ ou _versão prévia_, são acrescentados à propriedade `version` ou a outra propriedade como um **booliano**. Para obter mais informações sobre como Azure Policy versões internas, consulte [controle de versão interno](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md).

## <a name="parameters"></a>Parâmetros

Parâmetros ajudam a simplificar o gerenciamento de política, reduzindo o número de definições de política. Pense em parâmetros como os campos em um formulário – `name`, `address`, `city`, `state`. Esses parâmetros sempre permanecem iguais, porém, seus valores mudam com base no preenchimento individual do formulário.
Os parâmetros funcionam da mesma maneira ao criar iniciativas de política. Ao incluir parâmetros em uma definição de iniciativa de política, você pode reutilizar esse parâmetro nas políticas incluídas.

> [!NOTE]
> Depois que uma iniciativa é atribuída, os parâmetros de nível de Initative não podem ser alterados. Por conta disso, a recomendação é definir um **defaultValue** ao definir o parâmetro.

### <a name="parameter-properties"></a>Propriedades do parâmetro

Um parâmetro tem as seguintes propriedades que são usadas na definição da iniciativa de política:

- `name`: O nome do parâmetro. Usado pela função de implantação `parameters` dentro da regra de política. Para saber mais, confira [Usar o valor de parâmetro](#passing-a-parameter-value-to-a-policy-definition).
- `type`: determina se o parâmetro é uma **cadeia de caracteres**, uma **matriz**, um **objeto**, um **booliano**, um **inteiro**, um **float** ou um **datetime**.
- `metadata`: define as subpropriedades usadas principalmente pelo portal do Azure para exibição de informações simples:
  - `description`: a explicação de uso do parâmetro. Pode ser usado para fornecer exemplos de valores aceitáveis.
  - `displayName`: O nome amigável exibido no portal para o parâmetro.
  - `strongType`: (opcional) usado ao atribuir a definição de política por meio do portal. Fornece uma lista de reconhecimento de contexto. Para obter mais informações, confira [strongType](#strongtype).
- `defaultValue`: (opcional) define o valor do parâmetro em uma atribuição se não houver valor fornecido.
- `allowedValues`: (opcional) fornece uma matriz de valores que o parâmetro aceita durante a atribuição.

Por exemplo, você pode definir uma definição de iniciativa de política para limitar os locais de recursos nas várias definições de política incluídas. Um parâmetro para essa definição de iniciativa de política pode ser **allowedLocations**. O parâmetro estará disponível para cada definição de política incluída e definida durante a atribuição da iniciativa de política.

```json
"parameters": {
    "init_allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="passing-a-parameter-value-to-a-policy-definition"></a>Passando um valor de parâmetro para uma definição de política

Você declara quais parâmetros de iniciativa você passa para o que incluía definições de política na matriz [policyDefinitions](#policy-definitions) da definição de iniciativa. Embora o nome do parâmetro possa ser o mesmo, usar nomes diferentes nas iniciativas do que nas definições de política simplifica a legibilidade do código.

Por exemplo, o parâmetro de iniciativa de **init_allowedLocations** definido anteriormente pode ser passado para várias definições de política incluídas e seus parâmetros, **sql_locations** e **vm_locations**, desta forma:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

Este exemplo faz referência ao parâmetro **init_allowedLocations** que foi demonstrado nas [Propriedades do parâmetro](#parameter-properties).

### <a name="strongtype"></a>strongType

Na propriedade `metadata`, você pode usar **strongType** para fornecer uma lista de opções de seleção múltipla no portal do Azure. **strongType** pode ser um _tipo de recurso_ compatível ou um valor permitido. Para determinar se um _tipo de recurso_ é válido para **strongType**, use [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Há suporte para alguns tipos de recursos não retornados pelo **Get-AzResourceProvider**. Esses tipos de recursos são:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

Os valores de tipo não-recurso permitidos para **strongtype** são:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="policy-definitions"></a>Definições de política

A `policyDefinitions` parte da definição da iniciativa é uma _matriz_ da qual as definições de política existentes são incluídas na iniciativa. Conforme mencionado na [passagem de um valor de parâmetro para uma definição de política](#passing-a-parameter-value-to-a-policy-definition), essa propriedade é onde os [parâmetros de iniciativa](#parameters) são passados para a definição de política.

### <a name="policy-definition-properties"></a>Propriedades de definição de política

Cada elemento da _matriz_ que representa uma definição de política tem as seguintes propriedades:

- `policyDefinitionId` (String): a ID da definição de política personalizada ou interna a ser incluída.
- `policyDefinitionReferenceId` (cadeia de caracteres): um nome curto para a definição de política incluída.
- `parameters`: (Opcional) os pares de nome/valor para passar um parâmetro de iniciativa para a definição de política incluída como uma propriedade nessa definição de política. Para obter mais informações, confira [Parâmetros](#parameters).
- `groupNames` (matriz de cadeias de caracteres): (opcional) o grupo do qual a definição de política é membro. Para obter mais informações, consulte [grupos de políticas](#policy-definition-groups).

Aqui está um exemplo de `policyDefinitions` que tem duas definições de política incluídas que são passadas para o mesmo parâmetro de iniciativa:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

## <a name="policy-definition-groups"></a>Grupos de definição de política

As definições de política em uma definição de iniciativa podem ser agrupadas e categorizadas. O recurso de [conformidade regulatória](./regulatory-compliance.md) do Azure Policy (versão prévia) usa essa propriedade para agrupar definições em **domínios de conformidade**e **controles** . Essas informações são definidas na propriedade de `policyDefinitionGroups` _matriz_ . Detalhes adicionais de Agrupamento podem ser encontrados em um objeto **policyMetadata** criado pela Microsoft. Para obter informações, consulte [objetos de metadados](#metadata-objects).

### <a name="policy-definition-groups-parameters"></a>Parâmetros de grupos de definição de política

Cada elemento da _matriz_ em `policyDefinitionGroups` deve ter as duas propriedades a seguir:

- `name` (cadeia de caracteres) \[ obrigatório \] : o nome curto para o **grupo**. Em conformidade regulatória, o **controle**. O valor dessa propriedade é usado pelo `groupNames` no `policyDefinitions` .
- `category` (cadeia de caracteres): a hierarquia à qual o grupo pertence. Em conformidade regulatória, o **domínio de conformidade** do controle.
- `displayName` (String): o nome amigável para o **grupo** ou **controle**. Usado pelo portal.
- `description` (String): uma descrição do que o **grupo** ou **controle** cobre.
- `additionalMetadataId` (String): o local do objeto [policyMetadata](#metadata-objects) que tem detalhes adicionais sobre o domínio de **controle** e **conformidade**.

  > [!NOTE]
  > Os clientes podem apontar para um objeto [policyMetadata](#metadata-objects) existente. No entanto, esses objetos são _somente leitura_ e criados apenas pela Microsoft.

Um exemplo da `policyDefinitionGroups` propriedade da definição de iniciativa interna NIST é semelhante a esta:

```json
"policyDefinitionGroups": [
    {
        "name": "NIST_SP_800-53_R4_AC-1",
        "additionalMetadataId": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1"
    }
]
```

### <a name="metadata-objects"></a>Objetos de metadados

Os internos de conformidade regulatória criados pela Microsoft têm informações adicionais sobre cada controle.
Essas informações são:

- Exibido no portal do Azure na visão geral de um **controle** sobre uma iniciativa de conformidade regulatória.
- Disponível por meio da API REST. Consulte o `Microsoft.PolicyInsights` provedor de recursos e o [grupo de operações policyMetadata](/rest/api/policy-insights/policymetadata/getresource).
- Disponível por meio de CLI do Azure. Consulte o comando [AZ Policy Metadata](/cli/azure/policy/metadata) .

> [!IMPORTANT]
> Os objetos de metadados para conformidade regulatória são _somente leitura_ e não podem ser criados por clientes.

Os metadados de um agrupamento de política têm as seguintes informações no `properties` nó:

- `metadataId`: A **ID de controle** à qual o agrupamento está relacionado.
- `category` (obrigatório): o **domínio de conformidade** ao qual o **controle** pertence.
- `title` (obrigatório): o nome amigável da **ID de controle**.
- `owner` (obrigatório): identifica quem tem a responsabilidade pelo controle no Azure: _Customer_, _Microsoft_, _Shared_.
- `description`: Informações adicionais sobre o controle.
- `requirements`: Detalhes sobre a responsabilidade da implementação do controle.
- `additionalContentUrl`: Um link para obter mais informações sobre o controle. Essa propriedade é geralmente um link para a seção da documentação que aborda esse controle no padrão de conformidade.

Veja abaixo um exemplo do objeto **policyMetadata** . Este exemplo de metadados pertence ao controle _NIST SP 800-53 R4 AC-1_ .

```json
{
  "properties": {
    "metadataId": "NIST SP 800-53 R4 AC-1",
    "category": "Access Control",
    "title": "Access Control Policy and Procedures",
    "owner": "Shared",
    "description": "**The organization:**    \na. Develops, documents, and disseminates to [Assignment: organization-defined personnel or roles]:  \n1. An access control policy that addresses purpose, scope, roles, responsibilities, management commitment, coordination among organizational entities, and compliance; and  \n2. Procedures to facilitate the implementation of the access control policy and associated access controls; and  \n  
\nb. Reviews and updates the current:  \n1. Access control policy [Assignment: organization-defined frequency]; and  \n2. Access control procedures [Assignment: organization-defined frequency].",
    "requirements": "**a.**  The customer is responsible for developing, documenting, and disseminating access control policies and procedures. The customer access control policies and procedures address access to all customer-deployed resources and customer system access (e.g., access to customer-deployed virtual machines, access to customer-built applications).  \n**b.**  The customer is responsible for reviewing and updating access control policies and procedures in accordance with FedRAMP requirements.",
    "additionalContentUrl": "https://nvd.nist.gov/800-53/Rev4/control/AC-1"
  },
  "id": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1",
  "name": "NIST_SP_800-53_R4_AC-1",
  "type": "Microsoft.PolicyInsights/policyMetadata"
}
```

## <a name="next-steps"></a>Próximas etapas

- Ver a [estrutura de definição](./definition-structure.md)
- Examine os exemplos em [Amostras do Azure Policy](../samples/index.md).
- Revisar [Compreendendo os efeitos da política](effects.md).
- Entenda como [criar políticas de forma programática](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
