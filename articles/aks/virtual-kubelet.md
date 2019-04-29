---
title: Executar Kubelet Virtual em um cluster AKS (Serviço de Kubernetes do Azure)
description: Saiba como usar o Virtual Kubelet com o Serviço de Kubernetes do Azure (AKS) para executar contêineres do Linux e do Windows em Instâncias de Contêiner do Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/14/2018
ms.author: iainfou
ms.openlocfilehash: a6a2fb246e407d6ea240ff40f4d2fa2b1b780931
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023706"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Usar o Virtual Kubelet com o serviço de Kubernetes do Azure (AKS)

ACIs (Instâncias de Contêiner do Azure) fornecem um ambiente hospedado para execução de contêineres no Azure. Ao utilizar ACI, não é necessário gerenciar a infraestrutura de computação subjacente, o Azure lida com esse gerenciamento para você. Ao executar contêineres em ACI, você é cobrado por segundo para cada contêiner em execução.

Ao usar o provedor Kubelet Virtual para Instâncias de Contêiner do Azure, os contêineres do Linux e do Windows podem ser agendados em uma instância de contêiner, como se fosse um nó Kubernetes padrão. Essa configuração permite que você aproveite os recursos dos Kubernetes, além do valor de gerenciamento e a economia das instâncias de contêiner.

> [!NOTE]
> O AKS agora tem suporte interno para o agendamento de contêineres em ACI, chamado de *nós virtuais*. Esses nós virtuais atualmente dão suporte a instâncias de contêiner do Linux. Se precisar agendar instâncias de contêiner do Windows, continue usando o Virtual Kubelet. Caso contrário, você deve usar os nós virtuais, em vez das instruções do Virtual Kubelet manual observadas neste artigo. Você pode começar com os nós virtuais usando a [CLI do Azure][virtual-nodes-cli] ou o [portal do Azure][virtual-nodes-portal].
>
> Kubelet Virtual é um projeto experimental de software livre e deve ser usado como tal. Para contribuir, registrar problemas e ler mais sobre kubelet virtual, confira o [projeto Kubelet Virtual do GitHub][vk-github].

## <a name="prerequisite"></a>Pré-requisito

Este documento presume que você tenha um cluster AKS. Se você precisar de um cluster AKS, consulte o [início rápido do AKS (Serviço de Kubernetes do Azure)][aks-quick-start].

Você também precisa da CLI do Azure versão **2.0.33** ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Para instalar o Virtual Kubelet, [Helm](https://docs.helm.sh/using_helm/#installing-helm) também é necessário.

### <a name="for-rbac-enabled-clusters"></a>Para clusters habilitados para RBAC

Se o cluster do AKS for habilitado para o RBAC, você deverá criar uma conta de serviço e a associação de função para uso com o Tiller. Para saber mais, confira [Controle de acesso baseado em função do Helm][helm-rbac]. Para criar uma conta de serviço e uma associação de função, crie um arquivo chamado *rbac-virtual-kubelet.yaml* e cole a seguinte definição:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Aplique a conta de serviço e a associação com [kubectl apply][kubectl-apply] e especifique o arquivo *rbac-virtual-kubelet.yaml*, conforme mostrado no seguinte exemplo:

```
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Configure o Helm para usar a conta de serviço do tiller:

```console
helm init --service-account tiller
```

Agora, você pode continuar a instalar o Virtual Kubelet no cluster AKS.

## <a name="installation"></a>Instalação

Use o comando [az aks install-conector][aks-install-connector] para instalar o Kubelet Virtual. O exemplo a seguir implanta o conector do Linux e do Windows.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

Esses argumentos estão disponíveis para o comando `aks install-connector`.

| Argumento: | DESCRIÇÃO | Obrigatório |
|---|---|:---:|
| `--connector-name` | Nome do conector ACI.| Sim |
| `--name` `-n` | Nome do cluster gerenciado. | Sim |
| `--resource-group` `-g` | Nome do grupo de recursos. | Sim |
| `--os-type` | Tipo de sistema operacional de instâncias de contêiner. Valores permitidos: Ambos, Linux, Windows. Padrão: Linux. | Não  |
| `--aci-resource-group` | O grupo de recursos no qual criar os grupos de contêiner do ACI. | Não  |
| `--location` `-l` | O local para criar os grupos de contêiner do ACI. | Não  |
| `--service-principal` | A entidade de serviço usada para autenticação em APIs do Azure. | Não  |
| `--client-secret` | Segredo associado à entidade de serviço. | Não  |
| `--chart-url` | URL de um gráfico Helm que instala o conector ACI. | Não  |
| `--image-tag` | A marca de imagem da imagem de contêiner do kubelet virtual. | Não  |

## <a name="validate-virtual-kubelet"></a>Validar Kubelet Virtual

Para validar a instalação do Kubelet Virtual, retorne uma lista de nós Kubernetes usando o comando [kubectl get nodes][kubectl-get].

```
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Executar contêiner do Linux

Crie um arquivo chamado `virtual-kubelet-linux.yaml` e copie no YAML a seguir. Observe que [nodeSelector][node-selector] e [toleration][toleration] estão sendo usados para agendar o contêiner no nó.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Execute o aplicativo com o comando [kubectl create][kubectl-create].

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Use o comando [kubectl get pods][kubectl-get] com o argumento `-o wide` para gerar uma lista dos pods com o nó agendado. Observe que o pod `aci-helloworld` foi agendado no nó `virtual-kubelet-virtual-kubelet-linux`.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Executar contêiner do Windows

Crie um arquivo chamado `virtual-kubelet-windows.yaml` e copie no YAML a seguir. Observe que [nodeSelector][node-selector] e [toleration][toleration] estão sendo usados para agendar o contêiner no nó.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Execute o aplicativo com o comando [kubectl create][kubectl-create].

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Use o comando [kubectl get pods][kubectl-get] com o argumento `-o wide` para gerar uma lista dos pods com o nó agendado. Observe que o pod `nanoserver-iis` foi agendado no nó `virtual-kubelet-virtual-kubelet-win`.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Remover Kubelet Virtual

Use o comando [az aks remove-conector][aks-remove-connector] para remover o Kubelet Virtual. Substitua os valores de argumento pelo nome do conector, o cluster AKS e o grupo de recursos do cluster AKS.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

> [!NOTE]
> Se você encontrar erros ao remover ambos os conectores do sistema operacional, ou se quiser remover apenas o conector do sistema operacional Windows ou Linux, você pode especificar manualmente o tipo de sistema operacional. Adicione o parâmetro `--os-type` ao comando `az aks remove-connector` anterior e especifique `Windows` ou `Linux`.

## <a name="next-steps"></a>Próximas etapas

Para possíveis problemas com o Kubelet Virtual, consulte o [Peculiaridades e soluções alternativas conhecidas][vk-troubleshooting]. Para relatar problemas com o Kubelet Virtual, [abra um problema do GitHub][vk-issues].

Leia mais sobre o Virtual Kubelet no [projeto GitHub do Virtual Kubelet][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
