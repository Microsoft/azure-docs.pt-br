---
title: Notas sobre a versão do SQL Data Warehouse do Azure de setembro de 2018 | Microsoft Docs
description: Notas de versão do SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/08/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: bc559a1224aace2ee599c24c8dce07a6d55173fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474957"
---
# <a name="whats-new-in-azure-sql-data-warehouse-september-2018"></a>O que há de novo no SQL Data Warehouse do Azure? Setembro de 2018
O SQL Data Warehouse do Azure recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidos em setembro de 2018.

## <a name="new-lower-entry-point-for-sql-data-warehouse-gen2"></a>Novo ponto de entrada inferior para o SQL Data Warehouse Gen2
Em abril de 2018, a [Microsoft anunciou](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) o SQL Data Warehouse do Azure Gen2 que oferece 5x o desempenho, 5x a escala de computação, 4x a simultaneidade e armazenamento ilimitado. Conforme observado no [Data Warehouse no Benchmark da nuvem](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) da Gigaom, o SQL Data Warehouse Gen2 **supera o Amazon Redshift em 42%**.

A Gen2 agora está geralmente disponível em um ponto de entrada mais baixo do DWU500c, permitindo que você execute um data warehouse ou ambientes de teste / teste menores com todas as melhorias de serviço mais recentes. O novo ponto de entrada mantém todos os recursos da Gen2, incluindo [Cache adaptável](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/), [Lighting Fast Data Shuffling](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/) e suporte a [data warehouse em tempo real](https://azure.microsoft.com/blog/enabling-real-time-data-warehousing-with-azure-sql-data-warehouse/).

## <a name="sql-vulnerability-assessment"></a>Avaliação de vulnerabilidade do SQL
A [Avaliação de vulnerabilidade do SQL (VA)](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/25/sql-vulnerability-assessment-now-supports-azure-sql-data-warehouse-and-azure-sql-database-managed-instance/) é um serviço fácil de usar que monitora continuamente o data warehouse. Ela ajuda a garantir um alto nível de segurança em todos os momentos e que as políticas organizacionais sejam atendidas. Ela fornece um relatório abrangente de segurança, juntamente com as etapas de correção acionáveis para cada problema encontrado. Este relatório facilita gerenciar a dimensão de segurança do banco de dados de forma proativa e permite concentrar a atenção em ações de impacto mais altas, mesmo se você não for especialista em segurança. Para ambientes dinâmicos em que alterações sejam frequentes e difíceis de rastrear, a VA é imprescindível para detectar as configurações que podem deixar seu data warehouse vulnerável a ataques.

## <a name="improved-availability-with-query-restartability"></a>Disponibilidade aprimorada com capacidade de reinicialização de consulta
Durante a execução da consulta, qualquer número de problemas pode ocorrer, o que pode causar falha na consulta. Uma interrupção de rede, uma falha de hardware ou outra desconexão pode causar uma interrupção. O SQL Data Warehouse agora suporta capacidade de reinicialização de consulta para consultas SELECT de etapa ou de instrução. 

Com a possibilidade de reinicialização de consulta, uma consulta em andamento interrompida devido a uma falha será reiniciada automaticamente. Dependendo do número de etapas, da forma da consulta ou da interrupção da consulta durante a execução, o mecanismo do SQL Data Warehouse reiniciará a consulta completa ou será retomado a partir da última etapa da consulta concluída. Do ponto de vista de um usuário final, a consulta é concluída. 

## <a name="maintenance-scheduling-preview"></a>Manutenção de agendamento (visualização)
O Agendamento de Manutenção do SQL Data Warehouse do Azure agora está na pré-visualização. Esse novo recurso integra perfeitamente as Notificações de Manutenção Planejada de Integridade do Serviço, o Monitor de Verificação de Integridade do Recurso e o serviço de agendamento de manutenção do Azure SQL Data Warehouse. O agendamento de manutenção permite agendar uma janela de horário quando for conveniente receber novos recursos, atualizações e patches.

O Agendamento de Manutenção aproveita o Monitor do Azure e permite que os clientes determinem como desejam ser notificados sobre eventos de manutenção iminentes e quais fluxos automatizados devem ser acionados para gerenciar o tempo de inatividade e minimizar o impacto em suas operações. As notificações podem incluir um email ou texto. 

## <a name="wide-row-support-in-polybase"></a>Suporte a ampla linha no Polybase
Ao carregar dados no SQL Data Warehouse, a orientação geral é usar [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading#options-for-loading-with-polybase) com suporte ao carregamento de dados paralelos. Esta versão permite o suporte para colunas mais amplas de 32K a 1MB - permitindo que você carregue tabelas com tamanho de linhas largas. O suporte para linhas largas simplifica o processo de carregamento de dados para tabelas com linhas largas.

> [!Note]
> O tamanho total da linha não pode exceder 1 MB de tamanho.

## <a name="additional-language-support"></a>Suporte a idiomas adicionais
Esta versão introduz o suporte para os seguintes elementos de linguagem T-SQL:

### <a name="stringsplit"></a>STRING_SPLIT
O [STRING_SPLIT](https://docs.microsoft.com/sql/t-sql/functions/string-split-transact-sql) função divide uma cadeia de caracteres usando o separador especificado. STRING_SPLIT é útil em cenários de carregamento de dados em que uma coluna pode ter vários valores para analisar e inserir em outra tabela.

#### <a name="example"></a>Exemplo
```sql
DECLARE @tags NVARCHAR(400) = 'clothing,road,,touring,bike';

SELECT
    value
FROM
    STRING_SPLIT(@tags, ',')
WHERE
    RTRIM(value) <> '';
```

### <a name="compressdecompress-functions"></a>Funções de compactação/descompactação
O [COMPACTAR](https://docs.microsoft.com/sql/t-sql/functions/compress-transact-sql) / [DECOMPRESS](https://docs.microsoft.com/sql/t-sql/functions/decompress-transact-sql) funções permitem que você compactar ou descompactar uma cadeia de caracteres de entrada usando o algoritmo GZIP.

#### <a name="example"></a>Exemplo

```sql
SELECT
    name [name_original]
    , COMPRESS(name) [name_compressed]
    , CAST(DECOMPRESS(COMPRESS(name)) AS NVARCHAR(MAX)) [name_decompressed]
FROM
    sys.objects;
```

### <a name="if-exists-clause-for-dropping-views"></a>Cláusula IF EXISTS descartar exibições
A inclusão da cláusula IF EXISTS na instrução [DROP VIEW](https://docs.microsoft.com/sql/t-sql/statements/drop-view-transact-sql) simplifica o código T-SQL necessário para remover uma exibição do armazém de dados. A sintaxe IF EXISTS, quando aplicada a uma instrução DROP VIEW, eliminará a visualização se ela existir ou ignorará a instrução se a exibição não existir.

#### <a name="example"></a>Exemplo
```sql
DROP VIEW IF EXISTS dbo.TestView;
```
```
Message
--------------------------------------------------
Commands completed successfully.

```

## <a name="improved-compilation-time-for-singleton-inserts-and-ddl-statements"></a>Melhor tempo de compilação para Inserções Singleton e Instruções DDL 
Seguir um modelo tradicional de Extração, Transformação e Carregamento (ETL) para inserção de dados geralmente leva a uma inserção de singleton ([INSERT-VALUES](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql)) em uma tabela no banco de dados. Esta versão melhora o desempenho de operações de inserção de singleton, reduzindo o tempo de compilação necessário para executar esse tipo de instrução. Em alguns casos, a melhoria de compilação observada é até 3x mais rápida. Essa melhoria diminuirá o tempo necessário para executar uma instrução de inserção única. 

O aprimoramento também beneficia os data warehouses com um grande número de objetos reduzindo, da mesma forma, o tempo de compilação de consulta para instruções DDL (Data Definition Language), incluindo as operações CREATE, ALTER e DELETE. 

Finalmente, a melhoria reduz a execução geral de instruções que são executadas em tabelas largas - tabelas que possuem um grande número de colunas. A melhoria é uma redução no tempo na etapa de compilação de consulta que reduz o tempo de execução geral das consultas.

## <a name="bug-fixes"></a>Correções de bug

| Title | DESCRIÇÃO |
|:---|:---|
| **Corrigir ao criar estatísticas sobre distribuições para restrições exclusivas** | Esta correção aborda um erro que os usuários encontram ao executar UPDATE STATISTICS com apenas a Tabela especificada, quando a tabela possui uma restrição exclusiva definida. |
| **Corrigir ao compilar consultas em tabelas externas** | Essa correção resolve um defeito que afetou o tempo de compilação para consultas envolvendo tabelas externas.|
| **Correção ao executar uma instrução com tipos grandes** | Aborda um defeito na compilação de instrução preparada com parâmetros declarados como um dos *grandes* tipos (nvarchar (max), varchar (max) e varbinary (max)). |
| **Corrigir quando ocorrer um erro para consultas profundamente aninhadas** | Fornece uma mensagem de erro clara quando uma consulta profundamente aninhada excede os limites do sistema.|
| **Correção de erros de tempo de compilação quando uma instrução contém uma subconsulta correlacionada e uma constante de tempo de execução** |Endereços de tempo de compilação de erro para consultas com uma combinação específica de sub consultas correlacionadas e constantes de tempo de execução (como GETDATE ()).|
| **Tempo limite de endereço para adquirir bloqueios de objeto PDW e slot de simultaneidade para autostats** |Correção adiciona o tempo limite de bloqueio para impedir que solicitações autostats bloqueando as solicitações de origem por um longo tempo.|

## <a name="next-steps"></a>Próximas etapas
Agora que você conhece um pouco do SQL Data Warehouse, aprenda como [criar um SQL Data Warehouse][create a SQL Data Warehouse] rapidamente. Se você ainda não conhece o Azure, poderá achar o [Glossário do Azure][Azure glossary] útil à medida que encontrar nova terminologia. Ou, dê uma olhada em alguns desses outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogs]
* [Solicitações de recursos]
* [Vídeos]
* [Blogs da Equipe Consultoria para Clientes]
* [Fórum Stack Overflow]
* [Twitter]


[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs da Equipe Consultoria para Clientes]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
