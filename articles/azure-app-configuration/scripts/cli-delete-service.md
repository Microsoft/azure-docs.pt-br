---
title: Amostra de script da CLI do Azure – Excluir um repositório de Configuração de Aplicativos do Azure
titleSuffix: Azure App Configuration
description: Excluir um repositório de Configuração de Aplicativos do Azure usando um exemplo de script da CLI do Azure. Consulte os links no artigo de referência para ver os comandos usados no script.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 49d6a85faa55de5dbf50377998dbe2fc829d9f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929779"
---
# <a name="delete-an-azure-app-configuration-store"></a>Excluir um repositório de configurações de aplicativo do Azure

Este script de exemplo exclui uma instância da Configuração de Aplicativo do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Exemplo de script

```azurecli-interactive
#/bin/bash

# Delete an App Configuration store named myTestAppConfigStore from the Resource Group myResourceGroup
az appconfig delete --name myTestAppConfigStore --resource-group myResourceGroup
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para excluir um repositório de Configuração de Aplicativos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az appconfig delete](/cli/azure/appconfig#az-appconfig-delete) | Exclui um recurso do repositório de Configuração de Aplicativos. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Outros exemplos de script da CLI da Configuração de Aplicativos podem ser encontrados nos [Exemplos da CLI da Configuração de Aplicativos do Azure](../cli-samples.md).
