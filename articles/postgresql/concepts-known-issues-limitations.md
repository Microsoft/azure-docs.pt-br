---
title: Problemas conhecidos e limitações – Banco de Dados do Azure para PostgreSQL – servidor único e servidor flexível (versão prévia)
description: Lista os problemas conhecidos dos quais os clientes devem estar cientes.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 3c26368e6281ad74d617891ba15c980117b25a6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100106389"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Banco de Dados do Azure para PostgreSQL – Problemas conhecidos e limitações

Esta página fornece uma lista de problemas conhecidos do Banco de Dados do Azure para PostgreSQL que podem afetar seu aplicativo. Também lista opções de mitigação e recomendações para solucionar o problema.

## <a name="intelligent-performance---query-store"></a>Desempenho inteligente – Repositório de Consultas

Aplicável ao Banco de Dados do Azure para PostgreSQL – servidor único.

| Aplicável | Causa | Remediação|
| ----- | ------ | ---- | 
| PostgreSQL 9.6, 10 e 11 | A ativação do parâmetro do servidor `pg_qs.replace_parameter_placeholders` pode levar a um desligamento do servidor em alguns cenários raros. | Pelo portal do Azure, na seção Parâmetros do Servidor, transforme o valor do parâmetro `pg_qs.replace_parameter_placeholders` em `OFF` e salve-o.   | 

## <a name="server-parameters"></a>Parâmetros do Servidor

Aplicável ao Banco de Dados do Azure para PostgreSQL – servidor único e servidor flexível

| Aplicável | Causa | Remediação| 
| ----- | ------ | ---- | 
| PostgreSQL 9.6, 10 e 11 | A alteração do parâmetro do servidor `max_locks_per_transaction` para um valor mais alto do que o [recomendado](https://www.postgresql.org/docs/11/kernel-resources.html) pode levar à não inicialização do servidor após uma reinicialização. | Mantenha-o com o valor padrão (32 ou 64) ou altere-o para um valor razoável de acordo com a [documentação](https://www.postgresql.org/docs/11/kernel-resources.html) do PostgreSQL. <br> <br> No lado do serviço, isso está sendo trabalhado para limitar o valor alto com base no SKU.  | 

## <a name="next-steps"></a>Próximas etapas
- Confira [Melhores práticas para o Repositório de Consultas](./concepts-query-store-best-practices.md)
