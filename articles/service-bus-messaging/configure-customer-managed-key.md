---
title: Configure sua própria chave para criptografar dados do barramento de serviço do Azure em repouso
description: Este artigo fornece informações sobre como configurar sua própria chave para criptografar dados REST do barramento de serviço do Azure.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 132ee3883b818dcc5a5d8e0cc7b372daee41e273
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928083"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Configurar chaves gerenciadas pelo cliente para criptografar dados do barramento de serviço do Azure em repouso usando o portal do Azure
O barramento de serviço Premium do Azure fornece criptografia de dados em repouso com Criptografia do Serviço de Armazenamento do Azure (Azure SSE). O barramento de serviço Premium usa o armazenamento do Azure para armazenar os dados. Todos os dados armazenados com o armazenamento do Azure são criptografados usando chaves gerenciadas pela Microsoft. Se você usar sua própria chave (também conhecida como Bring Your Own Key (BYOK) ou chave gerenciada pelo cliente), os dados ainda serão criptografados usando a chave gerenciada pela Microsoft, mas, além disso, a chave gerenciada pela Microsoft será criptografada usando a chave gerenciada pelo cliente. Esse recurso permite que você crie, gire, desabilite e revogue o acesso a chaves gerenciadas pelo cliente que são usadas para criptografar chaves gerenciadas pela Microsoft. Habilitar o recurso BYOK é um processo de instalação única em seu namespace.

Há algumas advertências para a chave gerenciada pelo cliente para criptografia do lado do serviço. 
- Esse recurso é suportado pela camada [Premium do barramento de serviço do Azure](service-bus-premium-messaging.md) . Ele não pode ser habilitado para namespaces de barramento de serviço da camada Standard.
- A criptografia só pode ser habilitada para namespaces novos ou vazios. Se o namespace contiver quaisquer filas ou tópicos, a operação de criptografia falhará.

Você pode usar Azure Key Vault para gerenciar suas chaves e auditar o uso da chave. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves. Para obter mais informações sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](../key-vault/general/overview.md)

Este artigo mostra como configurar um cofre de chaves com chaves gerenciadas pelo cliente usando o portal do Azure. Para saber como criar um cofre de chaves usando o portal do Azure, consulte [início rápido: criar um Azure Key Vault usando o portal do Azure](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> O uso de chaves gerenciadas pelo cliente com o barramento de serviço do Azure requer que o cofre de chaves tenha duas propriedades necessárias configuradas. Eles são:  **exclusão reversível** e **não limpeza**. Essas propriedades são habilitadas por padrão quando você cria um novo cofre de chaves no portal do Azure. No entanto, se você precisar habilitar essas propriedades em um cofre de chaves existente, deverá usar o PowerShell ou CLI do Azure.

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente
Para habilitar as chaves gerenciadas pelo cliente no portal do Azure, siga estas etapas:

1. Navegue até o namespace Premium do barramento de serviço.
2. Na página **configurações** do namespace do barramento de serviço, selecione **criptografia**.
3. Selecione a **criptografia de chave gerenciada pelo cliente em repouso** , conforme mostrado na imagem a seguir.

    ![Habilitar chave gerenciada pelo cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Configurar um cofre de chaves com chaves

Depois de habilitar as chaves gerenciadas pelo cliente, você precisa associar a chave gerenciada pelo cliente ao namespace do barramento de serviço do Azure. O barramento de serviço dá suporte apenas a Azure Key Vault. Se você habilitar a opção **criptografia com chave gerenciada pelo cliente** na seção anterior, precisará ter a chave importada para Azure Key Vault. Além disso, as chaves devem ter **exclusão reversível** e **não limpar** configuradas para a chave. Essas configurações podem ser configuradas usando o [PowerShell](../key-vault/general/key-vault-recovery.md) ou a [CLI](../key-vault/general/key-vault-recovery.md).

1. Para criar um novo cofre de chaves, siga o guia de [início rápido](../key-vault/general/overview.md)do Azure Key Vault. Para obter mais informações sobre como importar chaves existentes, consulte [sobre chaves, segredos e certificados](../key-vault/general/about-keys-secrets-certificates.md).
1. Para ativar a exclusão reversível e limpar a proteção ao criar um cofre, use o comando [AZ keyvault Create](/cli/azure/keyvault#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Para adicionar a proteção de limpeza a um cofre existente (que já tem a exclusão reversível habilitada), use o comando [AZ keyvault Update](/cli/azure/keyvault#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Crie chaves seguindo estas etapas:
    1. Para criar uma nova chave, selecione **Gerar/Importar** do menu **Chaves** em **Configurações**.
        
        ![Selecionar botão gerar/importar](./media/configure-customer-managed-key/select-generate-import.png)

    1. Defina **Opções** para **Gerar** e forneça um nome à chave.

        ![Criar uma chave](./media/configure-customer-managed-key/create-key.png) 

    1. Agora você pode selecionar essa chave para associar ao namespace do barramento de serviço para criptografia na lista suspensa. 

        ![Selecionar chave do Key Vault](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Para redundância, você pode adicionar até três chaves. Caso uma das chaves tenha expirado ou não esteja acessível, as outras chaves serão usadas para criptografia.
        
    1. Preencha os detalhes da chave e clique em **selecionar**. Isso habilitará a criptografia da chave gerenciada pela Microsoft com sua chave (chave gerenciada pelo cliente). 


    > [!IMPORTANT]
    > Se você pretende usar a chave gerenciada pelo cliente juntamente com a recuperação de desastre geográfica, examine esta seção. 
    >
    > Para habilitar a criptografia da chave gerenciada pela Microsoft com uma chave gerenciada pelo cliente, uma [política de acesso](../key-vault/general/secure-your-key-vault.md) é configurada para a identidade gerenciada do barramento de serviço no cofre de chaves do Azure especificado. Isso garante o acesso controlado ao Azure keyvault do namespace do barramento de serviço do Azure.
    >
    > Devido a isso:
    > 
    >   * Se a [recuperação de desastre geográfica](service-bus-geo-dr.md) já estiver habilitada para o namespace do barramento de serviço e você estiver procurando habilitar a chave gerenciada pelo cliente, então 
    >     * Interromper o emparelhamento
    >     * [Configure a política de acesso](../key-vault/general/assign-access-policy-portal.md) para a identidade gerenciada para os namespaces primário e secundário para o cofre de chaves.
    >     * Configure a criptografia no namespace primário.
    >     * Emparelhe novamente os namespaces primário e secundário.
    > 
    >   * Se você estiver procurando habilitar a DR geográfica em um namespace do barramento de serviço em que a chave gerenciada pelo cliente já está configurada,
    >     * [Configure a política de acesso](../key-vault/general/assign-access-policy-portal.md) para a identidade gerenciada para o namespace secundário para o cofre de chaves.
    >     * Emparelhe os namespaces primários e secundários.


## <a name="rotate-your-encryption-keys"></a>Girar suas chaves de criptografia

Você pode girar sua chave no cofre de chaves usando o mecanismo de rotação do Azure Key Vaults. As datas de ativação e expiração também podem ser definidas para automatizar a rotação de chaves. O serviço do barramento de serviço detectará novas versões de chave e começará a usá-las automaticamente.

## <a name="revoke-access-to-keys"></a>Revogar o acesso às chaves

Revogar o acesso às chaves de criptografia não limpará os dados do barramento de serviço. No entanto, os dados não podem ser acessados no namespace do barramento de serviço. Você pode revogar a chave de criptografia por meio da política de acesso ou excluindo a chave. Saiba mais sobre as políticas de acesso e proteger o cofre de chaves de [acesso seguro a um cofre de chaves](../key-vault/general/secure-your-key-vault.md).

Depois que a chave de criptografia for revogada, o serviço do barramento de serviço no namespace criptografado se tornará inoperável. Se o acesso à chave estiver habilitado ou a chave excluída for restaurada, o serviço do barramento de serviço escolherá a chave para que você possa acessar os dados do namespace do barramento de serviço criptografado.

## <a name="use-resource-manager-template-to-enable-encryption"></a>Usar o modelo do Resource Manager para habilitar a criptografia
Esta seção mostra como realizar as tarefas a seguir usando **modelos de Azure Resource Manager**. 

1. Crie um namespace do barramento de serviço **Premium** com uma **identidade de serviço gerenciada**.
2. Crie um **cofre de chaves** e conceda o acesso de identidade de serviço ao cofre de chaves. 
3. Atualize o namespace do barramento de serviço com as informações do cofre de chaves (chave/valor). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Criar um namespace do barramento de serviço Premium com identidade de serviço gerenciada
Esta seção mostra como criar um namespace do barramento de serviço do Azure com a identidade de serviço gerenciada usando um modelo de Azure Resource Manager e o PowerShell. 

1. Crie um modelo de Azure Resource Manager para criar um namespace da camada Premium do barramento de serviço com uma identidade de serviço gerenciada. Nomeie o arquivo: **CreateServiceBusPremiumNamespace.jsem**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Crie um arquivo de parâmetro de modelo chamado: **CreateServiceBusPremiumNamespaceParams.jsem**. 

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<ServiceBusNamespaceName>` -Nome do namespace do barramento de serviço
    > - `<Location>` -Localização do seu namespace do barramento de serviço

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Execute o seguinte comando do PowerShell para implantar o modelo para criar um namespace Premium do barramento de serviço. Em seguida, recupere a ID do namespace do barramento de serviço para usá-lo mais tarde. Substitua `{MyRG}` pelo nome do grupo de recursos antes de executar o comando.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Conceder acesso de identidade do namespace do barramento de serviço ao cofre de chaves

1. Execute o comando a seguir para criar um cofre de chaves com **proteção de limpeza** e **exclusão reversível** habilitada. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (OU)
    
    Execute o comando a seguir para atualizar um **cofre de chaves existente**. Especifique os valores para o grupo de recursos e os nomes do cofre de chaves antes de executar o comando. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Defina a política de acesso do cofre de chaves para que a identidade gerenciada do namespace do barramento de serviço possa acessar o valor de chave no cofre de chaves. Use a ID do namespace do barramento de serviço da seção anterior. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Criptografar dados no namespace do barramento de serviço com a chave gerenciada pelo cliente do Key Vault
Você concluiu as seguintes etapas até agora: 

1. Criou um namespace Premium com uma identidade gerenciada.
2. Crie um cofre de chaves e receba o acesso de identidade gerenciada ao cofre de chaves. 

Nesta etapa, você atualizará o namespace do barramento de serviço com informações do Key Vault. 

1. Crie um arquivo JSON chamado **UpdateServiceBusNamespaceWithEncryption.js** com o seguinte conteúdo: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Crie um arquivo de parâmetro de modelo: **UpdateServiceBusNamespaceWithEncryptionParams.jsem**.

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<ServiceBusNamespaceName>` -Nome do namespace do barramento de serviço
    > - `<Location>` -Localização do seu namespace do barramento de serviço
    > - `<KeyVaultName>` -Nome do cofre de chaves
    > - `<KeyName>` -Nome da chave no cofre de chaves  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Execute o seguinte comando do PowerShell para implantar o modelo do Resource Manager. Substitua `{MyRG}` pelo nome do seu grupo de recursos antes de executar o comando. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:
- [Visão geral do barramento de serviço](service-bus-messaging-overview.md)
- [Visão geral de Key Vault](../key-vault/general/overview.md)