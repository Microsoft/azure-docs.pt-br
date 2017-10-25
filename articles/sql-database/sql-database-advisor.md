---
title: "Recomendações de desempenho - Banco de dados SQL do Azure | Microsoft Docs"
description: "O Banco de Dados SQL do Azure fornece recomendações para seus Bancos de Dados SQL que podem melhorar o desempenho de consulta atual."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/20/2017
ms.author: sstein
ms.openlocfilehash: 53bfbd602c2c395d510529eacd5b8075b20437ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="performance-recommendations"></a>Recomendações do desempenho

O Banco de Dados SQL do Azure aprende e adapta-se ao seu aplicativo e fornece recomendações personalizadas permitindo que você maximize o desempenho dos bancos de dados SQL. O desempenho é avaliado continuamente analisando seu histórico de uso do Banco de Dados SQL. As recomendações fornecidas são baseadas em um padrão de carga de trabalho exclusiva do banco de dados e ajudam a melhorar o desempenho.

> [!TIP]
> [Ajuste automático](sql-database-automatic-tuning.md) é a maneira recomendada de ajustar o desempenho. [Insights inteligentes](sql-database-intelligent-insights.md) é a maneira recomendada de monitoramento de desempenho. 
>

## <a name="create-index-recommendations"></a>Criar recomendações de índice
O Banco de Dados SQL do Azure monitora continuamente as consultas que estão sendo executadas e identifica os índices que podem melhorar o desempenho. Após haver certeza suficiente de que determinado índice está ausente, uma nova recomendação **Criar índice** será criada. O Banco de Dados SQL do Azure aumenta a confiança estimando o ganho de desempenho que o índice trará ao longo do tempo. Dependendo do ganho de desempenho estimado, as recomendações são categorizadas como alta, média ou baixa. 

Índices criados usando as recomendações sempre são sinalizados como índices auto_created. Você pode ver quais índices são auto_created examinando a exibição sys.indexes. Índices criados automaticamente não bloqueiam os comandos ALTER/RENAME. Se você tentar remover a coluna que tem um índice criado automaticamente, o comando passará o índice criado automaticamente será descartado com o comando também. Índices regulares bloqueariam o comando ALTER/RENAME em colunas que são indexadas.

Depois que a recomendação para criar índice for aplicada, o Banco de Dados do Azure SQL comparará o desempenho das consultas ao desempenho de linha de base. Se o novo índice tiver oferecido melhorias de desempenho, a recomendação será sinalizada como bem-sucedida e o relatório de impacto estará disponível. Caso o índice não traga os benefícios, ele será revertido automaticamente. Dessa forma, o Banco de Dados SQL do Azure garante que o uso das recomendações só melhore o desempenho do banco de dados.

Qualquer recomendação **Criar Índice** tem uma política que não permite aplicar a recomendação se o uso de DTU do banco de dados ou o pool estiver acima de 80% nos últimos 20 minutos ou se o armazenamento estiver acima de 90% de uso. Nesse caso, a recomendação será adiada.

## <a name="drop-index-recommendations"></a>Recomendações para Remover Índice
Além de detectar um índice ausente, o banco de dados do SQL Azure analisa continuamente o desempenho dos índices existentes. Se o índice não for usado, o banco de dados do SQL Azure recomendará cancelá-lo. Descartar um índice é recomendado em dois casos:
* O índice é uma duplicata de outro índice (mesma coluna indexada e incluída, esquema de partição e filtros)
* O índice é não utilizado por um período prolongado (93 dias)

Recomendações para Remover Índice também passam por verificação após a implementação. Se o desempenho for melhorado, o relatório de impacto estará disponível. Caso uma degradação do desempenho seja detectada, a recomendação será revertida.


## <a name="parameterize-queries-recommendations"></a>Recomendações para parametrizar consultas
Recomendações para **Parametrizar consultas** aparecem quando o serviço de Banco de Dados SQL detecta que você tem uma ou mais consultas que estão constantemente sendo recompiladas, mas terminam com o mesmo plano de execução de consulta. Essa condição abre uma oportunidade de aplicar parametrização forçada, o que permitirá que os planos de consulta sejam armazenados em cache e reutilizados no futuro, melhorando o desempenho e reduzindo o uso de recursos. 

Cada consulta emitida para o servidor SQL inicialmente precisa ser compilada para gerar um plano de execução. Cada plano gerado é adicionado ao cache do plano e as execuções subsequentes da mesma consulta poderão reutilizar esse plano pelo cache, eliminando a necessidade de compilações adicionais. 

Aplicativos que enviam consultas, que incluem valores sem parâmetros, podem causar uma sobrecarga de desempenho, no qual o plano de execução é compilado novamente para cada consulta com valores de parâmetros diferentes. Em muitos casos, as mesmas consultas com valores de parâmetros diferentes geram os mesmos planos de execução, mas esses planos são adicionados separadamente ao cache do plano. Os planos de execução de recompilação usam recursos do banco de dados, aumentam o tempo de duração da consulta e sobrecarregam o cache do plano, fazendo com que planos sejam removidos do cache. Esse comportamento do servidor SQL pode ser alterado ao definir a opção de parametrização forçada no banco de dados. 

Para ajudá-lo a estimar o impacto dessa recomendação, você receberá uma comparação entre o uso real da CPU e o uso projetado da CPU (como se a recomendação tivesse sido aplicada). Além poupar a CPU, a duração da consulta diminui pelo tempo gasto na compilação. A sobrecarga no cache do plano também será significativamente reduzida, permitindo que a maioria dos planos permaneça no cache e seja reutilizada. Você pode aplicar essa recomendação com rapidez e facilidade clicando no comando **Aplicar**. 

Depois de aplicar essa recomendação, ela habilitará a parametrização forçada em seu banco de dados em minutos e iniciará o processo de monitoramento, que dura aproximadamente 24 horas. Após esse período, você poderá ver o relatório de validação que mostra o uso da CPU do seu banco de dados 24 horas antes e depois da aplicação da recomendação. O Assistente do Banco de Dados SQL tem um mecanismo de segurança que reverte a recomendação aplicada automaticamente caso uma redução de desempenho seja detectada.

## <a name="fix-schema-issues-recommendations"></a>Recomendações para corrigir problemas do esquema
Recomendações para **Corrigir problemas do esquema** aparecem quando o serviço de Banco de Dados SQL observa uma anomalia no número erros de SQL relacionados ao esquema ocorrendo no Banco de Dados SQL do Azure. Essa recomendação normalmente aparece quando seu banco de dados encontra vários erros relacionados ao esquema (nome da coluna inválido, nome do objeto inválido etc.) no período de uma hora.

"Problemas de esquema" são uma classe de erros de sintaxe no servidor SQL que ocorrem quando a definição da consulta SQL e a definição do esquema do banco de dados não estão alinhadas. Por exemplo, uma das colunas esperadas pela consulta pode estar ausente na tabela de destino ou vice-versa. 

Recomendações para “Corrigir problemas do esquema” aparecem quando o serviço de Banco de Dados SQL do Azure observa uma anomalia no número erros de SQL relacionados ao esquema ocorrendo no Banco de Dados SQL do Azure. A tabela a seguir mostra os erros relacionados a problemas de esquema:

| Código do Erro SQL | Mensagem |
| --- | --- |
| 201 |Procedimento ou função '*' espera o parâmetro '*', que não foi fornecido. |
| 207 |Nome de coluna inválido '*'. |
| 208 |Nome de objeto inválido '*'. |
| 213 |O nome da coluna ou o número de valores fornecidos não corresponde à definição da tabela. |
| 2812 |Não foi possível encontrar o procedimento armazenado '*'. |
| 8144 |Função ou procedimento * tem muitos argumentos especificados. |

## <a name="next-steps"></a>Próximas etapas
Monitore suas recomendações e continue a aplicá-las para refinar o desempenho. Cargas de trabalho de banco de dados são dinâmicas e mudam continuamente. O assistente do Banco de Dados SQL continua a monitorar e fornecer recomendações que podem potencialmente melhorar o desempenho do seu banco de dados. 

* Consulte [Ajuste automático do banco de dados SQL do Azure](sql-database-automatic-tuning.md) para ajuste automático de índices do banco de dados e planos de execução de consulta.
* Consulte [Insights inteligentes do SQL Azure](sql-database-intelligent-insights.md) para monitorar automaticamente o desempenho do banco de dados com diagnóstico automatizado e análise de causa raiz de problemas de desempenho.
* Confira [Recomendações de desempenho no portal do Azure](sql-database-advisor-portal.md) para obter etapas sobre como usar as recomendações de desempenho no portal do Azure.
* Consulte [Análise de desempenho de consultas](sql-database-query-performance.md) para saber mais e visualizar o impacto no desempenho de suas principais consultas.


