---
title: Gerenciar as chaves de acesso da conta
titleSuffix: Azure Storage
description: Saiba como exibir, gerenciar e girar suas chaves de acesso da conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e5ea94fea00771b64634d6c28a7879fabb195f09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89069652"
---
# <a name="manage-storage-account-access-keys"></a>Gerenciar chaves de acesso da conta de armazenamento

Quando você cria uma conta de armazenamento, o Azure gera duas chaves de acesso de conta de armazenamento de 512 bits. Essas chaves podem ser usadas para autorizar o acesso a dados em sua conta de armazenamento por meio da autorização de chave compartilhada.

A Microsoft recomenda que você use Azure Key Vault para gerenciar suas chaves de acesso e que você regularmente gira e regenera suas chaves. O uso de Azure Key Vault facilita a rotação de suas chaves sem interrupções em seus aplicativos. Você também pode girar manualmente suas chaves.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Exibir chaves de acesso da conta

Você pode exibir e copiar as chaves de acesso da conta com o portal do Azure, o PowerShell ou o CLI do Azure. O portal do Azure também fornece uma cadeia de conexão para sua conta de armazenamento que você pode copiar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para exibir e copiar as chaves de acesso ou a cadeia de conexão da conta de armazenamento do portal do Azure:

1. Navegue até sua conta de armazenamento no [portal do Azure](https://portal.azure.com).
1. Em **Configurações**, selecione **Chaves de acesso**. Suas chaves de acesso da conta são exibidas, bem como a cadeia de conexão completa para cada chave.
1. Localize o valor da **chave** em **key1**e clique no botão **copiar** para copiar a chave da conta.
1. Como alternativa, você pode copiar a cadeia de conexão inteira. Encontre o valor da **Cadeia de conexão** em **key1** e clique no botão **Copiar** para copiar a cadeia de conexão.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Captura de tela mostrando como exibir chaves de acesso no portal do Azure":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para recuperar as chaves de acesso da conta com o PowerShell, chame o comando [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey) .

O exemplo a seguir recupera a primeira chave. Para recuperar a segunda chave, use `Value[1]` em vez de `Value[0]` . Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para listar as chaves de acesso da conta com CLI do Azure, chame o comando [AZ Storage Account Keys List](/cli/azure/storage/account/keys#az-storage-account-keys-list) , conforme mostrado no exemplo a seguir. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

Você pode usar qualquer uma das duas chaves para acessar o armazenamento do Azure, mas, em geral, é uma boa prática usar a primeira chave e reservar o uso da segunda chave para quando você estiver girando as chaves.

Para exibir ou ler as chaves de acesso de uma conta, o usuário deve ser um administrador de serviço ou deve ser atribuído a uma função do Azure que inclui a **ação Microsoft. Storage/storageAccounts/listkeys/**. Algumas funções internas do Azure que incluem essa ação são as funções de função de serviço de **operador de chave de conta de armazenamento** , **colaborador**e **proprietário**. Para obter mais informações sobre a função de administrador de serviços, consulte [funções clássicas de administrador de assinatura, funções do Azure e funções do Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md). Para obter informações detalhadas sobre funções internas para o armazenamento do Azure, consulte a seção **armazenamento** em [funções internas do Azure para o RBAC do Azure](../../role-based-access-control/built-in-roles.md#storage).

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Usar Azure Key Vault para gerenciar suas chaves de acesso

A Microsoft recomenda o uso de Azure Key Vault para gerenciar e girar suas chaves de acesso. Seu aplicativo pode acessar com segurança suas chaves no Key Vault, para que você possa evitar armazená-las com o código do aplicativo. Para obter mais informações sobre como usar Key Vault para o gerenciamento de chaves, consulte os seguintes artigos:

- [Gerenciar chaves de conta de armazenamento com o Azure Key Vault e o PowerShell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Gerenciar chaves de conta de armazenamento com o Azure Key Vault e a CLI do Azure](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Girar manualmente as chaves de acesso

A Microsoft recomenda que você gire suas chaves de acesso periodicamente para ajudar a manter sua conta de armazenamento segura. Se possível, use Azure Key Vault para gerenciar suas chaves de acesso. Se você não estiver usando Key Vault, será necessário girar suas chaves manualmente.

Duas chaves de acesso são atribuídas para que você possa girar as chaves. Ter duas chaves garante que seu aplicativo Mantenha o acesso ao armazenamento do Azure durante todo o processo.

> [!WARNING]
> A regeneração das chaves de acesso pode afetar todos os aplicativos ou serviços do Azure que dependem da chave de conta de armazenamento. Todos os clientes que usam a chave de conta para acessar a conta de armazenamento devem ser atualizados para usar a nova chave, incluindo os serviços de mídia, nuvem, aplicativos para área de trabalho e móveis e aplicativos de interface gráfica do usuário para o Armazenamento do Azure, tais como o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para girar suas chaves de acesso da conta de armazenamento no portal do Azure:

1. Atualize as cadeias de conexão no código do aplicativo para fazer referência à chave de acesso secundária da conta de armazenamento.
1. Navegue até sua conta de armazenamento no [portal do Azure](https://portal.azure.com).
1. Em **Configurações**, selecione **Chaves de acesso**.
1. Para regenerar a chave de acesso primária para sua conta de armazenamento, selecione o botão **regenerar** ao lado da chave de acesso primária.
1. Atualize as cadeias de conexão no código para referenciar a nova chave de acesso primária.
1. Regenere a chave de acesso secundária da mesma maneira.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para girar as chaves de acesso da conta de armazenamento com o PowerShell:

1. Atualize as cadeias de conexão no código do aplicativo para fazer referência à chave de acesso secundária da conta de armazenamento.
1. Chame o comando [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) para regenerar a chave de acesso primária, conforme mostrado no exemplo a seguir:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. Atualize as cadeias de conexão no código para referenciar a nova chave de acesso primária.
1. Regenere a chave de acesso secundária da mesma maneira. Para regenerar a chave secundária, use `key2` como o nome da chave em vez de `key1` .

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para girar suas chaves de acesso da conta de armazenamento com CLI do Azure:

1. Atualize as cadeias de conexão no código do aplicativo para fazer referência à chave de acesso secundária da conta de armazenamento.
1. Chame o comando [AZ Storage Account Keys Renew](/cli/azure/storage/account/keys#az-storage-account-keys-renew) para regenerar a chave de acesso primária, conforme mostrado no exemplo a seguir:

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. Atualize as cadeias de conexão no código para referenciar a nova chave de acesso primária.
1. Regenere a chave de acesso secundária da mesma maneira. Para regenerar a chave secundária, use `key2` como o nome da chave em vez de `key1` .

---

> [!NOTE]
> A Microsoft recomenda usar apenas uma das chaves em todos os aplicativos ao mesmo tempo. Se você usar a Chave 1 em alguns lugares e a Chave 2 em outros, nãos será possível alternar as chaves sem que algum aplicativo perca o acesso.

Para girar as chaves de acesso de uma conta, o usuário deve ser um administrador de serviço ou deve ser atribuído a uma função do Azure que inclui a **ação Microsoft. Storage/storageAccounts/regeneratekey/**. Algumas funções internas do Azure que incluem essa ação são as funções de função de serviço de **operador de chave de conta de armazenamento** , **colaborador**e **proprietário**. Para obter mais informações sobre a função de administrador de serviços, consulte [funções clássicas de administrador de assinatura, funções do Azure e funções do Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md). Para obter informações detalhadas sobre as funções internas do Azure para o armazenamento do Azure, consulte a seção **armazenamento** em [funções internas do Azure para o RBAC do Azure](../../role-based-access-control/built-in-roles.md#storage).

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da conta de armazenamento do Azure](storage-account-overview.md)
- [Criar uma conta de armazenamento](storage-account-create.md)
