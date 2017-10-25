---
title: "Criar e publicar um aplicativo gerenciado do catálogo de serviços do Azure | Microsoft Docs"
description: "Mostra como criar um aplicativo gerenciado do Azure destinado aos membros de sua organização."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/09/2017
ms.author: gauravbh
ms.openlocfilehash: 3ff2108d08bacc4bc5f79a768b9c131aa7e6fceb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Publicar um aplicativo gerenciado para consumo interno

Crie e publique [aplicativos gerenciados](managed-application-overview.md) do Azure destinados aos membros de sua organização. Por exemplo, um departamento de TI pode publicar aplicativos gerenciados que garantem a conformidade com os padrões organizacionais. Esses aplicativos gerenciados estão disponíveis por meio do catálogo de serviços, não pelo Azure Marketplace.

Para publicar um aplicativo gerenciado do catálogo de serviços, você deve:

* Criar um pacote .zip que contém os dois arquivos de modelo necessários.
* Decidir qual usuário, grupo ou aplicativo precisa de acesso ao grupo de recursos na assinatura do usuário.
* Criar a definição de aplicativo gerenciado que aponta para o pacote .zip e solicita o acesso à identidade.

## <a name="create-a-managed-application-package"></a>Criar um pacote de aplicativos gerenciados

A primeira etapa é criar os dois arquivos de modelo necessários. Empacote todos os dois arquivos em um arquivo .zip e carregue-o em um local acessível, como uma conta de armazenamento. Passe um link para esse arquivo .zip ao criar a definição de aplicativo gerenciado.

* **mainTemplate.json**: esse arquivo define os recursos do Azure que são provisionados como parte do aplicativo gerenciado. O modelo não é diferente de um modelo normal do Resource Manager. Por exemplo, para criar uma conta de armazenamento por meio de um aplicativo gerenciado, o mainTemplate.json contém:

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]",
            "apiVersion": "2016-01-01",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {}
  }
  ```

* **createUiDefinition.json**: o portal do Azure usa esse arquivo para gerar a interface do usuário para os usuários que criam o aplicativo gerenciado. Você define como os usuários fornecem a entrada para cada parâmetro. Você pode usar opções como uma lista suspensa, caixa de texto, caixa de senha e outras ferramentas de entrada. Para saber como criar um arquivo de definição de interface do usuário para um aplicativo gerenciado, consulte [Introdução a CreateUiDefinition](managed-application-createuidefinition-overview.md).

  O exemplo a seguir mostra um arquivo createUiDefinition.json que permite aos usuários especificar o prefixo de nome da conta de armazenamento por meio de uma caixa de texto.

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {
                "name": "storageAccounts",
                "type": "Microsoft.Common.TextBox",
                "label": "Storage account name prefix",
                "defaultValue": "storage",
                "toolTip": "Provide a value that is used for the prefix of your storage account. Limit to 11 characters.",
                "constraints": {
                    "required": true,
                    "regex": "^[a-z0-9A-Z]{1,11}$",
                    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-11 characters long."
                },
                "visible": true
            }
        ],
        "steps": [],
        "outputs": {
            "storageAccountNamePrefix": "[basics('storageAccounts')]"
        }
    }
  }
  ```

Depois que todos os arquivos necessários estiverem prontos, empacote-os como um arquivo .zip. Os dois arquivos devem estar no nível raiz do arquivo .zip. Se você colocá-los em uma pasta, receberá um erro ao criar a definição de aplicativo gerenciado que indica que os arquivos necessários não estão presentes. Carregue o pacote em um local acessível no qual ele pode ser consumido. O restante deste artigo pressupõe que o arquivo .zip exista em um contêiner de blobs de armazenamento publicamente acessível.

Você pode usar a CLI ou o portal do Azure para criar um aplicativo gerenciado para catálogo de serviços. As duas abordagens são mostradas neste artigo.

## <a name="create-managed-application-with-azure-cli"></a>Criar aplicativo gerenciado com a CLI do Azure

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Criar um grupo de usuários ou aplicativo do Azure Active Directory

A próxima etapa é selecionar um grupo de usuários ou um aplicativo para gerenciar os recursos em nome do cliente. Esse grupo de usuários ou aplicativo tem permissões no grupo de recursos gerenciados de acordo com a função atribuída. A função pode ser qualquer função interna de RBAC (Controle de acesso baseado em função) como Proprietário ou Colaborador. Também é possível conceder a um usuário individual permissões para gerenciar os recursos, mas, normalmente, você atribui essa permissão a um grupo de usuários. Para criar um novo grupo de usuários do Active Directory, consulte [Criar um grupo e adicionar membros no Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

É necessário usar a ID de objeto do grupo de usuários para gerenciar os recursos. O seguinte exemplo mostra como obter a ID de objeto do nome de exibição do grupo:

```azurecli-interactive
az ad group show --group exampleGroupName
```

O comando de exemplo retorna a seguinte saída:

```azurecli
{
    "displayName": "exampleGroupName",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```

Para recuperar apenas a ID de objeto, use:

```azurecli-interactive
groupid=$(az ad group show --group exampleGroupName --query objectId --output tsv)
```

### <a name="get-the-role-definition-id"></a>Obter a ID de definição da função

Em seguida, é necessário o ID de definição de função da função RBAC interna para conceder acesso ao usuário, grupo de usuários ou aplicativo. Normalmente, você usa a função Proprietário, Colaborador ou Leitor. O comando a seguir mostra como obter a ID de definição de função para a função Proprietário:

```azurecli-interactive
az role definition list --name owner
```

O comando retorna a seguinte saída:

```azurecli
{
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "properties": {
      "assignableScopes": [
        "/"
      ],
      "description": "Lets you manage everything, including access to resources.",
      "permissions": [
        {
          "actions": [
            "*"
         ],
         "notActions": []
        }
      ],
      "roleName": "Owner",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
}
```

Você precisa do valor da propriedade "name" do exemplo anterior. Recupere apenas essa propriedade com:

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

### <a name="create-the-managed-application-definition"></a>Criar a definição de aplicativo gerenciado

Se você ainda não tiver um grupo de recursos para armazenar a definição de aplicativo gerenciado, crie um agora:

```azurecli-interactive
az group create --name managedApplicationGroup --location westcentralus
```

Agora, crie o recurso de definição de aplicativo gerenciado.

```azurecli-interactive
az managedapp definition create \
  --name storageApp \
  --location "westcentralus" \
  --resource-group managedApplicationGroup \
  --lock-level ReadOnly \
  --display-name myteststorageapp \
  --description storageapp \
  --authorizations "$groupid:$roleid" \
  --package-file-uri <uri-path-to-zip-file>
```

Os parâmetros usados no exemplo anterior são:

* **resource-group**: o nome do grupo de recursos no qual a definição de aplicativo gerenciado é criada.
* **lock-level**: o tipo de bloqueio colocado no grupo de recursos gerenciado. Ela impede que o cliente execute operações indesejáveis no grupo de recursos. Atualmente, ReadOnly é o único nível de bloqueio com suporte. Quando ReadOnly é especificado, o cliente pode ler somente os recursos presentes no grupo de recursos gerenciados.
* **authorizations**: descreve a ID da entidade e a ID de definição de função que são usadas para conceder permissão ao grupo de recursos gerenciado. Ele é especificado no formato `<principalId>:<roleDefinitionId>`. Vários valores também podem ser especificados para essa propriedade. Se houver a necessidade de vários valores, eles deverão ser especificados no formulário `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Vários valores são separados por um espaço.
* **package-file-uri**: o local do pacote de aplicativos gerenciados que contém os arquivos de modelo, que podem ser um Azure Storage Blob.

## <a name="create-managed-application-with-portal"></a>Criar aplicativo gerenciado com o portal

1. Se necessário, crie um grupo de usuários do Azure Active Directory para gerenciar os recursos. Para obter mais informações, consulte [Criar um grupo e adicionar membros no Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).
1. No canto superior esquerdo, selecione **+Novo**.

   ![Novo serviço](./media/managed-application-publishing/new.png)

1. Pesquise o **catálogo de serviços**.

1. Nos resultados, role até encontrar a **Definição de Aplicativo gerenciado do Catálogo de Serviços**. Selecione-o.

   ![Pesquisar por definições de aplicativo gerenciado](./media/managed-application-publishing/select-managed-apps-definition.png)

1. Selecione **Criar** para iniciar o processo de criação da definição de aplicativo gerenciado.

   ![Criar a definição de aplicativo gerenciado](./media/managed-application-publishing/create-definition.png)

1. Forneça valores para o nome, o nome de exibição, a descrição, o local, a assinatura e o grupo de recursos. Para o URI do arquivo de pacote, forneça o caminho para o arquivo zip que você criou.

   ![Forneça os valores](./media/managed-application-publishing/fill-application-values.png)

1. Quando chegar até a seção de Autenticação e Nível de Bloqueio, selecione **Adicionar Autorização**.

   ![Adicionar autorização](./media/managed-application-publishing/add-authorization.png)

1. Selecione um grupo do Azure Active Directory para gerenciar os recursos e selecione **OK**.

   ![Adicionar grupo de autorização](./media/managed-application-publishing/add-auth-group.png)

1. Quando tiver fornecido todos os valores, selecione **Criar**.

   ![Criar aplicativo gerenciado](./media/managed-application-publishing/create-app.png)

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados](managed-application-overview.md).
* Para obter exemplos dos arquivos, consulte [Exemplos de aplicativo gerenciado](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).
* Para obter informações sobre como consumir um aplicativo gerenciado do Catálogo de Serviços, consulte [Consumir um aplicativo gerenciado do Catálogo de Serviços](managed-application-consumption.md).
* Para obter informações sobre como publicar aplicativos gerenciados para o Azure Marketplace, consulte [Aplicativos gerenciados do Azure no Marketplace](managed-application-author-marketplace.md).
* Para obter informações sobre como consumir um aplicativo gerenciado do Marketplace, consulte [Consumir aplicativos gerenciados pelo Azure no Marketplace](managed-application-consume-marketplace.md).
* Para saber como criar um arquivo de definição de interface do usuário para um aplicativo gerenciado, consulte [Introdução a CreateUiDefinition](managed-application-createuidefinition-overview.md).
