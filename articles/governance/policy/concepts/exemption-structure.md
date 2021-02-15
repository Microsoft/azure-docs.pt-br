---
title: Detalhes da estrutura de isenção de política
description: Descreve a definição de isenção de política usada pelo Azure Policy para isentar recursos da avaliação de iniciativas ou definições.
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: e6ced56c1dc65ca68998c5c58d3e985b63873e0b
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950169"
---
# <a name="azure-policy-exemption-structure"></a>Azure Policy estrutura de isenção

O recurso de isenção de Azure Policy (versão prévia) é usado para _isentar_ uma hierarquia de recursos ou um recurso individual da avaliação de iniciativas ou definições. Recursos com contagem de _isenção_ para conformidade geral, mas que não podem ser avaliados ou têm uma renúncia temporária. Para obter mais informações, consulte [entender o escopo em Azure Policy](./scope.md). Azure Policy isenções só funcionam com [modos do Resource Manager](./definition-structure.md#resource-manager-modes) e não funcionam com [modos de provedor de recursos](./definition-structure.md#resource-provider-modes).

> [!IMPORTANT]
> Esse recurso é gratuito durante a **Visualização**. Para obter detalhes de preços, consulte [preços de Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy/). Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você usa JSON para criar uma isenção de política. A isenção de política contém elementos para:

- nome de exibição
- descrição
- metadata
- atribuição de política
- definições de política dentro de uma iniciativa
- Categoria de isenção
- expiração

> [!NOTE]
> Uma isenção de política é criada como um objeto filho na hierarquia de recursos ou o recurso individual concede a isenção, para que o destino não seja incluído na definição de isenção.

Por exemplo, o JSON a seguir mostra uma isenção de política na categoria **renúncia** de um recurso para uma atribuição de iniciativa chamada `resourceShouldBeCompliantInit` . O recurso é _isento_ de apenas duas das definições de política na iniciativa, a `customOrgPolicy` definição de política personalizada (referência `requiredTags` ) e a definição de política interna ' locais permitidos ' (ID: `e56962a6-4747-49cd-b67b-bf8b01975c4c` , referência `allowedLocations` ):

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

Trecho da iniciativa relacionada com a correspondência `policyDefinitionReferenceIds` usada pela isenção de política:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>Nome de exibição e descrição

Use **DisplayName** e **Description** para identificar a isenção de política e fornecer contexto para seu uso com o recurso específico. **displayName** tem um comprimento máximo de _128_ caracteres e **description** tem um comprimento máximo de _512_ caracteres.

## <a name="metadata"></a>Metadados

A propriedade **Metadata** permite criar qualquer propriedade filho necessária para armazenar informações relevantes. No exemplo acima, as propriedades **requestedBy**, **approvedBy**, **approvedn**e **ticketRef** contêm valores de cliente para fornecer informações sobre quem solicitou a isenção, quem o aprovou e quando e um tíquete de acompanhamento interno para a solicitação. Essas propriedades de **metadados** são exemplos, mas não são necessárias e os **metadados** não são limitados a essas propriedades filho.

## <a name="policy-assignment-id"></a>ID de atribuição de política

Este campo deve ser o nome do caminho completo de uma atribuição de política ou uma atribuição de iniciativa.
`policyAssignmentId` é uma cadeia de caracteres e não uma matriz. Essa propriedade define a qual atribuição a hierarquia de recursos pai ou o recurso individual está _isento_ .

## <a name="policy-definition-ids"></a>IDs de definição de política

Se `policyAssignmentId` for para uma atribuição de iniciativa, a `policyDefinitionReferenceIds` Propriedade poderá ser usada para especificar quais definições de política na iniciativa a que o recurso de entidade tem uma isenção. Como o recurso pode ser isento de uma ou mais definições de política incluídas, essa propriedade é uma _matriz_. Os valores devem corresponder aos valores na definição de iniciativa nos `policyDefinitions.policyDefinitionReferenceId` campos.

## <a name="exemption-category"></a>Categoria de isenção

Existem duas categorias de isenção e são usadas para agrupar isenções:

- **Atenuado**: a isenção é concedida porque a intenção da política é atendida por meio de outro método.
- **Renúncia**: a isenção é concedida porque o estado de não conformidade do recurso é temporariamente aceito. Outro motivo para usar essa categoria é para uma hierarquia de recursos ou recursos que deve ser excluída de uma ou mais definições em uma iniciativa, mas não deve ser excluída de toda a iniciativa.

## <a name="expiration"></a>Expiração

Para definir quando uma hierarquia de recursos ou um recurso individual não é mais _isento_ de uma atribuição, defina a `expiresOn` propriedade. Essa propriedade opcional deve estar no formato de data/hora universal ISO 8601 `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

> [!NOTE]
> As isenções de política não são excluídas quando a `expiresOn` data é atingida. O objeto é preservado para manutenção de registro, mas a isenção não é mais atendida.

## <a name="required-permissions"></a>Permissões necessárias

As permissões do RBAC do Azure necessárias para gerenciar objetos de isenção de política estão no `Microsoft.Authorization/policyExemptions` grupo de operações. As funções internas de [colaborador de diretiva de recurso](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor) e administrador de [segurança](../../../role-based-access-control/built-in-roles.md#security-admin) têm as `read` permissões e o gravador de dados de informações de `write` [política (versão prévia)](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview) tem a `read` permissão.

As isenções têm medidas de segurança adicionais devido ao impacto da concessão de uma isenção. Além de exigir a `Microsoft.Authorization/policyExemptions/write` operação na hierarquia de recursos ou recurso individual, o criador de uma isenção deve ter o `exempt/Action` verbo na atribuição de destino.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [estrutura da definição de política](./definition-structure.md).
- Entenda como [criar políticas de forma programática](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).