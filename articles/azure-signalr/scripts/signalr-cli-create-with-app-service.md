---
title: Criar Serviço do SignalR com um Serviço de Aplicativo usando a CLI do Azure
description: Use a CLI do Azure para criar Serviço do SignalR com um Serviço de Aplicativo. Conheça todos os comandos da CLI do Serviço do Azure SignalR.
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/13/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e6e2484dc7fffdcecb64ef3b3afa3a7a4343d29c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787297"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>Criar um Serviço SignalR com um Serviço de Aplicativo

Esse exemplo de script cria um novo recurso do Serviço Azure SignalR, que é usado para enviar atualizações de conteúdo em tempo real para os clientes. Esse script também adiciona um novo plano de serviço de aplicativo e aplicativo Web para hospedar seu aplicativo Web do ASP.NET Core que usa o Serviço SignalR. O aplicativo Web é configurado com uma Configuração de Aplicativo chamada *AzureSignalRConnectionString* para se conectar ao novo recurso de Serviço SignalR.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Exemplo de script

Esse script usa a extensão *signalr* para a CLI do Azure. Execute o seguinte comando para instalar a extensão *signalr* para a CLI do Azure antes de usar esse exemplo de script:

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate unique names for the SignalR service, resource group, 
# app service, and app service plan
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"
myWebAppName=SignalRTestWebApp$randomNum
myAppSvcPlanName=$myAppSvcName"Plan"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 1 \
  --service-mode Default

# Create an App Service plan.
az appservice plan create --name $myAppSvcPlanName --resource-group $myResourceGroupName --sku FREE

# Create the Web App
az webapp create --name $myWebAppName --resource-group $myResourceGroupName --plan $myAppSvcPlanName  

# Get the SignalR primary connection string
primaryConnectionString=$(az signalr key list --name $mySignalRSvcName \
  --resource-group $myResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "AzureSignalRConnectionString=$primaryConnectionString"
```

Anote o nome real gerado para o novo grupo de recursos. Ele será mostrado na saída. Você usará esse nome de grupo de recursos quando quiser excluir todos os recursos do grupo.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Cada comando da tabela é vinculado à documentação específica do comando. Este script usa os seguintes comandos:

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az signalr create](/cli/azure/signalr#az_signalr_create) | Cria um recurso de Serviço Azure SignalR. |
| [az signalr key list](/cli/azure/signalr/key#az_signalr_key_list) | Lista as chaves que serão usadas pelo seu aplicativo ao enviar atualizações de conteúdo em tempo real com o SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) | Cria um Plano de Serviço de Aplicativo do Azure para hospedar aplicativos web. |
| [az webapp create](/cli/azure/webapp#az_webapp_create) | Cria um aplicativo Web do Azure usando o plano de hospedagem do Serviço de Aplicativo. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Adiciona uma nova configuração de aplicativo para o aplicativo Web. Essa configuração de aplicativo é usada para armazenar a cadeia de conexão do SignalR. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de script CLI do Serviço SignalR do Azure podem ser encontrados na [documentação do Serviço SignalR do Azure](../signalr-reference-cli.md).
