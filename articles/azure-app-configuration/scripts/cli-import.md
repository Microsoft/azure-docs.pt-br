---
title: Exemplo de script da CLI do Azure – Importar para um repositório de Configuração de Aplicativos
titleSuffix: Azure App Configuration
description: Usar script da CLI do Azure – Importar configuração para a Configuração de Aplicativos do Azure
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: f7d68024dd86a1eb2a0311cecf607d41a7067deb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173561"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importação para um repositório de configurações de aplicativo do Azure

Este script de exemplo importa configurações de pares chave-valor para um repositório de Configuração de Aplicativos do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Exemplo de script

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --format json --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para importar para um repositório de Configuração de Aplicativos. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az appconfig kv import](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Importa para um recurso do repositório de Configuração de Aplicativos. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, confira a [Documentação da CLI do Azure](/cli/azure).

Outros exemplos de script da CLI da Configuração de Aplicativos podem ser encontrados nos [Exemplos da CLI da Configuração de Aplicativos do Azure](../cli-samples.md).
