---
title: Exemplo de Script CLI do Azure - mapeie um domínio personalizado para um aplicativo de funções | Microsoft Docs
description: Exemplo de Script CLI do Azure - mapeie um domínio personalizado para um aplicativo de funções no Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/04/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 19fd73dff045d6d784dd70d1f3ad539a22faf413
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075478"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Mapear um domínio personalizado para um aplicativo de funções

Esse script de exemplo cria um aplicativo de funções em um plano do Serviço de Aplicativo e, em seguida, mapeia-os para um domínio personalizado que você fornecer. Quando o aplicativo de funções estiver hospedado em um [Plano Premium](../functions-scale.md#premium-plan) ou [Plano do Serviço de Aplicativo](../functions-scale.md#app-service-plan), você poderá mapear para um domínio personalizado usando um registro CNAME ou A. Para aplicativos de função em um [plano de consumo](../functions-scale.md#consumption-plan), há suporte apenas para a opção CNAME. Este exemplo cria um plano do serviço de aplicativo e requer um registro a para mapear o domínio. 

Para executar este script de exemplo, você já deve ter configurado um registro em seu domínio personalizado que aponta para o nome de domínio do seu aplicativo Web padrão. Para saber mais, veja [Mapear instruções de domínio personalizadas para o Serviço de Aplicativo do Azure](https://aka.ms/appservicecustomdns). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, será necessário usar a CLI do Azure 2.0 ou uma versão posterior. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Cria uma conta de armazenamento necessária para o aplicativo de funções. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Cria um Plano do Serviço de Aplicativo necessário para mapear um domínio personalizado. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Implante um aplicativo de funções no plano do Serviço de Aplicativo. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | Mapear um domínio personalizado para um aplicativo de funções. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de scripts da CLI do Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).
