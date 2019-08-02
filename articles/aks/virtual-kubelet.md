---
title: Executar Kubelet Virtual em um cluster AKS (Serviço de Kubernetes do Azure)
description: Saiba como usar o Virtual Kubelet com o Serviço de Kubernetes do Azure (AKS) para executar contêineres do Linux e do Windows em Instâncias de Contêiner do Azure.
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: f18992be353d2d6cc739412d98ccd97d5e78d4c7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613867"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Usar o Virtual Kubelet com o serviço de Kubernetes do Azure (AKS)

ACIs (Instâncias de Contêiner do Azure) fornecem um ambiente hospedado para execução de contêineres no Azure. Ao utilizar ACI, não é necessário gerenciar a infraestrutura de computação subjacente, o Azure lida com esse gerenciamento para você. Ao executar contêineres em ACI, você é cobrado por segundo para cada contêiner em execução.

Ao usar o provedor Kubelet Virtual para Instâncias de Contêiner do Azure, os contêineres do Linux e do Windows podem ser agendados em uma instância de contêiner, como se fosse um nó Kubernetes padrão. Essa configuração permite que você aproveite os recursos dos Kubernetes, além do valor de gerenciamento e a economia das instâncias de contêiner.

> [!NOTE]
> O AKS agora tem suporte interno para o agendamento de contêineres em ACI, chamado de *nós virtuais*. Esses nós virtuais atualmente dão suporte a instâncias de contêiner do Linux. Se precisar agendar instâncias de contêiner do Windows, continue usando o Virtual Kubelet. Caso contrário, você deve usar os nós virtuais, em vez das instruções do Virtual Kubelet manual observadas neste artigo. Você pode começar com os nós virtuais usando o [CLI do Azure][virtual-nodes-cli] or [Azure portal][virtual-nodes-portal].
>
> Kubelet Virtual é um projeto experimental de software livre e deve ser usado como tal. Para contribuir, problemas de arquivos e ler mais sobre o virtual kubelet, consulte o [projeto GitHub de Virtual Kubelet][vk-github].

## <a name="before-you-begin"></a>Antes de começar

Este documento presume que você tenha um cluster AKS. Se você precisar um cluster do AKS, consulte o [início rápido do serviço de Kubernetes do Azure (AKS)][aks-quick-start].

Você também precisa da versão da CLI do Azure **2.0.65** ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Para instalar o Virtual Kubelet, instale e configure [Helm][aks-helm] no cluster do AKS. Certifique-se de que o Tiller está [configurado para uso com o RBAC Kubernetes](#for-rbac-enabled-clusters), se necessário.

### <a name="register-container-instances-feature-provider"></a>Registrar o provedor de recursos de instâncias de contêiner

Se você não tiver usado o serviço de instância de contêiner do Azure (ACI), registre o provedor de serviço com sua assinatura. Você pode verificar o status do ACI provedor registro usando o [lista de provedor az][az-provider-list] de comando, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

O provedor *Microsoft.ContainerInstance* deve relatar como *registrado*, conforme mostrado na saída de exemplo a seguir:

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Se o provedor é exibido como *NotRegistered*, registre o provedor usando o [registro de provedor az][az-provider-register] conforme mostrado no exemplo a seguir:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>Para clusters habilitados para RBAC

Se o cluster do AKS for habilitado para o RBAC, você deverá criar uma conta de serviço e a associação de função para uso com o Tiller. Para obter mais informações, consulte [controle de acesso baseado em função do Helm][helm-rbac]. Para criar uma conta de serviço e uma associação de função, crie um arquivo chamado *rbac-virtual-kubelet.yaml* e cole a seguinte definição:

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

Aplicam-se a conta de serviço e associação com [aplicar kubectl][kubectl-apply] e especifique seu *rbac virtual kubelet.yaml* de arquivos, conforme mostrado no exemplo a seguir:

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Configure o Helm para usar a conta de serviço do tiller:

```console
helm init --service-account tiller
```

Agora, você pode continuar a instalar o Virtual Kubelet no cluster AKS.

## <a name="installation"></a>Instalação

Use o [az aks install-conector][aks-install-connector] comando para instalar o Virtual Kubelet. O exemplo a seguir implanta o conector do Linux e do Windows.

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

Esses argumentos estão disponíveis para o [az aks install-conector][aks-install-connector] comando.

| Argumento: | DESCRIÇÃO | Obrigatório |
|---|---|:---:|
| `--connector-name` | Nome do conector ACI.| Sim |
| `--name` `-n` | Nome do cluster gerenciado. | Sim |
| `--resource-group` `-g` | Nome do grupo de recursos. | Sim |
| `--os-type` | Tipo de sistema operacional de instâncias de contêiner. Valores permitidos: Ambos, Linux, Windows. Padrão: Linux. | Não |
| `--aci-resource-group` | O grupo de recursos no qual criar os grupos de contêiner do ACI. | Não |
| `--location` `-l` | O local para criar os grupos de contêiner do ACI. | Não |
| `--service-principal` | A entidade de serviço usada para autenticação em APIs do Azure. | Não |
| `--client-secret` | Segredo associado à entidade de serviço. | Não |
| `--chart-url` | URL de um gráfico Helm que instala o conector ACI. | Não |
| `--image-tag` | A marca de imagem da imagem de contêiner do kubelet virtual. | Não |

## <a name="validate-virtual-kubelet"></a>Validar Kubelet Virtual

Para validar que o Virtual Kubelet tenha sido instalado, retornar uma lista de nós de Kubernetes usando o [kubectl obter nós][kubectl-get] comando:

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Executar contêiner do Linux

Crie um arquivo chamado `virtual-kubelet-linux.yaml` e copie no YAML a seguir. Observe que um [nodeSelector][node-selector] and [toleration][toleration] estão sendo usados para agendar o contêiner no nó.

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

Execute o aplicativo com o [kubectl criar][kubectl-create] comando.

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Use o [kubectl get pods][kubectl-get] com o `-o wide` argumento para gerar uma lista de pods com o nó agendado. Observe que o pod `aci-helloworld` foi agendado no nó `virtual-kubelet-virtual-kubelet-linux`.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Executar contêiner do Windows

Crie um arquivo chamado `virtual-kubelet-windows.yaml` e copie no YAML a seguir. Observe que um [nodeSelector][node-selector] and [toleration][toleration] estão sendo usados para agendar o contêiner no nó.

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

Execute o aplicativo com o [kubectl criar][kubectl-create] comando.

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Use o [kubectl get pods][kubectl-get] com o `-o wide` argumento para gerar uma lista de pods com o nó agendado. Observe que o pod `nanoserver-iis` foi agendado no nó `virtual-kubelet-virtual-kubelet-windows`.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Remover Kubelet Virtual

Use o [az aks remover-conexão][aks-remove-connector] comando para remover o Virtual Kubelet. Substitua os valores de argumento pelo nome do conector, o cluster AKS e o grupo de recursos do cluster AKS.

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> Se você encontrar erros ao remover ambos os conectores do sistema operacional, ou se quiser remover apenas o conector do sistema operacional Windows ou Linux, você pode especificar manualmente o tipo de sistema operacional. Adicione o parâmetro `--os-type` ao comando `az aks remove-connector` anterior e especifique `Windows` ou `Linux`.

## <a name="next-steps"></a>Próximas etapas

Para possíveis problemas com o Virtual Kubelet, consulte o [conhecidos quirks e soluções alternativas][vk-troubleshooting]. To report problems with the Virtual Kubelet, [open a GitHub issue][vk-issues].

Leia mais sobre o Virtual Kubelet na [projeto GitHub de Virtual Kubelet][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md
[aks-helm]: kubernetes-helm.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register

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
