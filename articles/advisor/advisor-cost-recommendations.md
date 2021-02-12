---
title: Reduza os custos de serviço usando o Assistente do Azure
description: Use o Azure Advisor para otimizar o custo de suas implantações do Azure.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 767c82c818535304552c1c98ff50a252acc0d08a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458518"
---
# <a name="reduce-service-costs-by-using-azure-advisor"></a>Reduzir os custos de serviço usando o Azure Advisor

O assistente do Azure ajuda você a otimizar e reduzir o gasto geral do Azure identificando recursos ociosos e subutilizados. Você pode obter recomendações de custo na guia **Custo** no painel do Assistente.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Otimizar o gasto da máquina virtual redimensionando ou desligando instâncias subutilizadas 

Embora alguns cenários de aplicativos possam resultar na baixa utilização por design, normalmente, é possível economizar dinheiro gerenciando o tamanho e o número de máquinas virtuais. 

As ações recomendadas são desligadas ou redimensionadas, específicas ao recurso que está sendo avaliado.

O modelo de avaliação avançada do Advisor considera o desligamento de máquinas virtuais quando todas essas instruções são verdadeiras: 
- P95th do máximo do valor máximo da utilização da CPU é inferior a 3%. 
- A utilização da rede é inferior a 2% em um período de sete dias.
- A pressão de memória é inferior aos valores de limite

O Advisor considera o redimensionamento de máquinas virtuais quando é possível ajustar a carga atual em uma SKU menor (dentro da mesma família de SKUs) ou um número menor de instâncias, de modo que:
- A carga atual não fica acima de 80% de utilização para cargas de trabalho que não são voltadas para o usuário. 
- A carga não vai acima de 40% para cargas de trabalho voltadas para o usuário. 

Aqui, o Advisor determina o tipo de carga de trabalho analisando as características de utilização da CPU da carga de trabalho.

O Advisor mostra a economia de custos estimada para uma das ações recomendadas: redimensionar ou desligar. Para redimensionamento, o Advisor fornece informações de SKU atuais e de destino.

Se você quiser ser mais agressivo ao identificar máquinas virtuais subutilizadas, você pode ajustar a regra de utilização da CPU por assinatura.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>Otimizar o gasto para servidores MariaDB, MySQL e PostgreSQL por meio do dimensionamento correto 
O Advisor analisa seu uso e avalia se seus recursos de servidor de banco de dados MariaDB, MySQL ou PostgreSQL foram subutilizados por um longo período nos últimos sete dias. A baixa utilização de recursos resulta em despesas indesejadas que você pode corrigir sem impacto significativo no desempenho. Para reduzir os custos e gerenciar seus recursos com eficiência, recomendamos que você reduza o tamanho da computação (vCores) pela metade.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Reduzir os custos eliminando circuitos do ExpressRoute não provisionados

O Advisor identifica os circuitos do Azure ExpressRoute que estão no status do provedor de **não provisionado** por mais de um mês. Ele recomenda a exclusão do circuito se você não estiver planejando provisionar o circuito com seu provedor de conectividade.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Reduzir os custos excluindo ou reconfigurando gateways de rede virtual ociosos

O Advisor identifica os gateways de rede virtual que ficaram ociosos por mais de 90 dias. Como esses gateways são cobrados por hora, você deve considerar reconfigurá-los ou excluí-los se não pretende mais usá-los. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Compre instâncias de máquina virtual reservadas para economizar nos custos de pagamento conforme o uso

O Advisor revisa o uso da sua máquina virtual nos últimos 30 dias para determinar se você pode economizar dinheiro comprando uma reserva do Azure. O Advisor mostra as regiões e os tamanhos onde o potencial de economia é mais alto e a economia estimada de reservas de compra. Com as reservas do Azure, você pode comprar previamente os custos de base para as máquinas virtuais. Os descontos se aplicam automaticamente a VMs novas ou existentes que têm o mesmo tamanho e região que suas reservas. [Saiba mais sobre as Instâncias de VM Reservadas do Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/)

O Advisor também notifica você sobre suas instâncias reservadas que expirarão nos próximos 30 dias. Recomenda-se que você compre novas instâncias reservadas para evitar o preço pago conforme o uso.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>Comprar instâncias reservadas para vários tipos de recursos para economizar nos custos pagos conforme o uso

O Advisor analisa os padrões de uso dos últimos 30 dias para os seguintes recursos e recomenda as compras de capacidade reservada que otimizam os custos.

### <a name="azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB capacidade reservada
O Advisor analisa seus padrões de uso Azure Cosmos DB nos últimos 30 dias e recomenda as compras de capacidade reservada para otimizar os custos. Usando a capacidade reservada, você pode adquirir previamente Azure Cosmos DB uso por hora e economizar os custos pagos conforme o uso. A capacidade reservada é um benefício de cobrança e se aplica automaticamente a implantações novas e existentes. O Advisor calcula estimativas de economias para assinaturas individuais usando preços de reserva de 3 anos e extrapolando os padrões de uso observados nos últimos 30 dias. As recomendações de escopo compartilhado estão disponíveis para compras de capacidade reservada e podem aumentar a economia.

### <a name="sql-database-and-sql-managed-instance-reserved-capacity"></a>Capacidade reservada do banco de dados SQL e SQL Instância Gerenciada
O Advisor analisa os padrões de uso do banco de dados SQL e do SQL Instância Gerenciada nos últimos 30 dias. Em seguida, ele recomenda as compras de capacidade reservada que otimizam os custos. Usando a capacidade reservada, você pode pré-configurar o uso por hora do BD SQL e economizar os custos de computação do SQL. Sua licença do SQL é cobrada separadamente e não é descontada pela reserva. A capacidade reservada é um benefício de cobrança e se aplica automaticamente a implantações novas e existentes. O Advisor calcula estimativas de economias para assinaturas individuais usando preços de reserva de 3 anos e extrapolando os padrões de uso observados nos últimos 30 dias. As recomendações de escopo compartilhado estão disponíveis para compras de capacidade reservada e podem aumentar a economia. Para obter detalhes, confira [banco de dados SQL do Azure & capacidade reservada do sql instância gerenciada](../azure-sql/database/reserved-capacity-overview.md).

### <a name="app-service-stamp-fee-reserved-capacity"></a>Capacidade reservada da taxa de selo do serviço de aplicativo
O Advisor analisa o padrão de uso de taxas de selo para o ambiente isolado do serviço de Azure App nos últimos 30 dias e recomenda as compras de capacidade reservada que otimizam os custos. Usando a capacidade reservada, você pode adquirir previamente o uso por hora da taxa de carimbo de ambiente isolado e economizar os custos pagos conforme o uso. Observe que a capacidade reservada se aplica somente à taxa de carimbo e não às instâncias do serviço de aplicativo. A capacidade reservada é um benefício de cobrança e se aplica automaticamente a implantações novas e existentes. O Advisor calcula as estimativas de salvamento de assinaturas individuais usando preços de reserva de 3 anos com base nos padrões de uso nos últimos 30 dias.

### <a name="blob-storage-reserved-capacity"></a>Capacidade reservada do armazenamento de BLOBs
O Advisor analisa o armazenamento de BLOBs do Azure e Azure Data Lake o uso do armazenamento nos últimos 30 dias. Em seguida, ele calcula as compras de capacidade reservada que otimizam os custos. Com a capacidade reservada, você pode adquirir previamente o uso por hora e economizar em seus custos atuais sob demanda. A capacidade reservada do armazenamento de BLOBs aplica-se somente aos dados armazenados em contas de uso geral V2 e Azure Data Lake Storage Gen2 do blob do Azure. A capacidade reservada é um benefício de cobrança e se aplica automaticamente a implantações novas e existentes. O Advisor calcula estimativas de economias para assinaturas individuais usando preços de reserva de 3 anos e os padrões de uso observados nos últimos 30 dias. As recomendações de escopo compartilhado estão disponíveis para compras de capacidade reservada e podem aumentar a economia.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>Capacidade reservada do MariaDB, MySQL e PostgreSQL
O Advisor analisa seus padrões de uso para o banco de dados do Azure para MariaDB, o banco de dados do Azure para MySQL e o banco de dados do Azure para PostgreSQL nos últimos 30 dias. Em seguida, ele recomenda as compras de capacidade reservada que otimizam os custos. Usando a capacidade reservada, você pode pré-configurar o uso de MariaDB, MySQL e PostgreSQL por hora e economizar em seus custos atuais. A capacidade reservada é um benefício de cobrança e se aplica automaticamente a implantações novas e existentes. O Advisor calcula estimativas de economias para assinaturas individuais usando preços de reserva de 3 anos e os padrões de uso observados nos últimos 30 dias. As recomendações de escopo compartilhado estão disponíveis para compras de capacidade reservada e podem aumentar a economia.

### <a name="azure-synapse-analytics-reserved-capacity"></a>Capacidade reservada do Azure Synapse Analytics
O Advisor analisa os padrões de uso da análise de Synapse do Azure nos últimos 30 dias e recomenda as compras de capacidade reservada que otimizam os custos. Usando a capacidade reservada, você pode adquirir previamente a análise de Synapse de uso por hora e economizar seus custos sob demanda. A capacidade reservada é um benefício de cobrança e se aplica automaticamente a implantações novas e existentes. O Advisor calcula estimativas de economias para assinaturas individuais usando preços de reserva de 3 anos e os padrões de uso observados nos últimos 30 dias. As recomendações de escopo compartilhado estão disponíveis para compras de capacidade reservada e podem aumentar a economia.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Exclua endereços IP públicos não associados para economizar dinheiro

O Advisor identifica os endereços IP públicos que não estão associados aos recursos do Azure, como balanceadores de carga e VMs. Um encargo nominal é associado a esses endereços IP públicos. Se você não planeja usá-los, poderá economizar dinheiro excluindo-os.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Excluir os pipelines do Azure Data Factory que estão falhando

O Advisor detecta Azure Data Factory pipelines que falham repetidamente. Ele recomenda que você resolva os problemas ou exclua os pipelines se não precisar deles. Você será cobrado por esses pipelines, mesmo se eles não estiverem atendendo a você enquanto eles estiverem falhando.

## <a name="use-standard-snapshots-for-managed-disks"></a>Usar instantâneos padrão para discos gerenciados
Para economizar 60% do custo, é recomendável armazenar seus instantâneos no armazenamento padrão, independentemente do tipo de armazenamento do disco pai. Essa opção é a opção padrão para instantâneos de disco gerenciado. O Advisor identifica os instantâneos armazenados no armazenamento Premium e recomenda a migração do armazenamento Premium para Standard. [Saiba mais sobre os preços do disco gerenciado.](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="use-lifecycle-management"></a>Usar o gerenciamento do ciclo de vida
Usando a inteligência sobre a contagem de objetos do armazenamento de BLOBs do Azure, o tamanho total e as transações, o Advisor detecta se você deve habilitar o gerenciamento do ciclo de vida para dados de camada em uma ou mais de suas contas de armazenamento. Ele solicita que você crie regras de gerenciamento de ciclo de vida para hierarquizar automaticamente seus dados para o armazenamento frio ou de arquivo para otimizar os custos de armazenamento e, ao mesmo tempo, manter seus dados no armazenamento de BLOBs do Azure para compatibilidade de aplicativos.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Criar uma recomendação de disco do SO efêmero
O [disco do so efêmero](../virtual-machines/ephemeral-os-disks.md) permite que você: 
- Economize em custos de armazenamento para discos do sistema operacional. 
- Obter latência de leitura/gravação inferior para discos do sistema operacional. 
- Obtenha operações de reimagem de VM mais rápidas redefinindo o sistema operacional (e o disco temporário) para seu estado original.

É preferível usar o disco do so efêmero para VMs IaaS de curta duração ou VMs com cargas de trabalho sem estado. O Advisor fornece recomendações para recursos que podem se beneficiar do disco do sistema operacional efêmero.

## <a name="reduce-azure-data-explorer-table-cache-period-policy-for-cluster-cost-optimization-preview"></a>Reduzir o período de cache da tabela de Data Explorer do Azure (política) para otimização de custo do cluster (versão prévia)
O Advisor identifica os recursos em que a redução da política de cache de tabela liberará os nós de cluster Data Explorer do Azure com baixa utilização da CPU, memória e uma configuração de tamanho de cache alto.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Como acessar as recomendações de custo no Azure Advisor

1. Entre no [portal do Azure](https://portal.azure.com).

1. Pesquise e selecione [**Advisor**](https://aka.ms/azureadvisordashboard) em qualquer página.

1. No painel do **Advisor** , selecione a guia **custo** .

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as recomendações do Assistente, consulte:
* [Introdução ao Advisor](advisor-overview.md)
* [Pontuação do supervisor](azure-advisor-score.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md)