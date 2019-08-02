---
title: Entender como funcionam os efeitos
description: A definição do Azure Policy tem vários efeitos que determinam como a conformidade é gerenciada e relatada.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: c2bf19a2599d59b9ff2b3d189b26134f1528a878
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448564"
---
# <a name="understand-azure-policy-effects"></a>Compreender os efeitos do Azure Policy

Cada definição de política no Azure Policy tem um único efeito. Esse efeito determina o que acontece quando a regra de política é avaliada para correspondência. Os efeitos se comportam de modo diferente caso sejam para um novo recurso, um recurso atualizado ou um recurso existente.

Esses efeitos são atualmente suportados em uma definição de política:

- [Acrescentar](#append)
- [Audit](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Negar](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Desabilitado](#disabled)
- [EnforceRegoPolicy](#enforceregopolicy) (visualização)

## <a name="order-of-evaluation"></a>Ordem de avaliação

As solicitações para criar ou atualizar um recurso por meio do Azure Resource Manager são avaliadas primeiro pela política do Azure. A política do Azure cria uma lista de todas as atribuições que se aplicam ao recurso e, em seguida, avalia o recurso em relação a cada definição. A política do Azure processa vários dos efeitos antes de enviar a solicitação ao provedor de recursos apropriado. Isso impede que o processamento desnecessário por um provedor de recursos, quando um recurso não atende os controles de governança projetado da política do Azure.

- **Desabilitado** é marcado primeiro para determinar se a regra de política deve ser avaliada.
- **Append** é, então, avaliado. Como o efeito acrescentar pode alterar a solicitação, a alteração feita ao acrescentar pode impedir uma auditoria ou negar o efeito do gatilho.
- **Negar** é avaliada. Ao avaliar o efeito negar antes da auditoria, evita-se o log duplo de um recurso indesejado.
- **Audit** é avaliado antes de a solicitação ir para o provedor de recursos.

Depois que o provedor de recursos retorna um código de êxito, **AuditIfNotExists** e **DeployIfNotExists** fazem a avaliação para determinar se é necessário fazer registro em log para aumentar a conformidade.

Existe atualmente, qualquer ordem de avaliação para o **EnforceRegoPolicy** em vigor.

## <a name="disabled"></a>Desabilitado

Esse efeito é útil para testar situações ou quando a definição de política tiver parametrizado o efeito. Essa flexibilidade possibilita desabilitar uma única atribuição em vez de desabilitar todas as atribuições da política.

## <a name="append"></a>Acrescentar

O efeito acrescentar é usado para adicionar outros campos ao recurso solicitado durante a criação ou atualização. Um exemplo comum é a adição de marcas em recursos, como costCenter, ou especificar IPs permitidos para um recurso de armazenamento.

### <a name="append-evaluation"></a>Avaliação de acréscimo

Append é avaliado antes que a solicitação seja processada por um provedor de recursos durante a criação ou a atualização de um recurso. O efeito append adiciona o campos ao recurso quando a condição **if** da regra de política for atendida. Se o efeito append substituir um valor na solicitação original por um valor diferente, ele atuará como um efeito Negar e rejeitará a solicitação. Para acrescentar um novo valor a uma matriz existente, use a versão **[\*]** do alias.

Quando uma definição de política usando o efeito append é executada como parte de um ciclo de avaliação, ela não faz alterações em recursos já existentes. Em vez disso, ela marca qualquer recurso que atende a condição **se** como não conforme.

### <a name="append-properties"></a>Propriedades de acréscimo

Um efeito acrescentar tem apenas uma matriz **detalhes**, que é necessária. Como **detalhes** é uma matriz, pode levar um único par **valor do campo** ou múltiplos. Confira a [estrutura de definição](definition-structure.md#fields) para saber a lista de campos aceitáveis.

### <a name="append-examples"></a>Exemplos de acréscimo

Exemplo 1: par **campo/valor** exclusivo para acrescentar uma marca.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

Exemplo 2: dois pares **campo/valor** para acrescentar um conjunto de marcas.

```json
"then": {
    "effect": "append",
    "details": [{
            "field": "tags.myTag",
            "value": "myTagValue"
        },
        {
            "field": "tags.myOtherTag",
            "value": "myOtherTagValue"
        }
    ]
}
```

Exemplo 3: Única **valor do campo** emparelhar usando um não - **[\*]** [alias](definition-structure.md#aliases) com uma matriz **valor** para definir regras de IP em uma conta de armazenamento. Quando o alias não **[\*]** é uma matriz, o efeito acrescenta o **valor** como a matriz inteira. Se já existir a matriz, ocorre um evento de negação como resultado do conflito.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Exemplo 4: Par **campo/valor** exclusivo usando um [alias](definition-structure.md#aliases) **[\*]** com uma matriz **value** para definir as regras de IP em uma conta de armazenamento. Ao usar o alias **[\*]** , o efeito acrescenta o **valor** a uma matriz potencialmente já existente. Se a matriz ainda não existir, ela será criada.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="deny"></a>Negar

O efeito deny é usado para impedir uma solicitação de recurso que não corresponde aos padrões definidos em uma definição de política e leva à falha da solicitação.

### <a name="deny-evaluation"></a>Avaliação de deny

Ao criar ou atualizar um recurso, o efeito deny impede a solicitação antes de ser enviada ao provedor de recursos. A solicitação é retornada como um `403 (Forbidden)`. No portal, o Forbidden pode ser exibido como um status sobre a implantação que foi impedida pela atribuição de política.

Durante a avaliação dos recursos existentes, os recursos que correspondem a uma definição de política de negação são marcados como fora de conformidade.

### <a name="deny-properties"></a>Propriedades de deny

O efeito deny não têm propriedades adicionais para uso na condição **then** da definição de política.

### <a name="deny-example"></a>Exemplo de deny

Exemplo: Usando o efeito deny.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Audit

Audit é usado para criar um evento de aviso no log de atividades ao avaliar um recurso fora de conformidade, mas ela não para a solicitação.

### <a name="audit-evaluation"></a>Avaliação de auditoria

Auditoria é o último efeito verificado pela política do Azure durante a criação ou atualização de um recurso. A política do Azure, em seguida, envia o recurso para o provedor de recursos. Audit funciona da mesma forma para uma solicitação de recurso e um ciclo de avaliação. A política do Azure adiciona uma `Microsoft.Authorization/policies/audit/action` operação para o log de atividades e o marca como não compatível.

### <a name="audit-properties"></a>Propriedades de auditoria

O efeito audit não têm propriedades adicionais para uso na condição **then** da definição de política.

### <a name="audit-example"></a>Exemplo de auditoria

Exemplo: Usando o efeito audit.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

O efeito AuditIfNotExists habilita a auditoria em recursos que correspondem à condição **if**, mas não têm os componentes especificados nos **detalhes** da condição **then**.

### <a name="auditifnotexists-evaluation"></a>Avaliação de AuditIfNotExists

O efeito AuditIfNotExists é executado depois de um provedor de recursos ter tratado uma solicitação de criação ou atualização de recurso e ter retornado um código de status de êxito. A auditoria ocorre quando não existem recursos relacionados ou se os recursos definidos por **ExistenceCondition** não são avaliados como verdadeiros. A política do Azure adiciona uma `Microsoft.Authorization/policies/audit/action` operação para a atividade de log da mesma maneira como o efeito de auditoria. Quando disparado, o recurso que atendeu à condição **se** é o recurso marcado como não compatível.

### <a name="auditifnotexists-properties"></a>Propriedades de AuditIfNotExists

A propriedade **detalhes** dos efeitos AuditIfNotExists tem todas as subpropriedades que definem os recursos relacionados a serem correspondidos.

- **Tipo** [obrigatório]
  - Especifica o tipo do recurso relacionado a ser correspondido.
  - Se **details.type** é um tipo de recurso sob a **se** condição recurso, a política de consulta para os recursos deste **tipo** dentro do escopo do recurso avaliado. Caso contrário, consultas de política dentro do mesmo grupo de recursos que o recurso avaliado.
- **Nome** (opcional)
  - Especifica o nome exato do recurso a ser correspondido e faz com que a política busque um recurso específico em vez de todos os recursos do tipo especificado.
  - Quando a condição de valores para **if.field.type** e **then.details.type** corresponderem, em seguida, **nome** se torna _necessária_ e deve ser `[field('name')]`. No entanto, uma [auditoria](#audit) efeito deve ser considerado em vez disso.
- **ResourceGroupName** (opcional)
  - Permite que a correspondência do recurso relacionado venha de um grupo de recursos diferente.
  - Não se aplica se **type** for um recurso que estaria sob o recurso de condição **if**.
  - O padrão é o grupo de recursos do recurso de condição **se**.
- **ExistenceScope** (opcional)
  - Os valores permitidos são _Assinatura_ e _ResourceGroup_.
  - Define o escopo de onde buscar o recurso relacionado com o qual corresponder.
  - Não se aplica se **type** for um recurso que estaria sob o recurso de condição **if**.
  - Para _ResourceGroup_, limitaria para o grupo de recursos do recurso da condição **se** ou o grupo de recursos especificado em **ResourceGroupName**.
  - Para _Assinatura_, consulta a assinatura inteira para o recurso relacionado.
  - O padrão é _ResourceGroup_.
- **ExistenceCondition** (opcional)
  - Se não for especificado, todos os recursos relacionados de **type** cumprem o efeito e não acionam a auditoria.
  - Usa a mesma linguagem como a regra de política para a condição **se**, mas é avaliada em relação a cada recurso relacionado individualmente.
  - Se algum recurso relacionado correspondente for avaliado como verdadeiro, o efeito será atendido e não acionará a auditoria.
  - Pode usar [field()] para verificar a equivalência com valores na condição **se**.
  - Por exemplo, pode ser usado para validar que o recurso pai (na condição **se**) está no mesmo local que recursos como o recurso relacionado correspondente.

### <a name="auditifnotexists-example"></a>Exemplo de AuditIfNotExists

Exemplo: Avalia máquinas virtuais para determinar se a extensão antimalware existe e faz auditorias quando ausente.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Semelhante ao efeito AuditIfNotExists, o efeito DeployIfNotExists executa uma implantação de modelo quando a condição for atendida.

> [!NOTE]
> [Modelos aninhados](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template) são compatíveis com **deployIfNotExists**, mas [modelos vinculados](../../../azure-resource-manager/resource-group-linked-templates.md) não são compatíveis atualmente.

### <a name="deployifnotexists-evaluation"></a>Avaliação de DeployIfNotExists

DeployIfNotExists é executado depois de um provedor de recursos ter tratado uma solicitação de criação ou atualização de um recurso e ter retornado um código de status de êxito. Uma implantação de modelo ocorre quando não existem recursos relacionados ou se os recursos definidos por **ExistenceCondition** não são avaliados como verdadeiros.

Durante um ciclo de avaliação, as definições de política com um efeito DeployIfNotExists que correspondam a recursos são marcadas como não conformes, mas nenhuma ação é realizada no recurso.

### <a name="deployifnotexists-properties"></a>Propriedades de DeployIfNotExists

A propriedade **detalhes** dos efeitos DeployIfNotExists tem todas as subpropriedades que definem os recursos relacionados a serem correspondidos e a implantação de modelo a ser executada.

- **Tipo** [obrigatório]
  - Especifica o tipo do recurso relacionado a ser correspondido.
  - Começa tentando buscar um recurso sob o recurso de condição **se**, depois consulta dentro do mesmo grupo de recursos como o recurso de condição **se**.
- **Nome** (opcional)
  - Especifica o nome exato do recurso a ser correspondido e faz com que a política busque um recurso específico em vez de todos os recursos do tipo especificado.
  - Quando a condição de valores para **if.field.type** e **then.details.type** corresponderem, em seguida, **nome** se torna _necessária_ e deve ser `[field('name')]`.
- **ResourceGroupName** (opcional)
  - Permite que a correspondência do recurso relacionado venha de um grupo de recursos diferente.
  - Não se aplica se **type** for um recurso que estaria sob o recurso de condição **if**.
  - O padrão é o grupo de recursos do recurso de condição **se**.
  - Se uma implantação de modelo é executada, ele é implantado no grupo de recursos desse valor.
- **ExistenceScope** (opcional)
  - Os valores permitidos são _Assinatura_ e _ResourceGroup_.
  - Define o escopo de onde buscar o recurso relacionado com o qual corresponder.
  - Não se aplica se **type** for um recurso que estaria sob o recurso de condição **if**.
  - Para _ResourceGroup_, limitaria para o grupo de recursos do recurso da condição **se** ou o grupo de recursos especificado em **ResourceGroupName**.
  - Para _Assinatura_, consulta a assinatura inteira para o recurso relacionado.
  - O padrão é _ResourceGroup_.
- **ExistenceCondition** (opcional)
  - Se não for especificado, todos os recursos relacionados de **type** cumprem o efeito e não acionam a implantação.
  - Usa a mesma linguagem como a regra de política para a condição **se**, mas é avaliada em relação a cada recurso relacionado individualmente.
  - Se algum recurso relacionado correspondente for avaliado como verdadeiro, o efeito será atendido e não acionará a implantação.
  - Pode usar [field()] para verificar a equivalência com valores na condição **se**.
  - Por exemplo, pode ser usado para validar que o recurso pai (na condição **se**) está no mesmo local que recursos como o recurso relacionado correspondente.
- **roleDefinitionIds** [obrigatório]
  - Essa propriedade deve incluir uma matriz de cadeias de caracteres que correspondem à ID de controle de acesso baseado em função que pode ser acessada pela assinatura. Para obter mais informações, confira [correção – configurar a definição de política](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (opcional)
  - Os valores permitidos são _Assinatura_ e _ResourceGroup_.
  - Define o tipo de implantação a ser disparada. _Assinatura_ indica uma [implantação no nível de assinatura](../../../azure-resource-manager/deploy-to-subscription.md), _ResourceGroup_ indica uma implantação em um grupo de recursos.
  - Uma propriedade _localização_ deverá ser especificada na _Implantação_ quando usar implantações no nível de assinatura.
  - O padrão é _ResourceGroup_.
- **Implantação** [obrigatória]
  - Essa propriedade deve incluir a implantação de modelo completo que seria passada para o API PUT de `Microsoft.Resources/deployments`. Para obter mais informações, consulte a [API REST de implantações](/rest/api/resources/deployments).

  > [!NOTE]
  > Todas as funções dentro da propriedade **Implantação** são avaliadas como componentes do modelo, não da política. A exceção é a propriedade **parâmetros**, que passa os valores da política para o modelo. O **valor** nesta seção em um nome de parâmetro de modelo é usado para executar essa passagem de valor (consulte _fullDbName_ no exemplo do efeito DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Exemplo de DeployIfNotExists

Exemplo: Avalia os bancos de dados do SQL Server para determinar se transparentDataEncryption está habilitado. Se não estiver, uma implantação será executada para habilitá-lo.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Esse efeito é usado com uma definição de política *modo* de `Microsoft.ContainerService.Data`. Ele é usado para passar as regras de controle de admissão definidas com [Rego](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego) ao [abrir agente de política](https://www.openpolicyagent.org/) (OPA) em [serviço Kubernetes do Azure](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [A política do Azure para Kubernetes](rego-for-aks.md) está em visualização pública e dá suporte apenas a definições de políticas internas.

### <a name="enforceregopolicy-evaluation"></a>Avaliação EnforceRegoPolicy

O controlador de admissão abrir agente de política avalia todas as novas solicitações no cluster em tempo real.
A cada 5 minutos, uma verificação completa do cluster é concluída e os resultados relatados a política do Azure.

### <a name="enforceregopolicy-properties"></a>Propriedades de EnforceRegoPolicy

O **detalhes** propriedade do efeito EnforceRegoPolicy tem as subpropriedades que descrevem a regra de controle de admissão Rego.

- **policyId** [obrigatório]
  - Um nome exclusivo passado como um parâmetro para a regra de controle de admissão Rego.
- **diretiva** [obrigatório]
  - Especifica o URI da regra de controle de admissão de Rego.
- **policyParameters** [opcional]
  - Define os valores para passar para a política de rego e parâmetros.

### <a name="enforceregopolicy-example"></a>Exemplo de EnforceRegoPolicy

Exemplo: Rego regra de controle de admissão para permitir que apenas as imagens de contêiner especificado no AKS.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="layering-policies"></a>Políticas de camadas

Um recurso pode ser afetado por várias atribuições. Essas atribuições podem estar no mesmo escopo ou em escopos diferentes. Também é provável que cada uma dessas atribuições tenha um efeito diferente definido. A condição e o efeito de cada política são avaliados independentemente. Por exemplo:

- Política 1
  - Restringe o local do recurso para 'westus'
  - Atribuída à assinatura A
  - Efeito deny
- Política 2
  - Restringe o local do recurso para 'eastus'
  - Atribuída ao grupo de recursos B na assinatura A
  - Efeito audit
  
Essa configuração resultaria no seguinte:

- Os recursos já presentes no grupo de recursos B em “eastus” estão em conformidade em relação à política 2 e fora de conformidade em relação à política 1
- Os recursos presentes no grupo de recursos B e fora de 'eastus' estão fora de conformidade em relação à política 2 e fora de conformidade em relação à política 1 se não estão em 'westus'
- Novos recursos na assinatura A não presentes em “westus” são negados pela política 1
- Novos recursos na assinatura A e no grupo de recursos B em 'westus' são criados e estão fora de conformidade em relação à política 2

Se ambas as políticas 1 e 2 tiveram o efeito negar, a situação será alterada para:

- Os recursos já presentes no grupo de recursos B fora de “eastus” serão marcados como não conformes com a política 2
- Os recursos já presentes no grupo de recursos B fora de “westus” serão marcados como não conformes com a política 1
- Novos recursos na assinatura A não presentes em “westus” são negados pela política 1
- Os novos recursos no grupo de recursos B da assinatura A são negados

Cada atribuição é avaliada individualmente. Assim, não existe chance de um recurso passar por uma brecha nas diferenças de escopo. O resultado de políticas em camadas ou sobreposição de políticas é considerado **cumulativo mais restritivo**. Por exemplo, se as políticas 1 e 2 tivessem efeito deny, um recurso seria bloqueado pelas políticas conflitantes e sobrepostas. Caso ainda precise que o recurso seja criado no escopo de destino, revise as exclusões em cada atribuição para validar que as políticas corretas estão afetando os escopos corretos.

## <a name="next-steps"></a>Próximas etapas

- Examine os exemplos na [exemplos do Azure Policy](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](definition-structure.md).
- Entender como [criar políticas de forma programática](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](../how-to/remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).