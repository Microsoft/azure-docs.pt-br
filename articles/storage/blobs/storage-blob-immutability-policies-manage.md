---
title: Definir e gerenciar políticas de imutabilidade para o armazenamento de blobs – Armazenamento do Microsoft Azure
description: Saiba como usar o suporte WORM (Gravar uma vez, reutilizar frequentemente) para armazenamento de blobs (objeto), para armazenar dados em um estado não apagável e não modificável para um intervalo especificado.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a09dbd7d778a4f7ea2a9aac9ca07b9e6d06bc1ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84463596"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Definir e gerenciar políticas de imutabilidade para o armazenamento de blobs

O armazenem imutável para armazenamento de blobs do Azure possibilita que os usuários armazenem objetos de dados críticos baseados em negócios em um estado WORM (Gravar uma vez, reutilizar frequentemente). Esse estado torna os dados não apagáveis e não modificáveis para um intervalo especificado pelo usuário. Durante o intervalo de retenção, os blobs podem ser criados e lidos, mas não modificados ou excluídos. O armazenamento imutável está disponível nas contas de armazenamento de blobs e de uso geral v2 em todas as regiões do Azure.

Neste artigo, há informações de como definir e gerenciar políticas de imutabilidade e retenções legais para dados no armazenamento de blobs usando o portal do Microsoft Azure, o PowerShell ou a CLI do Azure. Para obter mais informações sobre armazenamento imutável, veja [Armazenar dados de blob comercialmente críticos com armazenamento imutável](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Definir políticas de retenção e retenções legais

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Crie um novo contêiner ou selecione um existente para armazenar os blobs que precisam ser mantidos no estado imutável. O contêiner precisa estar em uma conta de armazenamento de blobs ou de uso geral v2.

2. Selecione **Política de acesso** no menu de contexto. Em seguida, escolha **Adicionar Política** em **Armazenamento de Blobs Imutável**.

    ![Configurações de contêiner no portal](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Para habilitar a retenção baseada em tempo, selecione **baseada em tempo de retenção** no menu suspenso.

    !["Com base no tempo de retenção" selecionada em "Tipo de política"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Insira o intervalo de retenção em dias (os valores aceitáveis são de 1 a 146.000 dias).

    ![Caixa "Período de retenção de atualização para"](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    O estado inicial da política é desbloqueado, permitindo testar o recurso e fazer alterações na política antes de bloqueá-lo. O bloqueio da política é essencial para a conformidade com as normas, como SEC 17a-4.

5. A política de bloqueio. Clique no botão de reticências ( **…** ), e o seguinte menu será exibido com ações adicionais:

    !["Política de bloqueio" no menu](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Escolha **Bloquear Política** e confirme o bloqueio. Depois que a política é bloqueada, ela não pode ser excluída, e somente as extensões do intervalo de retenção são permitidas. Não são permitidas exclusões e substituições de blob. 

    ![Confirmar "Política de bloqueio" no menu](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Para ativar retenções legais, escolha **Adicionar Política**. Selecione **Retenção legal** no menu suspenso.

    !["Legal hold" no menu em "Tipo de política"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Crie uma retenção legal com uma ou mais tags.

    ![Caixa "Nome da tag" no tipo de política](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Para desmarcar uma retenção legal, remova a marcação do identificador de retenção legal aplicada.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

O recurso está incluído nos seguintes grupos de comandos: `az storage container immutability-policy`e`az storage container legal-hold`. Execute `-h` neles para ver os comandos.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O módulo de visualização Az.Storage é compatível com armazenamento imutável.  Para habilitar o recurso, siga estas etapas:

1. Certifique-se de que você tenha a versão mais recente do PowerShellGet instalado: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Remova qualquer instalação anterior do PowerShell do Azure.
3. Instale o Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

O exemplo de script do PowerShell a seguir é para referência. Este script cria uma nova conta e contêiner de armazenamento. Em seguida, ele mostra como definir e limpar retenções legais, criar e bloquear uma política de retenção baseada em tempo (também conhecida como política de imutabilidade) e estender o intervalo de retenção.

Primeiro, crie uma conta de Armazenamento do Microsoft Azure:

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$account = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Definir e limpar retenções legais:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Criar ou atualizar políticas de imutabilidade baseadas em tempo:

```powershell
# Create a time-based immutability policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Recuperar políticas de imutabilidade:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Bloquear políticas de imutabilidade (adicione `-Force` para ignorar o prompt):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

Estender as políticas de imutabilidade:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Remover uma política de imutabilidade desbloqueada (adicione `-Force` para ignorar o prompt):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Habilitar a permissão para gravar em blobs de acréscimo protegidos

### <a name="portal"></a>[Portal](#tab/azure-portal)

![Permitir gravações de acréscimo adicionais](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

O recurso está incluído nos seguintes grupos de comandos: `az storage container immutability-policy`e`az storage container legal-hold`. Execute `-h` neles para ver os comandos.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Create an immutability policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Próximas etapas

[Armazenar dados de blob comercialmente críticos com armazenamento imutável](storage-blob-immutable-storage.md)
