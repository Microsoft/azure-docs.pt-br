---
title: Entender como funcionam os efeitos
description: As definições do Azure Policy têm vários efeitos que determinam como a conformidade é gerenciada e relatada.
ms.date: 02/17/2021
ms.topic: conceptual
ms.openlocfilehash: 67445b3d0d63b3827f82822de00412bdab67c5ab
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741813"
---
# <a name="understand-azure-policy-effects"></a>Compreender os efeitos do Azure Policy

Cada definição de política no Azure Policy tem um único efeito. Esse efeito determina o que acontece quando a regra de política é avaliada para correspondência. Os efeitos se comportam de modo diferente caso sejam para um novo recurso, um recurso atualizado ou um recurso existente.

Atualmente, estes efeitos têm suporte em uma definição de política:

- [Acrescentar](#append)
- [Auditoria](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Deny](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Desabilitado](#disabled)
- [Modificar](#modify)

Os seguintes efeitos foram _preteridos_:

- [EnforceOPAConstraint](#enforceopaconstraint)
- [EnforceRegoPolicy](#enforceregopolicy)

> [!IMPORTANT]
> No lugar dos efeitos **EnforceOPAConstraint** ou **EnforceRegoPolicy** , use _Audit_ e _Deny_ com o modo do provedor de recursos `Microsoft.Kubernetes.Data` . As definições de política internas foram atualizadas. Quando as atribuições de política existentes dessas definições de política internas são modificadas, o parâmetro de _efeito_ deve ser alterado para um valor na lista _allowedValues_ atualizada.

## <a name="order-of-evaluation"></a>Ordem de avaliação

As solicitações para criar ou atualizar um recurso são avaliadas pela Azure Policy primeiro. O Azure Policy cria uma lista de todas as atribuições que se aplicam ao recurso e o avalia em relação a cada definição. Para um [modo do Gerenciador de recursos](./definition-structure.md#resource-manager-modes), o Azure Policy processa vários dos efeitos antes de entregar a solicitação ao provedor de recursos apropriado. Essa ordem impede o processamento desnecessário por um provedor de recursos quando um recurso não atende aos controles de governança criados de Azure Policy. Com um [modo de provedor de recursos](./definition-structure.md#resource-provider-modes), o provedor de recursos gerencia a avaliação e o resultado e relata os resultados de volta para Azure Policy.

- **Desabilitado** é marcado primeiro para determinar se a regra de política deve ser avaliada.
- **Append** e **Modify** são então avaliados. Como cada um pode alterar a solicitação, a alteração realizada pode impedir uma auditoria ou negar o efeito do gatilho. Esses efeitos só estão disponíveis com um modo do Gerenciador de recursos.
- **Negar** é avaliada. Ao avaliar o efeito negar antes da auditoria, evita-se o log duplo de um recurso indesejado.
- A **auditoria** é avaliada por último.

Depois que o provedor de recursos retornar um código de êxito em uma solicitação do modo do Gerenciador de recursos, **AuditIfNotExists** e **DeployIfNotExists** serão avaliados para determinar se o log ou a ação de conformidade adicional é necessária.

Além disso, `PATCH` as solicitações que modificam apenas `tags` campos relacionados restringem a avaliação de política para políticas que contêm condições que inspecionam os `tags` campos relacionados.

## <a name="append"></a>Acrescentar

O efeito acrescentar é usado para adicionar outros campos ao recurso solicitado durante a criação ou atualização. Um exemplo comum é especificar os IPs permitidos para um recurso de armazenamento.

> [!IMPORTANT]
> Append se destina ao uso com propriedades sem marca. Embora Append possa adicionar marcas a um recurso durante uma solicitação de criação ou atualização, é recomendável usar o efeito [Modify](#modify) para marcas.

### <a name="append-evaluation"></a>Avaliação de acréscimo

Append é avaliado antes que a solicitação seja processada por um provedor de recursos durante a criação ou a atualização de um recurso. O efeito append adiciona o campos ao recurso quando a condição **if** da regra de política for atendida. Se o efeito append substituir um valor na solicitação original por um valor diferente, ele atuará como um efeito Negar e rejeitará a solicitação. Para acrescentar um novo valor a uma matriz existente, use a versão **\[\*\]** do alias.

Quando uma definição de política usando o efeito append é executada como parte de um ciclo de avaliação, ela não faz alterações em recursos já existentes. Em vez disso, ela marca qualquer recurso que atende a condição **se** como não conforme.

### <a name="append-properties"></a>Propriedades de acréscimo

Um efeito acrescentar tem apenas uma matriz **detalhes**, que é necessária. Como **detalhes** é uma matriz, pode levar um único par **valor do campo** ou múltiplos. Confira a [estrutura de definição](definition-structure.md#fields) para saber a lista de campos aceitáveis.

### <a name="append-examples"></a>Exemplos de acréscimo

Exemplo 1: Par **campo/valor** exclusivo usando um [alias](definition-structure.md#aliases) não **\[\*\]** com um **valor** de matriz para definir as regras de IP em uma conta de armazenamento. Quando o alias não **\[\*\]** é uma matriz, o efeito acrescenta o **valor** como a matriz inteira. Se já existir a matriz, ocorre um evento de negação como resultado do conflito.

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

Exemplo 2: Par **campo/valor** exclusivo usando um [alias](definition-structure.md#aliases) **\[\*\]** com um **valor** de matriz para definir as regras de IP em uma conta de armazenamento. Com o uso do alias **\[\*\]** , o efeito acrescenta o **valor** a uma matriz potencialmente já existente. Se a matriz ainda não existir, ela será criada.

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

## <a name="audit"></a>Audit

Audit é usado para criar um evento de aviso no log de atividades ao avaliar um recurso fora de conformidade, mas ela não para a solicitação.

### <a name="audit-evaluation"></a>Avaliação de auditoria

Audit é o último efeito verificado pelo Azure Policy durante a criação ou a atualização de um recurso. Para um modo do Gerenciador de recursos, Azure Policy envia o recurso para o provedor de recursos. Audit funciona da mesma forma para uma solicitação de recurso e um ciclo de avaliação. Para recursos novos e atualizados, Azure Policy adiciona uma `Microsoft.Authorization/policies/audit/action` operação ao log de atividades e marca o recurso como sem conformidade.

### <a name="audit-properties"></a>Propriedades de auditoria

Para um modo do Gerenciador de recursos, o efeito de auditoria não tem nenhuma propriedade adicional para uso na condição **then** da definição de política.

Para um modo de provedor de recursos de `Microsoft.Kubernetes.Data` , o efeito de auditoria tem as seguintes subpropriedades adicionais de **detalhes**.

- **constrainttemplate** (obrigatório)
  - O modelo de restrição CRD (CustomResourceDefinition) que define novas restrições. O modelo define a lógica Rego, o esquema de restrição e os parâmetros de restrição que são passados por meio de **values** do Azure Policy.
- **restrição** (obrigatória)
  - A implementação de CRD do modelo de restrição. Usa parâmetros passados por meio de **values** como `{{ .Values.<valuename> }}`. No exemplo 2 abaixo, esses valores são `{{ .Values.excludedNamespaces }}` e `{{ .Values.allowedContainerImagesRegex }}` .
- **valores** (opcional)
  - Define quaisquer parâmetros e valores a ser passados para a restrição. Cada valor deve existir no modelo de restrição CRD.

### <a name="audit-example"></a>Exemplo de auditoria

Exemplo 1: usando o efeito de auditoria para os modos do Resource Manager.

```json
"then": {
    "effect": "audit"
}
```

Exemplo 2: usando o efeito de auditoria para um modo de provedor de recursos de `Microsoft.Kubernetes.Data` . As informações adicionais em **detalhes** definem o modelo de restrição e CRD para usar em kubernetes para limitar as imagens de contêiner permitidas.

```json
"then": {
    "effect": "audit",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

O AuditIfNotExists habilita a auditoria de recursos _relacionados_ ao recurso que corresponde à condição **If** , mas não tem as propriedades especificadas nos **detalhes** da condição **then** .

### <a name="auditifnotexists-evaluation"></a>Avaliação de AuditIfNotExists

O efeito AuditIfNotExists é executado depois de um provedor de recursos ter tratado uma solicitação de criação ou atualização de recurso e ter retornado um código de status de êxito. A auditoria ocorre quando não existem recursos relacionados ou se os recursos definidos por **ExistenceCondition** não são avaliados como verdadeiros. Para recursos novos e atualizados, Azure Policy adiciona uma `Microsoft.Authorization/policies/audit/action` operação ao log de atividades e marca o recurso como sem conformidade. Quando disparado, o recurso que atendeu à condição **se** é o recurso marcado como não compatível.

### <a name="auditifnotexists-properties"></a>Propriedades de AuditIfNotExists

A propriedade **detalhes** dos efeitos AuditIfNotExists tem todas as subpropriedades que definem os recursos relacionados a serem correspondidos.

- **Tipo** (obrigatório)
  - Especifica o tipo do recurso relacionado a ser correspondido.
  - Se **details.type** for um tipo de recurso abaixo do recurso de condição **if**, a política consultará os recursos desse **tipo** dentro do escopo do recurso avaliado. Caso contrário, a política consultará dentro do mesmo grupo de recursos que o recurso avaliado.
- **Nome** (opcional)
  - Especifica o nome exato do recurso a ser correspondido e faz com que a política busque um recurso específico em vez de todos os recursos do tipo especificado.
  - Quando os valores de condição para **If. Field. Type** e **. Details. Type** correspondem, o **nome** torna-se _obrigatório_ e deve ser `[field('name')]` ou `[field('fullName')]` para um recurso filho.
    No entanto, um efeito [Audit](#audit) deve ser considerado em seu lugar.
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

## <a name="deny"></a>Negar

O efeito deny é usado para impedir uma solicitação de recurso que não corresponde aos padrões definidos em uma definição de política e leva à falha da solicitação.

### <a name="deny-evaluation"></a>Avaliação de deny

Ao criar ou atualizar um recurso correspondente em um modo do Gerenciador de recursos, Deny impede a solicitação antes de ser enviada ao provedor de recursos. A solicitação é retornada como um `403 (Forbidden)`. No portal, o Forbidden pode ser exibido como um status sobre a implantação que foi impedida pela atribuição de política. Para um modo de provedor de recursos, o provedor de recursos gerencia a avaliação do recurso.

Durante a avaliação dos recursos existentes, os recursos que correspondem a uma definição de política de negação são marcados como fora de conformidade.

### <a name="deny-properties"></a>Propriedades de deny

Para um modo do Resource Manager, o efeito de negação não tem nenhuma propriedade adicional para uso na condição **then** da definição de política.

Para um modo de provedor de recursos de `Microsoft.Kubernetes.Data` , o efeito de negação tem as seguintes subpropriedades adicionais de **detalhes**.

- **constrainttemplate** (obrigatório)
  - O modelo de restrição CRD (CustomResourceDefinition) que define novas restrições. O modelo define a lógica Rego, o esquema de restrição e os parâmetros de restrição que são passados por meio de **values** do Azure Policy.
- **restrição** (obrigatória)
  - A implementação de CRD do modelo de restrição. Usa parâmetros passados por meio de **values** como `{{ .Values.<valuename> }}`. No exemplo 2 abaixo, esses valores são `{{ .Values.excludedNamespaces }}` e `{{ .Values.allowedContainerImagesRegex }}` .
- **valores** (opcional)
  - Define quaisquer parâmetros e valores a ser passados para a restrição. Cada valor deve existir no modelo de restrição CRD.

### <a name="deny-example"></a>Exemplo de deny

Exemplo 1: usando o efeito de negação para os modos do Resource Manager.

```json
"then": {
    "effect": "deny"
}
```

Exemplo 2: usando o efeito de negação para um modo de provedor de recursos de `Microsoft.Kubernetes.Data` . As informações adicionais em **detalhes** definem o modelo de restrição e CRD para usar em kubernetes para limitar as imagens de contêiner permitidas.

```json
"then": {
    "effect": "deny",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Semelhante a AuditIfNotExists, uma definição de política DeployIfNotExists executa uma implantação de modelo quando a condição é atendida.

> [!NOTE]
> [Modelos aninhados](../../../azure-resource-manager/templates/linked-templates.md#nested-template) são compatíveis com **deployIfNotExists**, mas [modelos vinculados](../../../azure-resource-manager/templates/linked-templates.md#linked-template) não são compatíveis atualmente.

### <a name="deployifnotexists-evaluation"></a>Avaliação de DeployIfNotExists

DeployIfNotExists é executado cerca de 15 minutos depois que um provedor de recursos tratou de uma solicitação criar ou atualizar assinatura ou recurso e retornou um código de status de êxito. Uma implantação de modelo ocorre quando não existem recursos relacionados ou se os recursos definidos por **ExistenceCondition** não são avaliados como verdadeiros. A duração da implantação depende da complexidade dos recursos incluídos no modelo.

Durante um ciclo de avaliação, as definições de política com um efeito DeployIfNotExists que correspondam a recursos são marcadas como não conformes, mas nenhuma ação é realizada no recurso. Os recursos fora de conformidade existentes podem ser corrigidos com uma [tarefa de correção](../how-to/remediate-resources.md).

### <a name="deployifnotexists-properties"></a>Propriedades de DeployIfNotExists

A propriedade **details** do efeito DeployIfNotExists tem todas as subpropriedades que definem os recursos relacionados a serem correspondidos e a implantação de modelo a ser executada.

- **Tipo** (obrigatório)
  - Especifica o tipo do recurso relacionado a ser correspondido.
  - Começa tentando buscar um recurso sob o recurso de condição **se**, depois consulta dentro do mesmo grupo de recursos como o recurso de condição **se**.
- **Nome** (opcional)
  - Especifica o nome exato do recurso a ser correspondido e faz com que a política busque um recurso específico em vez de todos os recursos do tipo especificado.
  - Quando os valores de condição para **If. Field. Type** e **. Details. Type** correspondem, o **nome** torna-se _obrigatório_ e deve ser `[field('name')]` ou `[field('fullName')]` para um recurso filho.
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
- **roleDefinitionIds** (obrigatório)
  - Essa propriedade deve incluir uma matriz de cadeias de caracteres que correspondem à ID de controle de acesso baseado em função que pode ser acessada pela assinatura. Para obter mais informações, confira [correção – configurar a definição de política](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (opcional)
  - Os valores permitidos são _Assinatura_ e _ResourceGroup_.
  - Define o tipo de implantação a ser disparada. _Assinatura_ indica uma [implantação no nível de assinatura](../../../azure-resource-manager/templates/deploy-to-subscription.md), _ResourceGroup_ indica uma implantação em um grupo de recursos.
  - Uma propriedade _localização_ deverá ser especificada na _Implantação_ quando usar implantações no nível de assinatura.
  - O padrão é _ResourceGroup_.
- **Implantação** (obrigatório)
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

## <a name="disabled"></a>Desabilitado

Esse efeito é útil para testar situações ou quando a definição de política tiver parametrizado o efeito. Essa flexibilidade possibilita desabilitar uma única atribuição em vez de desabilitar todas as atribuições da política.

Uma alternativa ao efeito desabilitado é **imposiçãomode**, que é definida na atribuição de política.
Quando **enforcementMode** é _Disabled_, os recursos ainda são avaliados. Não ocorre o registro em log, como logs de atividades, nem o efeito de política. Confira mais informações em [atribuição de política – modo de imposição](./assignment-structure.md#enforcement-mode).

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Esse efeito é usado com um _modo_ de definição de política de `Microsoft.Kubernetes.Data`. Ele é usado para passar as regras de controle de admissão do Gatekeeper v3 definidas com a [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) para [OPA (Open Policy Agent)](https://www.openpolicyagent.org/) para clusters Kubernetes no Azure.

> [!IMPORTANT]
> As definições de política de visualização limitada com efeito **EnforceOPAConstraint** e a categoria de **serviço kubernetes** relacionado são _preteridas_. Em vez disso, use a _auditoria_ de efeitos e a _negação_ com o modo do provedor de recursos `Microsoft.Kubernetes.Data` .

### <a name="enforceopaconstraint-evaluation"></a>Avaliação do EnforceOPAConstraint

O controlador de admissão do Open Policy Agent avalia qualquer nova solicitação no cluster em tempo real.
A cada 15 minutos, uma verificação completa do cluster é realizada, e os resultados são relatados para o Azure Policy.

### <a name="enforceopaconstraint-properties"></a>Propriedades do EnforceOPAConstraint

A propriedade **details** do efeito EnforceOPAConstraint tem as subpropriedades que descrevem a regra de controle de admissão do Gatekeeper v3.

- **constrainttemplate** (obrigatório)
  - O modelo de restrição CRD (CustomResourceDefinition) que define novas restrições. O modelo define a lógica Rego, o esquema de restrição e os parâmetros de restrição que são passados por meio de **values** do Azure Policy.
- **restrição** (obrigatória)
  - A implementação de CRD do modelo de restrição. Usa parâmetros passados por meio de **values** como `{{ .Values.<valuename> }}`. No exemplo a seguir, esses valores são `{{ .Values.cpuLimit }}` e `{{ .Values.memoryLimit }}`.
- **valores** (opcional)
  - Define quaisquer parâmetros e valores a ser passados para a restrição. Cada valor deve existir no modelo de restrição CRD.

### <a name="enforceopaconstraint-example"></a>Exemplo de EnforceOPAConstraint

Exemplo: regra de controle de admissão do Gatekeeper V3 para definição de limites de recursos de memória e CPU de contêiner em Kubernetes.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Esse efeito é usado com um _modo_ de definição de política de `Microsoft.ContainerService.Data`. Ele é usado para passar as regras de controle de admissão do Gatekeeper v2 definidas com o [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) para [OPA (Open Policy Agent)](https://www.openpolicyagent.org/) no [Serviço de Kubernetes do Azure](../../../aks/intro-kubernetes.md).

> [!IMPORTANT]
> As definições de política de visualização limitadas com o efeito **EnforceRegoPolicy** e a categoria relacionada do **Serviço de Kubernetes** foram _preteridas_. Em vez disso, use a _auditoria_ de efeitos e a _negação_ com o modo do provedor de recursos `Microsoft.Kubernetes.Data` .

### <a name="enforceregopolicy-evaluation"></a>Avaliação de EnforceRegoPolicy

O controlador de admissão do Open Policy Agent avalia qualquer nova solicitação no cluster em tempo real.
A cada 15 minutos, uma verificação completa do cluster é realizada, e os resultados são relatados para o Azure Policy.

### <a name="enforceregopolicy-properties"></a>Propriedades do EnforceRegoPolicy

A propriedade **details** do efeito EnforceRegoPolicy tem as subpropriedades que descrevem a regra de controle de admissão do Gatekeeper v2.

- **PolicyId** (obrigatório)
  - Um nome exclusivo passado como um parâmetro para a regra de controle de admissão Rego.
- **política** (obrigatória)
  - Especifica o URI da regra de controle de admissão Rego.
- **políticaparameters** (opcional)
  - Define quaisquer parâmetros e valores a serem passados para a política Rego.

### <a name="enforceregopolicy-example"></a>Exemplo de EnforceRegoPolicy

Exemplo: a regra de controle de admissão do Gatekeeper v2 para permitir apenas as imagens de contêiner especificadas em AKS.

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

## <a name="modify"></a>Modificar

Modify é usado para adicionar, atualizar ou remover propriedades ou marcas em uma assinatura ou recurso durante a criação ou atualização. Um exemplo comum é a atualização de marcas em recursos como costCenter. Os recursos fora de conformidade existentes podem ser corrigidos com uma [tarefa de correção](../how-to/remediate-resources.md). Uma única regra Modify pode ter qualquer quantidade de operações.

As seguintes operações são suportadas pela modificação:

- Adicionar, substituir ou remover marcas de recurso. Para marcas, uma política de modificação deve ter `mode` definido como _indexado_ , a menos que o recurso de destino seja um grupo de recursos.
- Adicionar ou substituir o valor do tipo de identidade gerenciada ( `identity.type` ) de máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais.
- Adicionar ou substituir os valores de determinados aliases (versão prévia).
  - Use `Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }`.
    no Azure PowerShell **4.6.0** ou superior para obter uma lista de aliases que podem ser usados com Modify.

> [!IMPORTANT]
> Se você estiver gerenciando marcas, é recomendável usar modificar em vez de acrescentar como modificar fornece tipos de operação adicionais e a capacidade de corrigir recursos existentes. No entanto, Append é recomendado se você não conseguir criar uma identidade gerenciada ou Modify ainda não oferecer suporte ao alias para a propriedade de recurso.

### <a name="modify-evaluation"></a>Avaliação de Modify

O efeito Modify é avaliado antes que a solicitação seja processada por um provedor de recursos durante a criação ou a atualização de um recurso. As operações de modificação são aplicadas ao conteúdo da solicitação quando a condição **If** da regra de política é atendida. Cada operação de modificação pode especificar uma condição que determina quando ela é aplicada. As operações com condições que são avaliadas como _false_ são ignoradas.

Quando um alias é especificado, as seguintes verificações adicionais são executadas para garantir que a operação de modificação não altere o conteúdo da solicitação de forma que faça com que o provedor de recursos o rejeite:

- A propriedade mapeada pelo alias é marcada como "modificável" na versão da API da solicitação.
- O tipo de token na operação de modificação corresponde ao tipo de token esperado para a propriedade na versão de API da solicitação.

Se uma dessas verificações falhar, a avaliação da política retornará para o **conflictEffect** especificado.

> [!IMPORTANT]
> É Recommeneded que modificar as definições que incluem aliases usam o **efeito de conflito** de _auditoria_ para evitar solicitações com falha usando versões de API em que a propriedade mapeada não é ' modificável '. Se o mesmo alias se comporta de forma diferente entre as versões de API, as operações de modificação condicional podem ser usadas para determinar a operação de modificação usada para cada versão de API.

Quando uma definição de política usando o efeito Modify é executada como parte de um ciclo de avaliação, ela não faz alterações em recursos já existentes. Em vez disso, ela marca qualquer recurso que atende a condição **se** como não conforme.

### <a name="modify-properties"></a>Propriedades de Modify

A propriedade **details** do efeito Modify tem todas as subpropriedades que definem as permissões necessárias para a correção e as **operações** usadas para adicionar, atualizar ou remover valores de marca.

- **roleDefinitionIds** (obrigatório)
  - Essa propriedade deve incluir uma matriz de cadeias de caracteres que correspondem à ID de controle de acesso baseado em função que pode ser acessada pela assinatura. Para obter mais informações, confira [correção – configurar a definição de política](../how-to/remediate-resources.md#configure-policy-definition).
  - A função definida deve incluir todas as operações concedidas à função de [Colaborador](../../../role-based-access-control/built-in-roles.md#contributor).
- **conflictEffect** (opcional)
  - Determina qual definição de política "WINS" no caso de mais de uma definição de política modifica a mesma propriedade ou quando a operação de modificação não funciona no alias especificado.
    - Para recursos novos ou atualizados, a definição de política com _Deny_ tem precedência. As definições de política com _auditoria_ ignoram todas as **operações**. Se mais de uma definição de política tiver _negação_, a solicitação será negada como um conflito. Se todas as definições de política tiverem _auditoria_, nenhuma das **operações** das definições de política conflitantes serão processadas.
    - Para recursos existentes, se mais de uma definição de política tiver _negação_, o status de conformidade será _conflito_. Se uma ou menos definições de política tiverem _negação_, cada atribuição retornará um status de conformidade de _não compatível_.
  - Valores disponíveis: _auditoria_, _negar_, _desabilitado_.
  - O valor padrão é _Deny_.
- **operações** (obrigatório)
  - Uma matriz de todas as operações de marca a serem concluídas em recursos correspondentes.
  - Propriedades:
    - **operação** (obrigatório)
      - Define a ação a ser tomada em um recurso correspondente. As opções são: _addOrReplace_, _Add_ e _Remove_. _Add_ se comporta de forma semelhante ao efeito [Append](#append).
    - **campo** (obrigatório)
      - A marca para adicionar, substituir ou remover. Os nomes de marca devem aderir à mesma convenção de nomenclatura dos outros [campos](./definition-structure.md#fields).
    - **value** (opcional)
      - O valor a ser definido para a marca.
      - Essa propriedade será necessária se a **operação** for _addOrReplace_ ou _Add_.
    - **condição** (opcional)
      - Uma cadeia de caracteres que contém uma expressão de linguagem Azure Policy com [funções de política](./definition-structure.md#policy-functions) que são avaliadas como _true_ ou _false_.
      - O não oferece suporte às seguintes funções de política: `field()` , `resourceGroup()` , `subscription()` .

### <a name="modify-operations"></a>Operações de Modify

A matriz de propriedades de **operações** torna possível alterar várias marcas de maneiras diferentes a partir de uma única definição de política. Cada operação é composta pelas propriedades **operation**, **field** e **value**. Operation determina o que a tarefa de correção faz nas marcas, field determina qual marca é alterada, e value define a nova configuração para essa marca. O exemplo abaixo faz as seguintes alterações na marca:

- Define a marca `environment` como "Test", mesmo que ela já exista com um valor diferente.
- Remove a marca `TempResource`.
- Define a marca `Dept` como o parâmetro de política _DeptName_ configurado na atribuição de política.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

A propriedade **operation** tem as seguintes opções:

|Operação |Descrição |
|-|-|
|addOrReplace |Adiciona a propriedade ou marca definida e o valor ao recurso, mesmo que a propriedade ou marca já exista com um valor diferente. |
|Adicionar |Adiciona a propriedade definida ou a marca e o valor ao recurso. |
|Remover |Remove a propriedade ou marca definida do recurso. |

### <a name="modify-examples"></a>Exemplos de Modify

Exemplo 1: Adicione a marca `environment` e substitua as marcas `environment` atuais por "Test":

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

Exemplo 2: Remova a marca `env` e adicione a marca `environment` ou substitua as marcas existentes de `environment` por um valor com parâmetros:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "conflictEffect": "deny",
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

Exemplo 3: Verifique se uma conta de armazenamento não permite acesso público de BLOB, a operação de modificação é aplicada somente ao avaliar solicitações com a versão de API maior ou igual a "2019-04-01":

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/microsoft.authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab"
        ],
        "conflictEffect": "audit",
        "operations": [
            {
                "condition": "[greaterOrEquals(requestContext().apiVersion, '2019-04-01')]",
                "operation": "addOrReplace",
                "field": "Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
                "value": false
            }
        ]
    }
}
```

## <a name="layering-policy-definitions"></a>Definições de políticas em camadas

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

Cada atribuição é avaliada individualmente. Assim, não existe chance de um recurso passar por uma brecha nas diferenças de escopo. O resultado das definições de políticas em camadas é considerado **cumulativo mais restritivo**. Por exemplo, se as políticas 1 e 2 tivessem um efeito deny, um recurso seria bloqueado pelas definições de políticas conflitantes e sobrepostas. Caso ainda precise que o recurso seja criado no escopo de destino, revise as exclusões em cada atribuição para validar que as políticas corretas de atribuição estão afetando os escopos corretos.

## <a name="next-steps"></a>Próximas etapas

- Revise os exemplos em [Exemplos do Azure Policy](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](definition-structure.md).
- Entenda como [criar políticas de forma programática](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
