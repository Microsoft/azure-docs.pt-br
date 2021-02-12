---
title: Criptografar o registro com uma chave gerenciada pelo cliente
description: Saiba mais sobre a criptografia em repouso do registro de contêiner do Azure e como criptografar seu registro Premium com uma chave gerenciada pelo cliente armazenada no Azure Key Vault
ms.topic: article
ms.date: 12/03/2020
ms.custom: ''
ms.openlocfilehash: fb30610457e539250c33d7d9726fe10f9c0f8c5a
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062721"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Criptografar o Registro usando uma chave gerenciada pelo cliente

Quando você armazena imagens e outros artefatos em um Registro de Contêiner do Azure, o Azure criptografa automaticamente o conteúdo do Registro em repouso com as [chaves gerenciadas pelo serviço](../security/fundamentals/encryption-models.md). Você pode complementar a criptografia padrão com uma camada de criptografia adicional usando uma chave que você cria e gerencia no Azure Key Vault (uma chave gerenciada pelo cliente). Este artigo descreve as etapas necessárias usando a CLI do Azure e o portal do Azure.

Há suporte para a criptografia do lado do servidor com chaves gerenciadas pelo cliente por meio da integração com o [Azure Key Vault](../key-vault/general/overview.md): 

* Você pode criar chaves de criptografia próprias e armazená-las em um cofre de chaves ou usar as APIs do Azure Key Vault para gerar chaves. 
* Com o Azure Key Vault, você também pode auditar o uso das chaves.
* O registro de contêiner do Azure dá suporte à rotação automática de chaves de criptografia do registro quando uma nova versão de chave está disponível no Azure Key Vault. Você também pode girar manualmente as chaves de criptografia do registro.

Esse recurso está disponível na camada de serviço **Premium** do registro de contêiner. Para obter informações sobre os limites e as camadas de serviço do Registro, confira [Camadas de serviço do Registro de Contêiner do Azure](container-registry-skus.md).


## <a name="things-to-know"></a>Observações importantes

* Atualmente, você pode habilitar uma chave gerenciada pelo cliente somente quando cria um Registro. Ao habilitar a chave, você configura uma identidade gerenciada *atribuída pelo usuário* para acessar o cofre de chaves.
* Depois de habilitar a criptografia com uma chave gerenciada pelo cliente em um registro, você não pode desabilitar a criptografia.  
* O registro de contêiner do Azure dá suporte apenas a chaves RSA ou RSA-HSM. Atualmente, não há suporte para chaves de curva elíptica.
* Atualmente, não há suporte para a [confiança no conteúdo](container-registry-content-trust.md) em um Registro criptografado com uma chave gerenciada pelo cliente.
* Em um Registro criptografado com uma chave gerenciada pelo cliente, os logs de execução das [Tarefas do ACR](container-registry-tasks-overview.md) são atualmente mantidos por apenas 24 horas. Caso precise manter os logs por um período mais longo, confira as diretrizes para [Exportar e armazenar os logs de execução de tarefas](container-registry-tasks-logs.md#alternative-log-storage).


> [!NOTE]
> Se o acesso ao seu cofre de chaves do Azure for restrito usando uma rede virtual com um [firewall Key Vault](../key-vault/general/network-security.md), serão necessárias etapas de configuração adicionais. Depois de criar o registro e habilitar a chave gerenciada pelo cliente, configure o acesso à chave usando a identidade gerenciada *atribuída pelo sistema* do registro e configure o registro para ignorar o firewall do Key Vault. Siga as etapas neste artigo primeiro para habilitar a criptografia com uma chave gerenciada pelo cliente e, em seguida, consulte as diretrizes para [cenário avançado: Key Vault firewall](#advanced-scenario-key-vault-firewall) mais adiante neste artigo.

## <a name="automatic-or-manual-update-of-key-versions"></a>Atualização automática ou manual de versões de chave

Uma consideração importante para a segurança de um registro criptografado com uma chave gerenciada pelo cliente é a frequência com que você atualiza (gira) a chave de criptografia. Sua organização pode ter políticas de conformidade que exigem atualização regular de [versões](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) de chave armazenadas em Azure Key Vault quando usadas como chaves gerenciadas pelo cliente. 

Ao configurar a criptografia do registro com uma chave gerenciada pelo cliente, você tem duas opções para atualizar a versão de chave usada para criptografia:

* **Atualizar automaticamente a versão da chave** – para atualizar automaticamente uma chave gerenciada pelo cliente quando uma nova versão estiver disponível no Azure Key Vault, omita a versão da chave ao habilitar a criptografia do registro com uma chave gerenciada pelo cliente. Quando um registro é criptografado com uma chave sem controle de versão, o registro de contêiner do Azure verifica regularmente o Key Vault em busca de uma nova versão de chave e atualiza a chave gerenciada pelo cliente dentro de 1 hora. O registro de contêiner do Azure usa automaticamente a versão mais recente da chave.

* **Atualizar manualmente a versão da chave** -para usar uma versão específica de uma chave para a criptografia do registro, especifique essa versão de chave ao habilitar a criptografia do registro com uma chave gerenciada pelo cliente. Quando um registro é criptografado com uma versão de chave específica, o registro de contêiner do Azure usa essa versão para criptografia até que você gire manualmente a chave gerenciada pelo cliente.

Para obter detalhes, consulte [escolher a ID da chave com ou sem a versão da chave](#choose-key-id-with-or-without-key-version) e a [versão da chave de atualização](#update-key-version), mais adiante neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Para usar as etapas de CLI do Azure neste artigo, você precisa CLI do Azure versão 2.2.0 ou posterior ou Azure Cloud Shell. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Habilitar a chave gerenciada pelo cliente – CLI

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se necessário, execute o comando [az group create][az-group-create] para criar um grupo de recursos para criar o cofre de chaves, o registro de contêiner e outros recursos necessários.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

Crie uma [identidade gerenciada atribuída ao usuário para os recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) com o comando [az identity create][az-identity-create]. Essa identidade será usada pelo Registro para acessar o serviço Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

Na saída do comando, anote os seguintes valores: `id` e `principalId`. Você precisará deles nas etapas posteriores para configurar o acesso ao registro ao cofre de chaves.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Para sua conveniência, armazene esses valores em variáveis de ambiente:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Criar um cofre de chave

Crie um cofre de chaves com [az keyvault create][az-keyvault-create] para armazenar uma chave gerenciada pelo cliente para a criptografia do Registro.

Por padrão, a configuração de **exclusão reversível** é habilitada automaticamente em um novo cofre de chaves. Para evitar a perda de dados causada por exclusões de chave ou cofre de chaves acidentais, habilite também a configuração de **proteção de limpeza** :

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

Para uso em etapas posteriores, obtenha a ID de recurso do cofre de chaves:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Habilitar o acesso ao cofre de chaves

Configure uma política para o cofre de chaves, de modo que a identidade possa acessá-lo. No comando [az keyvault set-policy][az-keyvault-set-policy] a seguir, passe a ID da entidade de segurança da identidade gerenciada que você criou, armazenada anteriormente em uma variável de ambiente. Defina as permissões de chave como **get**, **unwrapKey** e **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

Como alternativa, use o [RBAC do Azure para Key Vault](../key-vault/general/rbac-guide.md) (versão prévia) para atribuir permissões à identidade para acessar o cofre de chaves. Por exemplo, atribua a função de criptografia do serviço de criptografia Key Vault à identidade usando o comando [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create) :

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption (preview)" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Criar uma chave e obter a ID da chave

Execute o comando [az keyvault key create][az-keyvault-key-create] para criar uma chave no cofre de chaves.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Na saída do comando, anote a ID da chave, `kid`. Você usará essa ID na próxima etapa:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/<version>",
    "kty": "RSA",
[...]
```

### <a name="choose-key-id-with-or-without-key-version"></a>Escolha a ID da chave com ou sem a versão da chave

Para sua conveniência, armazene o formato escolhido para a ID de chave na variável de ambiente $keyID. Você pode usar uma ID de chave com uma versão ou uma chave sem uma versão.

#### <a name="manual-key-rotation---key-id-with-version"></a>Rotação de chave manual-ID da chave com versão

Quando usado para criptografar um registro com uma chave gerenciada pelo cliente, essa chave permite apenas a rotação de chave manual no registro de contêiner do Azure.

Este exemplo armazena a propriedade da chave `kid` :

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>Rotação de chave automática-ID da chave que omite a versão 

Quando usado para criptografar um registro com uma chave gerenciada pelo cliente, essa chave habilita a rotação de chaves automática quando uma nova versão de chave é detectada no Azure Key Vault.

Este exemplo remove a versão da propriedade da chave `kid` :

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>Criar um Registro com uma chave gerenciada pelo cliente

Execute o comando [az acr create][az-acr-create] para criar um Registro na camada de serviço Premium e habilitar a chave gerenciada pelo cliente. Passe a ID de identidade gerenciada e a ID da chave, armazenadas anteriormente em variáveis de ambiente:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Mostrar o status de criptografia

Para mostrar se a criptografia do Registro com uma chave gerenciada pelo cliente está habilitada, execute o comando [az acr encryption show][az-acr-encryption-show]:

```azurecli
az acr encryption show --name <registry-name>
```

Dependendo da chave usada para criptografar o registro, a saída é semelhante a:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Habilitar a chave gerenciada pelo cliente – portal

### <a name="create-a-managed-identity"></a>Criar uma identidade gerenciada

Crie uma [identidade gerenciada atribuída ao usuário para os recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) no portal do Azure. Para obter as etapas, confira [Criar uma identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Você usará o nome da identidade nas etapas posteriores.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Criar identidade atribuída ao usuário no portal do Azure":::

### <a name="create-a-key-vault"></a>Criar um cofre de chave

Para obter as etapas para criar um cofre de chaves, consulte [início rápido: criar um cofre de chaves usando o portal do Azure](../key-vault/general/quick-create-portal.md).

Ao criar um cofre de chaves para uma chave gerenciada pelo cliente, na guia **noções básicas** , habilite a configuração **limpar proteção** . Essa configuração ajuda a evitar a perda de dados causada por exclusões de chave ou cofre de chaves acidentais.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Criar um cofre de chaves no portal do Azure":::

### <a name="enable-key-vault-access"></a>Habilitar o acesso ao cofre de chaves

Configure uma política para o cofre de chaves, de modo que a identidade possa acessá-lo.

1. Navegue até o cofre de chaves.
1. Selecione **Configurações** > **Políticas de acesso > +Adicionar Política de Acesso**.
1. Selecione **Permissões de chave** e **Obter**, **Desencapsular Chave** e **Encapsular Chave**.
1. Em **selecionar entidade de segurança**, selecione o nome do recurso da identidade gerenciada atribuída pelo usuário.  
1. Selecione **Adicionar** e **Salvar**.

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="Criar uma política de acesso ao cofre de chaves":::

Como alternativa, use o [RBAC do Azure para Key Vault](../key-vault/general/rbac-guide.md) (versão prévia) para atribuir permissões à identidade para acessar o cofre de chaves. Por exemplo, atribua a função de criptografia do serviço de criptografia Key Vault à identidade.

1. Navegue até o cofre de chaves.
1. Selecione **controle de acesso (iam)**  >  **+ Adicionar**  >  **Adicionar atribuição de função**.
1. Na janela **Adicionar atribuição de função** :
    1. Selecione **Key Vault função criptografia do serviço de criptografia (versão prévia)** . 
    1. Atribuir acesso à **identidade gerenciada atribuída pelo usuário**.
    1. Selecione o nome do recurso de sua identidade gerenciada atribuída pelo usuário e selecione **salvar**.

### <a name="create-key-optional"></a>Criar chave (opcional)

Opcionalmente, crie uma chave no cofre de chaves para ser usada para criptografar o registro. Siga estas etapas se desejar selecionar uma versão de chave específica como uma chave gerenciada pelo cliente. 

1. Navegue até o cofre de chaves.
1. Selecione **Configurações** > **Chaves**.
1. Selecione **+Gerar/Importar** e insira um nome exclusivo para a chave.
1. Aceite os valores padrão restantes e selecione **Criar**.
1. Após a criação, selecione a chave e, em seguida, selecione a versão atual. Copie o **identificador de chave** para a versão de chave.

### <a name="create-azure-container-registry"></a>Crie um registro de contêiner do Azure

1. Selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner**.
1. Na guia **Informações Básicas**, selecione ou crie um grupo de recursos e insira o nome do Registro. Em **SKU**, selecione **Premium**.
1. Na guia **Criptografia**, em **Chave gerenciada pelo cliente**, selecione **Habilitada**.
1. Em **Identidade**, escolha a identidade gerenciada que você criou.
1. Em **criptografia**, escolha uma das seguintes opções:
    * Selecione **selecionar em Key Vault** e selecione um cofre de chaves e chave existentes ou **crie um novo**. A chave selecionada não tem controle de versão e habilita a rotação de chaves automática.
    * Selecione **inserir URI de chave** e forneça um identificador de chave diretamente. Você pode fornecer um URI de chave com versão (para uma chave que deve ser girada manualmente) ou um URI de chave sem versão (que habilita a rotação de chaves automática). 
1. Na guia **Criptografia**, escolha **Examinar + criar**.
1. Escolha **Criar** para implantar a instância do Registro.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Criar um registro criptografado no portal do Azure":::

Para ver o status de criptografia do Registro no portal, navegue até o Registro. Em **Configurações**, selecione **Criptografia**.

## <a name="enable-customer-managed-key---template"></a>Habilitar a chave gerenciada pelo cliente – modelo

Use também um modelo do Resource Manager para criar um Registro e habilitar a criptografia com uma chave gerenciada pelo cliente.

O modelo a seguir cria um registro de contêiner e uma identidade gerenciada atribuída ao usuário. Copie o conteúdo a seguir em um novo arquivo e salve-o usando um nome de arquivo como `CMKtemplate.json`.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}
```

Siga as etapas das seções anteriores para criar os seguintes recursos:

* Cofre de chaves, identificado pelo nome
* Chave do cofre de chaves, identificada pela ID da chave

Execute o comando [AZ Deployment Group Create][az-deployment-group-create] a seguir para criar o registro usando o arquivo de modelo anterior. Quando indicado, forneça um novo nome de Registro e um nome de identidade gerenciada, bem como o nome do cofre de chaves e a ID da chave que você criou.

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Mostrar o status de criptografia

Para mostrar o status da criptografia do Registro, execute o comando [az acr encryption show][az-acr-encryption-show]:

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>Usar o Registro

Depois de habilitar uma chave gerenciada pelo cliente em um Registro, você pode executar as mesmas operações de Registro que executa em um Registro não criptografado com uma chave gerenciada pelo cliente. Por exemplo, você pode fazer a autenticação no Registro e efetuar push de imagens do Docker. Confira exemplos de comandos em [Efetuar push e pull de uma imagem](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Girar a chave

Atualize a versão de chave no Azure Key Vault ou crie uma nova chave e, em seguida, atualize o registro para criptografar dados usando a chave. Execute essas etapas usando a CLI do Azure ou no portal.

Ao girar uma chave, normalmente, você especifica a mesma identidade usada ao criar o Registro. Opcionalmente, configure uma nova identidade atribuída ao usuário para o acesso à chave ou habilite e especifique a identidade atribuída ao sistema do registro.

> [!NOTE]
> Verifique se o [acesso ao cofre de chaves](#enable-key-vault-access) necessário está definido para a identidade que você configurou para o acesso à chave.

### <a name="update-key-version"></a>Atualizar versão da chave

Um cenário comum é atualizar a versão da chave usada como uma chave gerenciada pelo cliente. Dependendo de como a criptografia do registro é configurada, a chave gerenciada pelo cliente no registro de contêiner do Azure é atualizada automaticamente ou deve ser atualizada manualmente.

### <a name="azure-cli"></a>CLI do Azure

Use os comandos [az keyvault key][az-keyvault-key] para criar ou gerenciar suas chaves do cofre de chaves. Para criar uma nova versão de chave, execute o comando [AZ keyvault Key Create][az-keyvault-key-create] :

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

A próxima etapa depende de como a criptografia do registro está configurada:

* Se o registro estiver configurado para detectar atualizações de versão de chave, a chave gerenciada pelo cliente será atualizada automaticamente dentro de 1 hora.

* Se o registro estiver configurado para exigir atualização manual para uma nova versão de chave, execute o comando [AZ ACR Encryption Rotate-Key][az-acr-encryption-rotate-key] , passando a nova ID de chave e a identidade que você deseja configurar:

Para atualizar a versão da chave gerenciada pelo cliente manualmente:

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

> [!TIP]
> Ao executar o `az acr encryption rotate-key` , você pode passar uma ID de chave com versão ou uma ID de chave sem controle de versão. Se você usar uma ID de chave sem controle de versão, o registro será configurado para detectar automaticamente as atualizações da versão da chave mais tarde.

### <a name="portal"></a>Portal

Use as configurações de **criptografia** do registro para atualizar o cofre de chaves, a chave ou as configurações de identidade usadas para a chave gerenciada pelo cliente.

Por exemplo, para configurar uma nova chave:

1. No portal, navegue até o Registro.
1. Em **Configurações**, selecione **Criptografia** > **Alterar chave**.

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Girar a chave no portal do Azure":::
1. Em **criptografia**, escolha uma das seguintes opções:
    * Selecione **selecionar em Key Vault** e selecione um cofre de chaves e chave existentes ou **crie um novo**. A chave selecionada não tem controle de versão e habilita a rotação de chaves automática.
    * Selecione **inserir URI de chave** e forneça um identificador de chave diretamente. Você pode fornecer um URI de chave com versão (para uma chave que deve ser girada manualmente) ou um URI de chave sem versão (que habilita a rotação de chaves automática).
1. Conclua a seleção de chave e escolha **Salvar**.

## <a name="revoke-key"></a>Revogar chave

Revogue a chave de criptografia gerenciada pelo cliente alterando a política de acesso ou as permissões no cofre de chaves ou excluindo a chave. Por exemplo, use o comando [az keyvault delete-policy][az-keyvault-delete-policy] para alterar a política de acesso da identidade gerenciada usada pelo Registro:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

A revogação da chave efetivamente bloqueia o acesso a todos os dados do Registro, já que o Registro não pode acessar a chave de criptografia. Se o acesso à chave estiver habilitado ou a chave excluída for restaurada, o registro selecionará a chave para que você possa acessar novamente os dados de registro criptografados.

## <a name="advanced-scenario-key-vault-firewall"></a>Cenário avançado: Key Vault firewall

Se o cofre de chaves do Azure for implantado em uma rede virtual com um firewall Key Vault, execute as etapas adicionais a seguir depois de habilitar a criptografia de chave gerenciada pelo cliente no registro.

1. Configurar a criptografia do registro para usar a identidade atribuída pelo sistema do registro
1. Habilitar o registro para ignorar o firewall Key Vault
1. Girar a chave gerenciada pelo cliente

### <a name="configure-system-assigned-identity"></a>Configurar identidade atribuída pelo sistema

Configure a identidade gerenciada atribuída ao sistema de um Registro para acessar o cofre de chaves para as chaves de criptografia. Se você não estiver familiarizado com as diferentes identidades gerenciadas para recursos do Azure, confira a [visão geral](../active-directory/managed-identities-azure-resources/overview.md).

Para habilitar a identidade atribuída ao sistema do Registro no portal:

1. No portal, navegue até o Registro.
1. Selecione **Configurações** >  **Identidade**.
1. Em **Atribuído ao sistema**, defina **Status** como **Ativado**. Clique em **Salvar**.
1. Copie a **ID de Objeto** da identidade.

Para conceder à identidade o acesso ao cofre de chaves:

1. Navegue até o cofre de chaves.
1. Selecione **Configurações** > **Políticas de acesso > +Adicionar Política de Acesso**.
1. Selecione **Permissões de chave** e **Obter**, **Desencapsular Chave** e **Encapsular Chave**.
1. Escolha **selecionar entidade de segurança** e procure a ID de objeto da identidade gerenciada atribuída pelo sistema ou o nome do registro.  
1. Selecione **Adicionar** e **Salvar**.

Para atualizar as configurações de criptografia do Registro para usar a identidade:

1. No portal, navegue até o Registro.
1. Em **Configurações**, selecione **Criptografia** > **Alterar chave**.
1. Em **Identidade**, selecione **Atribuído ao sistema** e **Salvar**.

### <a name="enable-key-vault-bypass"></a>Habilitar bypass do Key Vault

Para acessar um cofre de chaves configurado com um firewall Key Vault, o registro deve ignorar o firewall. Verifique se o cofre de chaves está configurado para permitir o acesso por qualquer [serviço confiável](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). O registro de contêiner do Azure é um dos serviços confiáveis.

1. No portal, navegue até o cofre de chaves.
1. Selecione **configurações**  >  **rede**.
1. Confirme, atualize ou adicione configurações de rede virtual. Para obter etapas detalhadas, confira [Configurar as redes virtuais e os firewalls do Azure Key Vault](../key-vault/general/network-security.md).
1. Em **permitir que os serviços confiáveis da Microsoft ignorem esse firewall**, selecione **Sim**. 

### <a name="rotate-the-customer-managed-key"></a>Girar a chave gerenciada pelo cliente

Depois de concluir as etapas anteriores, gire a chave para uma nova chave no cofre de chaves por trás de um firewall. Para obter as etapas, confira a [tecla girar](#rotate-key) neste artigo.

## <a name="troubleshoot"></a>Solucionar problemas

### <a name="removing-managed-identity"></a>Removendo identidade gerenciada


Se você tentar remover uma identidade gerenciada atribuída pelo usuário ou pelo sistema de um registro que é usado para configurar a criptografia, você poderá ver uma mensagem de erro semelhante a:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
Você também não poderá alterar (girar) a chave de criptografia. As etapas de resolução dependem do tipo de identidade usado para criptografia.

**Identidade atribuída ao usuário**

Se esse problema ocorrer com uma identidade atribuída pelo usuário, primeiro reatribua a identidade usando o GUID exibido na mensagem de erro. Por exemplo:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
Em seguida, depois de alterar a chave e atribuir uma identidade diferente, você pode remover a identidade original atribuída pelo usuário.

**Identidade atribuída ao sistema**

Se esse problema ocorrer com uma identidade atribuída pelo sistema, [crie um tíquete de suporte do Azure](https://azure.microsoft.com/support/create-ticket/) para obter assistência para restaurar a identidade.


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [a criptografia em repouso no Azure](../security/fundamentals/encryption-atrest.md).
* Saiba mais sobre as políticas de acesso e como [proteger o acesso a um cofre de chaves](../key-vault/general/secure-your-key-vault.md).


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
