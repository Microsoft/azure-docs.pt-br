---
title: Gerenciar chaves de conta de armazenamento com o Azure Key Vault e a CLI do Azure
description: As chaves de conta de armazenamento fornecem uma integração perfeita entre o Azure Key Vault e o acesso baseado em chave a uma conta de armazenamento do Azure.
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 573e4c9d8db3f07f223826ab648f2ef57e1d9c58
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766309"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Gerenciar chaves de conta de armazenamento com o Key Vault e a CLI do Azure
> [!IMPORTANT]
> Recomendamos usar a integração do Armazenamento do Azure ao Azure AD (Azure Active Directory), o serviço de gerenciamento de identidades e acesso baseado em nuvem da Microsoft. A integração do Azure AD está disponível para [blobs e filas do Azure](../../storage/common/storage-auth-aad.md) e fornece acesso baseado em token OAuth2 ao Armazenamento do Azure (assim como o Azure Key Vault). O Azure AD permite que você autentique seu aplicativo cliente usando uma identidade de aplicativo ou de usuário, em vez das credenciais da conta de armazenamento. Você pode usar uma [identidade gerenciada do Azure AD](../../active-directory/managed-identities-azure-resources/index.yml) durante a execução no Azure. As identidades gerenciadas eliminam a necessidade de autenticação de cliente e do armazenamento de credenciais no ou com o seu aplicativo. Use a solução abaixo apenas quando a autenticação do Azure AD não for possível.

Uma conta de armazenamento do Azure usa credenciais compostas por um nome de conta e uma chave. A chave é gerada automaticamente e serve como uma senha, em vez de como uma chave de criptografia. O Key Vault gerencia as chaves de conta de armazenamento regenerando-as periodicamente na conta de armazenamento e fornece tokens de Assinatura de Acesso Compartilhado para acesso delegado aos recursos na sua conta de armazenamento.

Use o recurso de chave de conta de armazenamento gerenciada do Key Vault para listar (sincronizar) as chaves com uma conta de armazenamento do Azure e regenerar (girar) as chaves periodicamente. Gerencie as chaves de contas de armazenamento e contas de armazenamento Clássicas.

Ao usar o recurso de chave de conta de armazenamento gerenciada, considere os seguintes pontos:

- Os valores de chaves nunca são retornados em resposta a um chamador.
- Somente o Key Vault deve gerenciar suas chaves de conta de armazenamento. Não gerencie as chaves por conta própria e evite interferir nos processos do Key Vault.
- Um só objeto do Key Vault deve gerenciar as chaves de conta de armazenamento. Não permita o gerenciamento de chaves em vários objetos.
- Regenere as chaves usando apenas o Key Vault. Não regenere manualmente as chaves da conta de armazenamento.

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

- [Instale a CLI do Azure](/cli/azure/install-azure-cli).
- [Criar um cofre de chave](quick-create-cli.md)
- [Criar uma conta de Armazenamento do Azure](../../storage/common/storage-account-create.md?tabs=azure-cli). O nome da conta de armazenamento precisa conter apenas letras minúsculas e números. O nome precisa ter entre 3 e 24 caracteres.
      
## <a name="manage-storage-account-keys"></a>Gerenciar chaves de conta de armazenamento

### <a name="connect-to-your-azure-account"></a>Conectar-se à sua conta do Azure

Autentique sua sessão da CLI do Azure usando os comandos [az login](/powershell/module/az.accounts/connect-azaccount).

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Permitir acesso ao Key Vault na sua conta de armazenamento

Use o comando [az role assignment create](/cli/azure/role/assignment) da CLI do Azure para permitir o acesso do Key Vault à sua conta de armazenamento. Forneça ao comando os seguintes valores de parâmetros:

- `--role`: transmita a função do Azure "Função do Serviço de Operador da Chave de Conta de Armazenamento". Essa função limita o escopo de acesso à sua conta de armazenamento. Para uma conta de armazenamento clássica, transmita a "Função do Serviço de Operador da Chave de Conta de Armazenamento Clássica".
- `--assignee`: transmita o valor "https://vault.azure.net", que é a URL do Key Vault na nuvem pública do Azure (para a nuvem do Azure Government, use '--asingee-object-id'; confira [ID do aplicativo da entidade de serviço](#service-principal-application-id)).
- `--scope`: transmita a ID do recurso da conta de armazenamento, que está no formato `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Para encontrar sua ID de assinatura, use o comando [az account list](/cli/azure/account?#az_account_list) da CLI do Azure; para encontrar o nome da conta de armazenamento e o grupo de recursos da conta de armazenamento, use o comando [az storage account list](/cli/azure/storage/account?#az_storage_account_list) da CLI do Azure.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Dê a sua permissão de conta de usuário para as contas de armazenamento gerenciadas

Use o cmdlet [az keyvault-set-policy](/cli/azure/keyvault?#az_keyvault_set_policy) da CLI do Azure para atualizar a política de acesso ao Key Vault e conceder permissões da conta de armazenamento à sua conta de usuário.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Observe que as permissões para contas de armazenamento não estão disponíveis na página de "Políticas de acesso" da conta de armazenamento no portal do Azure.
### <a name="create-a-key-vault-managed-storage-account"></a>Criar uma conta de armazenamento gerenciada no Key Vault

 Crie uma conta de armazenamento gerenciada do Key Vault usando o comando [az keyvault storage](/cli/azure/keyvault/storage?#az_keyvault_storage_add) da CLI do Azure. Defina um período de regeneração de 90 dias. Quando chegar o período de girar a chave, o Key Vault vai regenerar a chave que não está ativa e definir a chave recém-criada como ativa. Somente uma das chaves é usada para emitir tokens SAS em um dado momento; essa é a chave ativa. Forneça ao comando os seguintes valores de parâmetros:

- `--vault-name`: transmita o nome do cofre de chaves. Para encontrar o nome do cofre de chaves, use o comando [az keyvault list](/cli/azure/keyvault?#az_keyvault_list) da CLI do Azure.
- `-n`: transmita o nome da conta de armazenamento. Para encontrar o nome da sua conta de armazenamento, use o comando [az storage account list](/cli/azure/storage/account?#az_storage_account_list) da CLI do Azure.
- `--resource-id`: transmita a ID do recurso da conta de armazenamento, que está no formato `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Para encontrar sua ID de assinatura, use o comando [az account list](/cli/azure/account?#az_account_list) da CLI do Azure; para encontrar o nome da conta de armazenamento e o grupo de recursos da conta de armazenamento, use o comando [az storage account list](/cli/azure/storage/account?#az_storage_account_list) da CLI do Azure.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Tokens de Assinatura de Acesso Compartilhado

Solicite também ao Key Vault que gere tokens de Assinatura de Acesso Compartilhado. Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento. Você pode permitir acesso aos clientes aos recursos da sua conta de armazenamento sem compartilhar as chaves de conta. Uma Assinatura de Acesso Compartilhado oferece uma forma segura de compartilhar seus recursos de armazenamento sem comprometer suas chaves de conta.

Os comandos desta seção executam as seguintes ações:

- Criam a definição `<YourSASDefinitionName>` de Assinatura de Acesso Compartilhado de uma conta. A definição é feita em uma conta de armazenamento gerenciada `<YourStorageAccountName>` do Key Vault no do cofre de chaves `<YourKeyVaultName>`.
- Criam um token de Assinatura de Acesso Compartilhado da conta para serviços Blob, Arquivo, Tabela e Fila. O token é criado para os tipos de recursos Serviço, Contêiner e Objeto. O token é criado com todas as permissões via HTTPS e com as datas de início e término especificadas.
- Criam uma definição de Assinatura de Acesso Compartilhado de armazenamento gerenciado do Key Vault no cofre. A definição tem o URI do modelo do token de Assinatura de Acesso Compartilhado que foi criada. A definição tem o tipo de Assinatura de Acesso Compartilhado `account` e é válida por N dias.
- Confirmam se a Assinatura de Acesso Compartilhado foi salva no seu cofre de chaves como um segredo.

### <a name="create-a-shared-access-signature-token"></a>Criar um token de Assinatura de Acesso Compartilhado

Crie uma definição de Assinatura de Acesso Compartilhado usando o comando [az storage account generate-sas](/cli/azure/storage/account?#az_storage_account_generate_sas) da CLI do Azure. Essa operação exige as permissões `storage` e `setsas`.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Depois que a operação for executada com êxito, copie a saída.

```console
"se=2020-01-01&sp=***"
```

Essa saída será transmitida para o parâmetro `--template-uri` na próxima etapa.

### <a name="generate-a-shared-access-signature-definition"></a>Gerar uma definição de Assinatura de Acesso Compartilhado

Use o comando [az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_create) da CLI do Azure transmitindo a saída da etapa anterior para o parâmetro `--template-uri`, a fim de criar uma definição de Assinatura de Acesso Compartilhado.  Você pode fornecer o nome de sua escolha para o parâmetro `-n`.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Confirme a definição de Assinatura de Acesso Compartilhado

Confirme se a definição de Assinatura de Acesso Compartilhado foi armazenada no cofre de chaves usando o comando [az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) da CLI do Azure.

Agora você pode usar o comando [az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) e a propriedade `id` para exibir o conteúdo desse segredo.

```azurecli-interactive
az keyvault storage sas-definition show --id https://<YourKeyVaultName>.vault.azure.net/storage/<YourStorageAccountName>/sas/<YourSASDefinitionName>
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [chaves, segredos e certificados](/rest/api/keyvault/).
- Examine os artigos no [blog da equipe do Azure Key Vault](/archive/blogs/kv/).
- Confira a documentação de referência do comando [az keyvault storage](/cli/azure/keyvault/storage).
