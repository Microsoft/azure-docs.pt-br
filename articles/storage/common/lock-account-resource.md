---
title: Aplicar um bloqueio de Azure Resource Manager a uma conta de armazenamento
titleSuffix: Azure Storage
description: Saiba como aplicar um bloqueio de Azure Resource Manager a uma conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 79b88ad58a2eb95a48a140b3b98d606af495cb94
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799778"
---
# <a name="apply-an-azure-resource-manager-lock-to-a-storage-account"></a>Aplicar um bloqueio de Azure Resource Manager a uma conta de armazenamento

A Microsoft recomenda bloquear todas as suas contas de armazenamento com um bloqueio de Azure Resource Manager para impedir a exclusão acidental ou mal-intencionada da conta de armazenamento. Há dois tipos de bloqueios de recurso de Azure Resource Manager:

- Um bloqueio **CannotDelete** impede que os usuários excluam uma conta de armazenamento, mas permite ler e modificar sua configuração.
- Um bloqueio **somente leitura** impede que os usuários excluam uma conta de armazenamento ou modifiquem sua configuração, mas permite a leitura da configuração.

Para obter mais informações sobre bloqueios de Azure Resource Manager, consulte [Bloquear recursos para evitar alterações](../../azure-resource-manager/management/lock-resources.md).

> [!CAUTION]
> O bloqueio de uma conta de armazenamento não protege contêineres ou BLOBs dentro dessa conta de serem excluídos ou substituídos. Para obter mais informações sobre como proteger dados de BLOB, consulte [visão geral de proteção de dados](../blobs/data-protection-overview.md).

## <a name="configure-an-azure-resource-manager-lock"></a>Configurar um bloqueio de Azure Resource Manager

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para configurar um bloqueio em uma conta de armazenamento com o portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento no portal do Azure.
1. Na seção **configurações** , selecione **bloqueios**.
1. Selecione **Adicionar**.
1. Forneça um nome para o bloqueio de recurso e especifique o tipo de bloqueio. Adicione uma observação sobre o bloqueio, se desejado.

    :::image type="content" source="media/lock-account-resource/lock-storage-account.png" alt-text="Captura de tela mostrando como bloquear uma conta de armazenamento com um bloqueio CannotDelete":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para configurar um bloqueio em uma conta de armazenamento com o PowerShell, primeiro verifique se você instalou o [módulo AZ PowerShell](https://www.powershellgallery.com/packages/Az). Em seguida, chame o comando [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) e especifique o tipo de bloqueio que você deseja criar, conforme mostrado no exemplo a seguir:

```azurepowershell
New-AzResourceLock -LockLevel CanNotDelete `
    -LockName <lock> `
    -ResourceName <storage-account> `
    -ResourceType Microsoft.Storage/storageAccounts `
    -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar um bloqueio em uma conta de armazenamento com CLI do Azure, chame o comando [AZ Lock Create](/cli/azure/lock#az_lock_create) e especifique o tipo de bloqueio que você deseja criar, conforme mostrado no exemplo a seguir:

```azurecli
az lock create \
    --name <lock> \
    --resource-group <resource-group> \
    --resource <storage-account> \
    --lock-type CanNotDelete \
    --resource-type Microsoft.Storage/storageAccounts
```

---

## <a name="authorizing-data-operations-when-a-readonly-lock-is-in-effect"></a>Autorizando operações de dados quando um bloqueio ReadOnly está em vigor

Quando um bloqueio **ReadOnly** é aplicado a uma conta de armazenamento, a operação [listar chaves](/rest/api/storagerp/storageaccounts/listkeys) é bloqueada para essa conta de armazenamento. A operação **listar chaves** é uma operação post HTTPS e todas as operações post são impedidas quando um bloqueio **ReadOnly** é configurado para a conta. A operação **listar chaves** retorna as chaves de acesso da conta, que podem ser usadas para ler e gravar em todos os dados na conta de armazenamento.

Se um cliente estiver em posse das chaves de acesso da conta no momento em que o bloqueio for aplicado à conta de armazenamento, esse cliente poderá continuar a usar as chaves para acessar os dados. No entanto, os clientes que não têm acesso às chaves precisarão usar as credenciais do Azure Active Directory (AD do Azure) para acessar dados de BLOB ou de fila na conta de armazenamento.

Os usuários do portal do Azure podem ser afetados quando um bloqueio **ReadOnly** é aplicado, se eles acessaram anteriormente BLOB ou dados de fila no portal com as chaves de acesso da conta. Depois que o bloqueio for aplicado, os usuários do portal precisarão usar as credenciais do Azure AD para acessar dados de BLOB ou fila no Portal. Para fazer isso, um usuário deve ter pelo menos duas funções de RBAC atribuídas a eles: a função leitor de Azure Resource Manager, no mínimo, e uma das funções de acesso a dados do armazenamento do Azure. Para obter mais informações, consulte um dos seguintes artigos:

- [Escolha como autorizar o acesso aos dados de blob no portal do Azure](../blobs/authorize-data-operations-portal.md)
- [Escolha como autorizar o acesso aos dados da fila no portal do Azure](../queues/authorize-data-operations-portal.md)

Os dados nos arquivos do Azure ou no serviço tabela podem ficar inacessíveis para clientes que acessaram anteriormente com as chaves de conta. Como prática recomendada, se você precisar aplicar um bloqueio **somente leitura** a uma conta de armazenamento, mova os arquivos do Azure e as cargas de trabalho do serviço de tabela para uma conta de armazenamento que não esteja bloqueada com um bloqueio **somente leitura** .

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da proteção de dados](../blobs/data-protection-overview.md)
- [Bloqueio de recursos para prevenir alterações](../../azure-resource-manager/management/lock-resources.md)
