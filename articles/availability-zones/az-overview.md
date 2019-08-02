---
title: O que são as Zonas de Disponibilidade do Azure? | Microsoft Docs
description: Para criar aplicativos altamente disponíveis e resilientes no Azure, as Zonas de Disponibilidade oferecem locais fisicamente separados que você pode usar para executar seus recursos.
services: ''
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/20/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 048a4dd9f35bd62886876f98bcbc5e6267cca6c8
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442228"
---
# <a name="what-are-availability-zones-in-azure"></a>O que são Zonas de Disponibilidade no Azure?
Zonas de Disponibilidade é uma oferta de alta disponibilidade que protege os aplicativos e dados contra falhas do datacenter. As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física das Zonas de Disponibilidade dentro de uma região protege os aplicativos e dados contra falhas do datacenter. Serviços com redundância de zona replicam os aplicativos e dados entre Zonas de Disponibilidade para proteger dos pontos únicos de falha. Com Zonas de Disponibilidade, o Azure oferece o melhor SLA de tempo de atividade da VM de 99,99% do setor. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explica a disponibilidade garantida do Azure como um todo.

Uma Zona de Disponibilidade em uma região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se você criar três ou mais VMs em três zonas em uma região do Azure, as VMs serão efetivamente distribuídas em três domínios de falha e três domínios de atualização. A plataforma do Azure reconhece essa distribuição nos domínios de atualização para garantir que as VMs em diferentes zonas não sejam atualizadas ao mesmo tempo.

Compila alta disponibilidade na arquitetura do aplicativo, colocalizando os recursos de computação, armazenamento, rede e dados em uma zona e replicando em outras zonas. Os serviços do Azure que fornecem suporte a Zonas de Disponibilidade enquadram-se em duas categorias:

- **Serviços zonais** – você fixa o recurso em uma zona específica (por exemplo, máquinas virtuais, discos gerenciados, endereços IP) ou
- **Serviços com redundância de zona** – a plataforma replica automaticamente entre zonas (por exemplo, armazenamento com redundância de zona, Banco de Dados SQL).

Para obter uma continuidade de negócios abrangente no Azure, compile a arquitetura do aplicativo usando a combinação de Zonas de Disponibilidade com pares de regiões do Azure. É possível replicar os aplicativos e dados de maneira síncrona usando Zonas de Disponibilidade em uma região do Azure para alta disponibilidade e replicação assíncrona em regiões do Azure para proteção de recuperação de desastre.
 
![exibição conceitual de uma zona reduzindo-se a uma região](./media/az-overview/az-graphic-two.png)

## <a name="services-support-by-region"></a>Suporte a serviços por região

As combinações de serviços e regiões do Azure que oferecem suporte a Zonas de Disponibilidade são:


|                                 |Américas |              |           |           | Europa |              |          |              | Pacífico Asiático |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |EUA Central|East US|Leste dos EUA 2|Oeste dos EUA 2|Centro da França|Norte da Europa|Sul do Reino Unido|Oeste da Europa|Leste do Japão|Sudeste da Ásia|
| **Computação**                         |            |              |           |           |                |              |          |             |            |                |
| Máquinas Virtuais do Linux          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Máquinas Virtuais do Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Conjuntos de Dimensionamento de Máquinas Virtuais      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Armazenamento**   |            |              |           |           |                |              |          |             |            |                |
| Managed Disks                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Armazenamento com redundância de zona          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Rede**                     |            |              |           |           |                |              |          |             |            |                |
| Endereço IP padrão        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Standard Load Balancer     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Gateway de VPN   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;  | &#10003;     |   &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Gateway de ExpressRoute   | &#10003;   |  &#10003;   | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |   &#10003;  | &#10003;       |
| Application Gateway   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    | &#10003;       | &#10003;       |
| Firewall do Azure           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| **Bancos de dados**                     |            |              |           |           |                |              |          |             |            |                |
| Azure Data Explorer                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |
| Banco de Dados SQL                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |            | &#10003;       |
| Cache Redis do Azure           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; |  |       |     | &#10003; |     |            | &#10003;       |
| **Analytics**                       |            |              |           |           |                |              |          |             |            |                |
| Hubs de Eventos                      | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| **Integração**                     |            |              |           |           |                |              |          |             |            |                |
| Barramento de Serviço (somente Camada Premium) | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |



## <a name="services-resiliency"></a>Resiliência de serviços
Todos os serviços de gerenciamento do Azure são arquitetados para serem resilientes de falhas no nível de região. No espectro de falhas, uma ou mais falhas de zona de disponibilidade em uma região têm um raio de falha menor em comparação a uma falha de região inteira. O Azure pode se recuperar de uma falha no nível de zona dos serviços de gerenciamento dentro da região ou de outra região do Azure. O Azure realiza uma manutenção crítica em uma zona por vez dentro de uma região, para evitar falhas que afetem os recursos do cliente implantados em Zonas de Disponibilidade dentro de uma região.

## <a name="pricing"></a>Preços
Não há custo adicional para máquinas virtuais implantadas em uma Zona de Disponibilidade. O SLA de 99,99% de tempo de atividade da VM é oferecido quando duas ou mais VMs são implantadas em duas ou mais Zonas de Disponibilidade dentro de uma região do Azure. Haverá encargos de transferência de dados adicionais de VM para VM entre Zona de Disponibilidade. Para obter mais informações, consulte a página [Preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Introdução às Zonas de Disponibilidade
- [Criar uma máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
- [Adicionar um Disco Gerenciado usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Criar um conjunto de dimensionamento de máquinas virtuais com redundância de zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Balancear carga de VMs entre zonas usando um Load Balancer Standard com um front-end com redundância de zona](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Balancear carga de VMs em uma zona usando um Load Balancer Standard com um front-end zonal](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Armazenamento com redundância de zona](../storage/common/storage-redundancy-zrs.md)
- [Banco de Dados SQL](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Recuperação de desastre geográfico dos Hubs de Eventos](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Recuperação de desastre geográfico do Barramento de Serviço](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Criar um gateway de rede virtual com redundância de zona](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Adicionar região com redundância de zona para Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Introdução o cache do Azure para Redis Zonas de Disponibilidade](https://aka.ms/redis/az/getstarted)

## <a name="next-steps"></a>Próximas etapas
- [Modelos de início rápido](https://aka.ms/azqs)
