---
title: Perguntas frequentes
titleSuffix: Azure SQL Managed Instance
description: Perguntas frequentes (FAQ) sobre a Instância Gerenciada de SQL do Azure
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 09/21/2020
ms.openlocfilehash: 9faaf79958443c252a8d913fbd7448389c610e09
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628570"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ) sobre a Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo contém as perguntas mais comuns sobre o [Azure SQL instância gerenciada](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Recursos compatíveis

**Onde posso encontrar uma lista de recursos com suporte no SQL Instância Gerenciada?**

Para obter uma lista dos recursos com suporte no SQL Instância Gerenciada, consulte [recursos do sql instância gerenciada do Azure](../database/features-comparison.md).

Para obter diferenças na sintaxe e no comportamento entre o Azure SQL Instância Gerenciada e o SQL Server, consulte [diferenças de T-SQL do SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Especificações técnicas, limites de recursos e outras limitações
 
**Onde posso encontrar características técnicas e limites de recursos para o SQL Instância Gerenciada?**

Para obter as características de geração de hardware disponíveis, consulte [diferenças técnicas em gerações de hardware](resource-limits.md#hardware-generation-characteristics).
Para as camadas de serviço disponíveis e suas características, consulte [diferenças técnicas entre as camadas de serviço](resource-limits.md#service-tier-characteristics).

**Para qual camada de serviço eu tenho direito?**

Qualquer cliente está qualificado para qualquer camada de serviço. No entanto, se você quiser trocar suas licenças existentes por tarifas com desconto no Azure SQL Instância Gerenciada usando [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), tenha em mente que os clientes do SQL Server Enterprise Edition com Software Assurance estão qualificados para os níveis de [uso geral](../database/service-tier-general-purpose.md) ou [comercialmente crítico](../database/service-tier-business-critical.md) de desempenho e os clientes do SQL Server Standard Edition com Software Assurance são elegíveis apenas para o nível de desempenho uso geral. Para obter mais detalhes, consulte [direitos específicos do AHB](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server).

**Quais tipos de assinatura têm suporte para o SQL Instância Gerenciada?**

Para obter a lista de tipos de assinatura com suporte, consulte [tipos de assinatura com suporte](resource-limits.md#supported-subscription-types). 

**Quais regiões do Azure têm suporte?**

As instâncias gerenciadas podem ser criadas na maioria das regiões do Azure; consulte [regiões com suporte para o SQL instância gerenciada](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Se você precisar de uma instância gerenciada em uma região que não tenha suporte atualmente, [envie uma solicitação de suporte por meio do portal do Azure](../database/quota-increase-request.md).

**Há limitações de cota para implantações do SQL Instância Gerenciada?**

A instância gerenciada tem dois limites padrão: limite no número de sub-redes que você pode usar e um limite no número de vCores que você pode provisionar. Os limites variam entre os tipos de assinatura e as regiões. Para obter a lista de limitações de recursos regionais por tipo de assinatura, consulte tabela de [limitação de recursos regionais](resource-limits.md#regional-resource-limitations). Esses são os limites flexíveis que podem ser aumentados sob demanda. Se você precisar provisionar instâncias mais gerenciadas em suas regiões atuais, envie uma solicitação de suporte para aumentar a cota usando o portal do Azure. Para obter mais informações, consulte [aumentos de cota de solicitação para o banco de dados SQL do Azure](../database/quota-increase-request.md).

**Posso aumentar o limite do número de bancos de dados (100) em minha instância gerenciada sob demanda?**

Não, e atualmente não há planos confirmados para aumentar o número de bancos de dados no SQL Instância Gerenciada. 

**Onde posso migrar se tiver mais de 8 TB de dados?**
Você pode considerar a migração para outros tipos do Azure que atendam à sua carga de trabalho: [hiperescala do banco de dados SQL do Azure](../database/service-tier-hyperscale.md) ou [SQL Server em máquinas virtuais do Azure](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Onde posso migrar se tiver requisitos de hardware específicos, como uma taxa de RAM maior para a proporção vCore ou mais CPUs?**
Você pode considerar a migração para [SQL Server em máquinas virtuais do Azure](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) ou memória/CPU [do banco de dados SQL do Azure](../database/sql-database-paas-overview.md) otimizada.

## <a name="known-issues-and-defects"></a>Problemas conhecidos e defeitos

**Onde posso encontrar problemas conhecidos e defeitos?**

Para defeitos de produto e problemas conhecidos, consulte [problemas conhecidos](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Novos recursos

**Onde posso encontrar os recursos mais recentes e os recursos na visualização pública?**

Para obter recursos novos e de visualização, consulte [notas de versão](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="create-update-delete-or-move-sql-managed-instance"></a>Criar, atualizar, excluir ou mover o SQL Instância Gerenciada

**Como posso provisionar o SQL Instância Gerenciada?**

Você pode provisionar uma instância nos modelos [portal do Azure](instance-create-quickstart.md), [PowerShell](scripts/create-configure-managed-instance-powershell.md), [CLI do Azure](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) e [ARM](/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates).

**Posso provisionar instâncias gerenciadas em uma assinatura existente?**

Sim, você pode provisionar um Instância Gerenciada em uma assinatura existente se essa assinatura pertencer aos [tipos de assinatura com suporte](resource-limits.md#supported-subscription-types).

**Por que não foi possível provisionar um Instância Gerenciada na sub-rede cujo nome começa com um dígito?**

Essa é uma limitação atual do componente subjacente que verifica o nome da sub-rede em relação ao Regex ^ [a-za-Z_] [^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ] * (? <! [ \. \s]) $. Todos os nomes que passam o Regex e são nomes de sub-rede válidos atualmente têm suporte.

**Como posso dimensionar minha instância gerenciada?**

Você pode dimensionar sua instância gerenciada de modelos [portal do Azure](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation), [PowerShell](/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell), [CLI do Azure](/cli/azure/sql/mi#az-sql-mi-update) ou [ARM](/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates).

**Posso mover meu Instância Gerenciada de uma região para outra?**

Sim, você pode. Para obter instruções, consulte [mover recursos entre regiões](../database/move-resources-across-regions.md).

**Como posso excluir meu Instância Gerenciada?**

Você pode excluir instâncias gerenciadas por meio de APIs REST portal do Azure, [PowerShell](/powershell/module/az.sql/remove-azsqlinstance), [CLI do Azure](/cli/azure/sql/mi#az-sql-mi-delete) ou [Resource Manager](/rest/api/sql/managedinstances/delete).

**Quanto tempo leva para criar ou atualizar uma instância, ou para restaurar um banco de dados?**

O tempo esperado para criar uma nova instância gerenciada ou para alterar as camadas de serviço (vCores, armazenamento) depende de vários fatores. Consulte [operações de gerenciamento](sql-managed-instance-paas-overview.md#management-operations).
 
## <a name="naming-conventions"></a>Convenções de nomenclatura

**Uma instância gerenciada pode ter o mesmo nome que uma instância local SQL Server?**

Não há suporte para a alteração de um nome de instância gerenciada.

**Posso alterar o prefixo da zona DNS?**

Sim, Instância Gerenciada zona DNS padrão *. Database.Windows.net* pode ser alterado. 

Para usar outra zona DNS em vez do padrão, por exemplo, *.contoso.com*: 
- Use CliConfig para definir um alias. A ferramenta é apenas um wrapper de configurações do registro, portanto, ela também pode ser feita usando a política de grupo ou um script.
- Use *CNAME* com a opção *TrustServerCertificate = true* .

## <a name="migration-options"></a>Opções de migração

**Como migrar do banco de dados SQL do Azure um pool elástico ou único para o SQL Instância Gerenciada?**

A instância gerenciada oferece os mesmos níveis de desempenho por computação e tamanho de armazenamento que outras opções de implantação do Banco de Dados SQL do Azure. Se você quiser consolidar dados em uma única instância ou se simplesmente precisar de um recurso com suporte exclusivo na instância gerenciada, poderá migrar seus dados usando a funcionalidade de exportação/importação (BACPAC). Aqui estão outras maneiras de considerar a migração do banco de dados SQL para o SQL Instância Gerenciada: 
- Usando a [fonte de dados externa](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210)
- Usando o [SqlPackage](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182)
- Usando [bcp](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7)

**Como posso migrar meu banco de dados de instância para um único Banco de Dados SQL do Azure?**

Uma opção é [exportar um banco de dados para BACPAC](../database/database-export.md) e, em seguida, [importar o arquivo BACPAC](../database/database-import.md). Essa é a abordagem recomendada caso o banco de dados seja menor que 100 GB.

A [replicação transacional](replication-two-instances-and-sql-server-configure-tutorial.md) poderá ser usada se todas as tabelas no banco de dados tiverem chaves *primárias* e não houver objetos OLTP na memória no banco de dados.

Os backups de COPY_ONLY nativos obtidos da instância gerenciada não podem ser restaurados para SQL Server porque a instância gerenciada tem uma versão de banco de dados superior em comparação com SQL Server. Para obter mais detalhes, consulte [backup somente cópia](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15).

**Como posso migrar minha instância de SQL Server para o SQL Instância Gerenciada?**

Para migrar sua instância de SQL Server, confira [migração de instância de SQL Server para instância gerenciada do SQL do Azure](migrate-to-instance-from-sql-server.md).

**Como posso migrar de outras plataformas para o SQL Instância Gerenciada?**

Para obter informações de migração sobre como migrar de outras plataformas, confira o [Guia de Migração de Banco de Dados do Azure](https://datamigration.microsoft.com/).

## <a name="switch-hardware-generation"></a>Alternar geração de hardware 

**Posso mudar minha geração de hardware de instância gerenciada entre Gen 4 e Gen 5 online?**

A alternância online automática de Gen4 para Gen5 é possível se o hardware Gen5 estiver disponível na região em que a instância gerenciada é provisionada. Nesse caso, você pode verificar a [página de visão geral do modelo vCore](../database/service-tiers-vcore.md) explicando como alternar entre as gerações de hardware.

Essa é uma operação de execução longa, pois uma nova instância gerenciada será provisionada em segundo plano e os bancos de dados serão transferidos automaticamente entre a instância antiga e a nova com um failover rápido no final do processo.

Observação: o hardware do Gen4 está sendo descontinuado e não está mais disponível para novas implantações. Todos os novos bancos de dados devem ser implantados em hardware Gen5. Alternar de Gen5 para Gen4 também não está disponível.

## <a name="performance"></a>Desempenho 

**Como comparar Instância Gerenciada desempenho para SQL Server desempenho?**

Para uma comparação de desempenho entre a instância gerenciada e o SQL Server, um bom ponto de partida é o artigo [Práticas recomendadas para comparação de desempenho entre a instância gerenciada do Azure SQL e o SQL Server](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210).

**O que causa diferenças de desempenho entre Instância Gerenciada e SQL Server?**

Consulte as [principais causas de diferenças de desempenho entre a instância gerenciada do SQL e o SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/). Para obter mais informações sobre o impacto do tamanho do arquivo de log em Uso Geral Instância Gerenciada desempenho, consulte [impacto do tamanho do arquivo de log em uso geral](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

**Como fazer para ajustar o desempenho da minha instância gerenciada?**

Você pode otimizar o desempenho da instância gerenciada do:
- [Ajuste automático](../database/automatic-tuning-overview.md) que fornece desempenho de pico e cargas de trabalho estáveis por meio do ajuste de desempenho contínuo com base no ia e no aprendizado de máquina.
-    [OLTP na memória](../in-memory-oltp-overview.md) que melhora a taxa de transferência e a latência em cargas de trabalho de processamento transacionais e fornece informações de negócios mais rápidas. 

Para ajustar ainda mais o desempenho, considere aplicar algumas das *práticas recomendadas* para o [ajuste de aplicativos e bancos de dados](../database/performance-guidance.md#tune-your-database).
Se sua carga de trabalho consistir em muitas transações pequenas, considere [alternar o tipo de conexão do proxy para o modo de redirecionamento](connection-types-overview.md#changing-connection-type) para latência mais baixa e taxa de transferência mais alta.

## <a name="monitoring-metrics-and-alerts"></a>Monitoramento, métricas e alertas

**Quais são as opções de monitoramento e alerta para minha instância gerenciada?**

Para todas as opções possíveis para monitorar e alertar sobre o consumo e o desempenho do SQL Instância Gerenciada, consulte [postagem do blog de opções de monitoramento do instância gerenciada SQL do Azure](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416). Para o monitoramento de desempenho em tempo real para o SQL MI, consulte [monitoramento de desempenho em tempo real para o Azure SQL DB instância gerenciada](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance).

**Posso usar o SQL Profiler para rastreamento de desempenho?**

Sim, o SQL Profiler tem suporte ou SQL Instância Gerenciada. Para obter mais detalhes, consulte [SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15).

**Há suporte para Assistente do Banco de Dados e Análise de Desempenho de Consultas para bancos de dados Instância Gerenciada?**

Não, não há suporte para eles. Você pode usar [DMVs](../database/monitoring-with-dmvs.md) e [repositório de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?preserve-view=true&view=sql-server-ver15) junto com o [SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15) e o [XEvents](/sql/relational-databases/extended-events/extended-events?preserve-view=true&view=sql-server-ver15) para monitorar seus bancos de dados.

**Posso criar alertas de métrica no SQL Instância Gerenciada?**

Sim. Para obter instruções, consulte [criar alertas para o SQL instância gerenciada](alerts-create.md).

**Posso criar alertas de métrica em um banco de dados na instância gerenciada?**

Você não pode, as métricas de alerta estão disponíveis somente para instância gerenciada. As métricas de alerta para bancos de dados individuais na instância gerenciada não estão disponíveis.

## <a name="storage-size"></a>Tamanho de armazenamento

**Qual é o tamanho máximo de armazenamento para o SQL Instância Gerenciada?**

O tamanho do armazenamento para o SQL Instância Gerenciada depende da camada de serviço selecionada (Uso Geral ou Comercialmente Crítico). Para limitações de armazenamento dessas camadas de serviço, consulte [características da camada de serviço](../database/service-tiers-general-purpose-business-critical.md).

**Qual é o tamanho mínimo de armazenamento disponível para uma instância gerenciada?**

A quantidade mínima de armazenamento disponível em uma instância é de 32 GB. O armazenamento pode ser adicionado em incrementos de 32 GB até o tamanho máximo de armazenamento. Os primeiros 32GB são gratuitos.

**Posso aumentar o espaço de armazenamento atribuído a uma instância, independentemente dos recursos de computação?**

Sim, você pode comprar o armazenamento de complemento, independentemente da computação, até certo ponto. Consulte *armazenamento reservado de instância máxima* na [tabela](resource-limits.md#hardware-generation-characteristics).

**Como posso otimizar o desempenho do armazenamento na camada de serviço Uso Geral?**

Para otimizar o desempenho do armazenamento, consulte [práticas recomendadas de armazenamento no uso geral](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525).

## <a name="backup-and-restore"></a>Backup e restauração

**O armazenamento de backup foi deduzido do meu armazenamento de instância gerenciada?**

Não, o armazenamento de backup não é deduzido do seu espaço de armazenamento de instância gerenciada. O armazenamento de backup é independente do espaço de armazenamento da instância e não é limitado em tamanho. O armazenamento de backup é limitado pelo período de tempo para reter o backup de seus bancos de dados de instância, configuráveis até 35 dias. Para mais detalhes, consulte [Backups automatizados](../database/automated-backups-overview.md).

**Como posso ver quando os backups automatizados são feitos na minha instância gerenciada?**

Para acompanhar quando os backups automatizados foram executados no Instância Gerenciada, consulte [como acompanhar o backup automatizado de um instância gerenciada do SQL do Azure](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355).

**Há suporte para backup sob demanda?**

Sim, você pode criar um backup completo somente cópia em seu armazenamento de BLOBs do Azure, mas ele só poderá ser restaurável no Instância Gerenciada. Para obter detalhes, consulte [backup somente cópia](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15). No entanto, o backup somente cópia será impossível se o banco de dados for criptografado pelo TDE gerenciado pelo serviço, uma vez que o certificado usado para criptografia é inacessível. Nesse caso, use o recurso de restauração pontual para mover o banco de dados para outro Instância Gerenciada SQL ou alternar para a chave gerenciada pelo cliente.

**A restauração nativa (de arquivos. bak) Instância Gerenciada é suportada?**

Sim, ele tem suporte e está disponível para versões do SQL Server 2005 +.  Para usar a restauração nativa, carregue o arquivo. bak no armazenamento de BLOBs do Azure e execute comandos T-SQL. Para obter mais detalhes, consulte [Native Restore from URL](./migrate-to-instance-from-sql-server.md#native-restore-from-url).

## <a name="business-continuity"></a>Continuidade de negócios

**Meus bancos de dados do sistema foram replicados para a instância secundária em um grupo de failover?**

Os bancos de dados do sistema não são replicados para a instância secundária em um grupo de failover. Portanto, os cenários que dependem de objetos dos bancos de dados do sistema serão impossíveis na instância secundária, a menos que os objetos sejam criados manualmente no secundário. Para solução alternativa, consulte [habilitar cenários dependentes do objeto dos bancos de dados do sistema](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases).
 
## <a name="networking-requirements"></a>Requisitos de rede 

**Quais são as restrições atuais de NSG de entrada/saída na sub-rede Instância Gerenciada?**

As regras necessárias de NSG e UDR são documentadas [aqui](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)e definidas automaticamente pelo serviço.
Tenha em mente que essas regras são apenas aquelas que precisamos para manter o serviço. Para se conectar à instância gerenciada e usar recursos diferentes, você precisará definir regras adicionais específicas do recurso, que você precisa manter.

**Como posso definir regras de NSG de entrada em portas de gerenciamento?**

O SQL Instância Gerenciada é responsável por definir regras em portas de gerenciamento. Isso é obtido por meio da funcionalidade chamada [configuração de sub-rede auxiliada por serviço](connectivity-architecture-overview.md#service-aided-subnet-configuration).
Isso é para garantir o fluxo ininterrupto do tráfego de gerenciamento a fim de atender a um SLA.

**Posso obter os intervalos de IP de origem que são usados para o tráfego de gerenciamento de entrada?**

Sim. Você pode analisar o tráfego proveniente do grupo de segurança de suas redes [Configurando logs de fluxo do observador de rede](../../network-watcher/network-watcher-monitoring-overview.md#analyze-traffic-to-or-from-a-network-security-group).

**Posso definir NSG para controlar o acesso ao ponto de extremidade de dados (porta 1433)?**

Sim. Depois que um Instância Gerenciada for provisionado, você poderá definir NSG que controla o acesso de entrada à porta 1433. É aconselhável restringir seu intervalo de IP o máximo possível.

**Posso definir o firewall local ou NVA para filtrar o tráfego de gerenciamento de saída com base em FQDNs?**

Não. Isso não é suportado por vários motivos:
-    O tráfego de roteamento que representa a resposta à solicitação de gerenciamento de entrada seria assimétrico e não pode funcionar.
-    O tráfego de roteamento que vai para o armazenamento seria afetado por restrições de taxa de transferência e latência, de modo que não será possível fornecer a qualidade de serviço esperada e a disponibilidade.
-    Com base na experiência, essas configurações são propensas a erros e não podem ser suportadas.

**Posso definir o NVA ou o firewall para o tráfego de não gerenciamento de saída?**

Sim. A maneira mais simples de conseguir isso é adicionar a regra 0/0 a um UDR associado à sub-rede da instância gerenciada para rotear o tráfego por meio do NVA.
 
**Quantos endereços IP preciso para um Instância Gerenciada?**

A sub-rede deve ter um número suficiente de [endereços IP](connectivity-architecture-overview.md#network-requirements)disponíveis. Para determinar o tamanho da sub-rede VNet para o SQL Instância Gerenciada, consulte [determinar o tamanho e o intervalo de sub-rede necessários para instância gerenciada](./vnet-subnet-determine-size.md). 

**E se não houver endereços IP suficientes para executar a operação de atualização de instância?**

Caso não haja [endereços IP](connectivity-architecture-overview.md#network-requirements) suficientes na sub-rede em que sua instância gerenciada é provisionada, você precisará criar uma nova sub-rede e uma nova instância gerenciada dentro dela. Também sugerimos que a nova sub-rede seja criada com mais endereços IP alocados para que as operações de atualização futuras evitem uma situação semelhante. Depois que a nova instância for provisionada, você poderá fazer backup e restaurar dados manualmente entre as instâncias novas e antigas ou executar a [restauração pontual](point-in-time-restore.md?tabs=azure-powershell)entre instâncias.

**Preciso de uma sub-rede vazia para criar uma Instância Gerenciada?**

Não. Você pode usar uma sub-rede vazia ou uma sub-rede que já contenha Instância Gerenciada (s). 

**Posso alterar o intervalo de endereços de sub-rede?**

Não se houver instâncias gerenciadas dentro do. Esta é uma limitação da infraestrutura de rede do Azure. Você só tem permissão para [adicionar mais espaço de endereço a uma sub-rede vazia](../../virtual-network/virtual-network-manage-subnet.md#change-subnet-settings). 

**Posso mover minha instância gerenciada para outra sub-rede?**

Não. Essa é uma limitação de design de Instância Gerenciada atual. No entanto, você pode provisionar uma nova instância em outra sub-rede e fazer backup e restaurar dados manualmente entre a instância antiga e a nova ou executar a [restauração pontual](point-in-time-restore.md?tabs=azure-powershell)entre instâncias.

**Preciso de uma rede virtual vazia para criar uma Instância Gerenciada?**

Isso não é necessário. Você pode [criar uma rede virtual para o Azure sql instância gerenciada](./virtual-network-subnet-create-arm-template.md) ou [Configurar uma rede virtual existente para o SQL instância gerenciada do Azure](./vnet-existing-add-subnet.md).

**Posso inserir um Instância Gerenciada com outros serviços em uma sub-rede?**

Não. No momento, não há suporte para colocar Instância Gerenciada em uma sub-rede que já contenha outros tipos de recursos.

## <a name="connectivity"></a>Conectividade 

**Posso me conectar à minha instância gerenciada usando o endereço IP?**

Não, isso não tem suporte. O nome de host de um Instância Gerenciada é mapeado para o balanceador de carga na frente do cluster virtual do Instância Gerenciada. Como um cluster virtual pode hospedar várias instâncias gerenciadas, uma conexão não pode ser roteada para o Instância Gerenciada apropriado sem especificar seu nome.
Para obter mais informações sobre a arquitetura de cluster virtual do SQL Instância Gerenciada, consulte [arquitetura de conectividade do cluster virtual](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Minha instância gerenciada pode ter um endereço IP estático?**

Não há suporte para esse recurso no momento.

Em situações raras, mas necessárias, talvez seja preciso fazer uma migração online de uma instância gerenciada para um novo cluster virtual. Se necessário, essa migração ocorre devido a alterações em nossa pilha de tecnologia destinadas a melhorar a segurança e a confiabilidade do serviço. Migrar para um novo cluster virtual resulta na alteração do endereço IP que é mapeado para o nome do host da instância gerenciada. O serviço de instância gerenciada não alega suporte a endereços IP estáticos e reserva o direito de alterá-lo sem aviso como parte dos ciclos de manutenção regulares.

Por esse motivo, é altamente recomendável depender da imutabilidade do endereço IP, pois isso poderia causar um tempo de inatividade desnecessário.

**Instância Gerenciada tem um ponto de extremidade público?**

Sim. Instância Gerenciada tem um ponto de extremidade público que é usado por padrão apenas para o gerenciamento de serviços, mas um cliente também pode habilitá-lo para acesso a dados. Para obter mais detalhes, consulte [usar o SQL instância gerenciada com pontos de extremidade públicos](./public-endpoint-overview.md). Para configurar o ponto de extremidade público, vá para [Configurar ponto de extremidade público no SQL instância gerenciada](public-endpoint-configure.md).

**Como o Instância Gerenciada controlar o acesso ao ponto de extremidade público?**

Instância Gerenciada controla o acesso ao ponto de extremidade público no nível de rede e de aplicativo.

Os serviços de gerenciamento e implantação se conectam a uma instância gerenciada usando um [ponto de extremidade de gerenciamento](./connectivity-architecture-overview.md#management-endpoint) que é mapeado para um balanceador de carga externo. O tráfego será roteado para os nós somente se ele for recebido em um conjunto predefinido de portas que apenas os componentes de gerenciamento da instância gerenciada usam. Um firewall interno nos nós é configurado para permitir o tráfego somente de intervalos de IP da Microsoft. Os certificados autenticam mutuamente toda a comunicação entre os componentes de gerenciamento e o plano de gerenciamento. Para obter mais detalhes, consulte [arquitetura de conectividade para o SQL instância gerenciada](./connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Eu poderia usar o ponto de extremidade público para acessar os dados em bancos de Instância Gerenciada?**

Sim. O cliente precisará habilitar o acesso a dados de ponto de extremidade público do [portal do Azure](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)  /  [PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) /ARM e configurar o NSG para bloquear o acesso à porta de dados (número da porta 3342). Para obter mais informações, consulte [Configurar o ponto de extremidade público no Azure sql instância gerenciada](public-endpoint-configure.md) e [usar o sql do Azure instância gerenciada com segurança com o ponto de extremidade público](public-endpoint-overview.md). 

**Posso especificar uma porta personalizada para os pontos de extremidade de dados SQL?**

Não, essa opção não está disponível.  Para o ponto de extremidade de dados privado, Instância Gerenciada usa o número de porta padrão 1433 e para o ponto de extremidade de dados públicos, Instância Gerenciada usa o número de porta padrão 3342.

**Qual é a maneira recomendada para conectar as instâncias gerenciadas colocadas em regiões diferentes?**

O emparelhamento de circuito de rota expressa é a maneira preferida de fazer isso. Há suporte para o emparelhamento de rede virtual global com a limitação descrita na observação abaixo.  

> [!IMPORTANT]
> [Em 9/22/2020 anunciamos o emparelhamento de rede virtual global para clusters virtuais recém-criados](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Isso significa que o emparelhamento de rede virtual global tem suporte para instâncias gerenciadas do SQL criadas em sub-redes vazias após a data do anúncio, bem como para todas as instâncias gerenciadas subsequentes criadas nessas sub-redes. Para todas as outras instâncias gerenciadas do SQL, o suporte ao emparelhamento é limitado às redes na mesma região devido às [restrições do emparelhamento de rede virtual global](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Consulte também a seção relevante do artigo [perguntas frequentes sobre redes virtuais do Azure](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) para obter mais detalhes. 

Se o emparelhamento de circuito de rota expressa e o emparelhamento de rede virtual global não forem possíveis, a única opção é criar uma conexão VPN site a site ([portal do Azure](../../vpn-gateway/tutorial-site-to-site-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) [CLI do Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)).

## <a name="mitigate-data-exfiltration-risks"></a>Reduzir riscos de vazamento de dados  

**Como posso reduzir os riscos de vazamento de dados?**

Para reduzir os riscos de vazamento de dados, é recomendável que os clientes apliquem um conjunto de configurações e controles de segurança:

- Ative a [Transparent Data Encryption (TDE)](../database/transparent-data-encryption-tde-overview.md) em todos os bancos de dados.
- Desative o CLR (Common Language Runtime). Isso também é recomendado no local.
- Use somente a autenticação Azure Active Directory (AD do Azure).
- Acesse a instância com uma conta de DBA com poucos privilégios.
- Configure o acesso Jumpbox JIT para a conta sysadmin.
- Ative a [auditoria do SQL](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) e integre-a nos mecanismos de alerta.
- Ative a [detecção de ameaças](../database/threat-detection-configure.md) do [Azure defender para SQL](../database/azure-defender-for-sql.md) Suite.

## <a name="dns"></a>DNS

**Posso configurar um DNS personalizado para o SQL Instância Gerenciada?**

Sim. Consulte [como configurar um DNS personalizado para o Azure SQL instância gerenciada](./custom-dns-configure.md).

**Posso fazer a atualização de DNS?**

Sim. Confira a [configuração sincronizar servidores DNS da rede virtual no cluster virtual do SQL instância gerenciada](./synchronize-vnet-dns-servers-setting-on-virtual-cluster.md).

## <a name="change-time-zone"></a>Alterar fuso horário

**Posso alterar o fuso horário de uma instância gerenciada existente?**

A configuração de fuso horário pode ser definida quando uma instância gerenciada é provisionada pela primeira vez. Não há suporte para a alteração do fuso horário de uma instância gerenciada existente. Para obter detalhes, consulte [limitações de fuso horário](timezones-overview.md#limitations).

As soluções alternativas incluem a criação de uma nova instância gerenciada com o fuso horário adequado e a execução de um backup e restauração manuais, ou o que recomendamos, executando uma [restauração pontual de instância cruzada](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance).


## <a name="security-and-database-encryption"></a>Segurança e criptografia de banco de dados

**A função de servidor sysadmin está disponível para o SQL Instância Gerenciada?**

Sim, os clientes podem criar logons que são membros da função sysadmin.  Os clientes que assumem o privilégio sysadmin também pressupõem a responsabilidade de operar a instância, o que pode afetar negativamente o compromisso do SLA. Para adicionar logon à função de servidor sysadmin, consulte [autenticação do Azure ad](./aad-security-configure-tutorial.md#azure-ad-authentication).

**Transparent Data Encryption há suporte para Instância Gerenciada do SQL?**

Sim, há suporte para Transparent Data Encryption para SQL Instância Gerenciada. Para obter detalhes, consulte [Transparent Data Encryption para SQL instância gerenciada](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal).

**Posso aproveitar o modelo "Traga sua própria chave" para TDE?**

Sim, Azure Key Vault para o cenário BYOK está disponível para Instância Gerenciada do Azure SQL. Para obter detalhes, consulte [Transparent Data Encryption com chave gerenciada pelo cliente](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key).

**Posso migrar um banco de dados SQL Server criptografado?**

Sim, você pode. Para migrar um banco de dados SQL Server criptografado, você precisa exportar e importar seus certificados existentes para o Instância Gerenciada e, em seguida, fazer um backup completo do banco de dados e restaurá-lo no Instância Gerenciada. 

Você também pode usar o [serviço de migração de banco de dados do Azure](https://azure.microsoft.com/services/database-migration/) para migrar os bancos de TDE criptografados.

**Como posso configurar a rotação do protetor de TDE para o SQL Instância Gerenciada?**

Você pode girar o protetor de TDE para Instância Gerenciada usando Azure Cloud Shell. Para obter instruções, consulte [Transparent Data Encryption no SQL instância gerenciada usando sua própria chave de Azure Key Vault](scripts/transparent-data-encryption-byok-powershell.md).

**Posso restaurar meu banco de dados criptografado para o SQL Instância Gerenciada?**

Sim, você não precisa descriptografar seu banco de dados para restaurá-lo para o SQL Instância Gerenciada. Você precisa fornecer um certificado/chave usado como o protetor de chave de criptografia no sistema de origem para o SQL Instância Gerenciada para poder ler dados do arquivo de backup criptografado. Há duas maneiras possíveis de fazer isso:

- *Carregar o protetor de certificado para o SQL instância gerenciada*. Isso só pode ser feito usando o PowerShell. O [script de exemplo](./tde-certificate-migrate.md) descreve todo o processo.
- *Carregue o protetor de chave assimétrica em Azure Key Vault e aponte instância gerenciada SQL para ele*. Essa abordagem é semelhante ao BYOK (traga sua própria chave) TDE caso de uso que também usa a integração Key Vault para armazenar a chave de criptografia. Se você não quiser usar a chave como um protetor de chave de criptografia e apenas quiser disponibilizar a chave para o SQL Instância Gerenciada restaurar bancos de dados criptografados, siga as instruções para [Configurar BYOK TDE](../database/transparent-data-encryption-tde-overview.md#manage-transparent-data-encryption)e não marque a caixa de seleção **tornar a chave selecionada o protetor de TDE padrão**.

Depois de disponibilizar o protetor de criptografia para o SQL Instância Gerenciada, você pode prosseguir com o procedimento de restauração do banco de dados padrão.

## <a name="purchasing-models-and-benefits"></a>Modelos e benefícios de compra

**Quais modelos de compra estão disponíveis para o SQL Instância Gerenciada?**

O SQL Instância Gerenciada oferece um [modelo de compra baseado em vCore](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model).

**Quais benefícios de custo estão disponíveis para o SQL Instância Gerenciada?**

Você pode economizar custos com os benefícios do SQL Azure das seguintes maneiras:
-    Maximize os investimentos existentes em licenças locais e economize até 55 por cento com [benefício híbrido do Azure](../azure-hybrid-benefit.md?tabs=azure-powershell). 
-    Confirme a uma reserva de recursos de computação e economize até 33 por cento com [benefício de instância reservada](../database/reserved-capacity-overview.md). Combine isso com o benefício híbrido do Azure para economizar até 82%. 
-    Economize até 55 por cento versus preços de lista com o benefício de preço de desenvolvimento [/teste do Azure](https://azure.microsoft.com/pricing/dev-test/) que oferece tarifas com desconto para suas cargas de trabalho de desenvolvimento e teste em andamento.

**Quem está qualificado para o benefício da instância reservada?**

Para se qualificar para o benefício da instância reservada, o tipo de assinatura deve ser um Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um contrato individual com preço pago conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para obter mais informações sobre reservas, consulte [benefício da instância reservada](../database/reserved-capacity-overview.md). 

**É possível cancelar, trocar ou reembolsar reservas?**

Você pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Cobrança para armazenamento de Instância Gerenciada e backup

**Quais são as opções de preços do SQL Instância Gerenciada?**

Para explorar Instância Gerenciada opções de preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

**Como rastrear o custo de cobrança da minha instância gerenciada?**

Você pode fazer isso usando a [Solução de Gerenciamento de Custos do Azure](../../cost-management-billing/index.yml). Navegue até **Assinaturas** no [portal do Azure](https://portal.azure.com) e selecione **Análise de Custo**. 

Use a opção **Custos acumulados** e, em seguida, filtre pelo **Tipo de recurso** como `microsoft.sql/managedinstances`.

**Quanto custa os backups automatizados?**

Você Obtém a quantidade igual de espaço de armazenamento de backup livre que o espaço de armazenamento de dados reservado adquirido, independentemente do período de retenção de backup definido. Se o consumo de armazenamento de backup estiver dentro do espaço de armazenamento de backup livre alocado, os backups automatizados na instância gerenciada não terão nenhum custo adicional para você, portanto, serão gratuitos. Exceder o uso do armazenamento de backup acima do espaço livre resultará em custos de cerca de $0.20 a $0.24 por GB/mês em regiões dos EUA, ou consulte a página de preços para obter detalhes de sua região. Para obter mais detalhes, confira [consumo de armazenamento de backup explicado](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923).

**Como posso monitorar o custo de cobrança do meu consumo de armazenamento de backup?**

Você pode monitorar o custo de armazenamento de backup por meio de portal do Azure. Para obter instruções, consulte [monitorar custos para backups automatizados](../database/automated-backups-overview.md?tabs=managed-instance#monitor-costs). 

**Como posso otimizar meus custos de armazenamento de backup na instância gerenciada?**

Para otimizar os custos de armazenamento de backup, consulte [ajuste de backup fino no SQL instância gerenciada](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

## <a name="cost-saving-use-cases"></a>Casos de uso de economia de custos

**Onde posso encontrar casos de uso e economias de custos resultantes com o SQL Instância Gerenciada?**

Estudos de caso do SQL Instância Gerenciada:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Para entender melhor os benefícios, os custos e os riscos associados à implantação do Azure SQL Instância Gerenciada, há também um estudo da Forrester: [o impacto econômico total de Banco de Dados SQL do Microsoft Azure instância gerenciada](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).

## <a name="password-policy"></a>Política de senha 

**Quais políticas de senha são aplicadas para os logons do SQL Instância Gerenciada SQL?**

A política de senha do SQL Instância Gerenciada para logons SQL herda as políticas da plataforma do Azure que são aplicadas às VMs que formam o cluster virtual que mantém a instância gerenciada. No momento não é possível alterar essas configurações, pois essas configurações são definidas pelo Azure e herdadas pela instância gerenciada.

 > [!IMPORTANT]
 > A plataforma Azure pode alterar os requisitos de política sem notificar os serviços que dependem dessas políticas.

**Quais são as políticas atuais da plataforma Azure?**

Cada logon deve definir sua senha no logon e alterar sua senha depois que atingir a idade máxima.

| **Política** | **Configuração de segurança** |
| --- | --- |
| Duração máxima da senha | 42 dias |
| Duração mínima da senha | 1 dia |
| Comprimento mínimo da senha | 10 caracteres |
| A senha deve atender aos requisitos de complexidade | habilitado |

**É possível desabilitar a complexidade e a expiração de senha no SQL Instância Gerenciada no nível de logon?**

Sim, é possível controlar CHECK_POLICY e CHECK_EXPIRATION campos no nível de logon. Você pode verificar as configurações atuais executando o seguinte comando T-SQL:

```sql
SELECT *
FROM sys.sql_logins
```

Depois disso, você pode modificar as configurações de logon especificadas executando:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(substitua ' test ' pelo nome de logon desejado e ajuste a política e os valores de expiração)


## <a name="service-updates"></a>Atualizações de serviço

**Qual é a alteração da AC raiz para o banco de dados SQL do Azure & SQL Instância Gerenciada?**

Consulte [rotação de certificado para o banco de dados SQL do Azure & sql instância gerenciada](../updates/ssl-root-certificate-expiring.md). 

**O que é um evento de manutenção planejada para o SQL Instância Gerenciada?**

Consulte [planejar eventos de manutenção do Azure no SQL instância gerenciada](../database/planned-maintenance.md). 


## <a name="azure-feedback-and-support"></a>Comentários e suporte do Azure

**Onde posso deixar minhas ideias para melhorias do SQL Instância Gerenciada?**

Você pode votar em um novo recurso de Instância Gerenciada ou em uma nova ideia de aperfeiçoamento sobre a votação no fórum de comentários sobre o [SQL instância gerenciada](https://feedback.azure.com/forums/915676-sql-managed-instance). Dessa forma, você pode contribuir para o desenvolvimento do produto e nos ajudar a priorizar nossas possíveis melhorias.

**Como posso criar uma solicitação de suporte do Azure?**

Para saber como criar uma solicitação de suporte do Azure, consulte [como criar uma solicitação de suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).
