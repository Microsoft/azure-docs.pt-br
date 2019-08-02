---
title: Gerenciar chaves de conta de armazenamento com o Azure Key Vault e a CLI do Azure
description: Chaves da conta de armazenamento fornecem integração perfeita entre o Azure Key Vault e baseada em chave de acesso a uma conta de armazenamento do Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 6ac054bc9750e4297080c4ab64030c9c6a5fb55a
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312848"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Gerenciar chaves de conta de armazenamento com o Azure Key Vault e a CLI do Azure 

O Azure Key Vault gerencia chaves para contas de armazenamento do Azure e contas de armazenamento clássicas. Você pode usar o recurso de conta de armazenamento gerenciado do Key Vault para completar várias funções de gerenciamento de chaves para você.

Uma [conta de armazenamento do Azure](/azure/storage/storage-create-storage-account) usa uma credencial que consiste em um nome de conta e uma chave. A chave é gerada automaticamente e serve como uma senha, em vez de um como uma chave de criptografia. Key Vault gerencia chaves de conta de armazenamento, armazenando-os como [segredos do Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). As chaves são listadas (sincronizadas) com uma conta de armazenamento do Azure e são regeneradas periodicamente ou _girado_. 

Quando você usa o recurso de chave de conta de armazenamento gerenciado, considere os seguintes pontos:

- Valores de chave nunca são retornados em resposta a um chamador.
- Somente o Cofre de chaves deve gerenciar as chaves da conta de armazenamento. Não gerenciar as chaves por conta própria e evitar interferência com processos de Cofre de chaves.
- Apenas um único objeto de Cofre de chaves deve gerenciar chaves de conta de armazenamento. Não permitir o gerenciamento de chaves de vários objetos.
- Você pode solicitar o Key Vault para gerenciar sua conta de armazenamento com uma entidade de usuário, mas não com uma entidade de serviço.
- Regenere chaves somente por meio de Cofre de chaves. Não regenere manualmente as chaves da conta de armazenamento. 

> [!NOTE]
> Integração de armazenamento do Azure com o Azure Active Directory (Azure AD) é baseados em nuvem acesso e identidade do serviço de gerenciamento. da Microsoft
> Integração do Azure AD está disponível para [blobs do Azure e filas](https://docs.microsoft.com/azure/storage/common/storage-auth-aad).
> Use o Azure AD para autenticação e autorização.
> Azure AD fornece acesso baseado em token do OAuth2 para o armazenamento do Azure assim como o Azure Key Vault.
>
> Azure AD permite que você autentique seu aplicativo cliente usando uma identidade de usuário ou aplicativo, em vez de credenciais de conta de armazenamento.
> Você pode usar um [identidade gerenciada do Azure AD](/azure/active-directory/managed-identities-azure-resources/) quando você executa no Azure. Identidades gerenciadas eliminam a necessidade de autenticação de cliente e armazenar credenciais no ou com seu aplicativo.
> O Azure AD usa o controle de acesso baseado em função (RBAC) para gerenciar a autorização, que também é compatível com o Cofre de chaves.

### <a name="service-principal-application-id"></a>ID da entidade de segurança de aplicativo de serviço

Um locatário do AD do Azure fornece a cada aplicativo registrado com um [entidade de serviço](/azure/active-directory/develop/developer-glossary#service-principal-object). A entidade de serviço serve como a identidade do aplicativo (ID). A ID do aplicativo é usada durante a instalação de autorização para acessar outros recursos do Azure por meio do RBAC.

Key Vault é um aplicativo da Microsoft previamente registrado em locatários tudo do Azure AD. O Cofre de chaves está registrado sob a mesma ID de aplicativo e dentro de cada nuvem do Azure.

| Locatários | Nuvem | ID do aplicativo |
| --- | --- | --- |
| AD do Azure | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| AD do Azure | Público do Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Outros  | Qualquer | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Pré-requisitos

Antes de usar o Cofre de chaves para gerenciar sua chave de conta de armazenamento, examine os pré-requisitos:

- Instale a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Criar uma [conta de armazenamento do Azure](https://azure.microsoft.com/services/storage/). Siga [essas etapas](https://docs.microsoft.com/azure/storage/).
- O nome da conta de armazenamento deve usar apenas letras minúsculas e números. O comprimento do nome deve estar entre 3 e 24 caracteres.        
      
## <a name="manage-storage-account-keys"></a>Gerenciar chaves de conta de armazenamento

Há quatro etapas básicas para usar o Key Vault para gerenciar chaves de conta de armazenamento:

1. Obtenha uma conta de armazenamento existente.
1. Busca um cofre de chaves existente.
1. Adicione uma conta de armazenamento do Cofre de chaves gerenciado no cofre. Definir `key1` como a chave ativa com um período de regeneração de 180 dias.
1. Use `key1` para definir um contexto de armazenamento para a conta de armazenamento especificada.

> [!NOTE]
> O Cofre de chaves como um serviço recebe permissões de operador em sua conta de armazenamento.
> 
> Depois de definir chaves de conta de armazenamento gerenciado do Azure Key Vault, apenas altere as chaves usando o Cofre de chaves.
> Para chaves de conta de armazenamento gerenciado, o Key Vault gerencia a rotação da chave de conta de armazenamento.

1. Depois de criar uma conta de armazenamento, execute o seguinte comando para obter a ID de recurso da conta de armazenamento para gerenciar:
    ```
    az storage account show -n storageaccountname
    ```

    Copie o valor de ID do recurso da saída do comando:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Saída de exemplo:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Atribua a função RBAC "Função de serviço do armazenamento conta chave operador" para o Cofre de chaves. Essa função limita o escopo de acesso à sua conta de armazenamento. Para uma conta de armazenamento clássico, use a função de "Clássico função conta de armazenamento chave operador Service".

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74` é a ID de objeto para o Key Vault na nuvem pública do Azure. Para obter a ID de objeto para o Cofre de chaves na nuvem do governo do Azure, consulte [ID da entidade de segurança de aplicativo de serviço](#service-principal-application-id).
    
1. Crie uma conta de armazenamento gerenciado do Key Vault:

    Defina um período de regeneração de 90 dias. Após 90 dias, o cofre da chave regenera `key1` e a chave ativa de troca `key2` para `key1`. `key1` em seguida, é marcado como a chave ativa. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Criar e gerar tokens

Você também pode pedir o Key Vault para gerar tokens de assinatura de acesso compartilhado. Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento. Você pode conceder clientes acesso aos recursos em sua conta de armazenamento sem compartilhar as chaves de conta. Uma assinatura de acesso compartilhado fornece uma maneira segura de compartilhar seus recursos de armazenamento sem comprometer as chaves da conta.

Os comandos nesta seção concluem as seguintes ações:

- Definir uma definição de assinatura de acesso de conta compartilhada `<YourSASDefinitionName>`. A definição está definida em uma conta de armazenamento gerenciado do Key Vault `<YourStorageAccountName>` no cofre de chaves `<VaultName>`.
- Crie um token de assinatura de acesso compartilhado de conta para serviços Blob, arquivo, tabela e fila. O token é criado para tipos de recursos de serviço, o contêiner e o objeto. O token é criado com todas as permissões, via https e com as datas de início e término especificadas.
- Defina uma definição de assinatura de acesso do Cofre de chaves gerenciado compartilhado de armazenamento no cofre. A definição tem o modelo de URI do token de assinatura de acesso compartilhado que foi criado. A definição tem o tipo de assinatura de acesso compartilhado `account` e é válido por N dias.
- Recupere o token de acesso real do segredo do Cofre de chave que corresponde à definição de assinatura de acesso compartilhado.

Depois de concluir as etapas na seção anterior, execute os seguintes comandos para fazer o Key Vault para gerar tokens de assinatura de acesso compartilhado. 

1. Crie uma definição de assinatura de acesso compartilhado. Depois que a definição de assinatura de acesso compartilhado é criada, peça ao Key Vault para gerar mais tokens de assinatura acesso compartilhado. Esta operação requer o `storage` e `setsas` permissões.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Para obter ajuda sobre a operação, consulte o [az storage gerar-sas de conta](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) documentação de referência.

    Depois que a operação é executada com êxito, copie a saída.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Use o `$sasToken` gerado pelo comando anterior e criar uma definição de assinatura de acesso compartilhado. Para obter mais informações sobre os parâmetros de comando, consulte o [criar definição de sas de armazenamento de Cofre de chaves de az](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) documentação de referência.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Quando o usuário não tem permissões para a conta de armazenamento, primeiro obtenha a ID de objeto do usuário:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Buscar tokens no código

Executar operações em sua conta de armazenamento buscando [compartilhado tokens de assinatura de acesso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do Key Vault.

Há três maneiras de se autenticar no Key Vault:

- Use uma identidade de serviço gerenciado. Essa abordagem é altamente recomendável.
- Use uma entidade de serviço e um certificado. 
- Use uma entidade de serviço e a senha. Essa abordagem não é recomendada.

Para obter mais informações, consulte [Azure Key Vault: Conceitos básicos](key-vault-whatis.md#basic-concepts).

O exemplo a seguir demonstra como buscar tokens de assinatura de acesso compartilhado. Você busca os tokens depois de criar uma definição de assinatura de acesso compartilhado. 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Se o token de assinatura de acesso compartilhado está prestes a expirar, a buscar o token de assinatura de acesso compartilhado do Key Vault e atualize o código.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Comandos da CLI do Azure

Para obter informações sobre os comandos da CLI do Azure que são relevantes para as contas de armazenamento gerenciado, consulte a [armazenamento de keyvault az](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) documentação de referência.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [chaves, segredos e certificados](https://docs.microsoft.com/rest/api/keyvault/).
- Examine os artigos sobre o [blog da equipe do Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
