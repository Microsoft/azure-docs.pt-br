---
title: Problemas conhecidos e limitações – banco de dados do Azure para PostgreSQL-servidor único e servidor flexível (visualização)
description: Lista os problemas conhecidos dos quais os clientes devem estar cientes.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 3c26368e6281ad74d617891ba15c980117b25a6e
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106389"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Banco de dados do Azure para PostgreSQL – problemas conhecidos e limitações

Esta página fornece uma lista de problemas conhecidos no banco de dados do Azure para PostgreSQL que podem afetar seu aplicativo. Ele também lista qualquer mitigação e recomendações para solucionar o problema.

## <a name="intelligent-performance---query-store"></a>Desempenho inteligente-Repositório de Consultas

Aplicável ao banco de dados do Azure para PostgreSQL-servidor único.

| Se | Causa | Remediação|
| ----- | ------ | ---- | 
| PostgreSQL 9,6, 10, 11 | A ativação do parâmetro do servidor `pg_qs.replace_parameter_placeholders` pode levar a um desligamento do servidor em alguns cenários raros. | Por meio do portal do Azure, seção de parâmetros do servidor, transforme o valor do parâmetro `pg_qs.replace_parameter_placeholders` `OFF` e salve-o.   | 

## <a name="server-parameters"></a>Parâmetros do Servidor

Aplicável ao banco de dados do Azure para PostgreSQL-servidor único e servidor flexível

| Se | Causa | Remediação| 
| ----- | ------ | ---- | 
| PostgreSQL 9,6, 10, 11 | Alterar o parâmetro de servidor `max_locks_per_transaction` para um valor mais alto do que o [recomendado](https://www.postgresql.org/docs/11/kernel-resources.html) pode levar ao servidor que não pode surgir após uma reinicialização. | Deixe-o com o valor padrão (32 ou 64) ou altere para um valor razoável por [documentação](https://www.postgresql.org/docs/11/kernel-resources.html)do PostgreSQL. <br> <br> No lado do serviço, isso está sendo trabalhado para limitar o valor alto com base na SKU.  | 

## <a name="next-steps"></a>Próximas etapas
- Confira Repositório de Consultas [práticas recomendadas](./concepts-query-store-best-practices.md)
