---
title: Usar transações no pool do SQL do Azure Synapse Analytics
description: Este artigo inclui dicas para implementar transações e desenvolver soluções no pool de SQL do Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/22/2019
ms.author: xiaoyul
ms.custom: azure-synapse
ms.reviewer: igorstan
ms.openlocfilehash: 8144c588d4b6794cadc0577bf63dabc2cc3e0efd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98677279"
---
# <a name="use-transactions-in-a-sql-pool-in-azure-synapse"></a>Usar transações em um pool do SQL no Azure Synapse 

Este artigo inclui dicas para implementar transações e desenvolver soluções em um pool do SQL.

## <a name="what-to-expect"></a>O que esperar

Como era esperado, o pool de SQL oferece suporte a transações como parte da carga de trabalho do data warehouse. No entanto, para garantir que o pool do SQL seja mantido em escala, alguns recursos são limitados em comparação com SQL Server. Este artigo destaca as diferenças.

## <a name="transaction-isolation-levels"></a>Níveis de isolamento da transação

O pool de SQL implementa transações ACID. O nível de isolamento do suporte transacional usa como padrão READ UNCOMMITTED.  Você pode alterá-lo para ler o isolamento de instantâneo confirmado ativando a opção de banco de dados READ_COMMITTED_SNAPSHOT para um pool SQL de usuário quando conectado ao banco de dados mestre.  

Após a habilitação, todas as transações nesse banco de dados serão executadas em READ COMMITTED SNAPSHOT ISOLATION, e a configuração READ UNCOMMITTED no nível da sessão não será respeitada. Confira [Opções ALTER DATABASE SET (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para detalhes.

## <a name="transaction-size"></a>Tamanho da transação

Uma única transação de modificação de dados é limitada em tamanho. O limite é aplicado por distribuição. Portanto, a alocação total pode ser calculada multiplicando o limite pela contagem de distribuição.

Para chegar a uma aproximação do número máximo de linhas na transação, divida o limite de distribuição pelo tamanho total de cada linha. Para colunas de tamanho variável, considere o uso de um tamanho médio de coluna em vez do tamanho máximo.

Na tabela a seguir, foram feitas duas suposições:

* Ocorreu uma distribuição uniforme dos dados
* O tamanho médio da linha é de 250 bytes

## <a name="gen2"></a>Gen2

| [DWU](./sql-data-warehouse-overview-what-is.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) | Limite por distribuição (GB) | Número de distribuições | Tamanho máximo de transações (GB) | Nº de linhas por distribuição | Máximo de linhas por transação |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4\.000.000 |240.000.000 |
| DW200c |1.5 |60 |90 |6\.000.000 |360.000.000 |
| DW300c |2.25 |60 |135 |9\.000.000 |540.000.000 |
| DW400c |3 |60 |180 |12.000.000 |720.000.000 |
| DW500c |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW1000c |7.5 |60 |450 |30.000.000 |1\.800.000.000 |
| DW1500c |11,25 |60 |675 |45.000.000 |2\.700.000.000 |
| DW2000c |15 |60 |900 |60.000.000 |3\.600.000.000 |
| DW2500c |18,75 |60 |1125 |75.000.000 |4\.500.000.000 |
| DW3000c |22,5 |60 |1\.350 |90.000.000 |5\.400.000.000 |
| DW5000c |37,5 |60 |2\.250 |150.000.000 |9\.000.000.000 |
| DW6000c |45 |60 |2\.700 |180.000.000 |10.800.000.000 |
| DW7500c |56,25 |60 |3\.375 |225.000.000 |13.500.000.000 |
| DW10000c |75 |60 |4\.500 |300.000.000 |18.000.000.000 |
| DW15000c |112,5 |60 |6\.750 |450.000.000 |27.000.000.000 |
| DW30000c |225 |60 |13.500 |900.000.000 |54.000.000.000 |

## <a name="gen1"></a>Gen1

| [DWU](./sql-data-warehouse-overview-what-is.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) | Limite por distribuição (GB) | Número de distribuições | Tamanho máximo de transações (GB) | Nº de linhas por distribuição | Máximo de linhas por transação |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4\.000.000 |240.000.000 |
| DW200 |1.5 |60 |90 |6\.000.000 |360.000.000 |
| DW300 |2.25 |60 |135 |9\.000.000 |540.000.000 |
| DW400 |3 |60 |180 |12.000.000 |720.000.000 |
| DW500 |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW600 |4.5 |60 |270 |18.000.000 |1\.080.000.000 |
| DW1000 |7.5 |60 |450 |30.000.000 |1\.800.000.000 |
| DW1200 |9 |60 |540 |36.000.000 |2\.160.000.000 |
| DW1500 |11,25 |60 |675 |45.000.000 |2\.700.000.000 |
| DW2000 |15 |60 |900 |60.000.000 |3\.600.000.000 |
| DW3000 |22,5 |60 |1\.350 |90.000.000 |5\.400.000.000 |
| DW6000 |45 |60 |2\.700 |180.000.000 |10.800.000.000 |

O limite de tamanho de transação é aplicado por transação ou operação. Ele não é aplicado em todas as transações simultâneas. Portanto, cada transação tem permissão para gravar essa quantidade de dados no log.

Para otimizar e minimizar a quantidade de dados gravados no log, consulte o artigo [Transactions best practices](sql-data-warehouse-develop-best-practices-transactions.md)(Melhores práticas de transações).

> [!WARNING]
> O tamanho máximo de transações só pode ser obtido para tabelas distribuídas HASH ou ROUND_ROBIN nas quais o espalhamento de dados é uniforme. Se a transação estiver gravando dados de maneira distorcida nas distribuições, provavelmente, o limite será alcançado antes do tamanho máximo de transações.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Estado da transação

O pool de SQL usa a função XACT_STATE() para relatar uma transação com falha usando o valor -2. Esse valor significa que a transação falhou e está marcada para reversão somente.

> [!NOTE]
> O uso de -2 pela função XACT_STATE para denotar uma transação com falha representa um comportamento diferente para o SQL Server. O SQL Server usa o valor -1 para representar uma transação não confirmável. O SQL Server consegue tolerar alguns erros dentro de uma transação sem precisar ser marcado como não confirmável. Por exemplo, `SELECT 1/0` causaria um erro, mas não forçaria uma transação em um estado não confirmado.

O SQL Server também permite leituras na transação não confirmável. No entanto, o pool de SQL não permite que você faça isso. Se ocorrer um erro dentro de uma transação do pool do SQL, ele entrará automaticamente no estado-2 e você não poderá fazer mais instruções SELECT até que a instrução tenha sido revertida.

Como tal, é importante verificar se o código do aplicativo para ver se ele usa XACT_STATE (), pois talvez seja necessário fazer modificações no código.

Por exemplo, em SQL Server, você pode ver uma transação semelhante à seguinte:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

O código anterior oferece a seguinte mensagem de erro:

Msg 111233, Nível 16, Estado 1, Linha 1 111233; a transação atual foi anulada, e as alterações pendentes foram revertidas. A causa desse problema é que uma transação em um estado somente reversão não foi explicitamente revertida antes de uma instrução DDL, DML ou SELECT.

Você não receberá a saída das funções ERROR_*.

No pool de SQL, o código precisa ser ligeiramente alterado:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

O comportamento esperado é observado agora. O erro na transação é gerenciado e as funções ERROR_* fornecem valores conforme o esperado.

Tudo o que mudou é que o ROLLBACK da transação deve ocorrer antes da leitura das informações de erro no bloco CATCH.

## <a name="error_line-function"></a>Função Error_line()

Também vale a pena observar que o pool de SQL não implementa nem aceita a função ERROR_LINE(). Se você tiver isso em seu código, será necessário removê-lo para que ele fique em conformidade com o pool de SQL.

Em vez disso, use rótulos de consulta em seu código para implementar a funcionalidade equivalente. Para obter mais detalhes, consulte o artigo [LABEL](sql-data-warehouse-develop-label.md).

## <a name="using-throw-and-raiserror"></a>Uso de THROW e RAISERROR

THROW é a implementação mais moderna para lançar exceções no pool de SQL, mas também há suporte para RAISERROR. No entanto, existem algumas diferenças que valem a pena prestar atenção.

* Os números das mensagens de erro definidas pelo usuário não podem estar no intervalo de 100.000 a 150.000 para THROW
* As mensagens de erro do RAISERROR são fixadas em 50.000
* Não há suporte para o uso de sys.messages

## <a name="limitations"></a>Limitações

O pool de SQL tem algumas outras restrições relacionadas a transações.

Elas são as seguintes:

* Sem transações distribuídas
* Não há transações aninhadas permitidas
* Não são permitidos pontos de salvamento
* Nenhuma transação nomeada
* Nenhuma transação marcada
* Não há suporte para DDL, como CREATE TABLE em uma transação definida pelo usuário

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a otimização das transações, consulte [Transactions best practices](sql-data-warehouse-develop-best-practices-transactions.md) (Melhores práticas de transações). Para saber mais sobre outras práticas recomendadas do pool do SQL, consulte [práticas recomendadas do pool do SQL](sql-data-warehouse-best-practices.md).