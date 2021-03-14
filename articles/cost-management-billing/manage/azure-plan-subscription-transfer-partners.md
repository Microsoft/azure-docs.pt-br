---
title: Transferir assinaturas em um plano do Azure de um parceiro para outro (versão prévia)
description: Este artigo ajuda você a entender o que precisa saber antes e depois de transferir a propriedade da cobrança de sua assinatura do Azure.
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.openlocfilehash: d234227de98beeed6898697fb6ed8e0865546e16
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555850"
---
# <a name="transfer-subscriptions-under-an-azure-plan-from-one-partner-to-another-preview"></a>Transferir assinaturas em um plano do Azure de um parceiro para outro (versão prévia)

Este artigo ajuda você a entender o que precisa saber antes e depois de transferir a propriedade da cobrança de sua assinatura do Azure. Para iniciar a transferência de uma assinatura do Azure que está em um plano do Azure de um parceiro da Microsoft para outro, você precisa entrar em contato com seu parceiro. O parceiro lhe enviará instruções de como começar. Após a conclusão do processo de transferência, a propriedade da cobrança de sua assinatura será alterada.

## <a name="user-access"></a>Acesso do usuário

O acesso a usuários, grupos ou entidades de serviço existentes que foram atribuídos usando o Azure RBAC (controle de acesso baseado em função) não é afetado durante a transição. O [RBAC do Azure](../../role-based-access-control/overview.md) ajuda você a gerenciar quem tem acesso aos recursos do Azure, o que eles podem fazer com esses recursos e a quais áreas eles têm acesso. Seu novo parceiro não recebe nenhum acesso do Azure RBAC aos seus recursos pela transferência da assinatura. O parceiro anterior mantém o acesso do Azure RBAC.

Consequentemente, é importante que você remova o acesso de RBAC do Azure do parceiro antigo e adicione o acesso para o novo parceiro. Para saber mais sobre como conceder o acesso ao novo parceiro, confira [O que é o Azure RBAC (controle de acesso baseado em função do Azure)?](../../role-based-access-control/overview.md) Para obter mais informações sobre como remover o acesso do Azure RBAC do parceiro anterior, confira [Remover atribuições de função do Azure](../../role-based-access-control/role-assignments-remove.md).

Além disso, seu novo parceiro não obtém automaticamente acesso de [AOBO (Administrador em nome de)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) às suas assinaturas. O AOBO é necessário para que seu parceiro gerencie as assinaturas do Azure em seu nome. Para obter mais informações sobre privilégios do Azure, confira [Obter permissões para gerenciar o serviço ou a assinatura de um cliente](/partner-center/customers-revoke-admin-privileges).

## <a name="stop-a-transfer"></a>Interromper uma transferência

Depois de receber a confirmação de que uma solicitação de transferência foi enviada, você poderá usar uma das opções a seguir **se não quiser que a transferência continue**.

- Se a solicitação ainda não tiver sido aceita pelo parceiro atual, você poderá pedir que seu novo parceiro cancele a solicitação de transferência iniciada (quando o status estiver pendente).
- Peça ao parceiro atual que não tome nenhuma medida quando receber a solicitação de transferência. Sem o consentimento do parceiro atual, a transferência não poderá continuar. A solicitação vai expirar.
- Você pode _remover a relação de revendedor_ com o novo parceiro. Essa ação remove a capacidade de mover sua assinatura. Ela cancela efetivamente a solicitação.

Você também pode pedir ajuda, relatar uma atividade mal intencionada ou suspeita usando qualquer uma das opções no site [Microsoft Legal](https://www.microsoft.com/legal/). A opção de relatar uma preocupação está em Conformidade e ética.

## <a name="next-steps"></a>Próximas etapas

- Para conceder a seu novo parceiro acesso do Azure RBAC, confira [O que é o Azure RBAC (controle de acesso baseado em função do Azure)?](../../role-based-access-control/overview.md)
- [Obtenha permissões para gerenciar o serviço ou a assinatura de um cliente](/partner-center/customers-revoke-admin-privileges).