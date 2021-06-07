---
title: 'Início Rápido: Criar um aplicativo multicontêiner'
description: Comece a usar os aplicativos multicontêiner no Serviço de Aplicativo do Azure implantando seu primeiro aplicativo multicontêiner.
keywords: serviço de aplicativo do azure, aplicativo web, linux, docker, compose, vários contêineres, aplicativo web para contêineres, contêiner, wordpress, bd do azure para mysql, banco de dados de produção com contêineres
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 0ea55c36c239b5ecdb51ef3dc7a3ff762718bd1e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768967"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Criar um aplicativo multicontêiner (versão prévia) usando uma configuração do Docker Compose

> [!NOTE]
> Vários contêineres estão em versão prévia.

O [Aplicativo Web para Contêineres](overview.md#app-service-on-linux) fornece uma maneira flexível de usar imagens do Docker. Este guia de início rápido mostra como implantar um aplicativo multicontêiner (versão prévia) para o Aplicativo Web para Contêineres no [Cloud Shell](../cloud-shell/overview.md) usando uma configuração do Docker Compose.

![Aplicativo multicontêiner de exemplo no Aplicativo Web para Contêineres][1]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Este artigo exige a versão 2.0.32 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="download-the-sample"></a>Baixar o exemplo

Para este início rápido, use o arquivo de composição do [Docker](https://docs.docker.com/compose/wordpress/#define-the-project). O arquivo de configuração podem ser localizados em [Exemplos do Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

No Cloud Shell, crie um diretório de início rápido e depois altere-o.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Em seguida, execute o comando a seguir para clonar o repositório de aplicativos de exemplo ao seu diretório de início rápido. Em seguida, altere para o diretório `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

No Cloud Shell, crie um grupo de recursos com o comando [`az group create`](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos nomeado *myResourceGroup* no localização *Centro-Sul dos EUA*. Para ver todos os locais com suporte para o Serviço de Aplicativo no Linux no nível **Standard**, execute o comando [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Em geral, você cria seu grupo de recursos e os recursos em uma região próxima a você.

Quando o comando for concluído, uma saída JSON mostra as propriedades do grupo de recursos.

## <a name="create-an-azure-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Azure

No Cloud Shell, crie um plano do Serviço de Aplicativo no grupo de recursos com o comando [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create).

O exemplo a seguir cria um plano do Serviço de Aplicativo denominado `myAppServicePlan` no tipo de preço **Standard** (`--sku S1`) e em um contêiner do Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Quando o Plano do Serviço de Aplicativo for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

<pre>
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
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
</pre>

## <a name="create-a-docker-compose-app"></a>Criar um aplicativo Docker Compose

> [!NOTE]
> O Docker Compose nos Serviços de Aplicativo do Azure tem um limite de 4 mil caracteres no momento.

Em seu terminal do Cloud Shell, crie um [aplicativo Web](overview.md#app-service-on-linux) multicontêiner no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [az webapp create](/cli/azure/webapp#az_webapp_create). Não se esqueça de substituir _\<app_name>_ por um nome de aplicativo exclusivo (os caracteres válidos são `a-z`, `0-9` e `-`).

```azurecli
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Quando o aplicativo Web for criado, a CLI do Azure mostrará um resultado semelhante ao seguinte exemplo:

<pre>
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="browse-to-the-app"></a>Navegar até o aplicativo

Navegue até o aplicativo implantado em (`http://<app_name>.azurewebsites.net`). O aplicativo pode demorar alguns minutos para carregar. Se um erro for exibido, aguarde mais alguns minutos e atualize o navegador.

![Aplicativo multicontêiner de exemplo no Aplicativo Web para Contêineres][1]

**Parabéns**, você criou um aplicativo multicontêiner no Aplicativo Web para Contêineres.

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo WordPress cm vários contêineres](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [Configurar um contêiner personalizado](configure-custom-container.md)

<!--Image references-->
[1]: media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
