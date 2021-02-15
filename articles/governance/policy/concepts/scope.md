---
title: Entender o escopo no Azure Policy
description: Descreve o conceito de escopo no Azure Resource Manager e como ele se aplica a Azure Policy para controlar quais recursos Azure Policy são avaliados.
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 83eda330a35b007abfa37046a202b14728726849
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984434"
---
# <a name="understand-scope-in-azure-policy"></a>Entender o escopo no Azure Policy

Há várias configurações que determinam quais recursos podem ser avaliados e quais recursos são avaliados por Azure Policy. O conceito principal para esses controles é _escopo_.
Para obter uma visão geral de alto nível, consulte [escopo em Azure Resource Manager](../../../azure-resource-manager/management/overview.md#understand-scope).
Este artigo explica o impacto do _escopo_ em Azure Policy e é objetos e propriedades relacionados.

## <a name="definition-location"></a>Local da definição

O primeiro escopo de instância usado pelo Azure Policy é quando uma definição de política é criada. A definição pode ser salva em um grupo de gerenciamento ou em uma assinatura. O local determina o escopo ao qual a iniciativa ou a política pode ser atribuída. Os recursos devem estar dentro da hierarquia de recursos do local de definição para o destino da atribuição.

Se o local da definição for:

- Os recursos somente de **assinatura** dentro dessa assinatura podem ser atribuídos à definição de política.
- Recursos somente de **grupo de gerenciamento** dentro de grupos de gerenciamento filho e assinaturas filho podem ser atribuídos à definição de política. Se você planeja aplicar a definição de política a várias assinaturas, o local deve ser um grupo de gerenciamento que contém cada assinatura.

O local deve ser o contêiner de recursos compartilhado por todos os recursos para os quais você deseja usar a definição de política. Esse contêiner de recursos é normalmente um grupo de gerenciamento próximo ao grupo de gerenciamento raiz.

## <a name="assignment-scopes"></a>Escopos de atribuição

Uma atribuição tem várias propriedades que definem um escopo. O uso dessas propriedades determina qual recurso para Azure Policy avaliar e quais recursos contam para conformidade. Essas propriedades são mapeadas para os seguintes conceitos:

- Inclusão-uma hierarquia de recursos ou recurso individual deve ser avaliado quanto à conformidade pela definição. A `properties.scope` propriedade em um objeto de atribuição determina o que incluir e avaliar para fins de conformidade. Para obter mais informações, consulte [definição de atribuição](./assignment-structure.md).

- Exclusão-uma hierarquia de recursos ou recurso individual não deve ser avaliado quanto à conformidade pela definição. A `properties.notScopes` propriedade _array_ em um objeto de atribuição determina o que excluir. Os recursos dentro desses escopos não são avaliados ou incluídos na contagem de conformidade. Para obter mais informações, consulte [definição de atribuição – escopos excluídos](./assignment-structure.md#excluded-scopes).

Além das propriedades na atribuição de política, é o objeto de [isenção de política](./exemption-structure.md) . As isenções aprimoram a história do escopo fornecendo um método para identificar uma parte de uma atribuição para não ser avaliada.

- Isenção (**gratuito no recurso de visualização** )-uma hierarquia de recursos ou recurso individual deve ser avaliado quanto à conformidade pela definição, mas não será avaliado por um motivo, como ter uma renúncia ou ser atenuado por meio de outro método. Os recursos nesse estado são mostrados como **isentos** em relatórios de conformidade para que possam ser acompanhados. O objeto de isenção é criado na hierarquia de recursos ou recurso individual como um objeto filho, que determina o escopo da isenção. Uma hierarquia de recursos ou recurso individual pode ser isento de várias atribuições. A isenção pode ser configurada para expirar em uma agenda usando a `expiresOn` propriedade. Para obter mais informações, consulte [definição de isenção](./exemption-structure.md).

  > [!NOTE]
  > Devido ao impacto de conceder uma isenção para uma hierarquia de recursos ou recurso individual, as isenções têm medidas de segurança adicionais. Além de exigir a `Microsoft.Authorization/policyExemptions/write` operação na hierarquia de recursos ou recurso individual, o criador de uma isenção deve ter o `exempt/Action` verbo na atribuição de destino.

## <a name="scope-comparison"></a>Comparação de escopo

A tabela a seguir é uma comparação das opções de escopo:

| | Inclusão | Exclusão (não escopos) | Isenção |
|---|:---:|:---:|:---:|
|**Os recursos são avaliados** | &#10004; | - | - |
|**Objeto do Resource Manager** | - | - | &#10004; |
|**Requer a modificação do objeto de atribuição de política** | &#10004; | &#10004; | - |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [estrutura da definição de política](./definition-structure.md).
- Entenda como [criar políticas de forma programática](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).