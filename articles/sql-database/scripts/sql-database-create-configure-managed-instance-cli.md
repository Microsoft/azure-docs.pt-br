---
title: 'CLI do Azure: Criar uma instância gerenciada'
description: Script de exemplo da CLI do Azure para criar uma instância gerenciada na Instância Gerenciada de SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 54e1aa993f177a4d3bc255287ae8c2fb14cf65af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87497266"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance"></a>Usar a CLI para criar uma Instância Gerenciada de SQL do Azure

Este exemplo de script da CLI do Azure cria uma Instância Gerenciada de SQL do Azure em uma sub-rede dedicada dentro de uma nova rede virtual. Ele também configura uma tabela de rotas e um grupo de segurança de rede para a rede virtual. Depois que o script tiver sido executado com êxito, a instância gerenciada poderá ser acessada de dentro da rede virtual ou de um ambiente local. Confira [Configurar a VM do Azure para se conectar a uma Instância Gerenciada de SQL do Azure]../../azure-sql/managed-instance/connect-vm-instance-configure.md) e [Configurar uma conexão ponto a site com uma Instância Gerenciada de SQL do Azure localmente](../../azure-sql/managed-instance/point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Para limitações, consulte [regiões com suporte](../../azure-sql/managed-instance/resource-limits.md#supported-regions) e [tipos de assinatura compatíveis](../../azure-sql/managed-instance/resource-limits.md#supported-subscription-types).

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

### <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Limpar a implantação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exemplo de referência

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Descrição |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | Comandos de rede virtual. |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Comandos de sub-rede de rede virtual. |
| [az network route-table](/cli/azure/network/route-table) | Comandos da tabela de rotas de rede. |
| [az sql mi](/cli/azure/sql/mi) | Comandos da Instância Gerenciada de SQL. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos de script da CLI do Banco de Dados SQL adicionais podem ser encontrados na [documentação do Banco de Dados SQL do Azure](../../azure-sql/database/az-cli-script-samples-content-guide.md).
