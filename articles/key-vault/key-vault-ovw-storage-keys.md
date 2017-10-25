---
ms.assetid: 
title: Chaves de conta de Armazenamento do Azure Key Vault
description: "Chaves da conta de armazenamento fornecem uma integração contínua entre o Azure Key Vault e o acesso baseado em chave para a Conta de Armazenamento do Azure."
ms.topic: article
services: key-vault
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 09/14/2017
ms.openlocfilehash: 83bcb339c16b8a1be15773ba35208461ecf8120e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-storage-account-keys"></a>Chaves de conta de Armazenamento do Azure Key Vault

Antes das Chaves de Conta de Armazenamento do Azure Key Vault, os desenvolvedores precisavam gerenciar suas próprias chaves de ASA (Conta de Armazenamento do Azure) e girá-las manualmente ou por meio de um automação externa. Agora, as Chave de Conta de Armazenamento do Key Vault são implementadas como [Segredos do Key Vault](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) para autenticação com uma conta de Armazenamento do Azure. 

O recurso de chave ASA (Conta de Armazenamento do Azure) gerencia rotação do segredo para você. Elimina a necessidade de contato direto com uma chave ASA ao oferecer SAS (Assinaturas de Acesso Compartilhado) como um método. 

Para obter mais informações gerais sobre Contas de Armazenamento do Azure, consulte [Sobre contas de armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Interfaces de suporte

O recurso de chaves da Conta de Armazenamento do Azure está inicialmente disponível por meio das interfaces REST, .NET/C# e PowerShell. Para obter mais informações, consulte [Referência do Key Vault](https://docs.microsoft.com/azure/key-vault/).


## <a name="what-key-vault-manages"></a>O que o Key Vault gerencia

O Key Vault executa várias funções de gerenciamento interno em seu nome quando você usa Chaves Gerenciadas de Conta de Armazenamento.

- O Azure Key Vault gerencia chaves de uma ASA (Conta de Armazenamento do Azure).
    - Internamente, o Azure Key Vault pode listar (sincronizar) chaves com uma Conta de Armazenamento do Azure.  
    - O Azure Key Vault gera novamente (gira) as chaves periodicamente. 
    - Os valores de chave nunca são retornados em resposta ao chamador. 
    - O Azure Key Vault gerencia chaves tanto de Contas de Armazenamento quanto de Contas de Armazenamento Clássicas. 
- O Azure Key Vault permite que você, o proprietário do cofre/objeto, crie definições de SAS (SAS de conta ou serviço).
    - O valor SAS, criado usando a definição de SAS, é retornado como um segredo por meio do caminho do URI REST. Para obter mais informações, consulte [Operações de conta de armazenamento do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations).

## <a name="naming-guidance"></a>Diretrizes de nomenclatura

- Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas.  
- Um nome de definição de SAS deve ter de 1 a 102 caracteres, contendo somente 0–9, a–z, A–Z.

## <a name="developer-experience"></a>Experiência do desenvolvedor

### <a name="before-azure-key-vault-storage-keys"></a>Antes das chaves de armazenamento do Azure Key Vault 

Os desenvolvedores costumavam precisar fazer o seguinte com uma chave de conta de armazenamento para obterem acesso ao armazenamento do Azure. 
 
```powershell
//create an Azure Storage Account using a connection string containing an account name and a storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>Depois das chaves de armazenamento do Azure Key Vault 

```powershell
//Make sure to set storage permissions appropriately on your key vault
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourVault' -ObjectId yourObjectId -PermissionsToStorage all

//Get secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  

-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get a SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If your SAS token is about to expire, Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Orientação para desenvolvedores

- Permita somente que o Key Vault gerencie suas chaves ASA. Não tentar gerenciá-las você mesmo, você interferirá nos processos do Key Vault. 
- Não permita que chaves ASA sejam gerenciadas por mais de um objeto do Key Vault. 
- Se precisar regenerar manualmente as chaves ASA, recomendamos que você as regenere por meio do Key Vault. 

## <a name="getting-started"></a>Introdução

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Configuração para permissões de RBAC (controle de acesso baseadas em função)

O Key Vault precisa de permissões para *listar* e *regenerar* chaves para uma conta de armazenamento. Configure essas permissões usando as seguintes etapas:

- Obter ObjectId do Key Vault: 

    `Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093`
    
     ou o
     
    `Get-AzureRmADServicePrincipal -SearchString "AzureKeyVault"`

- Atribua a função de Operador de Chave de Armazenamento à Identidade do Azure Key Vault: 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > Para um tipo de conta clássico, defina o parâmetro de função como *"Função de Serviço do Operador de Chave de Conta de Armazenamento Clássica"*

## <a name="working-example"></a>Exemplo de trabalho

O exemplo a seguir demonstra como criar uma Conta de Armazenamento do Azure gerenciada por Key Vault e as definições de SAS (Assinatura de Acesso Compartilhado).

### <a name="assumptions"></a>Suposições

As instruções a seguir são fatos conhecidos para este exemplo de trabalho.

- Seu recurso de armazenamento está localizado em: */subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1*

- O nome de seu cofre da chaves é: *yourtest1*

### <a name="get-a-service-principal"></a>Obter uma entidade de serviço

```powershell
Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093
```

A saída do comando anterior incluirá o ServicePrincipal, que chamaremos de *yourServicePrincipalId*. 

### <a name="set-permissions"></a>Definir permissões

Verifique se as permissões de armazenamento estão definidas como *todos*. Você pode obter yourUserPrincipalId e definir permissões no cofre usando os seguintes comandos.

```powershell
Get-AzureRmADUser -SearchString "your name"
```
Agora, pesquise o nome e receba o ObjectId relacionado, que será usado na definição de permissões no cofre.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourtest1' -ObjectId yourUserPrincipalId -PermissionsToStorage all
```

### <a name="allow-access"></a>Permitir o acesso

Você precisa conceder ao serviço Key Vault acesso às contas de armazenamento antes de poder criar uma conta de armazenamento gerenciado e definições de SAS.

```powershell
New-AzureRmRoleAssignment -ObjectId yourServicePrincipalId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '/subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1'
```

### <a name="create-storage-account"></a>Criar Conta de Armazenamento

Agora, crie uma Conta de Armazenamento Gerenciado e duas definições de SAS. A conta SAS fornece acesso ao serviço de blob com permissões diferentes.

```powershell
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="regeneration"></a>Regeneração

Defina o período de regeneração usando os comandos a seguir.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Configurar as definições de SAS

Configure as definições de SAS no Key Vault para sua conta de armazenamento gerenciado.

```powershell
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourtest1  -AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName yourtest1  -AccountName msak01 -Name blobsas2 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List,Write
```

### <a name="get-token"></a>Obter o token

Obtenha os tokens SAS correspondentes e faça chamadas para o armazenamento.

```powershell
$sasToken1 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas1).SecretValueText
$sasToken2 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas2).SecretValueText
```

### <a name="create-storage"></a>Criar armazenamento

Observe que a tentativa de acesso com *$sastoken1* falha, mas somos capazes de acessar com *$sastoken2*. 

```powershell
$context1 = New-AzureStorageContext -SasToken $sasToken1 -StorageAccountName yourtest1
$context2 = New-AzureStorageContext -SasToken $sasToken2 -StorageAccountName yourtest1
Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt"  -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt"  -Context $context2
```

### <a name="example-summary"></a>Resumo do exemplo

Você pode acessar o conteúdo do blob de armazenamento com o token SAS que tem acesso de gravação.

### <a name="relevant-powershell-cmdlets"></a>Cmdlets relevantes do Powershell

- [Get-AzureKeyVaultManagedStorageAccount ](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount?view=azurermps-4.3.1)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)

## <a name="storage-account-onboarding"></a>Integração da conta de armazenamento 

Exemplo: como um proprietário de objeto do Key Vault, você adiciona um objeto de conta de armazenamento ao seu Azure Key Vault para integrar uma conta de armazenamento.

Durante a integração, o Key Vault precisa verificar se a identidade da conta de integração tem permissões para *listar* e *regenerar* chaves de armazenamento. Para verificar essas permissões, o Key Vault obtém um token OBO (em nome de) do serviço de autenticação com público definido para o Azure Resource Manager e faz uma chamada de chave de *lista* para o serviço de armazenamento do Azure. Se a chamada de *lista* falhar, a criação do objeto Key Vault falhará com o código de status HTTP *Forbidden*. As chaves listadas dessa maneira são armazenadas em cache com o seu armazenamento de entidade do cofre de chaves. 

O Key Vault deve verificar se a identidade tem permissões para *regenerar* antes de poder assumir a propriedade da regeneração das suas chaves. Para verificar se a identidade, via token OBO, bem como a identidade de primeira parte do Key Vault têm essas permissões:

- O Key Vault lista permissões de RBAC no recurso de conta de armazenamento.
- O Key Vault valida a resposta por meio correspondência de expressão regular de ações e não ações. 

Encontre alguns exemplos de suporte em [Key Vault – Amostras de Chaves de Conta de Armazenamento Gerenciado](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167).

Se a identidade não tiver a permissão *regenerar* ou se a identidade da primeira parte do Key Vault não tiver a permissão *lista* ou *regenerar*, a solicitação de integração falhará, retornando um código de erro apropriado e uma mensagem. 

O token OBO só funcionará quando você usar os aplicativos cliente nativos de primeira parte do PowerShell ou da CLI.

## <a name="other-applications"></a>Outros aplicativos

- Tokens SAS, construídos usando chaves de conta de armazenamento do Key Vault, fornecem acesso ainda mais controlado a uma conta de armazenamento do Azure. Para obter mais informações, confira [Como usar assinaturas de acesso compartilhado](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Consulte também

- [Sobre chaves, segredos e certificados](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog da equipe do Key Vault](https://blogs.technet.microsoft.com/kv/)
