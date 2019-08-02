---
title: Exemplo de script da CLI do Azure - Criar um aplicativo Web que usa a autenticação do Serviço SignalR e GitHub
description: Exemplo de script da CLI do Azure - Criar um aplicativo Web que usa a autenticação do Serviço SignalR e GitHub
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/22/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 16fa44c5fa0b674fe27e2ec8e2dc8e640742ec63
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565788"
---
# <a name="create-a-web-app-that-uses-signalr-service-and-github-authentication"></a>Criar um aplicativo Web que usa a autenticação do Serviço SignalR e GitHub

Esse exemplo de script cria um novo recurso do Serviço Azure SignalR, que é usado para enviar atualizações de conteúdo em tempo real para os clientes. Esse script também adiciona um novo plano de serviço de aplicativo e aplicativo Web para hospedar seu aplicativo Web do ASP.NET Core que usa o Serviço SignalR. O aplicativo Web é configurado com as configurações de aplicativo para se conectar ao novo recurso de serviço SignalR e autenticar com a [autenticação GitHub](https://developer.github.com/v3/guides/basics-of-authentication/). O aplicativo Web também é configurado para usar uma fonte de implantação do repositório git local.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Esse script usa a extensão *signalr* para a CLI do Azure. Execute o seguinte comando para instalar a extensão *signalr* para a CLI do Azure antes de usar esse exemplo de script:

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service-github-oauth/create-signalr-with-app-service-github-oauth.sh "Create a new SignalR Service and Web App configured to use SignalR, GitHub OAuth, and local Git repository deployment source.")]

Anote o nome real gerado para o novo grupo de recursos. Ele será mostrado na saída. Você usará esse nome de grupo de recursos quando quiser excluir todos os recursos do grupo.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Cada comando na tabela redireciona para a documentação específica do comando. Este script usa os seguintes comandos:

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Cria um recurso de Serviço Azure SignalR. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Lista as chaves que serão usadas pelo seu aplicativo ao enviar atualizações de conteúdo em tempo real com o SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Cria um Plano de Serviço de Aplicativo do Azure para hospedar aplicativos web. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Cria um aplicativo Web do Azure usando o plano de hospedagem do Serviço de Aplicativo. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Adiciona novas configurações de aplicativo para o aplicativo web. Essas configurações de aplicativo são usadas para armazenar a cadeia de conexão SignalR e segredos do aplicativo GitHub OAuth. |
| [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) | Atualizar as credenciais de implantação. |
| [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) | Obtenha uma URL para um ponto de extremidade de repositório git para clonar e efetuar push para a implantação de aplicativo Web. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de script CLI do Serviço SignalR do Azure podem ser encontrados na [documentação do Serviço SignalR do Azure](../signalr-reference-cli.md).
