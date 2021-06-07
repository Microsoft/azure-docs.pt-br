---
title: 'CLI: dimensionar aplicativo com o Gerenciador de Tráfego'
description: Saiba como usar a CLI do Azure para automatizar a implantação e o gerenciamento do seu aplicativo do Serviço de Aplicativo. Este exemplo descreve como dimensionar um aplicativo em todo o mundo com o Gerenciador de Tráfego.
author: msangapu-msft
tags: azure-service-management
ms.assetid: e4033a50-0e05-4505-8ce8-c876204b2acc
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 089ab9d6b3c0f678c2a200872a0da4160fa6292a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787657"
---
# <a name="scale-an-app-service-app-worldwide-with-a-high-availability-architecture-using-azure-cli"></a>Dimensionar um aplicativo do Serviço de Aplicativo em todo o mundo com uma arquitetura de alta disponibilidade usando a CLI do Azure

Este script de exemplo cria um grupo de recursos, dois Planos do Serviço de Aplicativo, dois aplicativos, um perfil do Gerenciador de Tráfego e dois pontos de extremidade do Gerenciador de Tráfego. Depois que o exercício for concluído, você terá uma arquitetura altamente disponível, que fornece disponibilidade global de seu aplicativo com base na menor latência da rede.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/scale-geographic/scale-geographic.sh "Geographic Scale")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, um aplicativo do Serviço de Aplicativo, um perfil do Gerenciador de Tráfego e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Cria um Plano do Serviço de Aplicativo. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Cria um aplicativo do Serviço de Aplicativo. |
| [`az network traffic-manager profile create`](/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) | Cria um perfil de Gerenciador de Tráfego do Azure. |
| [`az network traffic-manager endpoint create`](/cli/azure/network/traffic-manager/endpoint#az_network_traffic-manager_endpoint_create) | Adiciona um endpoint a um perfil do Gerenciador de tráfego do Azure. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure](../samples-cli.md).
