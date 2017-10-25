---
title: Gerenciar recursos com a CLI do Azure | Microsoft Docs
description: Use a Interface de linha de comando (CLI) do Azure para gerenciar recursos e grupos do Azure
editor: 
manager: timlt
documentationcenter: 
author: tfitzmac
services: azure-resource-manager
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: c68f2a8b6e18dc2d51d8bbb5cd05bc037dc2fadb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Use a CLI do Azure para gerenciar recursos e grupos de recursos do Azure

Neste artigo, você aprenderá a gerenciar suas soluções com o CLI do Azure e o Azure Resource Manager. Se você não estiver familiarizado com o Resource Manager, veja [Visão geral do Resource Manager](resource-group-overview.md). O foco deste artigo são as tarefas de gerenciamento. Você vai:

1. Criar um grupos de recursos
2. Adicionar um recurso ao grupo de recursos
3. Adicionar uma marca ao recurso
4. Recursos de consulta baseados em nomes ou em valores de marca
5. Aplicar e remover um bloqueio no recurso
6. Excluir um grupo de recursos

Este artigo mostra como implantar um modelo do Resource Manager à sua assinatura. Para obter essas informações, consulte [Implantar recursos com modelos do Resource Manager e a CLI do Azure](resource-group-template-deploy-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e usar a CLI localmente, consulte [Instalar a CLI do Azure 2.0](/cli/azure/install-azure-cli).

## <a name="set-subscription"></a>Configurar assinatura

Se você tiver mais de uma assinatura, você pode alternar para uma assinatura diferente. Primeiro, vamos ver todas as assinaturas para sua conta.

```azurecli-interactive
az account list
```

Retorna uma lista de assinaturas habilitadas e desabilitadas.

```json
[
  {
    "cloudName": "AzureCloud",
    "id": "<guid>",
    "isDefault": true,
    "name": "Example Subscription One",
    "registeredProviders": [],
    "state": "Enabled",
    "tenantId": "<guid>",
    "user": {
      "name": "example@contoso.org",
      "type": "user"
    }
  },
  ...
]
```

Observe que uma assinatura está marcada como padrão. Essa assinatura é o contexto atual para operações. Para alternar para uma assinatura diferente, forneça o nome da assinatura com o comando **az account set**.

```azurecli-interactive
az account set -s "Example Subscription Two"
```

Para mostrar o contexto da assinatura atual, use **az account show** sem um parâmetro:

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de implantar os recursos em sua assinatura, você deve criar um grupo de recursos que conterá os recursos.

Para criar um grupo de recursos, use o comando **az group create**. O comando usa o parâmetro **name** para especificar um nome para o grupo de recursos e o parâmetro **location** para especificar o local.

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

A saída está neste formato:

```json
{
  "id": "/subscriptions/<subscription-id>/resourceGroups/TestRG1",
  "location": "southcentralus",
  "managedBy": null,
  "name": "TestRG1",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Se você precisar recuperar o grupo de recursos posteriormente, use o seguinte comando:

```azurecli-interactive
az group show --name TestRG1
```

Para obter todos os grupos de recursos na sua assinatura, use:

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>Adicionar recursos a um grupo de recursos

Para adicionar um recurso ao grupo de recursos, é possível usar o comando **az resource create** ou um comando específico para o tipo de recurso que você está criando (como **az storage account create**). Talvez seja mais fácil de usar um comando específico para um tipo de recurso, porque ele inclui parâmetros para as propriedades necessárias para o novo recurso. Para usar o **az resource create**, é necessário conhecer todas as propriedades a serem definidas sem ser solicitado para eles.

No entanto, adicionar um recurso por meio de um script pode causar uma confusão no futuro, pois o novo recurso não existe em um modelo do Resource Manager. Modelos permitem repetidamente e confiável implantar sua solução.

O comando a seguir cria uma conta de armazenamento. Em vez de usar o nome mostrado no exemplo, forneça um nome exclusivo para a conta de armazenamento. O nome deve ter entre 3 e 24 caracteres de comprimento e usar somente números e letras minúsculas. Se você usar o nome mostrado no exemplo, você recebe um erro porque esse nome já está em uso.

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

Se você precisar recuperar esse recurso posteriormente, use o seguinte comando:

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>Adicione uma marca

Marcas permitem que você organize seus recursos de acordo com propriedades diferentes. Por exemplo, você pode ter vários recursos em diferentes grupos de recursos que pertencem ao mesmo departamento. Você pode aplicar uma marca de departamento e o valor desses recursos para marcá-los como pertencentes à mesma categoria. Ou, você pode marcar se um recurso é usado em um ambiente de produção ou de teste. Neste artigo, você aplica marcas a apenas um recurso, mas em seu ambiente provavelmente faz mais sentido aplicar marcas a todos eles.

O comando a seguir aplica duas marcas à sua conta de armazenamento:

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

Marcas são atualizadas como um único objeto. Para adicionar uma marca a um recurso que já inclui marcas, primeiro recupere as marcas existentes. Adicionar nova marca para o objeto que contém as marcas existentes e reaplicar todas as marcas para o recurso.

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>Pesquisa de recursos

Use o comando **az resource list** para recuperar recursos para critérios de pesquisa diferentes.

* Para obter um recurso por nome, forneça o parâmetro **name**:

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* Para obter todos os recursos em um grupo de recursos, forneça o parâmetro **resource-group**:

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* Para obter todos os recursos com um valor e nome de marca, forneça o parâmetro **tag**:

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* Para todos os recursos com um tipo de recurso específico, forneça o parâmetro **resource-type**:

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="get-resource-id"></a>Obter ID de recurso

Muitos comandos usam uma ID de recurso como parâmetro. Para obter a ID de um recurso e armazená-la em uma variável, use:

```azurecli-interactive
webappID=$(az resource show -g exampleGroup -n exampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
```

## <a name="lock-a-resource"></a>Um recurso de bloqueio

Quando você precisa certificar-se de um recurso crítico for acidentalmente excluído ou modificado, aplica um bloqueio no recurso. Você pode especificar um **CanNotDelete** ou **ReadOnly**.

Para criar ou excluir bloqueios de gerenciamento, você deve ter acesso às ações `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*`. Das funções internas, somente Proprietário e Administrador do Acesso de Usuário recebem essas ações.

Para aplicar um bloqueio, use o seguinte comando:

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

O recurso bloqueado no exemplo anterior não pode ser excluído até que o bloqueio seja removido. Para remover um bloqueio, use:

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

Para saber mais sobre bloqueios de configuração, confira [Bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Remover recursos ou grupo de recursos
Você pode remover um recurso ou grupo de recursos. Ao remover um grupo de recursos, você também removerá todos os recursos dentro daquele grupo de recursos.

* Para excluir um recurso do grupo de recursos, use o comando de exclusão para o tipo de recurso que você está excluindo. Esse comando exclui o recurso, mas não exclui o grupo de recursos.

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* Para excluir um grupo de recursos e todos os seus recursos, use o comando **az group delete**.

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

Para ambos os comandos, é necessário confirmar que você deseja remover o recurso ou grupo de recursos.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a criação de modelos do Gerenciador de Recursos, consulte [Criando Modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para saber mais sobre como implantar modelos, consulte [Implantar um aplicativo com o Modelo do Azure Resource Manager](resource-group-template-deploy-cli.md).
* Você pode mover os recursos existentes para um novo grupo de recursos. Para obter exemplos, confira [Mover recursos para um novo grupo de recursos ou uma nova assinatura](resource-group-move-resources.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).