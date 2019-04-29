---
title: Topologias de rede para migrações da Instância Gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure | Microsoft Docs
description: Saiba mais sobre as configurações de origem e destino do Serviço de Migração de Banco de Dados.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: d12d6b1274a756bfb13761ab999a1539bcee3657
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61097767"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Topologias de rede para migrações da Instância Gerenciada do BD SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure
Este artigo discute as diversas topologias de rede com as quais o Serviço de Migração de Banco de Dados do Azure pode trabalhar para fornecer uma experiência de migração abrangente de SQL Servers locais para Instância Gerenciada do Banco de Dados SQL do Azure.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Instância Gerenciada do Banco de Dados SQL do Azure configurada para cargas de trabalho Híbridas 
Use essa topologia se a Instância Gerenciada do Banco de Dados SQL do Azure está conectada à rede local. Essa abordagem fornece o roteamento de rede mais simplificado e gera a taxa máxima de transferência de dados durante a migração.

![Topologia de rede para cargas de trabalho híbridas](media/resource-network-topologies/hybrid-workloads.png)

**Requisitos**
- Neste cenário, a Instância Gerenciada do Banco de Dados SQL do Azure e a instância do Serviço de Migração de Banco de Dados do Azure são criadas na mesma VNET do Azure, mas usam sub-redes diferentes.  
- A VNET usada neste cenário também é conectada à rede local com o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Instância Gerenciada do Banco de Dados SQL do Azure isolada da rede local
Use essa topologia de rede se o ambiente exigir um ou mais dos seguintes cenários:
- A Instância Gerenciada do Banco de Dados SQL do Azure está isolada da conectividade local, mas a instância do Serviço de Migração de Banco de Dados do Azure está conectada à rede local.
- Se políticas de RBAC (controle de acesso baseado em função) estiverem em vigor e você precisar limitar o acesso dos usuários à mesma assinatura que hospeda a Instância Gerenciada do Banco de Dados SQL do Azure.
- As VNETs usadas para a Instância Gerenciada do Banco de Dados SQL do Azure e o Serviço de Migração de Banco de Dados do Azure estão em assinaturas diferentes.

![Topologia de rede da Instância Gerenciada isolada da rede local](media/resource-network-topologies/mi-isolated-workload.png)

**Requisitos**
- A VNET usada pelo Serviço de Migração de Banco de Dados do Azure para este cenário também deve estar conectada à rede local por meio do uso de (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Configure um [emparelhamento de rede da VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre a VNET usada para a Instância Gerenciada do Banco de Dados SQL do Azure e o Serviço de Migração de Banco de Dados do Azure.


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Migrações de nuvem para nuvem: VNET compartilhada

Use essa topologia se o SQL Server de origem estiver hospedado em uma VM do Azure e compartilhar a mesma VNET com a Instância Gerenciada do Banco de Dados SQL do Azure e o Serviço de Migração de Banco de Dados.

![Topologia de rede para migrações de nuvem para nuvem com uma vnet compartilhada](media/resource-network-topologies/cloud-to-cloud.png)

**Requisitos**
- Nenhum requisito adicional.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Migrações de nuvem para nuvem: VNET isolada

Use essa topologia de rede se o ambiente exigir um ou mais dos seguintes cenários:
- A Instância Gerenciada do Banco de Dados SQL do Azure é provisionada em uma VNET isolada.
- Se políticas de RBAC (controle de acesso baseado em função) estiverem em vigor e você precisar limitar o acesso dos usuários à mesma assinatura que hospeda a Instância Gerenciada do Banco de Dados SQL do Azure.
- As VNETs usadas para a Instância Gerenciada do Banco de Dados SQL do Azure e o Serviço de Migração de Banco de Dados do Azure estão em assinaturas diferentes.

![Topologia de rede para migrações de nuvem para nuvem com uma vnet isolada](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Requisitos**
- Configure um [emparelhamento de rede da VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre a VNET usada para a Instância Gerenciada do Banco de Dados SQL do Azure e o Serviço de Migração de Banco de Dados do Azure.

## <a name="inbound-security-rules"></a>Regras de segurança de entrada

| **NOME**   | **PORTA** | **PROTOCOLO** | **FONTE** | **DESTINO** | **AÇÃO** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Qualquer      | Qualquer          | DMS SUBNET | Qualquer             | PERMITIR      |

## <a name="outbound-security-rules"></a>Regras de segurança de saída

| **NOME**                  | **PORTA**                                              | **PROTOCOLO** | **FONTE** | **DESTINO**           | **AÇÃO** | **Motivo para a regra**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| gerenciamento                | 443,9354                                              | TCP          | Qualquer        | Qualquer                       | PERMITIR      | Comunicação do plano de gerenciamento por meio do barramento de serviço e o armazenamento de BLOBs do Azure. <br/>(Se o emparelhamento da Microsoft é habilitado, você talvez não precise essa regra.)                                                             |
| Diagnósticos               | 12000                                                 | TCP          | Qualquer        | Qualquer                       | PERMITIR      | DMS usa a regra para coletar informações de diagnóstico para fins de solução de problemas.                                                                                                                      |
| Servidor de origem SQL         | 1433 (ou porta TCP IP que o SQL Server está escutando) | TCP          | Qualquer        | Espaço de endereço local | PERMITIR      | Conectividade de origem do SQL Server do DMS <br/>(Se você tiver conectividade site a site, talvez não seja necessário usar essa regra.)                                                                                       |
| Instância nomeada do SQL Server | 1434                                                  | UDP          | Qualquer        | Espaço de endereço local | PERMITIR      | Conectividade de origem de instância nomeada do SQL Server do DMS <br/>(Se você tiver conectividade site a site, talvez não seja necessário usar essa regra.)                                                                        |
| Compartilhamento SMB                 | 445                                                   | TCP          | Qualquer        | Espaço de endereço local | PERMITIR      | Compartilhamento de rede SMB para que o DMS armazene arquivos de backup de banco de dados para migrações para o MI do Banco de Dados SQL do Azure e os SQL Servers na VM do Azure <br/>(Se você tiver conectividade site a site, talvez não seja necessária essa regra). |
| DMS_subnet                | Qualquer                                                   | Qualquer          | Qualquer        | DMS_Subnet                | PERMITIR      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Veja também
- [Migrar o SQL Server para a Instância Gerenciada do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Visão geral de pré-requisitos para o uso do Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Criar uma rede virtual usando o portal do Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Próximas etapas
- Para obter uma visão geral do Serviço de Migração de Banco de Dados do Azure, consulte o artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](dms-overview.md).
- Para obter informações atuais sobre a disponibilidade regional do Serviço de Migração de Banco de Dados do Azure, consulte a página [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).