---
title: Como solucionar problemas de desempenho de consulta no Banco de Dados do Azure para MariaDB
description: Este artigo descreve como usar o EXPLAIN para solucionar problemas de desempenho de consulta no Banco de Dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 672635c8d8c84fa16c106ae79e97332fd740928d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745155"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>Como usar o EXPLAIN para perfilar o desempenho da consulta no Banco de Dados do Azure para MariaDB
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

Como pode ser observado a partir desse exemplo, o valor da *chave* é NULL. Essa saída significa que o MariaDB não pode encontrar nenhum índice otimizado para a consulta e executa uma verificação de tabela. Vamos otimizar essa consulta, adicionando um índice na coluna **ID**.

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

O novo EXPLAIN mostra que o MariaDB agora usa um índice para limitar o número de linhas a 1, o que reduziu drasticamente o tempo de busca.
 
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

Como pode ser visto na saída, o MariaDB não usa nenhum índice porque nenhum índice adequado está disponível. Também mostra *Usando temporário; Usando file sort*, o que significa que o MariaDB cria uma tabela temporária para satisfazer a cláusula **GROUP BY**.
 
Criar um índice na coluna **c2** sozinho não faz diferença, e o MariaDB ainda precisa criar uma tabela temporária:

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

Como o EXPLAIN acima mostra, o MariaDB agora usa o índice coberto e evita a criação de uma tabela temporária. 

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

O MariaDB executa uma operação de *classificação de arquivos* que é bastante lenta, especialmente quando precisa classificar várias linhas. Para otimizar essa consulta, um índice combinado pode ser criado em ambas as colunas que estão sendo classificadas.

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

O EXPLAIN agora mostra que o MariaDB é capaz de usar o índice combinado para evitar classificação adicional, já que o índice já está classificado.
 
## <a name="conclusion"></a>Conclusão
 
O uso de EXPLAIN e diferentes tipos de índices pode aumentar significativamente o desempenho. Só porque você tem um índice na tabela não significa necessariamente que o MariaDB seria capaz de usá-lo para suas consultas. Sempre valide suas pressuposições usando a EXPLAIN e otimize as consultas usando índices.

## <a name="next-steps"></a>Próximas etapas
- Para localizar respostas de pares às suas perguntas mais preocupantes ou publicar uma nova pergunta/resposta, visite o [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDatabaseforMariadb) ou o [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb).
