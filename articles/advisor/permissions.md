---
title: Permissões no Azure Advisor
description: As permissões do Advisor e como elas podem bloquear sua capacidade de configurar assinaturas ou adiar ou ignorar recomendações.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 402a21c47c4cba8f747d5d4601f9c95034c99262
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91712929"
---
# <a name="permissions-in-azure-advisor"></a>Permissões no Azure Advisor

O Azure Advisor fornece recomendações com base no uso e na configuração de seus recursos e assinaturas do Azure. O Advisor usa as [funções internas](../role-based-access-control/built-in-roles.md) fornecidas pelo Azure [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md) para gerenciar seu acesso a recomendações e recursos do Advisor. 

## <a name="roles-and-their-access"></a>Funções e seu acesso

A tabela a seguir define as funções e o acesso que eles têm no Advisor:

| **Função** | **Exibir recomendações** | **Editar regras** | **Editar configuração de assinatura** | **Editar configuração do grupo de recursos**| **Ignorar e adiar recomendações**|
|---|:---:|:---:|:---:|:---:|:---:|
|Proprietário da assinatura|**X**|**X**|**X**|**X**|**X**|
|Colaborador da assinatura|**X**|**X**|**X**|**X**|**X**|
|Leitor de assinatura|**X**|--|--|--|--|
|Proprietário do grupo de recursos|**X**|--|--|**X**|**X**|
|Colaborador do grupo de recursos|**X**|--|--|**X**|**X**|
|Leitor do grupo de recursos|**X**|--|--|--|--|
|Proprietário do recurso|**X**|--|--|--|**X**|
|Colaborador de recursos|**X**|--|--|--|**X**|
|Leitor de recursos|**X**|--|--|--|--|

> [!NOTE]
> O acesso para exibir recomendações depende de seu acesso ao recurso afetado da recomendação.

## <a name="permissions-and-unavailable-actions"></a>Permissões e ações não disponíveis

A falta de permissões adequadas pode bloquear sua capacidade de executar ações no Advisor. A seguir estão alguns problemas comuns.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Não é possível configurar assinaturas ou grupos de recursos

Ao tentar configurar assinaturas ou grupos de recursos no Advisor, você pode ver que a opção para incluir ou excluir está desabilitada. Esse status indica que você não tem um nível suficiente de permissão para esse grupo de recursos ou assinatura. Para resolver esse problema, saiba como [conceder acesso de usuário](../role-based-access-control/quickstart-assign-role-user-portal.md).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Não é possível adiar ou ignorar uma recomendação

Se você receber um erro ao tentar adiar ou descartar uma recomendação, talvez você não tenha permissões suficientes. Verifique se você tem pelo menos acesso de colaborador ao recurso afetado da recomendação que você está adiando ou descartando. Para resolver esse problema, saiba como [conceder acesso de usuário](../role-based-access-control/quickstart-assign-role-user-portal.md).

## <a name="next-steps"></a>Próximas etapas

Este artigo deu uma visão geral de como o Advisor usa o RBAC do Azure para controlar as permissões de usuário e como resolver problemas comuns. Para saber mais sobre o Assistente, consulte:

- [O que é o Azure Advisor?](./advisor-overview.md)
- [Introdução ao Azure Advisor](./advisor-get-started.md)
