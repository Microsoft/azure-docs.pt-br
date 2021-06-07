---
title: Data Encryption-CLI do Azure-banco de dados do Azure para MySQL
description: Saiba como configurar e gerenciar a criptografia de dados para o banco de dado do Azure para MySQL usando o CLI do Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6d9abc67035b4581a028d8e59ef080b4f1ffa5b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96519035"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Criptografia de dados para o Azure Database para MySQL usando o CLI do Azure

Saiba como usar o CLI do Azure para configurar e gerenciar a criptografia de dados para o banco de dado do Azure para MySQL.

## <a name="prerequisites-for-azure-cli"></a>Pré-requisitos para CLI do Azure

* É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.
* Crie um cofre de chaves e uma chave para usar para uma chave gerenciada pelo cliente. Habilite também a proteção de limpeza e a exclusão reversível no cofre de chaves.

  ```azurecli-interactive
  az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
  ```

* No Azure Key Vault criado, crie a chave que será usada para a criptografia de dados do banco de dado do Azure para MySQL.

  ```azurecli-interactive
  az keyvault key create --name <key_name> -p software --vault-name <vault_name>
  ```

* Para usar um cofre de chaves existente, ele deve ter as seguintes propriedades para usar como uma chave gerenciada pelo cliente:

  * [Exclusão reversível](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Limpar protegido](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```
  * Dias de retenção definidos como 90 dias
  ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --retention-days 90
    ```

* A chave deve ter os seguintes atributos a serem usados como chave gerenciada pelo cliente:
  * Sem data de validade
  * Não desabilitado
  * Executar operações **Get**, **Wrap** e **Unwrap**
  * atributo recoverylevel definido como **recuperável** (isso requer a exclusão reversível habilitada com o período de retenção definido como 90 dias)
  * Proteção de limpeza habilitada

Você pode verificar os atributos acima da chave usando o seguinte comando:

```azurecli-interactive
az keyvault key show --vault-name <key_vault_name> -n <key_name>
```

## <a name="set-the-right-permissions-for-key-operations"></a>Definir as permissões corretas para operações de chave

1. Há duas maneiras de obter a identidade gerenciada para seu banco de dados do Azure para MySQL.

   ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Crie um novo servidor de banco de dados do Azure para MySQL com uma identidade gerenciada.

   ```azurecli-interactive
   az mysql server create --name -g <resource_group> --location <locations> --storage-size size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> -geo-redundant-backup <Enabled/Disabled>  --assign-identity
   ```

   ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Atualize um banco de dados do Azure para servidor MySQL existente para obter uma identidade gerenciada.

   ```azurecli-interactive
   az mysql server update --name  <server name>  -g <resource_group> --assign-identity
   ```

2. Defina as **permissões de chave** (**Get**, **Wrap**, sem **encapsulamento**) para a **entidade de segurança**, que é o nome do servidor MySQL.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Definir a criptografia de dados para o Azure Database para MySQL

1. Habilite a criptografia de dados para o Azure Database para MySQL usando a chave criada no Azure Key Vault.

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    URL da chave:  `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Usando a criptografia de dados para servidores de restauração ou de réplica

Depois que o Banco de Dados do Azure para MySQL é criptografado com uma chave gerenciada pelo cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é criptografada. Você pode fazer essa nova cópia por meio de uma operação local ou de restauração geográfica, ou por meio de uma operação de réplica (local/entre regiões). Portanto, para um servidor MySQL criptografado, você pode usar as etapas a seguir para criar um servidor restaurado criptografado.

### <a name="creating-a-restoredreplica-server"></a>Criando um servidor restaurado/de réplica

* [Criar um servidor de restauração](howto-restore-server-cli.md) 
* [Criar um servidor de réplica de leitura](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Depois que o servidor for restaurado, revalidar a criptografia de dados do servidor restaurado

*   Atribuir identidade para o servidor de réplica
```azurecli-interactive
az mysql server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   Obter a chave existente que deve ser usada para o servidor restaurado/de réplica

```azurecli-interactive
az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   Definir a política para a nova identidade para o servidor restaurado/de réplica
  
```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* Validar novamente o servidor restaurado/de réplica com a chave de criptografia

```azurecli-interactive
az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>Recurso adicional para a chave que está sendo usada para o banco de dados do Azure para MySQL

### <a name="get-the-key-used"></a>Obter a chave usada

```azurecli-interactive
az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
```

URL da chave: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>Listar a chave usada

```azurecli-interactive
az mysql server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>Descartar a chave que está sendo usada

```azurecli-interactive
az mysql server key delete -g <resource_group> --kid <key url>
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Usando um modelo de Azure Resource Manager para habilitar a criptografia de dados

Além da portal do Azure, você também pode habilitar a criptografia de dados no servidor do Azure para MySQL usando modelos de Azure Resource Manager para servidores novos e existentes.

### <a name="for-a-new-server"></a>Para um novo servidor

Use um dos modelos de Azure Resource Manager criados previamente para provisionar o servidor com a criptografia de dados habilitada: [exemplo com criptografia de dados](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

Este modelo de Azure Resource Manager cria um banco de dados do Azure para o servidor MySQL e usa o **keyvault** e a **chave** passados como parâmetros para habilitar a criptografia de dados no servidor.

### <a name="for-an-existing-server"></a>Para um servidor existente

Além disso, você pode usar os modelos de Azure Resource Manager para habilitar a criptografia de dados em seus servidores do Azure para MySQL existentes.

* Passe a ID de recurso da chave de Azure Key Vault que você copiou anteriormente na `Uri` propriedade no objeto de propriedades.

* Use *2020-01-01-Preview* como a versão da API.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>Próximas etapas

 Para saber mais sobre a criptografia de dados, consulte [banco de dados do Azure para MySQL Data Encryption com chave gerenciada pelo cliente](concepts-data-encryption-mysql.md).
