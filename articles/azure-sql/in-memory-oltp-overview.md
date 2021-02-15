---
title: Tecnologias na memória
description: As tecnologias na memória melhoram muito o desempenho de cargas de trabalho transacionais e de análise no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-db-mi
ms.subservice: ''
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: 48b74a5507eb4a1d48b7bf70133e476a30fe8169
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779944"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-azure-sql-database-and-azure-sql-managed-instance"></a>Otimizar o desempenho usando tecnologias na memória no banco de dados SQL do Azure e Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

As tecnologias na memória permitem melhorar o desempenho do seu aplicativo e, potencialmente, reduzir o custo do seu banco de dados.

## <a name="when-to-use-in-memory-technologies"></a>Quando usar tecnologias na memória

Usando tecnologias na memória, você pode obter melhorias de desempenho com várias cargas de trabalho:

- **Transacional** (OLTP (processamento transacional online)) em que a maioria das solicitações lê ou atualiza o menor conjunto de dados (por exemplo, operações de CRUD).
- **Analítica** (OLAP (processamento analítico online)) em que a maioria das consultas tem cálculos complexos para fins de relatório, com um determinado número de consultas que carregam e acrescentam dados a tabelas existentes (denominadas carregamento em massa) ou excluem os dados das tabelas.
- **Mista** (HTAP (processamento analítico/transacional híbrido)) em que as consultas OLTP e OLAP são executadas no mesmo conjunto de dados.

Tecnologias In-Memory podem melhorar o desempenho dessas cargas de trabalho, mantendo os dados que devem ser processados na memória, usando a compilação nativa das consultas, ou processamento avançado, tal como processamento em lotes e instruções SIMD que estão disponíveis no hardware subjacente.

## <a name="overview"></a>Visão geral

O banco de dados SQL do Azure e o Azure SQL Instância Gerenciada têm as seguintes tecnologias na memória:

- *[OLTP In-Memory](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* aumenta o número de transações por segundo o e reduz a latência para o processamento de transações. Os cenários que se beneficiam do OLTP In-Memory são: processamento de transações de alta taxa de transferência, como comércio e jogos, ingestão de dados de eventos ou dispositivos IoT, cache, carregamento de dados e cenários de variáveis de tabela e tabelas temporárias.
- Os *índices columnstore clusterizados* reduzem seu volume de armazenamento (em até 10 vezes) e melhoram o desempenho de relatórios e consultas de análise. Você pode usá-lo com tabelas de fatos em data marts para colocar mais dados no banco de dados e melhorar o desempenho. Além disso, também é possível usá-lo com os dados históricos no banco de dados operacional para arquivar e conseguir consultar até 10 vezes mais dados.
- *Índices columnstore não clusterizados* para HTAP ajudam a obter análises em tempo real sobre seus negócios consultando o banco de dados operacional diretamente, sem a necessidade de executar um processo ETL (extração, transformação e carregamento) caro e aguardar o data warehouse ser populado. Índices columnstore não clusterizados permitem a execução rápida das consultas de análise no banco de dados OLTP, enquanto reduzem o impacto sobre a carga de trabalho operacional.
- *Índices columnstore clusterizados com otimização de memória* para HTAP permitem a você executar o processamento de transações com muita rapidez e executar *simultaneamente* consultas de análise muito rapidamente sobre os mesmos dados.

Tanto os índices columnstore quanto o OLTP In-Memory integram o produto SQL Server desde 2012 e 2014, respectivamente. O banco de dados SQL do Azure, o Azure SQL Instância Gerenciada e o SQL Server compartilham a mesma implementação de tecnologias na memória.

## <a name="benefits-of-in-memory-technology"></a>Benefícios da tecnologia na memória

Devido ao processamento mais eficiente de consulta e transação, as tecnologias na memória também ajudam a reduzir o custo. Você normalmente não precisa atualizar o tipo de preço do banco de dados para obter ganhos de desempenho. Em alguns casos, você pode até mesmo ser capaz de reduzir o tipo de preço e ainda ver melhorias de desempenho com tecnologias na memória.

Estes são dois exemplos de como o OLTP In-Memory ajudou a melhorar significativamente o desempenho:

- Usando o OLTP In-Memory, a [Quorum Business Solutions foi capaz de duplicar a carga de trabalho, melhorando as DTUs em 70%](https://resources.quorumsoftware.com/case-studies/quorum-doubles-key-database-s-workload-while-lowering-dtu).
- O vídeo a seguir demonstra uma melhoria significativa no consumo de recursos com uma carga de trabalho de exemplo: [vídeo OLTP em memória](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB). Para obter mais informações, consulte a postagem [no blog: OLTP na memória](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> As tecnologias na memória estão disponíveis nas camadas Premium e Comercialmente Crítico.

O vídeo a seguir explica possíveis ganhos de desempenho com tecnologias na memória. Lembre-se de que o ganho de desempenho que você verá sempre dependerá de diversos fatores, incluindo a natureza da carga de trabalho e dos dados, o padrão de acesso do banco de dados e assim por diante.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>

Este artigo descreve aspectos de In-Memory os índices de OLTP e columnstore específicos para o banco de dados SQL do Azure e o SQL Instância Gerenciada do Azure e também inclui exemplos:

- Você verá o impacto dessas tecnologias no armazenamento e dos limites de tamanho dos dados.
- Você verá como gerenciar a movimentação dos bancos de dados que utilizam essas tecnologias entre os diferentes tipos de preço.
- Você verá dois exemplos que ilustram o uso de In-Memory OLTP, bem como os índices columnstore.

Para obter mais informações sobre na memória no SQL Server, consulte:

- [Visão geral e cenários de uso do OLTP In-Memory](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (incluindo referências a estudos de caso de cliente e informações para começar)
- [Documentação para OLTP in-memory](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Guia de Índices columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview)
- HTAP (Processamento Transacional e Analítico Híbrido), também conhecido como [análise operacional em tempo real](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

## <a name="in-memory-oltp"></a>OLTP na memória

In-Memory tecnologia OLTP fornece operações de acesso a dados extremamente rápidas, mantendo todos os dados na memória. Ela também usa índices especializados, compilação nativa de consultas e acesso de dados sem bloqueio para melhorar o desempenho da carga de trabalho OLTP. Há duas maneiras de organizar seus dados OLTP In-Memory:

- Formato **rowstore com otimização de memória** , em que cada linha é um objeto de memória separado. Esse é um formato clássico de OLTP In-Memory otimizado para cargas de trabalho OLTP de alto desempenho. Há dois tipos de tabelas com otimização de memória que podem ser usados no formato rowstore com otimização de memória:

  - *Tabelas duráveis* (SCHEMA_AND_DATA) em que as linhas colocadas na memória são preservadas após reiniciar o servidor. Esse tipo de tabelas se comporta como uma tabela rowstore tradicional com os benefícios adicionais de otimizações de memória.
  - *Tabelas não duráveis* (SCHEMA_ONLY) em que as linhas não são preservadas após a reinicialização. Esse tipo de tabela foi projetado para dados temporários (por exemplo, substituição de tabelas temporárias), ou tabelas em que você precisa carregar rapidamente os dados antes de movê-los para alguma tabela persistente (denominadas tabelas de preparo).

- Formato **columnstore com otimização de memória** em que os dados são organizados em um formato de coluna. Essa estrutura é projetada para cenários HTAP em que você precisa executar consultas analíticas na mesma estrutura de dados onde a carga de trabalho OLTP está em execução.

> [!Note]
> A tecnologia de OLTP In-Memory foi projetada para as estruturas de dados que podem residir totalmente na memória. Como os dados na memória não podem ser descarregados para o disco, certifique-se de estar usando um banco de dados que tenha memória suficiente. Consulte [Tamanho dos dados e limite de armazenamento do OLTP In-Memory](#data-size-and-storage-cap-for-in-memory-oltp) para obter mais informações.

Uma rápida introdução sobre In-Memory OLTP: [início rápido 1: In-Memory tecnologias OLTP para um desempenho mais rápido do T-SQL](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp) (outro artigo para ajudá-lo a começar)

Vídeos detalhados sobre as tecnologias:

- [OLTP na memória](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (que contém uma demonstração dos benefícios de desempenho e as etapas para reproduzir esses resultados por conta própria)
- [Vídeos sobre o OLTP in-memory: O que é e quando e como usá-lo](/archive/blogs/sqlserverstorageengine/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it)

Há uma maneira programática de entender se determinado banco de dados dá suporte ao OLTP in-memory. Execute a seguinte consulta Transact-SQL:

```sql
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Se a consulta retorna **1** , há suporte para o OLTP in-memory neste banco de dados. As consultas a seguir identificam todos os objetos que precisam ser removidos antes que um banco de dados possa ser desatualizado para Uso Geral, Standard ou Basic:

```sql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Tamanho dos dados e limite de armazenamento do OLTP in-memory

O OLTP in-memory inclui tabelas com otimização de memória, que são usadas para armazenar dados do usuário. Essas tabelas precisam caber na memória. Como você gerencia a memória diretamente no banco de dados SQL, temos o conceito de uma cota para o usuário. Esse conceito é conhecido como *Armazenamento de OLTP In-Memory* .

Cada tipo de preço de banco de dados individual e de pool elástico compatível inclui determinada quantidade de armazenamento do OLTP in-memory.

- [Limites de recursos baseados em DTU-banco de dados individual](database/resource-limits-dtu-single-databases.md)
- [Limites de recursos baseados em DTU-pools elásticos](database/resource-limits-dtu-elastic-pools.md)
- [Limites de modelo de recursos baseado em vCore – Banco de dados individual](database/resource-limits-vcore-single-databases.md)
- [limites de recursos baseados em vCore – pools elásticos](database/resource-limits-vcore-elastic-pools.md)
- [limites de recursos baseados em vCore-instância gerenciada](managed-instance/resource-limits.md)

Os itens a seguir contam para seu limite de armazenamento do OLTP in-memory:

- Linhas de dados de usuário ativo em tabelas com otimização de memória e variáveis de tabela. Observe que as versões de linha antigas não entram na contagem do limite.
- Índices em tabelas com otimização de memória.
- Custo operacional das operações ALTER TABLE.

Se atingir o limite, você receberá um erro de limite de cota atingido e não conseguirá inserir ou atualizar os dados. Para atenuar esse erro, exclua dados ou aumente o tipo de preço do banco de dados ou do pool.

Para obter detalhes sobre o monitoramento In-Memory utilização do armazenamento OLTP e a configuração de alertas quando você quase atinge o limite, consulte [monitorar o armazenamento na memória](in-memory-oltp-monitor-space.md).

#### <a name="about-elastic-pools"></a>Sobre pools elásticos

Com os pools elásticos, o armazenamento do OLTP in-memory é compartilhado entre todos os bancos de dados no pool. Portanto, o uso de um banco de dados pode afetar outros bancos de dados. As duas mitigações para esse problema são:

- Configure um `Max-eDTU` ou `MaxvCore` para bancos de dados que sejam inferiores à contagem eDTU ou vCore para o pool como um todo. Isso proporciona um limite máximo à utilização no armazenamento do OLTP in-memory em qualquer banco de dados no pool ao tamanho que corresponde à contagem de eDTUs.
- Configure um `Min-eDTU` ou `MinvCore` que seja maior que 0. Esse mínimo garante que cada banco de dados no pool tenha a quantidade de armazenamento de OLTP In-Memory disponível que corresponde ao configurado `Min-eDTU` ou `vCore`.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Alterar camadas de serviço dos bancos de dados que usam tecnologias de OLTP In-Memory

Você pode sempre atualizar o banco de dados ou a instância para uma camada superior, como de Uso Geral para Comercialmente Crítico (ou Standard para Premium). Os recursos e funcionalidades disponíveis só aumentam.

Porém, fazer downgrade da camada pode afetar negativamente o banco de dados. O impacto é especialmente aparente quando você faz o downgrade de Comercialmente Crítico para Uso Geral (ou Premium para Standard ou Básico) quando o banco de dados contém objetos OLTP In-Memory. As tabelas otimizadas para memória não ficam disponíveis após o downgrade (mesmo se permanecerem visíveis). As mesmas considerações se aplicam quando você está reduzindo o tipo de preço de um pool elástico ou movendo um banco de dados com tecnologias na memória para um pool elástico Uso Geral, Standard ou básico.

> [!Important]
> Não há suporte para o OLTP In-Memory em bancos de dados na camada Uso Geral, Standard ou Básico. Portanto, não é possível mover um banco de dados que tenha In-Memory objetos OLTP para uma dessas camadas.

Antes de fazer o downgrade do banco de dados para Uso Geral, Standard ou Basic, remova todas as tabelas com otimização de memória e os tipos de tabela, bem como todos os módulos T-SQL compilados nativamente.

*Recursos de dimensionamento na camada de comercialmente crítico* : os dados em tabelas com otimização de memória devem se ajustar ao armazenamento OLTP In-Memory que está associado à camada do banco de dados ou à instância gerenciada ou está disponível no pool elástico. Se você tentar reduzir a camada ou mover o banco de dados para um pool que não tem armazenamento OLTP In-Memory suficiente disponível, a operação falhará.

## <a name="in-memory-columnstore"></a>Columnstore In-memory

A tecnologia columnstore In-memory permite armazenar e consultar uma grande quantidade de dados nas tabelas. A tecnologia Columnstore usa o formato de armazenamento de dados com base em coluna e processamento de consulta em lote para obter ganho de até 10 vezes o desempenho da consulta em cargas de trabalho OLAP sobre o armazenamento tradicional orientado por linha. Também é possível obter ganhos de até 10 vezes na compactação de dados sobre o tamanho dos dados descompactados.
Há dois tipos de modelos de columnstore que você pode usar para organizar seus dados:

- **Columnstore clusterizado** em que todos os dados na tabela são organizadas em formato colunar. Nesse modelo, todas as linhas na tabela são colocadas em formato colunar que compacta bastante os dados e permite que você execute rápidas consultas analíticas e relatórios na tabela. Dependendo da natureza dos seus dados, o tamanho dos dados pode ser reduzido de 10x a 100x. O modelo de columnstore clusterizado também permite a ingestão rápida de uma grande quantidade de dados (carregamento em massa) já que grandes lotes de dados maiores que 100.000 linhas são compactados antes de serem armazenadas no disco. Esse modelo é uma boa escolha para os cenários clássicos de data warehouse.
- **Columnstore não clusterizado** em que os dados são armazenados na tabela rowstore tradicional e há um índice no formato columnstore que é usado para as consultas analíticas. Esse modelo permite HTAP (Hybrid Transactional-Analytic Processing): a capacidade de executar a análise em tempo real de alto desempenho em uma carga de trabalho transacional. Consultas OLTP são executadas na tabela rowstore que é otimizada para acessar um pequeno conjunto de linhas, enquanto as consultas OLAP são executadas em um índice columnstore que é a melhor opção para exames e análises. O otimizador de consulta escolhe dinamicamente o formato de repositório ou do columnstore com base na consulta. Os índices columnstore não clusterizados não diminuem o tamanho dos dados, pois o conjunto de dados original é mantido na tabela rowstore original sem qualquer alteração. No entanto, o tamanho do índice columnstore adicional deve ser em ordem de magnitude menor do que o índice de árvore B equivalente.

> [!Note]
> A tecnologia columnstore In-memory mantém apenas os dados que são necessários para processamento na memória, enquanto os dados que ajustam-se à memória são armazenados em disco. Portanto, a quantidade de dados em estruturas columnstore na memória pode exceder a quantidade de memória disponível.

Vídeo detalhado sobre a tecnologia:

- [Índice Columnstore: vídeos de análise na memória do Ignite 2016](/archive/blogs/sqlserverstorageengine/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Tamanho dos dados e armazenamento para índices columnstore

Os índices columnstore não precisam caber na memória. Portanto, o único limite para o tamanho dos índices é o tamanho máximo do banco de dados geral, que está documentado nos artigos [modelo de compra baseado em DTU](database/service-tiers-dtu.md) e [modelo de compra baseado em vCore](database/service-tiers-vcore.md).

Ao usar os índices columnstore clusterizados, a compactação vertical é usada para o armazenamento de tabelas base. Essa compactação pode reduzir consideravelmente o volume de armazenamento dos dados do usuário, o que significa que você pode colocar mais dados no banco de dados. E a compactação pode ser ainda maior com a [compactação de arquivamento vertical](/sql/relational-databases/data-compression/data-compression#using-columnstore-and-columnstore-archive-compression). A quantidade de compactação que pode ser obtida depende da natureza dos dados, mas uma compactação de 10 vezes não é incomum.

Por exemplo, se você tiver um banco de dados com tamanho máximo de 1 TB (terabyte) e obter uma compactação de 10 vezes usando índices columnstore, você poderá colocar um total de 10 TB de dados de usuário no banco de dados.

Quando você usa os índices columnstore não clusterizado, a tabela base ainda é armazenada no formato rowstore tradicional. Portanto, as economias de armazenamento não são tão significativas quanto com os índices columnstore clusterizados. No entanto, se você estiver substituindo vários índices não clusterizados tradicionais por um único índice columnstore, você ainda poderá observar uma economia geral no espaço de armazenamento da tabela.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Alterar camadas de serviço dos bancos de dados contendo índices Columnstore

O *downgrade de banco de dados individual para básico ou Standard* pode não ser possível se a camada de destino estiver abaixo do S3. Os índices columnstore só têm suporte no tipo de preço Comercialmente Crítico/Premium e na camada Standard, S3 e superior, e não na camada Básico. Ao fazer o downgrade de seu banco de dados para um tipo ou nível sem suporte, seu índice columnstore fica indisponível. O sistema mantém seu índice columnstore, mas nunca utiliza o índice. Se, mais tarde, você atualizar de volta para um tipo ou nível com suporte, o índice columnstore será imediatamente disponibilizado para uso novamente.

Se você tiver um índice columnstore **clusterizado** , a tabela inteira ficará indisponível após o downgrade. Portanto, recomendamos que você remova todos os índices columnstore *clusterizado* antes de fazer o downgrade de seu banco de dados para um tipo ou nível sem suporte.

> [!Note]
> O SQL Instância Gerenciada dá suporte a índices Columnstore em todas as camadas.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido 1: Tecnologias OLTP In-Memory para um desempenho mais rápido do T-SQL](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp)
- [Usar o OLTP In-Memory em um aplicativo existente do SQL Azure](in-memory-oltp-configure.md)
- [Monitorar o armazenamento do OLTP In-Memory](in-memory-oltp-monitor-space.md) para o OLTP In-Memory
- [Experimente os recursos de memória](in-memory-sample.md)

## <a name="additional-resources"></a>Recursos adicionais

### <a name="deeper-information"></a>Informações mais detalhadas

- [Saiba como o Quorum dobra a principal carga de trabalho do banco de dados, enquanto reduz a DTU em 70% com o OLTP in-memory no Banco de Dados SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [Postagem no blog OLTP na memória](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Saiba mais sobre o OLTP in-memory](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Saiba mais sobre os índices columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview)
- [Saiba mais sobre a análise operacional em tempo real](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)
- Veja [Padrões comuns de carga de trabalho e considerações sobre migração](/previous-versions/dn673538(v=msdn.10)) (que descreve os padrões de carga de trabalho para os quais o OLTP In-Memory geralmente fornece ganhos significativos de desempenho)

### <a name="application-design"></a>Design do aplicativo

- [OLTP na memória (otimização na memória)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Usar o OLTP In-Memory em um aplicativo existente do SQL Azure](in-memory-oltp-configure.md)

### <a name="tools"></a>Ferramentas

- [Azure portal](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)
- [SSDT (SQL Server Data Tools)](/sql/ssdt/download-sql-server-data-tools-ssdt)