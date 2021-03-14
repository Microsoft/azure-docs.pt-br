---
title: 'Use o PowerShell para gerenciar dados: Azure Data Lake Storage Gen2'
description: Use os cmdlets do PowerShell para gerenciar diretórios e arquivos em contas de armazenamento que têm o namespace hierárquico habilitado.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe7ad949d7301a035eb17d2b4d8d678dfb2e0546
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650178"
---
# <a name="use-powershell-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Usar o PowerShell para gerenciar diretórios e arquivos no Azure Data Lake Storage Gen2

Este artigo mostra como usar o PowerShell para criar e gerenciar diretórios e arquivos em contas de armazenamento que têm um namespace hierárquico.

Para saber mais sobre como obter, definir e atualizar as listas de controle de acesso (ACL) de diretórios e arquivos, consulte [usar o PowerShell para gerenciar ACLs no Azure data Lake Storage Gen2](data-lake-storage-acl-powershell.md).

[Referência](/powershell/module/Az.Storage/)  |  do Mapeamento de Gen1 [para Gen2](#gen1-gen2-map)  |  [Enviar comentários](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem o namespace hierárquico habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar um.

- O .NET Framework é 4.7.2 ou superior instalado. Consulte [baixar .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).

- Versão do PowerShell `5.1` ou superior.

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

1. Verifique se a versão do PowerShell instalada é `5.1` ou superior usando o comando a seguir.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```

   Para atualizar sua versão do PowerShell, consulte [atualizando o Windows PowerShell existente](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)

2. Instale o módulo **AZ. Storage** .

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Para obter mais informações sobre como instalar módulos do PowerShell, consulte [instalar o Azure PowerShell Module](/powershell/azure/install-az-ps)

## <a name="connect-to-the-account"></a>Conectar à conta

Escolha como você deseja que os comandos obtenham autorização para a conta de armazenamento. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-azure-ad"></a>Opção 1: obter autorização usando Azure Active Directory (Azure AD)

Com essa abordagem, o sistema garante que sua conta de usuário tenha as atribuições apropriadas do Azure RBAC (controle de acesso baseado em função) e permissões de ACL.

1. Abra uma janela de comando do Windows PowerShell e entre em sua assinatura do Azure com o `Connect-AzAccount` comando e siga as instruções na tela.

   ```powershell
   Connect-AzAccount
   ```

2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento na qual você deseja criar e gerenciar diretórios. Neste exemplo, substitua o `<subscription-id>` valor do espaço reservado pela ID da sua assinatura.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ``` 

3. Obtenha o contexto da conta de armazenamento.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opção 2: obter autorização usando a chave da conta de armazenamento

Com essa abordagem, o sistema não verifica as permissões de ACL ou RBAC do Azure. Obtenha o contexto da conta de armazenamento usando uma chave de conta.

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="create-a-container"></a>Criar um contêiner

Um contêiner atua como um sistema de arquivos para seus arquivos. Você pode criar um usando o `New-AzStorageContainer` cmdlet. 

Este exemplo cria um contêiner chamado `my-file-system` .

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório usando o `New-AzDataLakeGen2Item` cmdlet. 

Este exemplo adiciona um diretório chamado `my-directory` a um contêiner.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Este exemplo adiciona o mesmo diretório, mas também define as permissões, umask, valores de propriedade e valores de metadados. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Obter propriedades do diretório

Este exemplo obtém um diretório usando o `Get-AzDataLakeGen2Item` cmdlet e, em seguida, imprime valores de propriedade no console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

> [!NOTE]
> Para obter o diretório raiz do contêiner, omita o `-Path` parâmetro.

## <a name="rename-or-move-a-directory"></a>Renomear ou mover um diretório

Renomeie ou mova um diretório usando o `Move-AzDataLakeGen2Item` cmdlet.

Este exemplo renomeia um diretório do nome `my-directory` para o nome `my-new-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Use o `-Force` parâmetro se você quiser substituir sem prompts.

Este exemplo move um diretório chamado `my-directory` para um subdiretório de `my-directory-2` nome `my-subdirectory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Excluir um diretório

Exclua um diretório usando o `Remove-AzDataLakeGen2Item` cmdlet.

Este exemplo exclui um diretório chamado `my-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Você pode usar o `-Force` parâmetro para remover o arquivo sem um prompt.

## <a name="download-from-a-directory"></a>Baixar de um diretório

Baixe um arquivo de um diretório usando o `Get-AzDataLakeGen2ItemContent` cmdlet.

Este exemplo baixa um arquivo chamado `upload.txt` de um diretório chamado `my-directory`.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Liste o conteúdo de um diretório usando o `Get-AzDataLakeGen2ChildItem` cmdlet. Você pode usar o parâmetro opcional `-OutputUserPrincipalName` para obter o nome (em vez da ID de objeto) de usuários.

Este exemplo lista o conteúdo de um diretório chamado `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

O exemplo a seguir lista `ACL` as `Permissions` Propriedades,, e `Group` `Owner` de cada item no diretório. O `-FetchProperty` parâmetro é necessário para obter valores para a `ACL` propriedade. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> Para listar o conteúdo do diretório raiz do contêiner, omita o `-Path` parâmetro.

## <a name="upload-a-file-to-a-directory"></a>Carregar um arquivo em um diretório

Carregue um arquivo em um diretório usando o `New-AzDataLakeGen2Item` cmdlet.

Este exemplo carrega um arquivo chamado `upload.txt` em um diretório chamado `my-directory`. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Este exemplo carrega o mesmo arquivo, mas, em seguida, define as permissões, umask, valores de propriedade e valores de metadados do arquivo de destino. Este exemplo também imprime esses valores no console.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> Para carregar um arquivo no diretório raiz do contêiner, omita o `-Path` parâmetro.

## <a name="show-file-properties"></a>Mostrar propriedades do arquivo

Este exemplo obtém um arquivo usando o `Get-AzDataLakeGen2Item` cmdlet e, em seguida, imprime valores de propriedade no console.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Excluir um arquivo

Exclua um arquivo usando o `Remove-AzDataLakeGen2Item` cmdlet.

Este exemplo exclui um arquivo chamado `upload.txt` . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Você pode usar o `-Force` parâmetro para remover o arquivo sem um prompt.

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Mapeamento de Gen1 para Gen2

A tabela a seguir mostra como os cmdlets usados para Data Lake Storage Gen1 são mapeados para os cmdlets para Data Lake Storage Gen2.

|Data Lake Storage Gen1 cmdlet| Data Lake Storage Gen2 cmdlet| Observações |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Por padrão, o cmdlet Get-AzDataLakeGen2ChildItem lista apenas os itens filho de primeiro nível. O parâmetro-Recurse lista os itens filhos recursivamente. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Os itens de saída do cmdlet Get-AzDataLakeGen2Item têm estas propriedades: ACL, proprietário, grupo, permissão.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|O cmdlet Get-AzDataLakeGen2FileContent baixar o conteúdo do arquivo para o arquivo local.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Esse cmdlet carrega o novo conteúdo do arquivo de um arquivo local.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|O cmdlet Update-AzDataLakeGen2Item atualiza apenas um único item e não recursivamente. Se quiser atualizar recursivamente, liste os itens usando o cmdlet Get-AzDataLakeStoreChildItem e, em seguida, pipeline para o cmdlet Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|O cmdlet Get-AzDataLakeGen2Item relatará um erro se o item não existir.|

## <a name="see-also"></a>Confira também

- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Cmdlets do PowerShell do Armazenamento](/powershell/module/az.storage)
