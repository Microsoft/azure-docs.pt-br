---
title: Recomendações de desempenho - Banco de dados SQL do Azure | Microsoft Docs
description: O Banco de Dados SQL do Azure fornece recomendações para seus Bancos de Dados SQL que podem melhorar o desempenho de consulta atual.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: d09adbfa7cb2782d710ef3116cbd7bc68ee247b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61417576"
---
# <a name="performance-recommendations-for-sql-database"></a>Recomendações de desempenho do Banco de Dados SQL

O Banco de Dados SQL do Azure aprende e se adapta ao seu aplicativo. Ele fornece recomendações personalizadas que permitem que você maximize o desempenho de seus bancos de dados SQL. O Banco de Dados SQL avalia e analisa continuamente o histórico de uso de bancos de dados SQL. As recomendações fornecidas são baseadas em padrões de carga de trabalho exclusiva do banco de dados e ajudam a melhorar o desempenho.

> [!TIP]
> [O ajuste automático](sql-database-automatic-tuning.md) é o método recomendado para ajustar automaticamente alguns dos problemas mais comuns de desempenho do banco de dados. [Análise de desempenho de consulta](sql-database-query-performance.md) é o método recomendado para necessidades de monitoramento de desempenho de Banco de Dados SQL do Microsoft Azure. [Análise de SQL do Azure](../azure-monitor/insights/azure-sql.md) é o método recomendado para monitoramento de desempenho do banco de dados avançado em grande escala, com inteligência interna para solução de problemas de desempenho automatizado.
>

## <a name="create-index-recommendations"></a>Criar recomendações de índice
O Banco de Dados SQL monitora continuamente as consultas que estão sendo executadas e identifica os índices que podem melhorar o desempenho. Após haver certeza suficiente de que um determinado índice está ausente, uma nova recomendação **Criar índice** é criada.

 O Banco de Dados SQL do Azure aumenta a confiança estimando o ganho de desempenho que o índice trará ao longo do tempo. Dependendo do ganho de desempenho estimado, as recomendações são categorizadas como alta, média ou baixa. 

Índices criados usando recomendações sempre são sinalizados como índices criados automaticamente. Você pode ver quais índices são criados automaticamente examinando a exibição sys.indexes. Índices criados automaticamente não bloqueiam os comandos ALTER/RENAME. 

Se você tentar descartar a coluna que tem um índice criado automaticamente por ela, o comando é passado. O índice criado automaticamente é descartado com o comando também. Índices regulares bloqueiam o comando ALTER/RENAME em colunas que são indexadas.

Depois que a recomendação para criar índice for aplicada, o Banco de Dados SQL do Azure comparará o desempenho das consultas com o desempenho de linha de base. Se o novo índice melhorou o desempenho, a recomendação é sinalizada como bem-sucedida e o relatório de impacto fica disponível. Se o índice não melhorou o desempenho, ele é revertido automaticamente. O Banco de Dados SQL usa este processo para garantir que as recomendações melhorem o desempenho do banco de dados.

Qualquer recomendação **criar índice** tem uma política de retirada que não permite aplicar a recomendação se o uso de recursos de um banco de dados ou pool estiver alto. A política de retirada leva em conta CPU, E/S de Dados, E/S de Log e armazenamento disponível. 

Se CPU, E/S de Dados ou Log de E/S for maior do que 80% nos 30 minutos anteriores, a recomendação para criar índice será adiada. Se o armazenamento disponível for inferior a 10% após o índice ser criado, a recomendação entrará em estado de erro. Se depois de alguns dias o ajuste automático ainda acreditar que o índice pode ser benéfico, o processo será iniciado novamente. 

Esse processo se repetirá até que não haja armazenamento suficiente disponível para criar um índice ou até que o índice não seja visto mais como benéfico.

## <a name="drop-index-recommendations"></a>Recomendações para Remover Índice
Além de detectar um índice ausente, o Banco de Dados SQL analisa continuamente o desempenho dos índices existentes. Se um índice não for usado, o banco de dados SQL do Azure recomendará cancelá-lo. Descartar um índice é recomendado em dois casos:
* O índice é uma duplicata de outro índice (mesma coluna indexada e incluída, esquema de partição e filtros).
* O índice não foi usado por um período prolongado (93 dias).

Recomendações para Remover Índice também passam por verificação após a implementação. Se o desempenho melhorar, o relatório de impacto ficará disponível. Se o desempenho diminuir, a recomendação será revertida.


## <a name="parameterize-queries-recommendations"></a>Recomendações para parametrizar consultas
Recomendações para *Parametrizar consultas* aparecem quando o serviço de Banco de Dados SQL detecta que você tem uma ou mais consultas que estão constantemente sendo recompiladas, mas terminam com o mesmo plano de execução de consulta. Essa condição cria uma oportunidade para aplicar a parametrização forçada. A parametrização forçada, por sua vez, permite que os planos de consulta sejam armazenadas em cache e reutilizados no futuro, o que melhora o desempenho e reduz o uso de recursos. 

Cada consulta que é emitida para o SQL Server inicialmente precisa ser compilada para gerar um plano de execução. Cada plano gerado é adicionado ao cache de planos. As execuções subsequentes da mesma consulta poderão reutilizar esse plano pelo cache, o que elimina a necessidade de compilações adicionais. 

Consultas com valores não parametrizados podem gerar sobrecarga de desempenho porque o plano de execução é recompilado toda vez que os valores não parametrizados são diferentes. Em muitos casos, as mesmas consultas com valores de parâmetros diferentes geram os mesmos planos de execução. No entanto, esses planos ainda são adicionados separadamente ao cache de planos. 

O processo de recompilação de planos de execução usa recursos do banco de dados, aumenta o tempo de duração da consulta e sobrecarrega o cache de planos. Esses eventos, por sua vez, fazem com que os planos sejam removido do cache. Esse comportamento do SQL Server pode ser alterado ao definir a opção de parametrização forçada no banco de dados. 

Para ajudá-lo a estimar o impacto dessa recomendação, você receberá uma comparação entre o uso real da CPU e o uso projetado da CPU (como se a recomendação tivesse sido aplicada). Essa recomendação pode ajudá-lo a obter uma economia de CPU. Ela também pode ajudar a reduzir a duração da consulta e a sobrecarga para o cache de planos, o que significa que mais dos planos podem permanecer no cache e ser reutilizados. Você pode aplicar essa recomendação com rapidez selecionando o comando **Aplicar**. 

Depois de aplicar essa recomendação, ele permite a parametrização forçada em minutos no banco de dados. Ele inicia o processo de monitoramento, que dura aproximadamente 24 horas. Após esse período, você pode ver o relatório de validação. Este relatório mostra o uso da CPU de seu banco de dados 24 horas antes e depois que a recomendação foi aplicada. O Assistente do Banco de Dados SQL tem um mecanismo de segurança que reverte a recomendação aplicada automaticamente se uma redução de desempenho seja detectada.

## <a name="fix-schema-issues-recommendations-preview"></a>Recomendações para corrigir problemas do esquema (versão prévia)

> [!IMPORTANT]
> A Microsoft está atualmente substituindo recomendações do tipo "Corrigir problema de esquema". Recomendamos que você use o [Intelligent Insights](sql-database-intelligent-insights.md) para monitorar seus problemas de desempenho do banco de dados, incluindo problemas de esquema que as recomendações de "Corrigir problemas do esquema" abordaram anteriormente.
> 

Recomendações para **Corrigir problemas do esquema** aparecem quando o serviço de Banco de Dados SQL observa uma anomalia no número erros de SQL relacionados ao esquema que estão ocorrendo no Banco de Dados SQL. Essa recomendação normalmente aparece quando seu banco de dados encontra vários erros relacionados ao esquema (nome da coluna inválido, nome do objeto inválido e assim por diante) no período de uma hora.

“Problemas do esquema” são uma classe de erros de sintaxe no SQL Server. Eles ocorrem quando a definição da consulta SQL e a definição do esquema do banco de dados não estão alinhadas. Por exemplo, uma das colunas esperadas pela consulta pode estar ausente na tabela de destino ou vice-versa. 

A recomendação para “Corrigir problemas do esquema” aparece quando o serviço de Banco de Dados SQL do Azure observa uma anomalia no número erros de SQL relacionados ao esquema ocorrendo no Banco de Dados SQL. A tabela a seguir mostra os erros relacionados a problemas de esquema:

| Código do erro SQL | Mensagem |
| --- | --- |
| 201 |Procedimento ou função '*' espera o parâmetro '*', que não foi fornecido. |
| 207 |Nome de coluna inválido '*'. |
| 208 |Nome de objeto inválido '*'. |
| 213 |O nome da coluna ou o número de valores fornecidos não corresponde à definição da tabela. |
| 2812 |Não foi possível encontrar o procedimento armazenado '*'. |
| 8144 |Função ou procedimento * tem muitos argumentos especificados. |

## <a name="custom-applications"></a>Aplicativos personalizados

Os desenvolvedores podem considerar desenvolvendo aplicativos personalizados usando as recomendações de desempenho para o banco de dados SQL. Todas as recomendações listadas no portal para um banco de dados pode ser acessado por meio [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API.

## <a name="next-steps"></a>Próximas etapas
Monitore suas recomendações e continue a aplicá-las para refinar o desempenho. Cargas de trabalho de banco de dados são dinâmicas e mudam continuamente. O Assistente do Banco de Dados SQL continua a monitorar e fornecer recomendações que podem potencialmente melhorar o desempenho do seu banco de dados. 

* Para obter mais informações sobre ajuste automático de índices do banco de dados e planos de execução de consulta, consulte [Ajuste automático do banco de dados SQL do Azure](sql-database-automatic-tuning.md).
* Para mais informações sobre monitoramento automático do desempenho do banco de dados com diagnóstico automatizado e análise de causa raiz de problemas de desempenho, consulte [Intelligent Insights do SQL Azure](sql-database-intelligent-insights.md).
*  Para mais informações sobre como usar as recomendações de desempenho no portal do Azure, confira [Recomendações de desempenho no portal do Azure](sql-database-advisor-portal.md).
* Consulte [Análise de desempenho de consultas](sql-database-query-performance.md) para saber mais e visualizar o impacto no desempenho de suas principais consultas.


