---
title: Etapas de uma implantação de blueprint
description: Conheça as etapas relacionadas à segurança e ao artefato que os serviços de plantas do Azure passam durante a criação de uma atribuição de Blueprint.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: e686dec1c9d79e42dafee17d8a937284aac4fdd6
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918544"
---
# <a name="stages-of-a-blueprint-deployment"></a>Etapas de uma implantação de blueprint

Quando um plano gráfico é implantado, uma série de ações é realizada pelo serviço de plantas do Azure para implantar os recursos definidos no plano gráfico. Este artigo fornece detalhes sobre o que cada etapa envolve.

A implantação Blueprint é disparada atribuindo um plano gráfico a uma assinatura ou [atualizando uma atribuição existente](../how-to/update-existing-assignments.md). Durante a implantação, os planos gráficos do Azure executam as seguintes etapas de alto nível:

> [!div class="checklist"]
> - Os planos gráficos do Azure concederam direitos de proprietário
> - O objeto de atribuição Blueprint é criado
> - Opcional – plantas do Azure cria a identidade gerenciada **atribuída pelo sistema**
> - A identidade gerenciada implanta artefatos do Blueprint
> - O serviço de plantas do Azure e os direitos de identidade gerenciados **atribuídos pelo sistema** são revogados

## <a name="azure-blueprints-granted-owner-rights"></a>Os planos gráficos do Azure concederam direitos de proprietário

A entidade de serviço de plantas do Azure recebe direitos de proprietário para a assinatura ou assinaturas atribuídas quando uma identidade gerenciada de identidade gerenciada [atribuída pelo sistema](../../../active-directory/managed-identities-azure-resources/overview.md) é usada. A função concedida permite que os planos gráficos do Azure criem e, posteriormente, revoguem a identidade gerenciada **atribuída pelo sistema** . Se estiver usando uma identidade gerenciada **atribuída pelo usuário** , a entidade de serviço dos planos gráficos do Azure não obterá nem precisará de direitos de proprietário na assinatura.

Os direitos serão concedidos automaticamente se a atribuição for feita por meio do Portal. No entanto, se a atribuição for feita por meio da API REST, a concessão dos direitos precisará ser feita com uma chamada à API separada. A AppId dos planos gráficos do Azure é `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` , mas a entidade de serviço varia de acordo com o locatário. Use [Azure Active Directory API do Graph](../../../active-directory/develop/active-directory-graph-api.md) e os serviços [de ponto de](/graph/api/resources/serviceprincipal) extremidade REST para obter a entidade de serviço. Em seguida, conceda ao Azure plantas a função de _proprietário_ por meio do [portal](../../../role-based-access-control/role-assignments-portal.md), [CLI do Azure](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [API REST](../../../role-based-access-control/role-assignments-rest.md)ou um [modelo de Azure Resource Manager](../../../role-based-access-control/role-assignments-template.md).

O serviço de plantas do Azure não implanta diretamente os recursos.

## <a name="the-blueprint-assignment-object-is-created"></a>O objeto de atribuição Blueprint é criado

Um usuário, grupo ou entidade de serviço atribui um plano gráfico a uma assinatura. O objeto de atribuição existe no nível de assinatura em que o plano gráfico foi atribuído. Os recursos criados pela implantação não são feitos no contexto da entidade de implantação.

Ao criar a atribuição Blueprint, o tipo de [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md) é selecionado. O padrão é uma identidade gerenciada **atribuída pelo sistema** . Uma identidade gerenciada **atribuída pelo usuário** pode ser escolhida. Ao usar uma identidade gerenciada **atribuída pelo usuário** , ela deve ser definida e ter permissões concedidas antes que a atribuição Blueprint seja criada. As funções internas do [operador](../../../role-based-access-control/built-in-roles.md#blueprint-operator) de [proprietário](../../../role-based-access-control/built-in-roles.md#owner) e do Blueprint têm a `blueprintAssignment/write` permissão necessária para criar uma atribuição que usa uma identidade gerenciada **atribuída pelo usuário** .

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>Opcional – plantas do Azure cria a identidade gerenciada atribuída pelo sistema

Quando a [identidade gerenciada atribuída pelo sistema](../../../active-directory/managed-identities-azure-resources/overview.md) é selecionada durante a atribuição, os planos gráficos do Azure criam a identidade e concedem a identidade gerenciada a função de [proprietário](../../../role-based-access-control/built-in-roles.md#owner) . Se uma [atribuição existente for atualizada](../how-to/update-existing-assignments.md), os planos gráficos do Azure usarão a identidade gerenciada criada anteriormente.

A identidade gerenciada relacionada à atribuição Blueprint é usada para implantar ou reimplantar os recursos definidos no plano gráfico. Esse design evita que as atribuições interfiram inadvertidamente entre si.
Esse design também dá suporte ao recurso de [bloqueio de recursos](./resource-locking.md) controlando a segurança de cada recurso implantado do plano gráfico.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>A identidade gerenciada implanta artefatos do Blueprint

Em seguida, a identidade gerenciada dispara as implantações do Resource Manager dos artefatos dentro do plano gráfico na [ordem de sequenciamento](./sequencing-order.md)definida. A ordem pode ser ajustada para garantir que os artefatos dependentes de outros artefatos sejam implantados na ordem correta.

Uma falha de acesso por uma implantação é geralmente o resultado do nível de acesso concedido à identidade gerenciada. O serviço de plantas do Azure gerencia o ciclo de vida de segurança da identidade gerenciada **atribuída pelo sistema** . No entanto, o usuário é responsável por gerenciar os direitos e o ciclo de vida de uma identidade gerenciada **atribuída pelo usuário** .

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>O serviço Blueprint e direitos de identidade gerenciada atribuídos pelo sistema são revogados

Depois que as implantações forem concluídas, as plantas do Azure revogarão os direitos da identidade gerenciada **atribuída pelo sistema** da assinatura. Em seguida, o serviço de plantas do Azure revoga seus direitos da assinatura. A remoção de direitos impede que os planos gráficos do Azure se tornem um proprietário permanente em uma assinatura.

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar [parâmetros estáticos e dinâmicos](./parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](./sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](./resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).
