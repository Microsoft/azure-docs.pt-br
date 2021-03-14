---
title: Serviços do Azure compatíveis com as zonas de disponibilidade
description: Para criar aplicativos altamente disponíveis e resilientes no Azure, as Zonas de Disponibilidade oferecem locais fisicamente separados que você pode usar para executar seus recursos.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: fee000009be90a083406725fe7f1e8c4382af3e9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701443"
---
# <a name="azure-services-that-support-availability-zones"></a>Serviços do Azure compatíveis com as zonas de disponibilidade

Microsoft Azure infraestrutura global é projetada e construída em cada camada para fornecer os mais altos níveis de redundância e resiliência aos seus clientes. A infraestrutura do Azure é composta por geografias, regiões e Zonas de Disponibilidade, que limitam o raio de uma falha e, portanto, limitam o impacto potencial aos dados e aplicativos do cliente. O Zonas de Disponibilidade do Azure Construct foi desenvolvido para fornecer uma solução de software e rede para proteger contra falhas de datacenter e fornecer alta disponibilidade (HA) maior para nossos clientes.

As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais data centers com energia, resfriamento e rede independentes. A separação física de Zonas de Disponibilidade dentro de uma região limita o impacto a aplicativos e dados de falhas de zona, como inundação em larga escala, grandes Storms e supertempestades e outros eventos que podem interromper o acesso ao site, a passagem segura, o tempo de atividade de utilitários estendidos e a disponibilidade de recursos. Zonas de Disponibilidade e seus data centers associados são projetados de modo que, se uma zona for comprometida, os serviços, a capacidade e a disponibilidade serão compatíveis com os outros Zonas de Disponibilidade na região.

Todos os serviços de gerenciamento do Azure são arquitetados para serem resilientes de falhas no nível de região. No espectro de falhas, uma ou mais falhas de zona de disponibilidade em uma região têm um raio de falha menor em comparação a uma falha de região inteira. O Azure pode se recuperar de uma falha no nível de zona dos serviços de gerenciamento dentro de uma região. O Azure realiza uma manutenção crítica em uma zona por vez dentro de uma região, para evitar falhas que afetem os recursos do cliente implantados em Zonas de Disponibilidade dentro de uma região.


![exibição conceitual de uma região do Azure com 3 zonas](./media/az-region/azure-region-availability-zones.png)


Os serviços do Azure que dão suporte a Zonas de Disponibilidade se enquadram em três categorias: serviços **zonas**, **com redundância de zona** e **não regionais** . As cargas de trabalho do cliente podem ser categorizadas para utilizar qualquer um desses cenários de arquitetura para atender à durabilidade e ao desempenho do aplicativo.

- **Serviços zonais** – um recurso pode ser implantado em uma zona de disponibilidade específica, selecionada automaticamente, para obter requisitos de desempenho ou latência mais rigorosos.  A resiliência é autoarquitetada ao replicar aplicativos e dados para uma ou mais zonas dentro da região.  Os recursos podem ser fixados em uma zona específica. Por exemplo, máquinas virtuais, discos gerenciados ou endereços IP padrão podem ser fixados em uma zona específica, o que permite maior resiliência por ter uma ou mais instâncias de recursos espalhadas entre zonas.

- **Serviços com redundância de zona** – a plataforma Azure replica o recurso e os dados entre zonas.  A Microsoft gerencia a entrega de alta disponibilidade, já que o Azure replica e distribui instâncias na região automaticamente.  ZRS, por exemplo, replica os dados entre três zonas para que uma falha de zona não afete a alta disponibilidade dos dados. 

- **Serviços não regionais** – os serviços estão sempre disponíveis em geografias do Azure e são resilientes a interrupções em toda a zona, bem como interrupções em toda a região. 


Para obter uma continuidade de negócios abrangente no Azure, compile a arquitetura do aplicativo usando a combinação de Zonas de Disponibilidade com pares de regiões do Azure. É possível replicar os aplicativos e dados de maneira síncrona usando Zonas de Disponibilidade em uma região do Azure para alta disponibilidade e replicação assíncrona em regiões do Azure para proteção de recuperação de desastre. Para saber mais, leia [criando soluções para alta disponibilidade usando o zonas de disponibilidade](/azure/architecture/high-availability/building-solutions-for-high-availability). 

## <a name="azure-services-supporting-availability-zones"></a>Serviços do Azure com suporte a Zonas de Disponibilidade

 - As máquinas virtuais de geração mais antigas não estão listadas. Para obter mais informações, consulte [Gerações anteriores de tamanhos de máquinas virtuais](../virtual-machines/sizes-previous-gen.md).
 - Conforme mencionado nas [regiões e zonas de disponibilidade no Azure](az-overview.md), alguns serviços são não regionais. Esses serviços não têm dependência em uma região específica do Azure, pois são resilientes a interrupções em toda a zona, bem como interrupções em toda a região.  A lista de serviços não regionais pode ser encontrada em [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/).


## <a name="azure-regions-with-availability-zones"></a>Regiões do Azure com Zonas de Disponibilidade


| Américas           | Europa               | África              | Pacífico Asiático   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Canadá Central     | França Central       | Norte da África do Sul * | Japan East     |
| Centro dos EUA         | Centro-Oeste da Alemanha |                     | Sudeste Asiático |
| Leste dos EUA            | Norte da Europa         |                     | Leste da Austrália |
| Leste dos EUA 2          | Sul do Reino Unido             |                     |                |
| EUA Central do Sul | Europa Ocidental          |                     |                |
| Gov. dos EUA – Virgínia    |                      |                     |                |
| Oeste dos EUA 2        |                      |                     |                |


\* Para saber mais sobre o suporte a Zonas de Disponibilidade e serviços disponíveis nessas regiões, entre em contato com seu representante de vendas ou cliente da Microsoft. Para as próximas regiões que oferecerão suporte a Zonas de Disponibilidade, confira [geografias do Azure](https://azure.microsoft.com/en-us/global-infrastructure/geographies/).


## <a name="azure-services-supporting-availability-zones"></a>Serviços do Azure com suporte a Zonas de Disponibilidade

- As máquinas virtuais de geração mais antigas não estão listadas abaixo. Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../virtual-machines/sizes-previous-gen.md).

- Alguns serviços não são regionais, consulte [regiões e zonas de disponibilidade no Azure](az-overview.md) para obter mais informações. Esses serviços não têm dependência em uma região específica do Azure, tornando-os resilientes a interrupções de toda a zona e interrupções em toda a região.  A lista de serviços não regionais pode ser encontrada em [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/).


### <a name="zone-resilient-services"></a>Serviços resistentes à zona 

: globe_with_meridians: serviços não regionais – os serviços estão sempre disponíveis em geografias do Azure e são resilientes a interrupções em toda a zona, bem como interrupções em toda a região.

: large_blue_diamond: resiliente às interrupções de toda a zona 

**Serviços fundamentais**

|     Produtos                                                    | Resiliência             |
|-----------------------------------------------------------------|:----------------------------:|
|     Conta de armazenamento                                           | : large_blue_diamond:  |
|     Gateway de aplicativo (v2)                                  | : large_blue_diamond:  |
|     Serviço de Backup do Azure                                                | : large_blue_diamond:  |
|     Azure Cosmos DB                                           | : large_blue_diamond:  |
|     Azure Data Lake Storage Gen 2                             | : large_blue_diamond:  |
|     Rota expressa do Azure                                       | : large_blue_diamond:  |
|     IP público do Azure                                           | : large_blue_diamond:  |
|     Banco de dados SQL do Azure (Uso Geral camada)                 | : large_blue_diamond:  |
|     Banco de dados SQL do Azure (Premium & camada de Comercialmente Crítico)     | : large_blue_diamond:  |
|     Armazenamento em Disco                                                | : large_blue_diamond:  |
|     Hubs de Eventos                                                  | : large_blue_diamond:  |
|     Key Vault                                                   | : large_blue_diamond:  |
|     Load Balancer                                               | : large_blue_diamond:  |
|     Barramento de Serviço                                                 | : large_blue_diamond:  |
|     Service Fabric                                            | : large_blue_diamond:  |
|     Armazenamento: camadas de armazenamento de BLOBs quentes/frias                      | : large_blue_diamond:  |
|     Armazenamento: Managed Disks                                    | : large_blue_diamond:  |
|     Conjuntos de dimensionamento de máquinas virtuais                               | : large_blue_diamond:  |
|     Máquinas virtuais                                          | : large_blue_diamond:  |
|     Máquinas virtuais: Av2-Series                              | : large_blue_diamond:  |
|     Máquinas virtuais: Bs-Series                               | : large_blue_diamond:  |
|     Máquinas virtuais: DSv2-Series                             | : large_blue_diamond:  |
|     Máquinas virtuais: DSv3-Series                             | : large_blue_diamond:  |
|     Máquinas virtuais: Dv2-Series                              | : large_blue_diamond:  |
|     Máquinas virtuais: Dv3-Series                              | : large_blue_diamond:  |
|     Máquinas virtuais: ESv3-Series                             | : large_blue_diamond:  |
|     Máquinas virtuais: Ev3-Series                              | : large_blue_diamond:  |
|     Máquinas virtuais: série F                                | : large_blue_diamond:  |
|     Máquinas virtuais: FS-Series                               | : large_blue_diamond:  |
|     Rede virtual                                           | : large_blue_diamond:  |
|     Gateway de VPN                                                 | : large_blue_diamond:  |


**Serviços principais**

| Produtos                                        | Resiliência |
|-------------------------------------------------|:------------:|
| Ambientes de Serviço de Aplicativo                        |      : large_blue_diamond:  |
| Azure Active Directory Domain Services          |      : large_blue_diamond:  |
| Azure Bastion                                   |      : large_blue_diamond:  |
| Cache Redis do Azure                           |      : large_blue_diamond:  |
| Serviços Cognitivos do Azure: Análise de Texto        |      : large_blue_diamond:  |
| Azure Data Explorer                             |      : large_blue_diamond:  |
| Banco de dados do Azure para MySQL – servidor flexível      |      : large_blue_diamond:  |
| Banco de dados do Azure para PostgreSQL – servidor flexível |      : large_blue_diamond:  |
| Proteção contra DDoS do Azure                           |      : large_blue_diamond:  |
| Firewall do Azure                                  |      : large_blue_diamond:  |
| Gerenciador de Firewall do Azure                          |      : large_blue_diamond:  |
| AKS (Serviço de Kubernetes do Azure)                  |      : large_blue_diamond:  |
| Link Privado do Azure                              |      : large_blue_diamond:  |
| Red Hat OpenShift no Azure                         |      : large_blue_diamond:  |
| Azure Site Recovery                             |      : large_blue_diamond:  |
| SQL do Azure: máquina virtual                      |      : large_blue_diamond:  |
| Azure Search                                    |      : large_blue_diamond:  |
| Firewall do aplicativo Web do Azure                  |      : large_blue_diamond:  |
| Serviços cognitivas: Análise de Texto              |      : large_blue_diamond:  |
| Registro de Contêiner                              |      : large_blue_diamond:  |
| Grade de Eventos                                      |      : large_blue_diamond:  |
| Observador de Rede                                 |      : large_blue_diamond:  |
| Observador de rede: Análise de Tráfego              |      : large_blue_diamond:  |
| Power BI Embedded                               |      : large_blue_diamond:  |
| Armazenamento de blob Premium                            |      : large_blue_diamond:  |
| Armazenamento: arquivos premium do Azure                    |      : large_blue_diamond:  |
| Máquinas virtuais: host dedicado do Azure          |      : large_blue_diamond:  |
| Máquinas virtuais: Ddsv4-Series                  |      : large_blue_diamond:  |
| Máquinas virtuais: Ddv4-Series                   |      : large_blue_diamond:  |
| Máquinas virtuais: Dsv4-Series                   |      : large_blue_diamond:  |
| Máquinas virtuais: Dv4-Series                    |      : large_blue_diamond:  |
| Máquinas virtuais: Edsv4-Series                  |      : large_blue_diamond:  |
| Máquinas virtuais: Edv4-Series                   |      : large_blue_diamond:  |
| Máquinas virtuais: Esv4-Series                   |      : large_blue_diamond:  |
| Máquinas virtuais: Ev4-Series                    |      : large_blue_diamond:  |
| Máquinas virtuais: Fsv2-Series                   |      : large_blue_diamond:  |
| Máquinas virtuais: série M                      |      : large_blue_diamond:  |
| WAN Virtual                                     |      : large_blue_diamond:  |
| WAN virtual: ExpressRoute                       |      : large_blue_diamond:  |
| WAN virtual: gateway de VPN ponto a site          |      : large_blue_diamond:  |
| WAN virtual: gateway de VPN site a site           |      : large_blue_diamond:  |


**Não regional**

|     Produtos                                  |     Resiliência    |
|-----------------------------------------------|:-------------------:|
|     DNS do Azure                                 |     : globe_with_meridians:             |
|     Azure Active Directory                  |     : globe_with_meridians:             |
|     Proteção Avançada contra Ameaças do Azure          |     : globe_with_meridians:             |
|     Assistente do Azure                             |     : globe_with_meridians:             |
|     Azure Blueprints                          |     : globe_with_meridians:             |
|     Serviços de Bot do Azure                        |     : globe_with_meridians:             |
|     Azure defender para IoT                  |     : globe_with_meridians:             |
|     Porta da frente do Azure                           |     : globe_with_meridians:             |
|     Proteção de informações do Azure            |     : globe_with_meridians:             |
|     Lighthouse do Azure                        |     : globe_with_meridians:             |
|     Aplicativos gerenciados do Azure              |     : globe_with_meridians:             |
|     Mapas do Azure                                |     : globe_with_meridians:             |
|     Azure Policy                              |     : globe_with_meridians:             |
|     Grafo de recursos do Azure                    |     : globe_with_meridians:             |
|     Azure Sentinel                            |     : globe_with_meridians:             |
|     Azure Stack                               |     : globe_with_meridians:             |
|     Borda do Azure Stack                        |     : globe_with_meridians:             |
|     Cloud Shell                               |     : globe_with_meridians:             |
|     Rede de Distribuição de Conteúdo                  |     : globe_with_meridians:             |
|     Gerenciamento de Custos                           |     : globe_with_meridians:             |
|     Sistema de Proteção de Dados do Cliente para Microsoft Azure    |     : globe_with_meridians:             |
|     Intune                                    |     : globe_with_meridians:             |
|     Serviço de emparelhamento Microsoft Azure         |     : globe_with_meridians:             |
|     portal do Microsoft Azure                  |     : globe_with_meridians:             |
|     Microsoft Cloud App Security              |     : globe_with_meridians:             |
|     Microsoft Graph                           |     : globe_with_meridians:             |
|     Central de segurança                         |     : globe_with_meridians:             |
|     Gerenciador de tráfego                         |     : globe_with_meridians:             |


## <a name="pricing-for-vms-in-availability-zones"></a>Preços para VMs no Zonas de Disponibilidade

Não há custo adicional para máquinas virtuais implantadas em uma Zona de Disponibilidade. Para obter mais informações, consulte a [página de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Introdução às Zonas de Disponibilidade

- [Criar uma máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
- [Adicionar um Disco Gerenciado usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Criar um conjunto de dimensionamento de máquinas virtuais com redundância de zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Balancear carga de VMs entre zonas usando um Load Balancer Standard com um front-end com redundância de zona](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Balancear carga de VMs em uma zona usando um Load Balancer Standard com um front-end zonal](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Armazenamento com redundância de zona](../storage/common/storage-redundancy.md)
- [Camada de uso geral do banco de dados SQL](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Recuperação de desastre geográfico dos Hubs de Eventos](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Recuperação de desastre geográfico do Barramento de Serviço](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Criar um gateway de rede virtual com redundância de zona](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Adicionar região com redundância de zona para Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Introdução o cache do Azure para Redis Zonas de Disponibilidade](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Criar uma instância do Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Criar um cluster AKS (serviço de kubernetes do Azure) que usa Zonas de Disponibilidade](../aks/availability-zones.md)


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Regiões e zonas de disponibilidade no Azure](az-overview.md)
