---
title: Práticas recomendadas para continuidade de negócios e recuperação de desastre do AKS
description: Conheça as práticas recomendadas de um operador de cluster para atingir o tempo de atividade máximo para seus aplicativos, fornecendo alta disponibilidade e preparando a recuperação de desastres no AKS (serviço kubernetes do Azure).
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 3ff8406a3634fa946ab8ce7aca694bbc57d556a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97976394"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para continuidade dos negócios e recuperação de desastres no Serviço de Kubernetes do Azure (AKS)

À medida que você vai gerenciando clusters no Serviço de Kubernetes do Azure (AKS), o tempo de atividade do aplicativo vai ficando mais importante. Por padrão, o AKS fornece alta disponibilidade usando vários nós em um [conjunto de dimensionamento de máquinas virtuais (VMSS)](../virtual-machine-scale-sets/overview.md). Mas esses vários nós não protegem o seu sistema contra uma falha de região. Para maximizar seu tempo de atividade, planeje com antecedência para manter a continuidade dos negócios e se preparar para a recuperação de desastres.

Este artigo se concentra em como planejar a continuidade dos negócios e a recuperação de desastres no AKS. Você aprenderá como:

> [!div class="checklist"]
> * Planeje os clusters AKS em várias regiões.
> * Rotear o tráfego entre vários clusters usando o Gerenciador de tráfego do Azure.
> * Use a replicação geográfica para seus registros de imagem de contêiner.
> * Planeje o estado do aplicativo em vários clusters.
> * Replique o armazenamento em várias regiões.

## <a name="plan-for-multiregion-deployment"></a>Planejar a implantação em multiregião

**Prática recomendada**: ao implantar vários clusters AKs, escolha regiões onde AKs está disponível e use regiões emparelhadas.

Um cluster do AKS é implantado em uma única região. Para proteger seu sistema contra falhas de região, implante seu aplicativo em vários clusters AKS em regiões diferentes. Ao planejar onde implantar o cluster AKS, considere:

* [**Disponibilidade da região AKs**](./quotas-skus-regions.md#region-availability): escolha regiões perto de seus usuários. AKS se expande continuamente em novas regiões.
* [**Regiões emparelhadas do Azure**](../best-practices-availability-paired-regions.md): para sua área geográfica, escolha duas regiões emparelhadas entre si. Regiões emparelhadas coordenam atualizações de plataforma e priorizam os esforços de recuperação quando necessário.
* **Disponibilidade do serviço**: decida se suas regiões emparelhadas devem ser quentes/quentes, quentes ou quentes ou quentes/frias. Deseja executar ambas as regiões ao mesmo tempo, com uma região *pronta* para começar a fornecer tráfego? Ou você deseja que uma região tenha tempo para se preparar para atender ao tráfego?

A disponibilidade da região AKS e as regiões emparelhadas são uma consideração conjunta. Implante seus clusters do AKS em regiões emparelhadas que são projetadas para gerenciar a recuperação de desastre de região juntas. Por exemplo, o AKS está disponível no Leste dos EUA e no Oeste dos EUA. Essas regiões são emparelhadas. Escolha essas duas regiões quando estiver criando uma estratégia de BC/DR AKS.

Ao implantar seu aplicativo, adicione outra etapa ao pipeline de CI/CD para implantar nesses vários clusters AKS. Se você não atualizar seus pipelines de implantação, os aplicativos poderão ser implantados em apenas uma de suas regiões e clusters AKS. O tráfego de cliente direcionado para uma região secundária não receberá as atualizações de código mais recentes.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Usar o Gerenciador de Tráfego do Azure para rotear o tráfego

**Prática recomendada**: o Gerenciador de tráfego do Azure pode direcionar os clientes para o cluster de AKs e a instância de aplicativo mais próximos. Para obter o melhor desempenho e redundância, direcione todo o tráfego do aplicativo pelo Gerenciador de tráfego antes que ele vá para o cluster AKS.

Se você tiver vários clusters AKS em regiões diferentes, use o Gerenciador de tráfego para controlar como o tráfego flui para os aplicativos que são executados em cada cluster. O [Gerenciador de Tráfego do Azure](../traffic-manager/index.yml) é um balanceador de carga de tráfego com base em DNS que pode distribuir o tráfego de rede entre regiões. Use o Gerenciador de tráfego para encaminhar usuários com base no tempo de resposta do cluster ou com base na geografia.

![AKS com o Gerenciador de tráfego](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Os clientes que têm um único cluster AKS normalmente se conectam ao nome DNS ou IP do serviço de um determinado aplicativo. Em uma implantação de multicluster, os clientes devem se conectar a um nome DNS do Gerenciador de tráfego que aponta para os serviços em cada cluster do AKS. Defina esses serviços usando os pontos de extremidade do Gerenciador de tráfego. Cada ponto de extremidade é o *IP de balanceador de carga de serviço*. Use essa configuração para direcionar o tráfego de rede do ponto de extremidade do Gerenciador de tráfego em uma região para o ponto de extremidade em uma região diferente.

![Roteamento geográfico por meio do Gerenciador de tráfego](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

O Gerenciador de tráfego executa pesquisas DNS e retorna o ponto de extremidade mais apropriado de um usuário. Os perfis aninhados podem priorizar um local primário. Por exemplo, os usuários devem geralmente se conectar à região geográfica mais próxima. Se essa região tiver um problema, o Gerenciador de tráfego direcionará os usuários para uma região secundária. Essa abordagem garante que os clientes possam se conectar a uma instância do aplicativo, mesmo que sua região geográfica mais próxima esteja indisponível.

Para obter informações sobre como configurar pontos de extremidade e roteamento, consulte [Configurar o método de roteamento de tráfego geográfico usando o Gerenciador de tráfego](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).

### <a name="application-routing-with-azure-front-door-service"></a>Roteamento de aplicativos com o serviço de porta frontal do Azure

Usando o protocolo anycast baseado em TCP dividido, o [serviço de porta frontal do Azure](../frontdoor/front-door-overview.md) garante que os usuários finais se conectem imediatamente ao pop da porta frontal mais próximo (ponto de presença). Recursos adicionais do serviço de porta frontal do Azure incluem término de TLS, domínio personalizado, firewall do aplicativo Web, reescrita de URL e afinidade de sessão. Examine as necessidades de seu tráfego de aplicativo para entender qual é a solução mais adequada.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Regiões de interconexão com emparelhamento de rede virtual global

Se os clusters precisarem se comunicar entre si, conectar ambas as redes virtuais entre si pode ser obtido por meio do [emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md). Essa tecnologia interconecta redes virtuais entre si, fornecendo alta largura de banda na rede de backbone da Microsoft, mesmo em regiões geográficas diferentes.

Um pré-requisito para emparelhar as redes virtuais em que os clusters AKS estão em execução é usar o Load Balancer padrão no cluster AKS, para que os serviços do kubernetes possam ser acessados pelo emparelhamento de rede virtual.

## <a name="enable-geo-replication-for-container-images"></a>Habilitar a replicação geográfica para imagens de contêiner

**Prática recomendada**: armazenar suas imagens de contêiner no registro de contêiner do Azure e replicar geograficamente o registro para cada região do AKS.

Para implantar e executar seus aplicativos no AKS, você precisará ter uma maneira de armazenar e efetuar pull das imagens de contêiner. O registro de contêiner se integra com o AKS, portanto, ele pode armazenar com segurança suas imagens de contêiner ou gráficos de Helm. O registro de contêiner dá suporte à replicação geográfica multimestre para replicar automaticamente suas imagens para regiões do Azure em todo o mundo. 

Para melhorar o desempenho e a disponibilidade, use a replicação geográfica do registro de contêiner para criar um registro em cada região em que você tenha um cluster AKS. Cada cluster AKS, em seguida, efetua pull das imagens de contêiner do registro de contêiner local na mesma região:

![Replicação geográfica do registro de contêiner para imagens de contêiner](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Quando você usa a replicação geográfica do registro de contêiner para efetuar pull de imagens da mesma região, os resultados são:

* **Mais rápido**: você efetua pull de imagens de conexões de rede de alta velocidade e baixa latência na mesma região do Azure.
* **Mais confiável**: se uma região não estiver disponível, o cluster AKs extrairá as imagens de um registro de contêiner disponível.
* Mais **barato**: não há nenhum encargo de saída de rede entre data centers.

A replicação geográfica é um recurso de registros de contêiner de SKU *Premium* . Para obter informações sobre como configurar a replicação geográfica, consulte [replicação geográfica do registro de contêiner](../container-registry/container-registry-geo-replication.md).

## <a name="remove-service-state-from-inside-containers"></a>Remover o estado do serviço de dentro dos contêineres

**Prática recomendada**: sempre que possível, não armazene o estado do serviço dentro do contêiner. Em vez disso, use uma PaaS (plataforma como serviço) do Azure que dá suporte à replicação em várias regiões.

*Estado do serviço* refere-se aos dados na memória ou em disco que um serviço requer para funcionar. O estado inclui as estruturas de dados e variáveis de membro que o serviço lê e grava. Dependendo de como o serviço é arquitetado, o estado também pode incluir arquivos ou outros recursos armazenados no disco. Por exemplo, o estado pode incluir os arquivos que um banco de dados usa para armazenar os logs de transações e de registros.

O estado pode ser externo ou colocado em um código que manipule o estado. Normalmente, você externamente o estado usando um banco de dados ou outro armazenamento que é executado em máquinas diferentes na rede ou que é executado fora do processo no mesmo computador.

Os contêineres e os microserviços são mais resilientes quando os processos executados dentro deles não mantêm o estado. Como os aplicativos quase sempre contêm algum estado, use uma solução PaaS, como Azure Cosmos DB, banco de dados do Azure para PostgreSQL, banco de dados do Azure para MySQL ou banco de dados SQL do Azure.

Para criar aplicativos portáteis, consulte as seguintes diretrizes:

* [A metodologia do aplicativo de 12 fatores](https://12factor.net/)
* [Executar um aplicativo Web em várias regiões do Azure](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Criar um plano de migração de armazenamento

**Prática recomendada**: se você usar o armazenamento do Azure, prepare e teste como migrar seu armazenamento da região primária para a região de backup.

Seus aplicativos podem usar o armazenamento do Azure para seus dados. Como seus aplicativos estão espalhados por vários clusters AKS em regiões diferentes, você precisa manter o armazenamento sincronizado. Aqui estão duas maneiras comuns de replicar o armazenamento:

* Replicação assíncrona baseada em infraestrutura
* Replicação assíncrona baseada em aplicativo

### <a name="infrastructure-based-asynchronous-replication"></a>Replicação assíncrona baseada em infraestrutura

Seus aplicativos podem exigir armazenamento persistente mesmo depois que um pod é excluído. No kubernetes, você pode usar volumes persistentes para manter o armazenamento de dados. Os volumes persistentes são montados em uma VM de nó e, em seguida, expostos ao pods. Os volumes persistentes seguem pods, mesmo se os pods forem movidos para um nó diferente dentro do mesmo cluster.

A estratégia de replicação que você usa depende da sua solução de armazenamento. Soluções de armazenamento comuns, como [Gluster](https://docs.gluster.org/en/latest/Administrator-Guide/Geo-Replication/), [ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [torre](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)e [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) , fornecem suas próprias diretrizes sobre recuperação de desastres e replicação.

A estratégia típica é fornecer um ponto de armazenamento comum em que os aplicativos possam gravar seus dados. Esses dados são replicados entre regiões e acessados localmente.

![Replicação assíncrona baseada em infraestrutura](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Se você usar o Azure Managed Disks, há algumas opções que podem ser usadas para lidar com a replicação e a recuperação de desastres. [Velero no Azure][velero] e [Kasten][kasten] são soluções de backup nativas para kubernetes, mas não têm suporte.

### <a name="application-based-asynchronous-replication"></a>Replicação assíncrona baseada em aplicativo

Atualmente, o kubernetes não fornece implementação nativa para replicação assíncrona baseada em aplicativo. Como contêineres e kubernetes são livremente acoplados, qualquer abordagem tradicional de aplicativo ou linguagem deve funcionar. Normalmente, os próprios aplicativos replicam as solicitações de armazenamento, que são então gravadas no armazenamento de dados subjacente de cada cluster.

![Replicação assíncrona baseada em aplicativo](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Próximas etapas

Este artigo se concentra na continuidade dos negócios e nas considerações de recuperação de desastres para clusters AKS. Para obter mais informações sobre as operações de cluster no AKS, consulte estes artigos sobre as práticas recomendadas:

* [Isolamento de multilocação e cluster][aks-best-practices-cluster-isolation]
* [Recursos básicos de Agendador Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md

[velero]: https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md
[kasten]: https://www.kasten.io/