---
title: O que é o RBAC (controle de acesso baseado em função) para recursos do Azure? | Microsoft Docs
description: Obtenha uma visão geral do RBAC (controle de acesso baseado em função) para recursos do Azure. Use atribuições de função para controlar o acesso aos recursos no Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4a5352e96e522a8bd32c428957b3bbfd62c5fa9b
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275928"
---
# <a name="what-is-role-based-access-control-rbac-for-azure-resources"></a>O que é o RBAC (controle de acesso baseado em função) para recursos do Azure?

O gerenciamento de acesso para recursos de nuvem é uma função crítica para qualquer organização que esteja usando a nuvem. O controle de acesso baseado em funções (RBAC) ajuda a gerenciar quem tem acesso aos recursos do Azure, o que pode fazer com esses recursos e a quais áreas tem acesso.

O RBAC é um sistema de autorização baseado no [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) que fornece gerenciamento de acesso refinado dos recursos do Azure.

## <a name="what-can-i-do-with-rbac"></a>O que posso fazer com o RBAC?

Aqui estão alguns exemplos do que você pode fazer com o RBAC:

- Permitir que um usuário gerencie máquinas virtuais em uma assinatura e outro usuário gerencie redes virtuais
- Permitir que um grupo de DBA gerencie bancos de dados SQL em uma assinatura
- Permitir que um usuário gerencie todos os recursos em um grupo de recursos, como máquinas virtuais, sites e sub-redes
- Permitir que um aplicativo acesse todos os recursos em um grupo de recursos

## <a name="best-practice-for-using-rbac"></a>Melhor prática para uso do RBAC

Com o RBAC, você pode separar as tarefas dentro de sua equipe e conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos. Em vez de apresentar todos irrestrito permissões em sua assinatura do Azure ou recursos, você pode permitir apenas determinadas ações para um escopo específico.

Ao planejar sua estratégia de controle de acesso, uma melhor prática é conceder aos usuários o privilégio mínimo para realizarem seus trabalhos. O diagrama a seguir mostra um padrão sugerido para o uso de RBAC.

![RBAC e privilégio mínimo](./media/overview/rbac-least-privilege.png)

## <a name="how-rbac-works"></a>Como funciona o RBAC

A maneira de controlar o acesso aos recursos usando RBAC é criar atribuições de função. Esse é um conceito fundamental que deve ser entendido, isto é, como as permissões são aplicadas. Uma atribuição de função consiste em três elementos: entidade de segurança, definição de função e escopo.

### <a name="security-principal"></a>Entidade de segurança

Uma *entidade de segurança* é um objeto que representa um usuário, grupo, entidade de serviço ou uma identidade gerenciada que está solicitando acesso aos recursos do Azure.

![Entidade de segurança para uma atribuição de função](./media/overview/rbac-security-principal.png)

- Usuário – Um indivíduo que tem um perfil no Azure Active Directory. Você também pode atribuir funções a usuários em outros locatários. Para obter informações sobre usuários de outras organizações, consulte [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).
- Grupo - Um grupo de usuários criados no Azure Active Directory. Quando você atribuir uma função a um grupo, todos os usuários dentro desse grupo têm essa função. 
- Entidade de serviço - Uma identidade de segurança usada por aplicativos ou serviços para acessar recursos específicos do Azure. Você pode pensar nela como uma *identidade do usuário* (nome de usuário e senha ou certificado) para um aplicativo.
- Identidade gerenciada - uma identidade no Azure Active Directory que é gerenciada automaticamente pelo Azure. Normalmente, você usa [identidades gerenciadas](../active-directory/managed-identities-azure-resources/overview.md) durante o desenvolvimento de aplicativos em nuvem para gerenciar as credenciais de autenticação nos serviços do Azure.

### <a name="role-definition"></a>Definição de função

Uma *definição de função* é um conjunto de permissões. Às vezes, é chamada apenas de *função*. Uma definição de função lista as operações que podem ser executadas, como leitura, gravação e exclusão. Funções podem ser de alto nível, como proprietário, ou específicas, como leitor de máquina virtual.

![Definição de função para uma atribuição de função](./media/overview/rbac-role-definition.png)

O Azure inclui várias [funções internas](built-in-roles.md) que você pode usar. A seguir são listadas quatro funções internas fundamentais. As três primeiras se aplicam a todos os tipos de recursos.

- [Proprietário](built-in-roles.md#owner) - Possui acesso total a todos os recursos, inclusive o direito de delegar acesso a outros usuários.
- [Colaborador](built-in-roles.md#contributor) - Pode criar e gerenciar todos os tipos de recursos do Azure, mas não pode conceder acesso a outras pessoas.
- [Leitor](built-in-roles.md#reader) - Pode exibir os recursos existentes do Azure.
- [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator) - Permite gerenciar o acesso do usuário aos recursos do Azure.

As demais funções internas permitem o gerenciamento de recursos específicos do Azure. Por exemplo, a função [Colaborador de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) permite que um usuário crie e gerencie máquinas virtuais. Se as funções internas não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias [funções personalizadas para recursos do Azure](custom-roles.md).

O Azure tem operações de dados que permitem a você conceder acesso a dados em um objeto. Por exemplo, se um usuário tem acesso de leitura de dados para uma conta de armazenamento, eles podem ler blobs ou mensagens dentro dessa conta de armazenamento. Para obter mais informações, confira [Noções básicas sobre definições de função para recursos do Azure](role-definitions.md).

### <a name="scope"></a>Escopo

*Escopo* é o conjunto de recursos ao qual o acesso se aplica. Quando você atribui uma função, você pode limitar ainda mais as ações permitidas definindo um escopo. Isso será útil se você quiser tornar alguém um [colaborador do site](built-in-roles.md#website-contributor), mas apenas para um grupo de recursos.

No Azure, você pode especificar um escopo em vários níveis: [grupo de gerenciamento](../governance/management-groups/index.md), assinatura, grupo de recursos ou recurso. Os escopos são estruturados em uma relação pai-filho.

![Escopo para uma atribuição de função](./media/overview/rbac-scope.png)

Quando você concede acesso a um escopo pai, essas permissões são herdadas pelos escopos filho. Por exemplo:

- Se você atribuir a função [Proprietário](built-in-roles.md#owner) a um usuário no escopo do grupo de gerenciamento, esse usuário poderá gerenciar tudo em todas as assinaturas no grupo de gerenciamento.
- Se você atribuir a função [Leitor](built-in-roles.md#reader) função a um grupo no escopo da assinatura, os membros desse grupo pode exibir todos os grupo de recursos e recursos na assinatura.
- Se você atribuir a função [Colaborador](built-in-roles.md#contributor) a um aplicativo no escopo do grupo de recursos, ele pode gerenciar recursos de todos os tipos nesse mesmo grupo de recursos, mas não em outros grupos de recursos na assinatura.

### <a name="role-assignments"></a>Atribuições de função

Uma *atribuição de função* é o processo de associar uma definição de função a um usuário, grupo, entidade de serviço ou identidade gerenciada em um escopo específico com a finalidade de conceder acesso. O acesso é concedido criando uma atribuição de função, e é revogado removendo uma atribuição de função.

O diagrama a seguir mostra um exemplo de uma atribuição de função. Neste exemplo, o grupo de Marketing foi atribuído à função [Colaborador](built-in-roles.md#contributor) para o grupo de recursos vendas farmacêuticas. Isso significa que os usuários do grupo de Marketing podem criar ou gerenciar qualquer recurso do Azure no grupo de recursos de vendas do setor farmacêutico. Os usuários de Marketing não possuem acesso a recursos fora do grupo de recursos de vendas do setor farmacêutico, a menos que sejam parte de outra atribuição de função.

![Atribuição de função para controlar o acesso](./media/overview/rbac-overview.png)

Você pode criar atribuições de função usando o portal do Azure, CLI do Azure, Azure PowerShell, SDKs do Azure ou APIs REST. Em cada assinatura, você pode ter até 2.000 atribuições de função. Para criar e remover as atribuições de função, você deve ter a permissão `Microsoft.Authorization/roleAssignments/*`. Essa permissão deve ser concedida pelas funções [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator).

## <a name="multiple-role-assignments"></a>Atribuições de função múltiplas

O que acontece se você tem várias atribuições de função sobrepostas? O RBAC é um modelo aditivo e, portanto, suas permissões efetivas são a adição das atribuições de função. Considere o exemplo a seguir em que um usuário recebe a função Colaborador no escopo da assinatura e a função Leitor em um grupo de recursos. A adição das permissões de Colaborador e das permissões de Leitor é, efetivamente, a função Colaborador para o grupo de recursos. Portanto, nesse caso, a atribuição de função Leitor não tem nenhum impacto.

![Atribuições de função múltiplas](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>Negar atribuições

Anteriormente, o RBAC era um modelo somente de permissão, sem negação, mas agora ele dá suporte a atribuições de negações, com limitações. Semelhante a uma atribuição de função, uma *atribuição de negação* anexa um conjunto de ações de negação a um usuário, grupo, entidade de serviço ou identidade gerenciada em um escopo específico com o objetivo de negar acesso. Uma atribuição de função define um conjunto de ações que são *permitidas*, enquanto uma atribuição de negação define um conjunto de ações que *não são permitidas*. Em outras palavras, as atribuições de negação impedem que os usuários executem ações especificadas, mesmo quando uma atribuição de função lhes concede acesso. As atribuições de negação têm precedência sobre as atribuições de função. Para obter mais informações, confira [Compreender atribuições de negação de recursos do Azure](deny-assignments.md).

## <a name="how-rbac-determines-if-a-user-has-access-to-a-resource"></a>Como o RBAC determina se um usuário tem acesso a um recurso

A seguir estão as etapas gerais que o RBAC usa para determinar se você tem acesso a um recurso no plano de gerenciamento. Convém entender isso quando você está tentando solucionar problemas de acesso.

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

- [Início Rápido: Exibir o acesso que um usuário tem aos recursos do Azure usando o portal do Azure](check-access.md)
- [Gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure](role-assignments-portal.md)
- [Entender as diferentes funções no Azure](rbac-and-directory-admin-roles.md)
- [Adoção da Nuvem Empresarial: Gerenciamento de acesso aos recursos no Azure](/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access)
