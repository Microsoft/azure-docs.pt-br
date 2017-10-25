---
title: "Gerencie o acesso e permissões com RBAC – RBAC do Azure | Microsoft Docs"
description: "Introdução ao gerenciamento de acesso com o controle de acesso baseado em função do Azure no Portal do Azure. Use as atribuições de função para atribuir permissões em seu diretório."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: 0462fe8ff75bdda397decb301c459795886e9e58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-role-based-access-control-in-the-azure-portal"></a>Introdução ao Controle de Acesso Baseado em Função no Portal do Azure
As empresas direcionadas a segurança devem focar em fornecer aos funcionários as permissões exatas necessárias. Muitas permissões podem expor uma conta a ataques. Permissões insuficientes significa que os funcionários não podem ter seu trabalho feito com eficiência. O RBAC (controle de acesso baseado em função) do Azure ajuda a resolver esse problema ao oferecer o gerenciamento de acesso refinado para o Azure.

Com o RBAC, você pode separar as tarefas dentro de sua equipe e conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos. Em vez de apresentar todos irrestrito permissões em sua assinatura do Azure ou recursos, você pode permitir apenas determinadas ações. Por exemplo, use o RBAC para permitir que um funcionário gerencie máquinas virtuais em uma assinatura, enquanto outro pode gerenciar bancos de dados SQL dentro da mesma assinatura.

## <a name="basics-of-access-management-in-azure"></a>Noções básicas de gerenciamento de acesso no Azure
Cada assinatura do Azure está associada com um diretório do Azure Active Directory (AD). Usuários, grupos e aplicativos do diretório podem gerenciar recursos na assinatura do Azure. Atribua esses direitos de acesso usando o portal do Azure, ferramentas de linha de comando do Azure e APIs de gerenciamento do Azure.

Conceda acesso ao atribuir a função RBAC apropriada a usuários, grupos e aplicativos em determinado escopo. O escopo de uma atribuição de função pode ser uma assinatura, um grupo de recursos ou um único recurso. Uma função atribuída a um escopo pai também concede acesso aos filhos contidos nele. Por exemplo, um usuário com acesso a um grupo de recursos pode gerenciar todos os recursos que ele contém, como sites, máquinas virtuais e sub-redes.

![Relação entre os elementos do Azure Active Directory - diagrama](./media/role-based-access-control-what-is/rbac_aad.png)

A função RBAC que você atribui determina quais recursos o usuário (grupo ou aplicativo) pode gerenciar dentro do escopo.

## <a name="built-in-roles"></a>Funções internas
O RBAC do Azure tem três funções básicas que se aplicam a todos os tipos de recurso:

* **proprietário** tem acesso total a todos os recursos, inclusive o direito de delegar acesso a outros usuários.
* **colaborador** pode criar e gerenciar todos os tipos de recursos do Azure, mas não pode conceder acesso a outras pessoas.
* **leitor** pode exibir os recursos existentes do Azure.

As demais funções RBAC no Azure permitem o gerenciamento de recursos específicos do Azure. Por exemplo, a função Colaborador de Máquina Virtual permite que o usuário crie e gerencie máquinas virtuais. Ela não lhes concede acesso à rede virtual ou à sub-rede com qual a máquina virtual se conecta. 

[Funções internas RBAC](role-based-access-built-in-roles.md) lista as funções disponíveis no Azure. Ela especifica as operações e o escopo que cada função interna concede aos usuários. Se você pretende definir suas próprias funções para ter ainda mais controle, confira como criar [Funções personalizadas no RBAC do Azure](role-based-access-control-custom-roles.md).

## <a name="resource-hierarchy-and-access-inheritance"></a>Hierarquia de recursos e herança de acesso
* Cada **assinatura** do Azure pertence somente a um diretório. (Porém, cada diretório pode ter mais de uma assinatura).
* Cada **grupo de recursos** pertence somente a uma assinatura.
* Cada **recurso** pertence somente a um grupo de recursos.

O acesso concedido em escopos pai é herdado em escopos filho. Por exemplo:

* Você pode atribuir a função Leitor a um grupo do Azure AD no escopo de assinatura. Os membros desse grupo podem exibir cada recurso e grupo de recursos na assinatura.
* Você pode atribuir a função Colaborador para um aplicativo no escopo do grupo de recursos. Ele pode gerenciar recursos de todos os tipos nesse grupo de recursos, mas não em outros grupos de recursos na assinatura.

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>RBAC do Azure versus administrador de assinatura clássico
Os administradores de assinatura clássicos têm acesso completo à assinatura do Azure. Eles podem gerenciar recursos usando o [Portal do Azure](https://portal.azure.com) com APIs do Azure Resource Manager ou o [Portal Clássico do Azure](https://manage.windowsazure.com) e o modelo de implantação clássico do Azure. No modelo RBAC, aos administradores clássicos é atribuída a função de proprietário no nível de assinatura.

Somente o Portal do Azure e as novas APIs do Azure Resource Manager dão suporte ao RBAC do Azure. Os usuários e aplicativos aos quais são atribuídas funções RBAC não podem usar o portal de gerenciamento clássico e o modelo de implantação clássico do Azure.

## <a name="authorization-for-management-vs-data-operations"></a>Autorização para o gerenciamento versus operações de dados
O RBAC do Azure tem suporte somente para as operações de gerenciamento dos recursos do Azure no Portal do Azure e nas APIs do Azure Resource Manager. Ele não pode autorizar todas as operações no nível de dados para os recursos do Azure. Por exemplo, você pode autorizar alguém para gerenciar Contas de Armazenamento, mas não para blobs ou tabelas em uma Conta de Armazenamento. Da mesma forma, um banco de dados SQL pode ser gerenciado, mas não as tabelas dentro dele.

## <a name="next-steps"></a>Próximas etapas
* Introdução ao [Controle de Acesso Baseado em Função](role-based-access-control-configure.md)no Portal do Azure.
* Confira as [Funções internas do RBAC do Azure](role-based-access-built-in-roles.md)
* Defina suas próprias [Funções personalizadas no RBAC do Azure](role-based-access-control-custom-roles.md)
