---
title: Escolha como autorizar o acesso aos dados de blob no portal do Azure
titleSuffix: Azure Storage
description: Quando você acessa dados de BLOB usando o portal do Azure, o portal faz solicitações para o armazenamento do Azure nos bastidores. Essas solicitações para o armazenamento do Azure podem ser autenticadas e autorizadas usando sua conta do Azure AD ou a chave de acesso da conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: contperf-fy21q1
ms.openlocfilehash: 319bbdd7809e224ca608fdac06d4b304c2052e86
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391534"
---
# <a name="choose-how-to-authorize-access-to-blob-data-in-the-azure-portal"></a>Escolha como autorizar o acesso aos dados de blob no portal do Azure

Quando você acessa dados de BLOB usando o [portal do Azure](https://portal.azure.com), o portal faz solicitações para o armazenamento do Azure nos bastidores. Uma solicitação para o armazenamento do Azure pode ser autorizada usando sua conta do Azure AD ou a chave de acesso da conta de armazenamento. O portal indica qual método você está usando e permite que você alterne entre os dois se tiver as permissões apropriadas.  

Você também pode especificar como autorizar uma operação de upload de blob individual no portal do Azure. Por padrão, o portal usa qualquer método que você já esteja usando para autorizar uma operação de upload de BLOB, mas você tem a opção de alterar essa configuração ao carregar um blob.

## <a name="permissions-needed-to-access-blob-data"></a>Permissões necessárias para acessar dados de BLOB

Dependendo de como você deseja autorizar o acesso aos dados de blob no portal do Azure, você precisará de permissões específicas. Na maioria dos casos, essas permissões são fornecidas por meio do controle de acesso baseado em função do Azure (RBAC do Azure). Para obter mais informações sobre o RBAC do Azure, consulte [o que é o Azure RBAC (controle de acesso baseado em função)?](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>Usar a chave de acesso da conta

Para acessar dados de blob com a chave de acesso da conta, você deve ter uma função do Azure atribuída a você que inclua a ação do RBAC do Azure **Microsoft. Storage/storageAccounts/listkeys/Action**. Essa função do Azure pode ser uma função interna ou personalizada. Funções internas que dão suporte a **Microsoft. Storage/storageAccounts/listkeys/Action** incluem:

- A função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) de Azure Resource Manager
- A função [colaborador](../../role-based-access-control/built-in-roles.md#contributor) de Azure Resource Manager
- A função de [colaborador da conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Quando você tenta acessar dados de blob no portal do Azure, o portal verifica primeiro se você recebeu uma função com **Microsoft. Storage/storageAccounts/listkeys/Action**. Se você tiver recebido uma função com essa ação, o portal usará a chave de conta para acessar dados de BLOB. Se você não tiver recebido uma função com essa ação, o portal tentará acessar os dados usando sua conta do Azure AD.

> [!IMPORTANT]
> Quando uma conta de armazenamento é bloqueada com um bloqueio Azure Resource Manager **ReadOnly** , a operação [listar chaves](/rest/api/storagerp/storageaccounts/listkeys) não é permitida para essa conta de armazenamento. **Listar chaves** é uma operação post e todas as operações post são impedidas quando um bloqueio **ReadOnly** é configurado para a conta. Por esse motivo, quando a conta é bloqueada com um bloqueio **ReadOnly** , os usuários devem usar as credenciais do Azure ad para acessar dados de blob no Portal. Para obter informações sobre como acessar dados de blob no portal com o Azure AD, consulte [usar sua conta do Azure ad](#use-your-azure-ad-account).

> [!NOTE]
> O administrador de serviço de funções de administrador de assinatura clássica e Co-Administrator incluem o equivalente da função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) de Azure Resource Manager. A função de **proprietário** inclui todas as ações, incluindo a **ação Microsoft. Storage/storageAccounts/listkeys/**, para que um usuário com uma dessas funções administrativas também possa acessar dados de blob com a chave de conta. Para obter mais informações, confira [Funções clássicas de administrador da assinatura, funções do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Usar sua conta do Azure AD

Para acessar dados de blob do portal do Azure usando sua conta do Azure AD, as duas instruções a seguir devem ser verdadeiras para você:

- Você recebeu a função [leitor](../../role-based-access-control/built-in-roles.md#reader) de Azure Resource Manager, no mínimo, no escopo do nível da conta de armazenamento ou superior. A função **leitor** concede as permissões mais restritas, mas outra função de Azure Resource Manager que concede acesso aos recursos de gerenciamento da conta de armazenamento também é aceitável.
- Você foi atribuído a uma função interna ou personalizada que fornece acesso aos dados de BLOB.

A atribuição de função de **leitor** ou outra atribuição de função de Azure Resource Manager é necessária para que o usuário possa exibir e navegar pelos recursos de gerenciamento de conta de armazenamento no portal do Azure. As funções do Azure que concedem acesso a dados BLOB não concedem acesso aos recursos de gerenciamento da conta de armazenamento. Para acessar dados de blob no portal, o usuário precisa de permissões para navegar pelos recursos da conta de armazenamento. Para obter mais informações sobre esse requisito, consulte [atribuir a função leitor para acesso ao portal](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

As funções internas que dão suporte ao acesso aos seus dados de blob incluem:

- [Proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): para o controle de acesso POSIX para Azure data Lake Storage Gen2.
- [Colaborador de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): permissões de leitura/gravação/exclusão para BLOBs.
- [Leitor de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): permissões somente leitura para BLOBs.

As funções personalizadas podem dar suporte a diferentes combinações das mesmas permissões fornecidas pelas funções internas. Para obter mais informações sobre como criar funções personalizadas do Azure, consulte [funções personalizadas do Azure](../../role-based-access-control/custom-roles.md) e [noções básicas sobre definições de função para recursos do Azure](../../role-based-access-control/role-definitions.md).

> [!IMPORTANT]
> A versão de visualização do Gerenciador de Armazenamento no portal do Azure não oferece suporte ao uso de credenciais do Azure AD para exibir e modificar dados de BLOB. Gerenciador de Armazenamento no portal do Azure sempre usa as chaves de conta para acessar dados. Para usar Gerenciador de Armazenamento no portal do Azure, você deve ser atribuído a uma função que inclui **Microsoft. Storage/storageAccounts/listkeys/Action**.

## <a name="navigate-to-blobs-in-the-azure-portal"></a>Navegue até BLOBs na portal do Azure

Para exibir dados de blob no portal, navegue até a **visão geral** da sua conta de armazenamento e clique nos links para **BLOBs**. Como alternativa, você pode navegar até a seção **serviço blob** no menu.

:::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Captura de tela mostrando como navegar para dados de blob no portal do Azure":::

## <a name="determine-the-current-authentication-method"></a>Determinar o método de autenticação atual

Quando você navega para um contêiner, o portal do Azure indica se você está usando a chave de acesso da conta ou sua conta do Azure AD para autenticação.

### <a name="authenticate-with-the-account-access-key"></a>Autenticar com a chave de acesso da conta

Se você estiver autenticando usando a chave de acesso da conta, verá a **chave de acesso** especificada como o método de autenticação no Portal:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Captura de tela mostrando o usuário que está acessando contêineres com a chave de conta":::

Para alternar para o usando a conta do Azure AD, clique no link realçado na imagem. Se você tiver as permissões apropriadas por meio das funções do Azure atribuídas a você, poderá continuar. No entanto, se você não tem as permissões corretas, verá uma mensagem de erro semelhante à seguinte:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Erro mostrado se a conta do Azure AD não dá suporte ao acesso":::

Observe que nenhum blob aparece na lista se sua conta do Azure AD não tem permissões para exibi-los. Clique no link **alternar para a chave de acesso** para usar a tecla de acesso para autenticação novamente.

### <a name="authenticate-with-your-azure-ad-account"></a>Autenticar com sua conta do Azure AD

Se você estiver autenticando usando sua conta do Azure AD, verá a **conta de usuário do Azure ad** especificada como o método de autenticação no Portal:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Captura de tela mostrando o usuário atualmente acessando contêineres com a conta do Azure AD":::

Para alternar para o usando a chave de acesso da conta, clique no link realçado na imagem. Se você tiver acesso à chave de conta, poderá continuar. No entanto, se você não tem acesso à chave de conta, verá uma mensagem de erro semelhante à seguinte:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-access-key.png" alt-text="Erro mostrado se você não tiver acesso à chave de conta":::

Observe que nenhum blob aparecerá na lista se você não tiver acesso às chaves da conta. Clique no link **alternar para a conta de usuário do Azure ad** para usar sua conta do Azure ad para autenticação novamente.

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>Especificar como autorizar uma operação de upload de BLOB

Ao carregar um blob do portal do Azure, você pode especificar se deseja autenticar e autorizar essa operação com a chave de acesso da conta ou com suas credenciais do Azure AD. Por padrão, o portal usa o método de autenticação atual, conforme mostrado em [determinar o método de autenticação atual](#determine-the-current-authentication-method).

Para especificar como autorizar uma operação de upload de BLOB, siga estas etapas:

1. Na portal do Azure, navegue até o contêiner no qual você deseja carregar um blob.
1. Selecione o botão **Carregar**.
1. Expanda a seção **avançado** para exibir as propriedades avançadas para o blob.
1. No campo **tipo de autenticação** , indique se você deseja autorizar a operação de upload usando sua conta do Azure ad ou com a chave de acesso da conta, conforme mostrado na imagem a seguir:

    :::image type="content" source="media/authorize-data-operations-portal/auth-blob-upload.png" alt-text="Captura de tela mostrando como alterar o método de autorização no carregamento de BLOB":::

## <a name="next-steps"></a>Próximas etapas

- [Autenticar o acesso a BLOBs e filas do Azure usando o Azure Active Directory](../common/storage-auth-aad.md)
- [Usar o portal do Azure para atribuir uma função do Azure para acesso aos dados de blob e de fila](../common/storage-auth-aad-rbac-portal.md)
- [Use o CLI do Azure para atribuir uma função do Azure para acesso aos dados de BLOB e de fila](../common/storage-auth-aad-rbac-cli.md)
- [Usar o módulo Azure PowerShell para atribuir uma função do Azure para acesso aos dados de BLOB e de fila](../common/storage-auth-aad-rbac-powershell.md)
