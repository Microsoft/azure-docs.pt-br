---
title: Limites de recursos do servidor do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos limites de recursos do servidor do Banco de Dados SQL do Azure para bancos de dados individuais e pools elásticos. Também fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou excedidos.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
manager: craigg
ms.date: 04/18/2019
ms.openlocfilehash: 4e4c0a6cd25587b33c06526b57e6acdbebb69c8b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445637"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Limites de recursos de Banco de Dados SQL para servidor do Banco de Dados SQL do Azure

Este artigo fornece uma visão geral dos limites de recursos do Banco de Dados SQL para um servidor do Banco de Dados SQL que gerencia bancos de dados individuais e pools elásticos. Também fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou excedidos.

> [!NOTE]
> Para limites de Instâncias Gerenciadas, confira [Limites de recursos do Banco de Dados SQL para Instâncias Gerenciadas](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limites máximos de recursos

| Recurso | Limite |
| :--- | :--- |
| Bancos de dados por servidor | 5\.000 |
| Número padrão de servidores por assinatura por região | 20 |
| Número máximo padrão de servidores por assinatura por região | 200 |  
| DTU / cota de eDTU por servidor | 54.000 |  
| Cota de vCore por servidor/instância | 540 |
| Pools de máx por servidor | Limitado pelo número de DTUs ou vCores. Por exemplo, se cada pool tiver 1.000 DTUs, um servidor poderá dar suporte a 54 pools.|
|||

> [!NOTE]
> Para obter mais cotas DTU / eDTU, vCore ou mais servidores do que o valor padrão, uma nova solicitação de suporte pode ser enviada no portal do Azure para a assinatura com o tipo de problema "Cota". O DTU / eDTU limite de cota e o banco de dados por servidor restringe o número de pools Elásticos por servidor.
> [!IMPORTANT]
> Conforme o número de bancos de dados vai se aproximando do limite por servidor do Banco de Dados SQL, pode ocorrer o seguinte:
> - Aumento de latência nas consultas em execução no banco de dados mestre.  Isso inclui modos de exibição de estatísticas de utilização de recursos, como sys.resource_stats.
> - Aumento de latência nas operações de gerenciamento e pontos de vista do portais de renderização que envolvem a enumeração de bancos de dados no servidor.

### <a name="storage-size"></a>Tamanho do armazenamento
- Para bancos de dados únicos rources, consulte o [limites de recurso baseado em DTU](sql-database-dtu-resource-limits-single-databases.md) ou [limites de recurso baseado em vCore](sql-database-vcore-resource-limits-single-databases.md) para os limites de tamanho de armazenamento por tipo de preço.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>O que acontece quando os limites de recursos do banco de dados são atingidos

### <a name="compute-dtus-and-edtus--vcores"></a>Computação (DTUs e eDTUs/ vCores)

Quando a utilização de computação de banco de dados (medida por DTUs e eDTUs ou vCores) torna-se alta, a latência da consulta aumenta e pode até mesmo atingir o tempo limite. Sob essas condições, as consultas poderão ser colocadas em fila pelo serviço e receberão recursos para execução à medida que os recursos forem liberados.
Ao encontrar uma utilização alta de computação, as opções de atenuação incluem:

- Aumentar o tamanho da computação do banco de dados ou do pool elástico a fim de fornecer ao banco de dados mais recursos de computação. Consulte [limites de recursos do banco de dados individual em escala](sql-database-single-database-scale.md) e [limites de recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta. Para saber mais, consulte [Ajuste/Dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Armazenamento

Quando o espaço usado do banco de dados atingir o limite de tamanho máximo, inserções e atualizações do banco de dados que aumentam o tamanho dos dados falharão, e os clientes receberão uma [mensagem de erro](sql-database-develop-error-messages.md). Os SELECTS e DELETES do banco de dados continuarão a ter êxito.

Ao encontrar uma utilização alta de espaço, as opções de atenuação incluem:

- Aumentar o tamanho máximo do banco de dados ou pool elástico ou adicione mais armazenamento. Consulte [limites de recursos do banco de dados individual em escala](sql-database-single-database-scale.md) e [limites de recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Se o banco de dados estiver em um pool elástico, o banco de dados poderá ser movido para fora do pool, de modo que seu espaço de armazenamento não seja compartilhado com outros bancos de dados.
- Encolher um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [Gerenciar espaço no arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessões e trabalhos (solicitações)

O número máximo de sessões e trabalhos é determinado pela camada de serviço e tamanho da computação (DTUs e eDTUs). Novas solicitações serão rejeitadas quando os limites de sessão ou de trabalho forem atingidos, e os clientes receberão uma mensagem de erro. Embora o número de conexões disponíveis possa ser controlado pelo aplicativo, o número de trabalhos simultâneos costuma ser mais difícil de ser estimado e controlado. Isso é especialmente verdadeiro durante os períodos de pico de carga, quando os limites de recurso de banco de dados são atingidos e os trabalhos acumulam devido a consultas de execução mais longas.

Ao encontrar uma utilização alta de sessão ou trabalho, as opções de atenuação incluem:

- Aumentar a camada de serviço ou o tamanho da computação do banco de dados ou do pool elástico. Consulte [limites de recursos do banco de dados individual em escala](sql-database-single-database-scale.md) e [limites de recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta se a causa do aumento da utilização de trabalho for devido à contenção de recursos de computação. Para saber mais, consulte [Ajuste/Dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="transaction-log-rate-governance"></a>Governança de taxa de Log de transação 
Governança de taxa de log de transações é um processo no banco de dados SQL do Azure usada para limitar as taxas de ingestão de alta para cargas de trabalho como bulk insert, SELECT INTO e compilações de índice. Esses limites são rastreados e aplicados no nível de subsegundos para a taxa de geração de registro de log, limitação de taxa de transferência, independentemente de quantos IOs pode ser emitida em relação aos arquivos de dados.  Taxas de geração de log de transações atualmente aumentam linearmente até um ponto que é dependente de hardware, com o máximo do log de taxa de permissão que está sendo 96 MB/s com o modelo de compra de vCore. 

> [!NOTE]
> O IOs físico real para arquivos de log de transações não é regido ou limitado. 

As taxas de log são definidas, de modo que pode ser obtidas e mantidos em uma variedade de cenários, enquanto o sistema geral pode manter sua funcionalidade com menor impacto à carga de usuário. Governança de taxa de log garante que backups permanecem dentro a recuperabilidade publicada SLAs do log de transações.  Esse controle também impede que uma lista de pendências excessiva em réplicas secundárias.

Conforme os registros de log são gerados, cada operação é avaliada e avaliada por se ele deve ser atrasado para manter uma taxa de log desejado máximo (MB/s por segundo). Os atrasos não são adicionados quando os registros de log são liberados para o armazenamento, em vez disso, governança de taxa de log é aplicada durante a geração de taxa de log em si.

A geração de log real taxas de imposto em tempo de execução também podem ser influenciadas por mecanismos de comentários, reduzindo temporariamente as taxas de log permitido para que o sistema pode se estabilizar. Gerenciamento de espaço do arquivo de log, evitando ficando em condições de espaço de log e o grupo de disponibilidade mecanismos de replicação temporariamente pode diminuir os limites do sistema geral. 

Modelagem de tráfego do administrador de taxa de log será exposto por meio dos seguintes tipos de espera (exposto na [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| Tipo de espera | Observações |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Limitação do banco de dados |
| POOL_LOG_RATE_GOVERNOR | Limitação de pool |
| INSTANCE_LOG_RATE_GOVERNOR | Limitação de nível de instância |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Controle de comentários, replicação física do grupo de disponibilidade no Premium/comercialmente crítico não manter |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Controle de comentários, limitando as taxas para evitar uma condição de espaço de log de insuficiente |
|||

Ao encontrar um limite de taxa de log que está limitando a escalabilidade desejada, considere as seguintes opções:
- Escalar verticalmente para uma camada maior de fim de obter a taxa máxima de log 96 MB/s. 
- Se os dados que está sendo carregados forem transitórios, ou seja, a preparação de dados em um processo ETL, pode ser carregado em tempdb (que é minimamente registrada). 
- Para cenários analíticos, carregue em uma tabela columnstore clusterizada coberto. Isso reduz a taxa de log necessários devido à compactação. Essa técnica aumente a utilização da CPU e só é aplicável aos conjuntos de dados que se beneficiam de índices columnstore clusterizados. 

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Para saber mais sobre DTUs e eDTUs, confira [DTUs e eDTUs](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Para obter informações sobre limites de tamanho de tempdb, consulte [TempDB no Banco de Dados SQL do Azure](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
