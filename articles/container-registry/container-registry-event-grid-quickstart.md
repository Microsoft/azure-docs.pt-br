---
title: Início Rápido - Enviar eventos de Registro de Contêiner do Azure para a Grade de Eventos
description: Neste início rápido, você habilita os eventos da Grade de Eventos para o registro de contêiner e, em seguida, efetua push de imagens de contêiner e exclui eventos para um aplicativo de exemplo.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 08/23/2018
ms.author: danlep
ms.custom: seodec18
ms.openlocfilehash: 49ee9a7f12601b0d93e320ab797be4a1ada41c04
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309795"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Início Rápido: Enviar eventos de registro de contêiner privado à Grade de Eventos

A Grade de Eventos do Azure é um serviço de roteamento de eventos totalmente gerenciado que fornece consumo uniforme de eventos usando um modelo de publicação-assinatura. Neste início rápido, você usa a CLI do Azure para criar um registro de contêiner, assinar eventos de registro e, em seguida, implantar um aplicativo Web de exemplo para receber os eventos. Por fim, você dispara os eventos `push` e `delete` da imagem de contêiner e exibe a carga do evento no aplicativo de exemplo.

Após concluir as etapas neste artigo, os eventos enviados do registro de contêiner para a Grade de Eventos aparecerão no aplicativo Web de exemplo:

![Navegador da Web que renderiza o aplicativo Web de exemplo com três eventos recebidos][sample-app-01]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][azure-account] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Os comandos da CLI do Azure neste artigo são formatados para o shell de **Bash**. Se você estiver usando um shell diferente, como PowerShell ou Prompt de Comando, talvez seja necessário ajustar os caracteres de continuação de linha ou as linhas de atribuição de variáveis apropriadamente. Este artigo usa variáveis para minimizar a quantidade de edição de comando necessária.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual você implanta e gerencia os recursos do Azure. O comando [AZ Group Create][az-group-create] a seguir cria um grupo de  recursos chamado MyResource Group na região *eastus* . Se você quiser usar um nome diferente para o grupo de recursos, configure `RESOURCE_GROUP_NAME` para um valor diferente.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Em seguida, implante um registro de contêiner no grupo de recursos com os seguintes comandos. Antes de executar o comando [AZ ACR Create][az-acr-create] , defina `ACR_NAME` como um nome para o registro. O nome deve ser exclusivo no Azure e é restrito a 5 a 50 caracteres alfanuméricos.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Depois que o registro é criado, a CLI do Azure retorna uma saída semelhante à seguinte:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```

## <a name="create-an-event-endpoint"></a>Criar um ponto de extremidade de evento

Nesta seção, você usa um modelo do Resource Manager localizado em um repositório GitHub para implantar um aplicativo Web de exemplo pré-criado no Serviço de Aplicativo do Azure. Posteriormente, você assina os eventos da Grade de Eventos do registro e especifica esse aplicativo como o ponto de extremidade para o qual os eventos são enviados.

Para implantar o aplicativo de exemplo, defina `SITE_NAME` como um nome exclusivo para o aplicativo Web e execute os seguintes comandos. O nome do site deve ser exclusivo no Azure porque faz parte do FQDN (nome de domínio totalmente qualificado) do aplicativo Web. Em uma seção posterior, você navega para o FQDN do aplicativo em um navegador da Web para exibir os eventos do registro.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Depois que a implantação for realizada com êxito (isso pode demorar alguns minutos), abra um navegador e navegue até o aplicativo da Web para verificar se está em execução:

`http://<your-site-name>.azurewebsites.net`

Você deve ver o aplicativo de exemplo renderizado sem nenhuma mensagem de evento exibida:

![Navegador da Web mostrando aplicativo Web de exemplo sem eventos exibidos][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Assinar eventos de registro

Na Grade de Eventos, você assina um *tópico* para informar quais eventos quer acompanhar e para onde enviá-los. O comando [AZ eventgrid Event-Subscription Create][az-eventgrid-event-subscription-create] a seguir assina o registro de contêiner que você criou e especifica a URL do seu aplicativo Web como o ponto de extremidade para o qual ele deve enviar eventos. As variáveis de ambiente que você preencheu nas seções anteriores são reutilizadas aqui, portanto, nenhuma edição é necessária.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Quando a assinatura estiver concluída, você deverá ver uma saída semelhante à seguinte:

```JSON
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Disparar eventos de registro

Agora que o aplicativo de exemplo está em execução e você assinou o registro com a Grade de Eventos, você está pronto para gerar alguns eventos. Nesta seção, você usa as Tarefas de ACR para compilar e efetuar push de uma imagem de contêiner ao registro. As Tarefas de ACR são um recurso do Registro de Contêiner do Azure que permite compilar imagens de contêiner na nuvem, sem precisar do Mecanismo de Docker instalado no computador local.

### <a name="build-and-push-image"></a>Compilar e efetuar push de imagem

Execute o seguinte comando da CLI do Azure para compilar uma imagem de contêiner do conteúdo de um repositório GitHub. Por padrão, as Tarefas de ACR efetuam push automaticamente de uma imagem compilada com êxito para o Registro, o que gera o evento `ImagePushed`.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

Você deverá ver uma saída semelhante à seguinte, enquanto as Tarefas de ACR compilam e, em seguida, efetuam push da imagem. A seguinte saída de exemplo foi truncada por questões de brevidade.

```console
$ az acr build -r $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

Para verificar se a imagem compilada está no registro, execute o seguinte comando para exibir as marcas no repositório "myimage":

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

A marca "v1" da imagem que você compilou deve aparecer na saída, semelhante à seguinte:

```console
$ az acr repository show-tags --name $ACR_NAME --repository myimage
[
  "v1"
]
```

### <a name="delete-the-image"></a>Excluir a imagem

Agora, gere um `ImageDeleted` evento excluindo a imagem com o comando [AZ ACR Repository Delete][az-acr-repository-delete] :

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Você deve ver uma saída semelhante à seguinte, solicitando a confirmação para excluir o manifesto e as imagens associadas:

```console
$ az acr repository delete --name $ACR_NAME --image myimage:v1
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): y
```

## <a name="view-registry-events"></a>Exibir eventos do registro

Agora, você irá efetuar push de uma imagem e, em seguida, excluí-la. Navegue até o aplicativo Web do Visualizador da Grade de Eventos e você verá ambos os eventos `ImageDeleted` e `ImagePushed`. Também será possível ver um evento de validação de assinatura gerado pela execução do comando na seção [Assinar eventos de registro](#subscribe-to-registry-events).

A captura de tela a seguir mostra o aplicativo de exemplo com os três eventos e o evento `ImageDeleted` é expandido para mostrar os detalhes.

![Navegador da Web mostrando o aplicativo de exemplo com eventos ImagePushed e ImageDeleted][sample-app-03]

Parabéns! Se você visualizar os eventos `ImagePushed` e `ImageDeleted`, isso significa que o registro está enviando eventos para a Grade de Eventos e a Grade de Eventos está encaminhando esses eventos para o ponto de extremidade do aplicativo Web.

## <a name="clean-up-resources"></a>Limpar recursos

Após concluir os recursos criados neste início rápido, você poderá excluí-los com o seguinte comando da CLI do Azure. Quando você excluir um grupo de recursos, todos os recursos nele contidos serão permanentemente excluídos.

**AVISO**: essa operação é irreversível. Antes de executar o comando, certifique-se de que nenhum dos recursos contidos no grupo será mais necessário.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Esquema de eventos da Grade de Eventos

É possível encontrar a referência de esquema da mensagem de evento do Registro de Contêiner do Azure na documentação da Grade de Eventos:

[Esquema de evento da Grade de Eventos do Azure para Registro de Contêiner](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um registro de contêiner, compilou uma imagem com Tarefas de ACR, excluiu-a e consumiu os eventos do registro da Grade de Eventos com um aplicativo de exemplo. Em seguida, vá para o tutorial das Tarefas de ACR para saber mais sobre como compilar imagens de contêiner na nuvem, incluindo builds automatizados na atualização de imagem base:

> [!div class="nextstepaction"]
> [Compilar imagens de contêiner na nuvem com Tarefas de ACR](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
