---
title: Escolha como autorizar o acesso aos dados da fila no portal do Azure
titleSuffix: Azure Storage
description: Quando você acessa os dados da fila usando o portal do Azure, o portal faz solicitações para o armazenamento do Azure nos bastidores. Essas solicitações para o armazenamento do Azure podem ser autenticadas e autorizadas usando sua conta do Azure AD ou a chave de acesso da conta de armazenamento.
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozguns
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: contperf-fy21q1
ms.openlocfilehash: fbb96fc1d2cb12e1aede07295357abfaa6d6b67f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385006"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>Escolha como autorizar o acesso aos dados da fila no portal do Azure

Quando você acessa os dados da fila usando o [portal do Azure](https://portal.azure.com), o portal faz solicitações para o armazenamento do Azure nos bastidores. Uma solicitação para o armazenamento do Azure pode ser autorizada usando sua conta do Azure AD ou a chave de acesso da conta de armazenamento. O portal indica qual método você está usando e permite que você alterne entre os dois se tiver as permissões apropriadas.

## <a name="permissions-needed-to-access-queue-data"></a>Permissões necessárias para acessar os dados da fila

Dependendo de como você deseja autorizar o acesso aos dados da fila no portal do Azure, você precisará de permissões específicas. Na maioria dos casos, essas permissões são fornecidas por meio do controle de acesso baseado em função do Azure (RBAC do Azure). Para obter mais informações sobre o RBAC do Azure, consulte [o que é o Azure RBAC (controle de acesso baseado em função)?](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>Usar a chave de acesso da conta

Para acessar os dados da fila com a chave de acesso da conta, você deve ter uma função do Azure atribuída a você que inclua a ação do RBAC do Azure **Microsoft. Storage/storageAccounts/listkeys/Action**. Essa função do Azure pode ser uma função interna ou personalizada. Funções internas que dão suporte a **Microsoft. Storage/storageAccounts/listkeys/Action** incluem:

- A [função de proprietário](../../role-based-access-control/built-in-roles.md#owner) de Azure Resource Manager
- A [função colaborador](../../role-based-access-control/built-in-roles.md#contributor) de Azure Resource Manager
- A [função de colaborador da conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Quando você tenta acessar os dados da fila no portal do Azure, o portal primeiro verifica se você recebeu uma função com **Microsoft. Storage/storageAccounts/listkeys/Action**. Se você tiver recebido uma função com essa ação, o portal usará a chave de conta para acessar os dados da fila. Se você não tiver recebido uma função com essa ação, o portal tentará acessar os dados usando sua conta do Azure AD.

> [!IMPORTANT]
> Quando uma conta de armazenamento é bloqueada com um bloqueio Azure Resource Manager **ReadOnly** , a operação [listar chaves](/rest/api/storagerp/storageaccounts/listkeys) não é permitida para essa conta de armazenamento. **Listar chaves** é uma operação post e todas as operações post são impedidas quando um bloqueio **ReadOnly** é configurado para a conta. Por esse motivo, quando a conta é bloqueada com um bloqueio **ReadOnly** , os usuários devem usar as credenciais do Azure ad para acessar os dados da fila no Portal. Para obter informações sobre como acessar dados de fila no portal com o Azure AD, consulte [usar sua conta do Azure ad](#use-your-azure-ad-account).

> [!NOTE]
> O **administrador de serviço** das funções de administrador de assinatura clássica e o **coadministrador** incluem o equivalente da [`Owner`](../../role-based-access-control/built-in-roles.md#owner) função Azure Resource Manager. A função de **proprietário** inclui todas as ações, incluindo a **ação Microsoft. Storage/storageAccounts/listkeys/**, para que um usuário com uma dessas funções administrativas também possa acessar os dados da fila com a chave de conta. Para obter mais informações, confira [Funções clássicas de administrador da assinatura, funções do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Usar sua conta do Azure AD

Para acessar os dados da fila do portal do Azure usando sua conta do Azure AD, as duas instruções a seguir devem ser verdadeiras para você:

- Você recebeu a função de Azure Resource Manager [`Reader`](../../role-based-access-control/built-in-roles.md#reader) , no mínimo, com escopo para o nível da conta de armazenamento ou superior. A função **leitor** concede as permissões mais restritas, mas outra função de Azure Resource Manager que concede acesso aos recursos de gerenciamento da conta de armazenamento também é aceitável.
- Você foi atribuído a uma função interna ou personalizada que fornece acesso aos dados da fila.

A atribuição de função de **leitor** ou outra atribuição de função de Azure Resource Manager é necessária para que o usuário possa exibir e navegar pelos recursos de gerenciamento de conta de armazenamento no portal do Azure. As funções do Azure que concedem acesso aos dados da fila não concedem acesso aos recursos de gerenciamento da conta de armazenamento. Para acessar os dados da fila no portal, o usuário precisa de permissões para navegar pelos recursos da conta de armazenamento. Para obter mais informações sobre esse requisito, consulte [atribuir a função leitor para acesso ao portal](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

As funções internas que dão suporte ao acesso aos dados da fila incluem:

- [Colaborador de dados da fila de armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): permissões de leitura/gravação/exclusão para filas.
- [Leitor de dados da fila de armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): permissões somente leitura para filas.

As funções personalizadas podem dar suporte a diferentes combinações das mesmas permissões fornecidas pelas funções internas. Para obter mais informações sobre como criar funções personalizadas do Azure, consulte [funções personalizadas do Azure](../../role-based-access-control/custom-roles.md) e [noções básicas sobre definições de função para recursos do Azure](../../role-based-access-control/role-definitions.md).

Não há suporte para a listagem de filas com uma função de administrador de assinatura clássica. Para listar filas, um usuário deve ter atribuído a eles a função **leitor** de Azure Resource Manager, a função de **leitor de dados fila de armazenamento** ou a função **colaborador de dados da fila de armazenamento** .

> [!IMPORTANT]
> A versão de visualização do Gerenciador de Armazenamento no portal do Azure não oferece suporte ao uso de credenciais do Azure AD para exibir e modificar os dados da fila. Gerenciador de Armazenamento no portal do Azure sempre usa as chaves de conta para acessar dados. Para usar Gerenciador de Armazenamento no portal do Azure, você deve ser atribuído a uma função que inclui **Microsoft. Storage/storageAccounts/listkeys/Action**.

## <a name="navigate-to-queues-in-the-azure-portal"></a>Navegue até as filas no portal do Azure

Para exibir os dados da fila no portal, navegue até a **visão geral** da sua conta de armazenamento e clique nos links para **filas**. Como alternativa, você pode navegar até a seção **serviço fila** no menu.

:::image type="content" source="media/authorize-data-operations-portal/queue-access-portal.png" alt-text="Captura de tela mostrando como navegar até os dados da fila no portal do Azure":::

## <a name="determine-the-current-authentication-method"></a>Determinar o método de autenticação atual

Quando você navega para uma fila, a portal do Azure indica se você está usando a chave de acesso da conta ou sua conta do Azure AD para autenticação.

### <a name="authenticate-with-the-account-access-key"></a>Autenticar com a chave de acesso da conta

Se você estiver autenticando usando a chave de acesso da conta, verá a **chave de acesso** especificada como o método de autenticação no Portal:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Captura de tela mostrando o usuário atualmente acessando filas com a chave de conta":::

Para alternar para o usando a conta do Azure AD, clique no link realçado na imagem. Se você tiver as permissões apropriadas por meio das funções do Azure atribuídas a você, poderá continuar. No entanto, se você não tem as permissões corretas, verá uma mensagem de erro semelhante à seguinte:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Erro mostrado se a conta do Azure AD não dá suporte ao acesso":::

Observe que nenhuma fila aparece na lista se sua conta do Azure AD não tem permissões para exibi-las. Clique no link **alternar para a chave de acesso** para usar a tecla de acesso para autenticação novamente.

### <a name="authenticate-with-your-azure-ad-account"></a>Autenticar com sua conta do Azure AD

Se você estiver autenticando usando sua conta do Azure AD, verá a **conta de usuário do Azure ad** especificada como o método de autenticação no Portal:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Captura de tela mostrando o usuário atualmente acessando filas com a conta do Azure AD":::

Para alternar para o usando a chave de acesso da conta, clique no link realçado na imagem. Se você tiver acesso à chave de conta, poderá continuar. No entanto, se você não tem acesso à chave de conta, o portal do Azure exibe uma mensagem de erro.

As filas não serão listadas no portal se você não tiver acesso às chaves da conta. Clique no link **alternar para a conta de usuário do Azure ad** para usar sua conta do Azure ad para autenticação novamente.

## <a name="next-steps"></a>Próximas etapas

- [Autenticar o acesso a BLOBs e filas do Azure usando o Azure Active Directory](../common/storage-auth-aad.md)
- [Usar o portal do Azure para atribuir uma função do Azure para acesso aos dados de blob e de fila](../common/storage-auth-aad-rbac-portal.md)
- [Use o CLI do Azure para atribuir uma função do Azure para acesso aos dados de BLOB e de fila](../common/storage-auth-aad-rbac-cli.md)
- [Usar o módulo Azure PowerShell para atribuir uma função do Azure para acesso aos dados de BLOB e de fila](../common/storage-auth-aad-rbac-powershell.md)
