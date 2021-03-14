---
title: Melhorar o desempenho de aplicativos do Azure com o Advisor
description: Use as recomendações de desempenho no Azure Advisor para melhorar a velocidade e a capacidade de resposta de seus aplicativos críticos para os negócios.
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 69fd86226375073c71903af5704ef3a635eeaffb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579919"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>Melhorar o desempenho de aplicativos do Azure usando o Azure Advisor

As recomendações de desempenho no Azure Advisor podem ajudar a melhorar a velocidade e a capacidade de resposta de seus aplicativos críticos para os negócios. Você pode obter recomendações de desempenho do Assistente na guia **Desempenho** do painel do Assistente.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Reduzir a vida útil do DNS em seu perfil do Gerenciador de tráfego para fazer failover para pontos de extremidade íntegros mais rapidamente

Você pode usar [as configurações de vida útil (TTL)](../traffic-manager/traffic-manager-performance-considerations.md) no seu perfil do Gerenciador de tráfego do Azure para especificar com que rapidez alternar pontos de extremidade se um determinado ponto de interrupção parar de responder às consultas. Se você reduzir os valores de TTL, os clientes serão roteados para pontos de extremidade em funcionamento mais rapidamente.

O Azure Advisor identifica os perfis do Gerenciador de tráfego que têm um TTL mais longo configurado. Ele recomenda configurar a TTL para 20 segundos ou 60 segundos, dependendo se o perfil está configurado para [failover rápido](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-by-using-sql-database-advisor-temporarily-disabled"></a>Melhorar o desempenho do banco de dados usando Assistente do Banco de Dados SQL (temporariamente desabilitado)

O Assistente do Azure fornece uma exibição consistente e consolidada de recomendações para todos os recursos do Azure. Ele se integra com Assistente do Banco de Dados SQL para oferecer recomendações para melhorar o desempenho de seus bancos de dados. Assistente do Banco de Dados SQL avalia o desempenho de seus bancos de dados analisando seu histórico de uso. Em seguida, ele oferece recomendações que são mais adequadas para executar a carga de trabalho típica do banco de dados.

> [!NOTE]
> Antes que você possa obter recomendações, seu banco de dados precisa estar em uso por cerca de uma semana e, dentro dessa semana, precisa haver alguma atividade consistente. O Assistente do Banco de Dados SQL pode ser otimizado com mais facilidade para padrões de consulta consistentes do que para intermitências irregulares de atividade.

Para obter mais informações, consulte [Assistente do banco de dados SQL](../azure-sql/database/database-advisor-implement-performance-recommendations.md).

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>Atualize sua biblioteca de cliente de armazenamento para a versão mais recente para melhorar a confiabilidade e o desempenho

A versão mais recente do SDK da biblioteca de cliente de armazenamento contém correções para problemas relatados pelos clientes e identificados proativamente por meio de nosso processo de controle de qualidade. A versão mais recente também apresenta a otimização de confiabilidade e desempenho junto com os novos recursos que podem melhorar sua experiência geral com o uso do armazenamento do Azure. O Advisor fornece recomendações e etapas necessárias para atualizar para a versão mais recente do SDK se você estiver usando uma versão obsoleta. As recomendações são para os idiomas com suporte: C++ e .NET.

## <a name="improve-app-service-performance-and-reliability"></a>Melhorar o desempenho e a confiabilidade do Serviço de Aplicativo

O Azure Advisor integra recomendações para melhorar a experiência do serviço de aplicativo e descobrir recursos relevantes da plataforma. Exemplos de recomendações do serviço de aplicativo são:
* Detecção de instâncias em que os recursos de memória ou CPU são esgotados pelos tempos de execução do aplicativo, com opções de mitigação.
* Detecção de instâncias em que os recursos colocalizadores, como aplicativos Web e bancos de dados, podem melhorar o desempenho e reduzir o custo.

Para obter mais informações, consulte [práticas recomendadas para o serviço de Azure app](../app-service/app-service-best-practices.md).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Usar discos gerenciados para evitar a limitação de e/s de disco

O Advisor identifica as máquinas virtuais que pertencem a uma conta de armazenamento que está atingindo sua meta de escalabilidade. Essa condição torna essas VMs suscetíveis à limitação de E/S. O Advisor recomendará que eles usem discos gerenciados para evitar a degradação do desempenho.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Melhorar o desempenho e a confiabilidade dos discos da máquina virtual usando o Armazenamento Premium

O Advisor identifica máquinas virtuais com discos padrão que têm um alto volume de transações em sua conta de armazenamento. Ele recomenda a atualização para discos Premium. 

O Armazenamento Premium do Azure dá suporte de disco de alto desempenho e baixa latência para máquinas virtuais executando cargas de trabalho com uso intensivo de E/S. Os discos de máquina virtual que usam contas de armazenamento Premium armazenam dados em SSDs (unidades de estado sólido). Para obter o melhor desempenho para seu aplicativo, recomendamos que você migre qualquer disco de máquina virtual que exija IOPS alto para o armazenamento Premium.

## <a name="remove-data-skew-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Remover a distorção de dados em suas tabelas do Azure Synapse Analytics para aumentar o desempenho da consulta

A distorção de dados pode causar gargalos de recursos ou movimentação de dados desnecessários quando você executa a carga de trabalho. O Advisor detecta a distorção de dados de distribuição de mais de 15%. Recomenda-se redistribuir seus dados e revisitar suas seleções de chave de distribuição de tabela. Para saber mais sobre como identificar e remover distorções, consulte [solução de problemas skew](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-in-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Criar ou atualizar estatísticas de tabela desatualizadas em suas tabelas do Azure Synapse Analytics para aumentar o desempenho da consulta

O Advisor identifica tabelas que não têm [Estatísticas de tabela](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md) atualizadas e recomenda criar ou atualizar as estatísticas. O otimizador de consulta no Azure Synapse Analytics usa estatísticas atualizadas para estimar a cardinalidade ou o número de linhas nos resultados da consulta. Essas estimativas permitem que o otimizador de consulta crie um plano de consulta para fornecer o desempenho mais rápido.

## <a name="improve-mysql-connection-management"></a>Melhorar o gerenciamento de conexões do MySQL

A análise do Advisor pode indicar que o aplicativo que se conecta a um servidor MySQL pode não estar gerenciando conexões com eficiência. Essa condição pode levar a um consumo de recursos desnecessário e à latência geral de aplicativo superior. Para melhorar o gerenciamento, recomendamos que você reduza o número de conexões de curta duração e elimine conexões ociosas desnecessárias. Você pode fazer esses aprimoramentos Configurando um pooler de conexões do servidor, como ProxySQL.


## <a name="scale-up-to-optimize-cache-utilization-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Escalar verticalmente para otimizar a utilização de cache nas tabelas do Azure Synapse Analytics para aumentar o desempenho da consulta

O Azure Advisor detecta se as tabelas do Azure Synapse Analytics têm uma porcentagem alta de uso de cache e uma porcentagem de baixo impacto. Essa condição indica uma remoção de cache alta, que pode afetar o desempenho da sua instância do Azure Synapse Analytics. O Advisor recomenda que você Escale verticalmente sua instância do Azure Synapse Analytics para garantir que você aloque capacidade de cache suficiente para sua carga de trabalho.

## <a name="convert-azure-synapse-analytics-tables-to-replicated-tables-to-increase-query-performance"></a>Converter tabelas do Azure Synapse Analytics em tabelas replicadas para aumentar o desempenho da consulta

O Advisor identifica tabelas que não são tabelas replicadas, mas que se beneficiarão da conversão. Ele sugere que você converta essas tabelas. As recomendações se baseiam em:
- O tamanho da tabela replicada. 
- O número de colunas. 
- O tipo de distribuição de tabela. 
- O número de partições na tabela do Azure Synapse Analytics. 

Heurística adicional pode ser fornecida na recomendação para o contexto. Para saber mais sobre como essa recomendação é determinada, consulte [recomendações do Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>Migre sua conta de armazenamento para Azure Resource Manager para obter os recursos mais recentes do Azure

Migre seu modelo de implantação de conta de armazenamento para Azure Resource Manager para aproveitar:
- Implantações de modelo.
- Opções de segurança adicionais.
- A capacidade de atualizar para uma conta do GPv2 para que você possa usar os recursos mais recentes do armazenamento do Azure. 

O Advisor identifica todas as contas de armazenamento autônomas que estão usando o modelo de implantação clássico e recomenda a migração para o modelo de implantação do Gerenciador de recursos.

> [!NOTE]
> Os alertas clássicos no Azure Monitor foram desativados em agosto de 2019. Recomendamos que você atualize sua conta de armazenamento clássica para usar o Resource Manager e manter a funcionalidade de alerta com a nova plataforma. Para obter mais informações, consulte [clássico alertas](../azure-monitor/alerts/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)de desativação.

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Projete suas contas de armazenamento para evitar atingir o limite máximo de assinaturas

Uma região do Azure dá suporte a um máximo de 250 contas de armazenamento por assinatura. Depois que esse limite for atingido, você não poderá criar contas de armazenamento nessa combinação de região/assinatura. O Advisor verifica suas assinaturas e fornece recomendações para você projetar para menos contas de armazenamento para qualquer assinatura/região que esteja próxima de atingir o limite máximo.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>Considere aumentar o tamanho do SKU do gateway de VPN para resolver o uso de alto P2S

Cada SKU de gateway de VPN do Azure pode dar suporte apenas a um número especificado de conexões P2S simultâneas. Se a contagem de conexões estiver perto do limite do gateway, as tentativas de conexão adicionais poderão falhar. Se você aumentar o tamanho do gateway, poderá dar suporte a mais usuários P2S simultâneos. O Advisor fornece recomendações e instruções para aumentar o tamanho do seu gateway.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>Considere aumentar o tamanho do SKU do gateway de VPN para atender à alta CPU

Em alta carga de tráfego, o gateway de VPN pode descartar os pacotes devido à alta CPU. Considere atualizar o SKU do gateway de VPN. Aumentar o tamanho do gateway de VPN garantirá que as conexões não sejam descartadas devido à alta CPU. O Advisor fornece recomendações para resolver esse problema proativamente. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>Aumente o tamanho do lote ao carregar, para maximizar a taxa de transferência de carga, a compactação de dados e o desempenho da consulta

O Advisor detecta se você pode aumentar o desempenho e a taxa de transferência de carga aumentando o tamanho do lote ao carregar no banco de dados. Você pode considerar o uso da instrução COPY. Se você não puder usar a instrução COPY, considere aumentar o tamanho do lote ao usar utilitários de carregamento como a API SQLBulkCopy ou o BCP. Uma boa regra geral é usar um tamanho de lote entre 100.000 e 1 milhão linhas. O aumento do tamanho do lote aumentará a taxa de transferência, a compactação de dados e o desempenho da consulta.

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>Colocalizar a conta de armazenamento na mesma região para minimizar a latência ao carregar

O Advisor detecta se você está carregando de uma região diferente do seu pool SQL dedicado. Considere carregar de uma conta de armazenamento que esteja na mesma região que seu pool SQL dedicado para minimizar a latência ao carregar dados. Essa alteração ajudará a minimizar a latência e aumentará o desempenho de carga.

## <a name="use-a-supported-kubernetes-version"></a>Usar uma versão kubernetes com suporte

O Advisor detecta versões sem suporte do kubernetes.

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>Otimizar o desempenho do banco de dados do Azure para MySQL, banco de dados do Azure para PostgreSQL e banco de dados do Azure para servidores MariaDB

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>Corrija a pressão da CPU de seu banco de dados do Azure para MySQL, banco de dados do Azure para PostgreSQL e banco de dados do Azure para servidores MariaDB com afunilamentos de CPU
A alta utilização da CPU por um período estendido pode causar um desempenho de consulta lento para sua carga de trabalho. Aumentar o tamanho da CPU ajudará a otimizar o tempo de execução das consultas de banco de dados e melhorar o desempenho geral. O Advisor identifica servidores com uma alta utilização de CPU que provavelmente está executando cargas de trabalho restritas à CPU e recomenda dimensionar sua computação.

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Reduzir as restrições de memória no banco de dados do Azure para MySQL, banco de dados do Azure para PostgreSQL e banco de dados do Azure para servidores MariaDB ou mover para um SKU com otimização de memória
Uma taxa de acertos de cache baixa pode resultar em um desempenho de consulta mais lento e maior IOPS. Essa condição pode ser causada por um plano de consulta insatisfatório ou uma carga de trabalho com uso intensivo de memória. Corrigir o plano de consulta ou [aumentar a memória](../postgresql/concepts-pricing-tiers.md) do banco de dados do Azure para PostgreSQL, banco de dados do Azure para MySQL ou banco de dados do Azure para MariaDB Server ajudará a otimizar a execução da carga de trabalho do banco de dados. O Azure Advisor identifica servidores afetados por essa alta rotatividade de pool de buffers. Recomenda-se que você execute uma destas ações: 
- Corrigir o plano de consulta
- Mover para uma SKU que tenha mais memória 
- Aumente o tamanho do armazenamento para obter mais IOPS.

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Use uma réplica de leitura do banco de dados do Azure para MySQL ou banco de dados do Azure para PostgreSQL para escalar leituras de cargas de trabalho com uso intensivo de leitura
O Advisor usa heurística baseada em carga de trabalho como a taxa de leituras para gravações no servidor nos últimos sete dias para identificar cargas de trabalho com uso intensivo de leitura. Um banco de dados do Azure para PostgreSQL ou um recurso do banco de dados do Azure para MySQL com alta taxa de leitura/gravação pode resultar em contenções de CPU ou memória e levar a um desempenho de consulta lento. Adicionar uma [réplica](../postgresql/howto-read-replicas-portal.md) ajudará a expandir as leituras para o servidor de réplica e a impedir a CPU ou as restrições de memória no servidor primário. O Advisor identifica servidores com cargas de trabalho com uso intensivo de leitura e recomenda que você adicione uma [réplica de leitura](../postgresql/concepts-read-replicas.md) para descarregar algumas das cargas de trabalho de leitura.


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Dimensione seu banco de dados do Azure para MySQL, banco de dados do Azure para PostgreSQL ou banco de dados do Azure para MariaDB Server para uma SKU superior para evitar restrições de conexão
Cada nova conexão com o servidor de banco de dados ocupa memória. O desempenho do servidor de banco de dados degrada se as conexões com o servidor estão falhando devido a um [limite superior](../postgresql/concepts-limits.md) na memória. O Azure Advisor identifica servidores em execução com muitas falhas de conexão. Ele recomenda atualizar os limites de conexão do servidor para fornecer mais memória ao servidor executando uma destas ações:
- Escalar verticalmente a computação. 
- Use SKUs com otimização de memória, que têm mais computação por núcleo.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Dimensione seu cache para um tamanho ou SKU diferente para melhorar o desempenho do aplicativo e do cache

As instâncias de cache têm melhor desempenho quando não estão sendo executadas sob alta pressão de memória, alta carga de servidor ou alta largura de banda de rede. Essas condições podem fazer com que elas fiquem sem resposta, experimentem a perda de dados ou fiquem indisponíveis. O Advisor identifica as instâncias de cache nessas condições. Recomenda-se que você execute uma destas ações:
- Aplique as práticas recomendadas para reduzir a pressão de memória, a carga do servidor ou a largura de banda da rede.
- Dimensione para um tamanho ou SKU diferente que tenha mais capacidade.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Adicionar regiões com tráfego à sua conta de Azure Cosmos DB

O Advisor detecta contas de Azure Cosmos DB que têm tráfego de uma região que não está configurada no momento. Ele recomenda a adição dessa região. Isso melhora a latência de solicitações provenientes dessa região e garante a disponibilidade em caso de interrupções de região. [Saiba mais sobre a distribuição de dados globais com Azure Cosmos DB.](../cosmos-db/distribute-data-globally.md)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>Configurar a política de indexação de Azure Cosmos DB usando caminhos personalizados incluídos ou excluídos

O Advisor identifica Azure Cosmos DB contêineres que estão usando a política de indexação padrão, mas podem se beneficiar de uma política de indexação personalizada. Essa determinação se baseia no padrão de carga de trabalho. A política de indexação padrão indexa todas as propriedades. Uma política de indexação personalizada com caminhos explícitos incluídos ou excluídos usados em filtros de consulta pode reduzir o RUs e o armazenamento consumidos para indexação. [Saiba mais sobre como modificar políticas de índice.](../cosmos-db/index-policy.md)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Defina o tamanho da página de consulta de Azure Cosmos DB (MaxItemCount) como-1 

O supervisor do Azure identifica Azure Cosmos DB contêineres que estão usando um tamanho de página de consulta de 100. Ele recomenda usar um tamanho de página de-1 para verificações mais rápidas. [Saiba mais sobre o MaxItemCount.](../cosmos-db/sql-api-query-metrics.md)

## <a name="consider-using-accelerated-writes-feature-in-your-hbase-cluster-to-improve-cluster-performance"></a>Considere o uso do recurso de gravações aceleradas em seu cluster HBase para melhorar o desempenho do cluster
O Azure Advisor analisa os logs do sistema nos últimos sete dias e identifica se o cluster encontrou os seguintes cenários:
1. Latência de tempo alta de sincronização de WAL 
2. Alta contagem de solicitações de gravação (pelo menos 3 janelas de 1 hora acima de 1.000 avg_write_requests/second/node)

Essas condições são indicadores de que o cluster passa por altas latências de gravação. Isso pode ser devido à carga de trabalho pesada executada no cluster. Para melhorar o desempenho do cluster, talvez você queira considerar a utilização do recurso de gravações aceleradas fornecido pelo Azure HDInsight HBase. O recurso de gravações aceleradas para clusters do Apache HBase do HDInsight anexa discos gerenciados por SSD Premium a cada RegionServer (nó de trabalho) em vez de usar o armazenamento em nuvem. Como resultado, ele fornece baixa latência de gravação e maior resiliência para seus aplicativos. Para ler mais sobre esse recurso, [saiba mais](../hdinsight/hbase/apache-hbase-accelerated-writes.md#how-to-enable-accelerated-writes-for-hbase-in-hdinsight)

## <a name="review-azure-data-explorer-table-cache-period-policy-for-better-performance-preview"></a>Examinar o tempo de cache da tabela Data Explorer do Azure – período (política) para melhorar o desempenho (versão prévia)
Essa recomendação exibe as tabelas do Azure Data Explorer que têm um grande número de consultas abrangendo além do período de cache configurado (política) (você verá as 10 principais tabelas por porcentagem de consulta que acessam dados fora do cache). A ação recomendada para melhorar o desempenho do cluster: Limite as consultas nesta tabela ao intervalo de tempo mínimo necessário (dentro da política definida). Como alternativa, se os dados do intervalo de tempo inteiro forem necessários, aumente o período de cache para o valor recomendado.

## <a name="improve-performance-by-optimizing-mysql-temporary-table-sizing"></a>Melhorar o desempenho com a otimização do dimensionamento da tabela temporária do MySQL
A análise do Advisor indica que o servidor MySQL pode estar incorrendo em sobrecarga de e/s desnecessária devido a configurações de parâmetro de tabela temporária baixa. Isso pode causar transações desnecessárias no disco e redução de desempenho. Recomendamos aumentar os valores de parâmetro “tmp_table_size” e “max_heap_table_size” para reduzir o número de transações que usam o disco. [Saiba mais](https://aka.ms/azure_mysql_tmp_table)

## <a name="distribute-data-in-server-group-to-distribute-workload-among-nodes"></a>Distribuir os dados no grupo de servidores para distribuir a carga de trabalho entre os nós
O Advisor identifica os grupos de servidores nos quais os dados não foram distribuídos, mas permanecem no coordenador. Com base nesse processo, o Advisor recomenda que, para os benefícios de Citus (hiperescala completa), distribua dados em nós de trabalho para seus grupos de servidores. Isso melhorará o desempenho da consulta utilizando o recurso de cada nó no grupo de servidores. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=2135201) 

## <a name="improve-user-experience-and-connectivity-by-deploying-vms-closer-to-windows-virtual-desktop-deployment-location"></a>Melhore a experiência e a conectividade do usuário implantando VMs mais próximas ao local de implantação da área de trabalho virtual do Windows
Determinamos que suas VMs estão localizadas em uma região diferente ou longe de onde os usuários estão se conectando, usando a Área de Trabalho Virtual do Windows (WVD). Isso pode levar a tempos de resposta de conexão prolongados e afetará a experiência do usuário em geral na WVD. Ao criar VMs para os pools de host, você deve tentar usar uma região mais próxima do usuário. Ter uma proximidade maior garante a satisfação contínua com o serviço da WVD e uma melhor qualidade geral da experiência. [Saiba mais sobre a latência de conexão aqui](../virtual-desktop/connection-latency.md).

## <a name="upgrade-to-the-latest-version-of-the-immersive-reader-sdk"></a>Atualizar para a versão mais recente do SDK de Leitura Avançada
Identificamos os recursos nessa assinatura que usam versões desatualizadas do SDK de Leitura Avançada. Usar a versão mais recente do SDK de Leitura Avançada fornece segurança atualizada, desempenho e um conjunto expandido de recursos para personalizar e aprimorar a experiência de integração.
Saiba mais sobre o [SDK do leitor de imersão](../cognitive-services/immersive-reader/index.yml).

## <a name="improve-vm-performance-by-changing-the-maximum-session-limit"></a>Melhorar o desempenho da VM alterando o limite máximo da sessão

O Advisor detecta que você tem um pool de hosts que tem profundidade primeiro definido como o algoritmo de balanceamento de carga e que o limite máximo de sessão do pool de hosts é maior ou igual a 999999. O balanceamento de carga em profundidade usa o limite máximo de sessão para determinar o número máximo de usuários que podem ter sessões simultâneas em um único host de sessão. Se o limite máximo de sessão for muito alto, todas as sessões de usuário serão direcionadas para o mesmo host de sessão e isso causará problemas de desempenho e confiabilidade. Portanto, ao definir um pool de hosts para ter o primeiro balanceamento de carga, você deve definir um limite de sessão máximo apropriado de acordo com a configuração de sua implantação e capacidade de suas VMs. 

Para saber mais sobre o balanceamento de carga na área de trabalho virtual do Windows, consulte [Configurar o método de balanceamento de carga de área de trabalho virtual do Windows](../virtual-desktop/troubleshoot-set-up-overview.md).

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Como acessar as recomendações de desempenho no Advisor

1. Entre no [Portal do Azure](https://portal.azure.com) e, em seguida, abra o [Assistente](https://aka.ms/azureadvisordashboard).

2.  No painel do Advisor, selecione a guia **desempenho** .

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as recomendações do Assistente, consulte:

* [Introdução ao Advisor](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Pontuação do supervisor](azure-advisor-score.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de confiabilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md)
* [API REST do Advisor](/rest/api/advisor/)