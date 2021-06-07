---
title: Usar o PowerShell para gerenciar ACLs no Azure Data Lake Storage Gen2
description: Use os cmdlets do PowerShell para gerenciar listas de controle de acesso (ACL) em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd522355d30564d84fec15bdc57c7397c1e6cfe4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104702534"
---
# <a name="use-powershell-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Usar o PowerShell para gerenciar ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar o PowerShell para obter, definir e atualizar as listas de controle de acesso de diretórios e arquivos. 

A herança de ACL já está disponível para novos itens filho que são criados em um diretório pai. Mas você também pode adicionar, atualizar e remover ACLs recursivamente nos itens filho existentes de um diretório pai sem precisar fazer essas alterações individualmente para cada item filho. 

[Referência](/powershell/module/Az.Storage/)  |  do Amostras de ACL [recursivas](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)  |  [Enviar comentários](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento precisa ter o HNS (namespace hierárquico) habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar um.

- CLI do Azure versão `2.6.0` ou superior

- Uma das seguintes permissões de segurança:

  - Uma [entidade de segurança](../../role-based-access-control/overview.md#security-principal) de Azure Active Directory (AD) provisionada à qual foi atribuída a função de proprietário de dados do [blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) no escopo do contêiner de destino, grupo de recursos pai ou assinatura.  

  - Usuário proprietário do contêiner ou diretório de destino ao qual você planeja aplicar as configurações de ACL. Para definir ACLs recursivamente, isso inclui todos os itens filho no contêiner ou diretório de destino.
  
  - Chave da conta de armazenamento.

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

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opção 1: obter autorização usando o Azure Active Directory (AD)

> [!NOTE]
> Se você estiver usando Azure Active Directory (Azure AD) para autorizar o acesso, verifique se a entidade de segurança recebeu a [função de proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos de alterá-las, consulte  [modelo de controle de acesso em Azure data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

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

## <a name="get-acls"></a>Obter ACLs

Obtenha a ACL de um diretório ou arquivo usando o `Get-AzDataLakeGen2Item` cmdlet.

Este exemplo obtém a ACL do diretório raiz de um **contêiner** e, em seguida, imprime a ACL no console.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Este exemplo obtém a ACL de um **diretório** e, em seguida, imprime a ACL no console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Este exemplo obtém a ACL de um **arquivo** e, em seguida, imprime a ACL no console.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

A imagem a seguir mostra a saída depois de obter a ACL de um diretório.

![Obter saída de ACL para o diretório](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

Neste exemplo, o usuário proprietário tem permissões de leitura, gravação e execução. O grupo proprietário tem permissões de somente leitura e execução. Para obter mais informações sobre as listas de controle de acesso, confira [Controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="set-acls"></a>Definir ACLs

Ao *definir* uma ACL, você **substitui** toda a ACL, incluindo todas as entradas. Se você quiser alterar o nível de permissão de uma entidade de segurança ou adicionar uma nova entidade de segurança à ACL sem afetar outras entradas existentes, deverá *Atualizar* a ACL em vez disso. Para atualizar uma ACL em vez de substituí-la, consulte a seção [Update ACLs](#update-acls) deste artigo.  

Se você optar por *definir* a ACL, deverá adicionar uma entrada para o usuário proprietário, uma entrada para o grupo proprietário e uma entrada para todos os outros usuários. Para saber mais sobre o usuário proprietário, o grupo proprietário e todos os outros usuários, consulte [usuários e identidades](data-lake-storage-access-control.md#users-and-identities).

Esta seção mostra como:

- Definir uma ACL
- Definir ACLs recursivamente

### <a name="set-an-acl"></a>Definir uma ACL

Use o `set-AzDataLakeGen2ItemAclObject` cmdlet para criar uma ACL para o usuário proprietário, o grupo proprietário ou outros usuários. Em seguida, use o `Update-AzDataLakeGen2Item` cmdlet para confirmar a ACL.

Este exemplo define a ACL no diretório raiz de um **contêiner** para o usuário proprietário, o grupo proprietário ou outros usuários e, em seguida, imprime a ACL no console.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Este exemplo define a ACL em um **diretório** para o usuário proprietário, o grupo proprietário ou outros usuários e, em seguida, imprime a ACL no console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

> [!NOTE]
> Se você quiser definir uma entrada ACL **padrão** , use o parâmetro **-DefaultScope** ao executar o comando **set-AzDataLakeGen2ItemAclObject** . Por exemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`. 

Este exemplo define a ACL em um **arquivo** para o usuário proprietário, o grupo proprietário ou outros usuários e, em seguida, imprime a ACL no console.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

> [!NOTE]
> Para definir a ACL de um grupo ou usuário específico, use suas respectivas IDs de objeto. Por exemplo, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ou `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

A imagem a seguir mostra a saída depois de definir a ACL de um arquivo.

![Obter saída de ACL para o arquivo](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

Neste exemplo, o usuário proprietário e o grupo proprietário têm permissões de somente leitura e gravação. Todos os outros usuários têm permissões de gravação e execução. Para obter mais informações sobre as listas de controle de acesso, confira [Controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Definir ACLs recursivamente

Defina ACLs recursivamente usando o cmdlet **set-AzDataLakeGen2AclRecursive** .

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Essas entradas fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece a um usuário específico as permissões de leitura e execução da ID de objeto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx".

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Se você quiser definir uma entrada ACL **padrão** , use o parâmetro **-DefaultScope** ao executar o comando **set-AzDataLakeGen2ItemAclObject** . Por exemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Para ver um exemplo que define ACLs recursivamente em lotes especificando um tamanho de lote, consulte o artigo de referência [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

## <a name="update-acls"></a>Atualizar ACLs

Ao *Atualizar* uma ACL, você modifica a ACL em vez de substituir a ACL. Por exemplo, você pode adicionar uma nova entidade de segurança à ACL sem afetar outras entidades de segurança listadas na ACL.  Para substituir a ACL em vez de atualizá-la, consulte a seção [set ACLs](#set-acls) deste artigo.

Para atualizar uma ACL, crie um novo objeto ACL com a entrada ACL que você deseja atualizar e, em seguida, use esse objeto em atualizar a operação de ACL. Não obtenha a ACL existente, basta fornecer as entradas de ACL a serem atualizadas.

Esta seção mostra como:

- Atualizar uma ACL
- Atualizar ACLs recursivamente

### <a name="update-an-acl"></a>Atualizar uma ACL

Primeiro, obtenha a ACL. Em seguida, use o `set-AzDataLakeGen2ItemAclObject` cmdlet para adicionar ou atualizar uma entrada de ACL. Use o `Update-AzDataLakeGen2Item` cmdlet para confirmar a ACL.

Este exemplo cria ou atualiza a ACL em um **diretório** para um usuário.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> Se você quiser atualizar uma entrada ACL **padrão** , use o parâmetro **-DefaultScope** ao executar o comando **set-AzDataLakeGen2ItemAclObject** . Por exemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`.

### <a name="update-acls-recursively"></a>Atualizar ACLs recursivamente

Atualize as ACLs recursivamente usando o cmdlet  **Update-AzDataLakeGen2AclRecursive** .

Este exemplo atualiza uma entrada ACL com permissão de gravação.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Para definir a ACL de um grupo ou usuário específico, use suas respectivas IDs de objeto. Por exemplo, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ou `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Para ver um exemplo que atualiza as ACLs recursivamente em lotes especificando um tamanho de lote, consulte o artigo de referência [Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) .

## <a name="remove-acl-entries"></a>Remover entradas de ACL

Esta seção mostra como:

- Remover uma entrada de ACL
- Remover entradas de ACL recursivamente

### <a name="remove-an-acl-entry"></a>Remover uma entrada de ACL

Este exemplo remove uma entrada de uma ACL existente.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="remove-acl-entries-recursively"></a>Remover entradas de ACL recursivamente

Você pode remover uma ou mais entradas de ACL recursivamente. Para remover uma entrada de ACL, crie um novo objeto ACL para a entrada de ACL a ser removida e use esse objeto em remover a operação de ACL. Não obtenha a ACL existente, basta fornecer as entradas de ACL a serem removidas.

Remova as entradas ACL usando o cmdlet **Remove-AzDataLakeGen2AclRecursive** .

Este exemplo remove uma entrada ACL do diretório raiz do contêiner.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Se você quiser remover uma entrada ACL **padrão** , use o parâmetro **-DefaultScope** ao executar o comando **set-AzDataLakeGen2ItemAclObject** . Por exemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Para ver um exemplo que remove ACLs recursivamente em lotes especificando um tamanho de lote, consulte o artigo de referência [Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) .

## <a name="recover-from-failures"></a>Recuperar de falhas

Você pode encontrar erros de tempo de execução ou de permissão ao modificar ACLs recursivamente. 

Para erros de tempo de execução, reinicie o processo desde o início. Poderão ocorrer erros de permissão se a entidade de segurança não tiver permissão suficiente para modificar a ACL de um diretório ou arquivo que está na hierarquia de diretório que está sendo modificada. Resolva o problema de permissão e, em seguida, escolha retomar o processo a partir do ponto de falha usando um token de continuação ou reiniciar o processo do início. Você não precisará usar o token de continuação se preferir reiniciar desde o início. Você pode reaplicar entradas de ACL sem nenhum impacto negativo.

Este exemplo retorna resultados para a variável e, em seguida, canaliza entradas com falha para uma tabela formatada.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

Com base na saída da tabela, você pode corrigir quaisquer erros de permissão e, em seguida, retomar a execução usando o token de continuação.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Para ver um exemplo que define ACLs recursivamente em lotes especificando um tamanho de lote, consulte o artigo de referência [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

Se você quiser que o processo seja concluído sem interrupções por erros de permissão, poderá especificar isso.

Este exemplo usa o `ContinueOnFailure` parâmetro para que a execução continue mesmo que a operação encontre um erro de permissão. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

Para ver um exemplo que define ACLs recursivamente em lotes especificando um tamanho de lote, consulte o artigo de referência [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Veja também

- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Cmdlets do PowerShell do Armazenamento](/powershell/module/az.storage)
- [Modelo de controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [ACLs (listas de controle de acesso) no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
