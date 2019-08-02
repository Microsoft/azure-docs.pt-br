---
title: RDP em nós de Windows Server de cluster do serviço de Kubernetes do Azure (AKS)
description: Saiba como criar uma conexão de RDP com o cluster do serviço de Kubernetes do Azure (AKS) nós de Windows Server para tarefas de manutenção e solução de problemas.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: mlearned
ms.openlocfilehash: 0238278b81255d735f8a950ca307d0e05100cfec
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614573"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Conectar-se com o RDP para o serviço de Kubernetes do Azure (AKS) nós do cluster do Windows Server para manutenção ou solução de problemas

Em todo o ciclo de vida do seu cluster do serviço de Kubernetes do Azure (AKS), você precisa acessar um nó do AKS Windows Server. Esse acesso pode ser para manutenção, coleção de logs ou outras operações de solução de problemas. Você pode acessar os nós do AKS Windows Server usando o RDP. Como alternativa, se você deseja usar o SSH para acessar os nós do AKS Windows Server e você tem acesso para o mesmo par de chaves que foi usado durante a criação do cluster, você pode seguir as etapas em [SSH em nós de cluster do serviço de Kubernetes do Azure (AKS)][ssh-steps]. Para fins de segurança, os nós do AKS não são expostos à internet.

Suporte de nó do Windows Server está atualmente em visualização no AKS.

Este artigo mostra como criar uma conexão de RDP com um nó do AKS usando seus endereços IP privados.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente com um nó do Windows Server. Se você precisar de um cluster do AKS, consulte o artigo sobre [criando um cluster do AKS com um contêiner do Windows usando a CLI do Azure][aks-windows-cli]. You need the Windows administrator username and password for the Windows Server node you want to troubleshoot. You also need an RDP client such as [Microsoft Remote Desktop][rdp-mac].

Você também precisa da CLI do Azure versão 2.0.61 ou posterior instalado e configurado. Execute  `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [instalar a CLI do Azure][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Implantar uma máquina virtual à mesma sub-rede que o seu cluster

Os nós do Windows Server do cluster do AKS não tem endereços IP acessíveis externamente. Para fazer uma conexão de RDP, você pode implantar uma máquina virtual com um endereço IP acessível publicamente à mesma sub-rede que os nós do Windows Server.

O exemplo a seguir cria uma máquina virtual denominada *myVM* na *myResourceGroup* grupo de recursos.

Primeiro, obtenha a sub-rede usada pelo seu pool de nós do Windows Server. Para obter a id de sub-rede, você precisa do nome da sub-rede. Para obter o nome da sub-rede, você precisa do nome da rede virtual. Obtenha o nome de rede virtual, consultando o cluster para sua lista de redes. Para consultar o cluster, você precisa de seu nome. Você pode obter todos esses executando o seguinte no Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Agora que você tem a SUBNET_ID, execute o seguinte comando na mesma janela do Azure Cloud Shell para criar a VM:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

A saída de exemplo a seguir mostra a VM foi criada com êxito e exibe o endereço IP público da máquina virtual.

```console
13.62.204.18
```

Registre o endereço IP público da máquina virtual. Você usará esse endereço em uma etapa posterior.

## <a name="get-the-node-address"></a>Obter o endereço do nó

Para gerenciar um cluster do Kubernetes, use [kubectl][kubectl], o cliente de linha de comando do Kubernetes. Se você usar o Azure Cloud Shell, o `kubectl` já estará instalado. Para instalar o `kubectl` localmente, use o comando [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Lista o endereço IP interno de nós do Windows Server usando o [kubectl get][kubectl-get] comando:

```console
kubectl get nodes -o wide
```

A saída de exemplo a seguir mostra os endereços IP internos de todos os nós no cluster, incluindo os nós do Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registre o endereço IP interno do nó do Windows Server que você deseja solucionar problemas. Você usará esse endereço em uma etapa posterior.

## <a name="connect-to-the-virtual-machine-and-node"></a>Conectar-se para a máquina virtual e o nó

Conectar-se para o endereço IP público da máquina virtual que você criou anteriormente usando um cliente RDP, como [área de trabalho remota Microsoft][rdp-mac].

![Imagem de se conectar à máquina virtual usando um cliente RDP](media/rdp/vm-rdp.png)

Depois de se conectar à sua máquina virtual, conecte-se para o *endereço IP interno* do nó do Windows Server que você deseja solucionar problemas usando um cliente RDP de dentro de sua máquina virtual.

![Imagem de conexão para o nó de servidor do Windows usando um cliente RDP](media/rdp/node-rdp.png)

Agora você está conectado ao seu nó do Windows Server.

![Imagem da janela de cmd no nó do servidor do Windows](media/rdp/node-session.png)

Agora você pode executar qualquer comando de solução de problemas *cmd* janela. Como nós do Windows Server usam o Windows Server Core, não há uma GUI completa ou outras ferramentas de GUI quando você se conecta a um nó do Windows Server por RDP.

## <a name="remove-rdp-access"></a>Remover o acesso RDP

Quando terminar, a conexão de RDP de saída para o nó do Windows Server e em seguida, saia da sessão RDP para a máquina virtual. Depois que você sair de ambas as sessões RDP, exclua a máquina virtual com o [az vm excluir][az-vm-delete] comando:

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Próximas etapas

Se você precisar de dados adicionais de solução de problemas, você poderá [exibir os logs do nó mestre de Kubernetes][view-master-logs] or [Azure Monitor][azure-monitor-containers].

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
