---
title: Início rápido – Criar um blob com o PowerShell
titleSuffix: Azure Storage
description: Neste início rápido, você usa o Azure PowerShell no armazenamento de objeto (Blob). Em seguida, use o PowerShell para carregar um blob no Armazenamento do Azure, baixar um blob e listar os blobs em um contêiner.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 03/31/2020
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f3fdeadcf90d94203a972946ccceefca74cd5c3d
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012472"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>Início Rápido: Carregar, baixar e listar blobs com o PowerShell

Use o módulo do Azure PowerShell para criar e gerenciar recursos do Azure. Criar ou gerenciar recursos do Azure pode ser feito da linha de comando do PowerShell ou em scripts. Este guia descreve o uso do PowerShell para transferir arquivos entre o disco local e o Armazenamento de Blobs do Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para acessar o Armazenamento do Azure, você precisará de uma assinatura do Azure. Se você ainda não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Você também precisará da função de Colaborador de Dados do Blob de Armazenamento para ler, gravar e excluir contêineres e blobs de Armazenamento do Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este início rápido exige o módulo Az do Azure PowerShell versão 0.7 ou posterior. Execute `Get-InstalledModule -Name Az -AllVersions | select Name,Version` para encontrar a versão. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Criar um contêiner

Os blobs são sempre carregados em um contêiner. É possível organizar grupos de blobs de forma similar àquela em que você organiza os arquivos em pastas no seu computador.

Defina o nome do contêiner e, em seguida, crie o contêiner usando [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Defina as permissões como `blob` para permitir acesso público dos arquivos. O nome do contêiner neste exemplo é *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contêiner

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Arquivos VHD que auxiliam VMs IaaS são blobs de páginas. Use os blobs de acréscimo para registro em log, como quando você quer gravar em um arquivo e depois adicionar mais informações. A maioria dos arquivos armazenados no Armazenamento de Blobs são blobs de blocos. 

Para carregar um arquivo em um blob de blocos, obtenha uma referência de contêiner e uma referência para o blob de blocos nesse contêiner. Depois de obter a referência de blob, carregue dados nele usando [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent). Essa operação cria o blob, se ele não existir, ou o substitui, se ele já existir.

Os exemplos a seguir carregam a *Image001.jpg* e *Image002.png* da pasta *D:\\_TestImages* no disco local para o contêiner que você criou.

```powershell
# upload a file to the default account (inferred) access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image000.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload a file to the Hot access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx `
  -StandardBlobTier Hot

# upload another file to the Cool access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx `
  -StandardBlobTier Cool

# upload a file to a folder to the Archive access tier
Set-AzStorageBlobContent -File "D:\_TestImages\foldername\Image003.jpg" `
  -Container $containerName `
  -Blob "Foldername/Image003.jpg" `
  -Context $ctx `
  -StandardBlobTier Archive
```

Carregue quantos arquivos desejar antes de continuar.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Obtenha uma lista de blobs no contêiner usando [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob). Este exemplo mostra apenas os nomes dos blobs carregados.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Baixar blobs

Baixe os blobs em seu disco local. Para cada blob que deseja baixar, defina o nome e chame [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent) para baixar o blob.

Este exemplo baixa os blobs para *D:\\_TestImages\Downloads* no disco local. 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>Transferência de dados com AzCopy

O utilitário de linha de comando AzCopy oferece transferência de dados de alto desempenho e programável para o Armazenamento do Azure. Use o AzCopy para transferir dados bidirecionalmente no Armazenamento de Blobs e nos Arquivos do Azure. Para obter mais informações sobre o AzCopy v10, a última versão do AzCopy, confira [Introdução ao AzCopy](../common/storage-use-azcopy-v10.md). Para saber mais sobre como usar o AzCopy v10 com o Armazenamento de Blobs, confira [Transferir dados com o AzCopy e o Armazenamento de Blobs](../common/storage-use-azcopy-v10.md#transfer-data).

O exemplo a seguir usa o AzCopy para carregar um arquivo local em um blob. Lembre-se de substituir os valores de exemplo pelos próprios valores:

```powershell
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Limpar os recursos

Remova todos os ativos que você criou. A maneira mais fácil de remover os ativos é excluir o grupo de recursos. Remover o grupo de recursos também exclui todos os recursos contidos no grupo. No exemplo a seguir, remover o grupo de recursos remove a conta de armazenamento e o próprio grupo de recursos.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você transferiu arquivos entre um sistema de arquivos local e o Armazenamento de Blobs do Azure. Para saber mais sobre como trabalhar com o Armazenamento de Blobs usando o PowerShell, explore os exemplos do Azure PowerShell para o Armazenamento de Blobs.

> [!div class="nextstepaction"]
> [Exemplos do Azure PowerShell para o Armazenamento de Blobs do Azure](storage-samples-blobs-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Referência de cmdlets de Armazenamento do Microsoft Azure PowerShell

* [Cmdlets do PowerShell do Armazenamento](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Gerenciador do Armazenamento do Microsoft Azure

* [O Gerenciador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.
