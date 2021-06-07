---
title: Otimizar o autovácuo-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como você pode otimizar a vácuo autoaspira em um banco de dados do Azure para PostgreSQL-servidor único
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 07/09/2020
ms.openlocfilehash: a94afc1ab970c2cd3f509c86efba4e455d46fd13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012554"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Otimizar o vácuo autoaspirar em um banco de dados do Azure para PostgreSQL-servidor único

Este artigo descreve como otimizar de maneira eficaz o vácuo automático em um Banco de Dados do Azure para PostgreSQL.

## <a name="overview-of-autovacuum"></a>Visão geral do vácuo automático

O PostgreSQL usa o MVCC (controle de simultaneidade multiversão) para permitir maior simultaneidade de banco de dados. Cada atualização leva a uma inserção e exclusão, e cada exclusão leva à marcação temporária de linhas para exclusão. A marcação temporária identifica tuplas inativas que serão limpas posteriormente. Para executar essas tarefas, o PostgreSQL executa um trabalho de vácuo.

Um trabalho de vácuo pode ser disparado manualmente ou automaticamente. Quando o banco de dados passa por operações com alta frequência de atualização ou exclusão, o número de tuplas inativas é maior. Quando o banco de dados está ocioso, esse número é menor. Você precisará executar trabalhos de vácuo com mais frequência quando a carga do banco de dados for pesada, o que dificulta a execução *manual* desses trabalhos.

O vácuo automático pode ser configurado e se beneficia do ajuste. Os valores padrão com que o PostgreSQL é fornecido tentam garantir que o produto funcione em todos os tipos de dispositivos. Esses dispositivos incluem o Raspberry Pis. Os valores de configuração ideais dependem:

- Do total de recursos disponíveis, como o tamanho do armazenamento e da SKU.
- Uso de recursos.
- Características de objetos individuais.

## <a name="autovacuum-benefits"></a>Benefícios do vácuo automático

Se você não executar o vácuo periodicamente, as tuplas inativas acumuladas poderão levar a:

- Sobrecarga de dados, como bancos de dados e tabelas maiores.
- Maiores índices de qualidade inferior.
- Aumento de E/S.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Monitorar a sobrecarga com consultas de vácuo automático

O exemplo de consulta a seguir foi criado para identificar o número de tuplas ativas e inativas em uma tabela chamada XYZ:

```sql
SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;
```

## <a name="autovacuum-configurations"></a>Configurações de vácuo automático

Os parâmetros de configuração que controlam o vácuo automático são baseados nas respostas de duas perguntas importantes:

- Quando ele deve começar?
- Quanto ele limpa depois de ter começado?

A seguir, há alguns parâmetros de configuração de vácuo automático que você pode atualizar com base nas perguntas acima, bem como algumas diretrizes.

Parâmetro|Descrição|Valor padrão
---|---|---
autovacuum_vacuum_threshold|Especifica o número mínimo de tuplas atualizadas ou excluídas necessárias para disparar uma operação de vácuo em qualquer tabela. O padrão é 50 tuplas. Este parâmetro pode ser definido apenas no arquivo postgresql.conf ou na linha de comando do servidor. Para substituir a configuração para tabelas individuais, altere os parâmetros de armazenamento da tabela.|50
autovacuum_vacuum_scale_factor|Especifica uma fração do tamanho da tabela a ser adicionada a autovacuum_vacuum_threshold ao decidir se uma operação de vácuo deve ser disparada. O padrão é 0.2, que é 20% do tamanho da tabela. Este parâmetro pode ser definido apenas no arquivo postgresql.conf ou na linha de comando do servidor. Para substituir a configuração para tabelas individuais, altere os parâmetros de armazenamento da tabela.|0,2
autovacuum_vacuum_cost_limit|Especifica o valor do limite de custo usado em operações de vácuo automáticas. Se -1, que é o padrão, for especificado, o valor normal de vacuum_cost_limit será usado. Se houver mais de uma função de trabalho, o valor será distribuído proporcionalmente entre as funções de trabalho de vácuo em execução. A soma dos limites para cada trabalho não excede o valor dessa variável. Este parâmetro pode ser definido apenas no arquivo postgresql.conf ou na linha de comando do servidor. Para substituir a configuração para tabelas individuais, altere os parâmetros de armazenamento da tabela.|-1
autovacuum_vacuum_cost_delay|Especifica o valor do atraso de custo usado em operações de vácuo automático. Se -1 for especificado, o valor normal de vacuum_cost_delay será usado. O valor padrão é 20 milissegundos. Este parâmetro pode ser definido apenas no arquivo postgresql.conf ou na linha de comando do servidor. Para substituir a configuração para tabelas individuais, altere os parâmetros de armazenamento da tabela.|20 ms
autovacuum_naptime | Especifica o atraso mínimo entre execuções do vácuo automático em qualquer determinado banco de dados. Em cada turno, o daemon examina o banco de dados e emite comandos VACUUM e ANALYZE conforme necessário para as tabelas nesse banco de dados. O atraso é medido em segundos. Este parâmetro pode ser definido apenas no arquivo postgresql.conf ou na linha de comando do servidor.| 15 s
autovacuum_max_workers | Especifica o número máximo de processos de vácuo automático, exceto pelo inicializador do vácuo automático, que podem ser executados simultaneamente. O padrão é três. Defina esse parâmetro somente na inicialização do servidor.|3

Para substituir as configurações de tabelas individuais, altere os parâmetros de armazenamento da tabela.

## <a name="autovacuum-cost"></a>Custo do vácuo automático

A seguir, estão os "custos" de executar uma operação de vácuo:

- As páginas de dados em que o vácuo é executado são bloqueadas.
- A computação e a memória são usadas quando um trabalho de vácuo está em execução.

Sendo assim, não execute trabalhos de vácuo com frequência excessiva ou insuficiente. Um trabalho de vácuo precisa se adaptar à carga de trabalho. Teste todas as alterações de parâmetro do vácuo automático devido às vantagens e desvantagens de cada um deles.

## <a name="autovacuum-start-trigger"></a>Gatilho de início do vácuo automático

O vácuo automático é disparado quando o número de tuplas inativas ultrapassa autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Aqui, reltuples é uma constante.

A limpeza promovida pelo vácuo automático deve acompanhar o ritmo do banco de dados. Caso contrário, você poderá ficar sem armazenamento e poderá ocorrer uma lentidão das consultas em geral. Amortizada ao longo do tempo, a frequência com que uma operação de vácuo limpa tuplas inativas deve ser igual à frequência com que as tuplas inativas são criadas.

Bancos de dados com muitas atualizações e exclusões têm mais tuplas inativas e requerem mais espaço. De modo geral, bancos de dados com muitas atualizações e exclusões se beneficiam de valores baixos de autovacuum_vacuum_scale_factor e autovacuum_vacuum_threshold. Os valores baixos impedem o acúmulo prolongado de tuplas inativas. Você pode usar valores mais altos para os dois parâmetros com bancos de dados menores porque a necessidade de vácuo é menos urgente. A execução frequente de operações de vácuo gera custo de computação e de memória.

O fator de escala padrão de 20 por cento funciona bem em tabelas com um percentual baixo de tuplas inativas. Por outro lado, ele não funciona bem em tabelas com alto percentual de tuplas inativas. Por exemplo, em uma tabela de 20 GB, esse fator de escala é equivalente a 4 GB de tuplas inativas. Em uma tabela de 1 TB, é equivalente a 200 GB de tuplas inativas.

Com o PostgreSQL, você pode definir esses parâmetros no nível de tabela ou no nível de instância. Atualmente, é possível definir esses parâmetros no nível da tabela apenas no Banco de Dados do Azure para PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Estimar o custo do vácuo automático

Executar o vácuo automático é "caro" e há parâmetros para controlar o runtime das operações de vácuo. Os parâmetros a seguir ajudam a estimar o custo da execução de vácuo:

- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

O processo de vácuo lê páginas físicas e verifica em busca de tuplas inativas. Considera-se que cada página em shared_buffers tem um custo igual a 1 (vacuum_cost_page_hit). Considera-se que todas as outras páginas têm um custo de 20 (vacuum_cost_page_dirty) quando há tuplas inativas ou de 10 (vacuum_cost_page_miss) quando não há nenhuma tupla inativa. A operação de vácuo é interrompida quando o processo ultrapassa autovacuum_vacuum_cost_limit.

Após o limite ser atingido, o processo fica suspenso pela duração especificada pelo parâmetro autovacuum_vacuum_cost_delay antes de ser iniciado novamente. Se o limite não for atingido, o vácuo automático começará após o valor especificado pelo parâmetro autovacuum_nap_time.

Em resumo, os parâmetros autovacuum_vacuum_cost_delay e autovacuum_vacuum_cost_limit controlam quanta limpeza de dados é permitida por unidade de tempo. Observe que os valores padrão são muito baixos para a maioria dos tipos de preço. Os valores ideais para esses parâmetros são dependentes do tipo de preço e devem ser configurados adequadamente.

O parâmetro autovacuum_max_workers determina o número máximo de processos de vácuo automático que podem ser executados simultaneamente.

Com o PostgreSQL, você pode definir esses parâmetros no nível de tabela ou no nível de instância. Atualmente, é possível definir esses parâmetros no nível da tabela apenas no Banco de Dados do Azure para PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Otimizar o vácuo automático por tabela

Você pode configurar todos os parâmetros de configuração anteriores por tabela. Veja um exemplo:

```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

O vácuo automático é um processo síncrono por tabela. Quanto maior o percentual de tuplas inativas de uma tabela, maior o "custo" do vácuo automático. Você pode dividir tabelas com alta taxa de atualizações e exclusões em várias tabelas. Dividir tabelas ajuda a paralelizar o vácuo automático e a reduzir o "custo" de concluir o vácuo automático em uma tabela. Você também pode aumentar o número de funções de trabalho de vácuo automático paralelas para garantir que os trabalhos sejam agendados livremente.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar e ajustar o vácuo automático, revise a seguinte documentação do PostgreSQL:

- [Capítulo 18, Configuração do servidor](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
- [Capítulo 24, Tarefas rotineiras de manutenção de banco de dados](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
