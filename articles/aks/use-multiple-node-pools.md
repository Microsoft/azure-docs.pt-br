---
title: Usar vários pools de nó no serviço kubernetes do Azure (AKS)
description: Saiba como criar e gerenciar vários pools de nós para um cluster no serviço kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 02/11/2021
ms.openlocfilehash: 8f18e19eca8895549f17c9f0f6822ecb4da2914b
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773497"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Criar e gerenciar vários pools de nós para um cluster no AKS (Serviço de Kubernetes do Azure)

No AKS (serviço kubernetes do Azure), os nós da mesma configuração são agrupados em *pools de nós*. Esses pools de nós contêm as VMs subjacentes que executam seus aplicativos. O número inicial de nós e seu tamanho (SKU) é definido quando você cria um cluster AKS, que cria um [pool de nós do sistema][use-system-pool]. Para dar suporte a aplicativos que têm demandas de armazenamento ou de computação diferentes, você pode criar *pools de nó de usuário* adicionais. Os pools de nó do sistema servem a principal finalidade de hospedar pods críticos do sistema, como CoreDNS e tunnelfront. Os pools de nó de usuário servem a principal finalidade de hospedar o pods do aplicativo. No entanto, os pods de aplicativo podem ser agendados em pools de nós do sistema se você quiser ter apenas um pool no cluster AKS. Os pools de nós de usuário são onde você coloca os pods específicos do aplicativo. Por exemplo, use esses pools de nó de usuário adicionais para fornecer GPUs para aplicativos de computação intensiva ou acesso ao armazenamento SSD de alto desempenho.

> [!NOTE]
> Esse recurso permite maior controle sobre como criar e gerenciar vários pools de nós. Como resultado, comandos separados são necessários para criar/atualizar/excluir. Anteriormente, as operações de cluster por meio `az aks create` `az aks update` do ou usaram a API managedCluster e eram a única opção para alterar o plano de controle e um único pool de nós. Esse recurso expõe uma operação separada definida para pools de agente por meio da API agentPool e requer o uso do `az aks nodepool` conjunto de comandos para executar operações em um pool de nós individual.

Este artigo mostra como criar e gerenciar vários pools de nós em um cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Você precisa do CLI do Azure versão 2.2.0 ou posterior instalado e configurado. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters do AKS com suporte a vários pools de nós:

* Consulte [cotas, restrições de tamanho de máquina virtual e disponibilidade de região no serviço de kubernetes do Azure (AKs)][quotas-skus-regions].
* Você pode excluir pools de nós do sistema, desde que você tenha outro pool de nós do sistema para assumir seu lugar no cluster AKS.
* Os pools do sistema devem conter pelo menos um nó e os pools de nós do usuário podem conter zero ou mais nós.
* O cluster AKS deve usar o balanceador de carga SKU padrão para usar vários pools de nós, o recurso não tem suporte com balanceadores de carga de SKU básicos.
* O cluster AKS deve usar conjuntos de dimensionamento de máquinas virtuais para os nós.
* O nome de um pool de nós pode conter apenas caracteres alfanuméricos minúsculos e deve começar com uma letra minúscula. Para pools de nós do Linux, o comprimento deve ter entre 1 e 12 caracteres, para pools de nó do Windows o comprimento deve ter entre 1 e 6 caracteres.
* Todos os pools de nós devem residir na mesma rede virtual.
* Ao criar vários pools de nós no momento da criação do cluster, todas as versões do kubernetes usadas por pools de nós devem corresponder à versão definida para o plano de controle. Isso pode ser atualizado depois que o cluster tiver sido provisionado usando operações de pool por nó.

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

> [!Important]
> Se você executar um pool de nós de sistema único para o cluster AKS em um ambiente de produção, recomendamos que você use pelo menos três nós para o pool de nós.

Para começar, crie um cluster AKS com um único pool de nós. O exemplo a seguir usa o comando [AZ Group Create][az-group-create] para criar um grupo de recursos chamado *MyResource* Group na região *eastus* . Um cluster AKS chamado *myAKSCluster* é então criado usando o comando [AZ AKs Create][az-aks-create] .

> [!NOTE]
> **Não há suporte para** o SKU do Load Balancer *básico* ao usar vários pools de nós. Por padrão, os clusters AKS são criados com o SKU do Load Balancer *Standard* do CLI do Azure e portal do Azure.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --load-balancer-sku standard
```

São necessários alguns minutos para criar o cluster.

> [!NOTE]
> Para garantir que o cluster opere de forma confiável, você deve executar pelo menos 2 (dois) nós no pool de nós padrão, pois os serviços de sistema essenciais estão sendo executados nesse pool de nós.

Quando o cluster estiver pronto, use o comando [AZ AKs Get-Credentials][az-aks-get-credentials] para obter as credenciais do cluster para uso com `kubectl` :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Adicionar um pool de nós

O cluster criado na etapa anterior tem um único pool de nós. Vamos adicionar um segundo pool de nós usando o comando [AZ AKs nodepool Add][az-aks-nodepool-add] . O exemplo a seguir cria um pool de nós chamado *mynodepool* que executa *3* nós:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> O nome de um pool de nós deve começar com uma letra minúscula e só pode conter caracteres alfanuméricos. Para pools de nós do Linux, o comprimento deve ter entre 1 e 12 caracteres, para pools de nó do Windows o comprimento deve ter entre 1 e 6 caracteres.

Para ver o status dos pools de nós, use o comando [AZ AKs node pool List][az-aks-nodepool-list] e especifique o grupo de recursos e o nome do cluster:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

A saída de exemplo a seguir mostra que *mynodepool* foi criado com êxito com três nós no pool de nós. Quando o cluster AKS foi criado na etapa anterior, um *nodepool1* padrão foi criado com uma contagem de nós de *2*.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Se nenhum *VmSize* for especificado quando você adicionar um pool de nós, o tamanho padrão será *Standard_D2s_v3* para pools de nós do Windows e *Standard_DS2_v2* para pools de nós do Linux. Se nenhum *OrchestratorVersion* for especificado, o padrão será a mesma versão do plano de controle.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Adicionar um pool de nós com uma sub-rede exclusiva (versão prévia)

Uma carga de trabalho pode exigir a divisão de nós de um cluster em pools separados para isolamento lógico. Esse isolamento pode ter suporte com sub-redes separadas dedicadas a cada pool de nós no cluster. Isso pode atender aos requisitos, como ter espaço de endereço de rede virtual não contíguo para dividir entre pools de nós.

#### <a name="limitations"></a>Limitações

* Todas as sub-redes atribuídas a nodepools devem pertencer à mesma rede virtual.
* O pods do sistema deve ter acesso a todos os nós/pods no cluster para fornecer funcionalidade crítica, como resolução de DNS e encapsulamento de kubectl logs/proxy de envio de porta/exec.
* Se você expandir sua VNET depois de criar o cluster, deverá atualizar o cluster (executar qualquer operação clster gerenciada, mas as operações do pool de nós não contam) antes de adicionar uma sub-rede fora do CIDR original. O AKS ocorrerá um erro no pool de agentes adicionar agora, embora o tenha permitido originalmente. Se você não souber como reconciliar o arquivo de cluster, um tíquete de suporte. 
* Não há suporte para a política de rede Calico. 
* Não há suporte para a política de rede do Azure.
* Kube-o proxy espera um único CIDR contíguo e o usa para três optmizations. Consulte este [K.E.P.](https://github.com/kubernetes/enhancements/tree/master/keps/sig-network/2450-Remove-knowledge-of-pod-cluster-CIDR-from-iptables-rules) e--cluster-CIDR [aqui](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/) para obter detalhes. No Azure CNI, a sub-rede do seu primeiro pool de nós será fornecida ao Kube-proxy. 

Para criar um pool de nós com uma sub-rede dedicada, passe a ID de recurso de sub-rede como um parâmetro adicional ao criar um pool de nós.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Atualizar um pool de nós

> [!NOTE]
> As operações de atualização e dimensionamento em um cluster ou pool de nós não podem ocorrer simultaneamente, se uma tentativa de erro for retornada. Em vez disso, cada tipo de operação deve ser concluído no recurso de destino antes da próxima solicitação no mesmo recurso. Leia mais sobre isso em nosso [Guia de solução de problemas](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade).

Os comandos nesta seção explicam como atualizar um único pool de nós específico. A relação entre a atualização da versão kubernetes do plano de controle e o pool de nós é explicada na [seção abaixo](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> A versão da imagem do sistema operacional do pool de nós está vinculada à versão kubernetes do cluster. Você só obterá atualizações de imagem do sistema operacional, seguindo uma atualização de cluster.

Como há dois pools de nós neste exemplo, devemos usar [AZ AKs nodepool upgrade][az-aks-nodepool-upgrade] para atualizar um pool de nós. Para ver as atualizações disponíveis, use [AZ AKs Get-upgrades][az-aks-get-upgrades]

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Vamos atualizar o *mynodepool*. Use o comando [AZ AKs nodepool upgrade][az-aks-nodepool-upgrade] para atualizar o pool de nós, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

Liste o status dos pools de nós novamente usando o comando [AZ AKs node pool List][az-aks-nodepool-list] . O exemplo a seguir mostra que *mynodepool* está no estado *atualizando* para *KUBERNETES_VERSION*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "KUBERNETES_VERSION",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Leva alguns minutos para atualizar os nós para a versão especificada.

Como prática recomendada, você deve atualizar todos os pools de nós em um cluster AKS para a mesma versão kubernetes. O comportamento padrão do `az aks upgrade` é atualizar todos os pools de nós junto com o plano de controle para alcançar esse alinhamento. A capacidade de atualizar pools de nós individuais permite executar uma atualização sem interrupção e agendar pods entre pools de nós para manter o tempo de atividade do aplicativo dentro das restrições acima mencionadas.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Atualizar um plano de controle de cluster com vários pools de nós

> [!NOTE]
> Kubernetes usa o esquema de controle de versão de [controle semântico](https://semver.org/) de versão padrão. O número de versão é expresso como *x. y. z*, em que *x* é a versão principal, *y* é a versão secundária e *z* é a versão do patch. Por exemplo, na versão *1.12.6*, 1 é a versão principal, 12 é a versão secundária e 6 é a versão do patch. A versão kubernetes do plano de controle e o pool de nós inicial são definidos durante a criação do cluster. Todos os pools de nós adicionais têm sua versão kubernetes definida quando são adicionados ao cluster. As versões do kubernetes podem ser diferentes entre pools de nós, bem como entre um pool de nós e o plano de controle.

Um cluster AKS tem dois objetos de recurso de cluster com versões do kubernetes associadas.

1. Uma versão kubernetes do plano de controle de cluster.
2. Um pool de nós com uma versão kubernetes.

Um plano de controle é mapeado para um ou vários pools de nós. O comportamento de uma operação de atualização depende de qual CLI do Azure comando é usado.

A atualização de um plano de controle AKS requer o uso do `az aks upgrade` . Esse comando atualiza a versão do plano de controle e todos os pools de nós no cluster.

Emitir o `az aks upgrade` comando com o `--control-plane-only` sinalizador atualiza apenas o plano de controle de cluster. Nenhum dos pools de nós associados no cluster foi alterado.

A atualização de pools de nós individuais requer o uso do `az aks nodepool upgrade` . Este comando atualiza somente o pool de nós de destino com a versão especificada do kubernetes

### <a name="validation-rules-for-upgrades"></a>Regras de validação para atualizações

As atualizações de kubernetes válidas para o plano de controle e os pools de nós do cluster são validadas pelos seguintes conjuntos de regras.

* Regras para versões válidas para atualizar pools de nós:
   * A versão do pool de nós deve ter a mesma versão *principal* que o plano de controle.
   * A versão *secundária* do pool de nós deve estar dentro de duas versões *secundárias* da versão do plano de controle.
   * A versão do pool de nós não pode ser maior que a versão de controle `major.minor.patch` .

* Regras para enviar uma operação de atualização:
   * Não é possível fazer downgrade do plano de controle ou de uma versão kubernetes do pool de nós.
   * Se uma versão de kubernetes do pool de nós não for especificada, o comportamento dependerá do cliente que está sendo usado. A declaração nos modelos do Resource Manager volta à versão existente definida para o pool de nós, se for usada, se nenhuma for definida, a versão do plano de controle será usada para fazer fallback.
   * Você pode atualizar ou dimensionar um plano de controle ou um pool de nós em um determinado momento, não pode enviar várias operações em um único plano de controle ou recurso de pool de nós simultaneamente.

## <a name="scale-a-node-pool-manually"></a>Dimensionar um pool de nós manualmente

À medida que as demandas de carga de trabalho do aplicativo mudam, talvez seja necessário dimensionar o número de nós em um pool de nós. O número de nós pode ser aumentado ou reduzido verticalmente.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Para dimensionar o número de nós em um pool de nós, use o comando [AZ AKs node pool Scale][az-aks-nodepool-scale] . O exemplo a seguir dimensiona o número de nós em *mynodepool* para *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Liste o status dos pools de nós novamente usando o comando [AZ AKs node pool List][az-aks-nodepool-list] . O exemplo a seguir mostra que *mynodepool* está no estado de *dimensionamento* com uma nova contagem de *5* nós:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Leva alguns minutos para que a operação de dimensionamento seja concluída.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Dimensionar um pool de nós específico automaticamente habilitando o dimensionador automático do cluster

O AKS oferece um recurso separado para dimensionar automaticamente os pools de nós com um recurso chamado de [dimensionador](cluster-autoscaler.md)automático do cluster. Esse recurso pode ser habilitado por pool de nós com contagens de escala mínima e máxima exclusivas por pool de nós. Saiba como [usar o conjunto de dimensionamento de clusters por pool de nós](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Excluir um pool de nós

Se você não precisar mais de um pool, poderá excluí-lo e remover os nós de VM subjacentes. Para excluir um pool de nós, use o comando [AZ AKs node pool Delete][az-aks-nodepool-delete] e especifique o nome do pool de nós. O exemplo a seguir exclui o *mynoodepool* criado nas etapas anteriores:

> [!CAUTION]
> Não há opções de recuperação para perda de dados que podem ocorrer quando você exclui um pool de nós. Se os pods não puderem ser agendados em outros pools de nós, esses aplicativos não estarão disponíveis. Certifique-se de não excluir um pool de nós quando aplicativos em uso não tiverem backups de dados ou a capacidade de executar em outros pools de nós no cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

A saída de exemplo a seguir do comando [AZ AKs node pool List][az-aks-nodepool-list] mostra que *mynodepool* está no estado *excluindo* :

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Leva alguns minutos para excluir os nós e o pool de nós.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Especificar um tamanho de VM para um pool de nós

Nos exemplos anteriores para criar um pool de nós, um tamanho de VM padrão foi usado para os nós criados no cluster. Um cenário mais comum é criar pools de nós com diferentes tamanhos e recursos de VM. Por exemplo, você pode criar um pool de nós que contém nós com grandes quantidades de CPU ou memória, ou um pool de nós que fornece suporte à GPU. Na próxima etapa, você [usa os tolerationss e](#setting-nodepool-taints) os informativos para informar ao agendador de kubernetes como limitar o acesso a pods que pode ser executado nesses nós.

No exemplo a seguir, crie um pool de nós baseado em GPU que usa o tamanho da VM *Standard_NC6* . Essas VMs são alimentadas pelo cartão NVIDIA Tesla K80. Para obter informações sobre tamanhos de VM disponíveis, consulte [tamanhos de máquinas virtuais do Linux no Azure][vm-sizes].

Crie um pool de nós usando o comando [AZ AKs node pool Add][az-aks-nodepool-add] novamente. Desta vez, especifique o nome *gpunodepool* e use o `--node-vm-size` parâmetro para especificar o tamanho do *Standard_NC6* :

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

A saída de exemplo a seguir do comando [AZ AKs node pool List][az-aks-nodepool-list] mostra que *gpunodepool* está *criando* nós com o *VmSize* especificado:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Leva alguns minutos para que o *gpunodepool* seja criado com êxito.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Especificar um seu, rótulo ou uma marca para um pool de nós

### <a name="setting-nodepool-taints"></a>Configurando nodepools

Ao criar um pool de nós, você pode adicionar os, rótulos ou marcas a esse pool de nós. Quando você adiciona um você, um rótulo ou uma marca, todos os nós dentro desse pool de nós também obtêm o seu não, o rótulo ou a marca.

Para criar um pool de nós com um AKs, use [AZ nodepool Add][az-aks-nodepool-add]. Especifique o nome *taintnp* e use o `--node-taints` parâmetro para especificar *SKU = GPU: NoSchedule* para o seu

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

> [!NOTE]
> Um não pode ser definido apenas para pools de nós durante a criação do pool de nós.

A saída de exemplo a seguir do comando [AZ AKs nodepool List][az-aks-nodepool-list] mostra que *taintnp* está *criando* nós com o *nodeTaints* especificado:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

As informações de seu kubernetes são visíveis no entanto para manipular regras de agendamento para nós. O Agendador Kubernetes pode usar taints e tolerations para restringir quais cargas de trabalho podem ser executados em nós.

* Um **taint** é aplicado a um nó que indica que apenas os pods específicos podem ser agendados neles.
* Um **toleration**, em seguida, é aplicado a um pod que lhes permite *tolerar* um taint de nó.

Para obter mais informações sobre como usar os recursos agendados do kubernetes avançados, consulte [práticas recomendadas para recursos avançados do Agendador no AKs][taints-tolerations]

Na etapa anterior, você aplicou a *SKU = GPU: NoSchedule* contra o que você criou no pool de nós. O manifesto YAML de exemplo básico a seguir usa um toleration para permitir que o Agendador de kubernetes execute um pod NGINX em um nó nesse pool de nós.

Crie um arquivo chamado `nginx-toleration.yaml` e copie no exemplo YAML a seguir:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Agende o Pod usando o `kubectl apply -f nginx-toleration.yaml` comando:

```console
kubectl apply -f nginx-toleration.yaml
```

Leva alguns segundos para agendar o pod e efetuar pull da imagem NGINX. Use o comando [kubectl para descrever o Pod][kubectl-describe] para exibir o status do pod. A saída de exemplo condensada a seguir mostra o *SKU = GPU: NoSchedule* toleration é aplicado. Na seção de eventos, o Agendador atribuiu o Pod ao nó *AKs-taintnp-28993262-vmss000000* :

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                Message
  ----    ------     ----   ----                -------
  Normal  Scheduled  4m48s  default-scheduler   Successfully assigned default/mypod to aks-taintnp-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet             pulling image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Pulled     4m43s  kubelet             Successfully pulled image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Created    4m40s  kubelet             Created container
  Normal  Started    4m40s  kubelet             Started container
```

Somente os pods que têm esse toleration aplicado podem ser agendados em nós em *taintnp*. Qualquer outro pod seria agendado no pool de nós *nodepool1* . Se você criar pools de nós adicionais, poderá usar os conteúdo e os Tolerations adicionais para limitar o que os pods podem ser agendados nesses recursos de nó.

### <a name="setting-nodepool-labels"></a>Configurando rótulos de nodepool

Você também pode adicionar rótulos a um pool de nós durante a criação do pool de nós. Os rótulos definidos no pool de nós são adicionados a cada nó no pool de nós. Esses [Rótulos são visíveis no kubernetes][kubernetes-labels] para manipular regras de agendamento para nós.

Para criar um pool de nós com um rótulo, use [AZ AKs nodepool Add][az-aks-nodepool-add]. Especifique o nome *labelnp* e use o `--labels` parâmetro para especificar *Dept = it* e *costcenter = 9999* para rótulos.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> O rótulo só pode ser definido para pools de nós durante a criação do pool de nós. Os rótulos também devem ser um par chave/valor e ter uma [sintaxe válida][kubernetes-label-syntax].

A saída de exemplo a seguir do comando [AZ AKs nodepool List][az-aks-nodepool-list] mostra que *labelnp* está *criando* nós com o *nodeLabels* especificado:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

### <a name="setting-nodepool-azure-tags"></a>Configurando marcas do Azure nodepool

Você pode aplicar uma marca do Azure a pools de nós em seu cluster AKS. As marcas aplicadas a um pool de nós são aplicadas a cada nó dentro do pool de nós e são mantidas por meio de atualizações. As marcas também são aplicadas a novos nós adicionados a um pool de nós durante operações de expansão. A adição de uma marca pode ajudar com tarefas como rastreamento de política ou estimativa de custo.

As marcas do Azure têm chaves que não diferenciam maiúsculas de minúsculas para operações, como ao recuperar uma marca pesquisando a chave. Nesse caso, uma marca com a chave fornecida será atualizada ou recuperada, independentemente de maiúsculas e minúsculas. Os valores de marca diferenciam maiúsculas de minúsculas.

No AKS, se várias marcas forem definidas com chaves idênticas, mas com maiúsculas e minúsculas diferentes, a marca usada será a primeira em ordem alfabética. Por exemplo, `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` resulta em `Key1` e `val1` está sendo definido.

Crie um pool de nós usando o [AKs AZ nodepool Add][az-aks-nodepool-add]. Especifique o nome *tagnodepool* e use o `--tag` parâmetro para especificar *Dept = it* e *costcenter = 9999* para marcas.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Você também pode usar o `--tags` parâmetro ao usar o comando [AZ AKs nodepool Update][az-aks-nodepool-update] , bem como durante a criação do cluster. Durante a criação do cluster, o `--tags` parâmetro aplica a marca ao pool de nós inicial criado com o cluster. Todos os nomes de marca devem aderir às limitações nas [marcas de uso para organizar os recursos do Azure][tag-limitation]. A atualização de um pool de nós com o `--tags` parâmetro atualiza quaisquer valores de marca existentes e acrescenta quaisquer novas marcas. Por exemplo, se o pool de nós tiver o *Dept = it* e o *costcenter = 9999* para marcas e você o tiver atualizado com *Team = dev* e *costcenter = 111* para marcas, você nodepool teria *Dept = it*, *costcenter = 111* e *Team = dev* para marcas.

A saída de exemplo a seguir do comando [AZ AKs nodepool List][az-aks-nodepool-list] mostra que *tagnodepool* está *criando* nós com a *marca* especificada:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Gerenciar pools de nós usando um modelo do Resource Manager

Ao usar um modelo de Azure Resource Manager para criar e gerenciar recursos, você normalmente pode atualizar as configurações em seu modelo e reimplantar para atualizar o recurso. Com pools de nós no AKS, o perfil do pool de nós inicial não pode ser atualizado depois que o cluster AKS tiver sido criado. Esse comportamento significa que você não pode atualizar um modelo existente do Resource Manager, fazer uma alteração nos pools de nós e reimplantar. Em vez disso, você deve criar um modelo do Resource Manager separado que atualize apenas os pools de nós para um cluster AKS existente.

Crie um modelo, como `aks-agentpools.json` e cole o exemplo de manifesto a seguir. Este modelo de exemplo define as seguintes configurações:

* Atualiza o pool de nós do *Linux* chamado *myagentpool* para executar três nós.
* Define os nós no pool de nós para executar o kubernetes versão *1.15.7*.
* Define o tamanho do nó como *Standard_DS2_v2*.

Edite esses valores conforme necessário para atualizar, adicionar ou excluir pools de nós conforme necessário:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Implante esse modelo usando o comando [AZ Deployment Group Create][az-deployment-group-create] , conforme mostrado no exemplo a seguir. O nome e o local do cluster AKS existentes serão solicitados:

```azurecli-interactive
az deployment group create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Você pode adicionar uma marca ao pool de nós adicionando a propriedade *tag* no modelo, conforme mostrado no exemplo a seguir.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Pode levar alguns minutos para atualizar o cluster AKS dependendo das configurações do pool de nós e das operações definidas no modelo do Resource Manager.

## <a name="assign-a-public-ip-per-node-for-your-node-pools"></a>Atribuir um IP público por nó para seus pools de nós

Os nós AKS não exigem seus próprios endereços IP públicos para comunicação. No entanto, os cenários podem exigir que os nós em um pool de nós recebam seus próprios endereços IP públicos dedicados. Um cenário comum é para cargas de trabalho de jogos, em que um console precisa fazer uma conexão direta com uma máquina virtual de nuvem para minimizar os saltos. Esse cenário pode ser obtido em AKS usando o IP público do nó.

Primeiro, crie um novo grupo de recursos.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

Crie um novo cluster AKS e anexe um IP público para seus nós. Cada um dos nós no pool de nós recebe um IP público exclusivo. Você pode verificar isso examinando as instâncias do conjunto de dimensionamento de máquinas virtuais.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

Para clusters AKS existentes, você também pode adicionar um novo pool de nós e anexar um IP público para seus nós.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

Você pode localizar os IPs públicos para seus nós de várias maneiras:

* Use o comando CLI do Azure [AZ vmss List-Instance-Public-IPS][az-list-ips].
* Use os [Comandos PowerShell ou bash][vmss-commands]. 
* Você também pode exibir os IPs públicos no portal do Azure exibindo as instâncias no conjunto de dimensionamento de máquinas virtuais.

> [!Important]
> O [grupo de recursos de nó][node-resource-group] contém os nós e seus IPs públicos. Use o grupo de recursos do nó ao executar comandos para localizar os IPs públicos para seus nós.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>Limpar os recursos

Neste artigo, você criou um cluster AKS que inclui nós baseados em GPU. Para reduzir o custo desnecessário, talvez você queira excluir o *gpunodepool* ou todo o cluster AKs.

Para excluir o pool de nós baseado em GPU, use o comando [AZ AKs nodepool Delete][az-aks-nodepool-delete] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Para excluir o próprio cluster, use o comando [AZ Group Delete][az-group-delete] para excluir o grupo de recursos AKs:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

Você também pode excluir o cluster adicional que você criou para o cenário de IP público para pools de nós.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [pools de nós do sistema][use-system-pool].

Neste artigo, você aprendeu a criar e gerenciar vários pools de nós em um cluster AKS. Para obter mais informações sobre como controlar os pods nos pools de nós, consulte [práticas recomendadas para recursos avançados do Agendador no AKs][operator-best-practices-advanced-scheduler].

Para criar e usar pools de nós de contêiner do Windows Server, consulte [criar um contêiner do Windows Server em AKs][aks-windows].

Use [grupos de posicionamento de proximidade][reduce-latency-ppg] para reduzir a latência para seus aplicativos AKs.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_create
[az-aks-get-upgrades]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_upgrades
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_update
[az-group-create]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_create
[az-group-delete]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete
[az-deployment-group-create]: /cli/azure/deployment/group?view=azure-cli-latest&preserve-view=true#az_deployment_group_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest&preserve-view=true#az_vmss_list_instance_public_ips
[reduce-latency-ppg]: reduce-latency-ppg.md
