---
title: Conectar uma conta do Azure Cosmos existente com pontos de extremidade de serviço de rede virtual
description: Conectar uma conta do Azure Cosmos existente com pontos de extremidade de serviço de rede virtual
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: eae343b0ac85f3fdf6d0f6d52c7afbb91f401df4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770724"
---
# <a name="connect-an-existing-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Conectar uma conta do Azure Cosmos existente com pontos de extremidade de serviço de rede virtual usando CLI do Azure
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.9.1 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Exemplo de script

O objetivo deste exemplo é mostrar como conectar uma conta do Azure Cosmos existente a uma nova rede virtual existente quando a sub-rede ainda não está configurada para pontos de extremidade de serviço usando o parâmetro `ignore-missing-vnet-service-endpoint`. Isso permite que a configuração da conta do Cosmos seja concluída sem erro antes que a configuração para a sub-rede da rede virtual seja concluída. Depois que a configuração da sub-rede for concluída, a conta do Cosmos poderá ser acessada por meio da sub-rede configurada.

> [!NOTE]
> Esta amostra descreve como usar uma conta da API do SQL (Core). Para usar este exemplo para outras APIs, aplique os parâmetros `enable-virtual-network` e `virtual-network-rules` no script abaixo ao script específico da sua API.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints-ignore-missing-vnet.sh "Create an Azure Cosmos account with service endpoints.")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Após a execução do script de exemplo, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Cria uma rede virtual do Azure. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Cria uma sub-rede para uma rede virtual do Azure. |
| [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show) | Retorna uma sub-rede para uma rede virtual do Azure. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Cria uma conta do Banco de Dados Cosmos do Azure. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) | Atualiza uma sub-rede para uma rede virtual do Azure. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a CLI do Azure Cosmos DB, confira [documentação da CLI do Azure Cosmos DB](/cli/azure/cosmosdb).

Todos os exemplos de scripts da CLI do Azure Cosmos DB podem ser encontrados no [Repositório GitHub da CLI do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
