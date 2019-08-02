---
title: Tutorial de Kubernetes no Azure - Criar um registro de contêiner
description: Neste tutorial do Serviço de Kubernetes do Azure (AKS), você cria uma instância de Registro de Contêiner do Azure e carrega uma imagem de contêiner do aplicativo de exemplo.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 5089326af1d7f6e057667cd916f35de92bf517ef
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614238"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Tutorial: Implantar e usar o Registro de Contêiner do Azure

O ACR (Registro de Contêiner do Azure) é um registro particular de imagens de contêiner. Um registro de contêiner privado permite criar e implantar seus aplicativos e o código personalizado com segurança. Neste tutorial, parte dois de sete, você pode implantar uma instância do ACR e enviar por push uma imagem de contêiner a ele. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma instância do Registro de Contêiner do Azure (ACR)
> * Marcar uma imagem de contêiner para ACR
> * Carregar a imagem para ACR
> * Exibir imagens no seu registro

Em tutoriais adicionais, essa instância do ACR será integrada a um cluster do Kubernetes no AKS e um aplicativo será implantado com base na imagem.

## <a name="before-you-begin"></a>Antes de começar

No [tutorial anterior][aks-tutorial-prepare-app], uma imagem de contêiner foi criada para um aplicativo de Votação do Azure simples. Se você não tiver criado a imagem do aplicativo de Votação do Azure, retorne ao [Tutorial 1 – Criar imagens de contêiner][aks-tutorial-prepare-app].

Este tutorial exige a execução da CLI do Azure versão 2.0.53 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-an-azure-container-registry"></a>Criar um Registro de Contêiner do Azure

Para criar um Registro de Contêiner do Azure, primeiro você precisa de um grupo de recursos. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create][az-group-create]. No exemplo a seguir, um grupo de recursos chamado *myResourceGroup* é criado na região *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie uma instância do Registro de Contêiner do Azure com o comando [az acr create][az-acr-create] e forneça seu próprio nome de registro. O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. No restante deste tutorial, `<acrName>` é usado como um espaço reservado para o nome do registro de contêiner. Forneça seu próprio nome de registro exclusivo. A SKU *Basic* é um ponto de entrada de otimização de custo para fins de desenvolvimento que fornece um equilíbrio entre o armazenamento e taxa de transferência.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>Logon no registro de contêiner

Para usar a instância do ACR, você deve primeiro fazer logon. Use o comando [az acr login][az-acr-login] e forneça o nome exclusivo especificado para o registro de contêiner na etapa anterior.

```azurecli
az acr login --name <acrName>
```

O comando retorna uma mensagem de *Logon bem-sucedido* quando é concluído.

## <a name="tag-a-container-image"></a>Marcar uma imagem de contêiner

Para ver uma lista das imagens locais atuais, use o comando [docker images][docker-images]:

```
$ docker images

REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Para usar a imagem de contêiner *azure-vote-front* com o ACR, a imagem precisa ser marcada com o endereço do servidor de logon do seu registro. Essa marca é usada para roteamento ao enviar imagens de contêiner por push a um registro da imagem.

Para obter o endereço do servidor de logon, use o comando [az acr list][az-acr-list] e consulte o *loginServer* da seguinte maneira:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Agora, marque a imagem local *azure-vote-front* com o endereço *acrloginServer* do registro de contêiner. Para indicar a versão da imagem, adicione *:v1* no final do nome da imagem:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Para verificar se as marcas foram aplicadas, execute [docker images][docker-images] novamente. Uma imagem é marcada com o endereço de instância do ACR e um número de versão.

```
$ docker images

REPOSITORY                                           TAG           IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest        eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry.azurecr.io/azure-vote-front      v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest        a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask         788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Efetuar push de imagens para registro

Com a imagem criada e marcada, envie por push a imagem *azure-vote-front* para a instância do ACR. Use [docker push][docker-push] e forneça seu próprio endereço do *acrLoginServer* para o nome da imagem da seguinte maneira:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

A conclusão do envio por push da imagem para o ACR pode levar alguns minutos.

## <a name="list-images-in-registry"></a>Lista de imagens no registro

Para retornar uma lista das imagens que foram enviadas por push para a instância do ACR, use o comando [az acr repository list][az-acr-repository-list]. Forneça seu próprio `<acrName>` da seguinte maneira:

```azurecli
az acr repository list --name <acrName> --output table
```

A saída de exemplo a seguir lista a imagem *azure-vote-front* como disponível no registro:

```
Result
----------------
azure-vote-front
```

Para ver as marcas para uma imagem específica, use o comando [az acr repository show-tags][az-acr-repository-show-tags] da seguinte maneira:

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

A saída de exemplo a seguir mostra a imagem *v1* marcada em uma etapa anterior:

```
Result
--------
v1
```

Agora você tem uma imagem de contêiner que é armazenada em uma instância privada de Registro de Contêiner do Azure. Essa imagem é implantada do ACR para um cluster Kubernetes no próximo tutorial.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um Registro de Contêiner do Azure e enviou por push uma imagem para ser usada em um cluster do AKS. Você aprendeu como:

> [!div class="checklist"]
> * Criar uma instância do Registro de Contêiner do Azure (ACR)
> * Marcar uma imagem de contêiner para ACR
> * Carregar a imagem para ACR
> * Exibir imagens no seu registro

Avance para o próximo tutorial para aprender como implantar um cluster do Kubernetes no Azure.

> [!div class="nextstepaction"]
> [Implantar um cluster do Kubernetes][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az-acr-login
[az-acr-list]: https://docs.microsoft.com/cli/azure/acr#az-acr-list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
