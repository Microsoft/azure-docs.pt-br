---
title: 'SQL Server Instância Gerenciada SQL: análise de desempenho'
description: Aprenda a criar e comparar uma linha de base de desempenho ao migrar seus bancos de dados SQL Server para o SQL Instância Gerenciada do Azure.
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: 2b6f9bef445cc07a3aa00377b41010a536855935
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358853"
---
# <a name="migration-performance-sql-server-to-sql-managed-instance-performance-analysis"></a>Desempenho da migração: SQL Server para a análise de desempenho do SQL Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Crie uma linha de base de desempenho para comparar o desempenho de sua carga de trabalho em um Instância Gerenciada SQL com sua carga de trabalho original em execução no SQL Server. 

## <a name="create-a-baseline"></a>Criar uma linha de base

O ideal é que o desempenho seja semelhante ou melhor após a migração, portanto, é importante medir e registrar os valores de desempenho de linha de base na origem e, em seguida, compará-los com o ambiente de destino. Uma linha de base de desempenho é um conjunto de parâmetros que definem a carga de trabalho média em sua fonte. 

Selecione um conjunto de consultas que são importantes para o e o representativo de sua carga de trabalho de negócios. Meça e documente a duração mínima/média/máxima e a utilização da CPU para essas consultas, bem como as métricas de desempenho no servidor de origem, como o uso médio/máximo da CPU, latência média/máxima de e/s de disco, taxa de transferência, IOPS, expectativa de vida da página média/máxima e tamanho máximo médio de tempdb. 

Os recursos a seguir podem ajudar a definir uma linha de base de desempenho: 

   - [Monitorar o uso da CPU ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [Monitorar o uso](/sql/relational-databases/performance-monitor/monitor-memory-usage)   de memória e determinar a quantidade de memória usada por diferentes componentes, como pool de buffers, cache de planos, pool de repositório de coluna, [OLTP na memória](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage), etc. Além disso, você deve encontrar os valores médio e de pico do contador de desempenho de memória expectativa de vida da página. 
   - Monitore o uso de e/s do disco na instância de SQL Server de origem usando a exibição [Sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)   ou os [contadores de desempenho](/sql/relational-databases/performance-monitor/monitor-disk-usage). 
   - Monitore a carga de trabalho e o desempenho de consulta examinando exibições de gerenciamento dinâmico (ou Repositório de Consultas se você estiver migrando do SQL Server 2016 e posterior). Identifique a duração média e o uso da CPU das consultas mais importantes em sua carga de trabalho. 

Quaisquer problemas de desempenho no SQL Server de origem devem ser resolvidos antes da migração. A migração de problemas conhecidos para qualquer novo sistema pode causar resultados inesperados e invalidar qualquer comparação de desempenho. 


## <a name="compare-performance"></a>Comparar o desempenho 

Depois de definir uma linha de base, compare o desempenho de carga de trabalho semelhante no SQL de destino Instância Gerenciada. Para exatidão, é importante que o ambiente do SQL Instância Gerenciada seja comparável ao ambiente de SQL Server o máximo possível. 

Há diferenças de infraestrutura do SQL Instância Gerenciada que tornam o desempenho correspondente exatamente improvável. Algumas consultas podem ser executadas mais rápido do que o esperado, enquanto outras podem ser mais lentas. O objetivo dessa comparação é verificar se o desempenho da carga de trabalho na instância gerenciada corresponde ao desempenho em SQL Server (em média) e para identificar quaisquer consultas críticas com desempenho que não correspondam ao seu desempenho original. 

A comparação de desempenho provavelmente resultará nos seguintes resultados: 

- O desempenho da carga de trabalho na instância gerenciada é alinhado ou melhor do que o desempenho da carga de trabalho no SQL Server de origem. Nesse caso, você confirmou com êxito que a migração foi bem-sucedida. 

- A maioria dos parâmetros de desempenho e das consultas na carga de trabalho é executada conforme o esperado, com algumas exceções, resultando em desempenho degradado. Nesse caso, identifique as diferenças e sua importância. Se houver algumas consultas importantes com desempenho degradado, investigue se os planos SQL subjacentes foram alterados ou se as consultas estão atingindo os limites de recursos. Você pode mitigar isso aplicando algumas dicas sobre consultas críticas (por exemplo, alterar o nível de compatibilidade, avaliador de cardinalidade herdado) diretamente ou usando guias de plano. Certifique-se de que estatísticas e índices estejam atualizados e equivalentes em ambos os ambientes. 

- A maioria das consultas é mais lenta em uma instância gerenciada em comparação com a instância de SQL Server de origem. Nesse caso, tente identificar as causas raiz da diferença, como [atingir um limite de recursos](../../managed-instance/resource-limits.md#service-tier-characteristics) , como os limites de taxa de e/s, memória ou log de instância. Se não houver nenhum limite de recurso causando a diferença, tente alterar o nível de compatibilidade do banco de dados ou alterar as configurações do banco de dados como estimativa de cardinalidade herdada e execute o teste novamente. Examine as recomendações fornecidas pela instância gerenciada ou Repositório de Consultas exibições para identificar as consultas com desempenho regressivo. 

O SQL Instância Gerenciada tem um recurso interno de correção de plano automático que está habilitado por padrão. Esse recurso garante que as consultas que funcionaram bem no passado não sejam prejudicadas no futuro. Se esse recurso não estiver habilitado, execute a carga de trabalho com as configurações antigas para que o SQL Instância Gerenciada possa aprender a linha de base de desempenho. Em seguida, habilite o recurso e execute a carga de trabalho novamente com as novas configurações. 

Faça alterações nos parâmetros do teste ou atualize para camadas de serviço mais altas para alcançar a configuração ideal para o desempenho da carga de trabalho que atenda às suas necessidades. 

## <a name="monitor-performance"></a>Monitorar desempenho 

O SQL Instância Gerenciada fornece ferramentas avançadas para monitoramento e solução de problemas, e você deve usá-las para monitorar o desempenho em sua instância do. Algumas das principais métricas a serem monitoradas são: 

- Uso da CPU na instância para determinar se o número de vCores que você provisionou é a correspondência correta para sua carga de trabalho. 
- Expectativa de vida da página em sua instância gerenciada para determinar se você precisa de [memória adicional](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444).
-  Estatísticas como INSTANCE_LOG_GOVERNOR ou PAGEIOLATCH que identificam problemas de e/s de armazenamento, especialmente na camada de Uso Geral, em que talvez seja necessário alocar previamente os arquivos para obter um desempenho de e/s melhor. 


## <a name="considerations"></a>Considerações  

Ao comparar o desempenho, considere o seguinte: 

- As configurações correspondem entre a origem e o destino. Valide se várias configurações de instância, banco de dados e tempdb são equivalentes entre os dois ambientes. As diferenças na configuração, nos níveis de compatibilidade, nas configurações de criptografia, nos sinalizadores de rastreamento, etc., podem ter o desempenho de distorção. 

- O armazenamento é configurado de acordo com [as práticas recomendadas](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525). Por exemplo, para Uso Geral, talvez seja necessário alocar previamente o tamanho dos arquivos para melhorar o desempenho. 

- Há [importantes diferenças de ambiente](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) que podem causar as diferenças de desempenho entre uma instância gerenciada e SQL Server. Identifique os riscos relevantes para o seu ambiente que possam contribuir para um problema de desempenho. 

- O repositório de consultas e o ajuste automático devem ser habilitados no seu Instância Gerenciada SQL, pois eles ajudam a medir o desempenho da carga de trabalho e a mitigar automaticamente os possíveis problemas de desempenho. 



## <a name="next-steps"></a>Próximas etapas

Para obter mais informações para otimizar seu novo ambiente de Instância Gerenciada SQL do Azure, consulte os seguintes recursos: 

- [Como identificar por que o desempenho da carga de trabalho no Azure SQL Instância Gerenciada é diferente de SQL Server?](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [Principais causas de diferenças de desempenho entre o SQL Instância Gerenciada e SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Práticas recomendadas de desempenho de armazenamento e considerações para o Azure SQL Instância Gerenciada (Uso Geral)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Monitoramento de desempenho em tempo real para o Azure SQL Instância Gerenciada (isso é arquivado, esse é o destino pretendido?)](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)