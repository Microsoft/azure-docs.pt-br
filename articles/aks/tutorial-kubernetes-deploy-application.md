---
title: Tutorial do Kubernetes no Azure – Implantar um aplicativo
description: Neste tutorial do Serviço de Kubernetes do Azure (AKS), você deve implantar um aplicativo de vários contêineres em seu cluster usando uma imagem personalizada armazenada no Registro de Contêiner do Azure.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: be4d3fd298a7c08aa640585beb741bad18a840ef
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614336"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Tutorial: Execute aplicativos no AKS (Serviço de Kubernetes do Azure)

Kubernetes fornece uma plataforma distribuída para aplicativos em contêineres. Você compilar e implanta seus próprios aplicativos e serviços em um cluster Kubernetes e permite que o cluster gerencie a disponibilidade e a conectividade. Neste tutorial, parte quatro de sete, um aplicativo de exemplo é implantado em um cluster Kubernetes. Você aprenderá como:

> [!div class="checklist"]
> * Atualizar um arquivo de manifesto do Kubernetes
> * Executar um aplicativo no Kubernetes
> * Testar o aplicativo

Nos tutoriais adicionais, esse aplicativo é escalado horizontalmente e atualizado.

Este guia de início rápido pressupõe uma compreensão básica dos conceitos do Kubernetes. Para obter mais informações, confira [Principais conceitos do Kubernetes para o AKS (Serviço de Kubernetes do Azure)][kubernetes-concepts].

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um aplicativo foi empacotado em uma imagem de contêiner, essa imagem foi carregada no Registro de Contêiner do Azure e um cluster Kubernetes foi criado.

Para concluir este tutorial, você precisa do arquivo de manifesto do Kubernetes `azure-vote-all-in-one-redis.yaml` pré-criado. Esse arquivo foi baixado com o código-fonte do aplicativo em um tutorial anterior. Verifique se você clonou repositório e se você alterou os diretórios para o repositório clonado. Se você ainda não executou essas etapas e deseja continuar acompanhando, comece com o [Tutorial 1 – Criar imagens de contêiner][aks-tutorial-prepare-app].

Este tutorial exige a execução da CLI do Azure versão 2.0.53 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="update-the-manifest-file"></a>Atualizar o arquivo de manifesto

Nesses tutoriais, uma instância de Registro de Contêiner do Azure (ACR) armazena a imagem de contêiner para o aplicativo de exemplo. Para implantar o aplicativo, você deve atualizar o nome da imagem no arquivo de manifesto do Kubernetes para incluir o nome do servidor de logon do ACR.

Obtenha o nome do servidor de logon do ACR usando o comando [az acr list][az-acr-list] da seguinte maneira:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

O arquivo de manifesto de exemplo do repositório git clonado no primeiro tutorial usa o nome do servidor de logon *microsoft*. Verifique se você está no diretório *azure-votação-aplicativo-redis* clonado e, em seguida, abra o arquivo de manifesto com um editor de texto, como `vi`:

```console
vi azure-vote-all-in-one-redis.yaml
```

Substitua *microsoft* pelo seu nome de servidor de logon do ACR. Esse valor pode ser encontrado na linha 51 do arquivo de manifesto. O seguinte exemplo mostra o nome da imagem padrão:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Forneça seu próprio nome de servidor de logon do ACR para que o arquivo de manifesto se pareça com o exemplo a seguir:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Salve e feche o arquivo. Em `vi`, use `:wq`.

## <a name="deploy-the-application"></a>Implantar o aplicativo

Para implantar o aplicativo, use o comando [kubectl apply][kubectl-apply]. Esse comando analisa o arquivo de manifesto e cria objetos Kubernetes definidos. Especifique o arquivo de manifesto de exemplo, conforme mostrado no exemplo a seguir:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

A saída de exemplo a seguir mostra os recursos criados com êxito no cluster AKS:

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testar o aplicativo

Quando o aplicativo é executado, um serviço de Kubernetes expõe o front-end do aplicativo à Internet. A conclusão desse processo pode levar alguns minutos.

Para monitorar o andamento, use o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```console
kubectl get service azure-vote-front --watch
```

Inicialmente, o *EXTERNAL-IP* para o serviço *azure-vote-front* é mostrado como *pendente*:

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Quando o endereço *EXTERNAL-IP* for alterado de *pendente* para um endereço IP público real, use `CTRL-C` para interromper o processo de inspeção do `kubectl`. A seguinte saída de exemplo mostra um endereço IP público válido atribuído ao serviço:

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Para ver o aplicativo em ação, abra um navegador da Web no endereço IP externo do sue serviço:

![Imagem do cluster Kubernetes no Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Se o aplicativo não foi carregado, isso poderá ser devido a um problema de autorização com o registro da imagem. Para exibir o status de seus contêineres, use o comando `kubectl get pods`. Se as imagens de contêiner não puderem ser extraídas, confira [permitir o acesso ao Registro de Contêiner com um segredo do Kubernetes](https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, o aplicativo Azure vote de exemplo foi implantado em um cluster Kubernetes no AKS. Você aprendeu como:

> [!div class="checklist"]
> * Atualizar os arquivos de manifesto do Kubernetes
> * Executar um aplicativo no Kubernetes
> * Testar o aplicativo

Avance para o próximo tutorial para aprender como dimensionar um aplicativo Kubernetes e a infraestrutura do Kubernetes subjacente.

> [!div class="nextstepaction"]
> [Dimensionar um aplicativo do Kubernetes e sua infraestrutura][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
[kubernetes-concepts]: concepts-clusters-workloads.md
[kubernetes-service]: concepts-network.md#services
