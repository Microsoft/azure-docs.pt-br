---
title: Visão geral dos grupos de disponibilidade Always On do SQL Server
description: Este artigo introduz os grupos de disponibilidade Always On do SQL Server nas Máquinas Virtuais do Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 8bbd56499c9b62248662fc5e8df0d5b3e1b672d4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504160"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Grupos de disponibilidade Always On no SQL Server em VMs do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo introduz os grupos de disponibilidade Always On para SQL Server nas VMs (Máquinas Virtuais) do Azure. 

## <a name="overview"></a>Visão geral

Os grupos de disponibilidade Always On em Máquinas Virtuais do Azure são semelhantes aos [Grupos de disponibilidade Always On locais](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). No entanto, como as máquinas virtuais são hospedadas no Azure, há algumas considerações adicionais também, como redundância de VM e roteamento de tráfego na rede do Azure. 

O seguinte diagrama ilustra um grupo de disponibilidade para SQL Server em VMs do Azure:

![Grupo de disponibilidade](./media/availability-group-overview/00-EndstateSampleNoELB.png)


## <a name="vm-redundancy"></a>Redundância de VM 

Para aumentar a redundância e a alta disponibilidade, as VMs do SQL Server devem estar no mesmo [conjunto de disponibilidade](../../../virtual-machines/availability-set-overview.md) ou em [zonas de disponibilidade](../../../availability-zones/az-overview.md) diferentes.

Colocar um conjunto de VMs no mesmo conjunto de disponibilidade oferece proteção contra interrupções em um datacenter causadas por uma falha no equipamento (as VMs em um Conjunto de Disponibilidade não compartilham recursos) ou contra atualizações (as VMs em um Conjunto de Disponibilidade não são atualizadas ao mesmo tempo). As Zonas de Disponibilidade oferecem proteção contra a falha de um datacenter inteiro, com cada zona representando um conjunto de datacenters dentro de uma região.  Com a garantia de que os recursos serão colocados em diferentes Zonas de Disponibilidade, nenhuma interrupção no nível do datacenter poderá colocar todas as suas VMs offline.

Ao criar VMs do Azure, você precisa escolher entre configurar Conjuntos de Disponibilidade vs Zonas de Disponibilidade.  Uma VM do Azure não pode participar de ambos.


## <a name="connectivity"></a>Conectividade 

Em uma implantação local tradicional, os clientes se conectam ao ouvinte do grupo de disponibilidade usando o VNN (nome da rede virtual) e o ouvinte roteia o tráfego para a réplica do SQL Server apropriada no grupo de disponibilidade. No entanto, há um requisito extra para rotear o tráfego na rede do Azure. 

Com o SQL Server em VMs do Azure, configure um [balanceador de carga](availability-group-vnn-azure-load-balancer-configure.md) para rotear o tráfego para o ouvinte do grupo de disponibilidade ou, se estiver no SQL Server 2019 CU8 e posterior, você poderá configurar um [ouvinte DNN (nome de rede distribuída)](availability-group-distributed-network-name-dnn-listener-configure.md) para substituir o ouvinte do grupo de disponibilidade de VNN tradicional. 

Para obter mais detalhes sobre as opções de conectividade de cluster, confira [Rotear conexões HADR para o SQL Server em VMs do Azure](hadr-cluster-best-practices.md#connectivity). 

### <a name="vnn-listener"></a>Ouvinte VNN 

Use um [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) para rotear o tráfego do cliente para o ouvinte VNN (nome da rede virtual) do grupo de disponibilidade tradicional na rede do Azure. 

O balanceador de carga contém o endereço IP do ouvinte VNN. Se você tiver mais de um grupo de disponibilidade, cada grupo exigirá um ouvinte VNN. Um balanceador de carga pode dar suporte a vários ouvintes.

Para começar, confira [configurar um balanceador de carga](availability-group-vnn-azure-load-balancer-configure.md). 

### <a name="dnn-listener"></a>Ouvinte DNN

O SQL Server 2019 CU8 apresenta suporte para o ouvinte DNN (nome de rede distribuída). O ouvinte DNN substitui o ouvinte do grupo de disponibilidade tradicional, fazendo com que não seja necessário que um Azure Load Balancer roteie o tráfego na rede do Azure. 

O ouvinte DNN é a solução de conectividade de HADR recomendada no Azure, pois simplifica a implantação, reduz a manutenção e o custo e reduz o tempo de failover em caso de falha. 

Use o ouvinte DNN para substituir um ouvinte VNN existente ou, como alternativa, use-o em conjunto com um ouvinte VNN existente para que o seu grupo de disponibilidade tenha dois pontos de conexão distintos: um que usa o nome do ouvinte VNN (e a porta, se ela não for padrão) e outro que usa o nome e a porta do ouvinte DNN. Isso pode ser útil para clientes que desejam evitar a latência de failover do balanceador de carga, mas ainda aproveitar os recursos do SQL Server que dependem do ouvinte VNN, como grupos de disponibilidade distribuídos, service broker ou fluxo de arquivos. Para saber mais, confira [Interoperabilidade de recursos do ouvinte DNN e do SQL Server](availability-group-dnn-interoperability.md)

Para começar, confira [configurar um ouvinte DNN](availability-group-distributed-network-name-dnn-listener-configure.md).


## <a name="deployment"></a>Implantação 

Há várias opções para implantar um grupo de disponibilidade no SQL Server em VMs do Azure, algumas com mais automação do que outras. 

A seguinte tabela fornece uma comparação das opções disponíveis:

| | Portal do Azure | CLI do Azure/PowerShell | Modelos de Início Rápido | Manual |
|---------|---------|---------|---------|---------|
|**Versão do SQL Server** |2016 + |2016 +|2016 +|2012 +|
|**Edição do SQL Server** |Enterprise |Enterprise |Enterprise |Enterprise, Standard|
|**Versão do Windows Server**| 2016 + | 2016 + | 2016 + | Tudo|
|**Cria o cluster para você**|Sim|Sim | Sim |Não|
|**Cria o grupo de disponibilidade para você** |Sim |Não|Não|Não|
|**Cria o ouvinte e o balanceador de carga de modo independente** |Não|Não|Não|Sim|
|**É possível criar um ouvinte DNN usando este método?**|Não|Não|Não|Sim|
|**Configuração de quorum do WSFC**|Testemunha da nuvem|Testemunha da nuvem|Testemunha da nuvem|Tudo|
|**DR com várias regiões** |Não|Não|Não|Sim|
|**Suporte a várias sub-redes** |Sim|Sim|Sim|Sim|
|**Suporte para um AD existente**|Sim|Sim|Sim|Sim|
|**DR com várias zonas na mesma região**|Sim|Sim|Sim|Sim|
|**AG distribuído sem AD**|Não|Não|Não|Sim|
|**AG distribuído sem cluster** |Não|Não|Não|Sim|

Para saber mais, confira [portal do Azure](availability-group-azure-portal-configure.md), [CLI do Azure/PowerShell](./availability-group-az-commandline-configure.md), [Modelos de Início Rápido](availability-group-quickstart-template-configure.md) e [Manual](availability-group-manually-configure-prerequisites-tutorial.md).

## <a name="considerations"></a>Considerações 

Em um cluster de failover de convidado de VM IaaS do Azure, recomendamos uma única NIC por servidor (nó de cluster) e uma única sub-rede. A Rede do Azure tem redundância física, o que torna desnecessários os adaptadores de rede e as sub-redes adicionais em um cluster convidado de uma VM de IaaS do Azure. Embora o relatório de validação de cluster emita um aviso de que os nós só são acessíveis em uma única rede, esse aviso pode ser ignorado com segurança em clusters de failover de convidado de uma VM de IaaS do Azure. 

## <a name="next-steps"></a>Próximas etapas

Examine as [práticas recomendadas do HADR](hadr-cluster-best-practices.md) e, em seguida, comece a implantar o seu grupo de disponibilidade usando o [portal do Azure](availability-group-azure-portal-configure.md), a [CLI do Azure/PowerShell](./availability-group-az-commandline-configure.md), os [Modelos de Início Rápido](availability-group-quickstart-template-configure.md) ou [manualmente](availability-group-manually-configure-prerequisites-tutorial.md).

Como alternativa, você pode implantar um [grupo de disponibilidade sem cluster](availability-group-clusterless-workgroup-configure.md) ou um grupo de disponibilidade em [várias regiões](availability-group-manually-configure-multiple-regions.md).
