---
title: Configurar seu ambiente para o operador Blueprint
description: Saiba como configurar seu ambiente do Azure para uso com a função interna do operador Blueprint do Azure.
ms.date: 02/05/2021
ms.topic: how-to
ms.openlocfilehash: 0e0f6680ab39481a480919af10fadc0f7103a1fb
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591594"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>Configurar seu ambiente para um operador de Blueprint

O gerenciamento de suas definições de plantas e atribuições de Blueprint pode ser atribuído a equipes diferentes. É comum que um arquiteto ou uma equipe de governança seja responsável pelo gerenciamento do ciclo de vida de suas definições de plantas, enquanto uma equipe de operações é responsável por gerenciar atribuições dessas definições de Blueprints controladas centralmente.

A função interna do **operador Blueprint** foi projetada especificamente para uso nesse tipo de cenário. A função permite que as equipes do tipo operações gerenciem a atribuição das definições do plano gráfico das organizações, mas não a capacidade de modificá-las. Isso requer algumas configurações em seu ambiente do Azure e este artigo explica as etapas necessárias.

## <a name="grant-permission-to-the-blueprint-operator"></a>Conceder permissão ao operador Blueprint

A primeira etapa é conceder a função **operador de Blueprint** para a conta ou grupo de segurança (recomendado) que vai atribuir plantas. Essa ação deve ser feita no nível mais alto da hierarquia do grupo de gerenciamento que abrange todos os grupos de gerenciamento e assinaturas aos quais a equipe de operações deve ter acesso de atribuição Blueprint. É recomendável seguir o princípio de privilégios mínimos ao conceder essas permissões.

1. Aconselhável [Criar um grupo de segurança e adicionar membros](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. [Adicionar uma atribuição de função](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) do **operador Blueprint** à conta ou ao grupo de segurança

## <a name="user-assign-managed-identity"></a>Identidade gerenciada de atribuição de usuário

Uma definição de Blueprint pode usar identidades gerenciadas atribuídas pelo sistema ou pelo usuário. No entanto, ao usar a função de **operador Blueprint** , a definição de Blueprint precisa ser configurada para usar uma identidade gerenciada atribuída pelo usuário. Além disso, a conta ou grupo de segurança que recebeu a função **operador de Blueprint** precisa receber a função **operador de identidade gerenciada** na identidade gerenciada atribuída pelo usuário. Sem essa permissão, as atribuições do Blueprint falham devido à falta de permissões.

1. [Crie uma identidade gerenciada atribuída pelo usuário](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) para uso por um plano gráfico atribuído.

1. Conceda à identidade gerenciada atribuída pelo usuário qualquer função ou permissão exigida pela definição do Blueprint para o escopo pretendido.

1. [Adicione uma atribuição de função](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) do **operador de identidade gerenciada** à conta ou ao grupo de segurança. Escopo a atribuição de função para a nova identidade gerenciada atribuída pelo usuário.

1. Como o **operador Blueprint**, [atribua um plano gráfico](../create-blueprint-portal.md#assign-a-blueprint) que usa a nova identidade gerenciada atribuída pelo usuário.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).