---
title: Migrar para o serviço kubernetes do Azure (AKS)
description: Migre para o serviço kubernetes do Azure (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 5881d03603002cc8d5bef1680083f6b4145bc77f
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526680"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrar para o serviço kubernetes do Azure (AKS)

Este artigo ajuda você a planejar e executar uma migração bem-sucedida para o AKS (serviço kubernetes do Azure). Para ajudá-lo a tomar decisões importantes, este guia fornece detalhes para a configuração atual recomendada para o AKS. Este artigo não abrange todos os cenários e, quando apropriado, o artigo contém links para informações mais detalhadas para o planejamento de uma migração bem-sucedida.

Este documento pode ser usado para ajudar a dar suporte aos seguintes cenários:

* Migrando um cluster AKS apoiado por [conjuntos de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) para conjuntos de [dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/overview.md)
* Migrando um cluster AKS para usar um [balanceador de carga SKU padrão](./load-balancer-standard.md)
* Migrando do [serviço de contêiner do Azure (ACS)-desativando 31 de janeiro de 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) para AKs
* Migrando do [mecanismo do AKS](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) para o AKs
* Migrando de clusters kubernetes não baseados no Azure para AKS
* Movendo recursos existentes para uma região diferente

Ao migrar, verifique se sua versão de kubernetes de destino está dentro da janela com suporte para AKS. Se estiver usando uma versão mais antiga, ela pode não estar dentro do intervalo com suporte e exigir que as versões de atualização sejam suportadas pelo AKS. Consulte [versões de kubernetes com suporte do AKS](./supported-kubernetes-versions.md) para obter mais informações.

Se você estiver migrando para uma versão mais recente do kubernetes, examine a [versão do kubernetes e a política de suporte à distorção de versão](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Várias ferramentas de código-fonte aberto podem ajudar com sua migração, dependendo do seu cenário:

* [Velero](https://velero.io/) (requer kubernetes 1.7 +)
* [Extensão da CLI do Azure Kube](https://github.com/yaron2/azure-kube-cli)
* [Returno](https://github.com/mhausenblas/reshifter)

Neste artigo, resumiremos os detalhes de migração para:

> [!div class="checklist"]
> * AKS com Standard Load Balancer e conjuntos de dimensionamento de máquinas virtuais
> * Serviços do Azure anexados existentes
> * Garantir cotas válidas
> * Alta disponibilidade e continuidade dos negócios
> * Considerações para aplicativos sem estado
> * Considerações para aplicativos com estado
> * Implantação de sua configuração de cluster

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS com Standard Load Balancer e conjuntos de dimensionamento de máquinas virtuais

O AKS é um serviço gerenciado que oferece recursos exclusivos com menor sobrecarga de gerenciamento. Como resultado de ser um serviço gerenciado, você deve selecionar entre um conjunto de [regiões](./quotas-skus-regions.md) ao qual o AKs dá suporte. A transição do cluster existente para o AKS pode exigir a modificação de seus aplicativos existentes para que eles permaneçam íntegros no plano de controle gerenciado do AKS.

Recomendamos o uso de clusters AKS apoiados por [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/index.yml) e o [Standard Load Balancer do Azure](./load-balancer-standard.md) para garantir que você obtenha recursos como [vários pools de nós](./use-multiple-node-pools.md), [zonas de disponibilidade](../availability-zones/az-overview.md), [intervalos de IP autorizados](./api-server-authorized-ip-ranges.md), [autodimensionador de cluster](./cluster-autoscaler.md), [Azure Policy para AKs](../governance/policy/concepts/policy-for-kubernetes.md)e outros novos recursos à medida que forem lançados.

Os clusters AKS apoiados por [conjuntos de disponibilidade de máquina virtual](../virtual-machines/availability.md#availability-sets) não têm suporte para muitos desses recursos.

O exemplo a seguir cria um cluster AKS com um pool de nós único apoiado por um conjunto de dimensionamento de máquinas virtuais. Ele usa um balanceador de carga padrão. Ele também habilita o dimensionador automático de cluster no pool de nós e define o mínimo de *1* o máximo de *3* nós:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>Serviços do Azure anexados existentes

Ao migrar clusters, você pode ter anexado serviços externos do Azure. Eles não exigem recriação de recursos, mas eles exigirão a atualização de conexões de clusters anteriores para novos para manter a funcionalidade.

* Registro de Contêiner do Azure
* Log Analytics
* Application Insights
* Gerenciador de Tráfego
* Conta de armazenamento
* Bancos de dados externos

## <a name="ensure-valid-quotas"></a>Garantir cotas válidas

Como as máquinas virtuais serão implantadas na sua assinatura durante a migração, você precisa verificar se suas cotas e limites são suficientes para esses recursos. Talvez seja necessário solicitar um aumento na [cota de vCPU](../azure-portal/supportability/per-vm-quota-requests.md).

Talvez seja necessário solicitar um aumento nas [cotas de rede](../azure-portal/supportability/networking-quota-requests.md) para garantir que você não utilize IPS. Consulte [Networking and IP Ranges for AKs](./configure-kubenet.md) para obter informações adicionais.

Para obter mais informações, consulte [limites de serviço e assinatura do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md). Para verificar suas cotas atuais, na portal do Azure, vá para a [folha assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), selecione sua assinatura e, em seguida, selecione **uso + cotas**.

## <a name="high-availability-and-business-continuity"></a>Alta disponibilidade e continuidade dos negócios

Se seu aplicativo não puder lidar com o tempo de inatividade, será necessário seguir as práticas recomendadas para cenários de migração de alta disponibilidade.  As práticas recomendadas para planejamento de continuidade de negócios complexos, recuperação de desastres e maximização do tempo de atividade estão além do escopo deste documento.  Leia mais sobre [as práticas recomendadas para continuidade dos negócios e recuperação de desastres no AKs (serviço kubernetes do Azure)](./operator-best-practices-multi-region.md) para saber mais.

Para aplicativos complexos, geralmente a migração ocorre ao longo do tempo em vez de uma só vez. Isso significa que os ambientes novos e antigos podem precisar se comunicar pela rede. Os aplicativos que usaram anteriormente `ClusterIP` serviços para comunicação podem precisar ser expostos como tipo `LoadBalancer` e ser protegidos adequadamente.

Para concluir a migração, você desejará apontar os clientes para os novos serviços em execução no AKS. Recomendamos que você redirecione o tráfego atualizando o DNS para apontar para o Load Balancer que fica na frente do cluster AKS.

O [Gerenciador de tráfego do Azure](../traffic-manager/index.yml) pode direcionar os clientes para o cluster kubernetes desejado e a instância do aplicativo.  O Gerenciador de tráfego é um balanceador de carga de tráfego baseado em DNS que pode distribuir o tráfego de rede entre regiões.  Para obter o melhor desempenho e redundância, direcione todo o tráfego do aplicativo pelo Gerenciador de tráfego antes que ele vá para o cluster AKS.  Em uma implantação de multicluster, os clientes devem se conectar a um nome DNS do Gerenciador de tráfego que aponta para os serviços em cada cluster do AKS. Defina esses serviços usando os pontos de extremidade do Gerenciador de tráfego. Cada ponto de extremidade é o *IP de balanceador de carga de serviço*. Use essa configuração para direcionar o tráfego de rede do ponto de extremidade do Gerenciador de tráfego em uma região para o ponto de extremidade em uma região diferente.

![AKS com o Gerenciador de tráfego](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

O [serviço de porta frontal do Azure](../frontdoor/front-door-overview.md) é outra opção para rotear o tráfego para clusters AKs.  O Azure Front Door Service permite que você defina, gerencie e monitore o roteamento global para seu tráfego da Web otimizando para melhor desempenho e failover global instantâneo para ter alta disponibilidade. 

### <a name="considerations-for-stateless-applications"></a>Considerações para aplicativos sem estado

A migração de aplicativos sem monitoração de estado é o caso mais simples. Aplique suas definições de recurso (YAML ou Helm) ao novo cluster, verifique se tudo funciona conforme o esperado e redirecione o tráfego para ativar o novo cluster.

### <a name="considerations-for-stateful-applications"></a>Considerações para aplicativos com estado

Planeje cuidadosamente a migração de aplicativos com estado para evitar perda de dados ou tempo de inatividade inesperado.

Se você usar os arquivos do Azure, poderá montar o compartilhamento de arquivos como um volume no novo cluster:
* [Montar arquivos estáticos do Azure como um volume](./azure-files-volume.md#mount-the-file-share-as-a-volume)

Se você usar o Azure Managed Disks, só poderá montar o disco se desanexado a qualquer VM:
* [Montar disco estático do Azure como um volume](./azure-disk-volume.md#mount-disk-as-volume)

Se nenhuma dessas abordagens funcionar, você poderá usar as opções de backup e restauração:
* [Velero no Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

#### <a name="azure-files"></a>Arquivos do Azure

Ao contrário dos discos, os Arquivos do Azure podem ser montados em vários hosts simultaneamente. No cluster do AKS, o Azure e o kubernetes não impedem que você crie um pod que o cluster do ACS ainda usa. Para evitar a perda de dados e o comportamento inesperado, verifique se os clusters não gravam nos mesmos arquivos ao mesmo tempo.

Se seu aplicativo puder hospedar várias réplicas que apontam para o mesmo compartilhamento de arquivos, siga as etapas de migração sem monitoração de estado e implante suas definições de YAML no novo cluster. Caso contrário, uma abordagem de migração possível envolve as seguintes etapas:

* Valide se o aplicativo está funcionando corretamente.
* Aponte seu tráfego ao vivo para o novo cluster AKS.
* Desconecte o cluster antigo.

Se você quiser iniciar com um compartilhamento vazio e fazer uma cópia dos dados de origem, poderá usar os [`az storage file copy`](/cli/azure/storage/file/copy) comandos para migrar seus dados.


#### <a name="migrating-persistent-volumes"></a>Migrando volumes persistentes

Se estiver migrando volumes persistentes existentes para o AKS, você geralmente seguirá estas etapas:

* Desative as gravações no aplicativo. (Essa etapa é opcional e requer tempo de inatividade.)
* Tire instantâneos dos discos.
* Crie novos Managed disks a partir dos instantâneos.
* Crie volumes persistentes no AKS.
* Atualize as especificações de pod para [usar volumes existentes](./azure-disk-volume.md) em vez de PersistentVolumeClaims (provisionamento estático).
* Implante seu aplicativo no AKS.
* Valide se o aplicativo está funcionando corretamente.
* Aponte seu tráfego ao vivo para o novo cluster AKS.

> [!IMPORTANT]
> Se optar por não desativar as gravações, você precisará replicar os dados para a nova implantação. Caso contrário, você perderá os dados que foram gravados depois de obter os instantâneos de disco.

Algumas ferramentas de código-fonte aberto podem ajudá-lo a criar discos gerenciados e a migrar volumes entre clusters kubernetes:

* [CLI do Azure extensão de cópia de disco](https://github.com/noelbundick/azure-cli-disk-copy-extension) copia e converte discos em grupos de recursos e regiões do Azure.
* A [extensão da CLI do Azure Kube](https://github.com/yaron2/azure-kube-cli) enumera volumes do ACS kubernetes e os migra para um cluster AKs.


### <a name="deployment-of-your-cluster-configuration"></a>Implantação de sua configuração de cluster

Recomendamos que você use seu pipeline de integração contínua (CI) e entrega contínua (CD) para implantar uma configuração válida em AKS. Você pode usar Azure Pipelines para [Compilar e implantar seus aplicativos no AKs](/azure/devops/pipelines/ecosystems/kubernetes/aks-template). Clone suas tarefas de implantação existentes e certifique-se de que `kubeconfig` aponta para o novo cluster AKs.

Se isso não for possível, exporte as definições de recursos do cluster kubernetes existente e aplique-as ao AKS. É possível usar `kubectl` para exportar objetos.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Movendo recursos existentes para outra região

Talvez você queira mover o cluster AKS para uma [região diferente com suporte do AKS][region-availability]. Recomendamos que você crie um novo cluster na outra região e, em seguida, implante seus recursos e aplicativos em seu novo cluster. Além disso, se você tiver qualquer serviço como [Azure dev Spaces][azure-dev-spaces] em execução no cluster AKs, também precisará instalar e configurar esses serviços no cluster na nova região.


Neste artigo, resumimos os detalhes de migração para:

> [!div class="checklist"]
> * AKS com Standard Load Balancer e conjuntos de dimensionamento de máquinas virtuais
> * Serviços do Azure anexados existentes
> * Garantir cotas válidas
> * Alta disponibilidade e continuidade dos negócios
> * Considerações para aplicativos sem estado
> * Considerações para aplicativos com estado
> * Implantação de sua configuração de cluster


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: ../dev-spaces/index.yml
