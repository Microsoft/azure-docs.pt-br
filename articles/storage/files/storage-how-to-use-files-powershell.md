---
title: Início Rápido para gerenciar compartilhamentos de arquivos do Azure com o Azure PowerShell
description: Use esse início rápido para saber como gerenciar compartilhamentos de arquivos do Azure usando o Azure PowerShell.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 802ad497f95a43665665d7e7dbd06c9081eba74a
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699516"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Início Rápido: Criar e gerenciar um compartilhamento de arquivos do Azure com o Azure PowerShell 
Este guia oferece orientações básicas sobre como trabalhar com [compartilhamentos de arquivos do Azure](storage-files-introduction.md) usando o PowerShell. Os compartilhamentos de arquivos do Azure são iguais a outros compartilhamentos de arquivos, mas são armazenados na nuvem e compatíveis com a plataforma do Azure. Os compartilhamentos de Arquivos do Azure oferecem suporte ao protocolo SMB padrão do setor e habilitar o compartilhamento de arquivos entre vários computadores, aplicativos e instâncias. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você quiser instalar e usar o PowerShell localmente, este guia exigirá o módulo do Azure PowerShell versão Az 0.7 ou posterior. Para descobrir qual versão do módulo do Azure PowerShell está em execução, execute `Get-Module -ListAvailable Az`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzAccount` para fazer logon na conta do Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Se você ainda não tem um grupo de recursos do Azure, crie um novo com o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

O exemplo abaixo cria um grupo de recursos chamado *myResourceGroup* na região Leste dos EUA:

```azurepowershell-interactive
New-AzResourceGroup `
    -Name myResourceGroup `
    -Location EastUS
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Uma conta de armazenamento é um pool compartilhado de armazenamento que você pode usar para implantar compartilhamentos de arquivos do Azure ou outros recursos de armazenamento, como blobs ou filas. Uma conta de armazenamento pode conter um número ilimitado de compartilhamentos, e um compartilhamento pode conter um número ilimitado de arquivos, até os limites de capacidade da conta de armazenamento.

Este exemplo cria uma conta de armazenamento usando o cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). A conta de armazenamento é denominada *mystorageaccount\<número aleatório>* e uma referência a essa conta de armazenamento é armazenada na variável **$storageAcct**. Os nomes de conta de armazenamento devem ser exclusivo e, portanto, use `Get-Random` para acrescentar um número ao nome e torná-lo exclusivo. 

```azurepowershell-interactive 
$storageAcct = New-AzStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure
Agora você pode criar seu primeiro compartilhamento de arquivos do Azure. Você pode criar um compartilhamento de arquivos usando o cmdlet [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare). Esse exemplo cria um compartilhamento chamado `myshare`.

```azurepowershell-interactive
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

Os nomes dos compartilhamentos precisam ter todos letras minúsculas, números e hifens, mas não podem começar com um hífen. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Usar o compartilhamento de arquivos do Azure
Os Arquivos do Azure fornecem dois métodos para trabalhar com arquivos e pastas dentro do seu compartilhamento de arquivos do Azure: o padrão do setor [protocolo SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) e o [protocolo REST de arquivo](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Para montar um compartilhamento de arquivos com SMB, consulte o documento abaixo com base em seu sistema operacional:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Usar um compartilhamento de arquivos do Azure com o protocolo REST de arquivo 
É possível trabalhar diretamente com o protocolo REST de arquivo (ou seja, modelar manualmente as chamadas REST HTTP por conta própria), mas a maneira mais comum de usar o protocolo REST de arquivo é usar o módulo do Azure PowerShell, a [CLI do Azure](storage-how-to-use-files-cli.md) ou um SDK de Armazenamento do Azure, que fornecem um bom wrapper do protocolo REST de arquivo na linguagem de script/programação de sua escolha.  

Na maioria dos casos, você usará o compartilhamento de arquivos do Azure em vez do protocolo SMB, pois isso permite usar os aplicativos e as ferramentas existentes que você espera poder usar, mas há várias razões por que é vantajoso usar a API REST de arquivo em vez de SMB, a saber:

- Você está buscando seu compartilhamento de arquivos no Cloud Shell do PowerShell (o que não pode montar os compartilhamentos de arquivos via SMB).
- Você precisa executar um script ou aplicativo de um cliente que não consegue montar um compartilhamento de SMB, como clientes locais que não têm a porta 445 desbloqueada.
- Você está aproveitando as vantagens dos recursos sem servidor, como o [Azure Functions](../../azure-functions/functions-overview.md). 

Os exemplos a seguir mostram como usar o módulo do Azure PowerShell para manipular o compartilhamento de arquivos do Azure com o protocolo REST de arquivo. 

#### <a name="create-directory"></a>Criar diretório
Para criar um novo diretório chamado *myDirectory* na raiz do seu compartilhamento de arquivos do Azure, use o cmdlet [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory).

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Carregar um arquivo
Para demonstrar como carregar um arquivo usando o cmdlet [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent), primeiro precisamos criar um arquivo dentro da unidade temporária do Cloud Shell do PowerShell para upload. 

Este exemplo coloca a data e a hora atuais em um novo arquivo na unidade temporária e o carrega no compartilhamento de arquivos.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Se você estiver executando o PowerShell localmente, substitua `C:\Users\ContainerAdministrator\CloudDrive\` por um caminho existente em seu computador.

Depois de carregar o arquivo, você pode usar o cmdlet [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) para verificar se o arquivo foi carregado no compartilhamento de arquivos do Azure. 

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

#### <a name="download-a-file"></a>Baixar um arquivo
Você pode usar o cmdlet [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) para baixar uma cópia do arquivo que você acabou de carregar na unidade temporária do Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
     -Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

Depois de baixar o arquivo, você pode usar o `Get-ChildItem` para ver se o arquivo foi baixado na unidade temporária do Cloud Shell do PowerShell.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

#### <a name="copy-files"></a>Copiar arquivos
Uma tarefa comum é copiar arquivos de um compartilhamento de arquivos para outro ou de/para um contêiner do Armazenamento de Blobs do Azure. Para demonstrar essa funcionalidade, você pode criar um novo compartilhamento e copiar o arquivo recém-carregado para esse novo compartilhamento usando o cmdlet [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy). 

```azurepowershell-interactive
New-AzStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Agora, se você listar os arquivos no novo compartilhamento, deverá ver o arquivo copiado.

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

Embora o cmdlet `Start-AzStorageFileCopy` seja conveniente para movimentações ad hoc de arquivos entre compartilhamentos de arquivos do Azure e contêineres de armazenamentos de Blobs do Azure, recomendamos o AzCopy para movimentações maiores (em termos de quantidade ou tamanho de arquivos que estão sendo movidos). Saiba mais sobre [AzCopy para Windows](../common/storage-use-azcopy.md) e [AzCopy para Linux](../common/storage-use-azcopy-linux.md). O AzCopy deve ser instalado localmente; ele não está disponível no Cloud Shell. 

## <a name="create-and-manage-share-snapshots"></a>Criar e gerenciar instantâneos de compartilhamento
Outra tarefa útil que você pode fazer com um compartilhamento de arquivos do Azure é criar instantâneos de compartilhamento. Um instantâneo preserva um ponto no tempo para um compartilhamento de arquivos do Azure. Os instantâneos de compartilhamento são semelhantes às tecnologias de sistema operacional que você talvez já conheça, como:
- [VSS (Serviço de Cópias de Sombra de Volume)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) para sistemas de arquivos Windows, como NTFS e ReFS
- Instantâneos do [LVM (Gerenciador de Volumes Lógicos)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux
- Instantâneos do [APFS (Sistema de arquivos da Apple)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS. 
 Você pode criar um instantâneo de compartilhamento para um compartilhamento usando o método `Snapshot` no objeto do PowerShell para um compartilhamento de arquivo, que é recuperado com o cmdlet [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare). 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Procurar instantâneos de compartilhamento
Você pode procurar o conteúdo do instantâneo de compartilhamento passando a referência do instantâneo (`$snapshot`) para o parâmetro `-Share` do cmdlet `Get-AzStorageFile`.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Listar instantâneos de compartilhamento
Você pode ver a lista de instantâneos tirados para seu compartilhamento com o comando a seguir.

```azurepowershell-interactive
Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Restaurar de um instantâneo de compartilhamento
Você pode restaurar um arquivo usando o comando `Start-AzStorageFileCopy` usado anteriormente. Para fins deste início rápido, iremos primeiro excluir nosso arquivo `SampleUpload.txt` carregado anteriormente para que possa ser restaurado do instantâneo.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"
 # Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Excluir um instantâneo de compartilhamento
Você pode excluir um instantâneo de compartilhamento usando o cmdlet [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare), com a variável que contém a referência `$snapshot` para o parâmetro `-Share`.

```azurepowershell-interactive
Remove-AzStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Limpar recursos
Quando tiver concluído, você poderá usar o cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos relacionados. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Como alternativa, você pode remover recursos individualmente:

- Para remover os compartilhamentos de arquivos do Azure que criamos para este início rápido.

    ```azurepowershell-interactive
    Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
        Remove-AzStorageShare -Context $storageAcct.Context -Name $_.Name
    }
    ```

- Para remover a própria conta de armazenamento (isso removerá implicitamente os compartilhamentos de arquivos do Azure que criamos, bem como qualquer outro recurso de armazenamento criado, como um contêiner do Armazenamento de Blobs do Azure).

    ```azurepowershell-interactive
    Remove-AzStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [O que Arquivos do Azure?](storage-files-introduction.md)
