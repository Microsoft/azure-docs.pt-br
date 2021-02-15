---
title: Usar o PowerShell para criar uma SAS de delegação de usuário para um contêiner ou BLOB
titleSuffix: Azure Storage
description: Saiba como criar uma SAS de delegação de usuário com credenciais de Azure Active Directory usando o PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 875b2a9f35562dd8f0d5df3c631e5ade1e3fbf75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714525"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Criar uma SAS de delegação de usuário para um contêiner ou BLOB com o PowerShell

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar as credenciais do Azure Active Directory (AD do Azure) para criar uma SAS de delegação de usuário para um contêiner ou BLOB com Azure PowerShell.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

Para criar uma SAS de delegação de usuário com o PowerShell, instale a versão 1.10.0 ou posterior do módulo AZ. Storage. Siga estas etapas para instalar a versão mais recente do módulo:

1. Desinstale as instalações anteriores do Azure PowerShell:

    - Remova as instalações anteriores do Azure PowerShell do Windows usando a configuração **Aplicativos e recursos** em **Configurações**.
    - Remova todos os módulos do **Azure** de `%Program Files%\WindowsPowerShell\Modules` .

1. Verifique se tem a versão mais recente do PowerShellGet instalado. Abra uma janela do Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Feche e reabra a janela do PowerShell depois de instalar o PowerShellGet.

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Certifique-se de ter instalado Azure PowerShell versão 3.2.0 ou posterior. Execute o seguinte comando para instalar a versão mais recente do módulo do Azure Storage PowerShell:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Feche e reabra a janela do PowerShell.

Para verificar qual versão do módulo AZ. Storage está instalada, execute o seguinte comando:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Para obter mais informações sobre como instalar o Azure PowerShell, consulte [Instalar o Azure PowerShell com o PowerShellGet](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Entrar no Azure PowerShell com o Azure AD

Chame o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) para entrar com sua conta do Azure AD:

```powershell
Connect-AzAccount
```

Para obter mais informações sobre como entrar com o PowerShell, consulte [entrar com Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-azure-rbac"></a>Atribuir permissões com o RBAC do Azure

Para criar uma SAS de delegação de usuário do Azure PowerShell, a conta do Azure AD usada para entrar no PowerShell deve ser atribuída a uma função que inclui a ação **Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey** . Essa permissão permite que a conta do Azure AD solicite a *chave de delegação do usuário*. A chave de delegação de usuário é usada para assinar a SAS de delegação de usuário. A função que fornece a ação **Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey** deve ser atribuída no nível da conta de armazenamento, no grupo de recursos ou na assinatura. Para obter mais informações sobre as permissões RBAC do Azure para criar uma SAS de delegação de usuário, consulte a seção **atribuir permissões com o RBAC do Azure** em [criar uma SAS de delegação de usuário](/rest/api/storageservices/create-user-delegation-sas).

Se você não tiver permissões suficientes para atribuir funções do Azure a uma entidade de segurança do Azure AD, talvez seja necessário solicitar ao proprietário da conta ou ao administrador que atribua as permissões necessárias.

O exemplo a seguir atribui a função de **colaborador de dados de blob de armazenamento** , que inclui a ação **Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey** . A função tem o escopo no nível da conta de armazenamento.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Para obter mais informações sobre as funções internas que incluem a ação **Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey** , consulte [funções internas do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Usar as credenciais do Azure AD para proteger uma SAS

Quando você cria uma SAS de delegação de usuário com Azure PowerShell, a chave de delegação de usuário usada para assinar a SAS é criada implicitamente. A hora de início e a hora de expiração que você especifica para a SAS também são usadas como a hora de início e a hora de expiração da chave de delegação do usuário. 

Como o intervalo máximo em que a chave de delegação de usuário é válida é de 7 dias a partir da data de início, você deve especificar um tempo de expiração para a SAS que está dentro de 7 dias da hora de início. A SAS é inválida depois que a chave de delegação do usuário expira, portanto, uma SAS com um tempo de expiração superior a 7 dias ainda será válida por sete dias.

Para criar uma SAS de delegação de usuário para um contêiner ou BLOB com Azure PowerShell, primeiro crie um novo objeto de contexto de armazenamento do Azure, especificando o `-UseConnectedAccount` parâmetro. O `-UseConnectedAccount` parâmetro especifica que o comando cria o objeto de contexto na conta do Azure AD com a qual você se conectou.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Criar uma SAS de delegação de usuário para um contêiner

Para retornar um token SAS de delegação de usuário para um contêiner, chame o comando [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) , passando o objeto de contexto de armazenamento do Azure que você criou anteriormente.

O exemplo a seguir retorna um token SAS de delegação de usuário para um contêiner. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

O token SAS de delegação de usuário retornado será semelhante a:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Criar uma SAS de delegação de usuário para um blob

Para retornar um token SAS de delegação de usuário para um blob, chame o comando [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) , passando o objeto de contexto de armazenamento do Azure que você criou anteriormente.

A sintaxe a seguir retorna uma SAS de delegação de usuário para um blob. O exemplo especifica o `-FullUri` parâmetro, que retorna o URI do blob com o token SAS anexado. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

O URI de SAS de delegação de usuário retornado será semelhante a:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Uma SAS de delegação de usuário não dá suporte à definição de permissões com uma política de acesso armazenada.

## <a name="revoke-a-user-delegation-sas"></a>Revogar uma SAS de delegação de usuário

Para revogar uma SAS de delegação de usuário do Azure PowerShell, chame o comando **REVOKE-AzStorageAccountUserDelegationKeys** . Esse comando revoga todas as chaves de delegação de usuário associadas à conta de armazenamento especificada. Todas as assinaturas de acesso compartilhado associadas a essas chaves são invalidadas.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Tanto a chave de delegação do usuário quanto as atribuições de função do Azure são armazenadas em cache pelo armazenamento do Azure, portanto, pode haver um atraso entre quando você inicia o processo de revogação e quando uma SAS de delegação de usuário existente se torna inválida.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma SAS de delegação de usuário (API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Operação de obtenção de chave de delegação de usuário](/rest/api/storageservices/get-user-delegation-key)
