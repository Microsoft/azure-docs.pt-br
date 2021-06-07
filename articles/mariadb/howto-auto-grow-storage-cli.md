---
title: Aumento automático do armazenamento – CLI do Azure – Banco de Dados do Azure para MariaDB
description: Este artigo descreve como habilitar o aumento automático do armazenamento usando a CLI do Azure no Banco de Dados do Azure para MariaDB.
author: rothja
ms.author: jroth
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 494f892bd4278192d78fea4fb6793d3090ba88ec
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552981"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Aumento automático do armazenamento do Banco de Dados do Azure para MariaDB com a CLI do Azure
Este artigo descreve como configurar um armazenamento de servidor do Banco de Dados do Azure para MariaDB para aumentar sem afetar a carga de trabalho.

O servidor que está [alcançando o limite de armazenamento](concepts-pricing-tiers.md#reaching-the-storage-limit) está definido como somente leitura. Se o aumento automático do armazenamento estiver habilitado para servidores com menos de 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado será aumentado em 5 GB assim que o armazenamento gratuito estiver abaixo de 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado aumenta em 5% quando o espaço livre de armazenamento está abaixo de 5% do tamanho de armazenamento provisionado. [Estes](concepts-pricing-tiers.md#storage) limites máximos de armazenamento se aplicam.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia:

- Você precisa de um [servidor do Banco de Dados do Azure para MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="enable-mariadb-server-storage-auto-grow"></a>Habilitar o aumento automático do armazenamento do servidor MariaDB

Habilite o aumento automático do armazenamento em um servidor existente com este comando:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Habilite o aumento automático do armazenamento enquanto cria um servidor com este comando:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como criar alertas sobre métricas](howto-alert-metric.md).
