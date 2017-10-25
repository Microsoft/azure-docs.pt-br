---
title: Consumir um aplicativo gerenciado do Azure | Microsoft Docs
description: Descreve como um cliente cria um aplicativo gerenciado do Azure a partir dos arquivos publicados.
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/23/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: ed8fbaf2a4546c8e31eeced11cd0b5627fd62c0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="consume-an-internal-managed-application"></a>Consumir um aplicativo gerenciado interno

Consuma [aplicativos gerenciados](managed-application-overview.md) do Azure destinados aos membros de sua organização. Por exemplo, selecione os aplicativos gerenciados de seu departamento de TI que garantem a conformidade com os padrões organizacionais. Esses aplicativos gerenciados estão disponíveis por meio do Catálogo de Serviços, não pelo Azure Marketplace.

Antes de continuar com este artigo, você deve ter um aplicativo gerenciado disponível no catálogo de serviços para sua assinatura. Se alguém em sua organização ainda não tiver criado um aplicativo gerenciado, consulte [Publicar um aplicativo gerenciado para consumo interno](managed-application-publishing.md).

No momento, você pode usar a CLI do Azure ou o portal do Azure para consumir um aplicativo gerenciado.

## <a name="create-the-managed-application-by-using-the-portal"></a>Criar o aplicativo gerenciado usando o portal

Para implantar um aplicativo gerenciado por meio do portal, siga estas etapas:

1. Vá para o portal do Azure. Pesquise **Aplicativo Gerenciado do Catálogo de Serviços**.

   ![Aplicativo gerenciado do Catálogo de Serviços](./media/managed-application-consumption/create-service-catalog-managed-application.png)

1. Selecione o aplicativo gerenciado que você deseja criar na lista de soluções disponíveis. Selecione **Criar**.

   ![Seleção do aplicativo gerenciado](./media/managed-application-consumption/select-offer.png)

1. Forneça valores para os parâmetros necessários para provisionar os recursos. Selecione **Centro-Oeste dos EUA** para local. Selecione **OK**.

   ![Parâmetros do aplicativo gerenciado](./media/managed-application-consumption/input-parameters.png)

1. O modelo valida os valores fornecidos. Se a validação for bem-sucedida, selecione **OK** para iniciar a implantação.

   ![Validação de aplicativo gerenciado](./media/managed-application-consumption/validation.png)

Depois que a implantação for concluída, os recursos apropriados definidos no modelo serão provisionados no grupo de recursos gerenciado que você forneceu.

## <a name="create-the-managed-application-by-using-azure-cli"></a>Criar o aplicativo gerenciado usando a CLI do Azure

Há duas maneiras de criar um aplicativo gerenciado usando a CLI do Azure:

* Use o comando para criar aplicativos gerenciados.
* Use o comando de implantação de modelo regular.

### <a name="use-the-template-deployment-command"></a>Use o comando de implantação de modelo

Implante o arquivo applianceMainTemplate.json que o fornecedor criou.

Depois crie dois grupos de recursos. O primeiro grupo de recursos é o local em que o recurso de aplicativo gerenciado é criado: Microsoft.Solutions/appliances. O segundo grupo de recursos contém todos os recursos definidos em mainTemplate.json. Esse grupo de recursos é gerenciado pelo ISV.

```azurecli
az group create --name mainResourceGroup --location westcentralus
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> Use `westcentralus` como o local do grupo de recursos.
>

Para implantar o applianceMainTemplate.json no mainResourceGroup, use o seguinte comando:

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri
```

Depois que o modelo anterior é executado, ele solicita os valores dos parâmetros que estão definidos no modelo. Além dos parâmetros que são necessários para o provisionamento de recursos em um modelo, você precisa de dois valores de parâmetro principais:

- **managedResourceGroupId**: a ID do grupo de recursos que contém os recursos definidos em applianceMainTemplate.json. A ID pertence ao formulário `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`. No exemplo anterior, é a ID do `managedResourceGroup`.
- **applianceDefinitionId**: A ID do recurso de definição de aplicativo gerenciado. Esse valor é fornecido pelo ISV.

> [!NOTE]
> O fornecedor deve conceder acesso ao grupo de recursos que contém a definição de aplicativo gerenciado. O recurso de definição é criado na assinatura do fornecedor. Portanto, um usuário, grupo de usuários ou aplicativo no locatário do cliente precisa ter acesso de leitura a esse recurso.

Depois que a implantação for concluída com êxito, veja se o aplicativo gerenciado foi criado em mainResourceGroup. O recurso storageAccount é criado em managedResourceGroup.

### <a name="use-the-create-command"></a>Use o comando de criar

Você pode usar o comando `az managedapp create` para criar um aplicativo gerenciado da definição de aplicativo gerenciado.

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus"
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401"
    --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401"
    --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401"
    --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}"
    --debug
```

* **appliance-definition-Id**: a ID do recurso da definição de aplicativo gerenciado criada na etapa anterior. Para obter essa ID, execute o seguinte comando:

  ```azurecli
  az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
  ```

  Esse comando retorna a definição de aplicativo gerenciado. Você precisa do valor da propriedade da ID.

* **managed-rg-id**: o nome do grupo de recursos que contém os recursos definidos em applianceMainTemplate.json. Esse grupo de recursos é o grupo de recursos gerenciado. Ele é gerenciado pelo editor. Se não existir, ele será criado para você.
* **resource-group**: o grupo de recursos em que o recurso de aplicativo gerenciado é criado. O recurso Microsoft.Solutions/appliance reside nesse grupo de recursos.
* **parameters**: Os parâmetros necessários para os recursos definidos em applianceMainTemplate.json.

## <a name="known-issues"></a>Problemas conhecidos

Esta versão prévia inclui os seguintes problemas:

* Um erro de servidor interno 500 é exibido durante a criação do aplicativo gerenciado. Se você tiver esse problema, é provável que ele seja intermitente. Repita a operação.
* Um novo grupo de recursos é necessário para o grupo de recursos gerenciado. Se for usado um grupo de recursos existente a implantação falha.
* O grupo de recursos que contém o recurso Microsoft.Solutions/appliances deve ser criado na localização **westcentralus**.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados](managed-application-overview.md).
* Para obter informações sobre como publicar um aplicativo gerenciado do Catálogo de Serviços, consulte [Criar e publicar um aplicativo gerenciado do Catálogo de Serviços](managed-application-publishing.md).
* Para obter informações sobre como publicar aplicativos gerenciados para o Azure Marketplace, consulte [Aplicativos gerenciados do Azure no Marketplace](managed-application-author-marketplace.md).
* Para obter informações sobre como consumir um aplicativo gerenciado do Marketplace, consulte [Consumir aplicativos gerenciados pelo Azure no Marketplace](managed-application-consume-marketplace.md).
