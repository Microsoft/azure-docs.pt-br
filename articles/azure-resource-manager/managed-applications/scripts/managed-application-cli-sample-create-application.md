---
title: 'Amostra de script da CLI do Azure: Implantar um aplicativo gerenciado'
description: Fornece um script de exemplo da CLI do Azure que implanta uma definição do Aplicativo Gerenciado do Azure na assinatura.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3ea2baa897efb5c1a01b32e92e76a69c9d1f231c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775467"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Implantar um aplicativo gerenciado no catálogo de serviços com a CLI do Azure

Este script implanta uma definição de aplicativo gerenciado do catálogo de serviços. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa o comando a seguir para implantar o aplicativo gerenciado. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az managedapp create](/cli/azure/managedapp#az_managedapp_create) | Crie um aplicativo gerenciado. Forneça a ID de definição e os parâmetros para o modelo. |


## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](../overview.md).
* Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).
