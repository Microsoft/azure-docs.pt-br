---
title: Usar sys_schema – Banco de Dados do Azure para MySQL
description: Saiba como usar sys_schema para encontrar problemas de desempenho e manter o banco de dados no Banco de Dados do Azure para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/30/2020
ms.openlocfilehash: 38b85748ad810002dc9cfd456ef6c138e8732ece
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605477"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Como usar sys_schema para ajuste de desempenho e manutenção de banco de dados no Banco de Dados do Azure para MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

O performance_schema do MySQL, disponível primeiramente no MySQL 5.5, fornece instrumentação para muitos recursos de servidor vitais, como alocação de memória, programas armazenados, bloqueio de metadados e etc. No entanto, o performance_schema contém mais de 80 tabelas e, obter as informações necessárias muitas vezes requer junção de tabelas dentro do performance_schema, assim como tabelas do information_schema. Ao compilar o performance_schema e o information_schema, o sys_schema fornece uma coleção avançada de [exibições amigáveis de usuário](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) em um banco de dados somente leitura e é totalmente habilitado no Banco de Dados do Azure para MySQL versão 5.7.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/sys-schema-views.png" alt-text="Exibições do sys_schema":::

Há 52 exibições no sys_schema e cada uma tem um dos prefixos a seguir:

- Host_summary ou E/S: Latências relacionadas à E/S.
- InnoDB: Bloqueios e status do buffer InnoDB.
- Memória: Uso de memória pelo host e pelos usuários.
- Esquema: Informações relacionadas ao esquema, como incremento automático, índices e etc.
- Instrução: Informações sobre instruções SQL; pode ser uma instrução que resultou em verificação de tabela completa ou tempo de consulta longa.
- Usuário: Recursos consumidos e agrupados pelos usuários. Exemplos são E/S de arquivos, conexões e memória.
- Aguarde: Aguarda eventos agrupados por host ou usuário.

Agora, vejamos alguns padrões comuns de uso do sys_schema. Para começar, agruparemos os padrões de uso em duas categorias: **Ajuste de desempenho** e **Manutenção de banco de dados**.

## <a name="performance-tuning"></a>Ajuste de desempenho

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

E/S é a operação mais cara no banco de dados. É possível localizar a latência média de E/S, consultando a exibição *sys.user_summary_by_file_io*. Com o padrão de 125 GB de armazenamento provisionado, a latência de E/S é de aproximadamente 15 segundos.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-125GB.png" alt-text="Latência de E/S: 125 GB":::

Como o Banco de Dados do Azure para MySQL escala a E/S em relação ao armazenamento, após aumentar o armazenamento provisionado para 1 TB, a latência de E/S reduz para 571 ms.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-1TB.png" alt-text="Latência de E/S: 1 TB":::

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

Apesar do planejamento cuidadoso, muitas consultas ainda podem resultar em verificações de tabela completas. Para saber mais sobre os tipos de índices e como otimizá-los, confira este artigo: [Como solucionar problemas com o desempenho da consulta](./howto-troubleshoot-query-performance.md). As verificações de tabela completas são tarefas com uso intensivo de recursos e prejudicam o desempenho do banco de dados. A maneira mais rápida de localizar tabelas com verificação de tabela completa é consultar a exibição *sys.schema_tables_with_full_table_scans*.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/full-table-scans.png" alt-text="Verificações de tabela completas":::

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Para solucionar problemas de desempenho do banco de dados, pode ser útil identificar os eventos que ocorrem dentro dele e usar a exibição *sys.user_summary_by_statement_type* pode ser o ideal.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/summary-by-statement.png" alt-text="Resumo por instrução":::

Neste exemplo, o Banco de Dados do Azure para MySQL gastou 53 minutos liberando o log de consultas do slog 44579 vezes. Isso é muito tempo e muitas E/Ss. É possível reduzir essa atividade, desabilitando o log de consultas lentas ou diminuindo a frequência do logon de consultas lentas do portal do Azure.

## <a name="database-maintenance"></a>Manutenção de banco de dados

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

[!IMPORTANT]
> Consultar essa exibição pode afetar o desempenho. Recomendamos executar essa solução de problemas fora do horário comercial de pico.

O pool de buffers InnoDB reside na memória e é o mecanismo de cache principal entre o SGBD e o armazenamento. O tamanho do pool de buffers InnoDB está vinculado ao nível de desempenho e não pode ser alterado, exceto se uma SKU do produto diferente for escolhida. Assim como acontece com a memória no sistema operacional, as páginas antigas são trocadas para criar espaço para dados mais atuais. Para localizar quais tabelas consomem a maior parte da memória do pool de buffers InnoDB, você pode consultar a exibição *sys.innodb_buffer_stats_by_table*.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png" alt-text="Status do buffer InnoDB":::

No gráfico acima, é evidente que, além das exibições e tabelas do sistema, cada tabela no banco de dados mysqldatabase033, que hospeda um dos sites WordPress, ocupa 16 KB, ou 1 página, de dados na memória.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Os índices são excelentes ferramentas para melhorar o desempenho de leitura, mas eles incorrem em custos adicionais para inserções e armazenamento. *Sys.schema_unused_indexes* e *sys.schema_redundant_indexes* fornecem informações sobre índices duplicados ou não utilizados.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/unused-indexes.png" alt-text="Índices não utilizados":::

:::image type="content" source="./media/howto-troubleshoot-sys-schema/redundant-indexes.png" alt-text="Índices redundantes":::

## <a name="conclusion"></a>Conclusão

Em resumo, o sys_schema é uma ótima ferramenta para ajuste de desempenho e manutenção de banco de dados. Aproveitar esse recurso no seu Banco de Dados do Azure para MySQL. 

## <a name="next-steps"></a>Próximas etapas
- Para encontrar respostas de pares para suas perguntas mais interessadas ou postar uma nova pergunta/resposta, visite [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
