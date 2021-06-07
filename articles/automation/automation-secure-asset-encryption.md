---
title: Criptografia de ativos seguros na Automação do Azure
description: Este artigo fornece conceitos para configurar a conta de automação para usar criptografia.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: b46cf3a742158a3347b43a1e9bc6d62d0b2160d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773684"
---
# <a name="encryption-of-secure-assets-in-azure-automation"></a>Criptografia de ativos seguros na Automação do Azure

Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são protegidos na Automação do Azure usando vários níveis de criptografia. Com base na chave de nível superior usada para a criptografia, há dois modelos de criptografia:

- Os que usam chaves gerenciadas pela Microsoft
- Os que usam chaves que você gerencia

## <a name="microsoft-managed-keys"></a>Chaves gerenciadas pela Microsoft

Por padrão, sua conta de automação do Azure usa chaves gerenciadas pela Microsoft.

Cada ativo protegido é criptografado e armazenado na Automação do Azure usando uma chave exclusiva (chave de Criptografia de Dados) gerada para cada conta de automação. Essas chaves são criptografadas e armazenadas na Automação do Azure usando ainda outra chave exclusiva gerada para cada conta chamada de AEK (Chave de Criptografia de Conta). Essas chaves de criptografia de conta são criptografadas e armazenadas na Automação do Azure usando chaves gerenciadas pela Microsoft. 

## <a name="keys-that-you-manage-with-key-vault-preview"></a>Chaves que você gerencia com o Key Vault (versão prévia)

Você pode gerenciar a criptografia de ativos seguros para sua conta de automação com suas chaves. Quando você especifica uma chave gerenciada pelo cliente no nível da conta da automação, essa chave é usada para proteger e controlar o acesso à chave de criptografia da conta para a conta de automação. Ela, por sua vez, é usada para criptografar e descriptografar todos os ativos seguros. Chaves gerenciadas pelo cliente oferecem maior flexibilidade para criar, desabilitar e revogar controles de acesso, assim como fazer a rotação deles. Você também pode auditar as chaves de criptografia usadas para proteger seus ativos seguros.

Use o Azure Key Vault para armazenar as chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves.  Para obter mais informações sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](../key-vault/general/overview.md)

## <a name="use-of-customer-managed-keys-for-an-automation-account"></a>Uso de chaves gerenciadas pelo cliente para uma conta de automação

Quando você usa a criptografia com chaves gerenciadas pelo cliente para uma conta de automação, a Automação do Azure encapsula a chave de criptografia da conta com a chave gerenciada pelo cliente no cofre de chaves associado. Habilitar chaves gerenciadas pelo cliente não afeta o desempenho e a conta é criptografada com a nova chave imediatamente, sem nenhum atraso.

Uma nova conta de automação é Always Encrypted usando chaves gerenciadas pela Microsoft. Não é possível habilitar chaves gerenciadas pelo cliente no momento em que a conta é criada. As chaves gerenciadas pelo cliente são armazenadas no Azure Key Vault e o cofre de chaves deve ser provisionado com políticas de acesso que concedem permissões de chave para a identidade gerenciada associada à conta de automação. A identidade gerenciada estará disponível somente depois que a conta de armazenamento for criada.

Quando você modifica a chave que está sendo usada para a criptografia de ativo seguro da Automação do Azure, habilitando ou desabilitando chaves gerenciadas pelo cliente, atualizando a versão da chave ou especificando uma chave diferente, a criptografia da chave de criptografia da conta é alterada, mas os ativos seguros em sua conta de automação do Azure não precisam ser criptografados novamente.

> [!NOTE] 
> Para habilitar chaves gerenciadas pelo cliente, você precisa fazer chamadas à API REST da Automação do Azure usando a versão de API 2020-01-13-preview

### <a name="prerequisites-for-using-customer-managed-keys-in-azure-automation"></a>Pré-requisitos para usar chaves gerenciadas pelo cliente na Automação do Azure

Antes de habilitar chaves gerenciadas pelo cliente para uma conta de automação, você deve verificar se os seguintes pré-requisitos são atendidos:

 - A chave gerenciada pelo cliente é armazenada em um Azure Key Vault. 
 - Habilite as propriedades de **Exclusão reversível** e **Não Limpar** no cofre de chaves. Esses recursos são necessários para permitir a recuperação de chaves em caso de exclusão acidental.
 - Somente as chaves RSA têm suporte com a criptografia de Automação do Azure. Para obter mais informações sobre chaves, confira [Sobre chaves, segredos e certificados do Azure Key Vault](../key-vault/general/about-keys-secrets-certificates.md).
- A conta de automação e o cofre de chaves podem estar em assinaturas diferentes, mas precisam estar no mesmo locatário do Azure Active Directory.

### <a name="assignment-of-an-identity-to-the-automation-account"></a>Atribuição de uma identidade à conta de automação

Para usar chaves gerenciadas pelo cliente com uma conta de automação, sua conta de automação precisa se autenticar no cofre de chaves que armazena chaves gerenciadas pelo cliente. A Automação do Azure usa identidades gerenciadas atribuídas ao sistema para autenticar a conta com o Azure Key Vault. Para obter mais informações sobre identidades gerenciadas, confira [O que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

Configure uma identidade gerenciada atribuída ao sistema à conta de automação usando a seguinte chamada à API REST:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corpo da solicitação:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

a identidade atribuída ao sistema para a conta de automação é retornada em uma resposta semelhante à seguinte:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configuration-of-the-key-vault-access-policy"></a>Configuração da política de acesso do Key Vault

Depois que uma identidade gerenciada é atribuída à conta de automação, você configura o acesso ao cofre de chaves que armazena chaves gerenciadas pelo cliente. A Automação do Azure requer **get**, **recover**, **wrapKey** e **UnwrapKey** nas chaves gerenciadas pelo cliente.

Essa política de acesso pode ser definida usando a seguinte chamada à API REST:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Corpo da solicitação:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> os campos **tenantId** e **objectId** devem ser fornecidos com valores de **identity.tenantId** e **identity.principalId**, respectivamente, da resposta da identidade gerenciada para a conta de automação.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Alterar a configuração da conta de automação para usar a chave gerenciada pelo cliente

Por fim, você pode mudar sua conta de automação de chaves gerenciadas pela Microsoft para chaves gerenciadas pelo cliente, usando a seguinte chamada à API REST:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corpo da solicitação:

```json
{
    "identity": {
    "type": "SystemAssigned"
    },
    "properties": {
        "encryption": {
            "keySource": "Microsoft.Keyvault",
            "keyvaultProperties": {
                "keyName": "sample-vault-key",
                "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
                "keyVersion": "7c73556c521340209371eaf623cc099d"
            }
        }
    }
}
```

Resposta de exemplo

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="rotation-of-a-customer-managed-key"></a>Rotação de uma chave gerenciada pelo cliente

Você pode fazer a rotação de uma chave gerenciada pelo cliente no Azure Key Vault de acordo com suas políticas de conformidade. Quando ocorre a rotação da chave, você deve atualizar a conta de automação para usar o novo URI da chave.

A rotação da chave não dispara a nova criptografia de ativos seguros na conta de automação. Nenhuma ação adicional necessária.

## <a name="revocation-of-access-to-a-customer-managed-key"></a>Revogação de acesso a uma chave gerenciada pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou a CLI do Azure. Para obter mais informações, confira [PowerShell do Azure Key Vault](/powershell/module/az.keyvault/) ou [CLI do Azure Key Vault](/cli/azure/keyvault). Revogar o acesso bloqueia efetivamente o acesso a todos os ativos seguros na conta de automação, pois a chave de criptografia não é acessível pelo Automação do Azure.

## <a name="next-steps"></a>Próximas etapas

- Para entender o Azure Key Vault, confira [O que é Azure Key Vault?](../key-vault/general/overview.md).
- Para trabalhar com certificados, confira [Gerenciar certificados na Automação do Azure](shared-resources/certificates.md).
- Para lidar com credenciais, confira [Gerenciar credenciais na Automação do Azure](shared-resources/credentials.md).
- Para usar variáveis de Automação, [Gerenciar variáveis na Automação do Azure](shared-resources/variables.md).
- Para obter ajuda ao trabalhar com conexões, confira [Gerenciar conexões na Automação do Azure](automation-connections.md).
