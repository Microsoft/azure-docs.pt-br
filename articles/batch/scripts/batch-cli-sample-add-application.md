---
title: "Amostra de Script da CLI do Azure – Adicionar um aplicativo no Lote | Microsoft Docs"
description: "Amostra de Script da CLI do Azure – Adicionar um aplicativo no Lote"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: cbfe8ab565ecf7f298a9a6c0f0c8298c675f178c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>Adicionar aplicativos ao Lote do Azure com a CLI do Azure

Este script demonstra como configurar um aplicativo para uso com um pool ou tarefa do Lote do Azure. Para configurar um aplicativo, coloque seu executável, junto com quaisquer dependências, em um arquivo .zip. Neste exemplo, o arquivo executável zip é chamado 'my-application-exe.zip'.

## <a name="prerequisites"></a>Pré-requisitos

- Instale a CLI do Azure usando as instruções fornecidas no [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) se ainda não tiver feito isso.
- Crie uma conta do lote do Azure caso ainda não tenha uma. Consulte [Criar uma conta do lote com a CLI do Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account) para um script de exemplo que cria uma conta.

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-application"></a>Limpar aplicativo

Depois de executar o script de exemplo acima, execute os comandos a seguir para remover o aplicativo e todos os seus pacotes de aplicativo carregados.

```azurecli
az batch application package delete -g myresourcegroup -n mybatchaccount --application-id myapp --version 1.0 --yes
az batch application delete -g myresourcegroup -n mybatchaccount --application-id myapp --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um aplicativo e carregar um pacote de aplicativo.
Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_create) | Criar um aplicativo.  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_set) | Atualiza as propriedades de um aplicativo.  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#az_batch_application_package_create) | Adiciona um pacote de aplicativos ao aplicativo especificado.  |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

As amostras de script da CLI do Lote adicionais podem ser encontrados na [documentação do Lote do Azure](../batch-cli-samples.md).
