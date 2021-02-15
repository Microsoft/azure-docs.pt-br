---
title: Usar o provedor de recursos de armazenamento do Azure para acessar recursos de gerenciamento
description: O provedor de recursos de armazenamento do Azure é um serviço que fornece acesso a recursos de gerenciamento para o armazenamento do Azure. Você pode usar o provedor de recursos de armazenamento do Azure para criar, atualizar, gerenciar e excluir recursos como contas de armazenamento, pontos de extremidade privados e chaves de acesso de conta.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcf3e9228c8e651efb8f97067f7ba9eead5959db
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789668"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Usar o provedor de recursos de armazenamento do Azure para acessar recursos de gerenciamento

O Azure Resource Manager é p serviço de implantação e gerenciamento do Azure. O provedor de recursos de armazenamento do Azure é um serviço baseado em Azure Resource Manager e que fornece acesso a recursos de gerenciamento para o armazenamento do Azure. Você pode usar o provedor de recursos de armazenamento do Azure para criar, atualizar, gerenciar e excluir recursos como contas de armazenamento, pontos de extremidade privados e chaves de acesso de conta. Para obter mais informações sobre Azure Resource Manager, consulte [Azure Resource Manager visão geral](../../azure-resource-manager/management/overview.md).

Você pode usar o provedor de recursos de armazenamento do Azure para executar ações como criar ou excluir uma conta de armazenamento ou obter uma lista de contas de armazenamento em uma assinatura. Para autorizar solicitações no provedor de recursos de armazenamento do Azure, use Azure Active Directory (Azure AD). Este artigo descreve como atribuir permissões a recursos de gerenciamento e aponta para exemplos que mostram como fazer solicitações no provedor de recursos de armazenamento do Azure.

## <a name="management-resources-versus-data-resources"></a>Recursos de gerenciamento versus recursos de dados

A Microsoft fornece duas APIs REST para trabalhar com recursos de armazenamento do Azure. Essas APIs formam a base de todas as ações que você pode executar no armazenamento do Azure. A API REST do armazenamento do Azure permite que você trabalhe com dados em sua conta de armazenamento, incluindo dados de BLOB, fila, arquivo e tabela. A API REST do provedor de recursos de armazenamento do Azure permite que você trabalhe com a conta de armazenamento e os recursos relacionados.

Uma solicitação que lê ou grava dados de blob requer permissões diferentes de uma solicitação que executa uma operação de gerenciamento. O RBAC do Azure fornece controle refinado sobre permissões para ambos os tipos de recursos. Quando você atribuir uma função do Azure a uma entidade de segurança, certifique-se de que você entendeu quais permissões o principal será concedido. Para obter uma referência detalhada que descreve quais ações estão associadas a cada função interna do Azure, consulte [funções internas do Azure](../../role-based-access-control/built-in-roles.md).

O armazenamento do Azure dá suporte ao uso do Azure AD para autorizar solicitações no armazenamento de BLOBs e filas. Para obter informações sobre as funções do Azure para operações de dados de BLOB e de fila, consulte [autorizar o acesso a BLOBs e filas usando o Active Directory](storage-auth-aad.md).

## <a name="assign-management-permissions-with-azure-role-based-access-control-azure-rbac"></a>Atribuir permissões de gerenciamento com o controle de acesso baseado em função do Azure (RBAC do Azure)

Cada assinatura do Azure tem um Azure Active Directory associado que gerencia usuários, grupos e aplicativos. Um usuário, grupo ou aplicativo também é conhecido como uma entidade de segurança no contexto da [plataforma de identidade da Microsoft](../../active-directory/develop/index.yml). Você pode conceder acesso a recursos em uma assinatura para uma entidade de segurança que é definida no Active Directory usando o controle de acesso baseado em função do Azure (RBAC do Azure).

Ao atribuir uma função do Azure a uma entidade de segurança, você também indica o escopo no qual as permissões concedidas pela função estão em vigor. Para operações de gerenciamento, você pode atribuir uma função no nível da assinatura, no grupo de recursos ou na conta de armazenamento. Você pode atribuir uma função do Azure a uma entidade de segurança usando o [portal do Azure](https://portal.azure.com/), as [ferramentas de CLI do Azure](/cli/azure/install-classic-cli), o [PowerShell](/powershell/azure/)ou a [API REST do provedor de recursos de armazenamento do Azure](/rest/api/storagerp).

Para obter mais informações, consulte [o que é o Azure RBAC (controle de acesso baseado em função)?](../../role-based-access-control/overview.md) e funções de [administrador de assinatura clássica, funções do Azure e funções de administrador do Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md).

### <a name="built-in-roles-for-management-operations"></a>Funções internas para operações de gerenciamento

O Azure fornece funções internas que concedem permissões para chamar operações de gerenciamento. O armazenamento do Azure também fornece funções internas especificamente para uso com o provedor de recursos de armazenamento do Azure.

Funções internas que concedem permissões para chamar operações de gerenciamento de armazenamento incluem as funções descritas na tabela a seguir:

|    Função do Azure    |    Descrição    |    Inclui acesso a chaves de conta?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Proprietário** | Pode gerenciar todos os recursos de armazenamento e acesso aos recursos.  | Sim, fornece permissões para exibir e regenerar as chaves da conta de armazenamento. |
| **Colaborador**  | Pode gerenciar todos os recursos de armazenamento, mas não pode gerenciar o acesso aos recursos. | Sim, fornece permissões para exibir e regenerar as chaves da conta de armazenamento. |
| **Leitor** | Pode exibir informações sobre a conta de armazenamento, mas não pode exibir as chaves de conta. | Não. |
| **Colaborador da Conta de Armazenamento** | Pode gerenciar a conta de armazenamento, obter informações sobre os grupos de recursos e recursos da assinatura e criar e gerenciar implantações de grupo de recursos de assinatura. | Sim, fornece permissões para exibir e regenerar as chaves da conta de armazenamento. |
| **Administrador de Acesso do Usuário** | Pode gerenciar o acesso à conta de armazenamento.   | Sim, permite que uma entidade de segurança atribua qualquer permissão a si mesma e a outras. |
| **Colaborador de Máquina Virtual** | Pode gerenciar máquinas virtuais, mas não a conta de armazenamento à qual elas estão conectadas.   | Sim, fornece permissões para exibir e regenerar as chaves da conta de armazenamento. |

A terceira coluna na tabela indica se a função interna dá suporte a **Microsoft. Storage/storageAccounts/listkeys/Action** . Essa ação concede permissões para ler e regenerar as chaves da conta de armazenamento. As permissões para acessar os recursos de gerenciamento de armazenamento do Azure também não incluem permissões para acessar dados. No entanto, se um usuário tiver acesso às chaves de conta, ele poderá usar as chaves de conta para acessar os dados do armazenamento do Azure por meio da autorização de chave compartilhada.

### <a name="custom-roles-for-management-operations"></a>Funções personalizadas para operações de gerenciamento

O Azure também dá suporte à definição de funções personalizadas do Azure para acesso a recursos de gerenciamento. Para obter mais informações sobre funções personalizadas, consulte [funções personalizadas do Azure](../../role-based-access-control/custom-roles.md).

## <a name="code-samples"></a>Exemplos de código

Para obter exemplos de código que mostram como autorizar e chamar operações de gerenciamento das bibliotecas de gerenciamento de armazenamento do Azure, consulte os seguintes exemplos:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Implantações Azure Resource Manager versus clássicas

O Gerenciador de Recursos e os modelos de implantação clássicos representam duas maneiras de implantar e gerenciar soluções do Azure. A Microsoft recomenda usar o modelo de implantação Azure Resource Manager ao criar uma nova conta de armazenamento. Se possível, a Microsoft também recomenda que você recrie contas de armazenamento clássica existentes com o modelo do Resource Manager. Embora você possa criar uma conta de armazenamento usando o modelo de implantação clássico, o modelo clássico é menos flexível e, eventualmente, será preterido.

Para obter mais informações sobre os modelos de implantação do Azure, consulte [Resource Manager e implantação clássica](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [O que é o RBAC do Azure (controle de acesso baseado em função do Azure)?](../../role-based-access-control/overview.md)
- [Metas de escalabilidade para o provedor de recursos de armazenamento do Azure](scalability-targets-resource-provider.md)