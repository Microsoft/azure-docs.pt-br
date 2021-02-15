---
title: Configuração de cluster no AKS (Serviços de Kubernetes do Azure)
description: Saiba como configurar um cluster no AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 5519157b58268b30ecb7a1af7b86d13d587a23b8
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519398"
---
# <a name="configure-an-aks-cluster"></a>Configurar um cluster do AKS

Como parte da criação de um cluster do AKS, talvez seja necessário personalizar a configuração do cluster para atender às suas necessidades. Este artigo apresenta algumas opções para personalizar o cluster do AKS.

## <a name="os-configuration"></a>Configuração do SO

O AKS agora dá suporte ao Ubuntu 18, 4 como o sistema operacional de nó padrão (SO) em disponibilidade geral (GA) para clusters em versões do kubernetes superiores a 1,18 para as versões abaixo de 1,18, AKS Ubuntu 16, 4 ainda é a imagem base padrão. Do kubernetes v 18e e superior, a base padrão é AKS Ubuntu 18, 4.

> [!IMPORTANT]
> Pools de nós criados em kubernetes v 18E ou superior padrão para `AKS Ubuntu 18.04` imagem de nó. Pools de nós em uma versão de kubernetes com suporte inferior a 1,18 recebem `AKS Ubuntu 16.04` como a imagem de nó, mas serão atualizados para `AKS Ubuntu 18.04` quando a versão do pool de nós kubernetes for atualizada para v 18E ou superior.
> 
> É altamente recomendável testar suas cargas de trabalho em pools de nós do AKS Ubuntu 18, 4 antes de usar clusters em 1,18 ou superior.


### <a name="use-aks-ubuntu-1804-ga-on-new-clusters"></a>Usar o AKS Ubuntu 18, 4 (GA) em novos clusters

Clusters criados em kubernetes v 18E ou superior padrão para `AKS Ubuntu 18.04` imagem de nó. Os pools de nós em uma versão do kubernetes com suporte inferior a 1,18 ainda serão recebidos `AKS Ubuntu 16.04` como a imagem do nó, mas serão atualizados para `AKS Ubuntu 18.04` quando a versão do cluster ou do pool de nós kubernetes for atualizada para o v 18E ou superior.

É altamente recomendável testar suas cargas de trabalho em pools de nós do AKS Ubuntu 18, 4 antes de usar clusters em 1,18 ou superior.

Para criar um cluster usando a `AKS Ubuntu 18.04` imagem do nó, basta criar um cluster executando o kubernetes v 18E ou superior, conforme mostrado abaixo

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-ga-on-existing-clusters"></a>Usar o AKS Ubuntu 18, 4 (GA) em clusters existentes

Clusters criados em kubernetes v 18E ou superior padrão para `AKS Ubuntu 18.04` imagem de nó. Os pools de nós em uma versão do kubernetes com suporte inferior a 1,18 ainda serão recebidos `AKS Ubuntu 16.04` como a imagem do nó, mas serão atualizados para `AKS Ubuntu 18.04` quando a versão do cluster ou do pool de nós kubernetes for atualizada para o v 18E ou superior.

É altamente recomendável testar suas cargas de trabalho em pools de nós do AKS Ubuntu 18, 4 antes de usar clusters em 1,18 ou superior.

Se os clusters ou pools de nós estiverem prontos para a `AKS Ubuntu 18.04` imagem de nó, você poderá simplesmente atualizá-los para um v 18E ou superior, como mostrado abaixo.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

Se você apenas deseja atualizar apenas um pool de nós:

```azurecli
az aks nodepool upgrade -name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="test-aks-ubuntu-1804-ga-on-existing-clusters"></a>Testar AKS Ubuntu 18, 4 (GA) em clusters existentes

Pools de nós criados em kubernetes v 18E ou superior padrão para `AKS Ubuntu 18.04` imagem de nó. Os pools de nós em uma versão de kubernetes com suporte inferior a 1,18 ainda serão recebidos `AKS Ubuntu 16.04` como a imagem de nó, mas serão atualizados para `AKS Ubuntu 18.04` quando a versão do pool de nós kubernetes for atualizada para o v 18E ou superior.

É altamente recomendável testar suas cargas de trabalho em pools de nós do AKS Ubuntu 18, 4 antes de atualizar seus pools de nós de produção.

Para criar um pool de nós usando a `AKS Ubuntu 18.04` imagem do nó, basta criar um pool de nós executando o kubernetes v 18E ou superior. O plano de controle de cluster precisa ser pelo menos no v 18E ou superior, mas os outros pools de nós podem permanecer em uma versão mais antiga do kubernetes.
A seguir, atualizamos primeiro o plano de controle e, em seguida, criamos um novo pool de nós com v 18E que receberá a nova versão do sistema operacional da imagem do nó.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14 --control-plane-only

az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

## <a name="container-runtime-configuration"></a>Configuração de tempo de execução de contêiner

Um tempo de execução de contêiner é um software que executa contêineres e gerencia imagens de contêiner em um nó. O tempo de execução ajuda a abstrair as chamadas sys ou a funcionalidade específica do sistema operacional (SO) para executar contêineres no Linux ou no Windows. Clusters AKS usando pools de nós do kubernetes versão 1,19 e maior uso `containerd` como seu tempo de execução de contêiner. Os clusters AKS usando kubernetes antes do v 1.19 para pools de nós usam [Moby](https://mobyproject.org/) (upstream Docker) como seu tempo de execução de contêiner.

![CRI do Docker 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) é um tempo de execução de contêiner de núcleo em conformidade com o protocolo [OCI](https://opencontainers.org/) (Open container Initiative) que fornece o conjunto mínimo de funcionalidades necessárias para executar contêineres e gerenciar imagens em um nó. Ele foi [donate](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) para a nuvem (CNCF) de computação nativa em março de 2017. A versão atual do Moby que o AKS usa já utiliza e é criada com base em `containerd` , conforme mostrado acima.

Com os `containerd` pools de nó e nó baseados em um, em vez de conversar com o `dockershim` , o kubelet se comunicará diretamente com `containerd` o plug-in do CRI (contêiner de tempo de execução da interface), removendo saltos extras no fluxo quando comparado à implementação do CRI do Docker. Dessa forma, você verá uma melhor latência de inicialização de Pod e o uso de menos recursos (CPU e memória).

Usando `containerd` o para nós AKs, a latência de inicialização de Pod melhora e o consumo de recursos de nó pelo tempo de execução do contêiner diminui. Esses aprimoramentos são habilitados por essa nova arquitetura, na qual o kubelet conversa diretamente com `containerd` o por meio do plug-in do CRI, enquanto na arquitetura Moby/Docker kubelet se comunicaria com o `dockershim` mecanismo do Docker antes de atingir `containerd` , portanto, com saltos extras no fluxo.

![CRI do Docker 2](media/cluster-configuration/containerd-cri.png)

`Containerd` funciona em todas as versões de GA do kubernetes no AKS e em cada versão de kubernetes de upstream acima de v 1.19 e dá suporte a todos os recursos kubernetes e AKS.

> [!IMPORTANT]
> Clusters com pools de nós criados no kubernetes v 1.19 ou superior padrão para `containerd` para seu tempo de execução de contêiner. Clusters com pools de nós em uma versão de kubernetes com suporte inferior a 1,19 recebem `Moby` para seu tempo de execução de contêiner, mas serão atualizados para `ContainerD` quando a versão do pool de nós kubernetes for atualizada para v 1.19 ou superior. Você ainda pode usar `Moby` pools de nós e clusters em versões mais antigas com suporte até o desligamento do suporte.
> 
> É altamente recomendável testar suas cargas de trabalho em pools de nós AKS com `containerD` antes de usar clusters em 1,19 ou superior.

### <a name="containerd-limitationsdifferences"></a>`Containerd` limitações/diferenças

* Para usar `containerd` como o tempo de execução do contêiner, você deve usar o AKs Ubuntu 18, 4 como sua imagem do sistema operacional base.
* Embora o conjunto de ferramentas do Docker ainda esteja presente nos nós, o kubernetes usa `containerd` como o tempo de execução do contêiner. Portanto, como o Moby/Docker não gerencia os contêineres criados por kubernetes nos nós, você não pode exibir ou interagir com seus contêineres usando comandos do Docker (como `docker ps` ) ou a API do Docker.
* Para `containerd` o, é recomendável usar [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) como uma CLI de substituição em vez da CLI do Docker para **solucionar problemas** de pods, contêineres e imagens de contêiner em nós kubernetes (por exemplo, `crictl ps` ). 
   * Ele não fornece a funcionalidade completa da CLI do Docker. Ele destina-se apenas à solução de problemas.
   * `crictl` oferece uma visão mais kubernetes dos contêineres, com conceitos como pods, etc. sendo presentes.
* `Containerd` configura o registro em log usando o `cri` formato de log padronizado (que é diferente do que você obtém no momento do driver JSON do Docker). Sua solução de registro em log precisa dar suporte ao `cri` formato de log (como [Azure monitor para contêineres](../azure-monitor/insights/container-insights-enable-new-cluster.md))
* Você não pode mais acessar o mecanismo do Docker, `/var/run/docker.sock` ou usar o Docker-in-Docker (DinD).
  * Se, no momento, você extrair logs de aplicativos ou dados de monitoramento do mecanismo do Docker, use algo como [Azure monitor para contêineres](../azure-monitor/insights/container-insights-enable-new-cluster.md) em vez disso. Além disso, o AKS não dá suporte à execução de nenhum comando fora de banda nos nós do agente que poderiam causar instabilidade.
  * Mesmo ao usar o Moby/Docker, a criação de imagens e o aproveitamento direto do mecanismo do Docker por meio dos métodos acima é fortemente desencorajada. O kubernetes não está totalmente ciente desses recursos consumidos, e essas abordagens apresentam vários problemas detalhados [aqui](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) e [aqui](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/), por exemplo.
* Criando imagens – você pode continuar a usar o fluxo de trabalho de Build do Docker atual normalmente, a menos que esteja criando imagens dentro do cluster AKS. Nesse caso, considere a possibilidade de alternar para a abordagem recomendada para criar imagens usando [tarefas ACR](../container-registry/container-registry-quickstart-task-cli.md)ou uma opção mais segura no cluster, como [Docker buildx](https://github.com/docker/buildx).

## <a name="generation-2-virtual-machines"></a>Máquinas virtuais de 2ª geração

O Azure dá suporte a [VMS (máquinas virtuais) de geração 2 (Gen2)](../virtual-machines/generation-2.md). As VMs de geração 2 oferecem suporte a recursos principais que não têm suporte em VMs de geração 1 (GEN1). Esses recursos incluem memória aumentada, Intel com Software Guard Extensions (Intel SGX) e memória persistente virtualizada (vPMEM).

As VMs de geração 2 usam a nova arquitetura de inicialização baseada em UEFI, em vez da arquitetura baseada em BIOS usada pelas VMs de geração 1.
Somente SKUs e tamanhos específicos dão suporte a VMs Gen2. Verifique a [lista de tamanhos com suporte](../virtual-machines/generation-2.md#generation-2-vm-sizes)para ver se sua SKU dá suporte ou requer Gen2.

Além disso, nem todas as imagens de VM dão suporte a Gen2, em AKS Gen2 VMs usarão a nova [imagem AKs Ubuntu 18, 4](#os-configuration). Esta imagem dá suporte a todos os tamanhos e SKUs de Gen2.

## <a name="ephemeral-os"></a>Sistema operacional efêmero

Por padrão, o Azure replica automaticamente o disco do sistema operacional para uma máquina virtual no armazenamento do Azure para evitar a perda de dados caso a VM precise ser realocada para outro host. No entanto, como os contêineres não são projetados para ter o estado local persistido, esse comportamento oferece um valor limitado, fornecendo algumas desvantagens, incluindo o provisionamento mais lento de nós e maior latência de leitura/gravação.

Por outro lado, os discos do sistema operacional efêmero são armazenados apenas no computador host, assim como um disco temporário. Isso fornece menor latência de leitura/gravação, juntamente com o dimensionamento mais rápido de nó e atualizações de cluster.

Como o disco temporário, um disco do sistema operacional efêmero é incluído no preço da máquina virtual, portanto, você não incorre em nenhum custo de armazenamento adicional.

> [!IMPORTANT]
>Quando um usuário não solicita explicitamente discos gerenciados para o sistema operacional, AKS usará como padrão o sistema operacional efêmero, se possível, para uma determinada configuração de nodepool.

Ao usar o sistema operacional efêmero, o disco do sistema operacional deve caber no cache da VM. Os tamanhos para o cache da VM estão disponíveis na [documentação do Azure](../virtual-machines/dv3-dsv3-series.md) entre parênteses ao lado da taxa de transferência de e/s ("tamanho do cache em GIB").

Usando o tamanho de VM padrão AKS Standard_DS2_v2 com o tamanho de disco do sistema operacional padrão de 100 GB como um exemplo, esse tamanho de VM dá suporte ao sistema operacional efêmero, mas tem apenas 86GB de tamanho de cache. Essa configuração será padronizada para discos gerenciados se o usuário não especificar explicitamente. Se um usuário explicitamente solicitou um sistema operacional efêmero, ele receberá um erro de validação.

Se um usuário solicitar o mesmo Standard_DS2_v2 com um disco de sistema operacional 60 GB, essa configuração usará como padrão o sistema operacional efêmero: o tamanho solicitado de 60 GB é menor do que o tamanho máximo de cache de 86GB.

Usando Standard_D8s_v3 com disco de sistema operacional de 100 GB, esse tamanho de VM dá suporte ao sistema operacional efêmero e tem 200 GB de espaço em cache. Se um usuário não especificar o tipo de disco do sistema operacional, o nodepool receberá o sistema operacional efêmero por padrão. 

O sistema operacional efêmero requer pelo menos 2.15.0 de versão do CLI do Azure.

### <a name="use-ephemeral-os-on-new-clusters"></a>Usar o sistema operacional efêmero em novos clusters

Configure o cluster para usar discos do sistema operacional efêmero quando o cluster for criado. Use o `--node-osdisk-type` sinalizador para definir o sistema operacional efêmero como o tipo de disco do sistema operacional para o novo cluster.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Se você quiser criar um cluster regular usando discos de sistema operacional anexados à rede, poderá fazer isso especificando `--node-osdisk-type=Managed` . Você também pode optar por adicionar mais pools de nó do sistema operacional efêmero, conforme mostrado abaixo.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Usar o sistema operacional efêmero em clusters existentes
Configure um novo pool de nós para usar discos do sistema operacional efêmero. Use o `--node-osdisk-type` sinalizador para definir como o tipo de disco do sistema operacional como o tipo de disco do sistema operacional para esse pool de nós.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> Com o sistema operacional efêmero, você pode implantar as imagens da VM e da instância até o tamanho do cache da VM. No caso do AKS, a configuração padrão de disco do so do nó usa 128 GB, o que significa que você precisa de um tamanho de VM que tenha um cache maior que 128 GB. O Standard_DS2_v2 padrão tem um tamanho de cache de 86GB, que não é grande o suficiente. O Standard_DS3_v2 tem um tamanho de cache de 172GB, que é grande o suficiente. Você também pode reduzir o tamanho padrão do disco do sistema operacional usando `--node-osdisk-size` . O tamanho mínimo para imagens AKS é 30 GB. 

Se você quiser criar pools de nós com discos de sistema operacional anexados à rede, você pode fazer isso especificando `--node-osdisk-type Managed` .

## <a name="custom-resource-group-name"></a>Nome do grupo de recursos personalizado

Quando você implanta um cluster do Serviço de Kubernetes do Azure no Azure, um segundo grupo de recursos é criado para os nós de trabalho. Por padrão, o AKS dará ao grupo de recursos do nó o nome `MC_resourcegroupname_clustername_location`, mas você também poderá fornecer seu próprio nome.

Para especificar um nome do grupo de recursos, instale a extensão da CLI do Azure da versão prévia do AKS versão 0.3.2 ou posterior. Usando a CLI do Azure, use o parâmetro `--node-resource-group` do comando `az aks create` para especificar um nome personalizado para o grupo de recursos. Se você usar um modelo do Azure Resource Manager para implantar um cluster do AKS, defina o nome do grupo de recursos usando a propriedade `nodeResourceGroup`.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

O grupo de recursos secundário é criado automaticamente pelo provedor de recursos do Azure na própria assinatura. Você só pode especificar o nome do grupo de recursos personalizado quando o cluster é criado. 

Ao trabalhar com o grupo de recursos do nó, tenha em mente que não é possível:

- Especificar um grupo de recursos existente para o grupo de recursos do nó.
- Especificar uma assinatura diferente para o grupo de recursos do nó.
- Alterar o nome do grupo de recursos do nó depois que o cluster for criado.
- Especificar nomes para os recursos gerenciados dentro do grupo de recursos do nó.
- Modificar ou excluir marcas criadas pelo Azure de recursos gerenciados dentro do grupo de recursos do nó.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [atualizar as imagens de nó](node-image-upgrade.md) no cluster.
- Confira [Atualizar um cluster do AKS (Serviço de Kubernetes do Azure)](upgrade-cluster.md) para saber como atualizar o cluster para a versão mais recente do Kubernetes.
- Leia mais sobre [ `containerd` e kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- Confira a lista de [Perguntas frequentes sobre o AKS](faq.md) para encontrar respostas a algumas perguntas comuns sobre o AKS.
- Leia mais sobre [discos do sistema operacional efêmero](../virtual-machines/ephemeral-os-disks.md).


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
