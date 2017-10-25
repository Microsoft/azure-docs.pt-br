---
title: Como usar o PowerShell para gerenciar os Arquivos do Azure | Microsoft Docs
description: Saiba como usar o PowerShell para gerenciar os Arquivos do Azure.
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: f919e1880f709b416867a29de14f1dcc63a165fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-powershell-to-manage-azure-files"></a>Como usar o PowerShell para gerenciar os Arquivos do Azure
Você pode usar o Azure PowerShell para criar e gerenciar compartilhamentos de arquivos.

## <a name="install-the-powershell-cmdlets-for-azure-storage"></a>Instalar os cmdlets do PowerShell para Armazenamento do Azure
Para se preparar para usar o PowerShell, baixe e instale os cmdlets do PowerShell do Azure. Consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azureps-cmdlets-docs) para obter o ponto e as instruções de instalação.

> [!NOTE]
> É recomendável baixar e instalar ou atualizar para o módulo mais recente do PowerShell do Azure.
> 
> 

Abra uma janela do Azure PowerShell clicando em **Iniciar** e digitando **Windows PowerShell**. A janela do PowerShell carregará o módulo do Azure PowerShell para você.

## <a name="create-a-context-for-your-storage-account-and-key"></a>Criar um contexto para sua conta e chave de armazenamento
Crie o contexto da conta de armazenamento. O contexto encapsula a conta de armazenamento e a chave da conta. Para obter instruções sobre como copiar a chave da conta no [portal do Azure](https://portal.azure.com), veja [Exibir e copiar chaves de acesso de armazenamento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#view-and-copy-storage-access-keys).

Substitua `storage-account-name` e `storage-account-key` pelo nome e chave da sua conta de armazenamento no exemplo a seguir.

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

## <a name="create-a-new-file-share"></a>Criar um novo compartilhamento de arquivo
Crie o novo compartilhamento, denominado `logs`.

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

Agora você tem um compartilhamento de arquivo no armazenamento de arquivos. Em seguida, adicionaremos um diretório e um arquivo.

> [!IMPORTANT]
> O nome do seu compartilhamento de arquivo deve estar em minúsculas. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://msdn.microsoft.com/library/azure/dn167011.aspx).
> 
> 

## <a name="create-a-directory-in-the-file-share"></a>Criar um diretório no compartilhamento de arquivos
Crie um diretório no compartilhamento. No exemplo a seguir, o diretório é denominado `CustomLogs`:

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

## <a name="upload-a-local-file-to-the-directory"></a>Carregar um arquivo local no diretório
Agora, carregue um arquivo local no diretório. O exemplo a seguir carrega um arquivo de `C:\temp\Log1.txt`. Edite o caminho do arquivo para que ele aponte para um arquivo válido em seu computador local.

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

## <a name="list-the-files-in-the-directory"></a>Listar os arquivos no diretório
Para ver o arquivo no diretório, você pode listar todos os arquivos do diretório. Esse comando retorna os arquivos e subdiretórios (se houver) no diretório CustomLogs.

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

Get-AzureStorageFile retorna uma lista de arquivos e diretórios para qualquer objeto de diretório que é passado. "Get-AzureStorageFile -Share $s" retorna uma lista de arquivos e diretórios no diretório raiz. Para obter uma lista de arquivos em um subdiretório, você precisa passar o subdiretório para Get-AzureStorageFile. É o que isso faz: a primeira parte do comando até o pipe retorna uma instância de diretório do subdiretório CustomLogs. Em seguida, ela é passada para Get-AzureStorageFile, que retorna os arquivos e diretórios em CustomLogs.

## <a name="copy-files"></a>Copiar arquivos
A partir da versão 0.9.7 do Azure PowerShell, você pode copiar um arquivo em outro arquivo, um arquivo em um blob ou um blob em um arquivo. A seguir, demonstramos como executar essas operações de cópia usando cmdlets do PowerShell.

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```
## <a name="next-steps"></a>Próximas etapas
Veja estes links para obter mais informações sobre o Arquivos do Azure.

* [Perguntas frequentes](../storage-files-faq.md)
* [Solução de problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Solução de problemas no Linux](storage-troubleshoot-linux-file-connection-problems.md)    