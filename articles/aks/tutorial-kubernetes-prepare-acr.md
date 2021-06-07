---
title: Tutorial de Kubernetes no Azure - Criar um registro de contêiner
description: Neste tutorial do Serviço de Kubernetes do Azure (AKS), você cria uma instância de Registro de Contêiner do Azure e carrega uma imagem de contêiner do aplicativo de exemplo.
services: container-service
ms.topic: tutorial
ms.date: 01/31/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fd53fab577797ad8bfdbf29b4a6d219e61ee3ef4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764253"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Tutorial: implantar e usar o Registro de Contêiner do Azure

O ACR (Registro de Contêiner do Azure) é um registro particular de imagens de contêiner. Um registro de contêiner privado permite criar e implantar seus aplicativos e o código personalizado com segurança. Neste tutorial, parte dois de sete, você pode implantar uma instância do ACR e enviar por push uma imagem de contêiner a ele. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma instância do Registro de Contêiner do Azure (ACR)
> * Marcar uma imagem de contêiner para ACR
> * Carregar a imagem para ACR
> * Exibir imagens no seu registro

Em tutoriais posteriores, essa instância do ACR será integrada a um cluster do Kubernetes no AKS e um aplicativo será implantado com base na imagem.

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

```console
docker images
```
A saída do comando acima mostra uma lista das imagens locais atuais:

```output
REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        7 minutes ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

Para usar a imagem de contêiner *azure-vote-front* com o ACR, a imagem precisa ser marcada com o endereço do servidor de logon do seu registro. Essa marca é usada para roteamento ao enviar imagens de contêiner por push a um registro da imagem.

Para obter o endereço do servidor de logon, use o comando [az acr list][az-acr-list] e consulte o *loginServer* da seguinte maneira:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Agora, marque a imagem local *azure-vote-front* com o endereço *acrLoginServer* do registro de contêiner. Para indicar a versão da imagem, adicione *:v1* no final do nome da imagem:

```console
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v1
```

Para verificar se as marcas foram aplicadas, execute [docker images][docker-images] novamente.

```console
docker images
```

Uma imagem é marcada com o endereço de instância do ACR e um número de versão.

```
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front    v1                  84b41c268ad9        16 minutes ago      944MB
mycontainerregistry.azurecr.io/azure-vote-front v1                  84b41c268ad9        16 minutes ago      944MB
mcr.microsoft.com/oss/bitnami/redis             6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                      python3.6           a16ce562e863        6 weeks ago         944MB
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

```output
Result
----------------
azure-vote-front
```

Para ver as marcas para uma imagem específica, use o comando [az acr repository show-tags][az-acr-repository-show-tags] da seguinte maneira:

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

A saída de exemplo a seguir mostra a imagem *v1* marcada em uma etapa anterior:

```output
Result
--------
v1
```

Agora você tem uma imagem de contêiner que é armazenada em uma instância privada de Registro de Contêiner do Azure. Essa imagem é implantada do ACR para um cluster Kubernetes no próximo tutorial.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um Registro de Contêiner do Azure e enviou por push uma imagem para ser usada em um cluster do AKS. Você aprendeu a:

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
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-list]: /cli/azure/acr#az_acr_list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
