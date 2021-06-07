---
title: Funções de administrador da assinatura clássica, funções do Azure e funções do Azure AD
description: Descreve as diferentes funções no Azure – funções de administrador de assinatura clássicas, funções do Azure e funções do Azure AD (Azure Active Directory)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/31/2021
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro;
ms.openlocfilehash: bd5533f4bc8420ee6d8900e8a17dfe7099d7945f
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106107190"
---
# <a name="classic-subscription-administrator-roles-azure-roles-and-azure-ad-roles"></a>Funções de administrador da assinatura clássica, funções do Azure e funções do Azure AD

Se você for novo no Azure, poderá ter alguma dificuldade em entender todas as diferentes funções no Azure. Este artigo ajuda a explicar as funções e quando usar cada uma:
- Funções de administrador de assinatura Clássico
- Funções do Azure
- Funções do Azure AD (Azure Active Directory)

## <a name="how-the-roles-are-related"></a>Como as funções estão relacionadas

Para entender melhor as funções no Azure, é bom conhecer sua história. Quando o Azure foi lançado inicialmente, o acesso aos recursos era gerenciado com apenas três funções de administrador: Administrador da conta, administrador de serviços e coadministrador. Posteriormente, o RBAC do Azure (controle de acesso baseado em função do Azure) foi adicionado. O Azure RBAC é um sistema de autorização mais recente que fornece gerenciamento de acesso refinado para recursos do Azure. O RBAC do Azure inclui muitas funções internas, pode ser atribuído em escopos diferentes e permite que você crie funções personalizadas próprias. Para gerenciar recursos no Azure AD, como usuários, grupos e domínios, há várias funções do Azure AD.

O diagrama a seguir é uma visão geral de como se relacionam as funções de administrador de assinatura clássico, as funções do Azure e as funções do Azure AD.

![As diferentes funções no Azure](./media/rbac-and-directory-admin-roles/rbac-admin-roles.png)


## <a name="classic-subscription-administrator-roles"></a>Funções de administrador de assinatura Clássico

Administrador da Conta, Administrador de Serviços e Coadministrador são as funções de administrador de assinatura clássicas no Azure. Os administradores de assinatura clássicos têm acesso total à assinatura do Azure. Eles podem gerenciar recursos usando o portal do Azure, as APIs do Azure Resource Manager e as APIs do modelo de implantação clássico. A conta que é usada para se inscrever no Azure é definida automaticamente como o Administrador da Conta e o Administrador de Serviços. Depois, outros Coadministradores podem ser adicionados. O administrador de serviços e os coadministradores têm o acesso equivalente ao de usuários que receberam a função de Proprietário (uma função do Azure) no escopo da assinatura. A tabela a seguir descreve as diferenças entre essas três funções administrativas de assinatura clássico.

| Administrador de assinatura clássico | Limite | Permissões | Observações |
| --- | --- | --- | --- |
| Administrador de conta | 1 por conta do Azure | <ul><li>Gerenciar a cobrança no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)</li><li>Gerenciar todas as assinaturas em uma conta</li><li>Criar novas assinaturas</li><li>Cancelar assinaturas</li><li>Alterar a cobrança de uma assinatura</li><li>Alterar o administrador do serviço</li></ul> | Conceitualmente, o proprietário de cobrança da assinatura. |
| Administrador de serviços | 1 por assinatura do Azure | <ul><li>Gerenciar serviços no [portal do Azure](https://portal.azure.com)</li><li>Cancelar a assinatura</li><li>Atribuir usuários à função de coadministrador</li></ul> | Por padrão, para uma nova assinatura, o Administrador da Conta também é o Administrador de Serviços.<br>O administrador de serviço tem o acesso equivalente ao de um usuário que é atribuído à função de Proprietário no escopo da assinatura.<br>O Administrador de Serviços não tem acesso completo ao portal do Azure. |
| Coadministrador | 200 por assinatura | <ul><li>Mesmos privilégios de acesso que o Administrador de Serviços, mas não pode alterar a associação de assinaturas nos diretórios do Azure</li><li>Atribui usuários à função de coadministrador, mas não pode alterar o administrador de serviço</li></ul> | O coadministrador tem o acesso equivalente ao de um usuário que é atribuído à função de Proprietário no escopo da assinatura. |

No portal do Azure, você pode gerenciar Coadministradores ou exibir o Administrador de Serviços usando a guia **Administradores clássicos**.

![Os administradores de assinatura clássicos do Azure no portal do Azure](./media/rbac-and-directory-admin-roles/subscription-view-classic-administrators.png)

No portal do Azure, você pode exibir ou alterar o Administrador de Serviços ou exibir o Administrador da Conta na folha de propriedades de sua assinatura.

![Administrador da conta e administrador de serviço no portal do Azure](./media/rbac-and-directory-admin-roles/account-admin.png)

Para obter mais informações, veja [Administradores de assinatura clássicos do Azure](classic-administrators.md).

### <a name="azure-account-and-azure-subscriptions"></a>Conta do Azure e assinaturas do Azure

Uma conta do Azure representa uma relação de cobrança. Uma conta do Azure é uma identidade de usuário, uma ou mais assinaturas do Azure e um conjunto de recursos do Azure associado. A pessoa que cria a conta é o administrador da conta para todas as assinaturas criadas nessa conta. Essa pessoa também é o administrador de serviço padrão da assinatura.

As assinaturas do Azure o ajudam a organizar o acesso aos recursos do Azure. Eles também ajudam a controlar como o uso de recursos é reportado, cobrado e pago. Cada assinatura pode ter uma configuração diferente de cobrança e pagamento, assim você pode ter diferentes assinaturas e planos diferentes por escritório, departamento, projeto e afins. Cada serviço pertence a uma assinatura e a ID da assinatura pode ser exigida para operações programáticas.

Cada assinatura está associada a um diretório do Azure AD. Para localizar o diretório ao qual a assinatura está associada, abra **Assinaturas** no portal do Azure e selecione uma assinatura para visualizar o diretório.

Contas e assinaturas são gerenciadas no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

## <a name="azure-roles"></a>Funções do Azure

O Azure RBAC é um sistema de autorização baseado no [Azure Resource Manager](../azure-resource-manager/management/overview.md) que fornece gerenciamento de acesso refinado aos recursos do Azure, como computação e armazenamento. O Azure RBAC inclui mais de 70 funções internas. Há quatro funções fundamentais do Azure. As três primeiras se aplicam a todos os tipos de recursos:

| Função do Azure | Permissões | Observações |
| --- | --- | --- |
| [Proprietário](built-in-roles.md#owner) | <ul><li>Acesso completo a todos os recursos</li><li>Delegar acesso a outras pessoas</li></ul> | O administrador de serviços e os coadministradores recebem a função de Proprietário no escopo da assinatura<br>Aplica-se a todos os tipos de recurso. |
| [Colaborador](built-in-roles.md#contributor) | <ul><li>Criar e gerenciar todos os tipos de recursos do Azure</li><li>Crie um novo locatário no Azure Active Directory</li><li>Não é possível conceder acesso a outras pessoas</li></ul> | Aplica-se a todos os tipos de recurso. |
| [Leitor](built-in-roles.md#reader) | <ul><li>Exibir recursos do Azure</li></ul> | Aplica-se a todos os tipos de recurso. |
| [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator) | <ul><li>Gerenciar o acesso do usuário aos recursos do Azure</li></ul> |  |

As demais funções internas permitem o gerenciamento de recursos específicos do Azure. Por exemplo, a função [Colaborador de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) permite que o usuário crie e gerencie máquinas virtuais. Para obter uma lista de todas as funções internas, confira [Funções internas do Azure](built-in-roles.md).

Somente o Portal do Azure e as APIs do Azure Resource Manager dão suporte ao RBAC do Azure. Os usuários, grupos e aplicativos aos quais são atribuídas funções do Azure não podem usar as [APIs do modelo de implantação clássico do Azure](../azure-resource-manager/management/deployment-models.md).

No portal do Azure, as atribuições de função usando o RBAC do Azure aparecem na folha **Controle de acesso (IAM)** . Essa folha pode ser encontrada em todo o portal, por exemplo, em grupos de gerenciamento, assinaturas, grupos de recursos e vários recursos.

![Folha Controle de acesso (IAM) no portal do Azure](./media/rbac-and-directory-admin-roles/access-control-role-assignments.png)

Quando você clicar na guia **Funções**, verá a lista de funções internas e personalizadas.

![Funções internas no portal do Azure](./media/rbac-and-directory-admin-roles/roles-list.png)

Para obter mais informações, confira [Atribuir funções do Azure usando o portal do Azure](role-assignments-portal.md).

## <a name="azure-ad-roles"></a>Funções do Azure AD

As funções do Azure AD são usadas para gerenciar recursos do Azure AD em um diretório, como criar ou editar usuários, atribuir funções administrativas a outros usuários, redefinir senhas de usuário, gerenciar licenças de usuário e gerenciar domínios. A tabela a seguir descreve algumas das funções do Azure AD mais importantes.

| Função do Azure AD | Permissões | Observações |
| --- | --- | --- |
| [Administrador global](../active-directory/roles/permissions-reference.md#global-administrator) | <ul><li>Gerenciar o acesso a todos os recursos administrativos do Azure Active Directory, bem como serviços federados ao Azure Active Directory</li><li>Atribuir funções de administrador a outras pessoas</li><li>Redefinir a senha para qualquer usuário e todos os outros administradores</li></ul> | A pessoa que se inscreve no locatário do Azure Active Directory torna-se um administrador global. |
| [Administrador de usuários](../active-directory/roles/permissions-reference.md#user-administrator) | <ul><li>Criar e gerenciar todos os aspectos de usuários e grupos</li><li>Gerenciar tíquetes de suporte</li><li>Monitorar a integridade do serviço</li><li>Alterar senhas de usuários, Administradores de Assistência Técnica e outros Administradores de Usuário</li></ul> |  |
| [Administrador de cobrança](../active-directory/roles/permissions-reference.md#billing-administrator) | <ul><li>Fazer compras</li><li>Gerenciar Assinaturas</li><li>Gerenciar tíquetes de suporte</li><li>Monitorar a integridade do serviço</li></ul> |  |

No portal do Azure, você pode ver a lista de funções do Azure AD na folha **Funções e administradores**. Para obter uma lista de todas as funções do Azure AD, confira [Permissões da função de administrador no Azure Active Directory](../active-directory/roles/permissions-reference.md).

![Funções do Azure AD no portal do Azure](./media/rbac-and-directory-admin-roles/directory-admin-roles.png)

## <a name="differences-between-azure-roles-and-azure-ad-roles"></a>Diferenças entre funções do Azure e funções do Azure AD

De maneira geral, as funções do Azure controlam permissões para gerenciar recursos do Azure e as funções do Azure AD controlam as permissões para gerenciar recursos do Azure Active Directory. A tabela a seguir compara algumas das diferenças.

| Funções do Azure | Funções do Azure AD |
| --- | --- |
| Gerenciar o acesso com recursos do Azure | Gerenciar o acesso a recursos do Azure Active Directory |
| Dá suporte a funções personalizadas | Dá suporte a funções personalizadas |
| O escopo pode ser especificado em vários níveis (grupo de gerenciamento, assinatura, grupo de recursos e recursos) | O [escopo](../active-directory/roles/custom-overview.md#scope) pode ser especificado no nível do locatário (em toda a organização), na unidade administrativa ou em um objeto individual (por exemplo, um aplicativo específico) |
| As informações de função podem ser acessadas no portal do Azure, na CLI do Azure, no Azure PowerShell, nos modelos do Azure Resource Manager, na API REST | As informações de função podem ser acessadas no portal de administração do Azure, no portal do administrador do Microsoft 365, no Microsoft Graph, no AzureAD PowerShell |

### <a name="do-azure-roles-and-azure-ad-roles-overlap"></a>As funções do Azure e as funções do Azure AD se sobrepõem?

Por padrão, as funções do Azure e do Azure AD não abrangem o Azure e o Azure AD. No entanto, se um Administrador global elevar o acesso escolhendo a opção **Gerenciamento de acesso para os recursos do Azure** no portal do Azure, ele receberá a função [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator) (uma função do Azure) em todas as assinaturas de um locatário específico. A função de Administrador de Acesso do Usuário permite que o usuário conceda a outros usuários o acesso aos recursos do Azure. Essa opção pode ser útil para recuperar o acesso a uma assinatura. Para obter mais informações, confira [Elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure](elevate-access-global-admin.md).

Várias funções do Azure AD englobam o Azure AD e o Microsoft 365, como as funções de Administrador global e Administrador de usuários. Por exemplo, se você for um membro da função de Administrador global, terá recursos de Administrador global no Azure AD e no Microsoft 365, por exemplo, poderá fazer alterações no Microsoft Exchange e no Microsoft SharePoint. No entanto, por padrão, o Administrador Global não tem acesso aos recursos do Azure.

![Funções do RBAC do Azure funções do Azure AD](./media/rbac-and-directory-admin-roles/azure-office-roles.png)

## <a name="next-steps"></a>Próximas etapas

- [O que é o RBAC do Azure (controle de acesso baseado em função do Azure)?](overview.md)
- [Permissões da função de administrador no Azure Active Directory](../active-directory/roles/permissions-reference.md)
- [Administradores da assinatura clássica do Azure](classic-administrators.md)
