---
title: Início Rápido – implantar um contêiner do Docker em Instâncias de Contêiner do Azure – CLI
description: Neste início rápido, você usa a CLI do Azure para implantar rapidamente um aplicativo Web em contêineres que é executado em uma instância de contêiner do Azure isolada
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 7a4a1c24211e644a796b4e60537978c327501383
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325778"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>Início Rápido: Implantar uma instância de contêiner no Azure usando a CLI do Azure

Use as Instâncias de Contêiner do Azure para executar contêineres do Docker sem servidor no Azure de maneira simples e rápida. Implante um aplicativo em uma instância de contêiner sob demanda quando você não precisa de uma plataforma de orquestração de contêiner completa como o Serviço de Kubernetes do Azure.

Neste início rápido, você usará a CLI do Azure para implantar um contêiner do Docker isolado e disponibilizar o respectivo aplicativo com um FQDN (nome de domínio totalmente qualificado). Alguns segundos depois de executar um comando único de implantação, é possível navegar para o aplicativo em execução no contêiner:

![Aplicativos implantados nas Instâncias de Contêiner do Azure exibidos no navegador][aci-app-browser]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][azure-account] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir esse guia de início rápido. Se você quer usá-lo localmente, recomendamos usar a versão 2.0.55 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Instâncias de Contêiner do Azure, assim como todos os recursos do Azure, precisam ser implantadas em um grupo de recursos. Os grupos de recursos facilitam organizar e gerenciar os recursos relacionados ao Azure.

Primeiramente, crie um grupo de recursos denominado *myResourceGroup* no local *eastus* com o seguinte comando [az group create][az-group-create]:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Criar um contêiner

Agora que tem um grupo de recursos, você pode executar um contêiner no Azure. Para criar uma instância de contêiner com a CLI do Azure, forneça um nome de grupo de recursos, um nome da instância de contêiner e uma imagem de contêiner do Docker para o comando [az container create][az-container-create]. Neste início rápido, você usa a imagem pública `mcr.microsoft.com/azuredocs/aci-helloworld`. Esta imagem empacota um pequeno aplicativo Web escrito no Node.js que veicula a uma página HTML estática.

Você pode expor seus contêineres à Internet especificando um ou mais portas a serem abertas, um rótulo de nome DNS ou ambos. Neste início rápido, você implanta um contêiner com um rótulo de nome DNS para que o aplicativo Web fique publicamente acessível.

Execute um comando similar ao seguinte para iniciar uma instância de contêiner. Defina um valor de `--dns-name-label` que seja exclusivo na região do Azure em que você criará a instância. Se você receber uma mensagem de erro “Rótulo de nome DNS não disponível”, tente usar um rótulo de nome DNS diferente.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

Em poucos segundos, você obterá uma resposta da CLI do Azure indicando que a implantação foi concluída. Verifique o status dele usando o comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

Ao executar o comando, o FQDN (nome de domínio totalmente qualificado) do contêiner e o estado de provisionamento dele são exibidos.

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Se o `ProvisioningState` do contêiner for **Êxito**, navegue até o FQDN dele no seu navegador. Se você encontrar uma página da Web semelhante à seguinte, parabéns! Você implantou com êxito um aplicativo em execução em um contêiner do Docker no Azure.

![Captura de tela de navegador mostrando aplicativo em execução em uma instância de contêiner do Azure][aci-app-browser]

Se o aplicativo não for exibido inicialmente, poderá ser necessário aguardar alguns segundos enquanto o DNS é propagado e, em seguida, tente atualizar seu navegador.

## <a name="pull-the-container-logs"></a>Acessar os logs de contêiner

Quando você precisar solucionar problemas de um contêiner ou do aplicativo que ele executa (ou apenas ver a saída dele), comece exibindo os logs da instância de contêiner.

Efetue pull nos logs da instância de contêiner com o comando [az container logs][az-container-logs]:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

A saída exibe os logs para o contêiner e deve mostrar as solicitações HTTP GET geradas quando o aplicativo é exibido no navegador.

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>Anexar fluxos de saída

Além de exibir os logs, é possível anexar a saída padrão local e os fluxos de erro padrão aos do contêiner.

Primeiro, execute o comando [az container attach][az-container-attach] para anexar seu console local os fluxos de saída do contêiner:

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

Depois de anexados, atualize seu navegador algumas vezes para gerar algumas saídas adicionais. Quando terminar, desanexe o console com `Control+C`. Você deverá ver uma saída semelhante ao seguinte:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando você conclui o contêiner, remova-o usando o comando [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Para verificar se o contêiner foi excluído, execute o comando [az container list](/cli/azure/container#az-container-list):

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

O contêiner **mycontainer** não deve aparecer na saída do comando. Se você não tiver outros contêineres no grupo de recursos, não será exibida nenhuma saída.

Se tiver concluído com o grupo de recursos *myResourceGroup* e todos os recursos que ele contém, exclua-o com o comando [az group delete][az-group-delete]:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou uma instância de contêiner do Azure usando uma imagem da Microsoft pública. Se você quiser criar uma imagem de contêiner e implantá-la usando um Registro de Contêiner do Azure privado, prossiga para o tutorial das Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Tutorial sobre Instâncias de Contêiner do Azure](./container-instances-tutorial-prepare-app.md)

Para experimentar as opções para contêineres em execução em um sistema de orquestração no Azure, veja os inícios rápidos do [AKS (Serviço de Kubernetes do Azure)][container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
