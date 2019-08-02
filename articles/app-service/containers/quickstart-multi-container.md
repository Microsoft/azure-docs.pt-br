---
title: Criar aplicativo com vários contêineres usando o Docker Compose – Serviço de Aplicativo do Azure
description: Implantar seu primeiro aplicativo multicontêiner no Aplicativo Web para Contêineres do Azure em minutos
keywords: serviço de aplicativo do azure, aplicativo web, linux, docker, compose, vários contêineres, aplicativo web para contêineres, contêiner, wordpress, bd do azure para mysql, banco de dados de produção com contêineres
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: fce819e84d5c532777ffee91ed4e75a0b65dec9b
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853698"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Criar um aplicativo multicontêiner (versão prévia) usando uma configuração do Docker Compose

O [Aplicativo Web para Contêineres](app-service-linux-intro.md) fornece uma maneira flexível de usar imagens do Docker. Este guia de início rápido mostra como implantar um aplicativo multicontêiner para o Aplicativo Web para Contêineres no [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) usando uma configuração do Docker Compose.

Você concluirá este início rápido no Cloud Shell, mas também pode executar esses comandos localmente com a [CLI do Azure](/cli/azure/install-azure-cli) (2.0.32 ou posterior). 

![Aplicativo multicontêiner de exemplo no Aplicativo Web para Contêineres][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Baixar o exemplo

Para este início rápido, use o arquivo de composição do [Docker](https://docs.docker.com/compose/wordpress/#define-the-project). O arquivo de configuração podem ser localizados em [Exemplos do Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

No Cloud Shell, crie um diretório de início rápido e depois altere-o.

```bash
mkdir quickstart

cd quickstart
```

Em seguida, execute o comando a seguir para clonar o repositório de aplicativos de exemplo ao seu diretório de início rápido. Em seguida, altere para o diretório `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

No Cloud Shell, crie um grupo de recursos com o comando [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create). O exemplo a seguir cria um grupo de recursos nomeado *myResourceGroup* no localização *Centro-Sul dos EUA*. Para ver todos os locais com suporte para o Serviço de Aplicativo no Linux no nível **Standard**, execute o comando [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Em geral, você cria seu grupo de recursos e os recursos em uma região próxima a você.

Quando o comando for concluído, uma saída JSON mostra as propriedades do grupo de recursos.

## <a name="create-an-azure-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Azure

No Cloud Shell, crie um plano do Serviço de Aplicativo no grupo de recursos com o comando [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).

O exemplo a seguir cria um plano do Serviço de Aplicativo denominado `myAppServicePlan` no tipo de preço **Standard** (`--sku S1`) e em um contêiner do Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Quando o Plano do Serviço de Aplicativo for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="create-a-docker-compose-app"></a>Criar um aplicativo Docker Compose

Em seu terminal do Cloud Shell, crie um [aplicativo Web](app-service-linux-intro.md) multicontêiner no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Não se esqueça de substituir _\<nome_do_aplicativo>_ por um nome de aplicativo exclusivo (os caracteres válidos são `a-z`, `0-9` e `-`).

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Quando o aplicativo Web for criado, a CLI do Azure mostrará um resultado semelhante ao seguinte exemplo:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Navegar até o aplicativo

Navegue até o aplicativo implantado em (`http://<app_name>.azurewebsites.net`). O aplicativo pode demorar alguns minutos para carregar. Se um erro for exibido, aguarde mais alguns minutos e atualize o navegador.

![Aplicativo multicontêiner de exemplo no Aplicativo Web para Contêineres][1]

**Parabéns**, você criou um aplicativo multicontêiner no Aplicativo Web para Contêineres.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo WordPress cm vários contêineres](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [Configurar um contêiner personalizado](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png