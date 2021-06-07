---
title: Início Rápido para gerenciar compartilhamentos de arquivos do Azure com o Azure PowerShell
description: Use esse início rápido para saber como gerenciar compartilhamentos de arquivos do Azure usando o Azure PowerShell.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8b4bd9ece5f010f1294356ad4673543834e5076a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94626904"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Início Rápido: criar e gerenciar um compartilhamento de arquivos do Azure com o Azure PowerShell 
Este guia oferece orientações básicas sobre como trabalhar com [compartilhamentos de arquivos do Azure](storage-files-introduction.md) usando o PowerShell. Os compartilhamentos de arquivos do Azure são iguais a outros compartilhamentos de arquivos, mas são armazenados na nuvem e compatíveis com a plataforma do Azure. Os compartilhamentos dos Arquivos do Azure dão suporte ao protocolo SMB padrão e ao protocolo NFS (Network File System – versão prévia) e habilita o compartilhamento de arquivos entre vários computadores, aplicativos e instâncias. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você quiser instalar e usar o PowerShell localmente, este guia exigirá o módulo do Azure PowerShell versão Az 0.7 ou posterior. Para descobrir qual versão do módulo do Azure PowerShell está em execução, execute `Get-Module -ListAvailable Az`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzAccount` para fazer logon na conta do Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Se você ainda não tem um grupo de recursos do Azure, crie um novo com o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

O seguinte exemplo cria um grupo de recursos chamado *myResourceGroup* na região Oeste dos EUA 2:

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Uma conta de armazenamento é um pool compartilhado de armazenamento que você pode usar para implantar compartilhamentos de arquivo do Azure. Uma conta de armazenamento pode conter um número ilimitado de compartilhamentos, e um compartilhamento pode conter um número ilimitado de arquivos, até os limites de capacidade da conta de armazenamento. Este exemplo cria uma versão de uso geral 2 (conta de armazenamento GPv2), que pode armazenar compartilhamentos de arquivo do Azure padrão ou outros recursos de armazenamento, como blobs ou filas, na mídia de rotação da HD (unidade de disco rígido). Os Arquivos do Azure também dão suporte a SSDs (unidades de disco de estado sólido) Premium; os compartilhamentos de arquivo premium do Azure podem ser criados em contas de armazenamento FileStorage.

Este exemplo cria uma conta de armazenamento usando o cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). A conta de armazenamento é denominada *mystorageaccount\<random number>*, e uma referência a essa conta de armazenamento na variável é armazenada na variável **$storageAcct**. Os nomes de conta de armazenamento devem ser exclusivo e, portanto, use `Get-Random` para acrescentar um número ao nome e torná-lo exclusivo. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_ZRS `
    -EnableLargeFileShare
```

> [!Note]  
> Compartilhamentos maiores que 5 TiB (até o máximo de 100 TiB por compartilhamento) só estão disponíveis em contas de armazenamento LRS (com redundância local) e ZRS (com redundância de zona). Para criar uma conta de armazenamento GRS (com redundância geográfica) ou GZRS (com redundância de zona geográfica), remova o parâmetro `-EnableLargeFileShare`.

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure
Agora você pode criar seu primeiro compartilhamento de arquivos do Azure. Você pode criar um compartilhamento de arquivo usando o cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare). Esse exemplo cria um compartilhamento chamado `myshare`.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

Os nomes dos compartilhamentos precisam ter todos letras minúsculas, números e hifens, mas não podem começar com um hífen. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Usar o compartilhamento de arquivos do Azure
Os Arquivos do Azure fornecem dois métodos para trabalhar com arquivos e pastas dentro do seu compartilhamento de arquivos do Azure: o padrão do setor [protocolo SMB (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) e o [protocolo REST de arquivo](/rest/api/storageservices/file-service-rest-api). 

Para montar um compartilhamento de arquivos com SMB, consulte o documento abaixo com base em seu sistema operacional:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Usar um compartilhamento de arquivos do Azure com o protocolo REST de arquivo 
É possível trabalhar com o protocolo REST de arquivo (ou seja, modelar manualmente as chamadas REST HTTP por conta própria), mas a maneira mais comum de usar o protocolo REST de arquivo é usar o módulo do Azure PowerShell, a [CLI do Azure](storage-how-to-use-files-cli.md) ou um SDK do Armazenamento do Azure, que fornecem um bom wrapper do protocolo REST de arquivo na linguagem de script/programação de sua escolha.  

Na maioria dos casos, você usará o compartilhamento de arquivos do Azure em vez do protocolo SMB, pois isso permite usar os aplicativos e as ferramentas existentes que você espera poder usar, mas há várias razões por que é vantajoso usar a API REST de arquivo em vez de SMB, a saber:

- Você está buscando seu compartilhamento de arquivos no Cloud Shell do PowerShell (o que não pode montar os compartilhamentos de arquivos via SMB).
- Você está aproveitando as vantagens dos recursos sem servidor, como o [Azure Functions](../../azure-functions/functions-overview.md). 
- Você está criando um serviço de valor agregado que interagirá com muitos compartilhamentos de arquivos do Azure, como realizar backup ou verificações antivírus.

Os exemplos a seguir mostram como usar o módulo do Azure PowerShell para manipular o compartilhamento de arquivos do Azure com o protocolo REST de arquivo. O parâmetro `-Context` é usado para recuperar a chave de conta de armazenamento para executar as ações indicadas no compartilhamento de arquivo. Para recuperar a chave de conta de armazenamento, é necessário ter a função do Azure de `Owner` na conta de armazenamento.

#### <a name="create-directory"></a>Criar diretório
Para criar um novo diretório chamado *myDirectory* na raiz do seu compartilhamento de arquivos do Azure, use o cmdlet [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory).

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Carregar um arquivo
Para demonstrar como carregar um arquivo usando o cmdlet [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent), primeiro precisamos criar um arquivo dentro da unidade temporária do Cloud Shell do PowerShell para upload. 

Este exemplo coloca a data e a hora atuais em um novo arquivo na unidade temporária e o carrega no compartilhamento de arquivos.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Se você estiver executando o PowerShell localmente, substitua `~/CloudDrive/` por um caminho existente em seu computador.

Depois de carregar o arquivo, você pode usar o cmdlet [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) para verificar se o arquivo foi carregado no compartilhamento de arquivos do Azure. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>Baixar um arquivo
Você pode usar o cmdlet [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) para baixar uma cópia do arquivo que você acabou de carregar na unidade temporária do Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

Depois de baixar o arquivo, você pode usar o `Get-ChildItem` para ver se o arquivo foi baixado na unidade temporária do Cloud Shell do PowerShell.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

#### <a name="copy-files"></a>Copiar arquivos
Uma tarefa comum é copiar arquivos de um compartilhamento de arquivo para outro. Para demonstrar essa funcionalidade, você pode criar um novo compartilhamento e copiar o arquivo recém-carregado para esse novo compartilhamento usando o cmdlet [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy). 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $otherShareName `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName $shareName `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName $otherShareName `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Agora, se você listar os arquivos no novo compartilhamento, deverá ver o arquivo copiado.

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $otherShareName `
    -Path "myDirectory2" 
```

Embora o cmdlet `Start-AzStorageFileCopy` seja conveniente para movimentações de arquivo ad hoc entre compartilhamentos de arquivo do Azure, para migrações e movimentações de dados maiores, recomendamos `robocopy` no Windows e `rsync` no macOS e Linux. `robocopy` e `rsync` usam o SMB para executar movimentações de dados em vez da API FileREST.

## <a name="create-and-manage-share-snapshots"></a>Criar e gerenciar instantâneos de compartilhamento
Outra tarefa útil que você pode fazer com um compartilhamento de arquivos do Azure é criar instantâneos de compartilhamento. Um instantâneo preserva um ponto no tempo para um compartilhamento de arquivos do Azure. Os instantâneos de compartilhamento são semelhantes às tecnologias de sistema operacional que você talvez já conheça, como:

- [VSS (Serviço de Cópias de Sombra de Volume)](/windows/desktop/VSS/volume-shadow-copy-service-portal) para sistemas de arquivos Windows, como NTFS e ReFS.
- Instantâneos do [LVM (Gerenciador de Volumes Lógicos)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux.
- Instantâneos do [APFS (Sistema de arquivos da Apple)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS. 

Você pode criar um instantâneo de compartilhamento para um compartilhamento usando o método `Snapshot` no objeto do PowerShell para um compartilhamento de arquivo, que é recuperado com o cmdlet [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare). 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.CloudFileShare.Snapshot()
```

### <a name="browse-share-snapshots"></a>Procurar instantâneos de compartilhamento
Você pode procurar o conteúdo do instantâneo de compartilhamento passando a referência do instantâneo (`$snapshot`) para o parâmetro `-Share` do cmdlet `Get-AzStorageFile`.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Listar instantâneos de compartilhamento
Você pode ver a lista de instantâneos tirados para seu compartilhamento com o comando a seguir.

```azurepowershell-interactive
Get-AzStorageShare `
        -Context $storageAcct.Context | `
    Where-Object { $_.Name -eq $shareName -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Restaurar de um instantâneo de compartilhamento
Você pode restaurar um arquivo usando o comando `Start-AzStorageFileCopy` usado anteriormente. Para fins deste início rápido, iremos primeiro excluir nosso arquivo `SampleUpload.txt` carregado anteriormente para que possa ser restaurado do instantâneo.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName $shareName `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Excluir um instantâneo de compartilhamento
Você pode excluir um instantâneo de compartilhamento usando o cmdlet [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare), com a variável que contém a referência `$snapshot` para o parâmetro `-Share`.

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>Limpar os recursos
Quando tiver concluído, você poderá usar o cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos relacionados. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Como alternativa, você pode remover recursos individualmente:

- Para remover os compartilhamentos de arquivos do Azure que criamos para este início rápido.

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > Você deve excluir todos os instantâneos de compartilhamento para os compartilhamentos de arquivo do Azure criados antes de excluir o compartilhamento de arquivo do Azure.

- Para remover a própria conta de armazenamento (isso removerá implicitamente os compartilhamentos de arquivos do Azure que criamos, bem como qualquer outro recurso de armazenamento criado, como um contêiner do Armazenamento de Blobs do Azure).

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [O que Arquivos do Azure?](storage-files-introduction.md)