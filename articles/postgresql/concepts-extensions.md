---
title: Usar extensões de PostgreSQL no banco de dados do Azure para PostgreSQL – servidor único
description: Descreve a capacidade de estender a funcionalidade do seu banco de dados usando as extensões no banco de dados do Azure para PostgreSQL – servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 412ce3c5245f3f22bfb03740a0451670dc6a90a7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448117"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Extensões de PostgreSQL no banco de dados do Azure para PostgreSQL – servidor único
O PostgreSQL fornece a capacidade de estender a funcionalidade de seu banco de dados usando as extensões. As extensões permitem o agrupamento de vários objetos SQL relacionados em um único pacote que pode ser carregado ou removido de seu banco de dados com um único comando. Depois de ser carregada no banco de dados, as extensões podem funcionar como recursos internos. Para obter mais informações sobre extensões do PostgreSQL, consulte  [Empacotando Objetos Relacionados em uma Extensão](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Como usar as extensões PostgreSQL
As extensões PostgreSQL devem ser instaladas no banco de dados para que você possa usá-las. Para instalar uma extensão específica, execute as [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) comando na ferramenta psql para carregar os objetos empacotados em seu banco de dados.

No momento, o Banco de Dados do Azure para PostgreSQL dá suporte a um subconjunto de extensões de chave, como é listado abaixo. As extensões além das listadas não têm suporte, não é possível criar sua própria extensão com o serviço de Banco de Dados do Azure para PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensões com suporte do Banco de Dados do Azure para PostgreSQL
As tabelas a seguir listam as extensões PostgreSQL padrão que têm suporte atualmente do Banco de Dados do Azure para PostgreSQL. Essas informações também estão disponíveis por meio da execução de `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Extensões de tipos de dados

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Fornece um tipo de dados para as senhas criptografadas automaticamente. |
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Fornece um tipo de cadeia de caracteres que não diferencia maiúsculas de minúsculas. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Fornece um tipo de dados para cubos multidimensionais. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Fornece um tipo de dados para armazenar conjuntos de pares chave-valor. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Fornece tipos de dados para padrões de numeração de produto internacionais. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Fornece um tipo de dados para estruturas semelhantes a árvores hierárquicas. |

### <a name="functions-extensions"></a>Extensões de funções

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Fornece um meio para calcular as distância ortodrômicas na superfície da Terra. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Fornece várias funções para determinar semelhanças e a distância entre cadeias de caracteres. |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Fornece funções e operadores para manipular matrizes de inteiros sem nulos. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Fornece funções de criptografia. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gerencia as tabelas de partição por hora ou ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Fornece funções e operadores para determinar a semelhança de texto alfanumérico, com base na correspondência de trigram. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Fornece funções que manipulam tabelas inteiras, incluindo a tabela de referência cruzada. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Gera UUIDs (identificadores exclusivos universais). (Veja abaixo uma observação sobre essa extensão). |
> | [orafce](https://github.com/orafce/orafce) | Fornece um subconjunto de funções e pacotes emulados de bancos de dados comerciais. |

### <a name="full-text-search-extensions"></a>Extensões de pesquisa de texto completo

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Fornece um modelo de dicionário de pesquisa de texto para números inteiros. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Um dicionário de pesquisa de texto que remove acentos (sinais diacríticos) dos lexemas. |

### <a name="index-types-extensions"></a>Extensões de tipos de índice

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Fornece classes de operador GIN de exemplo que implementam um comportamento como da árvore B para certos tipos de dados. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Fornece classes de operador de índice GiST que implementam a árvore B. |

### <a name="language-extensions"></a>Extensões de linguagem

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Linguagem de procedimento carregável PL/pgSQL. |
> | [plv8](https://plv8.github.io/) | Uma extensão de linguagem Javascript para PostgreSQL que pode ser usada para procedimentos armazenados, gatilhos, etc. |

### <a name="miscellaneous-extensions"></a>Extensões diversas

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Fornece um meio para examinar o que está acontecendo no cache do buffer compartilhado em tempo real. |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Fornece uma maneira para carregar dados de relação no cache de buffer. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Fornece um meio para rastrear as estatísticas de execução de todas as instruções SQL executadas por um servidor. (Veja abaixo uma observação sobre essa extensão). |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Fornece um meio para mostrar informações de bloqueio de nível de linha. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Fornece um meio para mostrar estatísticas em nível de tupla. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper de dados externos usado para acessar dados armazenados em servidores PostgreSQL externos. (Veja abaixo uma observação sobre essa extensão).|
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Fornece um meio de criação de índices hipotéticos que não gastam CPU ou disco. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Um módulo que suporta conexões com outros bancos de dados do PostgreSQL de dentro de uma sessão de banco de dados. (Veja abaixo uma observação sobre essa extensão). |


### <a name="postgis-extensions"></a>Extensões PostGIS

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Objetos espaciais e geográficos para PostgreSQL. |
> | address\_standardizer, address\_standardizer\_data\_us | Usado para analisar um endereço em elementos constituintes. Usado para oferecer suporte à etapa de normalização de endereços de geocodificação. |
> | [pgrouting](https://pgrouting.org/) | Estende o banco de dados geoespacial PostGIS/PostgreSQL para fornecer a funcionalidade de roteamento geoespacial. |


### <a name="time-series-extensions"></a>Extensões de série temporal

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [TimescaleDB](https://docs.timescale.com/latest) | Um série de tempo banco de dados SQL que dá suporte a automatizada com mais rapidez de particionamento para ingestão e consulta. Fornece orientados ao tempo funções analíticas, otimizações e dimensiona PostgreSQL para cargas de trabalho de série temporal. TimescaleDB é desenvolvida por e marca registrada da [escala de tempo, Inc.](https://www.timescale.com/) (Veja abaixo uma observação sobre essa extensão). |


## <a name="pgstatstatements"></a>pg_stat_statements
A extensão [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) é pré-carregada em cada servidor do Banco de Dados do Azure para PostgreSQL para fornecer a você uma maneira de acompanhar estatísticas de execução de instruções SQL.
A configuração `pg_stat_statements.track`, que controla quais instruções são contadas por extensão, tem `top` como padrão, que significa que todas as instruções emitidas diretamente por clientes são rastreadas. Dois outros níveis de rastreamento são `none` e `all`. Essa definição é configurável como um parâmetro de servidor por meio de [portal do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) ou da [CLI do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Há um equilíbrio entre as informações de execução de consulta fornecida por pg_stat_statements e o impacto no desempenho do servidor, que registra cada instrução SQL. Se você não está usando ativamente a extensão pg_stat_statements, recomendamos que você defina `pg_stat_statements.track` como `none`. Observe que alguns serviços de monitoramento de terceiros podem depender de pg_stat_statements para fornecer informações de desempenho de consulta, para confirmar se este é o caso para você ou não.

## <a name="dblink-and-postgresfdw"></a>dblink e postgres_fdw
dblink e postgres_fdw permitem que você se conecte de um servidor PostgreSQL a outro ou a outro banco de dados no mesmo servidor. O servidor de recebimento precisa permitir conexões do servidor de envio por meio de seu firewall. Ao usar essas extensões para conectar-se entre os servidores do Banco de Dados do Azure para PostgreSQL, isso pode ser feito definindo "Permitir acesso aos serviços do Azure" como LIGADO. Isso também será necessário se você quiser usar as extensões para executar um loop no mesmo servidor. A configuração "Permitir acesso aos serviços do Azure" pode ser encontrada na página do portal do Azure para o servidor Postgres em Segurança de Conexão. Ativar "Permitir acesso a serviços do Azure" coloca todos os IPs do Azure na lista de permissões.

Atualmente, as conexões de saída do banco de dados do Azure para PostgreSQL não têm suporte, exceto para conexões de outro banco de dados do Azure para servidores PostgreSQL.

## <a name="uuid"></a>uuid
Se você estiver planejando usar `uuid_generate_v4()` da extensão de uuid ossp, considere comparando com `gen_random_uuid()` da extensão de pgcrypto para benefícios de desempenho.


## <a name="timescaledb"></a>TimescaleDB
TimescaleDB é um banco de dados de série temporal é empacotado como uma extensão para PostgreSQL. TimescaleDB fornece orientados ao tempo funções analíticas, otimizações e pode ser dimensionado Postgres para cargas de trabalho de série temporal.

[Saiba mais sobre TimescaleDB](https://docs.timescale.com/latest), uma marca registrada da [escala de tempo, Inc.](https://www.timescale.com/)

### <a name="installing-timescaledb"></a>Instalando TimescaleDB
Para instalar o TimescaleDB, você precisará incluí-lo em bibliotecas compartilhadas de pré-carregamento do servidor. Uma alteração do Postgres `shared_preload_libraries` parâmetro requer um **reinicialização do servidor** entrem em vigor. Você pode alterar os parâmetros usando o [portal do Azure](howto-configure-server-parameters-using-portal.md) ou o [CLI do Azure](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> TimescaleDB pode ser habilitada no banco de dados do Azure para versões do PostgreSQL 9.6 e 10

Usando o [portal do Azure](https://portal.azure.com/):

1. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL.

2. Na barra lateral, selecione **parâmetros de servidor**.

3. Pesquise o parâmetro `shared_preload_libraries`.

4. Selecione **TimescaleDB**.

5. Selecione **salvar** para preservar suas alterações. Você receberá uma notificação quando a alteração é salva. 

6. Após a notificação **reiniciar** o servidor para aplicar essas alterações. Para saber como reiniciar um servidor, confira [Reiniciar um servidor de Banco de Dados do Azure para PostgreSQL](howto-restart-server-portal.md).


Agora você pode habilitar TimescaleDB no banco de dados Postgres. Conectar-se ao banco de dados e emita o seguinte comando:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Se você vir um erro, confirme que você [reiniciou o servidor](howto-restart-server-portal.md) depois de salvar shared_preload_libraries. 

Agora você pode criar um hypertable TimescaleDB [do zero](https://docs.timescale.com/getting-started/creating-hypertables) ou migrar [dados de série temporal existentes no PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).


## <a name="next-steps"></a>Próximas etapas
Se você não vir uma extensão que gostaria de usar, fale conosco. Vote em solicitações existentes ou criar novas solicitações de comentários no nosso [Fórum de comentários](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
