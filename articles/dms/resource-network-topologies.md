---
title: Topologias de rede para migrações de Instância Gerenciada de SQL
titleSuffix: Azure Database Migration Service
description: Conheça as configurações de origem e destino das migrações do Azure SQL Instância Gerenciada usando o serviço de migração de banco de dados do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: reference
ms.date: 01/08/2020
ms.openlocfilehash: 0799e8c76bc5d3969943d766aa83de40659a236a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101093345"
---
# <a name="network-topologies-for-azure-sql-managed-instance-migrations-using-azure-database-migration-service"></a>Topologias de rede para Azure SQL Instância Gerenciada migrações usando o serviço de migração de banco de dados do Azure

Este artigo discute várias topologias de rede com as quais o serviço de migração de banco de dados do Azure pode trabalhar para fornecer uma experiência de migração abrangente dos SQL Servers para o Azure SQL Instância Gerenciada.

## <a name="azure-sql-managed-instance-configured-for-hybrid-workloads"></a>Instância Gerenciada do SQL do Azure configurado para cargas de trabalho híbridas 

Use essa topologia se o Azure SQL Instância Gerenciada estiver conectado à sua rede local. Essa abordagem fornece o roteamento de rede mais simplificado e gera a taxa máxima de transferência de dados durante a migração.

![Topologia de rede para cargas de trabalho híbridas](media/resource-network-topologies/hybrid-workloads.png)

**Requisitos**

- Nesse cenário, o SQL Instância Gerenciada e a instância do serviço de migração de banco de dados do Azure são criados na mesma Rede Virtual do Microsoft Azure, mas usam sub-redes diferentes.  
- A rede virtual usada neste cenário também está conectada à rede local usando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

## <a name="sql-managed-instance-isolated-from-the-on-premises-network"></a>SQL Instância Gerenciada isolado da rede local

Use essa topologia de rede se o ambiente exigir um ou mais dos seguintes cenários:

- O SQL Instância Gerenciada é isolado da conectividade local, mas sua instância do serviço de migração de banco de dados do Azure está conectada à rede local.
- Se as políticas do controle de acesso baseado em função (RBAC do Azure) do Azure estiverem em vigor e você precisar limitar os usuários a acessarem a mesma assinatura que está hospedando o SQL Instância Gerenciada.
- As redes virtuais usadas para o SQL Instância Gerenciada e o serviço de migração de banco de dados do Azure estão em assinaturas diferentes.

![Topologia de rede da Instância Gerenciada isolada da rede local](media/resource-network-topologies/mi-isolated-workload.png)

**Requisitos**

- A rede virtual que o serviço de migração de banco de dados do Azure usa para esse cenário também deve estar conectada à rede local usando o ( https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- Configure o [emparelhamento de rede VNet](../virtual-network/virtual-network-peering-overview.md) entre a rede virtual usada para o SQL instância gerenciada e o serviço de migração de banco de dados do Azure.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migrações da nuvem para a nuvem: rede virtual compartilhada

Use essa topologia se o SQL Server de origem estiver hospedado em uma VM do Azure e compartilhar a mesma rede virtual com o SQL Instância Gerenciada e o serviço de migração de banco de dados do Azure.

![Topologia de rede para migrações de nuvem para nuvem com uma VNet compartilhada](media/resource-network-topologies/cloud-to-cloud.png)

**Requisitos**

- Nenhum requisito adicional.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Migrações de nuvem para nuvem: rede virtual isolada

Use essa topologia de rede se o ambiente exigir um ou mais dos seguintes cenários:

- O SQL Instância Gerenciada é provisionado em uma rede virtual isolada.
- Se as políticas do controle de acesso baseado em função (RBAC do Azure) do Azure estiverem em vigor e você precisar limitar os usuários a acessarem a mesma assinatura que está hospedando o SQL Instância Gerenciada.
- As redes virtuais usadas para o SQL Instância Gerenciada e o serviço de migração de banco de dados do Azure estão em assinaturas diferentes.

![Topologia de rede para migrações de nuvem para nuvem com uma VNet isolada](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Requisitos**

- Configure o [emparelhamento de rede VNet](../virtual-network/virtual-network-peering-overview.md) entre a rede virtual usada para o SQL instância gerenciada e o serviço de migração de banco de dados do Azure.

## <a name="inbound-security-rules"></a>Regras de segurança de entrada

| **NOME**   | **Porto** | **PROTOCOLO** | **ORIGINAL** | **DESTINO** | **AÇÃO** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Qualquer      | Qualquer          | DMS SUBNET | Qualquer             | Allow      |

## <a name="outbound-security-rules"></a>Regras de segurança de saída

| **NOME**                  | **Porto**                                              | **PROTOCOLO** | **ORIGINAL** | **DESTINO**           | **AÇÃO** | **Motivo para a regra**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ServiceBus                | 443, ServiceTag: ServiceBus                           | TCP          | Qualquer        | Qualquer                       | Allow      | Comunicação do plano de gerenciamento por meio do barramento de serviço. <br/>(Se o emparelhamento da Microsoft é habilitado, você talvez não precise essa regra.)                                                             |
| Armazenamento                   | 443, ServiceTag: armazenamento                              | TCP          | Qualquer        | Qualquer                       | Allow      | Plano de gerenciamento usando o armazenamento de BLOBs do Azure. <br/>(Se o emparelhamento da Microsoft é habilitado, você talvez não precise essa regra.)                                                             |
| Diagnósticos               | 443, ServiceTag: AzureMonitor                         | TCP          | Qualquer        | Qualquer                       | Allow      | DMS usa a regra para coletar informações de diagnóstico para fins de solução de problemas. <br/>(Se o emparelhamento da Microsoft é habilitado, você talvez não precise essa regra.)                                                  |
| Servidor de origem SQL         | 1433 (ou porta TCP IP que o SQL Server está escutando) | TCP          | Qualquer        | Espaço de endereço local | Allow      | Conectividade de origem do SQL Server do DMS <br/>(Se você tiver conectividade site a site, talvez não seja necessário usar essa regra.)                                                                                       |
| Instância nomeada do SQL Server | 1434                                                  | UDP          | Qualquer        | Espaço de endereço local | Allow      | Conectividade de origem de instância nomeada do SQL Server do DMS <br/>(Se você tiver conectividade site a site, talvez não seja necessário usar essa regra.)                                                                        |
| Compartilhamento SMB                 | 445 (se o cenário neeeds)                             | TCP          | Qualquer        | Espaço de endereço local | Allow      | Compartilhamento de rede SMB para que o DMS armazene arquivos de backup de banco de dados para migrações para o MI do Banco de Dados SQL do Azure e os SQL Servers na VM do Azure <br/>(Se você tiver conectividade site a site, talvez não seja necessária essa regra). |
| DMS_subnet                | Qualquer                                                   | Qualquer          | Qualquer        | DMS_Subnet                | Allow      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Veja também

- [Migrar SQL Server para o SQL Instância Gerenciada](./tutorial-sql-server-to-managed-instance.md)
- [Visão geral dos pré-requisitos para usar o serviço de migração de banco de dados do Azure](./pre-reqs.md)
- [Criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do serviço de migração de banco de dados do Azure, consulte o artigo [o que é o Azure Database Migration Service?](dms-overview.md).
- Para obter informações atuais sobre a disponibilidade regional do serviço de migração de banco de dados do Azure, consulte a página [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) .