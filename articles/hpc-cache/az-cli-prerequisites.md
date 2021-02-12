---
title: CLI do Azure pré-requisitos para o cache do Azure HPC
description: Etapas de instalação antes de usar CLI do Azure para criar ou modificar um cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 13f45c96a830110bd0f4a2d4a2b422921d7a2e31
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654449"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Configurar a CLI do Azure para o Azure HPC Cache

Siga estas etapas para preparar seu ambiente antes de usar CLI do Azure para criar ou gerenciar um cache do HPC do Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - O cache HPC do Azure requer a versão 2,7 ou posterior do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="set-default-resource-group-optional"></a>Definir grupo de recursos padrão (opcional)

A maioria dos comandos HPC-cache exige que você passe o grupo de recursos do cache. Você pode definir o grupo de recursos padrão usando [AZ configure](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Próximas etapas

Depois de instalar a extensão de CLI do Azure e fazer logon, você pode usar CLI do Azure para criar e gerenciar sistemas de cache HPC do Azure.

* [Criar um Azure HPC Cache](hpc-cache-create.md)
* [CLI do Azure a documentação do HPC-cache](/cli/azure/ext/hpc-cache/hpc-cache)
