---
title: Como solucionar problemas de desempenho de consultas no Banco de Dados do Azure para MySQL
description: Este artigo descreve como usar a EXPLAIN para solucionar problemas de desempenho de consultas no Banco de Dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 819e2393619766d46385cdd6fe550fff1e1a7631
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720205"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>Como usar a EXPLAIN para analisar o desempenho de consultas no Banco de Dados do Azure para MySQL
**EXPLAIN** é uma ferramenta útil para otimizar consultas. A instrução EXPLAIN pode ser utilizada para obter informações sobre como as instruções SQL são executadas. A saída a seguir mostra um exemplo da execução de uma instrução EXPLAIN.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Como pode ser observado a partir desse exemplo, o valor da *chave* é NULL. Esta saída significa que o MySQL não consegue localizar nenhum índice otimizado para a consulta e executa uma verificação de tabela completa. Vamos otimizar essa consulta, adicionando um índice na coluna **ID**.

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

A nova EXPLAIN mostra que o MySQL agora usa um índice para limitar o número de linhas para 1, o que, por sua vez, reduziu drasticamente o tempo de pesquisa.
 
## <a name="covering-index"></a>Índice de cobertura
Um índice de cobertura consiste em todas as colunas de uma consulta no índice para reduzir a recuperação de valor a partir de tabelas de dados. Aqui está uma ilustração na instrução **GROUP BY** a seguir.
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Como pode ser observado a partir da saída, o MySQL não usa nenhum índice porque não há índices adequados disponíveis. Além disso, também mostra *Usando temporário; Usando classificação de arquivo*, que significa que o MySQL cria uma tabela temporária para satisfazer a cláusula **GROUP BY**.
 
É indiferente criar um índice na coluna **c2** sozinho e o MySQL ainda precisa criar uma tabela temporária:

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Neste caso, um **índice coberto** em ambos **c1** e **c2** pode ser criado, através do qual adicionar o valor de **c2**" diretamente no índice para eliminar pesquisa de dados adicionais.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Como mostrado na EXPLAIN acima, o MySQL agora usa o índice coberto e evita criar uma tabela temporária. 

## <a name="combined-index"></a>Índice combinado
Um índice combinado consiste em valores de várias colunas e pode ser considerado uma matriz de linhas ordenadas por concatenação de valores das colunas indexadas. Esse método pode ser útil em uma instrução **GROUP BY**.

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

O MySQL executa uma operação de *classificação de arquivo* que é bastante lenta, especialmente quando é necessário classificar muitas linhas. Para otimizar essa consulta, um índice combinado pode ser criado em ambas as colunas que estão sendo classificadas.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

A EXPLAIN agora mostra que o MySQL é capaz de usar o índice combinado para evitar a classificação adicional, já que o índice já está classificado.
 
## <a name="conclusion"></a>Conclusão
 
O uso de EXPLAIN e diferentes tipos de índices pode aumentar significativamente o desempenho. O simples fato de haver um índice na tabela, não significa necessariamente que o MySQL poderá utilizá-lo para consultas. Sempre valide suas pressuposições usando a EXPLAIN e otimize as consultas usando índices.


## <a name="next-steps"></a>Próximas etapas
- Para localizar respostas de pares às suas perguntas mais preocupantes ou publicar uma nova pergunta/resposta, visite o [Fórum do MSDN](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) ou o [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
