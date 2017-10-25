---
title: Solucionar problemas de desempenho do Banco de dados SQL do Azure com Insights | Microsoft Docs
description: "O Intelligent Insights ajuda você a solucionar os problemas de desempenho do Banco de dados SQL do Azure.”"
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: edd8c526a681c9cd5226ede6110f21c3362aaef6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Solucionar problemas de desempenho do banco de dados SQL do Azure com Insights inteligentes

Esta página fornece informações sobre problemas de desempenho no Banco de dados SQL do Azure por meio do log de diagnóstico de desempenho de banco de dados do recurso [Insights inteligentes](sql-database-intelligent-insights.md). Esse log de diagnóstico pode ser enviado para [Log Analytics do Azure](../log-analytics/log-analytics-azure-sql.md), [Hub de eventos do Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Armazenamento do Azure](sql-database-metrics-diag-logging.md#stream-into-azure-storage) ou uma solução de terceiros para recursos personalizados de alertas e relatórios de DevOps.

> [!NOTE]
> Para obter um guia rápido de solução de problemas de desempenho do Banco de dados SQL do Azure por meio de Insights inteligentes, consulte o fluxograma [Fluxo recomendado para solução de problemas](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) neste documento.
>

## <a name="detectable-database-performance-patterns"></a>Padrões detectáveis de desempenho do banco de dados

O recurso Insights Inteligentes detecta automaticamente problemas de desempenho com o Banco de dados SQL do Azure em tempos de espera de execução da consulta, erros, tempos limite e gera padrões de desempenho detectados para o log de diagnóstico. Padrões de desempenho detectáveis estão resumidos na tabela a seguir:

| Padrões de desempenho detectáveis | Detalhes da saída |
| :------------------- | ------------------- |
| [Atingindo os limites do recurso](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Consumo de recursos disponíveis (DTUs), threads de trabalho do banco de dados ou sessões de logon de banco de dados disponíveis na assinatura monitorada atingiram os limites, causando problemas de desempenho do Banco de dados SQL do Azure. |
| [Aumento da carga de trabalho](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Foi detectado aumento da carga de trabalho ou acumulação contínua de carga de trabalho no banco de dados, causando problemas de desempenho do Banco de dados SQL do Azure. |
| [Demanda de memória](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Trabalhadores que solicitam concessões de memória estão aguardando alocações de memória para quantidades de tempo estatisticamente significativas ou existe acumulação no aumento de trabalhadores que solicitam concessões de memória, o que está afetando o desempenho do Banco de dados SQL do Azure. |
| [Bloqueio](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Foi detectado bloqueio excessivo do banco de dados que está afetando o desempenho do Banco de dados SQL do Azure. |
| [Aumento de MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | A opção Grau Máximo de Paralelismo (MAXDOP) foi alterada e está afetando a eficiência da execução da consulta.  |
| [Contenção de pagelatch](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Foi detectada uma contenção de pagelatch que está afetando o desempenho do Banco de dados SQL do Azure. Vários threads estão tentando, simultaneamente, acessar as mesmas páginas de buffer de dados na memória, resultando em maior tempo de espera, o que afeta o desempenho do Banco de dados SQL do Azure. |
| [Índice ausente](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Foi detectado um problema de índice ausente que está afetando o desempenho do Banco de dados SQL do Azure. |
| [Nova consulta](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Foi detectada nova consulta que afeta o desempenho geral do Banco de dados SQL do Azure.  |
| [Estatística de espera incomum](sql-database-intelligent-insights-troubleshoot-performance.md#unusual-wait-statistic) | Foram detectados tempos de espera incomuns do banco de dados que afetam o desempenho do Banco de dados SQL do Azure. |
| [Contenção de TempDB](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Vários threads estão tentando acessar os mesmos recursos de TempDB, provocando um afunilamento que afeta o desempenho do Banco de dados SQL do Azure. |
| [Insuficiência de DTU no pool elástico](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | A insuficiência de eDTUs no pool elástico está afetando o desempenho do Banco de dados SQL do Azure. |
| [Regressão de plano](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Foi detectado um novo plano ou uma alteração na carga de trabalho de um plano existente que está afetando o desempenho do Banco de dados SQL do Azure. |
| [Alteração do valor de configuração no escopo do banco de dados](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | A alteração de configuração no banco de dados está afetando o desempenho do Banco de dados SQL do Azure. |
| [Cliente lento](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Foi detectada lentidão no aplicativo cliente, que não está sendo capaz de consumir a saída do Banco de dados SQL do Azure rápido o suficiente, o que está afetando o desempenho do Banco de dados SQL do Azure. |
| [Downgrade de tipo de preço](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | A ação de downgrade de tipo de preço diminuiu os recursos disponíveis, afetando o desempenho do Banco de dados SQL do Azure. |

> [!TIP]
> Para otimização contínua do Banco de dados SQL do Azure considere habilitar o [ajuste automático do Banco de dados SQL do Azure](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automatic-tuning) – um recurso exclusivo de inteligência interna do SQL do Azure que monitora continuamente o banco de dados SQL do Azure, ajusta automaticamente os índices e aplica correções de plano de execução da consulta.
>

A seção a seguir descreve os padrões de desempenho detectáveis listados anteriormente em mais detalhes.

## <a name="reaching-resource-limits"></a>Atingindo os limites do recurso

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável combina problemas de desempenho relacionados ao alcance dos limites de recurso, limites de trabalho e limites de sessão disponíveis. Quando esse problema de desempenho é detectado, um campo de descrição do log de diagnóstico indica se o problema de desempenho está relacionado a limites de recurso, trabalho ou sessão.

Recursos no Banco de dados SQL do Azure são geralmente conhecidos como [recursos de DTU](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-what-is-a-dtu) e consistem em uma medida combinada de recursos de CPU e E/S (E/S de dados e log de transação). O padrão de alcance dos de limites do recurso é reconhecido quando uma degradação de desempenho de consulta detectada é causada por qualquer um dos limites de recurso medidos.

Recurso de limites de sessão denota o número de logons simultâneos disponíveis para o Banco de dados SQL do Azure. Esse padrão de desempenho é reconhecido no caso de aplicativos que estão se conectando aos Bancos de dados SQL do Azure atingirem o número de logons simultâneos disponíveis para o banco de dados. Se aplicativos tentarem usar mais sessões do que as disponíveis em um banco de dados, o desempenho da consulta será afetado.

O alcance dos limites de trabalho é um caso específico de atingir os limites do recurso, uma vez que trabalhadores disponíveis não são contados no uso de DTU. O alcance dos limites de trabalho em um banco de dados pode causar o surgimento de tempos de espera específicos do recurso e, portanto, uma degradação no desempenho da consulta.

### <a name="troubleshooting"></a>Solucionar problemas

O log de diagnóstico gera hashes de consulta das consultas que afetaram o desempenho e as porcentagens de consumo do recurso. Use essas informações como ponto de partida para otimizar a carga de trabalho do banco de dados. Em particular, considere a otimização de consultas que afetam o desempenho, a adição de índices ou a otimização de aplicativos com uma distribuição de carga de trabalho mais uniforme. Caso não seja possível reduzir as cargas de trabalho ou executar a otimização, considere aumentar o tipo de preço da assinatura do Banco de dados SQL do Azure para aumentar a quantidade de recursos disponíveis.

Se você atingiu os limites de sessão disponíveis, otimize seus aplicativos em termos de reduzir o número de logons feitos no banco de dados. Se não for possível reduzir o número de logons de seus aplicativos no banco de dados, considere aumentar o tipo de preço do banco de dados ou talvez dividi-lo em vários bancos de dados e movê-lo, para obter uma distribuição de carga de trabalho mais balanceada.

Para obter mais sugestões sobre como resolver limites de sessão, consulte [Como lidar com os limites máximos de logons no Banco de dados SQL do Azure](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Para descobrir os limites de recurso disponíveis para sua assinatura, consulte [Limites de recursos do Banco de dados SQL do Azure](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-resource-limits).

## <a name="workload-increase"></a>Aumento da carga de trabalho

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho identifica problemas causados por aumento da carga de trabalho ou sua forma mais grave de empilhamento de carga de trabalho.

Essa detecção é feita por meio de combinação de várias métricas. A métrica básica medida está detectando um aumento na carga de trabalho em comparação com a linha de base de carga de trabalho anterior. A outra forma de detecção baseia-se em avaliar um grande aumento nos threads de trabalho ativos, grande o suficiente para afetar o desempenho da consulta.

Em sua forma mais grave em que a carga de trabalho sofre empilhamento contínuo porque o banco de dados SQL do Azure não é capaz de processar a carga de trabalho. O resultado é o aumento contínuo do tamanho da carga de trabalho – ou seja, a condição de empilhamento da carga de trabalho. Devido a essa condição, o tempo de espera da carga de trabalho que aguarda execução está aumentando e representa um dos mais graves problemas de desempenho do banco de dados. Esse problema foi detectado por meio do monitoramento de aumento no número de threads de trabalho anulados. 

### <a name="troubleshooting"></a>Solucionar problemas

O log de diagnóstico gera o número de consultas cuja execução aumentou e o hash de consulta da consulta que mais contribuiu para o aumento da carga de trabalho. Use essas informações como ponto de partida para otimizar a carga de trabalho e especialmente para a consulta identificada como a que mais contribuiu para o aumento da carga de trabalho.

Considere também a distribuição de cargas de trabalho mais uniformes para o banco de dados. Otimize também a consulta que afeta o desempenho e adicione índices. Outra abordagem a considerar pode ser a distribuição da carga de trabalho entre vários bancos de dados. Caso não seja possível, aumente o tipo de preço da assinatura do Banco de dados SQL do Azure para aumentar a quantidade de recursos disponíveis.

## <a name="memory-pressure"></a>Demanda de memória

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho indica degradação no desempenho do banco de dados atual devido à demanda de memória ou, em sua forma mais grave, uma condição de empilhamento de memória, em comparação com a linha de base de desempenho dos últimos sete dias.

Demanda de memória indica uma condição de desempenho em que há um número suficiente de threads de trabalho solicitando concessões de memória no Banco de dados SQL do Azure. Isso causa uma condição de alta utilização de memória em que o banco de dados SQL do Azure não é capaz de alocar memória de maneira eficiente a todos os trabalhadores que a solicitam. Um dos motivos mais comuns para esse problema está relacionado à quantidade de memória disponível para o Banco de dados SQL do Azure, por um lado, e por outro lado um aumento na carga de trabalho que causa aumento de threads de trabalho e demanda de memória.

A forma mais grave de demanda de memória é a condição de empilhamento de memória, indicando que há um número maior de threads de trabalho solicitando concessões de memória do que consultas que liberam a memória. Esse número de threads de trabalho que solicita concessões de memória também pode ser aumentar continuamente (ou seja, empilhar), uma vez que o mecanismo não consegue alocar memória com eficiência suficiente para atender à demanda. Condição de empilhamento de memória representa um dos mais graves problemas de desempenho do banco de dados.

### <a name="troubleshooting"></a>Solucionar problemas

O log de diagnóstico gera os detalhes de repositório do objeto na memória, com o administrador (ou seja, o thread de trabalho) marcado como o motivo principal de alto uso de memória e os carimbos de data/hora relevantes. Essas informações podem ser usadas como base para a solução de problemas. 

Considere a otimização ou remoção de consultas relacionadas aos administradores com maior uso de memória. Verifique se você não está consultando dados que não planeja usar. A prática recomendada é sempre usar uma cláusula WHERE em suas consultas. Além disso, é recomendável criar índices não clusterizados para buscar os dados, em vez de examiná-los.

Convém considerar ainda reduzir a carga de trabalho, otimizá-la ou distribuí-la em vários bancos de dados. Outra abordagem a considerar pode ser a distribuição da carga de trabalho entre vários bancos de dados. Caso não seja possível, aumente o tipo de preço da assinatura do Banco de dados SQL do Azure para aumentar a quantidade de recursos disponíveis na memória para o banco de dados.

Para sugestões adicionais de solução de problemas, consulte [Reflexão sobre concessões de memória: o misterioso consumidor de memória do SQL Server com vários nomes](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/).

## <a name="locking"></a>Bloqueio

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho indica degradação no desempenho do banco de dados atual em que foi detectado bloqueio excessivo do banco de dados em comparação com a linha de base de desempenho dos últimos sete dias. 

Em RDBMS modernos, o bloqueio é essencial para a implementação de sistemas multithread em que o desempenho é maximizado por meio da execução simultânea de várias transações paralelas de banco de dados e trabalho, quando possível. Em termos simples, o bloqueio, neste contexto, refere-se ao mecanismo de acesso interno no qual apenas uma única transação pode acessar com exclusividade linhas, páginas, tabelas e arquivos necessários e não entrar em conflito com outra transação de recursos. Quando a transação que bloqueou os recursos para uso termina de usá-los, o bloqueio desses recursos é liberado permitindo que outras transações acessem os recursos necessários. Para obter mais informações sobre bloqueio, consulte [Bloqueio no mecanismo de banco de dados](https://msdn.microsoft.com/library/ms190615.aspx).

Se as transações executadas pelo SQL Engine estiverem aguardando por longos períodos para acessar recursos bloqueados para uso, esse tempo de espera afetará o desempenho de execução da carga de trabalho. 

### <a name="troubleshooting"></a>Solucionar problemas

O log de diagnóstico gera detalhes do bloqueio que podem ser usados como base para solução de problemas. Analise as consultas de bloqueio relatadas, ou seja, consultas que estão apresentando degradação de desempenho de bloqueio e remova-as. Em alguns casos, talvez a otimização de consultas de bloqueio seja bem-sucedida.
A maneira mais simples e segura de atenuar o problema é manter as transações curtas e reduzir a superfície de bloqueio das consultas mais caras. Considere dividir grandes lotes de operações em operações menores. Uma prática recomendada é reduzir a superfície de bloqueio de consulta, tornando a consulta o mais eficiente possível. Reduza grandes exames, uma vez que aumentam as chances de deadlocks e afetam negativamente o desempenho geral do banco de dados. Para consultas identificadas que causam bloqueio, crie novos índices ou adicione colunas ao índice existente para evitar exames de tabela. Para obter mais sugestões, consulte [Como resolver problemas de bloqueio causados por escalonamento de bloqueios no SQL Server](https://support.microsoft.com/en-us/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Aumento de MAXDOP

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição em que um plano de execução de consulta escolhido foi paralelizado mais do que devia. O otimizador de consultas do Banco de dados SQL do Azure aprimora o desempenho da carga de trabalho, tomando a decisão de executar a consulta em paralelo para melhorar a velocidade, sempre que possível. Em alguns casos, entretanto, trabalhadores paralelos que processam uma consulta gastam mais tempo aguardando uns aos outros para sincronizar e mesclar os resultados em comparação com a execução da mesma consulta com menos trabalhadores paralelos ou até mesmo, em alguns casos, um único thread de trabalho.

O sistema especialista analisa o desempenho do banco de dados atual comparado com o período da linha de base e determina se uma consulta de execução anterior está em execução mais lenta do que antes, em razão de o plano de execução da consulta estar mais paralelizado do que deveria.

O MAXDOP da opção de configuração do servidor no Banco de dados SQL do Azure é usado para controlar o número de núcleos de CPU que podem ser usados para executar a mesma consulta em paralelo. 

### <a name="troubleshooting"></a>Solucionar problemas

O log de diagnóstico gera hashes de consulta relacionados às consultas para as quais a duração da execução aumentou porque foram paralelizadas mais do que deveriam. O log também gera tempos de espera CXP. Esse tempo representa o tempo que um thread único de coordenador/organizador (thread 0) aguarda a conclusão de todos os outros threads antes de mesclar os resultados e seguir adiante. Além disso, o log de diagnóstico gera os tempos de espera em que as consultas de baixo desempenho estavam aguardando execução. Essas informações são a base da solução de problemas.

Veja primeiro a possibilidade de otimizar ou simplificam as consultas complexas. Uma prática recomendada é dividir trabalhos em lote grandes em partes menores. Além disso, verifique se você tem índices criados que dão suporte às consultas. Também é possível impor manualmente o MAXDOP (Grau Máximo de Paralelismo) a uma consulta específica sinalizada como de mau desempenho. Essa operação é configurada usando o T-SQL. Consulte a opção de configuração do servidor [Configurar o grau máximo de paralelismo](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

A definição da opção de configuração do servidor MAXDOP zero (0) como um valor padrão deve indicar que o banco de dados SQL do Azure pode usar todos os núcleos de CPU lógicos disponíveis para paralelizar threads executando uma única consulta. A configuração do MAXDOP como um (1) indica que apenas um núcleo pode ser usado para uma única execução de consulta – em termos práticos, isso significa que o paralelismo está desativado. Dependendo do caso, dos núcleos disponíveis para o banco de dados e das informações do log de diagnóstico, ajuste a opção MAXDOP para o número de núcleos de execução paralela da consulta que resolveu o problema, no seu caso.

## <a name="pagelatch-contention"></a>Contenção de pagelatch

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho indica degradação atual de desempenho da carga de trabalho do banco de dados devido à contenção de pagelatch, em comparação com a linha de base da carga de trabalho dos últimos sete dias.

Travas são mecanismos de sincronização leve usados pelo Banco de dados SQL do Azure para habilitar multithread e garantir a consistência da estruturas na memória, incluindo índices, páginas de dados e outras estruturas internas.

Há muitos tipos de travas disponíveis no Banco de dados SQL do Azure. Para fins de simplicidade, travas de buffer são usadas para proteger páginas na memória no pool de buffers e travas de E/S são usadas para proteger páginas ainda não carregadas no pool de buffers. Sempre que os dados são gravados ou lidos de uma página no pool de buffers, um thread de trabalho precisa adquirir uma trava de buffer para a página primeiro. Sempre que um thread de trabalho tenta acessar uma página que não está disponível no pool de buffers na memória, a solicitação de E/S é feita para carregar as informações necessárias do armazenamento, indicando uma forma mais grave de degradação do desempenho.

Contenção de travas na página ocorre quando vários threads tentam, ao mesmo tempo, adquirir travas na mesma estrutura da memória, apresentando um tempo de espera maior para a execução da consulta. No caso de contenção de E/S de pagelatch quando os dados precisam ser acessados do armazenamento, esse tempo de espera é ainda maior e pode afetar consideravelmente o desempenho da carga de trabalho. Contenção de pagelatch é o cenário mais comum de threads aguardando entre si e competindo por recursos em vários sistemas de CPU.

### <a name="troubleshooting"></a>Solucionar problemas

O log de diagnóstico gera os detalhes de contenção do pagelatch que podem ser usados como uma base para solucionar o problema.

Como um pagelatch é um mecanismo de controle interno do Banco de dados SQL do Azure, ele determina automaticamente quando usá-los. Decisões do aplicativo, incluindo design de esquema, podem afetar o comportamento de pagelatch devido ao comportamento determinista das travas.
Um método de lidar com contenção de trava é substituir uma chave de índice sequencial por uma não sequencial, para distribuir uniformemente as inserções em um intervalo de índice. Geralmente, isso é feito por uma coluna principal no índice que distribui a carga de trabalho proporcionalmente. Outro método a considerar é o particionamento de tabela. Uma abordagem comum para reduzir a contenção excessiva de travas é criar um esquema de particionamento de hash com uma coluna computada em uma tabela particionada. No caso de contenção de E/S de pagelatch, a introdução de índices é recomendada para atenuar esse problema de desempenho. Para obter mais informações, consulte [Diagnosticando e resolvendo contenção de trava no SQL Server](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (download do PDF).

## <a name="missing-index"></a>Índice ausente

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho indica degradação atual de desempenho da carga de trabalho do banco de dados em comparação com a linha de base dos últimos sete dias devido a um índice ausente.

Um índice é usado para acelerar o desempenho das consultas. Ele fornece acesso rápido aos dados da tabela com a redução do número de páginas de conjunto de dados que precisam ser visitadas ou examinadas.

Consultas específicas que provocam degradação de desempenho são identificadas por meio dessa detecção, para a qual a criação de índices seria benéfica ao desempenho.

### <a name="troubleshooting"></a>Solucionar problemas

O log de diagnóstico gera hashes de consulta para as consultas que afetam o desempenho da carga de trabalho. Crie índices para essas consultas. Otimize ou remova também essas consultas se elas não forem necessárias. Uma boa prática de desempenho é evitar a consulta de dados que você não usa.

> [!TIP]
> Você sabia que a inteligência interna do Azure pode gerenciar automaticamente os melhores índices de desempenho para seus bancos de dados?
>
> Para otimização contínua do Banco de dados SQL do Azure é recomendável que você habilite o [ajuste automático do Banco de dados SQL do Azure](sql-database-automatic-tuning.md) – um recurso exclusivo de inteligência interna do SQL do Azure que monitora continuamente o banco de dados SQL do Azure e ajusta e cria índices automaticamente para seus bancos de dados.
>

## <a name="new-query"></a>Nova Consulta

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica que uma nova consulta de baixo desempenho foi detectada e está afetando o desempenho da carga de trabalho em comparação com a linha de base de desempenho de sete dias.

A escrita de uma consulta de bom desempenho pode, às vezes, ser uma tarefa desafiadora. Para obter mais informações sobre como escrever consultas, consulte [Escrevendo consultas de SQL](https://msdn.microsoft.com/library/bb264565.aspx) e para otimizar o desempenho da consulta existente, consulte [Ajuste de consulta](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Solucionar problemas

O log de diagnóstico gera informações para até duas (2) novas consultas mais consumidoras de CPU, incluindo seus hashes de consulta. Como a consulta detectada está afetando o desempenho da carga de trabalho, convém otimizar a consulta. A prática recomendada é não recuperar dados que você não usa. O uso de consultas com a cláusula WHERE é também recomendável. A simplificação de consultas complexas e a divisão em consultas menores também é recomendável. A divisão de grandes lotes de consulta em lotes menores também é uma boa prática a considerar. A introdução de índices em novas consultas geralmente é uma boa prática para atenuar esse problema de desempenho.

Considere o uso de [Análise de desempenho de consultas no banco de dados SQL do Azure](sql-database-query-performance.md).

## <a name="unusual-wait-statistic"></a>Estatística de espera incomum

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma degradação de desempenho da carga de trabalho no qual o baixo desempenho de consultas é identificado em comparação com a linha de base da carga de trabalho dos últimos sete dias.

Nesse caso o sistema não pôde classificar consultas de baixo desempenho de consultas em nenhuma outra categoria de desempenho detectável padrão, mas detectou estatísticas de espera responsáveis pela regressão e está, portanto, considerando-as como consultas com &#8220; *estatísticas de espera incomum*&#8221; em que a estatística de espera incomum responsável pela regressão também é exposta. 

### <a name="troubleshooting"></a>Solucionar problemas

O log de diagnóstico gera informações sobre detalhes de tempo de espera incomum, hashes de consulta da consultas afetadas e tempos de espera.

Nesse caso, o sistema não pôde identificar com êxito a causa raiz do baixo desempenho das consultas e as informações de diagnóstico sobre as consultas de baixo desempenho podem servir como um bom ponto de partida para solução de problemas manual. Otimize o desempenho dessas consultas. Geralmente, é uma boa prática não buscar dados que você não usa e simplificar e dividir consultas complexas em consultas menores. Para obter mais informações sobre como otimizar o desempenho da consulta, veja [Ajuste de consulta](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>Contenção de TempDB

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição de desempenho do banco de dados no qual existe um afunilamento de threads que tentam acessar recursos de TempDB (essa condição não está relacionada a E/S). O cenário típico para esse problema de desempenho são centenas de consultas simultâneas que criam, usam e, em seguida, soltam pequenas tabelas TempDB, que são tabelas TempDB. O sistema detectou que o número de consultas simultâneas que usam as mesmas tabelas TempDB aumentou com uma significância estatística a ponto de afetar o desempenho do banco de dados, em comparação com a linha de base de desempenho dos últimos sete dias.

### <a name="troubleshooting"></a>Solucionar problemas

O log de diagnóstico gera detalhes de contenção de TempDB que podem ser usados como um ponto de partida na solução de problemas de desempenho. Há algumas coisas que você pode adotar para atenuar esse tipo de contenção e aumentar a produtividade da carga de trabalho geral. Pare de usar tabelas temporárias. Considere também o uso de tabelas com otimização de memória. Consulte [Introdução a tabelas com otimização de memória](https://docs.microsoft.com/en-us/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Insuficiência de DTU no pool elástico

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma degradação no desempenho da carga de trabalho do banco de dados atual em comparação com a linha de base dos sete dias anteriores, devido à insuficiência de DTUs disponíveis no pool elástico de sua assinatura. 

Recursos no Banco de dados SQL do Azure são geralmente conhecidos como [recursos de DTU](sql-database-what-is-a-dtu.md) e consistem em uma medida combinada de recursos de CPU e E/S (E/S de dados e log de transação). [Recursos de pool elástico do Azure](sql-database-elastic-pool.md) são usados como um pool de recursos disponíveis de eDTU compartilhados entre vários bancos de dados para fins de dimensionamento. Quando os recursos de eDTU disponíveis em seu pool elástico não forem suficientemente grandes para dar suporte a todos os bancos de dados no pool, o problema de desempenho de insuficiência de DTU no pool elástico é detectado pelo sistema.

### <a name="troubleshooting"></a>Solucionar problemas

O log de diagnóstico gera informações sobre o pool elástico, lista os principais bancos de dados que consomem DTU e fornece um percentual de DTU do pool usada pelo banco de dados que mais consome.

Como essa condição de desempenho está relacionada a vários bancos de dados que usam o mesmo pool de eDTUs no pool elástico, as etapas de solução de problemas precisam se concentrar nos bancos de dados que mais consomem DTU. Reduza a carga de trabalho nos principais bancos de dados consumidores, incluindo a otimização das consultas que mais consomem nesses bancos de dados. Verifique se você não está consultando dados que não usa. Outra abordagem é otimizar aplicativos usando os bancos de dados que mais consomem DTU e redistribuir a carga de trabalho entre vários bancos de dados de consumo.

Se não for possível reduzir e otimizar a carga de trabalho atual em seus bancos de dados que mais consomem DTU, considere aumentar o tipo de preço do pool elástico. Tal aumento resulta no aumento de DTUs disponíveis no pool elástico.

## <a name="plan-regression"></a>Regressão de plano

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição em que o banco de dados SQL do Azure inicia usando um plano de execução de consulta com qualidade inferior. Planos de qualidade inferior geralmente causam aumento na execução da consulta, levando a longos tempo de espera da consulta atual e outras.

O Banco de dados SQL do Azure determina o plano de execução da consulta com o menor custo para uma execução de consulta. No entanto, como o tipo de consultas e cargas de trabalho é alterado, às vezes os planos existentes não são mais eficientes ou talvez o Banco de dados SQL do Azure não tenha feito uma boa avaliação. Como uma questão de correção, planos de execução de consulta podem ser forçados manualmente.

Esse padrão de desempenho detectável combina três casos diferentes de regressão de plano: regressão de plano novo, regressão de plano antigo e planos existentes com carga de trabalho alterada. O tipo do qual a regressão do plano específico ocorreu são fornecidas na propriedade &#8220; *details*&#8221; no log de diagnóstico.

A condição de regressão de novo plano refere-se a um estado no qual o Banco de dados SQL do Azure inicia a execução de um novo plano de execução de consulta que não é tão eficiente quanto o plano antigo. A condição de regressão do plano antigo refere-se a um estado em que o Banco de dados SQL do Azure troca o plano novo, mais eficiente, pelo plano antigo, que não é tão eficiente quanto o novo. A regressão de carga de trabalho alterada de planos existentes refere-se ao estado em que dois planos, o novo e o antigo, estão continuamente sendo alternadas, com o equilíbrio indo mais em direção ao plano de baixo desempenho.

Para obter mais informações sobre regressões de plano, consulte [O que é regressão de plano no SQL Server](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/). 

### <a name="troubleshooting"></a>Solucionar problemas

O log de diagnóstico gera hashes de consulta, boa ID de plano, ID de plano inválida e IDs de consulta que podem ser usadas como base para solução de problemas de desempenho dessa condição.

Analise o plano com melhor desempenho para suas consultas específicas e que você possa identificar com os hashes de consulta fornecidos. Depois de determinar o plano que funciona melhor para suas consultas, você pode forçá-lo manualmente. Para obter mais informações, consulte [Como o SQL Server impede regressões de plano](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Você sabia que a inteligência interna do Azure pode gerenciar automaticamente os melhores planos de execução de consulta de desempenho para seus bancos de dados?
>
> Para otimização contínua de desempenho do Banco de dados SQL do Azure é recomendável que você habilite o [Ajuste automático do Banco de dados SQL do Azure](sql-database-automatic-tuning.md) – um recurso exclusivo de inteligência interna do SQL do Azure que monitora continuamente o banco de dados SQL do Azure e ajusta e cria os melhores planos de execução de consulta de desempenho para seus bancos de dados.
>

## <a name="database-scoped-configuration-value-change"></a>Alteração do valor de configuração no escopo do banco de dados

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição em que houve uma alteração na configuração do escopo do banco de dados que está causando a regressão de desempenho detectado em comparação com o comportamento da carga de trabalho de banco de dados nos últimos sete dias. Isso indica que alterações recentes feitas na configuração de escopo do banco de dados não parecem ser benéficas ao desempenho do banco de dados.

A alteração de configuração no escopo do banco de dados pode ser definida para cada banco de dados individual. Essa configuração é usada em cada caso para otimizar o desempenho individual do banco de dados. As opções a seguir podem ser configuradas para cada banco de dados individual: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES e CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Solucionar problemas

O log de diagnóstico gera alterações de configuração no escopo do banco de dados feitas recentemente e que causaram a degradação do desempenho em comparação com o comportamento da carga de trabalho dos últimos sete dias. Considere a reversão das alterações de configuração aos valores anteriores ou ajuste valor por valor até atingir o nível de desempenho desejado. Também é possível copiar os valores de configuração de escopo do banco de dados de um banco de dados semelhante, com um desempenho satisfatório. Se não for possível solucionar o problema de desempenho, reverta aos valores padrão do Banco de dados SQL do Azure e tente ajustar dessa linha de base.

Para obter mais detalhes sobre como otimizar a configuração de escopo do banco de dados e a sintaxe T-SQL sobre alteração da configuração, consulte [Alterar a configuração (Transact-SQL) no escopo do banco de dados](https://msdn.microsoft.com/en-us/library/mt629158.aspx).

## <a name="slow-client"></a>Cliente lento

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição em que o cliente que está usando o Banco de dados SQL do Azure não é capaz de consumir a saída do banco de dados tão rapidamente quanto o banco de dados é capaz de enviar os resultados. Como o banco de dados SQL do Azure não está olhando os resultados das consultas executadas em um buffer, está lento e aguardando o cliente consumir as saídas da consulta transmitida antes de continuar. Essa condição também pode estar relacionada a uma rede lenta que não é capaz de transmitir suficientemente rápido as saídas do Banco de dados SQL do Azure para o cliente de consumo.

Essa condição será gerada apenas se houver uma regressão de desempenho detectada em comparação com o comportamento da carga de trabalho do banco de dados nos últimos sete dias. Isso garante que o problema de desempenho seja detectado apenas se houver uma degradação de desempenho estatisticamente significativa em comparação com o comportamento anterior do desempenho.

### <a name="troubleshooting"></a>Solucionar problemas

Esse padrão de desempenho detectável indica uma condição de cliente com solução de problemas necessária no aplicativo do lado do cliente ou na rede do cliente. O log de diagnóstico gera hashes de consulta e tempos de espera que parecem estar aguardando o máximo para que o cliente os consuma dentro de 2 horas anteriores de tempo. Essas páginas podem ser usadas como base para a solução de problemas.

Pense em otimizar o desempenho do seu aplicativo para o consumo dessas consultas. Considere também possíveis problemas de latência de rede. Como o problema de degradação de desempenho foi com base na alteração da linha de base de desempenho dos últimos sete dias, investigue se houve alterações de aplicativo ou alterações na condição da rede dentro do período recente que possam ter causado esse evento de regressão de desempenho. 

## <a name="pricing-tier-downgrade"></a>Downgrade de tipo de preço

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição na qual o tipo de preço do Banco de dados SQL do Azure sofreu downgrade. Por causa da redução de recursos (DTUs) disponíveis para o banco de dados, o sistema detectou uma queda no desempenho de banco de dados atual medido conforme a linha de base dos últimos sete dias.

Além disso, pode haver uma condição na qual o tipo de preço da assinatura do Banco de dados SQL do Azure sofreu downgrade e, em seguida, atualizada para uma tipo superior em um curto período de tempo. Isso indica uma detecção de degradação de desempenho temporária produzida na seção de detalhes do log de diagnóstico, à medida que o tipo de preço passa por downgrade e atualização.

### <a name="troubleshooting"></a>Solucionar problemas

Se você reduziu o tipo de preço, portanto, os DTUs disponíveis para o Banco de dados SQL do Azure e está satisfeito com o desempenho, não há nada a fazer neste momento. Se após a redução do tipo de preço você não estiver satisfeito com o desempenho do Banco de dados SQL do Azure, reduza as cargas de trabalho do banco de dados ou considere aumentar o tipo de preço.

## <a name="recommended-troubleshooting-flow"></a>Fluxo de solução de problemas recomendado

Siga o fluxograma abaixo para uma abordagem recomendada à solução de problemas de desempenho usando Insights inteligentes.

Acesso a Insights inteligentes por meio do portal do Azure, navegando até Análise de SQL do Azure. Tente localizar o alerta de desempenho de entrada e clique nele. Identifique o que está acontecendo na página de detecções. Observe a Análise da causa raiz fornecida do problema, o texto da consulta, as tendências de tempo da consulta e a evolução dos incidentes. Usando a recomendação do Insights inteligentes sobre solução de problema de desempenho, tente resolvê-lo. 

[![Solução de problemas de fluxograma](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Clique no fluxograma para baixar uma versão dele em PDF.

O Insights inteligentes geralmente precisa 1h para executar a análise da causa raiz do problema de desempenho. Caso você não consiga localizar o problema no Insights inteligentes (na maioria dos casos, esses são problemas de menos de 1h) e se esse problema é importante para você, use o QDS (Repositório de Dados de Consultas ) para identificar manualmente a causa raiz do problema de desempenho. Para obter mais informações, consulte [Monitoramento de desempenho usando o Repositório de consultas](https://docs.microsoft.com/en-us/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Próximas etapas
- Aprenda os conceitos de [Insights inteligentes](sql-database-intelligent-insights.md)
- Use o [log de diagnóstico de desempenho do Banco de dados SQL do Azure com Insights inteligentes](sql-database-intelligent-insights-use-diagnostics-log.md)
- Monitore o [Banco de dados SQL do Azure usando a Análise de SQL do Azure](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql)
- Saiba como [coletar e consumir dados de log dos recursos do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
