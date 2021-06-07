---
title: Desenvolver no AKS (serviço kubernetes do Azure) com o Helm
description: Use Helm com AKS e registro de contêiner do Azure para empacotar e executar contêineres de aplicativo em um cluster.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 4f5232920853908aa5ad714313ead201494caa0d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493061"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Início rápido: desenvolver no serviço de kubernetes do Azure (AKS) com Helm

O [Helm][helm] é uma ferramenta de empacotamento de software livre que ajuda você a instalar e gerenciar o ciclo de vida de aplicativos kubernetes. De forma semelhante aos gerenciadores de pacotes do Linux, como *apt* e *yum*, o Helm gerencia gráficos kubernetes, que são pacotes de recursos de kubernetes pré-configurados.

Neste guia de início rápido, você usará o Helm para empacotar e executar um aplicativo no AKS. Para obter mais detalhes sobre como instalar um aplicativo existente usando o Helm, consulte o guia de instruções [instalar aplicativos existentes com Helm no AKs][helm-existing] .

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita](https://azure.microsoft.com/free).
* A [CLI do Azure](/cli/azure/install-azure-cli) instalada.
* [Helm v3 instalado][helm-install].

## <a name="create-an-azure-container-registry"></a>Criar um Registro de Contêiner do Azure
Você precisará armazenar suas imagens de contêiner em um ACR (registro de contêiner do Azure) para executar seu aplicativo no cluster AKS usando o Helm. Forneça seu próprio nome de registro exclusivo no Azure e que contenha 5-50 caracteres alfanuméricos. A SKU *Basic* é um ponto de entrada de otimização de custo para fins de desenvolvimento que fornece um equilíbrio entre o armazenamento e taxa de transferência.

O exemplo abaixo usa [AZ ACR Create][az-acr-create] para criar um ACR chamado *MyHelmACR* no *MyResource* do SKU *básico* .

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

A saída será semelhante ao exemplo a seguir. Anote seu valor de *loginServer* para seu ACR, pois você o usará em uma etapa posterior. No exemplo abaixo, *myhelmacr.azurecr.Io* é o *loginServer* para *myhelmacr*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
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

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

Seu novo cluster AKS precisa acessar seu ACR para efetuar pull das imagens de contêiner e executá-las. Use o seguinte comando para:
* Crie um cluster AKS chamado *MyAKS* e anexe *MyHelmACR*.
* Conceda ao cluster *MyAKS* acesso ao seu ACR *MyHelmACR* .


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>Conectar-se ao cluster AKS

Para conectar um cluster kubernetes localmente, use o cliente de linha de comando kubernetes, [kubectl][kubectl]. `kubectl` Já está instalado se você usar Azure Cloud Shell. 

1. Instale `kubectl` localmente usando o `az aks install-cli` comando:

    ```azurecli
    az aks install-cli
    ```

2. Configure `kubectl` o para se conectar ao cluster kubernetes usando o `az aks get-credentials` comando. O exemplo de comando a seguir obtém as credenciais para o cluster AKS chamado *MyAKS* no grupo *MyResource*:  

    ```azurecli
    az aks get-credentials --resource-group MyResourceGroup --name MyAKS
    ```

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

Este guia de início rápido usa [um aplicativo de exemplo Node.js do repositório de exemplo Azure dev Spaces][example-nodejs]. Clone o aplicativo do GitHub e navegue até o `dev-spaces/samples/nodejs/getting-started/webfrontend` diretório.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Criar um Dockerfile

Crie um novo arquivo *Dockerfile* usando os seguintes comandos:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Criar e enviar por push o aplicativo de exemplo para o ACR

Usando o Dockerfile anterior, execute o comando [AZ ACR Build][az-acr-build] para compilar e enviar por push uma imagem para o registro. O `.` no final do comando define o local do Dockerfile (neste caso, o diretório atual).

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>Criar seu gráfico do Helm

Gere o gráfico do Helm usando o `helm create` comando.

```console
helm create webfrontend
```

Atualizar *WebFrontEnd/Values. YAML*:
* Substitua o loginServer do registro que você anotou em uma etapa anterior, como *myhelmacr.azurecr.Io*.
* Altere `image.repository` para `<loginServer>/webfrontend`
* Altere `service.type` para `LoadBalancer`

Por exemplo:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: myhelmacr.azurecr.io/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

Atualize `appVersion` para `v1` no *WebFrontEnd/Char. YAML*. Por exemplo,

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Executar o gráfico do Helm

Instale seu aplicativo usando o gráfico do Helm usando o `helm install` comando.

```console
helm install webfrontend webfrontend/
```

Demora alguns minutos para o serviço retornar um endereço IP público. Monitore o progresso usando o `kubectl get service` comando com o `--watch` argumento.

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Navegue até o balanceador de carga do aplicativo em um navegador usando o `<EXTERNAL-IP>` para ver o aplicativo de exemplo.

## <a name="delete-the-cluster"></a>Excluir o cluster

Use o comando [AZ Group Delete][az-group-delete] para remover o grupo de recursos, o cluster AKs, o registro de contêiner, as imagens de contêiner armazenadas no ACR e todos os recursos relacionados.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando você excluir o cluster, a entidade de serviço do Azure Active Directory usada pelo cluster do AKS não será removida. Para obter as etapas para remover a entidade de serviço, confira [Considerações sobre a entidade de serviço do AKS e sua exclusão][sp-delete].
> 
> Se você tiver usado uma identidade gerenciada, ela será gerenciada pela plataforma e não exigirá remoção.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar o Helm, consulte a documentação do Helm.

> [!div class="nextstepaction"]
> [Documentação do Helm][helm-documentation]

[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
