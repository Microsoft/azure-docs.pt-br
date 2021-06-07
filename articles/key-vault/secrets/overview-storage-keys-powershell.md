---
title: Conta de armazenamento gerenciado do Azure Key Vault - versão PowerShell
description: O recurso de conta de armazenamento gerenciada fornece uma integração perfeita entre o Azure Key Vault e uma conta de armazenamento do Azure.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/10/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aa4daf7c8d951a7b42479533a3a5a50c06c5c144
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748476"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Gerenciar chaves de conta de armazenamento com o Key Vault e o Azure PowerShell
> [!IMPORTANT]
> Recomendamos usar a integração do Armazenamento do Azure ao Azure AD (Azure Active Directory), o serviço de gerenciamento de identidades e acesso baseado em nuvem da Microsoft. A integração do Azure AD está disponível para [blobs e filas do Azure](../../storage/common/storage-auth-aad.md) e fornece acesso baseado em token OAuth2 ao Armazenamento do Azure (assim como o Azure Key Vault).
> O Azure AD permite que você autentique seu aplicativo cliente usando uma identidade de aplicativo ou de usuário, em vez das credenciais da conta de armazenamento. Você pode usar uma [identidade gerenciada do Azure AD](../../active-directory/managed-identities-azure-resources/index.yml) durante a execução no Azure. As identidades gerenciadas eliminam a necessidade de autenticação de cliente e do armazenamento de credenciais no ou com o seu aplicativo. Use a solução abaixo apenas quando a autenticação do Azure AD não for possível.

Uma conta de armazenamento do Azure usa credenciais compostas por um nome de conta e uma chave. A chave é gerada automaticamente e serve como uma senha, em vez de como uma chave de criptografia. O Key Vault gerencia as chaves de conta de armazenamento regenerando-as periodicamente na conta de armazenamento e fornece tokens de Assinatura de Acesso Compartilhado para acesso delegado aos recursos na sua conta de armazenamento.

Use o recurso de chave de conta de armazenamento gerenciada do Key Vault para listar (sincronizar) as chaves com uma conta de armazenamento do Azure e regenerar (girar) as chaves periodicamente. Gerencie as chaves de contas de armazenamento e contas de armazenamento Clássicas.

Ao usar o recurso de chave de conta de armazenamento gerenciada, considere os seguintes pontos:

- Os valores de chaves nunca são retornados em resposta a um chamador.
- Somente o Key Vault deve gerenciar suas chaves de conta de armazenamento. Não gerencie as chaves por conta própria e evite interferir nos processos do Key Vault.
- Um só objeto do Key Vault deve gerenciar as chaves de conta de armazenamento. Não permita o gerenciamento de chaves em vários objetos.
- Regenere as chaves usando apenas o Key Vault. Não regenere manualmente as chaves da conta de armazenamento.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>ID do aplicativo da entidade de serviço

Um locatário do Azure AD fornece a cada aplicativo registrado uma [entidade de serviço](../../active-directory/develop/developer-glossary.md#service-principal-object). A entidade de serviço serve como a ID do aplicativo, que é usada durante a configuração de autorização para acesso a outros recursos do Azure por meio do Azure RBAC.

O Key Vault é um aplicativo da Microsoft que é pré-registrado em todos os locatários do Azure AD. O Key Vault é registrado na mesma ID do Aplicativo em cada nuvem do Azure.

| Locatários | Nuvem | ID do aplicativo |
| --- | --- | --- |
| AD do Azure | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| AD do Azure | Público do Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Outro  | Qualquer | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia, primeiro, você precisará fazer o seguinte:

- [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).
- [Criar um cofre de chave](quick-create-powershell.md)
- [Criar uma conta de Armazenamento do Azure](../../storage/common/storage-account-create.md?tabs=azure-powershell). O nome da conta de armazenamento precisa conter apenas letras minúsculas e números. O nome precisa ter entre 3 e 24 caracteres.


## <a name="manage-storage-account-keys"></a>Gerenciar chaves de conta de armazenamento

### <a name="connect-to-your-azure-account"></a>Conectar-se à sua conta do Azure

Autentique a sessão do PowerShell usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

```azurepowershell-interactive
Connect-AzAccount
```
Caso você tenha várias assinaturas do Azure, liste-as usando o cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) e especifique a assinatura que deseja usar com o cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Definir variáveis

Primeiro, defina as variáveis a serem usadas pelos cmdlets do PowerShell nas etapas a seguir. Certifique-se de atualizar os espaços reservados “YourResourceGroupName”, "YourStorageAccountName" e "YourKeyVaultName" e definir $keyVaultSpAppId como `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` (como especificado acima em [ID do Aplicativo da Entidade de Serviço](#service-principal-application-id)).

Também usaremos os cmdlets [Get-AzContext](/powershell/module/az.accounts/get-azcontext) e [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) do Azure PowerShell para obter sua ID de usuário e o contexto da sua conta de armazenamento do Azure.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1" #(key1 or key2 are allowed)

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

```
>[!Note]
> Para a conta de armazenamento Clássica, use "primary" e "secondary" em $storageAccountKey <br>
> Use 'Get-AzResource -Name "ClassicStorageAccountName" -ResourceGroupName $resourceGroupName' em vez de 'Get-AzStorageAccount' para a conta de armazenamento Clássica

### <a name="give-key-vault-access-to-your-storage-account"></a>Permitir acesso ao Key Vault na sua conta de armazenamento

Antes que o Key Vault possa acessar e gerenciar suas chaves de conta de armazenamento, você deve autorizar o acesso a sua conta de armazenamento. O aplicativo do Key Vault requer permissões para *lista* e *regenerar* chaves para sua conta de armazenamento. Essas permissões são habilitadas por meio da função interna [Função de Serviço do Operador da Chave de Conta de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-key-operator-service-role) do Azure.

Atribua essa função à entidade de serviço do Key Vault, limitando o escopo à sua conta de armazenamento, por meio do cmdlet [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) do Azure PowerShell.

```azurepowershell-interactive
# Assign Azure role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role."
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Após a atribuição de função bem-sucedida, você verá uma saída semelhante ao exemplo a seguir:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Se o Key Vault já foi adicionado à função na sua conta de armazenamento, você receberá a mensagem *"A atribuição de função já existe".* erro. Você também pode verificar a atribuição de função usando a página de "Controle de acesso (IAM)" da conta de armazenamento no portal do Azure.

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Dê a sua permissão de conta de usuário para as contas de armazenamento gerenciadas

Use o cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) do Azure PowerShell para atualizar a política de acesso ao Key Vault e conceder permissões da conta de armazenamento à sua conta de usuário.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Observe que as permissões para contas de armazenamento não estão disponíveis na página de "Políticas de acesso" da conta de armazenamento no portal do Azure.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Adicione uma conta de armazenamento gerenciada para a sua instância do Key Vault

Use o cmdlet [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) do Azure PowerShell para criar uma conta de armazenamento gerenciada na instância do Key Vault. O switch `-DisableAutoRegenerateKey` especifica NÃO regenerar as chaves da conta de armazenamento.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Após a adição bem sucedida da conta de armazenamento sem regeneração de chave, você verá saída semelhante para o exemplo a seguir:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                :
```

### <a name="enable-key-regeneration"></a>Habilitar regeneração de chave

Caso deseje que o Key Vault regenere as chaves de conta de armazenamento periodicamente, use o cmdlet [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) do Azure PowerShell para definir um período de regeneração. Neste exemplo, definiremos um período de regeneração de três dias. Quando chegar o período de girar a chave, o Key Vault vai regenerar a chave que não está ativa e definir a chave recém-criada como ativa. Somente uma das chaves é usada para emitir tokens SAS em um dado momento. Essa é a chave ativa.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Após a adição bem-sucedida da conta de armazenamento com a regeneração de chave, você verá uma saída semelhante ao exemplo a seguir:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                :
```

## <a name="shared-access-signature-tokens"></a>Tokens de Assinatura de Acesso Compartilhado

Solicite também ao Key Vault que gere tokens de Assinatura de Acesso Compartilhado. Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento. Você pode permitir acesso aos clientes aos recursos da sua conta de armazenamento sem compartilhar as chaves de conta. Uma Assinatura de Acesso Compartilhado oferece uma forma segura de compartilhar seus recursos de armazenamento sem comprometer suas chaves de conta.

Os comandos desta seção executam as seguintes ações:

- Criam a definição de Assinatura de Acesso Compartilhado de uma conta.
- Criam um token de Assinatura de Acesso Compartilhado da conta para serviços Blob, Arquivo, Tabela e Fila. O token é criado para os tipos de recursos Serviço, Contêiner e Objeto. O token é criado com todas as permissões via HTTPS e com as datas de início e término especificadas.
- Criam uma definição de Assinatura de Acesso Compartilhado de armazenamento gerenciado do Key Vault no cofre. A definição tem o URI do modelo do token de Assinatura de Acesso Compartilhado que foi criada. A definição tem o tipo de Assinatura de Acesso Compartilhado `account` e é válida por N dias.
- Confirmam se a Assinatura de Acesso Compartilhado foi salva no seu cofre de chaves como um segredo.
-
### <a name="set-variables"></a>Definir variáveis

Primeiro, defina as variáveis a serem usadas pelos cmdlets do PowerShell nas etapas a seguir. Lembre-se de atualizar os espaços reservados <YourStorageAccountName> e <YourKeyVaultName>.

Também usaremos os cmdlets [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) do Azure PowerShell para obter o contexto da sua conta de armazenamento do Azure.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>Criar um token de Assinatura de Acesso Compartilhado

Crie uma definição de Assinatura de Acesso Compartilhado usando os cmdlets [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken) do Azure PowerShell.

```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
O valor de $sasToken será semelhante a este.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Gerar uma definição de Assinatura de Acesso Compartilhado

Use o cmdlet [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition) do Azure PowerShell para criar uma definição de Assinatura de Acesso Compartilhado.  Você pode fornecer o nome de sua escolha para o parâmetro `-Name`.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Confirme a definição de Assinatura de Acesso Compartilhado

Confirme se a definição de Assinatura de Acesso Compartilhado foi armazenada no cofre de chaves usando o cmdlet [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) do Azure PowerShell.

Primeiro, encontre a definição de Assinatura de Acesso Compartilhado no cofre de chaves.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

O segredo correspondente à definição de SAS terá estas propriedades:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Agora você pode usar o cmdlet [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) com os parâmetros `VaultName` e `Name` para ver o conteúdo desse segredo.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

A saída desse comando mostrará a cadeia de caracteres de definição de SAS.

## <a name="next-steps"></a>Próximas etapas

- [Amostras da conta de armazenamento gerenciada](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Referência do PowerShell do Key Vault](/powershell/module/az.keyvault/#key_vault)
