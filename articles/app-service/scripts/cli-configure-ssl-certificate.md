---
title: 'CLI: fazer upload de um certificado TLS/SSL em um aplicativo e associá-lo a ele'
description: Saiba como usar a CLI do Azure para automatizar a implantação e o gerenciamento do seu aplicativo do Serviço de Aplicativo. Esta amostra descreve como associar um certificado TLS/SSL personalizado a um aplicativo.
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 1d03018cca9757c8a6ddddbff96738a407bba7d4
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006388"
---
# <a name="bind-a-custom-tlsssl-certificate-to-an-app-service-app-using-cli"></a>Associar um certificado TLS/SSL personalizado a um aplicativo do Serviço de Aplicativo usando a CLI

Este script de exemplo cria um aplicativo no Serviço de Aplicativo com seus recursos relacionados e, em seguida, associa o certificado TLS/SSL de um nome de domínio personalizado a ele. Neste exemplo, você precisa de:

* Acesso à página de configuração do DNS do registrador de seu domínio.
* Um arquivo .PFX válido e sua senha para o certificado TLS/SSL que você deseja carregar e associar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, você precisará da CLI do Azure versão 2.0 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Cria um Plano do Serviço de Aplicativo. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Cria um aplicativo do Serviço de Aplicativo. |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname#az-webapp-config-hostname-add) | Mapeia um domínio personalizado para um aplicativo do Serviço de Aplicativo. |
| [`az webapp config ssl upload`](/cli/azure/webapp/config/ssl#az-webapp-config-ssl-upload) | Carrega um certificado TLS/SSL a um aplicativo do Serviço de Aplicativo. |
| [`az webapp config ssl bind`](/cli/azure/webapp/config/ssl#az-webapp-config-ssl-bind) | Associa um certificado TLS/SSL carregado a um aplicativo do Serviço de Aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure](../samples-cli.md).
