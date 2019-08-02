---
title: Alta disponibilidade e recuperação de desastres no serviço de Kubernetes do Azure (AKS)
description: Aprenda as práticas recomendadas de um operador cluster para atingir o máximo de tempo de atividade para seus aplicativos, fornecendo alta disponibilidade e a preparação para recuperação de desastres no serviço de Kubernetes do Azure (AKS).
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.openlocfilehash: 4d4535af1814ab1250bbd56c989b4849013adff6
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614835"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para continuidade dos negócios e recuperação de desastres no Serviço de Kubernetes do Azure (AKS)

À medida que você vai gerenciando clusters no Serviço de Kubernetes do Azure (AKS), o tempo de atividade do aplicativo vai ficando mais importante. O AKS fornece alta disponibilidade usando vários nós em um conjunto de disponibilidade. Mas esses vários nós não protegem seu sistema contra uma falha de região. Para maximizar o tempo de atividade, planeje com antecedência para manter a continuidade dos negócios e preparar para recuperação de desastres.

Este artigo se concentra em como planejar a continuidade dos negócios e recuperação de desastres no AKS. Você aprenderá como:

> [!div class="checklist"]
> * Planejar clusters AKS em várias regiões.
> * Rotear o tráfego entre vários clusters, usando o Gerenciador de tráfego do Azure.
> * Use a replicação geográfica para seus registros de imagem de contêiner.
> * Plano para o estado do aplicativo entre vários clusters.
> * Replica o armazenamento em várias regiões.

## <a name="plan-for-multiregion-deployment"></a>Plano de implantação multiregion

**Melhor prática**: Quando você implantar vários clusters AKS, escolha regiões em que o AKS está disponível e use as regiões emparelhadas.

Um cluster do AKS é implantado em uma única região. Para proteger seu sistema contra falhas de região, implante seu aplicativo em vários clusters AKS em regiões diferentes. Ao planejar onde implantar o cluster do AKS, considere:

* [**Disponibilidade de região do AKS**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Escolha regiões perto de seus usuários. AKS continuamente se expande em novas regiões.
* [**Regiões emparelhadas do Azure**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions): Para sua área geográfica, escolha duas regiões emparelhadas umas com as outras. Regiões emparelhadas coordenar as atualizações de plataforma e priorizar os esforços de recuperação onde for necessário.
* **Disponibilidade do serviço**: Decida se as regiões emparelhadas devem ser quente/hot, hot/passiva ou ativa/passiva. Você deseja executar ambas as regiões ao mesmo tempo, com uma região *pronto* para começar a servir o tráfego? Ou você deseja que uma região para ter tempo para se preparar servir o tráfego?

Disponibilidade de região do AKS e regiões emparelhadas são considerados conjunto. Implante seus clusters do AKS em regiões emparelhadas que são projetadas para gerenciar a recuperação de desastre de região juntas. Por exemplo, o AKS está disponível no Leste dos EUA e Oeste dos EUA. Essas regiões são combinadas. Quando você estiver criando uma estratégia de continuidade de negócios/recuperação de desastres de AKS, escolha essas duas regiões.

Quando você implanta seu aplicativo, adicione outra etapa ao seu pipeline de CI/CD para implantar esses vários clusters AKS. Se você não atualizar seus pipelines de implantação, aplicativos podem ser implantados em apenas uma das suas regiões e clusters AKS. Tráfego de cliente que é direcionado para uma região secundária não receberão as atualizações mais recentes do código.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Usar o Gerenciador de Tráfego do Azure para rotear o tráfego

**Melhor prática**: O Azure Traffic Manager pode direcionar clientes para sua instância de cluster e aplicativo de AKS mais próximo. Para obter melhor desempenho e redundância, direcione todo o tráfego de aplicativo por meio do Gerenciador de tráfego antes que ele entre no cluster do AKS.

Se você tiver vários clusters AKS em regiões diferentes, use o Gerenciador de tráfego para controlar como o tráfego flui para os aplicativos executados em cada cluster. O [Gerenciador de Tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/) é um balanceador de carga de tráfego com base em DNS que pode distribuir o tráfego de rede entre regiões. Use o Gerenciador de tráfego para rotear usuários com base no tempo de resposta do cluster ou com base na geografia.

![AKS com o Gerenciador de tráfego](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Os clientes que têm um único cluster AKS normalmente conecte-se para o nome IP ou DNS do serviço de um determinado aplicativo. Em uma implantação multicluster, os clientes devem se conectar a um nome DNS do Traffic Manager que aponta para os serviços em cada cluster do AKS. Defina esses serviços usando pontos de extremidade do Gerenciador de tráfego. Cada ponto de extremidade é o *IP do balanceador de carga de serviço*. Use essa configuração para direcionar o tráfego de rede do ponto de extremidade de Gerenciador de tráfego em uma região para o ponto de extremidade em uma região diferente.

![Geográfico de roteamento por meio do Gerenciador de tráfego](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

O Gerenciador de tráfego executa pesquisas DNS e retorna o ponto de extremidade mais apropriado do usuário. Perfis aninhados podem priorizar um local primário. Por exemplo, os usuários geralmente devem se conectar à sua região geográfica mais próxima. Se essa região tem um problema, o Traffic Manager direciona os usuários para uma região secundária em vez disso. Essa abordagem garante que os clientes podem conectar a uma instância de aplicativo, mesmo se sua região geográfica mais próximo não estiver disponível.

Para obter informações sobre como configurar pontos de extremidade e roteamento, consulte [configurar o método de roteamento de tráfego geográfico usando o Gerenciador de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Roteamento de aplicativo de camada 7 com o serviço de porta da frente do Azure

O Gerenciador de tráfego usa o DNS (camada 3) para o tráfego de forma. [O serviço do Azure da frente](https://docs.microsoft.com/azure/frontdoor/front-door-overview) fornece uma opção de roteamento de HTTP/HTTPS (camada 7). Recursos adicionais do serviço de porta da frente do Azure incluem SSL encerramento, domínio personalizado, firewall do aplicativo web, reescrita de URL e afinidade de sessão. Examine as necessidades de seu tráfego de aplicativo para entender qual é a solução mais adequada.

## <a name="enable-geo-replication-for-container-images"></a>Habilitar a replicação geográfica para imagens de contêiner

**Melhor prática**: Store suas imagens de contêiner no registro de contêiner do Azure e fazer a replicação geográfica do registro para cada região do AKS.

Para implantar e executar seus aplicativos no AKS, você precisará ter uma maneira de armazenar e efetuar pull das imagens de contêiner. Registro de contêiner integra-se com o AKS, portanto, ele pode armazenar suas imagens de contêiner ou gráficos Helm com segurança. Registro de contêiner dá suporte a replicação geográfica com vários mestres para replicar automaticamente suas imagens para as regiões do Azure em todo o mundo. 

Para melhorar o desempenho e disponibilidade, use a replicação geográfica do registro de contêiner para criar um registro em cada região em que você tem um cluster do AKS. Cada cluster do AKS, em seguida, efetua pull de imagens de contêiner do registro de contêiner local na mesma região:

![Contêiner replicação geográfica do registro para imagens de contêiner](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Quando você usa replicação geográfica do registro de contêiner para imagens de pull da mesma região, os resultados são:

* **Com mais rapidez**: Efetuar o pull de imagens de conexões de rede de alta velocidade e baixa latência na mesma região do Azure.
* **Mais confiável**: Se uma região não estiver disponível, o cluster do AKS efetua pull as imagens de um registro de contêiner disponível.
* **Cheaper**: Não há nenhum encargo de saída de rede entre datacenters.

Replicação geográfica é um recurso do *Premium* registros de contêiner do SKU. Para obter informações sobre como configurar a replicação geográfica, consulte [replicação geográfica do registro de contêiner](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Remover o estado do serviço de dentro dos contêineres

**Melhor prática**: Sempre que possível, não armazene o estado do serviço dentro do contêiner. Em vez disso, use uma plataforma do Azure como um serviço (PaaS) que dá suporte a – replicação.

*Estado do serviço* refere-se aos dados na memória ou no disco que um serviço requer para funcionar. O estado inclui as estruturas de dados e variáveis de membro que o serviço lê e grava. Dependendo de como o serviço é estruturado, o estado também pode incluir arquivos ou outros recursos que são armazenados no disco. Por exemplo, o estado pode incluir os arquivos de que um banco de dados usa para armazenar logs de transações e dados.

Estado pode ser externalizado ou colocado com o código que manipula o estado. Normalmente, você Externalizar estado usando um banco de dados ou outro armazenamento de dados que são executados em diferentes computadores pela rede ou que é executado fora do processo no mesmo computador.

Contêineres e microsserviços são mais resilientes quando os processos que são executados dentro deles não mantêm o estado. Como os aplicativos quase sempre contêm algum estado, use uma solução de PaaS, como o banco de dados do Azure para MySQL, banco de dados do Azure para PostgreSQL, ou o banco de dados SQL.

Para criar aplicativos portáteis, consulte as diretrizes a seguir:

* [A metodologia de aplicativo de 12 fatores](https://12factor.net/)
* [Executar um aplicativo web em várias regiões do Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Criar um plano de migração de armazenamento

**Melhor prática**: Se você usar o armazenamento do Azure, preparar e testar como migrar o armazenamento da região primária para a região de backup.

Os aplicativos podem usar o armazenamento do Azure para seus dados. Como os aplicativos são distribuídos entre vários clusters AKS em regiões diferentes, você precisará manter o armazenamento sincronizado. Aqui estão duas maneiras comuns de replicar o armazenamento:

* Replicação assíncrona baseada em infraestrutura
* Replicação assíncrona baseada em aplicativo

### <a name="infrastructure-based-asynchronous-replication"></a>Replicação assíncrona baseada em infraestrutura

Os aplicativos possam exigir armazenamento persistente, mesmo depois que um pod é excluído. No Kubernetes, você pode usar volumes persistentes para o armazenamento de dados de persistência. Volumes persistentes são montados em uma VM de nó e, em seguida, expostos para os pods. Volumes persistentes siga pods, mesmo se os pods são movidos para um nó diferente dentro do mesmo cluster.

A estratégia de replicação que você usa depende de sua solução de armazenamento. Soluções de armazenamento comuns, como [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [Ceph](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [torre](https://rook.io/docs/rook/master/disaster-recovery.html), e [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) fornecem suas próprias diretrizes sobre recuperação de desastre e replicação.

A estratégia típica é fornecer um ponto de armazenamento comum em que os aplicativos podem gravar seus dados. Esses dados são replicados entre regiões e acessados localmente.

![Replicação assíncrona baseada em infraestrutura](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Se você usar Azure Managed Disks, você pode escolher as soluções de replicação e recuperação de desastres como estes:

* [Velero no Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Replicação assíncrona baseada em aplicativo

Atualmente, o Kubernetes não fornece nenhuma implementação nativa para a replicação assíncrona baseada em aplicativo. Porque os contêineres e Kubernetes são flexíveis, qualquer abordagem tradicional de aplicativo ou linguagem deve funcionar. Normalmente, os próprios aplicativos replicam as solicitações de armazenamento, em seguida, são gravadas no armazenamento de dados subjacente de cada cluster.

![Replicação assíncrona baseada em aplicativo](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Próximas etapas

Este artigo se concentra na continuidade de negócios e considerações de recuperação de desastres para clusters do AKS. Para obter mais informações sobre as operações de cluster do AKS, consulte estes artigos sobre práticas recomendadas:

* [Isolamento de multilocação e cluster][aks-best-practices-cluster-isolation]
* [Recursos básicos de Agendador Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
