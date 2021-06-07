---
title: Solucionar problemas de desempenho com o Intelligent Insights
description: Intelligent Insights ajuda a solucionar problemas de desempenho do banco de dados SQL do Azure e do Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: troubleshooting
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: 17ea6716f090144e8dfef16721bfb69dc23e9912
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589319"
---
# <a name="troubleshoot-azure-sql-database-and-azure-sql-managed-instance-performance-issues-with-intelligent-insights"></a>Solucionar problemas de desempenho do banco de dados SQL do Azure e do Azure SQL Instância Gerenciada com Intelligent Insights
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Esta página fornece informações sobre os problemas de desempenho do banco de dados SQL do Azure e do Azure SQL Instância Gerenciada detectados por meio do log de recursos do [Intelligent insights](intelligent-insights-overview.md) . Métricas e logs de recursos podem ser transmitidos para [Azure monitor logs](../../azure-monitor/insights/azure-sql.md), [hubs de eventos do Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs), [armazenamento do Azure](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)ou uma solução de terceiros para recursos personalizados de alertas e relatórios de DevOps.

> [!NOTE]
> Para obter um guia de solução de problemas de desempenho rápido usando Intelligent Insights, consulte o fluxograma de [fluxo de problemas recomendado](intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) neste documento.
>
> O insights inteligentes é um recurso de visualização, não disponível nas seguintes regiões: Europa Ocidental, Europa Setentrional, oeste dos EUA 1 e leste dos EUA 1.

## <a name="detectable-database-performance-patterns"></a>Padrões detectáveis de desempenho do banco de dados

Intelligent Insights detecta automaticamente problemas de desempenho com base em tempos de espera de execução de consulta, erros ou tempos limite. Intelligent Insights gera padrões de desempenho detectados para o log de recursos. Padrões de desempenho detectáveis estão resumidos na tabela a seguir.

| Padrões de desempenho detectáveis | Banco de Dados SQL do Azure | Instância Gerenciada do Azure SQL |
| :------------------- | ------------------- | ------------------- |
| [Atingindo os limites do recurso](intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | O consumo de recursos disponíveis (DTUs), threads de trabalho de banco de dados ou sessões de logon de banco de dados disponíveis na assinatura monitorado atingiu seus limites de recursos. Isso está afetando o desempenho. | O consumo de recursos de CPU está atingindo seus limites de recursos. Isso está afetando o desempenho do banco de dados. |
| [Aumento da carga de trabalho](intelligent-insights-troubleshoot-performance.md#workload-increase) | Foi detectado aumento da carga de trabalho ou acumulação contínua de carga de trabalho no banco de dados. Isso está afetando o desempenho. | Foi detectado um aumento da carga de trabalho. Isso está afetando o desempenho do banco de dados. |
| [Demanda de memória](intelligent-insights-troubleshoot-performance.md#memory-pressure) | Os trabalhadores que solicitaram concessões de memória precisam aguardar as alocações de memória para quantidades estatisticamente significativas, ou um aumento de acumulação de trabalhos que solicitou concessão de memória existe. Isso está afetando o desempenho. | Os operadores que solicitaram concessões de memória estão esperando alocações de memória para quantidades de tempo estatisticamente significativas. Isso está afetando o desempenho do banco de dados. |
| [Bloqueio](intelligent-insights-troubleshoot-performance.md#locking) | Foi detectado um bloqueio excessivo de banco de dados afetando o desempenho. | Bloqueio de banco de dados excessivo foi detectado, afetando o desempenho do banco de dados. |
| [Aumento de MAXDOP](intelligent-insights-troubleshoot-performance.md#increased-maxdop) | A opção de grau máximo de paralelismo (MAXDOP) foi alterada, afetando a eficiência da execução da consulta. Isso está afetando o desempenho. | A opção de grau máximo de paralelismo (MAXDOP) foi alterada, afetando a eficiência da execução da consulta. Isso está afetando o desempenho. |
| [Contenção de pagelatch](intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Vários threads estão tentando, simultaneamente, acessar as mesmas páginas de buffer de dados na memória, resultando em maior tempo de espera, causando contenção de pagelatch. Isso está afetando o desempenho. | Vários threads estão tentando, simultaneamente, acessar as mesmas páginas de buffer de dados na memória, resultando em maior tempo de espera, causando contenção de pagelatch. Isso está afetando o desempenho do banco de dados. |
| [Índice Ausente](intelligent-insights-troubleshoot-performance.md#missing-index) | O índice ausente foi detectado, afetando o desempenho. | Foi detectada a ausência do índice, afetando o desempenho do banco de dados. |
| [Nova Consulta](intelligent-insights-troubleshoot-performance.md#new-query) | Foi detectada uma nova consulta afetando o desempenho geral. | Foi detectada nova consulta que afeta o desempenho geral do banco de dados. |
| [Aumento da Estatística de Espera](intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Foram detectados tempos de espera de banco de dados maiores que afetam o desempenho. | Foi detectado um aumento dos tempos de espera, afetando o desempenho do banco de dados. |
| [Contenção de TempDB](intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Vários threads estão tentando acessar os mesmos recursos de TempDB, provocando um gargalo. Isso está afetando o desempenho. | Vários threads estão tentando acessar os mesmos recursos de TempDB, provocando um gargalo. Isso está afetando o desempenho do banco de dados. |
| [Escassez de DTU do pool elástico](intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | A escassez de eDTUs disponíveis no pool elástico está afetando o desempenho. | Não disponível para o Azure SQL Instância Gerenciada porque ele usa o modelo vCore. |
| [Regressão do plano](intelligent-insights-troubleshoot-performance.md#plan-regression) | Foi detectado um novo plano ou uma alteração na carga de trabalho de um plano existente. Isso está afetando o desempenho. | Foi detectado um novo plano ou uma alteração na carga de trabalho de um plano existente. Isso está afetando o desempenho do banco de dados. |
| [Alteração do valor de configuração no escopo do banco de dados](intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Foi detectada uma alteração de configuração no banco de dados que está afetando seu desempenho. | Foi detectada uma alteração de configuração no banco de dados que está afetando seu desempenho. |
| [Cliente lento](intelligent-insights-troubleshoot-performance.md#slow-client) | O cliente de aplicativo lento não pode consumir a saída do banco de dados rápido o suficiente. Isso está afetando o desempenho. | O cliente de aplicativo lento não pode consumir a saída do banco de dados rápido o suficiente. Isso está afetando o desempenho do banco de dados. |
| [Downgrade de tipo de preço](intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | A ação de downgrade do tipo de preço diminuiu os recursos disponíveis. Isso está afetando o desempenho. | A ação de downgrade do tipo de preço diminuiu os recursos disponíveis. Isso está afetando o desempenho do banco de dados. |

> [!TIP]
> Para otimizar o desempenho contínuo de bancos de dados, habilite o [ajuste automático](automatic-tuning-overview.md). Esse recurso de inteligência interna monitora continuamente o banco de dados, ajusta os índices automaticamente e aplica as correções do plano de execução de consulta.
>

A seção a seguir descreve os padrões de desempenho detectáveis com mais detalhes.

## <a name="reaching-resource-limits"></a>Atingindo os limites do recurso

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável combina problemas de desempenho relacionados a alcançar os limites de recurso, limites de trabalho e limites de sessão disponíveis. Depois que esse problema de desempenho é detectado, um campo de descrição do log de diagnóstico indica se o problema de desempenho está relacionado a limites de recurso, trabalho ou sessão.

Os recursos no banco de dados SQL do Azure são geralmente referidos a recursos de [DTU](service-tiers-dtu.md) ou [vCore](service-tiers-vcore.md) , e os recursos no Azure SQL instância gerenciada são chamados de recursos vCore. O padrão de alcance dos de limites do recurso é reconhecido quando uma degradação de desempenho de consulta detectada é causada por qualquer um dos limites de recurso medidos.

O recurso de limites de sessão denota o número de logons simultâneos disponíveis para o banco de dados. Esse padrão de desempenho é reconhecido quando os aplicativos que estão conectados aos bancos de dados atingiram o número de logons simultâneos disponíveis para ele. Se aplicativos tentarem usar mais sessões do que as disponíveis em um banco de dados, o desempenho da consulta será afetado.

O alcance dos limites de trabalho é um caso específico de atingir os limites do recurso, pois trabalhadores disponíveis não são contados no uso de DTU ou vCore. O alcance dos limites de trabalho em um banco de dados pode causar o surgimento de tempos de espera específicos do recurso, o que resulta em uma degradação no desempenho da consulta.

### <a name="troubleshooting"></a>Solução de problemas

O log de diagnóstico gera hashes de consulta das consultas que afetaram o desempenho e as porcentagens de consumo do recurso. Use estas informações como ponto de partida para otimizar a carga de trabalho do banco de dados. Em particular, você pode otimizar as consultas que afetam a degradação do desempenho adicionando índices. Ou você pode otimizar aplicativos com uma distribuição de carga de trabalho mais uniforme. Se não for possível reduzir as cargas de trabalho ou fazer otimizações, considere aumentar o tipo de preço da sua assinatura de banco de dados para aumentar a quantidade de recursos disponíveis.

Se você tiver atingido os limites de sessão disponíveis, poderá otimizar seus aplicativos reduzindo o número de logons feitos no banco de dados. Se não for possível reduzir o número de logons de seus aplicativos para o banco de dados, considere aumentar o tipo de preço da sua assinatura de banco de dados. Ou você pode dividir e mover o banco de dados em vários bancos de dados para uma distribuição mais balanceada de carga de trabalho.

Para obter mais sugestões sobre como resolver limites de sessão, consulte [como lidar com os limites de logons máximos](/archive/blogs/latam/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins). Consulte [visão geral dos limites de recursos em um servidor](resource-limits-logical-server.md) para obter informações sobre os limites nos níveis de servidor e assinatura.

## <a name="workload-increase"></a>Aumento da carga de trabalho

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho identifica problemas causados por aumento da carga de trabalho ou sua forma mais grave, empilhamento de carga de trabalho.

Essa detecção é feita por uma combinação de várias métricas. A métrica básica medida está detectando um aumento na carga de trabalho em comparação com a linha de base de carga de trabalho anterior. A outra forma de detecção baseia-se em avaliar um grande aumento nos threads de trabalho ativos, grande o suficiente para afetar o desempenho da consulta.

Em sua forma mais grave, a carga de trabalho pode ser continuamente compilada devido à incapacidade de um banco de dados de lidar com a carga de trabalho. O resultado é o aumento contínuo do tamanho da carga de trabalho, que é a condição de empilhamento da carga de trabalho. Devido a essa condição, o tempo que a carga de trabalho espera para execução aumenta. Essa condição representa um dos mais graves problemas de desempenho do banco de dados. Esse problema é detectado por meio de monitoramento de aumento no número de threads de trabalho anulados.

### <a name="troubleshooting"></a>Solução de problemas

O log de diagnóstico gera o número de consultas cuja execução aumentou e o hash de consulta da consulta que mais contribuiu para o aumento da carga de trabalho. Use estas informações como ponto de partida para otimizar a carga de trabalho. A consulta identificada como principal colaboradora do aumento da carga de trabalho é especialmente útil como ponto de partida.

Considere também distribuir as cargas de trabalho de maneira mais uniforme para o banco de dados. Considere otimizar a consulta que está afetando o desempenho adicionando índices. Você também pode distribuir sua carga de trabalho entre vários bancos de dados. Se essas soluções não forem possíveis, considere aumentar o tipo de preço da sua assinatura de banco de dados para aumentar a quantidade de recursos disponíveis.

## <a name="memory-pressure"></a>Demanda de memória

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho indica degradação no desempenho do banco de dados atual devido à pressão de memória ou, em sua forma mais grave, uma condição de empilhamento de memória, em comparação com a linha de base de desempenho dos últimos sete dias.

A pressão de memória denota uma condição de desempenho na qual há um grande número de threads de trabalho solicitando concessões de memória. O alto volume causa uma alta condição de utilização de memória na qual o banco de dados não pode alocar a memória com eficiência para todos os trabalhos que a solicitam. Um dos motivos mais comuns para esse problema está relacionado à quantidade de memória disponível para o banco de dados por um lado. Por outro lado, um aumento na carga de trabalho provoca o aumento nos threads de trabalho e na pressão de memória.

A forma mais grave de pressão de memória é a condição de empilhamento de memória. Essa condição indica que há um número maior de threads de trabalho solicitando concessões de memória do que o número de consultas que estão liberando a memória. Esse número de threads de trabalho que solicitam concessões de memória também pode ser continuamente aumentado (empilhando up) porque o mecanismo de banco de dados não pode alocar memória com eficiência suficiente para atender à demanda. A condição de empilhamento de memória representa um dos mais graves problemas de desempenho do banco de dados.

### <a name="troubleshooting"></a>Solução de problemas

O log de diagnóstico gera os detalhes de repositório do objeto na memória, com o administrador (ou seja, o thread de trabalho) marcado como o motivo principal de alto uso de memória e os carimbos de data/hora relevantes. Você pode usar essa informação como a base para a solução de problemas.

Você pode otimizar ou remover consultas relacionadas aos administradores com o uso de memória mais alto. Você também pode garantir que não esteja consultando dados que você não planeje usar. A prática recomendada é sempre usar uma cláusula WHERE em suas consultas. Além disso, é recomendável criar índices não clusterizados para buscar os dados, em vez de examiná-lo.

Você também pode reduzir a carga de trabalho otimizando-a ou distribuindo-a em vários bancos de dados. Ou você pode distribuir sua carga de trabalho entre vários bancos de dados. Se essas soluções não forem possíveis, considere aumentar o tipo de preço do seu banco de dados para aumentar a quantidade de recursos de memória disponíveis para o banco de dados.

Para sugestões adicionais de solução de problemas, consulte [Reflexão sobre concessões de memória: o misterioso consumidor de memória do SQL Server com vários nomes](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-meditation-the-mysterious-sql-server-memory/ba-p/333994).

## <a name="locking"></a>Bloqueio

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho indica degradação no desempenho do banco de dados atual em que foi detectado bloqueio excessivo do banco de dados em comparação com a linha de base de desempenho dos últimos sete dias.

Em RDBMS modernos, o bloqueio é essencial para a implementação de sistemas multithread em que o desempenho é maximizado por meio da execução simultânea de várias transações paralelas de banco de dados e trabalho, quando possível. O bloqueio, neste contexto, refere-se ao mecanismo de acesso interno no qual apenas uma única transação pode acessar com exclusividade linhas, páginas, tabelas e arquivos necessários e não competir com outra transação por recursos. Quando a transação que bloqueou os recursos para uso termina de usá-los, o bloqueio desses recursos é liberado, o que permite que outras transações acessem os recursos necessários. Para obter mais informações sobre bloqueio, consulte [Bloqueio no mecanismo de banco de dados](/previous-versions/sql/sql-server-2008-r2/ms190615(v=sql.105)).

Se as transações executadas pelo SQL Engine estiverem aguardando por longos períodos para acessar recursos bloqueados para uso, esse tempo de espera afetará o desempenho de execução da carga de trabalho.

### <a name="troubleshooting"></a>Solução de problemas

O log de diagnóstico gera detalhes de bloqueio que você pode usar como base para solução de problemas. Você pode analisar as consultas de bloqueio relatadas, ou seja, as consultas que apresentam a degradação de desempenho de bloqueio e removê-las. Em alguns casos, talvez a otimização de consultas de bloqueio seja bem-sucedida.

A maneira mais simples e segura de atenuar o problema é manter as transações curtas e reduzir a superfície de bloqueio das consultas mais caras. Você pode dividir um lote grande de operações em operações menores. Uma melhor prática é reduzir a superfície de bloqueio de consulta, tornando a consulta o mais eficiente possível. Reduza verificações grandes, pois elas aumentam as chances de deadlocks e afetam negativamente o desempenho geral do banco de dados. Para consultas identificadas que causam o bloqueio, você pode criar novos índices ou adicionar colunas ao índice existente para evitar as verificações de tabela.

Para obter mais sugestões, consulte:
- [Entender e resolver problemas de bloqueio do SQL do Azure](understand-resolve-blocking.md)
- [Como resolver problemas de bloqueio causados pelo escalonamento de bloqueios no SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in)

## <a name="increased-maxdop"></a>Aumento de MAXDOP

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição em que um plano de execução de consulta escolhido foi paralelizado mais do que devia ter sido. O otimizador de consulta pode melhorar o desempenho da carga de trabalho executando consultas em paralelo para acelerar as coisas sempre que possível. Em alguns casos, trabalhadores paralelos que processam uma consulta gastam mais tempo aguardando uns aos outros para sincronizar e mesclar os resultados em comparação a executar a mesma consulta com menos trabalhadores paralelos ou, até mesmo, em alguns casos, em comparação a um único thread de trabalho.

O sistema especialista analisa o desempenho de banco de dados atual em comparação ao período de linha de base. Ele determina se uma consulta em execução anteriormente está sendo executada de modo mais lento que antes porque o plano de execução de consulta está mais paralelizado que deveria estar.

A opção de configuração de servidor MAXDOP é usada para controlar quantos núcleos de CPU podem ser usados para executar a mesma consulta em paralelo.

### <a name="troubleshooting"></a>Solução de problemas

O log de diagnóstico produz hashes de consulta relacionados a consultas para as quais a duração da execução é aumentada porque eles foram paralelizados mais do que deveriam ter sido. O log também gera tempos de espera CXP. Esse tempo representa o tempo que um thread único de coordenador/organizador (thread 0) aguarda a conclusão de todos os outros threads antes de mesclar os resultados e seguir adiante. Além disso, o log de diagnóstico gera os tempos de espera em que as consultas de baixo desempenho estavam aguardando em execução no geral. Você pode usar essa informação como a base para a solução de problemas.

Primeiro, otimize ou simplifique consultas complexas. Uma prática recomendada é dividir trabalhos em lote grandes em partes menores. Além disso, crie índices para dar suporte às suas consultas. Você pode impor manualmente o grau máximo de paralelismo (MAXDOP) para uma consulta sinalizada como de baixo desempenho. Para configurar essa operação usando T-SQL, consulte [Configurar a opção de configuração de servidor MAXDOP](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

A definição da opção de configuração de servidor MAXDOP como zero (0) como um valor padrão denota que o banco de dados pode usar todos os núcleos de CPU disponíveis para paralelizar threads para executar uma única consulta. Configuração MAXDOP como um (1) indica que apenas um núcleo pode ser usado para uma única execução de consulta. Em termos práticos, isso significa que o paralelismo está desativado. Dependendo do caso, dos núcleos disponíveis para o banco de dados e das informações do log de diagnóstico, ajuste a opção MAXDOP para o número de núcleos de execução paralela da consulta que pode resolver o problema no seu caso.

## <a name="pagelatch-contention"></a>Contenção de pagelatch

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho indica degradação atual de desempenho da carga de trabalho do banco de dados devido à contenção de pagelatch em comparação com a linha de base da carga de trabalho dos últimos sete dias.

Travas são mecanismos de sincronização leves usados para habilitar multithreading. Elas garantem a consistência de estruturas em memória que incluem índices, páginas de dados e outras estruturas internas.

Há muitos tipos de travas disponíveis. Para fins de simplicidade, travas de buffer são usadas para proteger as páginas em memória no pool de buffers. Travas de E/S são usadas para proteger as páginas que ainda não foram carregadas no pool de buffers. Sempre que os dados são gravados ou lidos de uma página no pool de buffers, um thread de trabalho precisa adquirir uma trava de buffer para a página primeiro. Sempre que um thread de trabalho tenta acessar uma página que ainda não está disponível no pool de buffers em memória, é feita uma solicitação de E/S ao carregar as informações necessárias do armazenamento. Esta sequência de eventos indica uma forma mais grave de degradação do desempenho.

Contenção de travas na página ocorre quando vários threads tentam ao mesmo tempo adquirir travas na mesma estrutura da memória, o que introduz um tempo de espera maior para a execução da consulta. No caso de contenção de E/S pagelatch, quando os dados precisam ser acessados do armazenamento, esse tempo de espera é ainda maior. Isso pode afetar consideravelmente o desempenho da carga de trabalho. Contenção de pagelatch é o cenário mais comum de threads aguardando entre si e competindo por recursos em vários sistemas de CPU.

### <a name="troubleshooting"></a>Solução de problemas

O log de diagnóstico gera detalhes de contenção pagelatch. Você pode usar essa informação como a base para a solução de problemas.

Como um pagelatch é um mecanismo de controle interno, ele determina automaticamente quando usá-los. Decisões do aplicativo, incluindo design de esquema, podem afetar o comportamento de pagelatch devido ao comportamento determinista das travas.

Um método de lidar com contenção de trava é substituir uma chave de índice sequencial por uma não sequencial para distribuir uniformemente as inserções em um intervalo de índice. Normalmente, uma coluna à esquerda no índice distribui a carga de trabalho proporcionalmente. Outro método a considerar é o particionamento de tabela. Uma abordagem comum para reduzir a contenção excessiva de travas é criar um esquema de particionamento de hash com uma coluna computada em uma tabela particionada. No caso de contenção de E/S pagelatch, introduzir índices ajuda a reduzir esse problema de desempenho.

Para obter mais informações, consulte [Diagnosticar e resolver contenção de trava no SQL Server](http://databaser.net/moniwiki/pds/PerformanceTuning/SQLServerLatchContention.pdf) (download do PDF).

## <a name="missing-index"></a>Índice ausente

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho indica degradação atual de desempenho da carga de trabalho do banco de dados em comparação com a linha de base dos últimos sete dias devido a um índice ausente.

Um índice é usado para acelerar o desempenho das consultas. Ele fornece acesso rápido aos dados da tabela com a redução do número de páginas de conjunto de dados que precisam ser visitadas ou examinadas.

Consultas específicas que provocam degradação de desempenho são identificadas por meio dessa detecção, para as quais a criação de índices seria benéfica ao desempenho.

### <a name="troubleshooting"></a>Solução de problemas

O log de diagnóstico gera hashes de consulta para as consultas que foram identificadas como afetando o desempenho da carga de trabalho. Você pode criar índices para essas consultas. Você também pode otimizar ou remover essas consultas se elas não forem necessárias. Uma boa prática de desempenho é evitar a consulta de dados que você não usa.

> [!TIP]
> Você sabia que a inteligência interna pode gerenciar automaticamente os índices de melhor desempenho para seus bancos de dados?
>
> Para otimizar o desempenho contínuo, recomendamos que você habilite o [ajuste automático](automatic-tuning-overview.md). Esse recurso de inteligência interna exclusivo monitora continuamente seu banco de dados e ajusta e cria índices automaticamente para seus bancos.
>

## <a name="new-query"></a>Nova Consulta

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho indica que uma nova consulta de baixo desempenho foi detectada e está afetando o desempenho da carga de trabalho em comparação à linha de base de desempenho de sete dias.

Escrever uma consulta de bom desempenho pode, às vezes, ser uma tarefa desafiadora. Para obter mais informações sobre como escrever consultas, consulte [Escrever consultas SQL](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90)). Para otimizar o desempenho de consulta existente, consulte [Ajuste de consulta](/previous-versions/sql/sql-server-2008-r2/ms176005(v=sql.105)).

### <a name="troubleshooting"></a>Solução de problemas

O log de diagnóstico gera informações para até duas novas consultas que mais consomem CPU, incluindo seus hashes de consulta. Como a consulta detectada afeta o desempenho da carga de trabalho, você pode otimizar sua consulta. É uma boa prática recuperar apenas os dados que você precisa usar. Também é recomendável usar consultas com uma cláusula WHERE. Também recomendamos que você simplifique as consultas complexas e dividi-as em consultas menores. Outra boa prática é dividir as consultas de lote grande em consultas de lote menor. A introdução de índices em novas consultas geralmente é uma boa prática para atenuar esse problema de desempenho.

No banco de dados SQL do Azure, considere o uso de [análise de desempenho de consultas](query-performance-insight-use.md).

## <a name="increased-wait-statistic"></a>Maior estatística de espera

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma degradação de desempenho da carga de trabalho no qual o baixo desempenho de consultas é identificado em comparação com a linha de base da carga de trabalho dos últimos sete dias.

Nesse caso, o sistema não pode classificar as consultas com baixo desempenho em outras categorias de desempenho detectáveis padrão, mas ele detectou a estatística de espera responsável pela regressão. Portanto, ela as considera consultas com *estatísticas de espera elevadas*, em que a estatística de espera responsável pela regressão também é exposta.

### <a name="troubleshooting"></a>Solução de problemas

O log de diagnóstico gera informações sobre detalhes de tempo de espera elevadas, hashes de consulta das consultas afetadas.

Como o sistema não conseguiu identificar com sucesso a causa raiz das consultas de baixo desempenho, as informações de diagnóstico são um bom ponto de partida para solução de problemas manual. Você pode otimizar o desempenho das consultas. É uma boa prática buscar apenas os dados que você precisa usar e simplificar e dividir consultas complexas em partes menores.

Para obter mais informações sobre como otimizar o desempenho da consulta, consulte [ajuste de consulta](/previous-versions/sql/sql-server-2008-r2/ms176005(v=sql.105)).

## <a name="tempdb-contention"></a>Contenção de TempDB

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição de desempenho do banco de dados na qual existe um gargalo de threads que tentam acessar recursos do tempDB. (Essa condição não está relacionada a e/s.) O cenário típico para esse problema de desempenho é centenas de consultas simultâneas que criam, usam e depois descartam pequenas tabelas tempDB. O sistema detectou que o número de consultas simultâneas que usam as mesmas tabelas tempDB aumentou com uma significância estatística suficiente para afetar o desempenho do banco de dados em comparação à linha de base de desempenho dos últimos sete dias.

### <a name="troubleshooting"></a>Solução de problemas

O log de diagnóstico gera detalhes de contenção de tempDB. Você pode usar as informações como o ponto de partida para solução de problemas. Há duas medidas que você pode adotar para aliviar esse tipo de contenção e aumentar a taxa de transferência da carga de trabalho geral: você pode parar de usar as tabelas temporárias. Você também pode usar tabelas de otimização de memória.

Para obter mais informações, consulte [Introduction to Memory-Optimized Tables](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables).

## <a name="elastic-pool-dtu-shortage"></a>Insuficiência de DTU no pool elástico

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma degradação no desempenho de carga de trabalho de banco de dados atual em comparação à linha de base dos últimos sete dias. É devido à falta de DTUs disponíveis no pool elástico de sua assinatura.

Os [recursos do pool elástico do Azure](elastic-pool-overview.md) são usados como um pool de recursos disponíveis compartilhados entre vários bancos de dados para fins de dimensionamento. Quando os recursos de eDTU disponíveis em seu pool elástico não forem suficientemente grandes para dar suporte a todos os bancos de dados no pool, um problema de desempenho de insuficiência de DTU no pool elástico será detectado pelo sistema.

### <a name="troubleshooting"></a>Solução de problemas

O log de diagnóstico gera informações sobre o pool elástico, lista os principais bancos de dados que consomem DTU e fornece um percentual de DTU do pool usada pelo banco de dados que mais consome.

Uma vez que essa condição de desempenho está relacionada a vários bancos de dados que usam o mesmo pool de eDTUs no pool elástico, as etapas de solução de problemas concentram-se nos bancos de dados que mais consomem DTU. Você pode reduzir a carga de trabalho nos bancos de dados de maior consumo, o que inclui a otimização das consultas de maior consumo nesses bancos de dados. Você também pode garantir que consulte dados que você não usa. Outra abordagem é otimizar aplicativos usando os bancos de dados que mais consomem DTU e redistribuir a carga de trabalho entre vários bancos de dados de consumo.

Se não for possível reduzir e otimizar a carga de trabalho atual em seus bancos de dados que mais consomem DTU, considere aumentar o tipo de preço do pool elástico. Esse aumento resulta no aumento de DTUs disponíveis no pool elástico.

## <a name="plan-regression"></a>Regressão de plano

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável denota uma condição na qual o banco de dados utiliza um plano de execução de consulta de qualidade inferior. O planos não ideal geralmente causa maior execução de consulta, levando a tempos de espera mais longos para a consulta atual e outras consultas.

O mecanismo de banco de dados determina o plano de execução de consulta com o menor custo para uma execução de consulta. À medida que o tipo de consultas e cargas de trabalho mudam, às vezes os planos existentes não são mais eficientes, ou talvez o mecanismo de banco de dados não fez uma boa avaliação. Como uma questão de correção, planos de execução de consulta podem ser forçados manualmente.

Esse padrão de desempenho detectável combina três casos diferentes de regressão de plano: regressão de plano novo, regressão de plano antigo e planos existentes com carga de trabalho alterada. O tipo específico de regressão de plano que ocorreu é apresentado na propriedade *detalhes* no log de diagnóstico.

A nova condição de regressão do plano refere-se a um estado no qual o mecanismo de banco de dados começa a executar um novo plano de execução de consulta que não é tão eficiente quanto o plano antigo. A condição de regressão do plano antigo refere-se ao estado quando o mecanismo de banco de dados alterna do uso de um plano novo e mais eficiente para o plano antigo, o que não é tão eficiente quanto o novo plano. A regressão de carga de trabalho alterada dos planos existentes refere-se ao estado em que os planos antigo e novo alternam-se continuamente, com o saldo indo mais para o plano de baixo desempenho.

Para obter mais informações sobre regressões de plano, consulte [O que é regressão de plano no SQL Server?](/archive/blogs/sqlserverstorageengine/what-is-plan-regression-in-sql-server).

### <a name="troubleshooting"></a>Solução de problemas

O log de diagnóstico gera hashes de consulta, ID de plano bom, ID de plano ruim e IDs de consulta. Você pode usar essa informação como a base para a solução de problemas.

Você pode analisar o plano com melhor desempenho para suas consultas específicas e que você possa identificar com os hashes de consulta fornecidos. Depois de determinar o plano que funciona melhor para suas consultas, você pode forçá-lo manualmente.

Para obter mais informações, consulte [Saiba como o SQL Server impede regressões de plano](/archive/blogs/sqlserverstorageengine/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions).

> [!TIP]
> Você sabia que o recurso de inteligência interna pode gerenciar automaticamente os planos de execução de consulta de melhor desempenho para seus bancos de dados?
>
> Para otimizar o desempenho contínuo, recomendamos que você habilite o [ajuste automático](automatic-tuning-overview.md). Esse recurso de inteligência interna monitora continuamente seu banco de dados e ajusta e cria automaticamente planos de execução de consulta de melhor desempenho para seus bancos de dados.

## <a name="database-scoped-configuration-value-change"></a>Alteração do valor de configuração no escopo do banco de dados

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição em que uma alteração à configuração do escopo do banco de dados causa regressão do desempenho detectado em comparação ao comportamento da carga de trabalho de banco de dados nos últimos sete dias. Esse padrão denota que uma alteração recente feita à configuração no escopo do banco de dados não parece ser benéfica ao desempenho do banco de dados.

As alterações de configuração no escopo do banco de dados podem ser definidas para cada banco de dados individual. Essa configuração é usada caso a caso para otimizar o desempenho individual do seu banco de dados. As opções a seguir podem ser configuradas para cada banco de dados individual: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES e CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Solução de problemas

O log de diagnóstico gera alterações de configuração no escopo do banco de dados feitas recentemente e que causaram a degradação do desempenho em comparação ao comportamento da carga de trabalho dos últimos sete dias. Você pode reverter as alterações de configuração para os valores anteriores. Você também pode ajustar valor por valor até que o nível de desempenho desejado seja atingido. Você pode copiar os valores de configuração de escopo do banco de dados de um banco de dados semelhante com um desempenho satisfatório. Se não for possível solucionar o problema de desempenho, reverta para os valores padrão e tente ajustar o início dessa linha de base.

Para obter mais informações sobre como otimizar a configuração de escopo do banco de dados e a sintaxe T-SQL sobre alteração da configuração, consulte [Alterar a configuração (Transact-SQL) no escopo do banco de dados](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="slow-client"></a>Cliente lento

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição em que o cliente que usa o banco de dados não pode consumir a saída do banco de dados tão rápido quanto o banco de dados envia os resultados. Como o banco de dados não está armazenando os resultados das consultas executadas em um buffer, ele fica lento e aguarda o cliente consumir as saídas de consulta transmitidas antes de continuar. Essa condição também pode estar relacionada a uma rede que não é suficientemente rápida para transmitir saídas do banco de dados para o cliente consumidor.

Essa condição será gerada apenas se uma regressão de desempenho for detectada em comparação ao comportamento da carga de trabalho do banco de dados nos últimos sete dias. Esse problema de desempenho é detectado somente se ocorrer uma degradação de desempenho estatisticamente significativa em comparação com o comportamento anterior do desempenho.

### <a name="troubleshooting"></a>Solução de problemas

Esse padrão de desempenho detectável indica uma condição no lado do cliente. Solução de problemas é necessária no aplicativo do lado do cliente ou na rede do lado do cliente. O log de diagnóstico gera hashes de consulta e tempos de espera que parecem estar aguardando ao máximo para que o cliente os consuma dentro das duas últimas horas. Você pode usar essa informação como a base para a solução de problemas.

Você pode otimizar o desempenho do seu aplicativo para consumo dessas consultas. Você também pode considerar possíveis problemas de latência de rede. Uma vez que o problema de degradação de desempenho era baseado em alteração na última linha de base de desempenho de sete dias, você pode investigar se alterações recentes de condição de aplicativo ou de rede causaram esse evento de regressão de desempenho.

## <a name="pricing-tier-downgrade"></a>Downgrade de tipo de preço

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição em que o tipo de preço da sua assinatura do banco de dados foi desatualizado. Por causa da redução de recursos (DTUs) disponíveis para o banco de dados, o sistema detectou uma queda no desempenho de banco de dados atual em comparação à linha de base de sete dias anterior.

Além disso, pode haver uma condição em que o tipo de preço da sua assinatura de banco de dados foi downgrade e, em seguida, atualizado para uma camada mais alta em um curto período de tempo. A detecção dessa degradação de desempenho temporária é produzida na seção de detalhes do log de diagnóstico como um downgrade e uma atualização do tipo de preço.

### <a name="troubleshooting"></a>Solução de problemas

Se você reduziu seu tipo de preço e, portanto, as DTUs disponíveis e está satisfeito com o desempenho, não há nada que você precise fazer. Se você reduziu seu tipo de preço e não está satisfeito com o desempenho do banco de dados, reduza suas cargas de trabalho de banco de dados ou considere aumentar o tipo de preço para um nível mais alto.

## <a name="recommended-troubleshooting-flow"></a>Fluxo de solução de problemas recomendado

 Siga o fluxograma para uma abordagem recomendada à solução de problemas de desempenho usando o Intelligent Insights.

Acesse o Intelligent Insights por meio do portal do Azure indo para Análise de SQL do Azure. Tente localizar o alerta de desempenho de entrada e selecione-o. Identifique o que está acontecendo na página de detecções. Observe a análise da causa raiz fornecida do problema, o texto da consulta, as tendências de tempo da consulta e a evolução do incidente. Tente resolver o problema usando a recomendação Intelligent Insights para mitigar o problema de desempenho.

[![Fluxograma de solução de problemas](./media/intelligent-insights-troubleshoot-performance/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Selecione o fluxograma para baixar uma versão em PDF.

O Intelligent Insights geralmente precisa de uma hora para executar a análise da causa raiz do problema de desempenho. Se você não puder localizar o problema no Intelligent Insights e ele for crucial para você, use o Repositório de Consultas para identificar manualmente a causa raiz do problema de desempenho. (Normalmente, esses problemas têm menos de uma hora.) Para obter mais informações, consulte [monitorar o desempenho usando o repositório de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os conceitos de [Intelligent insights](intelligent-insights-overview.md) .
- Use o [log de diagnóstico de desempenho Intelligent insights](intelligent-insights-use-diagnostics-log.md).
- Monitor usando o [análise de SQL do Azure](../../azure-monitor/insights/azure-sql.md).
- Saiba como [coletar e consumir dados de log dos recursos do Azure](../../azure-monitor/essentials/platform-logs-overview.md).