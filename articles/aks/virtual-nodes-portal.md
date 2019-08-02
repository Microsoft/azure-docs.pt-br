---
title: Criar nós virtuais usando o portal no AKS (Serviços de Kubernetes do Azure)
description: Saiba como usar o portal do Azure para criar um cluster do AKS (Serviços de Kubernetes do Azure) que usa nós virtuais para executar pods.
services: container-service
author: mlearned
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8752d888e24e7135d488be6d1b377070a30fe4eb
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613837"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Criar e configurar um cluster do AKS (Serviços de Kubernetes do Azure) para usar nós virtuais no portal do Azure

Para implantar rapidamente as cargas de trabalho em um cluster do AKS (Serviço de Kubernetes do Azure) é possível usar nós virtuais. Com nós virtuais, você tem provisionamento rápido de pods e paga somente por segundo pelo seu tempo de execução. Em um cenário de dimensionamento, não é necessário aguardar o dimensionador automático de cluster do Kubernetes para implantar nós de computação de VM para executar os pods adicionais. Nós virtuais têm suporte somente conosco e pods do Linux.

Este artigo mostra como criar e configurar os recursos de rede virtual e um cluster do AKS com nós virtuais habilitados.

## <a name="before-you-begin"></a>Antes de começar

Nós virtuais permitem a comunicação de rede entre pods executados em ACI e o cluster do AKS. Para fornecer essa comunicação, uma sub-rede de rede virtual é criada e permissões delegadas são atribuídas. Nós virtuais só funcionam com clusters do AKS criados usando rede *avançada*. Por padrão, os clusters do AKS são criados com rede *básica*. Este artigo mostra como criar uma rede virtual e sub-redes para então implantar um cluster do AKS que usa rede avançada.

Se você não tiver usado anteriormente ACI, registre o provedor de serviço com sua assinatura. Você pode verificar o status do ACI provedor registro usando o [lista de provedor az][az-provider-list] de comando, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

O provedor *Microsoft.ContainerInstance* deve relatar como *registrado*, conforme mostrado na saída de exemplo a seguir:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Se o provedor é exibido como *NotRegistered*, registrar o provedor usando o [az registrar provedor] [az-provider-register] conforme mostrado no exemplo a seguir:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Disponibilidade regional

As regiões a seguir têm suporte para implantações de nó virtual:

* Leste da Austrália (australiaeast)
* Centro dos EUA (centralus)
* Leste dos EUA (eastus)
* Leste dos EUA 2 (eastus2)
* Leste do Japão (japaneast)
* Norte da Europa (northeurope)
* Sudeste da Ásia (southeastasia)
* Centro-Oeste dos EUA (westcentralus)
* Europa Ocidental (westeurope)
* Oeste dos EUA (westus)
* Oeste dos EUA 2 (westus2)

## <a name="known-limitations"></a>Limitações conhecidas
Funcionalidade de nós virtual é muito dependente de conjunto de recursos do ACI. Os cenários a seguir ainda não têm suporte com os nós virtuais

* Usando a entidade de serviço para as imagens ACR de pull. [Solução alternativa](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) é usar [segredos do Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Limitações de rede virtual](../container-instances/container-instances-vnet.md) incluindo emparelhamento de rede virtual, as políticas de rede do Kubernetes e o tráfego de saída à internet com grupos de segurança de rede.
* Contêineres de init
* [Aliases de host](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumentos](../container-instances/container-instances-exec.md#restrictions) para exec na ACI
* [Daemonsets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) não implantará os pods para o nó virtual
* [Nós do Windows Server (atualmente em visualização no AKS)](windows-container-cli.md) juntamente com os nós virtuais não têm suporte. Você pode usar os nós virtuais agendar contêineres do Windows Server sem a necessidade de nós do Windows Server em um cluster AKS.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Serviço Kubernetes**.

Na página **Noções básicas**, configure as seguintes opções:

- *DETALHES DO PROJETO*: escolha uma assinatura do Azure e marque ou crie um grupo de recursos do Azure, por exemplo, *meuGrupodeRecursos*. Insira um **nome do cluster do Kubernetes**, como *myAKSCluster*.
- *DETALHES DO CLUSTER*: escolha uma região, a versão do Kubernetes e o prefixo de nome DNS para o cluster do AKS.
- *POOL DE NÓ PRIMÁRIO*: escolha um tamanho de VM para os nós de AKS. O tamanho da VM **não pode** ser alterado após a implantação de um cluster AKS.
     - Selecione o número de nós para implantação no cluster. Para este artigo, defina a **Contagem de nós** como *1*. A contagem de nós **pode** ser ajustada após a implantação do cluster.

Clique em **Avançar: Escala**.

Sobre o **dimensionamento** página, selecione *habilitado* sob **nós virtuais**.

![Criar um cluster do AKS e habilitar os nós virtuais](media/virtual-nodes-portal/enable-virtual-nodes.png)

Por padrão, cria-se uma entidade de serviço do Azure Active Directory. Essa entidade de serviço é usada na comunicação e na integração do cluster com outros serviços do Azure.

O cluster também é configurado para acesso avançado à rede. Os nós virtuais são configurados para usar sua própria sub-rede da rede virtual do Azure. Essa sub-rede tem permissões delegadas para se conectar a recursos do Azure entre o cluster do AKS. Se você ainda não tiver uma sub-rede delegada, o portal do Azure criará e configurará a sub-rede e a rede virtual do Azure para usar com os nós virtuais.

Selecione **Examinar + criar**. Após concluir a validação, escolha **Criar**.

Demora alguns minutos para o cluster do AKS ser criado e ficar pronto para uso.

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Para gerenciar um cluster Kubernetes, use [kubectl][kubectl], o cliente de linha de comando Kubernetes. O cliente `kubectl` está pré-instalado no Azure Cloud Shell.

Para abrir o Cloud Shell, escolha **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

Use o [az aks get-credentials][az-aks-get-credentials] comando para configurar `kubectl` para se conectar ao cluster Kubernetes. O exemplo a seguir obtém as credenciais para o nome do cluster *myAKSCluster* no grupo de recursos chamado *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a conexão ao seu cluster, use o comando [kubectl get][kubectl-get] para retornar uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

O resultado do exemplo a seguir mostra o único nó de VM criado e o nó virtual para Linux, *virtual-node-aci-linux*:

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Implantar um aplicativo de exemplo

No Azure Cloud Shell, crie um arquivo chamado `virtual-node.yaml` e copie no YAML a seguir. Para agendar o contêiner no nó, uma [nodeSelector][node-selector] and [toleration][toleration] são definidos. Essas configurações permitem que o pod seja agendado no nó virtual e confirmam se o recurso foi habilitado com êxito.

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
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Execute o aplicativo com o [kubectl aplicar][kubectl-apply] comando.

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Use o [kubectl get pods][kubectl-get] com o `-o wide` argumento para gerar uma lista de pods e o nó agendado. Observe que o pod `virtual-node-helloworld` foi agendado no nó `virtual-node-linux`.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

O pod é atribuído a um endereço IP interno da sub-rede da rede virtual do Azure delegada para uso com nós virtuais.

> [!NOTE]
> Se você usar imagens armazenadas no registro de contêiner do Azure [configurar e usar um segredo Kubernetes][acr-aks-secrets]. Uma limitação atual de nós virtuais é que você não pode usar o Azure integrado autenticação de entidade de serviço do AD. Se você não usar um segredo, pods agendados em nós virtuais falham ao iniciar e relatam o erro `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testar o pod de nó virtual

Para testar o pod em execução no nó virtual, navegue até o aplicativo de demonstração com um cliente Web. Como o pod é atribuído a um endereço IP interno, é possível testar rapidamente essa conectividade por outro pod no cluster do AKS. Crie um pod de teste e uma sessão de terminal a ele:

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

Instale `curl` no pod usando `apt-get`:

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Agora, acesse o endereço de seu pod usando `curl`; por exemplo *http://10.241.0.4* . Forneça seu próprio endereço IP interno exibido no comando `kubectl get pods` anterior:

```azurecli-interactive
curl -L http://10.241.0.4
```

O aplicativo de demonstração é exibido, conforme mostrado na saída de exemplo condensada a seguir:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Feche a sessão de terminal do pod de teste com `exit`. Quando a sessão for encerrada, o pod será excluído.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, agendamos um pod no nó virtual e atribuímos a um endereço IP privado interno. Ao invés disso, você pode criar uma implantação de serviço e direcionar o tráfego para o pod por meio de um balanceador de carga ou controlador de entrada. Para obter mais informações, consulte [criar um controlador de entrada básico no AKS][aks-basic-ingress].

Os nós virtuais são um componente de uma solução de dimensionamento no AKS. Para saber mais sobre essas soluções de dimensionamento, consulte os artigos a seguir:

- [Usar o dimensionador automático horizontal de pods do Kubernetes][aks-hpa]
- [Usar o dimensionador automático cluster do Kubernetes][aks-cluster-autoscaler]
- [Confira o exemplo de dimensionamento automático para os nós virtuais][virtual-node-autoscale]
- [Leia mais sobre a biblioteca de software livre Virtual Kubelet][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
[az-provider-list]: /cli/azure/provider#az-provider-list