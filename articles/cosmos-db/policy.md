---
title: Use Azure Policy para implementar governança e controles para recursos de Azure Cosmos DB
description: Saiba como usar Azure Policy para implementar governança e controles para recursos de Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 1390f5db6e0f0370788bef60d5a2cafee1e8a96d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93080644"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>Use Azure Policy para implementar governança e controles para recursos de Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O [Azure Policy](../governance/policy/overview.md) ajuda a reforçar os padrões de governança organizacional, a avaliar a conformidade dos recursos e a implementar a correção automática. Os casos de uso comuns incluem segurança, gerenciamento de custos e consistência de configuração.

Definições de política interna do Azure Policy. Você pode criar definições de política personalizadas para cenários que não são abordados pelas definições de política internas. Para obter mais detalhes, confira a [documentação do Azure Policy](../governance/policy/overview.md).

> [!IMPORTANT]
> Azure Policy é imposta no nível do provedor de recursos para os serviços do Azure. Os SDKs do Cosmos DB podem executar a maioria das operações de gerenciamento em recursos de banco de dados, contêiner e taxa de transferência que ignoram o provedor de recursos Cosmos DB, ignorando assim qualquer política criada usando Azure Policy. Para garantir a imposição de políticas, consulte [impedindo alterações dos SDKs de Azure Cosmos DB](role-based-access-control.md#prevent-sdk-changes)

## <a name="assign-a-built-in-policy-definition"></a>Atribuir uma definição de política incorporada

As definições de política descrevem as condições de conformidade do recurso e o efeito a ser tomada se uma condição for atendida. As _atribuições_ de política são criadas com base nas _definições_ de política. Você pode usar definições de política internas ou personalizadas para seus recursos do Microsoft Azure Cosmos DB. As atribuições de política têm como escopo um grupo de gerenciamento do Azure, uma assinatura do Azure ou um grupo de recursos e são aplicadas aos recursos dentro do escopo selecionado. Opcionalmente, você pode excluir recursos específicos do escopo.

Você pode criar atribuições de política com o [portal do Azure](../governance/policy/assign-policy-portal.md), [Azure PowerShell](../governance/policy/assign-policy-powershell.md), [CLI do Azure](../governance/policy/assign-policy-azurecli.md)ou [modelo ARM](../governance/policy/assign-policy-template.md).

Para criar uma atribuição de política de uma definição de política interna para Azure Cosmos DB, use as etapas no artigo [Criar uma atribuição de política com o portal do Azure](../governance/policy/assign-policy-portal.md).

Na etapa para selecionar uma definição de política, insira `Cosmos DB` no campo de pesquisa para filtrar a lista de definições de políticas internas disponíveis. Selecione uma das definições de política internas disponíveis e escolha **Selecionar** para continuar criando a atribuição de política.

> [!TIP]
> Você também pode usar os nomes de definição de política interna mostrados no painel **Definições disponíveis** com modelos Azure PowerShell, CLI do Azure ou ARM para criar atribuições de política.

:::image type="content" source="./media/policy/available-definitions.png" alt-text="Pesquisar definições de políticas internas para o Azure Cosmos DB":::

## <a name="create-a-custom-policy-definition"></a>Criar uma definição de política personalizada

Para cenários que não são abordados pelas definições de política internas, você pode criar [uma definição de política personalizada](../governance/policy/tutorials/create-custom-policy-definition.md). Posteriormente, você cria uma _atribuição_ de política com a sua _definição_ de política personalizada.

### <a name="property-types-and-property-aliases-in-policy-rules"></a>Tipos de propriedade e aliases de propriedade em regras de política

Use as [etapas de definição de política personalizada](../governance/policy/tutorials/create-custom-policy-definition.md) para identificar as propriedades de recurso e os aliases de propriedade, que são necessários para criar regras de política.

Para identificar Azure Cosmos DB aliases de propriedade específicos, use o namespace `Microsoft.DocumentDB` com um dos métodos mostrados no artigo etapas de definição de política personalizada.

#### <a name="use-the-azure-cli"></a>Usar a CLI do Azure:
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>Usar PowerShell do Azure:
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

Esses comandos geram a lista de nomes de alias de propriedade para a propriedade do Microsoft Azure Cosmos DB. Veja a seguir um trecho da saída:

```json
[
  "Microsoft.DocumentDB/databaseAccounts/sku.name",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*]",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*].id",
  "Microsoft.DocumentDB/databaseAccounts/isVirtualNetworkFilterEnabled",
  "Microsoft.DocumentDB/databaseAccounts/consistencyPolicy.defaultConsistencyLevel",
  "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
  "Microsoft.DocumentDB/databaseAccounts/Locations",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*]",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*].locationName",
  "..."
]
```

Você pode usar qualquer um desses nomes de alias de propriedade nas [regras de definição de política personalizada](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule).

Veja a seguir um exemplo de definição de política que verifica se uma conta de Azure Cosmos DB está configurada para vários locais de gravação. A definição de política personalizada inclui duas regras: uma para verificar o tipo específico de alias de propriedade e a segunda para a propriedade específica do tipo, nesse caso, o campo que armazena a configuração de vários locais de gravação. Ambas as regras usam os nomes de alias.

```json
"policyRule": {
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DocumentDB/databaseAccounts"
      },
      {
        "field": "Microsoft.DocumentDB/databaseAccounts/enableMultipleWriteLocations",
        "notEquals": true
      }
    ]
  },
  "then": {
    "effect": "Audit"
  }
}
```

As definições de política personalizadas podem ser usadas para criar atribuições de política, como acontece com as definições de política internas.

## <a name="policy-compliance"></a>Conformidade da política

Depois que as atribuições de política são criadas, o Azure Policy avalia os recursos no escopo da atribuição. A _conformidade_ de cada recurso com a política é avaliada. O _efeito_ especificado na política é então aplicado a recursos sem conformidade.

Você pode examinar os resultados de conformidade e os detalhes de correção no [portal do Azure](../governance/policy/how-to/get-compliance-data.md#portal) ou por meio da [CLI do Azure](../governance/policy/how-to/get-compliance-data.md#command-line) ou dos logs do [Azure monitor](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs).

A captura de tela a seguir mostra dois exemplos de atribuições de política.

Uma atribuição é baseada em uma definição de política interna, que verifica se os recursos do Microsoft Azure Cosmos DB são implantados somente nas regiões do Azure permitidas. Conformidade de recursos mostra o resultado da avaliação de política (compatível ou não compatível) para recursos no escopo.

A outra atribuição é baseada em uma definição de política personalizada. Essa atribuição verifica se as contas do Cosmos DB estão configuradas para vários locais de gravação.

Depois que as atribuições de política são implantadas, o painel de conformidade mostra os resultados da avaliação. Observe que isso pode levar até 30 minutos após a implantação de uma atribuição de política. Além disso, as [verificações de avaliação de política podem ser iniciadas sob demanda](../governance/policy/how-to/get-compliance-data.md#on-demand-evaluation-scan) imediatamente após a criação de atribuições de política.

A captura de tela mostra os seguintes resultados de avaliação de conformidade para contas de Azure Cosmos DB no escopo:

- Zero de duas contas são compatíveis com uma política que a filtragem de rede virtual (VNet) deve ser configurada.
- Zero de duas contas são compatíveis com uma política que exige que a conta seja configurada para vários locais de gravação
- Zero de duas contas são compatíveis com uma política que os recursos foram implantados para regiões do Azure permitidas.

:::image type="content" source="./media/policy/compliance.png" alt-text="Resultados de conformidade para atribuições de Azure Policy listadas":::

Para corrigir os recursos sem conformidade, consulte [como corrigir recursos com o Azure Policy](../governance/policy/how-to/remediate-resources.md).

## <a name="next-steps"></a>Próximas etapas

- [Examine as definições de política personalizada de exemplo para Azure Cosmos DB](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB), incluindo as políticas de filtragem de VNet e de várias local de gravação mostradas acima.
- [Criar uma atribuição de política no portal do Azure](../governance/policy/assign-policy-portal.md)
- [Revisar as definições de políticas internas do Azure Policy para o Microsoft Azure Cosmos DB](./policy-reference.md)