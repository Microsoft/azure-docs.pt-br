---
title: "O OLTP in-memory melhora o desempenho de transações do SQL | Microsoft Docs"
description: "Adote o OLTP Na Memória para melhorar o desempenho transacional em um banco de dados SQL existente."
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: MightyPen
ms.assetid: c2bf14a0-905b-47b4-afb6-efe9a61147d5
ms.service: sql-database
ms.custom: develop databases
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: jodebrui
ms.openlocfilehash: 50eed9aed417778bd497f55e20c8e732fdae9cf9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Usar o OLTP in-memory para melhorar o desempenho do aplicativo no Banco de Dados SQL
O [OLTP in-memory](sql-database-in-memory.md) pode ser usado para melhorar o desempenho do processamento de transações, a ingestão de dados e os cenários de dados transitórios, em Bancos de Dados SQL [Premium](sql-database-service-tiers.md) do Azure sem aumentar o tipo de preço. 

> [!NOTE] 
> Saiba como o [Quorum dobra a principal carga de trabalho do banco de dados, enquanto reduz a DTU em 70% com o Banco de Dados SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Siga estas etapas para adotar o in-memory OLTP no banco de dados existente.

## <a name="step-1-ensure-you-are-using-a-premium-database"></a>Etapa 1: Garantir que você está usando um banco de dados Premium
Há suporte para o OLTP in-memory apenas em bancos de dados Premium. Na Memória terá suporte se o resultado retornado for 1 (e não 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* significa *Processamento Extremo de Transações*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Etapa 2: Identificar os objetos para migrar para o OLTP Na Memória
O SSMS inclui um relatório **Visão Geral da Análise do Desempenho da Transação** que pode ser executado em um banco de dados com uma carga de trabalho ativa. O relatório identifica as tabelas e os procedimentos armazenados candidatos à migração para OLTP Na Memória.

No SSMS, para gerar o relatório:

* No **Pesquisador de Objetos**, clique com o botão direito do mouse no nó do banco de dados.
* Clique em **Relatórios** > **Relatórios Padrão** > **Visão Geral da Análise de Desempenho da Transação**.

Para saber mais, confira [Determinando se uma tabela ou um procedimento armazenado deve ser transportado para o OLTP Na Memória](http://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Etapa 3: Criar um banco de dados de teste comparável
Suponha que o relatório indique que o banco de dados tem uma tabela que pode se beneficiar do que está sendo convertido em uma tabela com otimização de memória. É recomendável que você primeiro teste para confirmar a indicação.

Você precisa de uma cópia de teste do seu banco de dados de produção. O banco de dados de teste deve estar no mesmo nível da camada de serviço que o banco de dados de produção.

Para facilitar o teste, ajuste seu banco de dados de teste da seguinte maneira:

1. Conecte-se ao banco de dados de teste usando o SSMS.
2. Para evitar a necessidade da opção WITH (SNAPSHOT) em consultas, defina a opção de banco de dados como mostrado na seguinte instrução T-SQL:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Etapa 4: migrar tabelas
Você deve criar e preencher uma cópia com otimização de memória da tabela que você deseja testar. Você pode criá-la usando:

* O Assistente de Otimização de Memória útil no SSMS.
* T-SQL Manual.

#### <a name="memory-optimization-wizard-in-ssms"></a>Assistente de Otimização de Memória no SSMS
Para usar essa opção de migração:

1. Conecte-se ao banco de dados de teste com o SSMS.
2. No **Pesquisador de Objetos**, clique com o botão direito do mouse na tabela e clique em **Supervisor de Otimização de Memória**.
   
   * O assistente **Supervisor Otimizador de Memória da Tabela** é exibido.
3. No assistente, clique em **Validação de migração** (ou no botão **Avançar**) para ver se a tabela tem algum recurso sem suporte nas tabelas com otimização de memória. Para obter mais informações, consulte:
   
   * A *lista de verificação de otimização de memória* no [Supervisor de Otimização de Memória](http://msdn.microsoft.com/library/dn284308.aspx).
   * [Construções Transact-SQL sem suporte do OLTP Na Memória](http://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrando para o OLTP Na Memória](http://msdn.microsoft.com/library/dn247639.aspx).
4. Se a tabela não tiver recursos sem suporte, o supervisor poderá executar o esquema e a migração de dados reais para você.

#### <a name="manual-t-sql"></a>T-SQL Manual
Para usar essa opção de migração:

1. Conecte-se ao banco de dados de teste usando o SSMS (ou um utilitário semelhante).
2. Obtenha o script T-SQL completo para a tabela e seus índices.
   
   * No SSMS, clique com o botão direito do mouse no nó da sua tabela.
   * Clique em **Script de Tabela como** > **CRIAR Para** > **Nova Janela de Consulta**.
3. Na janela de script, adicione WITH (MEMORY_OPTIMIZED = ON) à instrução CREATE TABLE.
4. Se houver um índice CLUSTERED, altere-o para NONCLUSTERED.
5. Renomeie a tabela existente usando SP_RENAME.
6. Crie a nova cópia da tabela com otimização de memória executando o script CREATE TABLE editado.
7. Copie os dados para sua tabela com otimização de memória usando INSERT...SELECT * INTO:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Etapa 5 (opcional): migrar procedimentos armazenados
O recurso Na Memória também pode modificar um procedimento armazenado para melhorar o desempenho.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Considerações sobre procedimentos armazenados compilados nativamente
Um procedimento armazenado compilado nativamente deve ter as seguintes opções na sua cláusula WITH T-SQL:

* NATIVE_COMPILATION
* SCHEMABINDING: significa tabelas cujas definições de coluna o procedimento armazenado não pode alterar de alguma forma que afete o próprio procedimento armazenado, a menos que você descarte o procedimento armazenado.

Um módulo nativo deve usar grandes [blocos ATOMIC](http://msdn.microsoft.com/library/dn452281.aspx) para o gerenciamento de transações. Não há uma função para BEGIN TRANSACTION explícita ou para ROLLBACK TRANSACTION. Se seu código detectar uma violação de uma regra de negócio, poderá finalizar o bloco atômico com uma instrução [THROW](http://msdn.microsoft.com/library/ee677615.aspx) .

### <a name="typical-create-procedure-for-natively-compiled"></a>CREATE PROCEDURE típico para compilados nativamente
Normalmente, o T-SQL para criar um procedimento armazenado nativamente compilado é semelhante ao seguinte modelo:

```
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
   
   * Para obter detalhes, consulte [Problemas de migração para procedimentos armazenados compilados nativamente](http://msdn.microsoft.com/library/dn296678.aspx).
4. Renomeie o antigo procedimento armazenado usando SP_RENAME. Ou simplesmente descarte-o usando DROP.
5. Execute o script T-SQL CREATE PROCEDURE editado.

## <a name="step-6-run-your-workload-in-test"></a>Etapa 6: executar sua carga de trabalho no teste
Execute uma carga de trabalho em seu banco de dados de teste que seja semelhante à carga de trabalho executada em seu banco de dados de produção. Isso deve revelar o ganho de desempenho obtido com o uso do recurso Na Memória para tabelas e procedimentos armazenados.

Os principais atributos da carga de trabalho são:

* Número de conexões simultâneas.
* Taxa de leitura/gravação.

Para ajustar e executar a carga de trabalho de teste, considere usar a ferramenta útil ostress.exe, ilustrada [aqui](sql-database-in-memory.md).

Para minimizar a latência de rede, execute o teste na mesma região geográfica do Azure onde está o banco de dados.

## <a name="step-7-post-implementation-monitoring"></a>Etapa 7: Monitoramento pós-implementação
Considere monitorar os efeitos de desempenho de  suas implementações de Na Memória em produção:

* [Monitorar o armazenamento na memória](sql-database-in-memory-oltp-monitoring.md).
* [Monitoramento de Banco de Dados SQL usando exibições de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Links relacionados
* [OLTP Na Memória (Otimização Na Memória)](http://msdn.microsoft.com/library/dn133186.aspx)
* [Introdução aos procedimentos armazenados compilados nativamente](http://msdn.microsoft.com/library/dn133184.aspx)
* [Supervisor de Otimização de Memória](http://msdn.microsoft.com/library/dn284308.aspx)

