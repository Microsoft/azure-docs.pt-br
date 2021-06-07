---
title: Criar função de Azure Resource Manager personalizada e atribuir à entidade de serviço-Azure
description: Este artigo fornece orientação sobre como criar uma função de Azure Resource Manager personalizada e atribuir à entidade de serviço para análise de vídeo ao vivo em IoT Edge usando CLI do Azure.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 80974c111dd451314635d06334766322bc68e437
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210437"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>Criar função de Azure Resource Manager personalizada e atribuir à entidade de serviço

A análise de vídeo ao vivo na instância do módulo IoT Edge precisa de uma conta ativa dos serviços de mídia do Azure para que ela funcione corretamente. A relação entre a análise de vídeo ao vivo no módulo IoT Edge e a conta de serviço de mídia do Azure é estabelecida por meio de um conjunto de propriedades de módulo de propriedade. Uma dessas propriedades de cópia é uma [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) que permite que a instância do módulo se comunique e dispare as operações necessárias na conta dos serviços de mídia. Para minimizar a inutilização potencial e/ou a exposição acidental de dados do dispositivo de borda, essa entidade de serviço deve ter a menor quantidade de privilégios.

Este artigo mostra as etapas para criar uma função de Azure Resource Manager personalizada com Azure Cloud Shell, que é usada para criar uma entidade de serviço.

## <a name="prerequisites"></a>Pré-requisitos  

Os pré-requisitos para este artigo são os seguintes:

* Assinatura do Azure com assinatura de proprietário.
* Um Azure Active Directory com privilégios para criar um aplicativo e atribuir a entidade de serviço a uma função.

A maneira mais fácil de verificar se a sua conta tem as permissões adequadas é por meio do portal. Consulte [Verificar permissão necessária](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="overview"></a>Visão geral  

Vamos abordar as etapas para criar uma função personalizada e vinculá-la a uma entidade de serviço na seguinte ordem:

1. Crie uma conta de serviço de mídia, se você ainda não tiver uma.
1. Crie uma entidade de serviço.
1. Crie uma função de Azure Resource Manager personalizada com privilégios limitados.
1. "Restringir" os privilégios de entidade de serviço usando a função personalizada criada.
1. Execute um teste simples para ver se é possível restringir com êxito a entidade de serviço.
1. Capture os parâmetros que serão usados nos manifestos de implantação do IoT Edge.

### <a name="create-a-media-services-account"></a>Criar uma conta de Serviços de Mídia  

Se você não tiver uma conta de serviço de mídia, use as etapas a seguir para criar uma.

1. Navegue até o [Cloud Shell](https://shell.azure.com/).
1. Selecione "bash" como seu ambiente na lista suspensa no lado esquerdo da janela do Shell

    ![A tela capturs mostra o bash selecionado na janela do Shell.](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. Defina sua assinatura do Azure como a conta padrão usando o seguinte modelo de comando:
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. Crie um [grupo de recursos](/cli/azure/group#az-group-create) e uma [conta de armazenamento](/cli/azure/storage/account#az-storage-account-create).
1. Agora, crie uma conta de serviço de mídia do Azure usando o seguinte modelo de comando no Cloud Shell:

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>Criar uma entidade de serviço  

Agora, vamos criar uma nova entidade de serviço e vinculá-la à sua conta de serviço de mídia.

Sem nenhum parâmetro de autenticação, a autenticação baseada em senha é usada com uma senha aleatória para a entidade de serviço. Em Cloud Shell, use o seguinte modelo de comando:

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

Esse comando produz uma resposta como esta:

```
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "<yourServicePrincipalPassword>",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": " <yourAMSAccountName >",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "Central US",
  "ResourceGroup": " <yourResouceGroup >",
  "SubscriptionId": "<yourSubscriptionId>"
}

```
1. A saída de uma entidade de serviço com autenticação de senha inclui a chave de senha que, nesse caso, é o parâmetro "AadSecret". 

    Certifique-se de que tenha copiado esse valor já que não será possível recuperá-lo posteriormente. Se você esquecer a senha, [redefina as credenciais da entidade de serviço](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).
1. A appId e a chave de locatário aparecem na saída como "AadClientId" e "AadTenantId", respectivamente. Eles são usados na autenticação de entidade de serviço. Registre seus valores, mas eles podem ser recuperados a qualquer momento com o comando [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list).

### <a name="create-a-custom-role-definition"></a>Criar uma definição de função personalizada  

Para criar uma função personalizada, aqui estão as etapas que você deve seguir:

1. Crie um arquivo JSON de definição de função no sistema local e salve o texto a seguir no arquivo. 
    1. Substitua < Suaidassinatura> pela sua ID de assinatura do Azure
    1. As únicas ações permitidas para essa função são:
        * listContainerSas – ajuda o módulo a listar as URLs do contêiner de armazenamento com SAS (assinaturas de acesso compartilhado) para carregar e baixar o conteúdo do ativo.
        * Gravar ativos – ajuda o módulo a criar ou atualizar qualquer ativo
        * listEdgePolicies – lista as políticas que são aplicadas ao dispositivo de borda  
        
        ```
        {
          "Name": "LVAEdge User",
          "IsCustom": true,
          "Description": "Can create assets, view list of containers and view Edge policies",
          "Actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action"
          ],
          "NotActions": [],
          "DataActions": [],
          "NotDataActions": [],
          "AssignableScopes": [
            "/subscriptions/<yourSubscriptionId>"
          ]
        }
        ```  
          
1. Depois de criado, execute o seguinte modelo de comando para criar a nova definição de função na assinatura:
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    Após a execução bem-sucedida do comando, você verá a seguinte saída:
    
    ```
    {
      "assignableScopes": [
      "/subscriptions/<yourSubscriptionId>"
      ],
      "description": "Can create assets, view list of containers and view Edge policies",
      "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<unique name>",
      "name": "<unique name>",
      "permissions": [
        {
          "actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action",
          ],
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": " LVAEdge User ",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

### <a name="create-role-assignment"></a>Criar atribuição de função  

Para adicionar uma atribuição de função, você precisará do objectId da entidade de serviço à qual deseja atribuir a função personalizada que acabou de criar.

Use o seguinte comando em Cloud Shell para obter o objectId:

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>` pode ser recuperado da saída da etapa [criar entidade de serviço](#create-service-principal) .

O comando acima imprimirá o objectId da entidade de serviço. 

```
“objectId” : “<yourObjectId>”,
```

Use o modelo de [comando AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create) para vincular a função personalizada com a entidade de serviço:

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

Parâmetros:

|Parâmetros|Descrição| 
|---|---|
|--função |Nome ou ID da função personalizada. Em nosso caso: "usuário LVAEdge".|
|--atribuir-ID de objeto|ID de objeto da entidade de serviço que será usada.|

O resultado terá a seguinte aparência:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleAssignments/<yourCustomRoleId>",
  "name": "<yourCustomRoleId>",
  "principalId": "<yourServicePrincipalId>",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<yourRoleDefinitionId>",
  "scope": "/subscriptions/<yourSubscriptionId>",
  "type": "Microsoft.Authorization/roleAssignments"
} 
```

### <a name="confirm-that-role-assignment-happened"></a>Confirme se a atribuição de função ocorreu

Para confirmar que a entidade de serviço agora está vinculada à função personalizada que acabamos de criar, execute o seguinte comando:

```
az role assignment list  --assignee < objectId>
```

O resultado deve ter esta aparência:

```
[
  {
    "canDelegate": null,
    "id": "/subscriptions/xxx/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "principalId": "<yourServicePrincipalID>",
    "principalName": "<yourServicePrincipalName>",
    "principalType": "ServicePrincipal",
    "roleDefinitionId": "/subscriptions/xxx/providers/Microsoft.Authorization/roleDefinitions/zzz",
    "roleDefinitionName": "LVAEdge User",
    "scope": "/subscriptions/<yourSubscription ID>",
    "type": "Microsoft.Authorization/roleAssignments
  }
]  
```
 
Procure "roleDefinitionName" e veja que seu valor está definido como "LVAEdge User". 

Isso confirma que vinculamos a função de usuário personalizada à entidade de serviço usada para nosso aplicativo.

### <a name="test-the-service-principal-access-control"></a>Testar o controle de acesso da entidade de serviço

1. Faça logon usando a entidade de serviço. Para isso, precisaremos de três informações para que o Azure Active Directory nos conceda o token de acesso apropriado que podemos obter da saída da etapa [criar entidade de serviço](#create-service-principal) :
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. Agora, vamos tentar fazer logon usando o modelo de comando abaixo:
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  Agora, vamos ver se o logon é restrito à entidade de serviço com a função "usuário LVAEdge" tentando criar um grupo de recursos para verificar se ele falha. Execute o seguinte comando no Cloud Shell:

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    Esse comando deve falhar e se parecerá com:
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>Próximas etapas  

Observe os seguintes valores deste artigo. Esses valores serão necessários para que você configure as propriedades de ' Atualizar ' da análise de vídeo ao vivo no módulo IoT Edge, confira o [esquema JSON do módulo](module-twin-configuration-schema.md).

| Variável deste artigo|Nome da propriedade bificada para análise de vídeo ao vivo no IoT Edge|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|
