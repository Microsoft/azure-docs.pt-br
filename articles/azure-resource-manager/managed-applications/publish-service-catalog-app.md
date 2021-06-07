---
title: Publicar um aplicativo gerenciado do catálogo de serviços
description: Mostra como criar um aplicativo gerenciado do Azure destinado aos membros de sua organização.
author: tfitzmac
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 04/14/2020
ms.author: tomfitz
ms.openlocfilehash: b255cafb9040f87c902fe6c094c3e0db3d461e4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093635"
---
# <a name="quickstart-create-and-publish-a-managed-application-definition"></a>Início Rápido: Criar e publicar uma definição de aplicativo gerenciado

Este guia de início rápido fornece uma introdução para trabalhar com [Aplicativos Gerenciados do Azure](overview.md). Crie e publique um aplicativo gerenciado destinado aos membros de sua organização.

Para publicar um aplicativo gerenciado no catálogo de serviços, você precisa:

* Crie um modelo que define os recursos para implantar com o aplicativo gerenciado.
* Defina os elementos da interface do usuário para o portal ao implantar o aplicativo gerenciado.
* Crie um pacote _.zip_ que contenha os arquivos de modelo necessários.
* Decidir qual usuário, grupo ou aplicativo precisa de acesso ao grupo de recursos na assinatura do usuário.
* Crie a definição de aplicativo gerenciado que aponta para o pacote _.zip_ e solicita o acesso à identidade.

## <a name="create-the-arm-template"></a>Criar um modelo do ARM

Cada definição de aplicativo gerenciado contém um arquivo chamado _mainTemplate.json_. Nele, você pode definir os recursos do Azure que serão implantados. O modelo não é diferente de um modelo do ARM normal.

Crie um arquivo chamado _mainTemplate.json_. O nome diferencia maiúsculas de minúsculas.

Adicione o seguinte JSON ao seu arquivo. Ele define os parâmetros para a criação de uma conta de armazenamento e especifica as propriedades dela.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountNamePrefix": {
      "type": "string"
    },
    "storageAccountType": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageEndpoint": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
    }
  }
}
```

Salve o arquivo _mainTemplate.json_.

## <a name="define-your-create-experience"></a>Definir a experiência de criação

Como editor, você define a experiência do portal para criar o aplicativo gerenciado. O arquivo _createUiDefinition.json_ gera a interface do portal. Você define como os usuários fornecem a entrada para cada parâmetro usando [elementos de controle](create-uidefinition-elements.md), incluindo menus suspensos, caixas de texto e caixas de senha.

Criar um arquivo chamado _createUiDefinition.json_ (esse nome diferencia maiúsculas de minúsculas)

Adicione o JSON inicial a seguir ao arquivo e salve-o.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "storageConfig",
        "label": "Storage settings",
        "subLabel": {
          "preValidation": "Configure the infrastructure settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Storage settings",
        "elements": [
          {
            "name": "storageAccounts",
            "type": "Microsoft.Storage.MultiStorageAccountCombo",
            "label": {
              "prefix": "Storage account name prefix",
              "type": "Storage account type"
            },
            "defaultValue": {
              "type": "Standard_LRS"
            },
            "constraints": {
              "allowedTypes": [
                "Premium_LRS",
                "Standard_LRS",
                "Standard_GRS"
              ]
            }
          }
        ]
      }
    ],
    "outputs": {
      "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
      "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
      "location": "[location()]"
    }
  }
}
```

Para saber mais, consulte [Introdução a CreateUiDefinition](create-uidefinition-overview.md).

## <a name="package-the-files"></a>Empacote os arquivos

Adicione os dois arquivos em um arquivo _.zip_ chamado _app.zip_. Os dois arquivos precisam estar no nível raiz do arquivo _.zip_. Se você colocá-los em uma pasta, receberá um erro ao criar a definição de aplicativo gerenciado que indica que os arquivos necessários não estão presentes.

Carregue o pacote em um local acessível no qual ele pode ser consumido. Você precisará fornecer um nome exclusivo para a conta de armazenamento.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name storageGroup -Location eastus

$storageAccount = New-AzStorageAccount `
  -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent `
  -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name storageGroup --location eastus

az storage account create \
    --name mystorageaccount \
    --resource-group storageGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create \
    --account-name mystorageaccount \
    --name appcontainer \
    --public-access blob

az storage blob upload \
    --account-name mystorageaccount \
    --container-name appcontainer \
    --name "app.zip" \
    --file "D:\myapplications\app.zip"

```

---

## <a name="create-the-managed-application-definition"></a>Criar a definição de aplicativo gerenciado

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Criar um grupo de usuários ou aplicativo do Azure Active Directory

A próxima etapa é selecionar um grupo de usuários, um usuário ou um aplicativo a fim de gerenciar os recursos para o cliente. Essa identidade tem permissões no grupo de recursos gerenciado de acordo com a função atribuída. A função pode ser qualquer função interna do Azure, como Proprietário ou Colaborador. Para criar um novo grupo de usuários do Active Directory, consulte [Criar um grupo e adicionar membros no Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

É necessário usar a ID de objeto do grupo de usuários para gerenciar os recursos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
groupid=$(az ad group show --group mygroup --query objectId --output tsv)
```

---

### <a name="get-the-role-definition-id"></a>Obter a ID de definição da função

Em seguida, você precisará da ID de definição de função da função interna do Azure para permitir acesso ao usuário, ao grupo de usuários ou ao aplicativo. Normalmente, você usa a função Proprietário, Colaborador ou Leitor. O comando a seguir mostra como obter a ID de definição de função para a função Proprietário:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
ownerid=$(az role definition list --name Owner --query [].name --output tsv)
```

---

### <a name="create-the-managed-application-definition"></a>Criar a definição de aplicativo gerenciado

Se você ainda não tiver um grupo de recursos para armazenar a definição de aplicativo gerenciado, crie um agora:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

---

Agora, crie o recurso de definição de aplicativo gerenciado.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
blob=$(az storage blob url --account-name mystorageaccount --container-name appcontainer --name app.zip --output tsv)

az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$groupid:$ownerid" \
  --package-file-uri "$blob"
```

---

Quando o comando for concluído, você tem uma definição de aplicativo gerenciado no seu grupo de recursos.

Alguns dos parâmetros usados no exemplo anterior são:

* **grupo de recursos**: o nome do grupo de recursos no qual a definição de aplicativo gerenciado é criada.
* **nível de bloqueio**: o tipo de bloqueio colocado no grupo de recursos gerenciados. Ela impede que o cliente execute operações indesejáveis no grupo de recursos. Atualmente, ReadOnly é o único nível de bloqueio com suporte. Quando ReadOnly é especificado, o cliente pode ler somente os recursos presentes no grupo de recursos gerenciados. As identidades do publicador que concedem acesso ao grupo de recursos gerenciado são isentas de bloqueio.
* **authorizations**: descreve a ID da entidade e a ID de definição de função que são usadas para conceder permissão ao grupo de recursos gerenciados. Ele é especificado no formato `<principalId>:<roleDefinitionId>`. Se for necessário mais de um valor, especifique-os no formato `<principalId1>:<roleDefinitionId1>,<principalId2>:<roleDefinitionId2>`. Os valores são separados por uma vírgula.
* **URI do arquivo de pacote**: A localização de um pacote _.zip_ que contém os arquivos necessários.

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>Traga seu próprio armazenamento para a definição de aplicativo gerenciado

É possível optar por armazenar a definição de aplicativo gerenciado em uma conta de armazenamento fornecida por você durante a criação, para que a localização dessa definição e o acesso a ela possam ser totalmente gerenciados por você para suas necessidades regulatórias.

> [!NOTE]
> O modelo "traga seu próprio armazenamento" só é compatível com as implantações de modelo do ARM ou da API REST da definição de aplicativo gerenciado.

### <a name="select-your-storage-account"></a>Selecione sua conta de armazenamento

Você deve [criar uma conta de armazenamento](../../storage/common/storage-account-create.md) para conter a definição de aplicativo gerenciado para uso com o Catálogo de Serviços.

Copie a ID do recurso da conta de armazenamento. Ela será usada posteriormente ao implantar a definição.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>Defina a atribuição de função para "Provedor de Recursos de Dispositivo" em sua conta de armazenamento

Antes que a definição de aplicativo gerenciado possa ser implantada em sua conta de armazenamento, você deverá fornecer permissões de colaborador para a função de **Provedor de Recursos do Dispositivo** para que ela possa gravar os arquivos de definição no contêiner da sua conta de armazenamento.

1. No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento.
1. Selecione **Controle de Acesso (IAM)** para exibir as configurações de controle de acesso da conta de armazenamento. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.
1. Na janela **Adicionar atribuição de função**, selecione a função **Colaborador**.
1. No campo **Atribuir acesso a**, selecione **usuário, grupo ou entidade de serviço do Azure AD**.
1. Em **Selecionar**, pesquise a função **Provedor de Recursos do Dispositivo** e selecione-a.
1. Salve a atribuição de função.

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>Implantar a definição de aplicativo gerenciado com um modelo do ARM

Use o seguinte modelo do ARM para implantar o aplicativo gerenciado empacotado como uma nova definição de aplicativo gerenciado no Catálogo de Serviços cujos arquivos de definição são armazenados e mantidos na sua conta de armazenamento:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "applicationName": {
      "type": "string",
      "metadata": {
        "description": "Managed Application name"
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "definitionStorageResourceID": {
      "type": "string",
      "metadata": {
        "description": "Storage account resource ID for where you're storing your definition"
      }
    },
    "_artifactsLocation": {
      "type": "string",
      "metadata": {
        "description": "The base URI where artifacts required by this template are located."
      }
    }
  },
  "variables": {
    "lockLevel": "None",
    "description": "Sample Managed application definition",
    "displayName": "Sample Managed application definition",
    "managedApplicationDefinitionName": "[parameters('applicationName')]",
    "packageFileUri": "[parameters('_artifactsLocation')]",
    "defLocation": "[parameters('definitionStorageResourceID')]",
    "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
    "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Solutions/applicationDefinitions",
      "apiVersion": "2020-08-21-preview",
      "name": "[variables('managedApplicationDefinitionName')]",
      "location": "[parameters('location')]",
      "properties": {
        "lockLevel": "[variables('lockLevel')]",
        "description": "[variables('description')]",
        "displayName": "[variables('displayName')]",
        "packageFileUri": "[variables('packageFileUri')]",
        "storageAccountId": "[variables('defLocation')]"
      }
    }
  ],
  "outputs": {}
}
```

Adicionamos uma nova propriedade chamada `storageAccountId` às suas propriedades `applicationDefinitions` e fornecemos a ID da conta de armazenamento na qual você deseja armazenar a sua definição como o valor dessa propriedade:

Você pode verificar se os arquivos de definição de aplicativo estão salvos na sua conta de armazenamento fornecida em um contêiner intitulado `applicationDefinitions`.

> [!NOTE]
> Para aumentar a segurança, você pode criar uma definição de aplicativos gerenciados armazenando-a em um [blob de conta de Armazenamento do Azure em que a criptografia está habilitada](../../storage/common/storage-service-encryption.md). O conteúdo da definição é criptografado por meio das opções de criptografia da conta de armazenamento. Somente os usuários com permissões para o arquivo podem ver a definição no Catálogo de Serviços.

## <a name="make-sure-users-can-see-your-definition"></a>Verifique se os usuários podem ver sua definição

Você tem acesso à definição de aplicativo gerenciado, mas você deve certificar-se de que outros usuários na sua organização podem acessá-lo. Conceda a eles pelo menos a função de Leitor para a definição. Eles podem ter herdado esse nível de acesso da assinatura ou grupo de recursos. Para verificar quem tem acesso à definição e adicionar usuários ou grupos, confira [Atribuir funções do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Próximas etapas

Você publicou a definição de aplicativo gerenciado. Agora, saiba como implantar uma instância dessa definição.

> [!div class="nextstepaction"]
> [Início Rápido: Implantar aplicativo do catálogo de serviços](deploy-service-catalog-quickstart.md)
