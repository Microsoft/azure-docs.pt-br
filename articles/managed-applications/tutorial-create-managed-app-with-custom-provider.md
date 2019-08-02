---
title: Criar um aplicativo gerenciado do Azure com ações e recursos personalizados
description: Este tutorial descreve como criar um aplicativo gerenciado do Azure com um provedor personalizado do Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.openlocfilehash: 3dd0887114156956b55f554d0265e3ca2b9b10ab
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68336101"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Tutorial: criar um aplicativo gerenciado com ações e recursos personalizados

Neste tutorial, você cria seu próprio aplicativo gerenciado com ações e recursos personalizados. O aplicativo gerenciado conterá uma ação personalizada na página de `Overview`, um tipo de recurso personalizado exibido como um item de menu separado em `Table of Content` e uma ação de contexto personalizada na página de recursos personalizados.

Este tutorial inclui as seguintes etapas:

> [!div class="checklist"]
> * Criar um arquivo de definição da interface do usuário para criar uma instância de aplicativo gerenciado
> * Criar um modelo de implantação com provedor personalizado do Azure, conta de armazenamento do Azure e função do Azure
> * Criar artefato de definição de exibição com ações e recursos personalizados
> * Implantar uma definição de aplicativo gerenciado
> * Implantar uma instância do aplicativo gerenciado
> * Executar ações personalizadas e criar recursos personalizados

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará saber:

* Como [Criar e publicar a definição de um aplicativo gerenciado](publish-service-catalog-app.md).
* Como [Implantar o aplicativo de catálogo de serviços por meio do portal do Azure](deploy-service-catalog-quickstart.md).
* Como [Criar uma interface do usuário do portal do Azure para seu aplicativo gerenciado](create-uidefinition-overview.md).
* [Exibir funcionalidades do artefato de definição](concepts-view-definition.md).
* [Funcionalidades do provedor personalizado do Azure](custom-providers-overview.md).

## <a name="user-interface-definition"></a>Definição da interface do usuário

Neste tutorial, você criará um aplicativo gerenciado e o grupo de recursos gerenciados dele conterá a instância do provedor personalizado, a conta de armazenamento e a função. A função do Azure usada neste exemplo implementa uma API que trata operações de provedor personalizadas para ações e recursos. A conta de armazenamento do Azure é usada como armazenamento básico para os recursos do provedor personalizado.

A definição da interface do usuário para criar uma instância de aplicativo gerenciado inclui os elementos de entrada `funcname` e `storagename`. O nome da conta de armazenamento e o nome da função devem ser globalmente exclusivos. Por padrão, os arquivos de função serão implantados do [pacote de funções de amostra](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip), mas você pode alterar isso adicionando um elemento de entrada para um link de pacote em *createUIDefinition.json*:

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

e uma saída em *createUIDefinition.json*:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

A amostra *createUIDefinition.json* completa pode ser encontrada na [Referência: artefatos de elementos da interface do usuário](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Modelo com provedor personalizado

Para criar uma instância de aplicativo gerenciado com provedor personalizado, você precisa definir um recurso de provedor personalizado com nome **público** e digitar **Microsoft.CustomProviders/resourceProviders** em seu **mainTemplate.json**. Nesse recurso, você define os tipos de recursos e as ações para o serviço. Para implantar instâncias da função do Azure e da conta de armazenamento do Azure, defina recursos do tipo `Microsoft.Web/sites` e `Microsoft.Storage/storageAccounts`, respectivamente.

Neste tutorial, você criará um tipo de recurso `users`, a ação personalizada `ping` e a ação personalizada `users/contextAction` que será executada no contexto de um recurso personalizado `users`. Para cada tipo de recurso e ação, forneça um ponto de extremidade que aponte para a função com o nome fornecido em [createUIDefinition.json](#user-interface-definition). Especifique o **routingType** como `Proxy,Cache` para os tipos de recurso e como `Proxy` para ações:

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

A amostra *mainTemplate.json* completa pode ser encontrada na [Referência: artefato do modelo de implantação](reference-main-template-artifact.md).

## <a name="view-definition-artifact"></a>Exibir artefato de definição

Para definir a interface do usuário que inclui ações personalizadas e recursos personalizados em seu aplicativo gerenciado, você precisa criar o artefato **viewDefinition.json**. Para obter mais informações sobre o artefato de definição de exibição, consulte [Exibir artefato de definição em Aplicativos Gerenciados do Azure](concepts-view-definition.md).

Neste tutorial, você definirá:
* Uma página de *Visão geral* com botão de barra de ferramentas que representa uma ação personalizada `TestAction` com entrada de texto básico.
* Uma página de *Usuários* que representa um tipo de recurso personalizado `users`.
* Uma ação de recurso personalizada `users/contextAction` na página de *Usuários* que será executada no contexto de recurso personalizado do tipo `users`.

O exemplo a seguir mostra a configuração de exibição para uma página de "Visão geral":

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

O exemplo abaixo inclui a configuração da página de recursos de "Usuários" com ação de recurso personalizado:

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

A amostra *viewDefinition.json* completa pode ser encontrada na [Referência: exibir artefato de definição](reference-view-definition-artifact.md).

## <a name="managed-application-definition"></a>Definição do aplicativo gerenciado

Empacote os seguintes artefatos de aplicativo gerenciado em um arquivo morto zip e carregue-o no armazenamento:

* createUiDefinition.json
* mainTemplate.json
* viewDefinition.json

Todos os arquivos devem estar no nível da raiz. O pacote com artefatos pode ser armazenado em qualquer armazenamento, por exemplo, blob do GitHub ou blob da conta de armazenamento do Azure. Eis um script para carregar o pacote de aplicativos na conta de armazenamento: 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

Execute o script da CLI do Azure abaixo ou siga as etapas no portal do Azure para implantar uma definição de aplicativo gerenciado do catálogo de serviços:

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

# <a name="azure-clitabazurecli-interactive"></a>[CLI do Azure](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite e selecione **Centro de Aplicativos Gerenciados**.
2. No **Centro de Aplicativos Gerenciados**, escolha **definição de aplicativo do catálogo de serviços** e clique em **Adicionar**. 
    
    ![Adicionar catálogo de serviços](./media/managed-application-with-custom-providers/service-catalog-managed-application.png)

3. Fornecer valores para criar uma definição de catálogo de serviços:

    * Forneça um **Nome** exclusivo para a definição do catálogo de serviços, o **Nome de exibição** e a *Descrição* (opcional).
    * Selecione a **Assinatura**, o **Grupo de recursos** e o **Local** em que a definição de aplicativo será criada. Você pode usar o mesmo grupo de recursos que é usado para o pacote zip ou criar um grupo de recursos.
    * Para o **URI de um Arquivo de Pacote**, forneça o caminho para o arquivo zip que você criou nas etapas anteriores.

    ![Forneça os valores](./media/managed-application-with-custom-providers/add-service-catalog-managed-application.png)

4. Quando chegar até a seção de Autenticação e Nível de Bloqueio, selecione **Adicionar Autorização**.

    ![Adicionar autorização](./media/managed-application-with-custom-providers/add-authorization.png)

5. Selecione um grupo do Azure Active Directory para gerenciar os recursos e selecione **OK**.

   ![Adicionar grupo de autorização](./media/managed-application-with-custom-providers/add-auth-group.png)

6. Quando tiver fornecido todos os valores, selecione **Criar**.

   ![Criar a definição de aplicativo gerenciado](./media/managed-application-with-custom-providers/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Instância do aplicativo gerenciado

Quando a definição do aplicativo gerenciado for implantada, execute o script abaixo ou siga as etapas no portal do Azure para implantar a instância do seu aplicativo gerenciado com provedor personalizado:

# <a name="azure-clitabazurecli-interactive"></a>[CLI do Azure](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite e selecione **Centro de Aplicativos Gerenciados**.
2. No **Centro de Aplicativos Gerenciados**, escolha **aplicativos do catálogo de serviços** e clique em **Adicionar**. 

    ![Adicionar aplicativo gerenciado](./media/managed-application-with-custom-providers/add-managed-application.png)

3. Na página **aplicativos do catálogo de serviços**, digite o nome de exibição da definição do catálogo de serviços na caixa de pesquisa. Selecione a definição criada na etapa anterior e clique em **Criar**.

    ![Selecionar catálogo de serviços](./media/managed-application-with-custom-providers/select-service-catalog-definition.png)

4. Forneça valores para criar uma instância de aplicativo gerenciado da definição do catálogo de serviços:

    * Selecione a **Assinatura**, o **Grupo de recursos** e o **Local** em que a instância do aplicativo será criada.
    * Forneça nomes exclusivos para a função do Azure e a conta de armazenamento do Azure.

    ![Configurações do aplicativo](./media/managed-application-with-custom-providers/application-settings.png)

5. Quando a validação for aprovada, clique em **OK** para implantar uma instância de um aplicativo gerenciado. 
    
    ![Implantar aplicativo gerenciado](./media/managed-application-with-custom-providers/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Ações e recursos personalizados

Depois que a instância do aplicativo do catálogo de serviços tiver sido implantada, você terá dois novos grupos de recursos. O primeiro grupo de recursos, `applicationGroup`, contém uma instância do aplicativo gerenciado; o segundo grupo de recursos, `managedResourceGroup`, mantém os recursos do aplicativo gerenciado, incluindo o **provedor personalizado**.

![Grupos de recursos do aplicativo](./media/managed-application-with-custom-providers/application-resource-groups.png)

Você pode ir para a instância do aplicativo gerenciado e executar a **ação personalizada** na página de "Visão geral", criar o recurso personalizado **usuários** na página de "Usuários" e executar a **ação de contexto personalizado** no recurso personalizado.

* Vá para a página de "Visão geral" e clique no botão "Ação de ping":

![Executar ação personalizada](./media/managed-application-with-custom-providers/perform-custom-action.png)

* Vá para a página de "Usuários" e clique no botão "Adicionar". Forneça entradas para criar um recurso e enviar o formulário:

![Criar um recurso personalizado](./media/managed-application-with-custom-providers/create-custom-resource.png)

* Vá para a página de "Usuários", selecione um recurso "usuários" e clique em "Ação de contexto personalizado":

![Criar um recurso personalizado](./media/managed-application-with-custom-providers/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Procurando ajuda

Se você tiver dúvidas sobre os Aplicativos Gerenciados do Azure, tente perguntar no [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-managedapps). Uma pergunta semelhante pode já ter sido feita e respondida. Portanto, verifique primeiro antes de postar. Adicione a marca `azure-managedapps` para obter uma resposta rápida.

## <a name="next-steps"></a>Próximas etapas

Para publicar o aplicativo gerenciado no Azure Marketplace, veja [Aplicativos Gerenciados do Azure no Marketplace](publish-marketplace-app.md).
