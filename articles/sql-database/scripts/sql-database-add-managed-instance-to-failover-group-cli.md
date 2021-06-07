---
title: Exemplo da CLI – Grupo de failover – Instância Gerenciada de SQL do Azure
description: Script de exemplo da CLI do Azure para criar uma Instância Gerenciada de SQL do Azure, adicioná-la a um grupo de failover e testar o failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: f196db537ef0a64d14930ed6bc67696ee4614c23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86528889"
---
# <a name="use-cli-to-add-an-azure-sql-managed-instance-to-a-failover-group"></a>Usar a CLI para adicionar uma Instância Gerenciada de SQL do Azure a um grupo de failover

Este exemplo de script da CLI do Azure cria duas instâncias gerenciadas, adiciona-as a um grupo de failover e, em seguida, testa o failover da instância gerenciada primária para a instância gerenciada secundária.

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

### <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Limpar a implantação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele. Você precisará remover o grupo de recursos duas vezes. Remover o grupo de recursos pela primeira vez removerá a instância gerenciada e os clusters virtuais, mas falhará em seguida com esta mensagem de erro: `az group delete : Long running operation failed with status 'Conflict'.`. Execute o comando az group delete uma segunda vez para remover todos os recursos residuais, bem como o grupo de recursos.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exemplo de referência

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Descrição |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | Comandos de rede virtual.  |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Comandos de sub-rede de rede virtual. |
| [az network nsg](/cli/azure/network/nsg) | Comandos do grupo de segurança de rede. |
| [az network route-table](/cli/azure/network/route-table) | Comandos da tabela de rotas. |
| [az sql mi](/cli/azure/sql/mi) | Comandos da Instância Gerenciada de SQL. |
| [az network public-ip](/cli/azure/network/public-ip) | Comandos de endereço IP público de rede. |
| [az network vnet-gateway](/cli/azure/network/vnet-gateway) | Comandos do gateway de rede virtual. |
| [az sql instance-failover-group](/cli/azure/sql/instance-failover-group) | Comandos do grupo de failover da Instância Gerenciada de SQL. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos de script da CLI do Banco de Dados SQL adicionais podem ser encontrados na [documentação do Banco de Dados SQL do Azure](../../azure-sql/database/az-cli-script-samples-content-guide.md).
