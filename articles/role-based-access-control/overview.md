---
title: O que é o RBAC do Azure (controle de acesso baseado em função do Azure)?
description: Obtenha uma visão geral do RBAC do Azure (controle de acesso baseado em função do Azure). Use atribuições de função para controlar o acesso aos recursos no Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: overview
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.custom: contperf-fy21q1, azuread-video-2020
ms.openlocfilehash: a39ae904bb1dd36c3d3e19e8f51ff24e8d975f9b
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033911"
---
# <a name="what-is-azure-role-based-access-control-azure-rbac"></a>O que é o RBAC do Azure (controle de acesso baseado em função do Azure)?

O gerenciamento de acesso para recursos de nuvem é uma função crítica para qualquer organização que esteja usando a nuvem. O RBAC do Azure (controle de acesso baseado em funções do Azure) ajuda a gerenciar quem tem acesso aos recursos do Azure, o que pode fazer com esses recursos e a quais áreas tem acesso.

O RBAC do Azure é um sistema de autorização baseado no [Azure Resource Manager](../azure-resource-manager/management/overview.md) que fornece gerenciamento de acesso refinado dos recursos do Azure.

Este vídeo fornece uma visão geral rápida do RBAC do Azure.

>[!VIDEO https://www.youtube.com/embed/Dzhm-garKBM]

## <a name="what-can-i-do-with-azure-rbac"></a>O que posso fazer com o RBAC do Azure?

Aqui estão alguns exemplos do que você pode fazer com o RBAC do Azure:

- Permitir que um usuário gerencie máquinas virtuais em uma assinatura e outro usuário gerencie redes virtuais
- Permitir que um grupo de DBA gerencie bancos de dados SQL em uma assinatura
- Permitir que um usuário gerencie todos os recursos em um grupo de recursos, como máquinas virtuais, sites e sub-redes
- Permitir que um aplicativo acesse todos os recursos em um grupo de recursos

## <a name="how-azure-rbac-works"></a>Como o RBAC do Azure funciona

A maneira de controlar o acesso aos recursos usando RBAC do Azure é criar atribuições de função. Esse é um conceito fundamental que deve ser entendido: como as permissões são aplicadas. Uma atribuição de função consiste em três elementos: entidade de segurança, definição de função e escopo.

### <a name="security-principal"></a>Entidade de segurança

Uma *entidade de segurança* é um objeto que representa um usuário, grupo, entidade de serviço ou uma identidade gerenciada que está solicitando acesso aos recursos do Azure. Você pode atribuir uma função a qualquer uma dessas entidades de segurança.

![Entidade de segurança para uma atribuição de função](./media/shared/rbac-security-principal.png)

### <a name="role-definition"></a>Definição de função

Uma *definição de função* é um conjunto de permissões. Normalmente ela é chamada apenas de *função*. Uma definição de função lista as operações que podem ser executadas, como leitura, gravação e exclusão. Funções podem ser de alto nível, como proprietário, ou específicas, como leitor de máquina virtual.

![Definição de função para uma atribuição de função](./media/shared/rbac-role-definition.png)

O Azure inclui várias [funções internas](built-in-roles.md) que você pode usar. Por exemplo, a função [Colaborador de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) permite que um usuário crie e gerencie máquinas virtuais. Se as funções internas não atenderem às necessidades específicas de sua organização, você poderá criar [funções personalizadas do Azure](custom-roles.md) próprias.

Este vídeo fornece uma visão geral rápida das funções internas e das funções personalizadas.

>[!VIDEO https://www.youtube.com/embed/I1mefHptRgo]

O Azure tem operações de dados que permitem a você conceder acesso a dados em um objeto. Por exemplo, se um usuário tem acesso de leitura de dados para uma conta de armazenamento, eles podem ler blobs ou mensagens dentro dessa conta de armazenamento.

Para saber mais, veja [Noções básicas sobre definições de função do Azure](role-definitions.md).

### <a name="scope"></a>Escopo

*Escopo* é o conjunto de recursos ao qual o acesso se aplica. Quando você atribui uma função, você pode limitar ainda mais as ações permitidas definindo um escopo. Isso será útil se você quiser tornar alguém um [colaborador do site](built-in-roles.md#website-contributor), mas apenas para um grupo de recursos.

No Azure, você pode especificar um escopo em quatro níveis: [grupo de gerenciamento](../governance/management-groups/overview.md), assinatura, [grupo de recursos](../azure-resource-manager/management/overview.md#resource-groups) ou recurso. Os escopos são estruturados em uma relação pai-filho. Você pode atribuir funções em qualquer um desses níveis de escopo.

![Escopo para uma atribuição de função](./media/shared/rbac-scope.png)

Para obter mais informações sobre escopo, confira [Noções básicas de escopo](scope-overview.md).

### <a name="role-assignments"></a>Atribuições de função

Uma *atribuição de função* é o processo de associar uma definição de função a um usuário, grupo, entidade de serviço ou identidade gerenciada em um escopo específico com a finalidade de conceder acesso. O acesso é concedido criando uma atribuição de função, e é revogado removendo uma atribuição de função.

O diagrama a seguir mostra um exemplo de uma atribuição de função. Neste exemplo, o grupo de Marketing foi atribuído à função [Colaborador](built-in-roles.md#contributor) para o grupo de recursos vendas farmacêuticas. Isso significa que os usuários do grupo de Marketing podem criar ou gerenciar qualquer recurso do Azure no grupo de recursos de vendas do setor farmacêutico. Os usuários de Marketing não possuem acesso a recursos fora do grupo de recursos de vendas do setor farmacêutico, a menos que sejam parte de outra atribuição de função.

![Atribuição de função para controlar o acesso](./media/overview/rbac-overview.png)

Você pode criar atribuições de função usando o portal do Azure, CLI do Azure, Azure PowerShell, SDKs do Azure ou APIs REST.

Para obter mais informações, confira [Etapas para adicionar uma atribuição de função](role-assignments-steps.md).

## <a name="multiple-role-assignments"></a>Atribuições de função múltiplas

O que acontece se você tem várias atribuições de função sobrepostas? O RBAC do Azure é um modelo aditivo e, portanto, suas permissões efetivas são a soma das atribuições de função. Considere o exemplo a seguir em que um usuário recebe a função Colaborador no escopo da assinatura e a função Leitor em um grupo de recursos. A soma das permissões de Colaborador e das permissões de Leitor é, efetivamente, a função Colaborador para o grupo de recursos. Portanto, nesse caso, a atribuição de função Leitor não tem nenhum impacto.

![Atribuições de função múltiplas](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>Negar atribuições

Anteriormente, o RBAC do Azure era um modelo somente de permissão, sem negação, mas agora ele é compatível com atribuições de negações, com limitações. Semelhante a uma atribuição de função, uma *atribuição de negação* anexa um conjunto de ações de negação a um usuário, grupo, entidade de serviço ou identidade gerenciada em um escopo específico com o objetivo de negar acesso. Uma atribuição de função define um conjunto de ações que são *permitidas*, enquanto uma atribuição de negação define um conjunto de ações que *não são permitidas*. Em outras palavras, as atribuições de negação impedem que os usuários executem ações especificadas, mesmo quando uma atribuição de função lhes concede acesso. As atribuições de negação têm precedência sobre as atribuições de função.

Para obter mais informações, confira [Compreender atribuições de negação do Azure](deny-assignments.md).

## <a name="how-azure-rbac-determines-if-a-user-has-access-to-a-resource"></a>Como o RBAC do Azure determina se um usuário tem acesso a um recurso

Veja a seguir as etapas gerais que o RBAC do Azure usa para determinar se você tem acesso a um recurso no plano de gerenciamento. Convém entender isso quando você está tentando solucionar problemas de acesso.

1. Um usuário (ou uma entidade de serviço) adquire um token do Azure Resource Manager.

    O token inclui as associações a um grupo do usuário (incluindo associações de grupo transitivas).

1. O usuário faz uma chamada à API REST para o Azure Resource Manager com o token anexado.

1. O Azure Resource Manager recupera todas as atribuições de função e as atribuições de negação que se aplicam ao recurso no qual a ação está sendo realizada.

1. O Azure Resource Manager restringe as atribuições de função que se aplicam a esse usuário ou ao seu grupo e determina as funções que o usuário tem para este recurso.

1. O Azure Resource Manager determina se a ação na chamada à API está incluída nas funções que o usuário tem para este recurso.

1. Se o usuário não tem uma função com a ação no escopo solicitado, o acesso não é concedido. Caso contrário, o Azure Resource Manager verifica se uma atribuição de negação se aplica.

1. Se houver uma atribuição de negação aplicável, o acesso será bloqueado. Caso contrário, o acesso será permitido.

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Próximas etapas

- [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](role-assignments-portal.md)
- [Entender as diferentes funções](rbac-and-directory-admin-roles.md)
- [Cloud Adoption Framework: Gerenciamento de acesso aos recursos no Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
