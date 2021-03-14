---
title: Usar zonas de disponibilidade no AKS (Serviço de Kubernetes do Azure)
description: Saiba como criar um cluster que distribui nós entre zonas de disponibilidade no AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.custom: fasttrack-edit, references_regions, devx-track-azurecli
ms.topic: article
ms.date: 09/04/2020
ms.openlocfilehash: 3eec8a6c331227d9d6298c46b272a5784080d342
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180319"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Criar um cluster do AKS (Serviço de Kubernetes do Azure) que usa zonas de disponibilidade

Um cluster do AKS (Serviço de Kubernetes do Azure) distribui recursos como nós e armazenamento entre seções lógicas da infraestrutura subjacente do Azure. Esse modelo de implantação ao usar zonas de disponibilidade, garante que os nós em uma determinada zona de disponibilidade sejam separados fisicamente dos definidos em outra zona de disponibilidade. Os clusters do AKS implantados com várias zonas de disponibilidade configuradas em um cluster fornecem um nível mais alto de disponibilidade para proteger contra uma falha de hardware ou um evento de manutenção planejada.

Definindo pools de nós em um cluster para abranger várias zonas, os nós em um determinado pool de nós podem continuar operando mesmo se uma única zona ficar inoperante. Seus aplicativos podem continuar disponíveis mesmo se houver uma falha física em um único datacenter, se orquestrados para tolerar a falha de um subconjunto de nós.

Este artigo mostra como criar um cluster do AKS e distribuir os componentes do nó entre zonas de disponibilidade.

## <a name="before-you-begin"></a>Antes de começar

Você precisará da CLI do Azure versão 2.0.76 ou posterior instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Limitações e disponibilidade de região

Atualmente, os clusters do AKS podem ser criados usando zonas de disponibilidade nas seguintes regiões:

* Leste da Austrália
* Canadá Central
* Centro dos EUA
* Leste dos EUA 
* Leste dos EUA 2
* França Central
* Centro-Oeste da Alemanha
* Leste do Japão
* Norte da Europa
* Sudeste Asiático
* Centro-Sul dos Estados Unidos
* Sul do Reino Unido
* Gov. dos EUA – Virgínia
* Europa Ocidental
* Oeste dos EUA 2

As seguintes limitações se aplicam quando você cria um cluster do AKS usando zonas de disponibilidade:

* Você só pode definir zonas de disponibilidade quando o cluster ou pool de nós for criado.
* Não é possível atualizar as configurações da zona de disponibilidade depois que o cluster for criado. Você também não pode atualizar um cluster de zona de não disponibilidade existente para usar zonas de disponibilidade.
* O tamanho do nó escolhido (SKU da VM) selecionado deve estar disponível em todas as zonas de disponibilidade selecionadas.
* Clusters com zonas de disponibilidade habilitadas exigem o uso do Azure Standard Load Balancer para distribuição entre zonas. Este tipo de balanceador de carga só pode ser definido no momento da criação do cluster. Para saber mais e ver as limitações do Standard Load Balancer, confira [Limitações da SKU do Azure Load Balancer Standard][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Limitações de discos do Azure

Os volumes que usam os discos gerenciados do Azure não são recursos com redundância de zona no momento. Os volumes não podem ser anexados entre zonas e devem estar colocalizados na mesma zona que um determinado nó que hospeda o pod de destino.

O kubernetes está ciente das zonas de disponibilidade do Azure desde a versão 1,12. Você pode implantar um objeto PersistentVolumeClaim referenciando um disco gerenciado do Azure em um cluster AKS de várias zonas e o kubernetes cuidará [do agendamento de](https://kubernetes.io/docs/setup/best-practices/multiple-zones/#storage-access-for-zones) qualquer Pod que declara esse PVC na zona de disponibilidade correta.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Visão geral das zonas de disponibilidade para clusters do AKS

As zonas de disponibilidade são uma oferta de alta disponibilidade que protege os aplicativos e dados contra falhas do datacenter. As zonas são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há sempre mais de uma zona em todas as regiões habilitadas para zona. A separação física das zonas de disponibilidade dentro de uma região protege os aplicativos e os dados contra falhas do datacenter.

Para saber mais, confira [O que são zonas de disponibilidade no Azure?][az-overview].

Os clusters do AKS implantados usando zonas de disponibilidade podem distribuir nós em várias zonas em uma única região. Por exemplo, um cluster na região  *Leste dos EUA 2* pode criar nós em todas as três zonas de disponibilidade no *Leste dos EUA 2*. Essa distribuição de recursos de cluster do AKS melhora a disponibilidade do cluster, pois eles são resilientes à falha de uma zona específica.

![Distribuição de nó do AKS entre zonas de disponibilidade](media/availability-zones/aks-availability-zones.png)

Se uma única zona ficar indisponível, seus aplicativos continuarão a ser executados se o cluster estiver difundido entre várias zonas.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Criar um cluster do AKS entre zonas de disponibilidade

Quando você cria um cluster usando o comando [az aks create][az-aks-create], o parâmetro `--zones` define em quais nós de agente de zonas eles são implantados. Os componentes do plano de controle, como etcd ou a API, serão distribuídos entre as zonas disponíveis na região se você definir o `--zones` parâmetro no momento da criação do cluster. As zonas específicas pelas quais os componentes do plano de controle são difundidos são independentes das zonas explícitas selecionadas para o pool de nós inicial.

Se você não definir zonas para o pool de agente padrão ao criar um cluster do AKS, os componentes de plano de controle não serão garantidos para serem difundidos entre as zonas de disponibilidade. Você pode adicionar pools de nós extras usando o comando [az aks nodepool add][az-aks-nodepool-add] e especificar `--zones` para novos nós, mas ele não alterará a forma como o plano de controle foi difundido entre zonas. As configurações de zona de disponibilidade só podem ser definidas no tempo de criação do cluster ou do pool de nós.

O exemplo abaixo cria um cluster do AKS chamado *myAKSCluster* no grupo de recursos chamado *myResourceGroup*. Um total de *3* nós são criados – um agente na zona *1*, um em *2* e, em seguida, um em *3*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

Leva alguns minutos para o cluster do AKS ser criado.

Ao decidir a qual zona um novo nó deve pertencer, um determinado pool de nós do AKS usará um [melhor balanceamento de zona de esforço oferecido pelos Conjuntos de Dimensionamento de Máquinas Virtuais do Azure subjacentes][vmss-zone-balancing]. Um determinado pool de nós do AKS é considerado "balanceado" se em cada zona tiver o mesmo número de VMs ou +\- uma VM em todas as outras zonas para o conjunto de dimensionamento.

## <a name="verify-node-distribution-across-zones"></a>Verificar a distribuição de nós entre zonas

Quando o cluster estiver pronto, liste os nós de agente no conjunto de dimensionamento para ver em qual zona de disponibilidade eles estão implantados.

Primeiro, obtenha as credenciais do cluster do AKS usando o comando [az aks get-credentials][az-aks-get-credentials]:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Em seguida, use o comando [kubectl descrevem][kubectl-describe] para listar os nós no cluster e filtrar o valor de *Failure-Domain.beta.kubernetes.Io/Zone* . O exemplo a seguir é para um shell bash.

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

A saída de exemplo a seguir mostra os três nós distribuídos pela região especificada e zonas de disponibilidade, como *eastus2-1* para a primeira zona de disponibilidade e *eastus2-2* para a segunda zona de disponibilidade:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

À medida que você adiciona nós extras a um pool de agentes, a plataforma do Azure distribui automaticamente as VMs subjacentes entre as zonas de disponibilidade especificadas.

Observe que nas versões mais recentes do Kubernetes (1.17.0 e posterior), o AKS está usando o rótulo mais recente `topology.kubernetes.io/zone` além do `failure-domain.beta.kubernetes.io/zone` preterido. Você pode obter o mesmo resultado acima com executando o seguinte script:

```console
kubectl get nodes -o custom-columns=NAME:'{.metadata.name}',REGION:'{.metadata.labels.topology\.kubernetes\.io/region}',ZONE:'{metadata.labels.topology\.kubernetes\.io/zone}'
```

Que fornecerá uma saída mais sucinta:

```console
NAME                                REGION   ZONE
aks-nodepool1-34917322-vmss000000   eastus   eastus-1
aks-nodepool1-34917322-vmss000001   eastus   eastus-2
aks-nodepool1-34917322-vmss000002   eastus   eastus-3
```

## <a name="verify-pod-distribution-across-zones"></a>Verificar a distribuição de pods entre zonas

Conforme documentado em [Well-Known Labels, Annotations and Taints][kubectl-well_known_labels], o Kubernetes usa o rótulo de `failure-domain.beta.kubernetes.io/zone` para distribuir automaticamente os pods em um serviço ou controlador de replicação entre as diferentes zonas disponíveis. Para testar isso, você pode escalar verticalmente o cluster de três para cinco nós, para verificar a difusão correta de pods:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Quando a operação de dimensionamento for concluída após alguns minutos, o comando `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` em um shell bash deverá fornecer uma saída semelhante a este exemplo:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

Agora temos dois nós adicionais nas zonas 1 e 2. Você pode implantar um aplicativo que consiste em três réplicas. Usaremos NGINX como exemplo:

```console
kubectl create deployment nginx --image=nginx
kubectl scale deployment nginx --replicas=3
```

Exibindo os nós em que os pods estão em execução, você vê os pods em execução nos nós correspondentes a três zonas de disponibilidade diferentes. Por exemplo, com o comando `kubectl describe pod | grep -e "^Name:" -e "^Node:"` em um shell bash, você obterá uma saída semelhante a esta:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Como você pode ver na saída anterior, o primeiro pod está em execução no nó 0, que está localizado na zona de disponibilidade `eastus2-1`. O segundo pod está em execução no nó 2, que corresponde a `eastus2-3` e o terceiro no nó 4, em `eastus2-2`. Sem nenhuma configuração adicional, o Kubernetes está difundindo os pods corretamente entre todas as três zonas de disponibilidade.

## <a name="next-steps"></a>Próximas etapas

Este artigo detalha como criar um cluster do AKS que usa zonas de disponibilidade. Para mais considerações sobre clusters altamente disponíveis, confira [Melhores práticas para a continuidade dos negócios e recuperação de desastres no AKS][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/labels-annotations-taints/
