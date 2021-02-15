---
title: Otimizar inserções em massa-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como você pode otimizar operações de inserção em massa em um banco de dados do Azure para PostgreSQL-servidor único.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 4d10f06577738364e3f4a0ea40221d37ebfb31c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86116277"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Otimizar inserções em massa e usar dados transitórios em um banco de dado do Azure para PostgreSQL-servidor único 
Este artigo descreve como você pode otimizar operações de inserção em massa e usar dados temporários em um servidor do Banco de Dados do Azure para PostgreSQL.

## <a name="use-unlogged-tables"></a>Usar tabelas não registradas
Se você tem operações de carga de trabalho que envolvem dados temporários ou inserem grandes conjuntos de dados em massa, considere o uso de tabelas não registradas.

Tabelas sem registro é um recurso do PostgreSQL que pode ser usado efetivamente para otimizar inserções em massa. O PostgreSQL usa WAL (registro em log write-ahead). Por padrão, ele fornece atomicidade e durabilidade. Atomicidade, consistência, isolamento e durabilidade são as propriedades ACID. 

Inseri-las em uma tabela não registrada significa que o PostgreSQL faz inserções sem gravar no log de transações, que, por si só, é uma operação de E/S. Como resultado, essas tabelas são consideravelmente mais rápidas do que as tabelas comuns.

Use as seguintes opções para criar uma tabela não registrada:
- Crie uma nova tabela não registrada usando a sintaxe `CREATE UNLOGGED TABLE <tableName>`.
- Converta uma tabela registrada existente em uma tabela não registrada usando a sintaxe `ALTER TABLE <tableName> SET UNLOGGED`.  

Para reverter o processo, use a sintaxe `ALTER TABLE <tableName> SET LOGGED`.

## <a name="unlogged-table-tradeoff"></a>Troca de tabela sem registro
Tabelas não registradas não estão livres de falhas. Uma tabela não registrada é automaticamente truncada após uma falha ou sujeita a um desligamento não limpo. O conteúdo de uma tabela não registrada também não é replicado para servidores em espera. Quaisquer índices criados em uma tabela não registrada também são descompactados automaticamente. Após a conclusão da operação de inserção, converta a tabela para registrada para que a inserção seja durável.

Em algumas cargas de trabalho de clientes, observamos um aprimoramento no desempenho de aproximadamente 15% a 20% no uso de tabelas não registradas.

## <a name="next-steps"></a>Próximas etapas
Analise sua carga de trabalho para usos de dados transitórios e inserções em massa grandes. Confira a seguinte documentação do PostgreSQL:
 
- [Criar comandos SQL de tabela](https://www.postgresql.org/docs/current/static/sql-createtable.html)
