---
title: Etapas de uma implantação de blueprint
description: Conheça as etapas que os serviços de especificações técnicas do Azure passa por durante a implantação.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d7000813b51fb9c9aae9a21cbded3ae0028e83f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684685"
---
# <a name="stages-of-a-blueprint-deployment"></a>Etapas de uma implantação de blueprint

Quando um plano gráfico é implantado, uma série de ações é obtida pelo serviço de especificações técnicas do Azure para implantar os recursos definidos no esquema do. Este artigo fornece detalhes sobre cada etapa envolve.

Implantação de projeto é disparada, atribuindo um plano gráfico para uma assinatura ou [atualizando uma atribuição existente](../how-to/update-existing-assignments.md). Durante a implantação, plantas realiza as seguintes etapas de alto nível:

> [!div class="checklist"]
> - Planos gráficos recebe direitos de proprietário
> - O objeto de atribuição de plano gráfico é criado
> - Opcional - plantas cria **atribuído pelo sistema** identidade gerenciada
> - A identidade gerenciada implanta artefatos do blueprint
> - Especificações técnicas de serviço e **atribuído pelo sistema** identidade gerenciada direitos são revogados

## <a name="blueprints-granted-owner-rights"></a>Planos gráficos recebe direitos de proprietário

A entidade de serviço do Azure BluePrint recebe direitos de proprietário para a assinatura atribuída ou assinaturas. A função concedida permite planos gráficos criar e revogar posteriormente, o [atribuído pelo sistema de identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md).

Os direitos são concedidos automaticamente se a atribuição é feita por meio do portal. No entanto, se a atribuição é feita por meio da API REST, concedendo direitos precisa estar com uma API separada chamar. A AppId do Azure Blueprint é `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, mas varia de acordo com a entidade de serviço por locatário. Use [API do Graph do Azure Active Directory](../../../active-directory/develop/active-directory-graph-api.md) e o ponto de extremidade REST [servicePrincipals](/graph/api/resources/serviceprincipal) para obter a entidade de serviço. Em seguida, conceda as especificações técnicas do Azure a _proprietário_ função por meio de [Portal](../../../role-based-access-control/role-assignments-portal.md), [CLI do Azure](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST API](../../../role-based-access-control/role-assignments-rest.md), ou uma [modelo do Resource Manager](../../../role-based-access-control/role-assignments-template.md).

O serviço de planos gráficos diretamente não implanta os recursos.

## <a name="the-blueprint-assignment-object-is-created"></a>O objeto de atribuição de plano gráfico é criado

Um usuário, grupo ou entidade de serviço atribui um plano gráfico para uma assinatura. O objeto de atribuição existe no nível da assinatura em que o plano gráfico foi atribuído. Criado pela implantação de recursos não são feitos no contexto da entidade de implantação.

Ao criar a atribuição de planta, tipo de [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md) está selecionado. O padrão é uma **atribuído pelo sistema** identidade gerenciada. Um **atribuída ao usuário** identidade gerenciada pode ser escolhida. Ao usar um **atribuída pelo usuário** gerenciado de identidade, ele deve ser definido e permissões antes da atribuição de planta é criada.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Opcional - plantas cria atribuído pelo sistema de identidade gerenciada

Quando [atribuído pelo sistema de identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md) é selecionado durante a atribuição, plantas criará a identidade e concede a identidade gerenciada a [proprietário](../../../role-based-access-control/built-in-roles.md#owner) função. Se um [atribuição existente será atualizada](../how-to/update-existing-assignments.md), plantas usa a identidade gerenciada criada anteriormente.

A identidade gerenciada relacionada à atribuição de planta é usada para implantar ou reimplantar os recursos definidos no esquema do. Este projeto evita as atribuições de inadvertidamente interferindo entre si.
Esse design também oferece suporte a [recurso de bloqueio](./resource-locking.md) recurso controlando a segurança de cada recurso implantado do plano gráfico.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>A identidade gerenciada implanta artefatos do blueprint

A identidade gerenciada, em seguida, dispara as implantações do Resource Manager dos artefatos no plano gráfico em definidas [pedido de sequenciamento](./sequencing-order.md). A ordem pode ser ajustada para garantir a artefatos dependentes de outros artefatos que são implantados na ordem correta.

Uma falha de acesso por uma implantação costuma ser o resultado do nível de acesso concedido à identidade gerenciada. O serviço de planos gráficos gerencia o ciclo de vida de segurança de **atribuído pelo sistema** identidade gerenciada. No entanto, o usuário é responsável por gerenciar os direitos e o ciclo de vida de um **atribuída ao usuário** identidade gerenciada.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Serviço de especificações técnicas e direitos de identidade gerenciada atribuído pelo sistema são revogados

Depois que as implantações estiverem concluídas, plantas Revoga os direitos do **atribuído pelo sistema** identidade gerenciada da assinatura. Em seguida, o serviço de planos gráficos Revoga os direitos da assinatura. Remoção de direitos impede plantas de um proprietário permanente em uma assinatura.

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar [parâmetros estáticos e dinâmicos](parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).