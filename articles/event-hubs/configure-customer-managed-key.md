---
title: Configure sua própria chave para criptografar dados de hubs de eventos do Azure em repouso
description: Este artigo fornece informações sobre como configurar sua própria chave para criptografar dados REST dos hubs de eventos do Azure.
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: c9d1ac1c3a3387600fed80939598baafe658054b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "100595985"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Configurar chaves gerenciadas pelo cliente para criptografar dados de hubs de eventos do Azure em repouso usando o portal do Azure
Os hubs de eventos do Azure fornecem criptografia de dados em repouso com o Criptografia do Serviço de Armazenamento do Azure (Azure SSE). O serviço de hubs de eventos usa o armazenamento do Azure para armazenar os dados. Todos os dados armazenados com o armazenamento do Azure são criptografados usando chaves gerenciadas pela Microsoft. Se você usar sua própria chave (também conhecida como Bring Your Own Key (BYOK) ou chave gerenciada pelo cliente), os dados ainda serão criptografados usando a chave gerenciada pela Microsoft, mas, além disso, a chave gerenciada pela Microsoft será criptografada usando a chave gerenciada pelo cliente. Esse recurso permite que você crie, gire, desabilite e revogue o acesso a chaves gerenciadas pelo cliente que são usadas para criptografar chaves gerenciadas pela Microsoft. Habilitar o recurso BYOK é um processo de instalação única em seu namespace.

> [!NOTE]
> - O recurso BYOK tem suporte dos clusters de [locatário único dedicados aos hubs de eventos](event-hubs-dedicated-overview.md) . Ele não pode ser habilitado para namespaces de hubs de eventos padrão.
> - A criptografia só pode ser habilitada para namespaces novos ou vazios. Se o namespace contiver hubs de eventos, a operação de criptografia falhará.

Você pode usar Azure Key Vault para gerenciar suas chaves e auditar o uso da chave. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves. Para obter mais informações sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](../key-vault/general/overview.md)

Este artigo mostra como configurar um cofre de chaves com chaves gerenciadas pelo cliente usando o portal do Azure. Para saber como criar um cofre de chaves usando o portal do Azure, consulte [início rápido: criar um Azure Key Vault usando o portal do Azure](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> O uso de chaves gerenciadas pelo cliente com os hubs de eventos do Azure requer que o cofre de chaves tenha duas propriedades necessárias configuradas. Eles são:  **exclusão reversível** e **não limpeza**. Essas propriedades são habilitadas por padrão quando você cria um novo cofre de chaves no portal do Azure. No entanto, se você precisar habilitar essas propriedades em um cofre de chaves existente, deverá usar o PowerShell ou CLI do Azure.

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente
Para habilitar as chaves gerenciadas pelo cliente no portal do Azure, siga estas etapas:

1. Navegue até o cluster Hubs de Eventos Dedicados.
1. Selecione o namespace no qual você deseja habilitar BYOK.
1. Na página **configurações** do seu namespace de hubs de eventos, selecione **criptografia**. 
1. Selecione a **criptografia de chave gerenciada pelo cliente em repouso** , conforme mostrado na imagem a seguir. 

    ![Habilitar chave gerenciada pelo cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Configurar um cofre de chaves com chaves
Depois de habilitar as chaves gerenciadas pelo cliente, você precisa associar a chave gerenciada pelo cliente ao namespace de hubs de eventos do Azure. Os hubs de eventos oferecem suporte apenas a Azure Key Vault. Se você habilitar a opção **criptografia com chave gerenciada pelo cliente** na seção anterior, precisará ter a chave importada para Azure Key Vault. Além disso, as chaves devem ter **exclusão reversível** e **não limpar** configuradas para a chave. Essas configurações podem ser configuradas usando o [PowerShell](../key-vault/general/key-vault-recovery.md) ou a [CLI](../key-vault/general/key-vault-recovery.md).

1. Para criar um novo cofre de chaves, siga o guia de [início rápido](../key-vault/general/overview.md)do Azure Key Vault. Para obter mais informações sobre como importar chaves existentes, consulte [sobre chaves, segredos e certificados](../key-vault/general/about-keys-secrets-certificates.md).
1. Para ativar a exclusão reversível e limpar a proteção ao criar um cofre, use o comando [AZ keyvault Create](/cli/azure/keyvault#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Para adicionar a proteção de limpeza a um cofre existente (que já tem a exclusão reversível habilitada), use o comando [AZ keyvault Update](/cli/azure/keyvault#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Crie chaves seguindo estas etapas:
    1. Para criar uma nova chave, selecione **Gerar/Importar** do menu **Chaves** em **Configurações**.
        
        ![Selecionar botão gerar/importar](./media/configure-customer-managed-key/select-generate-import.png)
    1. Defina **Opções** para **Gerar** e forneça um nome à chave.

        ![Criar uma chave](./media/configure-customer-managed-key/create-key.png) 
    1. Agora você pode selecionar essa chave para associar ao namespace de hubs de eventos para criptografia na lista suspensa. 

        ![Selecionar chave do Key Vault](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Preencha os detalhes da chave e clique em **selecionar**. Isso habilitará a criptografia da chave gerenciada pela Microsoft com sua chave (chave gerenciada pelo cliente). 


## <a name="rotate-your-encryption-keys"></a>Girar suas chaves de criptografia
Você pode girar sua chave no cofre de chaves usando o mecanismo de rotação do Azure Key Vaults. As datas de ativação e expiração também podem ser definidas para automatizar a rotação de chaves. O serviço de hubs de eventos detectará novas versões de chave e começará a usá-las automaticamente.

## <a name="revoke-access-to-keys"></a>Revogar o acesso às chaves
Revogar o acesso às chaves de criptografia não limpará os dados dos hubs de eventos. No entanto, os dados não podem ser acessados no namespace de hubs de eventos. Você pode revogar a chave de criptografia por meio da política de acesso ou excluindo a chave. Saiba mais sobre as políticas de acesso e proteger o cofre de chaves de [acesso seguro a um cofre de chaves](../key-vault/general/secure-your-key-vault.md).

Depois que a chave de criptografia for revogada, o serviço de hubs de eventos no namespace criptografado se tornará inoperável. Se o acesso à chave estiver habilitado ou a chave de exclusão for restaurada, o serviço de hubs de eventos escolherá a chave para que você possa acessar os dados do namespace de hubs de eventos criptografados.

## <a name="set-up-diagnostic-logs"></a>Configuração dos logs de diagnóstico 
A configuração de logs de diagnóstico para namespaces habilitados para BYOK fornece as informações necessárias sobre as operações. Esses logs podem ser habilitados e transmitidos posteriormente para um hub de eventos ou analisados por meio do log Analytics ou transmitidos para o armazenamento para executar análises personalizadas. Para saber mais sobre os logs de diagnóstico, consulte [visão geral dos logs de diagnóstico do Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Habilitar logs do usuário
Siga estas etapas para habilitar logs para chaves gerenciadas pelo cliente.

1. Na portal do Azure, navegue até o namespace que tem BYOK habilitado.
1. Selecione **configurações de diagnóstico** em **monitoramento**.

    ![Selecionar configurações de diagnóstico](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Selecione **+ Adicionar configuração de diagnóstico**. 

    ![Selecione Adicionar configuração de diagnóstico](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Forneça um **nome** e selecione para onde deseja transmitir os logs.
1. Selecione **CustomerManagedKeyUserLogs** e **salvar**. Essa ação habilita os logs para BYOK no namespace.

    ![Selecione a opção de logs de usuário de chave gerenciada pelo cliente](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Esquema do log 
Todos os logs são armazenados no formato JSON (JavaScript Object Notation). Cada entrada tem campos de cadeia de caracteres que usam o formato descrito na tabela a seguir. 

| Nome | Descrição |
| ---- | ----------- | 
| TaskName | Descrição da tarefa que falhou. |
| ActivityId | ID interna que é usada para acompanhamento. |
| category | Define a classificação da tarefa. Por exemplo, se a chave do cofre de chaves estiver sendo desabilitada, ela seria uma categoria de informações ou, se uma chave não puder ser desativada, ela poderá ficar com erro. |
| resourceId | ID de recurso do Azure Resource Manager |
| keyVault | Nome completo do cofre de chaves. |
| chave | O nome da chave que é usado para criptografar o namespace de hubs de eventos. |
| version | A versão da chave que está sendo usada. |
| operation | A operação executada na chave em seu cofre de chaves. Por exemplo, desabilitar/habilitar a chave, encapsular ou desencapsular |
| code | O código associado à operação. Exemplo: código de erro 404 significa que a chave não foi encontrada. |
| message | Qualquer mensagem de erro associada à operação |

Aqui está um exemplo do log para uma chave gerenciada pelo cliente:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Usar o modelo do Resource Manager para habilitar a criptografia
Esta seção mostra como realizar as tarefas a seguir usando **modelos de Azure Resource Manager**. 

1. Crie um **namespace de hubs de eventos** com uma identidade de serviço gerenciada.
2. Crie um **cofre de chaves** e conceda o acesso de identidade de serviço ao cofre de chaves. 
3. Atualize o namespace de hubs de eventos com as informações do cofre de chaves (chave/valor). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Criar um cluster de hubs de eventos e um namespace com a identidade de serviço gerenciada
Esta seção mostra como criar um namespace de hubs de eventos do Azure com a identidade de serviço gerenciada usando um modelo de Azure Resource Manager e o PowerShell. 

1. Crie um modelo de Azure Resource Manager para criar um namespace de hubs de eventos com uma identidade de serviço gerenciada. Nomeie o arquivo: **CreateEventHubClusterAndNamespace.jsem**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Crie um arquivo de parâmetro de modelo chamado: **CreateEventHubClusterAndNamespaceParams.jsem**. 

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<EventHubsClusterName>` -Nome do seu cluster de hubs de eventos    
    > - `<EventHubsNamespaceName>` -Nome do seu namespace de hubs de eventos
    > - `<Location>` -Localização do seu namespace de hubs de eventos

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Execute o seguinte comando do PowerShell para implantar o modelo para criar um namespace de hubs de eventos. Em seguida, recupere a ID do namespace de hubs de eventos para usá-lo mais tarde. Substitua `{MyRG}` pelo nome do grupo de recursos antes de executar o comando.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Conceder acesso de identidade ao namespace de hubs de eventos ao key Vault

1. Execute o comando a seguir para criar um cofre de chaves com **proteção de limpeza** e **exclusão reversível** habilitada. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (OU)    
    
    Execute o comando a seguir para atualizar um **cofre de chaves existente**. Especifique os valores para o grupo de recursos e os nomes do cofre de chaves antes de executar o comando. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Defina a política de acesso do cofre de chaves para que a identidade gerenciada do namespace de hubs de eventos possa acessar o valor de chave no cofre de chaves. Use a ID do namespace de hubs de eventos da seção anterior. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Criptografar dados no namespace dos hubs de eventos com a chave gerenciada pelo cliente do Key Vault
Você concluiu as seguintes etapas até agora: 

1. Criou um namespace Premium com uma identidade gerenciada.
2. Crie um cofre de chaves e receba o acesso de identidade gerenciada ao cofre de chaves. 

Nesta etapa, você atualizará o namespace de hubs de eventos com informações do Key Vault. 

1. Crie um arquivo JSON chamado **CreateEventHubClusterAndNamespace.js** com o seguinte conteúdo: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
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

2. Crie um arquivo de parâmetro de modelo: **UpdateEventHubClusterAndNamespaceParams.jsem**. 

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<EventHubsClusterName>` -Nome do seu cluster de hubs de eventos.        
    > - `<EventHubsNamespaceName>` -Nome do seu namespace de hubs de eventos
    > - `<Location>` -Localização do seu namespace de hubs de eventos
    > - `<KeyVaultName>` -Nome do cofre de chaves
    > - `<KeyName>` -Nome da chave no cofre de chaves

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Solucionar problemas
Como prática recomendada, sempre habilite logs como mostrado na seção anterior. Ele ajuda a controlar as atividades quando a criptografia BYOK está habilitada. Ele também ajuda a definir o escopo dos problemas.

Veja a seguir os códigos de erros comuns a serem procurados quando a criptografia BYOK estiver habilitada.

| Ação | Código do erro | Estado resultante dos dados |
| ------ | ---------- | ----------------------- | 
| Remover a permissão de encapsulamento/desencapsulamento de um cofre de chaves | 403 |    Inaccessible |
| Remover a associação de função do AAD de uma entidade de segurança do AAD que concedeu a permissão de encapsulamento/desencapsulamento | 403 |  Inaccessible |
| Excluir uma chave de criptografia do cofre de chaves | 404 | Inaccessible |
| Excluir o cofre de chaves | 404 | Inacessível (pressupõe que a exclusão reversível esteja habilitada, que é uma configuração necessária.) |
| Alterando o período de validade na chave de criptografia, de modo que já tenha expirado | 403 |   Inaccessible  |
| Alterar o NBF (não antes) de tal chave de criptografia de chave não está ativa | 403 | Inaccessible  |
| Selecionando a opção **permitir serviços MSFT** para o Firewall do cofre de chaves ou bloqueando o acesso à rede para o cofre de chaves que tem a chave de criptografia | 403 | Inaccessible |
| Movendo o cofre de chaves para um locatário diferente | 404 | Inaccessible |  
| Problema de rede intermitente ou interrupção de DNS/AAD/MSI |  | Acessível usando a chave de criptografia de dados em cache |

> [!IMPORTANT]
> Para habilitar o geo-DR em um namespace que está usando a criptografia BYOK, o namespace secundário para emparelhamento deve estar em um cluster dedicado e deve ter uma identidade gerenciada atribuída ao sistema habilitada nele. Para saber mais, confira [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:
- [Visão geral de Hubs de Eventos](event-hubs-about.md)
- [Visão geral de Key Vault](../key-vault/general/overview.md)