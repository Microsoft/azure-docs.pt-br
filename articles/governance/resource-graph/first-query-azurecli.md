---
title: 'Início Rápido: sua primeira consulta da CLI do Azure'
description: Neste início rápido, você segue as etapas para habilitar a extensão do Resource Graph para a CLI do Azure e executar sua primeira consulta.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5111f59eb760afda4e206837ca5bdf8bcc201338
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98917817"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Início Rápido: Execute a primeira consulta ao Resource Graph usando a CLI do Azure

A primeira etapa para usar o Azure Resource Graph é verificar se a extensão para a [CLI do Azure](/cli/azure/) está instalada. Este início rápido orienta você pelo processo de adição da extensão à instalação da CLI do Azure. Você pode usar a extensão com a CLI do Azure instalada localmente ou por meio do [Azure Cloud Shell](https://shell.azure.com).

No final deste processo, você terá adicionado a extensão à sua instalação da CLI do Azure de preferência e executado sua primeira consulta ao Resource Graph.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Adicionar a extensão do Resource Graph

Para habilitar a CLI do Azure para consultar o Azure Resource Graph, é necessário adicionar a extensão. Essa extensão funcionará sempre que a CLI do Azure puder ser usada, incluindo no [bash no Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (tanto autônomo quanto dentro do portal), na [imagem do Docker da CLI do Azure](https://hub.docker.com/_/microsoft-azure-cli) ou instalada localmente.

1. Verifique se a CLI do Azure mais recente está instalada (pelo menos a versão **2.0.76**). Se ainda não estiver instalado, siga [estas instruções](/cli/azure/install-azure-cli-windows).

1. Em seu ambiente da CLI do Azure preferido, importe-a com o seguinte comando:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Verifique se a extensão foi instalada e se é a versão esperada (pelo menos **1.0.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta ao Resource Graph

Com a extensão da CLI do Azure adicionada ao seu ambiente de preferência, é hora de experimentar uma consulta simples ao Resource Graph. A consulta retornará os cinco primeiros recursos do Azure com o **Nome** e o **Tipo de recurso** de cada recurso.

1. Execute a primeira consulta ao Azure Resource Graph usando a extensão `graph` e o comando `query`:

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Devido a essa consulta de exemplo não fornecer um modificador de classificação, tal como `order by`, executar essa consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação.

1. Atualizar a consulta para `order by` a propriedade **Name**:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Assim como ocorre com a primeira consulta, executar esta consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação. A ordem dos comandos de consulta é importante. Neste exemplo, o `order by` vem após o `limit`. Essa ordem de comando limita primeiro os resultados da consulta e os ordena.

1. Atualize a consulta para, primeiro, `order by` a propriedade **Name** e depois `limit` conforme os primeiros cinco resultados:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'Resources | project name, type | order by name asc | limit 5'
   ```

Quando a consulta final for executada várias vezes, supondo que nada em seu ambiente esteja sendo alterado, os resultados retornados serão consistentes e ordenados segundo a propriedade **Name**, mas ainda limitados aos cinco primeiros resultados.

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser remover a extensão do Resource Graph do seu ambiente da CLI do Azure, faça isso usando o comando a seguir:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você adicionou a extensão do Resource Graph ao seu ambiente da CLI do Azure e executou sua primeira consulta. Para saber mais sobre a linguagem do Resource Graph, prossiga para a página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obter mais informações sobre a linguagem de consulta](./concepts/query-language.md)
