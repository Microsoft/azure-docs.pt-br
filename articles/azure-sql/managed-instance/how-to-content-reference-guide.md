---
title: Configurar & gerenciar a referência de conteúdo
titleSuffix: Azure SQL Managed Instance
description: Um guia de referência do conteúdo que ensina a configurar e gerenciar o Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 04/16/2019
ms.openlocfilehash: b7f2f060f32cf3bf92660a2e8b75a45b7ec76b82
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779757"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Referência de conteúdo do Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Neste artigo, você pode encontrar uma referência de conteúdo para vários guias, scripts e explicações que ajudam a gerenciar e configurar o Azure SQL Instância Gerenciada.

## <a name="load-data"></a>Carregar dados

- [Migrar para o Azure SQL instância gerenciada](migrate-to-instance-from-sql-server.md): Saiba mais sobre o processo de migração e as ferramentas recomendadas para migração para o SQL do Azure instância gerenciada.
- [Migrar certificado TDE para o Azure SQL instância gerenciada](tde-certificate-migrate.md): se o banco de dados do SQL Server estiver protegido com Transparent Data Encryption (TDE), você precisará migrar o certificado que o SQL instância gerenciada pode usar para descriptografar o backup que você deseja restaurar no Azure.
- [Importar um BD de um BACPAC](../database/database-import.md)
- [Exportar um BD de um BACPAC](../database/database-export.md)
- [Carregar dados com o BCP](../load-from-csv-with-bcp.md)
- [Carregar dados com Azure Data Factory](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>Configuração de rede

- [Determinar o tamanho da sub-rede](vnet-subnet-determine-size.md): como a sub-rede não pode ser redimensionada após a implantação do SQL instância gerenciada, você precisa calcular qual intervalo de endereços IP é necessário para o número e os tipos de instâncias gerenciadas que você planeja implantar na sub-rede. 
- [Criar uma nova VNet e sub-rede](virtual-network-subnet-create-arm-template.md): Configure a rede virtual e a sub-rede de acordo com os [requisitos de rede](connectivity-architecture-overview.md#network-requirements). 
- [Configurar uma VNet e uma sub-rede existentes](vnet-existing-add-subnet.md): Verifique os requisitos de rede e configure sua rede virtual e sub-rede existentes para implantar o SQL instância gerenciada. 
- [Configurar DNS personalizado](custom-dns-configure.md): Configure o DNS personalizado para conceder acesso a recursos externos a domínios personalizados do SQL instância gerenciada por meio de um servidor vinculado de perfis do db mail. 
- [Sincronizar configuração de rede](azure-app-sync-network-configuration.md): Atualize o plano de configuração de rede se você não puder estabelecer uma conexão depois de [integrar seu aplicativo a uma rede virtual do Azure](../../app-service/web-sites-integrate-with-vnet.md).
- [Localize o endereço IP do ponto de extremidade de gerenciamento](management-endpoint-find-ip-address.md): Determine o ponto de extremidade público que o SQL instância gerenciada está usando para fins de gerenciamento. 
- [Verificar a proteção de firewall interna](management-endpoint-verify-built-in-firewall.md): Verifique se o SQL instância gerenciada permite o tráfego somente nas portas necessárias e outras regras de firewall internas. 
- [Conectar aplicativos](connect-application-instance.md): Saiba mais sobre padrões diferentes para conectar os aplicativos ao SQL instância gerenciada.

## <a name="feature-configuration"></a>Configuração de recurso

- [Configurar a autenticação do Azure AD](../database/authentication-aad-configure.md)
- [Configurar o acesso condicional](../database/conditional-access-configure.md)
- [Autenticação multifator do Azure AD](../database/authentication-mfa-ssms-overview.md)
- [Configurar autenticação multifator](../database/authentication-mfa-ssms-configure.md)
- [Configurar uma política de retenção temporal](../database/temporal-tables-retention-policy.md)
- [Configurar TDE com BYOK](../database/transparent-data-encryption-byok-configure.md)
- [Girar chaves BYOK da TDE](../database/transparent-data-encryption-byok-key-rotation.md)
- [Remover um protetor de TDE](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [Configurar OLTP na memória](../in-memory-oltp-configure.md)
- [Configurar Automação do Azure](../database/automation-manage.md)
- A [replicação transacional](replication-between-two-instances-configure-tutorial.md) permite replicar seus dados entre instâncias gerenciadas ou de SQL Server locais para o SQL instância gerenciada e vice-versa.
- [Configurar a detecção de ameaças](threat-detection-configure.md) – a [detecção de ameaças](../database/threat-detection-overview.md) é um recurso interno do Azure SQL instância gerenciada que detecta vários ataques em potencial, como injeção de SQL ou acesso de locais suspeitos. 
- A [criação de alertas](alerts-create.md) permite que você configure alertas em métricas monitoradas, como utilização da CPU, consumo de espaço de armazenamento, IOPS e outros para o SQL instância gerenciada. 

## <a name="monitoring-and-tuning"></a>Monitoramento e ajuste

- [Ajuste manual](../database/performance-guidance.md)
- [Utilizar DMVs para monitorar o desempenho](../database/monitoring-with-dmvs.md)
- [Usar Repositório de Consultas para monitorar o desempenho](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Solucionar problemas de desempenho com o Intelligent Insights](../database/intelligent-insights-troubleshoot-performance.md)
- [Usar o log de diagnóstico Intelligent Insights](../database/intelligent-insights-use-diagnostics-log.md)
- [Monitorar In-Memory espaço OLTP](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Eventos estendidos

- [Eventos estendidos](../database/xevent-db-diff-from-svr.md)
- [Armazenar eventos estendidos em um arquivo de evento](../database/xevent-code-event-file.md)
- [Armazenar eventos estendidos em um buffer de anéis](../database/xevent-code-ring-buffer.md)

### <a name="alerting"></a>Alertas

- [Criar alertas na instância gerenciada](alerts-create.md)

## <a name="operations"></a>Operações

- [Failover manual iniciado pelo usuário no SQL Instância Gerenciada](user-initiated-failover.md)

## <a name="develop-applications"></a>Desenvolver aplicativos

- [Conectividade](../database/connect-query-content-reference-guide.md#libraries)
- [Usar o Conector do Spark](../../cosmos-db/spark-connector.md)
- [Autenticar um aplicativo](../database/application-authentication-get-client-id-keys.md)
- [Usar o envio em lote para obter melhor desempenho](../performance-improve-use-batching.md)
- [Diretrizes de conectividade](../database/troubleshoot-common-connectivity-issues.md)
- [Aliases de DNS](../database/dns-alias-overview.md)
- [Configurar um alias DNS usando o PowerShell](../database/dns-alias-powershell-create.md)
- [Portas - ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C e C ++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>Design de aplicativos

- [Design para a recuperação de desastres](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [Design para pools elásticos](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Design para atualizações do aplicativo](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Design de aplicativos SaaS multilocatários

- [Padrões de design do SaaS](../database/saas-tenancy-app-design-patterns.md)
- [Indexador de vídeo de SaaS](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Segurança de aplicativo de SaaS](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Próximas etapas

Comece [implantando o SQL instância gerenciada](instance-create-quickstart.md).