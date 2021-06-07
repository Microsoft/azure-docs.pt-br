---
title: 'Início rápido: Encaminhar eventos do Cache do Azure para Redis para o ponto de extremidade da Web com a CLI do Azure'
description: Use a Grade de Eventos do Azure para assinar eventos do Cache do Azure para Redis, disparar um evento e ver os resultados.
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 7f33ca0043400962054fabb1aadb1da612fe5426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99806419"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-azure-cli"></a>Início rápido: Encaminhar eventos do Cache do Azure para Redis para o ponto de extremidade da Web com a CLI do Azure

A Grade de Eventos do Azure é um serviço de eventos para a nuvem. Neste guia de início rápido, você usará a CLI do Azure para assinar eventos do Cache do Azure para Redis, disparar um evento e ver os resultados.

Normalmente, você envia eventos para um ponto de extremidade que processa os dados de evento e realiza ações. No entanto, para simplificar este guia de início rápido, você enviará eventos para um aplicativo Web que coletará e exibirá as mensagens. Quando você concluir as etapas descritas neste guia de início rápido, verá que os dados do evento foram enviados ao aplicativo Web.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá que você esteja executando a última versão da CLI do Azure (2.0.70 ou posterior). Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Caso não esteja usando o Cloud Shell, primeiro você deve entrar usando `az login`.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos da Grade de Eventos são implantados como recursos individuais do Azure e precisam ser provisionados em um grupo de recursos do Azure. Um grupo de recursos é uma coleção lógica na qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). 

O exemplo a seguir cria um grupo de recursos chamado `<resource_group_name>` na localização *westcentralus*.  Substitua `<resource_group_name>` por um nome exclusivo para o grupo de recursos.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-cache-instance"></a>Criar uma instância de cache

```azurecli-interactive
#/bin/bash

# Create a Basic C0 (256 MB) Azure Cache for Redis instance
az redis create --name <cache_name> --resource-group <resource_group_name> --location westcentralus --sku Basic --vm-size C0
```


## <a name="create-a-message-endpoint"></a>Criar um ponto de extremidade de mensagem

Antes de assinar o tópico, vamos criar o ponto de extremidade para a mensagem do evento. Normalmente, o ponto de extremidade executa ações com base nos dados de evento. Para simplificar este início rápido, você implanta um [aplicativo Web criado previamente](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens de eventos. A solução implantada inclui um plano do Serviço de Aplicativo, um aplicativo Web do Aplicativo do Serviço de e o código-fonte do GitHub.

Substitua `<your-site-name>` por um nome exclusivo para o aplicativo Web. O nome do aplicativo Web deve ser exclusivo, pois é parte de uma entrada DNS.

```azurecli-interactive
sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

A implantação pode levar alguns minutos para ser concluída. Depois que a implantação for bem-sucedida, exiba seu aplicativo Web para garantir que ele esteja em execução. Em um navegador da Web, navegue até: `https://<your-site-name>.azurewebsites.net`

Você deve ver o site sem mensagens exibidas no momento.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-azure-cache-for-redis-instance"></a>Assinar a sua instância do Cache do Azure para Redis

Nesta etapa, você assinará um tópico para indicar à Grade de Eventos quais eventos deseja acompanhar e para que local esses eventos serão enviados. O exemplo a seguir assina a instância do Cache do Azure para Redis criada e transmite a URL do aplicativo Web como o ponto de extremidade para notificação de eventos. Substitua `<event_subscription_name>` por um nome para a assinatura de evento. Em `<resource_group_name>` e `<cache_name>`, use os valores criados anteriormente.

O ponto de extremidade para seu aplicativo Web deve incluir o sufixo `/api/updates/`.

```azurecli-interactive
cacheId=$(az redis show --name <cache_name> --resource-group <resource_group_name> --query id --subscription <subscription_id>)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --source-resource-id $cacheId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Exiba novamente o seu aplicativo Web e observe que um evento de validação de assinatura foi enviado a ele. Selecione o ícone de olho para expandir os dados de evento. A Grade de Eventos envia o evento de validação de modo que o ponto de extremidade possa verificar se ele deseja receber os dados de evento. O aplicativo Web inclui o código para validar a assinatura.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Visualizador da Grade de Eventos do Azure.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Disparar um evento do Cache do Azure para Redis

Agora, vamos disparar um evento para ver como a Grade de Eventos distribui a mensagem para o ponto de extremidade. Vamos exportar os dados armazenados na sua instância do Cache do Azure para Redis. Novamente, use os valores de `{cache_name}` e `{resource_group_name}` criados anteriormente.

```azurecli-interactive
az redis export  --ids '/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}' \
    --prefix '<prefix_for_exported_files>' \
    --container '<SAS_url>'  
```

Você disparou o evento, e a Grade de Eventos enviou a mensagem para o ponto de extremidade configurado durante a assinatura. Exiba seu aplicativo Web para ver o evento que você acabou de enviar.


```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ExportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ExportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ExportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Limpar os recursos
Se você pretende continuar trabalhando com essa instância do Cache do Azure para Redis e a assinatura de evento, não limpe os recursos criados neste guia de início rápido. Caso contrário, use o comando a seguir para excluir os recursos criados neste guia de início rápido.

Substitua `<resource_group_name>` pelo recurso de grupo criado acima.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como criar tópicos e assinaturas de eventos, saiba mais sobre os eventos do Cache do Azure para Redis e como a Grade de Eventos pode ajudar você:

- [Como responder aos eventos do Cache do Azure para Redis](cache-event-grid.md)
- [Sobre a Grade de Eventos](../event-grid/overview.md)
