---
title: 'Início Rápido: Criar uma consulta compartilhada com a CLI do Azure'
description: Neste guia de início rápido, você seguirá as etapas necessárias para habilitar a extensão do Resource Graph para a CLI do Azure e criar uma consulta compartilhada.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: ec1b010771790339a13777624f04c7bd2db01f11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99594359"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>Início Rápido: Criar uma consulta compartilhada do Resource Graph usando a CLI do Azure

A primeira etapa para o uso do Azure Resource Graph com a [CLI do Azure](/cli/azure/) é verificar se a extensão está instalada. Este início rápido orienta você pelo processo de adição da extensão à instalação da CLI do Azure. Você pode usar a extensão com a CLI do Azure instalada localmente ou por meio do [Azure Cloud Shell](https://shell.azure.com).

Ao final deste processo, você terá adicionado a extensão à instalação da CLI do Azure escolhida e criado uma consulta compartilhada do Resource Graph.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Adicionar a extensão do Resource Graph

A fim de habilitar a CLI do Azure para funcionar com o Azure Resource Graph, é necessário adicionar a extensão. Essa extensão funcionará sempre que a CLI do Azure puder ser usada, incluindo no [bash no Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (tanto autônomo quanto dentro do portal), na [imagem do Docker da CLI do Azure](https://hub.docker.com/_/microsoft-azure-cli) ou instalada localmente.

1. Verifique se a CLI do Azure mais recente está instalada (pelo menos, a versão **2.8.0**). Se ainda não estiver instalado, siga [estas instruções](/cli/azure/install-azure-cli-windows).

1. No ambiente escolhido da CLI do Azure, use [az extension add](/cli/azure/extension#az_extension_add) para importar a extensão do Resource Graph com o seguinte comando:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Confirme se a extensão foi instalada e se ela tem a versão esperada (pelo menos, **1.1.0**) com [az extension list](/cli/azure/extension#az_extension_list):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>Criar uma consulta compartilhada do Resource Graph

Com a extensão da CLI do Azure adicionada ao ambiente escolhido, é hora de experimentar uma consulta compartilhada do Resource Graph. A consulta compartilhada é um objeto do Azure Resource Manager ao qual você pode conceder permissão ou o qual você pode executar no Explorador do Azure Resource Graph. A consulta resume a contagem de todos os recursos agrupados por _localização_.

1. Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create) para armazenar a consulta compartilhada do Azure Resource Graph. Esse grupo de recursos é chamado `resource-graph-queries`, e a localização é `westus2`.

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. Crie a consulta compartilhada do Azure Resource Graph usando a extensão `graph` e o comando [az graph shared-query create](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_create):

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. Liste as consultas compartilhadas no novo grupo de recursos. O comando [az graph shared-query list](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_list) retorna uma matriz de valores.

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. Para obter só um resultado da consulta compartilhada, use o comando [az graph shared-query show](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_show).

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. Execute a consulta compartilhada na CLI do Azure com a sintaxe `{{shared-query-uri}}` em um comando [az graph query](/cli/azure/ext/resource-graph/graph#ext_resource_graph_az_graph_query).
   Primeiro, copie o campo `id` do resultado do comando `show` anterior. Substitua o texto `shared-query-uri` no exemplo pelo valor do campo `id`, mas mantenha os caracteres `{{` e `}}` ao redor.

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > A sintaxe de `{{shared-query-uri}}` é uma **versão prévia do recurso**.

Outra maneira de encontrar consultas compartilhadas do Resource Graph é por meio do portal do Azure. No portal, use a barra de pesquisa para procurar "consultas do Resource Graph". Selecione a consulta compartilhada. Na página **Visão geral**, a guia **Consulta** exibe a consulta salva. O botão **Editar** abre-a no [Explorador do Resource Graph](./first-query-portal.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Caso deseje remover a consulta compartilhada, o grupo de recursos e a extensão do Resource Graph do ambiente da CLI do Azure, faça isso usando os seguintes comandos:

- [az graph shared-query delete](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_delete)
- [az group delete](/cli/azure/group#az_group_delete)
- [az extension remove](/cli/azure/extension#az_extension_remove)

```azurecli-interactive
# Delete the Azure Resource Graph shared query
az graph shared-query delete --resource-group 'resource-graph-queries' \
   --name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group without prompting for confirmation
az group delete --resource-group 'resource-graph-queries' --yes

# Remove the Azure Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você adicionou a extensão do Resource Graph ao ambiente da CLI do Azure e criou uma consulta compartilhada. Para saber mais sobre a linguagem do Resource Graph, prossiga para a página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obter mais informações sobre a linguagem de consulta](./concepts/query-language.md)