---
title: Tutorial do Kubernetes no Azure - Atualizar um aplicativo
description: Neste tutorial do Serviço de Kubernetes do Azure (AKS), você aprenderá como atualizar uma implantação existente do aplicativo para AKS com uma nova versão do código do aplicativo.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: b645fc9f67229d087a5d1655f733e2f3e50d4471
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614386"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Tutorial: Atualizar um aplicativo no AKS (Serviço de Kubernetes do Azure)

Depois que um aplicativo foi implantado no Kubernetes, ele pode ser atualizado especificando uma nova imagem de contêiner ou versão de imagem. Uma atualização é preparada para que apenas uma parte da implantação seja atualizada ao mesmo tempo. Essa atualização em etapas permite que o aplicativo continue em execução durante a atualização. Ela também oferece um mecanismo de reversão, caso ocorra uma falha de implantação.

Neste tutorial, parte seis de sete, o aplicativo de exemplo Azure Vote é atualizado. Você aprenderá como:

> [!div class="checklist"]
> * Atualizar o código do aplicativo front-end
> * Criar uma imagem de contêiner atualizada
> * Efetuar push da imagem de contêiner para o Registro de Contêiner do Azure
> * Implantar a imagem de contêiner atualizada

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um aplicativo foi empacotado em uma imagem de contêiner. Essa imagem foi carregada no Registro de Contêiner do Azure e você criou um cluster do AKS. O aplicativo foi então implantado no cluster do AKS.

Um repositório de aplicativo também foi clonado, que inclui o código-fonte do aplicativo e um arquivo do Docker Compose pré-criado usado neste tutorial. Verifique se você criou um clone do repositório e se alterou os diretórios para o diretório clonado. Se você ainda não concluiu essas etapas e deseja continuar acompanhando, comece com o [Tutorial 1 – Criar mensagens de contêiner][aks-tutorial-prepare-app].

Este tutorial exige a execução da CLI do Azure versão 2.0.53 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="update-an-application"></a>Atualizar um aplicativo

Vamos fazer uma alteração no aplicativo de exemplo e, em seguida, atualize a versão já implantada no cluster do AKS. Verifique se você está no diretório *azure-voting-app-redis* clonado. O código-fonte do aplicativo de exemplo pode então ser encontrado no diretório *azure-vote*. Abra o arquivo *config_file.cfg* do arquivo com um editor, como `vi`:

```console
vi azure-vote/azure-vote/config_file.cfg
```

Altere os valores de *VOTE1VALUE* e *VOTE2VALUE* para valores diferentes, como cores. O seguinte exemplo mostra os valores atualizados:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Salve e feche o arquivo. Em `vi`, use `:wq`.

## <a name="update-the-container-image"></a>Atualizar a imagem do contêiner

Para recriar a imagem de front-end e testar o aplicativo atualizado, use [docker-compose][docker-compose]. O argumento `--build` é usado para instruir o Docker Compose a recriar a imagem do aplicativo:

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>Testar o aplicativo localmente

Para verificar que a atualização imagem de contêiner atualizada mostra as alterações, abra um navegador da Web local para `http://localhost:8080`.

![Imagem do cluster Kubernetes no Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

Os valores atualizados fornecidos no arquivo *config_file.cfg* são exibidos no aplicativo em execução.

## <a name="tag-and-push-the-image"></a>Marcar e enviar imagem por push

Para usar corretamente a imagem atualizada, marque a imagem *azure-vote-front* com o nome do servidor de logon do registro do ACR. Obter o nome do servidor de logon com o comando [az acr list](/cli/azure/acr):

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Utilize a [docker tag][docker-tag] para marcar a imagem. Substitua `<acrLoginServer>` pelo nome do servidor de logon do ACR ou pelo nome de host do registro público e atualize a versão da imagem para *:v2* da seguinte maneira:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Agora use [docker push][docker-push] para fazer upload da imagem no registro. Substitua `<acrLoginServer>` pelo nome do servidor de logon do ACR.

> [!NOTE]
> Se você tiver problemas de push para o registro do ACR, verifique se você ainda está conectado. Execute o comando [az acr login][az-acr-login] usando o nome do Registro de Contêiner do Azure criado na etapa [Criar um Registro de Contêiner do Azure](tutorial-kubernetes-prepare-acr.md#create-an-azure-container-registry). Por exemplo, `az acr login --name <azure container registry name>`.

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>Implantar o aplicativo atualizado

Para fornecer o tempo de atividade máximo, várias instâncias do pod de aplicativos precisam estar em execução. Verifique o número de instâncias de front-end em execução com o comando [kubectl get pods][kubectl-get]:

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Caso não tenha vários pods de front-end, dimensione a implantação do *azure-vote-front* da seguinte maneira:

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

Para atualizar o aplicativo, utilize o comando [kubectl set][kubectl-set]. Atualize `<acrLoginServer>` com o servidor de logon ou com o nome do host do registro de contêiner e especifique a versão do aplicativo *v2*:

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Para monitorar a implantação, use o comando [kubectl get pod][kubectl-get]. Conforme o aplicativo atualizado é implantado, os pods são encerrados e recriados com a nova imagem de contêiner.

```console
kubectl get pods
```

A saída de exemplo a seguir mostra os pods de finalização e as novas instâncias em execução conforme o andamento da implantação:

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>Testar o aplicativo atualizado

Para exibir o aplicativo de atualização, primeiro obtenha o endereço IP externo do serviço `azure-vote-front`:

```console
kubectl get service azure-vote-front
```

Agora abra um navegador da Web local no endereço IP do serviço:

![Imagem do cluster Kubernetes no Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você atualizou um aplicativo e distribuiu essa atualização para um cluster do AKS. Você aprendeu como:

> [!div class="checklist"]
> * Atualizar o código do aplicativo front-end
> * Criar uma imagem de contêiner atualizada
> * Efetuar push da imagem de contêiner para o Registro de Contêiner do Azure
> * Implantar a imagem de contêiner atualizada

Avance para o próximo tutorial para saber como atualizar um cluster do AKS para uma nova versão do Kubernetes.

> [!div class="nextstepaction"]
> [Atualizar o Kubernetes][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
