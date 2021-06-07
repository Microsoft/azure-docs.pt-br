---
title: Criar um painel do portal do Azure com a CLI do Azure
description: 'Início Rápido: Saiba como criar um painel no portal do Azure usando a CLI do Azure. Um painel é uma exibição concentrada e organizada dos seus recursos da nuvem.'
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.date: 12/4/2020
ms.openlocfilehash: d951c692c7d3c282ae68c5f9b53e9cda5407df10
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481014"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>Início Rápido: Criar um painel do portal do Azure com a CLI do Azure

Um painel no portal do Azure é uma exibição concentrada e organizada dos seus recursos da nuvem. O foco deste artigo é o processo de usar a CLI do Azure para criar um painel.
O painel mostra o desempenho de uma VM (máquina virtual), bem como algumas informações e links estáticos.


[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Se tiver várias assinaturas do Azure, escolha a que for adequada para a cobrança dos recursos.
Selecione uma assinatura usando o comando [az account set](/cli/azure/account#az_account_set):

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- Crie um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) usando o comando [az group create](/cli/azure/group#az_group_create) ou use um grupo de recursos existente:

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados como um grupo.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma máquina virtual usando o comando [az vm create](/cli/azure/vm#az_vm_create):

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> A senha deve ser complexa.
> Esse é um novo nome de usuário e uma nova senha.
> Não é, por exemplo, a conta que você usa para entrar no Azure.
> Para obter mais informações, confira [Requisitos de nome de usuário](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) e [Requisitos de senha](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

A implantação é iniciada e normalmente leva alguns minutos para ser concluída.
Após a conclusão da implantação, vá para a próxima seção.

## <a name="download-the-dashboard-template"></a>Baixar o modelo de painel

Como os painéis do Azure são recursos, eles podem ser representados como JSON.
Para obter mais informações, confira [A estrutura de Painéis do Azure](./azure-portal-dashboards-structure.md).

Baixe o seguinte arquivo: [portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json).

Personalize o modelo baixado alterando os seguintes valores para seus valores:

* `<subscriptionID>`: Sua assinatura
* `<rgName>`: grupo de Recursos, por exemplo, `myResourceGroup`
* `<vmName>`: nome da máquina virtual, por exemplo, `SimpleWinVM`
* `<dashboardTitle>`: título do painel, por exemplo, `Simple VM Dashboard`
* `<location>`: sua região do Azure, por exemplo, `centralus`

Para obter mais informações, confira [Referência de modelos de painéis no portal da Microsoft](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Implantar o modelo de painel

Agora você pode implantar o modelo de dentro da CLI do Azure.

1. Execute o comando [az portal dashboard create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) para implantar o modelo:

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. Verifique se o painel foi criado com êxito executando o comando [az portal dashboard show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show):

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

Para ver todos os painéis para a assinatura atual, use [az portal dashboard list](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Você também pode ver todos os painéis de um grupo de recursos:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

Você pode atualizar um painel usando o comando [az portal dashboard update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update):

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a máquina virtual e o painel associado, exclua o grupo de recursos que os contém.

> [!CAUTION]
> O exemplo a seguir exclui o grupo de recursos especificado e todos os recursos contidos nele.
> Se existirem recursos fora do escopo deste artigo no grupo de recursos especificado, eles também serão excluídos.

```azurecli
az group delete --name myResourceGroup
```

Para remover apenas o painel, use o comando [az portal dashboard delete](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_delete):

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o suporte à CLI do Azure para painéis, confira [az portal dashboard](/cli/azure/ext/portal/portal/dashboard).
