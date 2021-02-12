---
title: Instalar aplicativos existentes com o Helm no AKS
description: Saiba como usar a ferramenta de empacotamento Helm para implantar contêineres em um cluster do AKS (serviço kubernetes do Azure)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 12/07/2020
ms.author: zarhoads
ms.openlocfilehash: f12dffe0b538738a8f6dd00cd3d87d44da828f21
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779160"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Instalar aplicativos existentes com o Helm no serviço kubernetes do Azure (AKS)

O [Helm][helm] é uma ferramenta de empacotamento de software livre que ajuda você a instalar e gerenciar o ciclo de vida de aplicativos kubernetes. Assim como os gerenciadores de pacotes do Linux, como *apt* e *yum*, o Helm é usado para gerenciar os gráficos do kubernetes, que são pacotes de recursos kubernetes pré-configurados.

Este artigo mostra como configurar e usar o Helm em um cluster Kubernetes no AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Você também precisa da CLI do Helm instalada, que é o cliente que é executado no seu sistema de desenvolvimento. Ele permite que você inicie, pare e gerencie aplicativos com o Helm. Se você usa o Azure Cloud Shell, o a CLI do Helm já está instalada. Para obter instruções de instalação em sua plataforma local, consulte [instalando o Helm][helm-install].

> [!IMPORTANT]
> O Helm destina-se a ser executado em nós do Linux. Se você tiver nós do Windows Server em seu cluster, deverá garantir que Helm pods sejam agendadas apenas para execução em nós do Linux. Você também precisa garantir que todos os gráficos do Helm instalados também estejam agendados para execução nos nós corretos. Os comandos neste artigo usam [node-seletores] [K8S-node-selector] para garantir que os pods estejam agendados para os nós corretos, mas nem todos os gráficos de Helm podem expor um seletor de nó. Você também pode considerar o uso de outras opções em seu cluster, [como os][taints]seus contras.

## <a name="verify-your-version-of-helm"></a>Verifique sua versão do Helm

Use o `helm version` comando para verificar se você tem o Helm 3 instalado:

```console
helm version
```

O exemplo a seguir mostra o Helm versão 3.0.0 instalado:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>Instalar um aplicativo com o Helm v3

### <a name="add-helm-repositories"></a>Adicionar repositórios do Helm

Use o comando [Helm][helm-repo-add] do repositório para adicionar o repositório de *entrada-Nginx* .

```console
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>Localizar gráficos Helm

Os gráficos do Helm são usados para implantar aplicativos em um cluster Kubernetes. Para pesquisar gráficos Helm criados previamente, use o comando [Helm Search][helm-search] :

```console
helm search repo ingress-nginx
```

A saída de exemplo condensada a seguir mostra alguns dos gráficos do Helm disponíveis para uso:

```console
$ helm search repo ingress-nginx

NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
ingress-nginx/ingress-nginx     2.12.0          0.34.1          Ingress controller for Kubernetes using NGINX a...
```

Para atualizar a lista de gráficos, use o comando [helm repo update][helm-repo-update].

```console
helm repo update
```

O exemplo a seguir mostra uma atualização de repositório bem-sucedida:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Executar gráficos Helm

Para instalar gráficos com o Helm, use o comando de [instalação do Helm][helm-install-command] e especifique um nome de versão e o nome do gráfico a ser instalado. Para ver a instalação de um gráfico do Helm em ação, vamos instalar uma implantação básica do Nginx usando um gráfico do Helm.

```console
helm install my-nginx-ingress ingress-nginx/ingress-nginx \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

A saída do exemplo condensado a seguir mostra o status de implantação dos recursos do Kubernetes criados pelo gráfico do Helm:

```console
$ helm install my-nginx-ingress ingress-nginx/ingress-nginx \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller'
...
```

Use o `kubectl get services` comando para obter o *IP externo* do seu serviço.

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller
```

Por exemplo, o comando abaixo mostra o *IP externo* para o serviço *My-Nginx-ingress-ingress-Nginx-Controller* :

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller

NAME                                        TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)                      AGE   SELECTOR
my-nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.2.237   <EXTERNAL-IP>    80:31380/TCP,443:32239/TCP   72s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=my-nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>Listar versões

Para ver uma lista de versões instaladas no cluster, use o `helm list` comando.

```console
helm list
```

O exemplo a seguir mostra a versão do *My-Nginx-Ingres* implantada na etapa anterior:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Limpar os recursos

Quando você implanta um gráfico Helm, vários recursos do Kubernetes são criados. Esses recursos incluem pods, implantações e serviços. Para limpar esses recursos, use o comando [Helm Uninstall][helm-cleanup] e especifique o nome da versão, conforme encontrado no comando anterior `helm list` .

```console
helm uninstall my-nginx-ingress
```

O exemplo a seguir mostra que a versão nomeada *My-Nginx-Ingres* foi desinstalada:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como gerenciar implantações de aplicativo do Kubernetes usando o Helm, consulte a documentação do Helm.

> [!div class="nextstepaction"]
> [Documentação do Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md
