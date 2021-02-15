---
title: In-Memory OLTP melhora o desempenho do SQL TXN
description: Adote In-Memory OLTP para melhorar o desempenho transacional em um banco de dados existente no banco de dados SQL do Azure e no SQL Instância Gerenciada do Azure.
services: sql-database
ms.service: sql-database
ms.custom: sqldbrb=2
ms.subservice: development
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: e17e98e784b7453c87814c5cce5c03568f66b1cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619739"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>Use In-Memory OLTP para melhorar o desempenho do aplicativo no banco de dados SQL do Azure e no SQL do Azure Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

O [OLTP in-memory](in-memory-oltp-overview.md) pode ser usado para melhorar o desempenho do processamento de transações, a ingestão de dados e os cenários de dados transitórios, em bancos de dados das [camadas Premium e Comercialmente Crítico](database/service-tiers-vcore.md) sem aumentar o tipo de preço.

> [!NOTE]
> Saiba como o [Quorum duplica a carga de trabalho do banco de dados principal ao reduzir a DTU por 70% com o banco de dados SQL do Azure](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

Siga estas etapas para adotar o in-memory OLTP no banco de dados existente.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Etapa 1: Verifique se que você estivá usando um banco de dados de camada Premium e Comercialmente Crítico

Há suporte para o OLTP in-memory apenas em bancos de dados de camada Premium e Comercialmente Crítico. Na Memória terá suporte se o resultado retornado for 1 (e não 0):

```sql
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* significa *Processamento Extremo de Transações*

## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Etapa 2: Identificar os objetos para migrar para o OLTP Na Memória

O SSMS inclui um relatório **Visão Geral da Análise do Desempenho da Transação** que pode ser executado em um banco de dados com uma carga de trabalho ativa. O relatório identifica as tabelas e os procedimentos armazenados candidatos à migração para OLTP Na Memória.

No SSMS, para gerar o relatório:

* No **Pesquisador de Objetos**, clique com o botão direito do mouse no nó do banco de dados.
* Clique em **relatórios**  >  **padrão relatórios**  >  **análise de desempenho da transação visão geral**.

Para saber mais, confira [Determinando se uma tabela ou um procedimento armazenado deve ser transportado para o OLTP Na Memória](/sql/relational-databases/in-memory-oltp/determining-if-a-table-or-stored-procedure-should-be-ported-to-in-memory-oltp).

## <a name="step-3-create-a-comparable-test-database"></a>Etapa 3: Criar um banco de dados de teste comparável

Suponha que o relatório indique que o banco de dados tem uma tabela que pode se beneficiar do que está sendo convertido em uma tabela com otimização de memória. É recomendável que você primeiro teste para confirmar a indicação.

Você precisa de uma cópia de teste do seu banco de dados de produção. O banco de dados de teste deve estar no mesmo nível da camada de serviço que o banco de dados de produção.

Para facilitar o teste, ajuste seu banco de dados de teste da seguinte maneira:

1. Conecte-se ao banco de dados de teste usando o SSMS.
2. Para evitar a necessidade da opção WITH (SNAPSHOT) em consultas, defina a opção de banco de dados como mostrado na seguinte instrução T-SQL:

   ```sql
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Etapa 4: migrar tabelas

Você deve criar e preencher uma cópia com otimização de memória da tabela que você deseja testar. Você pode criá-la usando:

* O Assistente de Otimização de Memória útil no SSMS.
* T-SQL Manual.

### <a name="memory-optimization-wizard-in-ssms"></a>Assistente de Otimização de Memória no SSMS

Para usar essa opção de migração:

1. Conecte-se ao banco de dados de teste com o SSMS.
2. No **Pesquisador de Objetos**, clique com o botão direito do mouse na tabela e clique em **Supervisor de Otimização de Memória**.

   O assistente **Supervisor Otimizador de Memória da Tabela** é exibido.
3. No assistente, clique em **Validação de migração** (ou no botão **Avançar**) para ver se a tabela tem algum recurso sem suporte nas tabelas com otimização de memória. Para obter mais informações, consulte:

   * A *lista de verificação de otimização de memória* no [Supervisor de Otimização de Memória](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor).
   * [Construções Transact-SQL sem suporte pelo OLTP In-Memory](/sql/relational-databases/in-memory-oltp/transact-sql-constructs-not-supported-by-in-memory-oltp).
   * [Migrando para In-Memory OLTP](/sql/relational-databases/in-memory-oltp/plan-your-adoption-of-in-memory-oltp-features-in-sql-server).
4. Se a tabela não tiver recursos sem suporte, o supervisor poderá executar o esquema e a migração de dados reais para você.

### <a name="manual-t-sql"></a>T-SQL Manual

Para usar essa opção de migração:

1. Conecte-se ao banco de dados de teste usando o SSMS (ou um utilitário semelhante).
2. Obtenha o script T-SQL completo para a tabela e seus índices.

   * No SSMS, clique com o botão direito do mouse no nó da sua tabela.
   * Clique em **tabela de script como**  >  **criar para a**  >  **nova janela de consulta**.
3. Na janela de script, adicione WITH (MEMORY_OPTIMIZED = ON) à instrução CREATE TABLE.
4. Se houver um índice CLUSTERED, altere-o para NONCLUSTERED.
5. Renomeie a tabela existente usando SP_RENAME.
6. Crie a nova cópia da tabela com otimização de memória executando o script CREATE TABLE editado.
7. Copie os dados para sua tabela com otimização de memória usando INSERT...SELECT * INTO:

```sql
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```

## <a name="step-5-optional-migrate-stored-procedures"></a>Etapa 5 (opcional): migrar procedimentos armazenados

O recurso Na Memória também pode modificar um procedimento armazenado para melhorar o desempenho.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Considerações sobre procedimentos armazenados compilados nativamente

Um procedimento armazenado compilado nativamente deve ter as seguintes opções na sua cláusula WITH T-SQL:

* NATIVE_COMPILATION
* SCHEMABINDING: significa tabelas cujas definições de coluna o procedimento armazenado não pode alterar de alguma forma que afete o próprio procedimento armazenado, a menos que você descarte o procedimento armazenado.

Um módulo nativo deve usar grandes [blocos ATOMIC](/sql/relational-databases/in-memory-oltp/atomic-blocks-in-native-procedures) para o gerenciamento de transações. Não há uma função para BEGIN TRANSACTION explícita ou para ROLLBACK TRANSACTION. Se seu código detectar uma violação de uma regra de negócio, poderá finalizar o bloco atômico com uma instrução [THROW](/sql/t-sql/language-elements/throw-transact-sql) .

### <a name="typical-create-procedure-for-natively-compiled"></a>CREATE PROCEDURE típico para compilados nativamente

Normalmente, o T-SQL para criar um procedimento armazenado nativamente compilado é semelhante ao seguinte modelo:

```sql
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* Para TRANSACTION_ISOLATION_LEVEL, o SNAPSHOT é o valor mais comum para o procedimento armazenado nativamente compilado. No entanto, também há suporte para um subconjunto dos outros valores:
  
  * REPEATABLE READ
  * SERIALIZABLE
* O valor LANGUAGE deve estar presente na exibição sys.languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Como migrar um procedimento armazenado

As etapas de migração são:

1. Obtenha o script CREATE PROCEDURE para o procedimento armazenado interpretado regular.
2. Reescreva o cabeçalho para que ele corresponda ao modelo anterior.
3. Verificar se o código T-SQL de procedimento armazenado usa os recursos sem suporte para procedimentos armazenados compilados nativamente. Implemente soluções alternativas, se necessário.

   Para obter detalhes, consulte [Problemas de migração para procedimentos armazenados compilados nativamente](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables).
4. Renomeie o antigo procedimento armazenado usando SP_RENAME. Ou simplesmente descarte-o usando DROP.
5. Execute o script T-SQL CREATE PROCEDURE editado.

## <a name="step-6-run-your-workload-in-test"></a>Etapa 6: executar sua carga de trabalho no teste

Execute uma carga de trabalho em seu banco de dados de teste que seja semelhante à carga de trabalho executada em seu banco de dados de produção. Isso deve revelar o ganho de desempenho obtido com o uso do recurso Na Memória para tabelas e procedimentos armazenados.

Os principais atributos da carga de trabalho são:

* Número de conexões simultâneas.
* Taxa de leitura/gravação.

Para adaptar e executar a carga de trabalho de teste, considere o uso da `ostress.exe` ferramenta útil, que ilustra neste artigo [na memória](in-memory-oltp-overview.md) .

Para minimizar a latência de rede, execute o teste na mesma região geográfica do Azure onde está o banco de dados.

## <a name="step-7-post-implementation-monitoring"></a>Etapa 7: Monitoramento pós-implementação

Considere monitorar os efeitos de desempenho de  suas implementações de Na Memória em produção:

* [Monitorar In-Memory armazenamento](in-memory-oltp-monitor-space.md).
* [Monitorando usando exibições de gerenciamento dinâmico](database/monitoring-with-dmvs.md)

## <a name="related-links"></a>Links relacionados

* [OLTP na memória (otimização na memória)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
* [Introdução aos procedimentos armazenados compilados nativamente](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables)
* [Orientador de otimização da memória](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor)
