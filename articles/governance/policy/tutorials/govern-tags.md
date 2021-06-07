---
title: 'Tutorial: Gerenciar a governança de marcas'
description: Neste tutorial, use o efeito Modificar do Azure Policy para criar e impor um modelo de governança de tag em recursos novos e existentes.
ms.date: 03/31/2021
ms.topic: tutorial
ms.openlocfilehash: 1e8850dd6fe380b0ea38a2a1722734d917c63d0c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093275"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Tutorial: Gerenciar a governança de marca com o Azure Policy

[Marcas](../../../azure-resource-manager/management/tag-resources.md) representam um aspecto fundamental da organização dos recursos do Azure em uma taxonomia. Ao seguir as [práticas recomendadas para gerenciamento de marcas](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources), essas podem ser a base de aplicação das suas políticas de negócios com o Azure Policy ou para [controlar custos com o Gerenciamento de Custos](../../../cost-management-billing/costs/cost-mgt-best-practices.md#tag-shared-resources).
Não importa como ou por quê você usa essas marcas, é importante que possa adicionar, alterar e removê-las rapidamente de seus recursos do Azure. Para ver se o recurso do Azure é compatível com marcação, confira [Suporte à marcação](../../../azure-resource-manager/management/tag-support.md).

O efeito [Modificar](../concepts/effects.md#modify) do Azure Policy foi desenvolvido para ajudar na governança das marcas, independentemente do estágio atual da governança do recurso. O **Modificar** ajuda quando:

- Você não tem experiência na nuvem e não tem qualquer governança de marca
- Você já tem milhares de recursos sem governança de marca
- Você já tem uma taxonomia que precisa de mudança

Neste tutorial, você concluirá as seguintes tarefas:

> [!div class="checklist"]
> - Identificar seus requisitos de negócios
> - Mapear cada requisito para uma definição de política
> - Agrupar as políticas de tag em uma iniciativa

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="identify-requirements"></a>Identificar os requisitos

Como qualquer implementação de qualidade dos controles de governança, os requisitos devem vir das necessidades de seus negócios e estarem bem definidos antes da criação dos controles técnicos. Para o tutorial deste cenário, os itens a seguir são nossos requisitos de negócios:

- Duas marcas obrigatórias em todos os recursos: _CostCenter_ e _Env_
- _CostCenter_ deve existir em todos os contêineres e recursos individuais
  - Os recursos herdam do contêiner no qual residem, mas podem ser substituídos individualmente
- _Env_ deve existir em todos os contêineres e recursos individuais
  - Os recursos determinam o ambiente por esquema de nomenclatura do contêiner e não podem ser substituídos
  - Todos os recursos em um contêiner fazem parte do mesmo ambiente

## <a name="configure-the-costcenter-tag"></a>Configurar a marca CostCenter

Em termos específicos a um ambiente do Azure gerenciado pelo Azure Policy, os requisitos da marca _CostCenter_ exigem os seguintes resultados:

- Negar grupos de recursos que não tenham a marca _CostCenter_
- Modificar recursos para adicionar a marca _CostCenter_ do grupo de recursos pai quando esta não estiver presente

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Negar grupos de recursos que não tenham a marca CostCenter

Como a marca _CostCenter_ de um grupo de recursos não pode ser determinada pelo nome do grupo de recursos, ela deve ser definida na solicitação para criar o grupo de recursos. A regra de política a seguir com o efeito [Deny](../concepts/effects.md#deny) impede a criação ou atualização de grupos de recursos que não tenham a marca _CostCenter_:

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> Como essa regra de política é direcionada a um grupo de recursos, o _mode_ na definição de política deve ser "All" em vez de "Indexed".

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Modificar os recursos para que herdem a marca CostCenter quando não estiver presente

A segunda necessidade de _CostCenter_ é que qualquer recurso herde a marca do grupo de recursos pai quando esta não estiver presente. Se a marca já está definida no recurso, mesmo se é diferente do grupo de recursos pai, ela deve ser mantida sozinha. A regra de política a seguir usa [Modificar](../concepts/effects.md#modify):

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

Essa regra de política usa a operação **add** em vez de **addOrReplace**, pois não queremos alterar o valor da marca se ela estiver presente ao [corrigir](../how-to/remediate-resources.md) recursos existentes. Ela também usa a função de modelo `[resourcegroup()]` para obter o valor da marca do grupo de recursos pai.

> [!NOTE]
> Como essa regra de política é direcionada a recursos que oferecem suporte a marcas, o _mode_ na definição da política deve ser "Indexed". Essa configuração também garante que essa política ignore grupos de recursos.

## <a name="configure-the-env-tag"></a>Configurar a marca Env

Em termos específicos a um ambiente do Azure gerenciado pelo Azure Policy, os requisitos da marca _Env_ exigem os seguintes resultados:

- Modificar a marca _Env_ no grupo de recursos com base no esquema de nomenclatura do grupo de recursos
- Modificar a marca _Env_ em todos os recursos do grupo de recursos para que seja a mesma do grupo de recursos pai

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Modificar a marca Env dos grupos de recursos com base no nome

É necessário ter uma política [Modificar](../concepts/effects.md#modify) para cada ambiente existente em seu ambiente do Azure. A política Modificar de cada um é semelhante a esta definição de política:

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        },
        {
            "field": "tags['Env']",
            "notEquals": "Production"
        }

    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> Como essa regra de política é direcionada a um grupo de recursos, o _mode_ na definição de política deve ser "All" em vez de "Indexed".

Essa política só corresponde os grupos de recursos ao esquema de nomenclatura de exemplo usado para recursos de produção de `prd-`. É possível alcançar um esquema de nomenclatura mais complexo com várias condições de **match** em vez do único **like** desse exemplo.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Modificar recursos para herdar a marca Env

O requisito de negócios exige que todos os recursos tenham a mesma marca _Env_ de seu grupo de recursos pai. Essa marca não pode ser substituída, portanto usaremos a operação **addOrReplace** com efeito o [Modificar](../concepts/effects.md#modify). O exemplo de política Modificar é semelhante à seguinte regra:

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> Como essa regra de política é direcionada a recursos que oferecem suporte a marcas, o _mode_ na definição da política deve ser "Indexed". Essa configuração também garante que essa política ignore grupos de recursos.

Essa regra de política procura qualquer recurso que não tenha o valor dos grupos de recursos pai para a marca _Env_ ou que não tenha a marca _Env_. Os recursos correspondentes têm a marca _Env_ definida com o valor dos grupos de recursos pai, mesmo que a marca já exista no recurso, mas com um valor diferente.

## <a name="assign-the-initiative-and-remediate-resources"></a>Atribuir a iniciativa e corrigir recursos

Após a criação das políticas de marca acima, junte-as em uma única iniciativa para governança de marcas e atribua-as a um grupo de gerenciamento ou assinatura. Em seguida, a iniciativa e as políticas incluídas avaliam a conformidade dos recursos existentes e alteram as solicitações de recursos novos ou atualizados que correspondam à propriedade **if** na regra de política. No entanto, a política não atualiza automaticamente os recursos existentes sem conformidade com as alterações de marca definidas.

Assim como as políticas [deployIfNotExists](../concepts/effects.md#deployifnotexists), a política **Modificar** usa tarefas de correção para alterar os recursos existentes sem conformidade. Siga as instruções em [Como corrigir recursos](../how-to/remediate-resources.md) para identificar seus recursos **Modificar** sem conformidade e corrija as marcas em sua taxonomia definida.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você estiver trabalhando com os recursos deste tutorial, use as etapas a seguir para excluir qualquer uma das atribuições ou definições criadas acima:

1. Selecione **Definições** (ou **Atribuições** se você estiver tentando excluir uma atribuição) em **Criação** no lado esquerdo da página Azure Policy.

1. Pesquisar pela nova iniciativa ou definição de política (ou atribuição) que você quer remover.

1. Clique com o botão direito na linha e selecione as reticências no final da definição ou da atribuição e selecione **Excluir Definição** (ou **Excluir Atribuição**).

## <a name="review"></a>Revisão

Neste tutorial, você aprendeu as seguintes tarefas:

> [!div class="checklist"]
> - Identificou seus requisitos de negócios
> - Mapeou cada requisito para uma definição de política
> - Agrupou as políticas de marca em uma iniciativa

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as estruturas das definições de políticas, consulte este artigo:

> [!div class="nextstepaction"]
> [Estrutura de definição do Azure Policy](../concepts/definition-structure.md)