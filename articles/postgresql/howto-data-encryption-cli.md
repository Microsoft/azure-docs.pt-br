---
title: Data Encryption-CLI do Azure-for Azure Database para PostgreSQL-servidor único
description: Saiba como configurar e gerenciar a criptografia de dados para o servidor único para PostgreSQL usando o CLI do Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 757782e8842fbcaca9c8d95ec8086dd5791a817b
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240606"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>Criptografia de dados para um servidor único para PostgreSQL para Azure usando o CLI do Azure

Saiba como usar o CLI do Azure para configurar e gerenciar a criptografia de dados para o servidor único para PostgreSQL do banco de dado do Azure.

## <a name="prerequisites-for-azure-cli"></a>Pré-requisitos para CLI do Azure

* É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.
* Crie um cofre de chaves e uma chave para usar para uma chave gerenciada pelo cliente. Habilite também a proteção de limpeza e a exclusão reversível no cofre de chaves.

   ```azurecli-interactive
   az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
   ```

* No Azure Key Vault criado, crie a chave que será usada para a criptografia de dados do servidor único para PostgreSQL para o Azure Database.

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

* A chave deve ter os seguintes atributos para usar como uma chave gerenciada pelo cliente:
  * Sem data de validade
  * Não desabilitado
  * Executar operações de **obtenção** , **encapsulamento** e **desencapsulamento**

## <a name="set-the-right-permissions-for-key-operations"></a>Definir as permissões corretas para operações de chave

1. Há duas maneiras de obter a identidade gerenciada para o banco de dados do Azure para PostgreSQL servidor único.

    ### <a name="create-an-new-azure-database-for-postgresql-server-with-a-managed-identity"></a>Crie um novo servidor de banco de dados do Azure para PostgreSQL com uma identidade gerenciada.

    ```azurecli-interactive
    az postgres server create --name <server_name> -g <resource_group> --location <location> --storage-size <size>  -u <user> -p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled> --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-postgresql-server-to-get-a-managed-identity"></a>Atualize um banco de dados do Azure para servidor PostgreSQL existente para obter uma identidade gerenciada.

    ```azurecli-interactive
    az postgres server update --resource-group <resource_group> --name <server_name> --assign-identity
    ```

2. Defina as **permissões de chave** ( **Get** , **Wrap** , sem **encapsulamento** ) para a **entidade de segurança** , que é o nome do servidor de servidor único PostgreSQL.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Definir criptografia de dados para um servidor único do banco de dado do Azure para PostgreSQL

1. Habilite a criptografia de dados para o servidor único do banco de dado do Azure para PostgreSQL usando a chave criada no Azure Key Vault.

    ```azurecli-interactive
    az postgres server key create --name <server_name> -g <resource_group> --kid <key_url>
    ```

    URL da chave:  `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Usando a criptografia de dados para servidores de restauração ou de réplica

Depois que o servidor único do Banco de Dados do Azure para PostgreSQL é criptografado com uma chave gerenciada pelo cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é criptografada. Você pode fazer essa nova cópia por meio de uma operação local ou de restauração geográfica, ou por meio de uma operação de réplica (local/entre regiões). Portanto, para um servidor de servidor único do PostgreSQL criptografado, você pode usar as etapas a seguir para criar um servidor criptografado.

### <a name="creating-a-restoredreplica-server"></a>Criando um servidor restaurado/de réplica

* [Criar um servidor de restauração](howto-restore-server-cli.md)
* [Criar um servidor de réplica de leitura](howto-read-replicas-cli.md)

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Depois que o servidor for restaurado, revalidar a criptografia de dados do servidor restaurado

*   Atribuir identidade para o servidor de réplica
```azurecli-interactive
az postgres server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   Obter a chave existente que deve ser usada para o servidor restaurado/de réplica

```azurecli-interactive
az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   Definir a política para a nova identidade para o servidor restaurado/de réplica

```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* Validar novamente o servidor restaurado/de réplica com a chave de criptografia

```azurecli-interactive
az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>Recurso adicional para a chave que está sendo usada para o banco de dados do Azure para PostgreSQL servidor único

### <a name="get-the-key-used"></a>Obter a chave usada

```azurecli-interactive
az postgres server key show --name <server name>  -g <resource_group> --kid <key url>
```

URL da chave: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>Listar a chave usada

```azurecli-interactive
az postgres server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>Descartar a chave que está sendo usada

```azurecli-interactive
az postgres server key delete -g <resource_group> --kid <key url> 
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Usando um modelo de Azure Resource Manager para habilitar a criptografia de dados

Além do portal do Azure, você também pode habilitar a criptografia de dados no banco de dado do Azure para PostgreSQL servidor único usando modelos de Azure Resource Manager para um servidor novo e existente.

### <a name="for-a-new-server"></a>Para um novo servidor

Use um dos modelos de Azure Resource Manager criados previamente para provisionar o servidor com a criptografia de dados habilitada: [exemplo com criptografia de dados](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Este modelo de Azure Resource Manager cria um servidor único do banco de dados do Azure para PostgreSQL e usa o **keyvault** e a **chave** passados como parâmetros para habilitar a criptografia de dados no servidor.

### <a name="for-an-existing-server"></a>Para um servidor existente
Além disso, você pode usar os modelos de Azure Resource Manager para habilitar a criptografia de dados em seus servidores únicos do banco de dado do Azure para PostgreSQL.

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
      "type": "Microsoft.DBforPostgreSQL/servers",
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
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
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
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
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
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
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

 Para saber mais sobre a criptografia de dados, consulte [banco de dados do Azure para PostgreSQL criptografia de dado de servidor único com chave gerenciada pelo cliente](concepts-data-encryption-postgresql.md).
