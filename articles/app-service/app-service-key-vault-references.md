---
title: Referências do Key Vault – Serviço de Aplicativo do Azure | Microsoft Docs
description: Referência conceitual e guia de configuração para referências do Azure Key Vault no Serviço de Aplicativo do Azure e nas Funções do Azure
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: e7a049c8def0a5014aeb8a0e7a16aaa8def28009
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705692"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>Use as referências do Key Vault para o serviço de aplicativo e as funções do Azure (visualização)

> [!NOTE] 
> As referências do Key Vault estão atualmente em pré-visualização.

Este tópico mostra como trabalhar com segredos do Cofre de Chaves do Azure no seu aplicativo Serviço de Aplicativo ou no Aplicativo de Funções do Azure sem exigir alterações de código. [Azure Key Vault](../key-vault/key-vault-overview.md) é um serviço que fornece gerenciamento centralizado de segredos, com controle total sobre políticas de acesso e histórico de auditoria.

## <a name="granting-your-app-access-to-key-vault"></a>Concedendo o seu acesso ao aplicativo para o Google Key Vault

Para ler os segredos do Key Vault, você precisa criar um vault e conceder permissão ao aplicativo para acessá-lo.

1. Crie um cofre de chaves seguindo o início rápido do [ Key Vault ](../key-vault/quick-create-cli.md).

1. Crie uma [Identidade gerenciada designada pelo sistema](overview-managed-identity.md) para seu aplicativo.

   > [!NOTE] 
   > No momento, as referências do Key Vault suportam apenas identidades gerenciadas atribuídas pelo sistema. Identidades atribuídas pelo usuário não podem ser usadas.

1. Crie uma política de [acesso no Key Vault](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) para a identidade do aplicativo que você criou anteriormente. Ative a permissão secreta "Obter" nesta política. Não defina o "aplicativo autorizado" ou as configurações `applicationId`, pois isso não é compatível com uma identidade gerenciada.

    Concedendo acesso a um aplicativo identidade no cofre de chaves é uma operação de onetime e permanecerá igual para todas as assinaturas do Azure. Você pode usá-lo para implantar certificados quantos desejar. 

## <a name="reference-syntax"></a>Sintaxe de referência

Uma referência do Key Vault é da forma `@Microsoft.KeyVault({referenceString})`, em que `{referenceString}` é substituído por uma das seguintes opções:

> [!div class="mx-tdBreakAll"]
> | Cadeia de caracteres de referência                                                            | DESCRIÇÃO                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri = _secretUri_                                                       | O **SegredoUri** deve ser o URI do plano de dados completo de um segredo no Key Vault, incluindo uma versão, por exemplo, https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName = _vaultName_; SecretName = _secretName_; SecretVersion = _secretVersion_ | O **VaultName** deve ser o nome do seu recurso Key Vault. O **SecretName** deve ser o nome do segredo de destino. O **SecretVersion** deve ser a versão do segredo a ser usado. |

> [!NOTE] 
> Na visualização atual, as versões são obrigatórias. Ao girar segredos, você precisará atualizar a versão na configuração do seu aplicativo.

Por exemplo, uma referência completa seria semelhante ao seguinte:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Como alternativa:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Configurações de aplicativos de origem do cofre de chaves

As referências do Key Vault podem ser usadas como valores para [Configurações do aplicativo](configure-common.md#configure-app-settings), permitindo que você mantenha segredos no Key Vault em vez da configuração do site. As configurações do aplicativo são criptografadas com segurança em repouso, mas se você precisar de recursos de gerenciamento secreto, elas devem entrar no Key Vault.

Para usar uma referência do Key Vault para uma configuração de aplicativo, defina a referência como o valor da configuração. Seu aplicativo pode fazer referência ao segredo por meio de sua chave normalmente. Nenhuma alteração de código é necessária.

> [!TIP]
> A maioria das configurações de aplicativos que usam referências do Key Vault deve ser marcada como configurações de slot, pois você deve ter cofres separados para cada ambiente.

### <a name="azure-resource-manager-deployment"></a>Implantação do Azure Resource Manager

Ao automatizar implantações de recursos por meio dos modelos do Azure Resource Manager, talvez seja necessário sequenciar suas dependências em uma ordem específica para que esse recurso funcione. Observe que você precisará definir as configurações do aplicativo como seu próprio recurso, em vez de usar uma propriedade `siteConfig` na definição do site. Isso ocorre porque o site precisa ser definido primeiro para que a identidade atribuída pelo sistema seja criada com ele e possa ser usada na política de acesso.

Um exemplo de psuedo-template para um aplicativo de função pode ser semelhante ao seguinte:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> Neste exemplo, a implantação do controle de origem depende das configurações do aplicativo. Esse comportamento normalmente é inseguro, pois a atualização da configuração do aplicativo se comporta de maneira assíncrona. No entanto, como incluímos a configuração do aplicativo `WEBSITE_ENABLE_SYNC_UPDATE_SITE`, a atualização é síncrona. Isso significa que a implantação do controle de origem só será iniciada quando as configurações do aplicativo tiverem sido totalmente atualizadas.
